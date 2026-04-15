# 不客套 bu-ketao

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![繁體中文](https://img.shields.io/badge/語言-繁體中文-red.svg)](#)
[![壓縮率](https://img.shields.io/badge/壓縮率-~72%25-brightgreen.svg)](#實測數據)
[![Claude Code](https://img.shields.io/badge/Claude_Code-skill-blueviolet.svg)](rules/claude-code-skill.md)
[![ChatGPT](https://img.shields.io/badge/ChatGPT-compatible-74aa9c.svg)](rules/system-prompt.md)
[![Cursor](https://img.shields.io/badge/Cursor-compatible-000.svg)](rules/cursorrules)
[![Copilot](https://img.shields.io/badge/Copilot-compatible-1f6feb.svg)](rules/copilot-instructions.md)

[English](README.en.md)

**砍掉中文 AI 的客套問候話，留下乾貨。**

中文 LLM 的囉唆跟英文完全不同 —— 英文靠砍冠詞（a/an/the）就能省很多，但中文根本沒有冠詞。廢話的來源不一樣，壓縮的方式也該不一樣。

## 問題

問一個技術問題，中文 AI 回你這個：

> **好的，讓我來幫你分析一下這個問題。** 你的組件之所以會頻繁重新渲染，**主要是因為**你在每次渲染時都創建了一個新的物件引用。**簡單來說**，當你把一個 inline object 作為 prop 傳遞時，React 會認為這是一個全新的值，因此觸發重新渲染。**需要注意的是**，這是一個非常常見的效能問題。建議你使用 `useMemo` 來記憶化這個物件，這樣就能避免不必要的重新渲染了。**希望這對你有幫助！**

用不客套壓縮後：

> inline object prop = 每次新 ref = re-render。`useMemo` 包住。

同樣的資訊，少 80% 的字。

## 為什麼中文需要自己的規則

英文壓縮工具（如 [caveman](https://github.com/JuliusBrussee/caveman)）的核心手段套到中文完全不太實用：

| 英文壓縮手段                     | 對中文有用？        |
| -------------------------- | ------------- |
| 砍冠詞 a/an/the               | ❌ 中文沒有冠詞      |
| 砍填充詞 just/really/basically | ⚠️ 中文有不同的填充詞  |
| 縮寫 DB/auth/config          | ❌ 中文本身每個字就很緊湊 |

中文 AI 的廢話有自己的結構，我們歸納出 **14 類文字冗餘 + 3 類行為冗餘**。

## 中文 AI 客服腔完整詞庫

### 砍除類（Drop）— 刪掉零語義損失

#### 客套話

| 位置 | 廢話 |
|------|------|
| 開場 | 好的 / 當然 / 讓我來幫你分析一下 / 我來為你詳細解釋 |
| 結尾 | 希望這對你有幫助 / 歡迎繼續提問 / 如果你還有其他問題 / 歡迎隨時提問 |
| 過渡 | 接下來讓我們看看 / 讓我進一步說明 |

#### 填充詞

> 需要注意的是 / 值得一提的是 / 值得注意的是 / 一般來說 / 通常來說 / 簡單來說 / 具體來說 / 事實上 / 基本上 / 實際上 / 坦白說 / 嚴格來說

這些詞刪掉後，後面的句子意思完全不變。

#### 避險語

> 從某種程度上來說 / 可能需要根據實際情況調整 / 這只是我的建議 / 具體情況可能因環境而異 / 建議在正式環境中先進行測試

AI 用這些語句甩責。在技術回答中，如果你確定就直接說，不確定就明講哪裡不確定。

#### 空洞宣稱

> 具有重要意義 / 至關重要的 / 不可或缺的 / 前所未有的 / 具有深遠的影響 / 具有里程碑意義

連一個購物中心都能被 AI 描述成「文化遺產的中心」。砍。

#### 模糊引用

> 研究表明... / 有學者認為... / 根據最佳實踐... / 已有文獻指出...

沒有具體出處的引用 = 虛構的權威感。要嘛給來源，要嘛別提。

#### 重複解釋

中文 LLM 最嚴重的問題：同一件事用 2-3 種方式說。

> 連接池會重複使用已建立的連接。**換句話說**，它維護一組現成的連接，避免重複建立。**也就是說**，不用每次都從頭連線。

三句話講的是同一件事。砍到剩一句。

#### 總結印章

> 一句話總結：... / 總而言之 / 簡而言之 / 概括來說

直接講結論，不要貼「這是結論」的標籤。

#### 覆述問題

用戶問「React 為什麼 re-render」，AI 開頭回「你問的是 React component 為什麼會重新渲染這個問題...」。用戶知道自己問了什麼，不需要 AI 複讀。

#### 條件式推銷

> 如果你告訴我你的環境，我可以給更具體的建議 / 如果你願意，我下一步可以幫你...

推銷的隱蔽變體。回答完就停，有具體下一步就直接講，不要包條件句。

#### 白話翻譯區塊

> 翻成人話就是... / 用白話說... / 簡單來說就是...

先用技術語言解釋一遍，再開一個「降維解釋」區塊重述同一件事。一次講清楚就好。

### 替換類（Replace）— 壓縮不刪除

| 原文 | 壓縮為 |
|------|--------|
| 首先...其次...再者...最後...總結來說 | 去掉序號，直接列點 |
| 由於...因此... | 箭頭（→）或直述 |
| 不僅是 X，更是 Y | X 也 Y |
| 根據分析，我們發現... | 直述結果 |
| 四字成語堆砌（循序漸進、行之有效、與日俱增） | 只保留必要的 |
| 被動語態（可以被認為是） | 改主動語態 |
| 「從 X 到 Y」虛假範圍修辭 | 直述具體內容 |
| 不是 X，而是 Y（否定對比框架） | 直述 Y。❌「真正的問題不是效能，而是架構」→ ✅「真正的問題是架構」 |
| 比較題寫平衡論文 | 直接給推薦 + 簡短理由，每邊最多 3-4 點。不要列完優缺點說「看你需求」 |

### 行為類（觀察，不進壓縮規則）

除了文字冗餘，中文 LLM 還有行為層面的廢話：

| 行為 | 範例 | 真實意圖 |
|------|------|---------|
| **推卸迴避** | 「這是之前就知道的問題，不在這次範圍之內」 | 不想做 |
| **催促收工** | 「可以 commit 了嗎？」「目前修改已經可以正常運作了」 | 想結束對話 |
| **主動推銷** | 「如果你願意，我可以幫你做一件更有價值的事」 | 膨脹 scope |

這些是行為問題，不是文字問題，所以不放進壓縮規則。但知道它們存在有助於辨識 AI 的意圖。

## 三個等級

| 等級 | 策略 | 適合場景 |
|------|------|---------|
| **lite** | 砍客套/填充/避險，保留完整句子 | 正式文件、給非工程師看的回答 |
| **full** | 砍模板句、空洞宣稱、重複解釋。碎片句 OK，一重點一句 | 日常開發對話（預設） |
| **ultra** | 最大壓縮，語言無關——英文更短就用英文（pool not 連接池）。箭頭因果、縮寫 | 快速 debug、context 吃緊時 |

### 等級對比範例

**問題**：「為什麼 React component 會 re-render？」

**原始（AI 客服腔）**：
> 好的，讓我來幫你分析一下這個問題。你的組件之所以會頻繁重新渲染，主要是因為你在每次渲染時都創建了一個新的物件引用。簡單來說，當你把一個 inline object 作為 prop 傳遞時，React 會認為這是一個全新的值，因此觸發重新渲染。需要注意的是，這是一個非常常見的效能問題。建議你使用 `useMemo` 來記憶化這個物件，這樣就能避免不必要的重新渲染了。希望這對你有幫助！

**lite**：
> 組件頻繁重新渲染，因為每次渲染都創建新的物件引用。inline object prop 每次都是新 reference，觸發 re-render。用 `useMemo` 包住即可。

**full**：
> inline object prop = 每次新 ref = re-render。`useMemo` 包住。

**ultra**：
> inline obj prop → new ref → re-render。`useMemo`。

## 實測數據

用 Claude 跑 14 組真實情境（技術 + 非技術），每組跑 verbose baseline（無壓縮）vs bu-ketao full：

| 情境 | 領域 | 原始 tokens | 壓縮 tokens | 省下 | 壓縮率 |
|------|------|------------|------------|------|--------|
| Obsidian plugin 生命週期 | 技術 | 354 | 128 | 226 | 64% |
| TypeScript build vs IDE 報錯 | 技術 | 291 | 118 | 173 | 59% |
| Git rebase 衝突處理 | 技術 | 294 | 112 | 182 | 62% |
| Docker OOMKilled 排查 | 技術 | 290 | 148 | 142 | 49% |
| CSS flexbox vs grid 選擇 | 技術 | 301 | 92 | 209 | 69% |
| Python GIL 解釋 | 技術 | 296 | 76 | 220 | 74% |
| OAuth 2.0 Flow | 技術 | 699 | 151 | 548 | 78% |
| React 18 useEffect | 技術 | 508 | 106 | 402 | 79% |
| MongoDB vs PostgreSQL | 技術 | 510 | 147 | 363 | 71% |
| Nginx WebSocket proxy | 技術 | 449 | 59 | 390 | 87% |
| 催款郵件 | 商業 | 996 | 250 | 746 | 75% |
| p-value 解釋 | 學術 | 1,094 | 386 | 708 | 65% |
| 台北三天兩夜行程 | 生活 | 1,633 | 402 | 1,231 | 75% |
| 拒絕加班 | 職場 | 1,138 | 284 | 854 | 75% |
| **全部合計** | | **8,853** | **2,459** | **6,394** | **72%** |

Token 計算使用 cl100k_base tokenizer（GPT-4/Claude 系列）。

完整 before/after 對比：[`examples/before-after.md`](examples/before-after.md)

### 成本換算

以 Claude Sonnet output 定價 $15/M tokens 估算：

| | tokens | 成本 |
|---|---|---|
| 原始 14 組回應 | 8,853 | $0.133 |
| 壓縮 14 組回應 | 2,459 | $0.037 |
| **省下** | **6,394** | **$0.096** |

### Token 經濟學

中文字在大部分 tokenizer 裡都比英文貴：

| Tokenizer | 中文 token 稅（vs 同語義英文） |
|-----------|-------------------------------|
| GPT-4 | 4.94x |
| GPT-4o | 3.38x |
| Claude | ~3-4x（估計） |
| Qwen2.5 | 2.62x |

**砍一個中文廢話字，省的 token 比砍一個英文廢話詞還多。** 中文壓縮的 ROI 比英文更高。

## 使用方式

### Claude Code（skill）

把 [`rules/claude-code-skill.md`](rules/claude-code-skill.md) 複製到 `~/.claude/commands/bu-ketao.md`：

```
/bu-ketao          # 預設 full
/bu-ketao lite     # 專業簡潔
/bu-ketao ultra    # 最大壓縮
```

### ChatGPT / Claude / Gemini（system prompt）

把 [`rules/system-prompt.md`](rules/system-prompt.md) 的內容貼進 system prompt。適用任何支援 system instructions 的 LLM。

### ChatGPT Custom Instructions / GPTs

把 [`rules/openai-custom-instructions.md`](rules/openai-custom-instructions.md) 的內容貼進 ChatGPT 的 Custom Instructions 或 GPTs 的 system prompt。針對 OpenAI 的 token 上限精簡過。

### GitHub Copilot

把 [`rules/copilot-instructions.md`](rules/copilot-instructions.md) 複製到專案的 `.github/copilot-instructions.md`。額外包含 code comment 壓縮規則——comment 只寫 WHY，不寫 WHAT。

### Cursor

把 [`rules/cursorrules`](rules/cursorrules) 複製到專案根目錄的 `.cursorrules`。

## 相關專案

| 專案 | 做法 | 與不客套的差異 |
|------|------|---------------|
| [caveman](https://github.com/JuliusBrussee/caveman) | 英文 LLM 輸出壓縮（砍冠詞、縮寫） | 英文規則套中文沒用；wenyan 模式是文言文風格化，非實用壓縮 |
| [talk-normal](https://github.com/hexiecs/talk-normal) | 任意 LLM 去 AI slop，中英雙語 | 範圍較廣但中文規則較淺；不客套的繁中冗餘分類（14 類）更深入，有 before/after 實測 |
| [cjk-token-reducer](https://github.com/jserv/cjk-token-reducer) | 把 CJK 輸入翻成英文再送 LLM，省 35-50% input token | 壓的是**輸入端**（翻譯 prompt），不客套壓的是**輸出端**（讓 AI 少廢話）。兩者可互補 |
| [LLMLingua](https://github.com/microsoft/LLMLingua) | 用小模型計算困惑度，移除低資訊量 token | 通用 prompt 壓縮，非針對中文客服腔；壓輸入不壓輸出 |
| [prompt-optimizer](https://github.com/linshenkx/prompt-optimizer) | 中文 prompt 優化器（25k+ stars） | 重點在「讓 AI 回答更好」，不是「讓 AI 回答更短」 |


不客套的規則也已[提交到 caveman 上游](https://github.com/JuliusBrussee/caveman/pull/160)作為 `zh-tw-lite/full/ultra` 模式。

## 檔案結構

```
bu-ketao/
├── README.md                           ← 你在讀的這份（繁體中文）
├── README.en.md                        ← English version
├── LICENSE                             ← MIT
├── rules/
│   ├── system-prompt.md                ← 通用 system prompt（SSOT）
│   ├── claude-code-skill.md            ← Claude Code skill
│   ├── cursorrules                     ← Cursor 格式
│   ├── openai-custom-instructions.md   ← ChatGPT / GPTs 格式
│   └── copilot-instructions.md         ← GitHub Copilot 格式
└── examples/
    └── before-after.md                 ← 完整實測 before/after
```

## 授權

MIT
