# 國中教育會考題庫補正系統

> 三代理人 QA Pipeline — 適用於 OCR 輸出題庫的結構化補正流程

---

## 系統定位

從 cap.rcpet.edu.tw 下載 PDF，透過視覺辨識（Qwen）轉為 JSON 題庫，再經由三代理人 QA 系統補正，最終部署至 GitHub Pages 供學生使用。

**Repo：** https://github.com/killkli/tw-exam-bank
**URL：** https://killkli.github.io/tw-exam-bank/

---

## 題庫現況問題（OCR 輸出的典型缺陷）

| 問題類型 | 說明 | 處理方式 |
|----------|------|----------|
| **題組文章漏收** | JSON 只有問題文字，閱讀測驗原文完全缺失 | 回查 OCR MD 原始檔補足 `passage` 欄位 |
| **題數不足** | 原始 JSON 題數少於實際（如105年18題→實際41題） | 補足缺失題目 |
| **題號跳號** | 題號不連續（111年出現44題但有跳號） | 重新整理題號對應 |
| **圖片/聽力題** | 需視覺或音頻素材，JSON 無法承載 | 維持 `answer: null`，標記為不可用 |

---

## 三代理人 QA Pipeline

```
研究者 Agent → 專家 Agent → 評估 Agent
```

### 研究者 Agent
-職責：查對 OCR MD 原始檔，比對 JSON 題庫是否漏收文章
-輸出：識別題組結構，標記缺失的 `passage`

### 專家 Agent
-職責：填寫每題答案（可用/不可用），補足 `group_id`
-輸出：`answer` 欄位填答，`usable: true/false` 標記

### 評估 Agent
-職責：審查結果，給出 Pass/Conditional Pass/Fail
-產出：通過率、警告數、失敗數統計

---

## 補正工作流程（兩階段）

### 階段一：題組文章補正（先生文章）

```
for each 年份（分批並行）:
    1. 讀取 OCR MD 原始檔（如 110年_英語閱讀.md）
    2. 識別所有題組（group_id）
    3. 補足每個題組的完整文章（passage 欄位）
    4. 補足缺失題目（原始 JSON 題數不足時）
    5. 維持 answer: null（等待專家填答）
```

### 階段二：專家填答

```
for each 年份（分批並行）:
    1. 專家 Agent 逐一填答可作答題目
    2. 圖片題/聽力題/地圖題 → answer: null
    3. 輸出：年份_英語科_題庫_補正.json
```

---

## 數據統計（英語科 102-114年）

| 指標 | 數值 |
|------|------|
| 總年份 | 13 年（102-114） |
| 總題數 | ~500+ 題 |
| 可用率 | ~85% |
| 不可用率 | ~15%（圖片題、聽力題、地圖題） |

---

## JSON 結構（含補正欄位）

```json
{
  "year": "110",
  "subject": "英語",
  "questions": [
    {
      "id": "110-29",
      "group_id": "110-reading-3",    // 新增：題組識別
      "passage": "書信原文內容...",      // 新增：題組文章
      "question": "問題文字",
      "options": ["A", "B", "C", "D"],
      "answer": "C",
      "usable": true,
      "type": "reading",
      "difficulty": "medium",
      "concepts": ["閱讀理解", "主旨"]
    }
  ]
}
```

---

## 關鍵教訓

1. **OCR JSON ≠ 完整題庫** — 視覺辨識只輸出問題，題組文章需另外補入
2. **永遠回查原始檔** — 專家 Agent 若只看 JSON 會漏掉文章（Session 1 犯過的錯誤）
3. **題數不符是警訊** — 某年份 JSON 只有18題但實際41題，說明辨識過程有題目漏掉了
4. **並行有上限** — `delegate_task` 建議 3-4 批次，每批次處理的年份不宜過多

---

## 與其他 QA 系統的比較

| 特性 | 教育會考題庫 | 日本印度勞動力分析 |
|------|-------------|-----------------|
| 資料來源 | PDF→OCR→JSON | 統計PDF+網頁 |
| 缺失類型 | 文章漏收、題數不足 | 數據鴻溝、置信度低 |
| 審查重點 | 題組完整性 | 數據準確性 |
| 最終產出 | 互動式作答系統 | 分析報告 |

---

## 數學科差異（2026-04-25 新增）

數學科題庫與英語科最大的差異：**大量 LaTeX 數學公式**需要驗證渲染正確性。

### KaTeX 驗證 Pipeline

使用 Node.js KaTeX API（而非 regex）驗證所有 `$...$` 區塊：

```bash
cd /tmp && npm install katex
node ~/.hermes/skills/math-exam-bank-pipeline/scripts/check_latex.js
```

腳本位置：`~/.hermes/skills/data-science/latex-render-pipeline/scripts/check_latex.js`

### 常見 KaTeX 錯誤類型

| 錯誤類型 | 範例 | 原因 |
|----------|------|------|
| `\\ ` 在 `\left(` 內 | `$\left(0,\ \dfrac{9}{2}\right)$` | `\\ ` 不是合法 KaTeX，應用 `\,` |
| 控制字元 | `\x08egin{cases}` | OCR 引入 U+0008 Backspace |
| 雙反斜杠 | `$\\left[` | `\\left[` → 應為 `\left[` |
| 多餘反斜杠 | `\left(-\dfrac{1}{4}\right)` 前多 `\` | OCR 誤判 |

### 為何不用 Regex 驗證
Regex（如 `\[([^\]]{1,3})\]`）誤報率高，`$a$`、`$x$` 等單字母變數被判為 TINY_MATH_BLOCK 但 KaTeX 可正確渲染。**必須用 KaTeX API 實際渲染**才能確認。

### 數學科部署
- **Repo**: `https://github.com/killkli/math-exam-guardian`
- **URL**: `https://killkli.github.io/math-exam-guardian/`
- **渲染引擎**: KaTeX 0.16.11（CDN）
- **部署模式**: `build_type=legacy`（token 缺少 workflow scope）

---

## Source

- [Exam Question Bank Sessions](/hermes-memos/sources/exam-question-bank-sessions-2026-04-24/) — 英語科 session 紀錄
- [Exam Question Bank Math](/hermes-memos/sources/exam-question-bank-math-2026-04-25/) — 數學科 session 紀錄
