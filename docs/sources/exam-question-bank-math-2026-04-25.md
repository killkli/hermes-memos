# 教育會考數學科題庫工作 Session（2026-04-25）

> 消化自 session（數學科題庫網站建立 + LaTeX 驗證修正）

---

## 背景

用戶在 04-24 完成英語科題庫補正後，決定另建數學科題庫網站（math-exam-guardian repo），因為數學科題目含有大量 LaTeX 數學公式，需要專門的渲染驗證流程。

---

## 數學科題庫網站建立

### Repo & 部署
- **Repo**: `https://github.com/killkli/math-exam-guardian`（public）
- **URL**: `https://killkli.github.io/math-exam-guardian/`
- **部署模式**: `build_type=legacy`（GitHub token 缺少 `workflow` scope，無法用 Actions）
- **關鍵檔案**:
  - `index.html` — 單頁應用，含 KaTeX CDN 0.16.11
  - `app.js` — 過濾/搜尋/彈窗邏輯，載入 `data/questions.json`
  - `style.css` — 學術風格，深藍 #1a2b4a + 琥珀 #f59e0b
  - `data/questions.json` — 354 題（13年份 102-114）
  - `.nojekyll` — 防止 Jekyll 處理 `_data/` 目錄

### JSON 結構（標準化）
```json
{
  "questions": [
    {
      "id": "108-26",
      "year": 108,
      "question_number": 26,
      "type": "choice",
      "subtype": "幾何",
      "difficulty": "medium",
      "question": "題目 LaTeX",
      "figure_desc": "共享圖形描述（可為 null）",
      "figure_type": "none | shared_figure | per_option",
      "options": {"A": "A選項", "B": "B選項", "C": "C選項", "D": "D選項"},
      "answer": "B",
      "concepts": ["概念標籤陣列"],
      "page_source": "p.p2"
    }
  ],
  "metadata": {"total_questions": 354, "total_years": 13}
}
```

### 107年結構異常
QA subagent 使用不同欄位名稱：
- 用 `question_text` 而非 `question`
- 用 `option_A/B/C/D` 而非 `options` 字典
- 無 `id` 欄位

**正規化**：批次處理時自動轉換為標準格式，並新增 `id` = `{year}-{num}`。

---

## KaTeX 渲染問題發現與修正

### 驗證工具
`scripts/check_latex.js` — 使用 Node.js KaTeX API 而非 regex：
```javascript
const katex = require('katex');
const raw = fs.readFileSync(jsonPath, 'utf8');
const data = JSON.parse(raw);
let errors = [];
for (const q of data.questions) {
  // 提取 $...$ 區塊
  const blocks = (q.question + ' ' + Object.values(q.options).join(' '))
    .match(/\$[^\$]+\$/g) || [];
  for (const block of blocks) {
    try {
      katex.renderToString(block.slice(1, -1), { throwOnError: true });
    } catch(e) {
      errors.push({qid: q.id, block, error: e.message});
    }
  }
}
```

**為何不用 regex**：Regex 誤報率高（`$a$`、`$x$` 都被標記為 TINY_MATH_BLOCK 但實際上 KaTeX 可正確渲染）。

### 發現的 9 個 KaTeX 錯誤

| 題號 | 位置 | 錯誤 | 修正 |
|------|------|------|------|
| **108-26** | A/B/C/D 選項 | `$\left(0,\ \dfrac{9}{2}\right)$` — `\\ `（雙反斜杠+空白）在 `\left(` 內 | → `$\left(0,\\,\dfrac{9}{2}\right)$`（`\,` thin space） |
| **113-1** | 題目 | `\left(-\dfrac{1}{4}\right)` 前有多餘 `\` | 移除多餘反斜杠 |
| **113-3** | 題目 | `\x08egin{cases}` — `\x08`（ASCII backspace U+0008，是 OCR 控制字元）| → `\begin{cases}` |
| **113-16** | C 選項 | `$\\left[` → 雙反斜杠在 `\left[` 內 | → `\left[` |
| **113-16** | D 選項 | `$\\$` → 雙反斜杠在根號內 | → `\` |

### 107年正規化
- 新增 `id` = `{year}-{num}`
- `question_text` → `question`
- `option_A/B/C/D` → `options.A/B/C/D`
- `options` list → 轉為 dict format

---

## GitHub Pages 架構發現

### `.nojekyll` 必要性
GitHub Pages 預設用 Jekyll 處理，Jekyll 忽略以 `_` 开头的目录（如 `_data`、`_config`）。在 repo 根目录放置空文件 `.nojekyll` 可跳過 Jekyll 處理。

### `build_type=legacy` vs `workflow`
- `workflow`：需要 token 有 `workflow` scope，可自動觸發 Actions
- `legacy`：直接上傳檔案，無 CI/CD，靜態 site
- 當前 token 缺少 `workflow` scope → 用 `legacy` 模式

### 修正後部署指令
```bash
gh repo deploy --repo killkli/math-exam-guardian --no-input
```

---

## 工具與技術棧

| 項目 | 技術 |
|------|------|
| 數學公式渲染 | KaTeX 0.16.11（CDN） |
| OCR 模型 | Claude Sonnet 4.6（數學公式辨識） |
| 驗證工具 | Node.js + KaTeX API |
| 網站框架 | Vanilla JS（無需建置） |
| 部署 | GitHub Pages（legacy upload） |

---

## 衍生技能

### `latex-render-pipeline`
`~/.hermes/skills/data-science/latex-render-pipeline/`
- `SKILL.md` — Pipeline 文件
- `scripts/check_latex.js` — 驗證腳本
- Node modules: `/tmp/node_modules/katex/`（需先 `cd /tmp && npm install katex`）

### 使用方式
```bash
cd /tmp && npm install katex  # 第一次需執行
node ~/.hermes/skills/data-science/latex-render-pipeline/scripts/check_latex.js
```

---

## Source
- [Exam Question Bank Sessions](/hermes-memos/sources/exam-question-bank-sessions-2026-04-24/) — 前日英語科 session
- [Exam Question Bank QA System](/hermes-memos/concepts/exam-question-bank-qa-system/) — 題庫 QA 系統 concept page
