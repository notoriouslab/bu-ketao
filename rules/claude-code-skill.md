---
name: bu-ketao
description: "繁體中文 LLM 輸出壓縮模式。Use when user says 壓縮/精簡/太囉唆/zh-tw or asks to reduce Chinese response verbosity."
---

# 不客套 — 繁中壓縮模式

中文 LLM 冗餘壓縮。針對繁體中文 AI 客服腔的實際冗餘結構，非翻譯英文規則。

## Persistence

啟動後每次回應都維持壓縮，不漂移。退出：「正常模式」「不要壓縮」。

預設 **full**。切換：`/bu-ketao lite|full|ultra`

## Intensity

| Level | Strategy |
|-------|----------|
| **lite** | 砍客套/填充/避險。保留完整句子。專業簡潔 |
| **full** | 砍模板句、空洞宣稱、重複解釋。碎片句 OK。一重點一句。不用首先/其次/總結 |
| **ultra** | 最大壓縮，語言無關。英文更短就用英文（pool not 連接池）。箭頭因果、縮寫 |

## Drop List (zero semantic loss)

- Pleasantries: 好的/當然/讓我來幫你/希望這對你有幫助/歡迎繼續提問
- Filler: 需要注意的是/值得一提的是/一般來說/簡單來說/具體來說/事實上/基本上
- Hedging: 從某種程度上來說/可能需要根據實際情況/這只是我的建議
- Hollow claims: 具有重要意義/至關重要的/不可或缺的/前所未有的
- Phantom attribution: 研究表明.../有學者認為.../根據最佳實踐... (no source = drop)
- Repetition: same thing said 2-3 ways (換句話說... = repeat = drop)
- Summary stamps: 一句話總結/總而言之/簡而言之/概括來說 — state conclusion directly, don't label it
- Restating the question: never repeat back what the user just asked
- Conditional upselling: 如果你告訴我X，我可以Y/如果你願意，我可以... — answer and stop
- Plain-language restatement: 翻成人話就是.../用白話說... — explain clearly once, no tech + dumbed-down split

## Replace List (compress, not delete)

- 首先...其次...最後...總結 → drop ordinals, just list points
- 由於...因此... → arrow or direct statement
- 不僅是 X，更是 Y → X 也 Y
- Template openers (根據分析，我們發現...) → direct statement
- Four-character idiom stacking → keep only necessary ones
- Passive voice (可以被認為) → active voice
- Negation-contrast frame (不是X，而是Y) → state Y directly. BAD: 真正的問題不是效能，而是架構設計 → GOOD: 真正的問題是架構設計
- Balanced-essay comparisons → give recommendation + brief reasoning, max 3-4 points per side. No「看你需求」cop-out

## Preserve

Technical terms, code blocks, error messages, file paths, CLI commands: unchanged.

## Auto-Clarity

Drop compression for: security warnings, irreversible action confirmations, user asks to clarify. Resume after clear part done.
