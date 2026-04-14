# 不客套 bu-ketao

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![繁體中文](https://img.shields.io/badge/lang-Traditional_Chinese-red.svg)](#)
[![Compression](https://img.shields.io/badge/compression-~72%25-brightgreen.svg)](#test-results)
[![Claude Code](https://img.shields.io/badge/Claude_Code-skill-blueviolet.svg)](rules/claude-code-skill.md)
[![ChatGPT](https://img.shields.io/badge/ChatGPT-compatible-74aa9c.svg)](rules/system-prompt.md)
[![Cursor](https://img.shields.io/badge/Cursor-compatible-000.svg)](rules/cursorrules)

[繁體中文](README.md)

**Strip the 客套 (pleasantries) from Chinese LLM output. ~72% compression, zero semantic loss.**

"bu-ketao" (不客套) literally means "no pleasantries" in Chinese. Chinese LLMs are verbose in fundamentally different ways than English ones — there are no articles (a/an/the) to drop. The fluff has entirely different sources, so the compression rules must be different too.

## The Problem

Ask a Chinese LLM a technical question, and you get:

> **好的，讓我來幫你分析一下這個問題。** 你的組件之所以會頻繁重新渲染，**主要是因為**你在每次渲染時都創建了一個新的物件引用。**簡單來說**，當你把一個 inline object 作為 prop 傳遞時，React 會認為這是一個全新的值，因此觸發重新渲染。**需要注意的是**，這是一個非常常見的效能問題。建議你使用 `useMemo` 來記憶化這個物件，這樣就能避免不必要的重新渲染了。**希望這對你有幫助！**

With bu-ketao:

> inline object prop = 每次新 ref = re-render。`useMemo` 包住。

Same information. ~80% fewer characters.

## Why Chinese Needs Its Own Rules

English compression tools (like [caveman](https://github.com/JuliusBrussee/caveman)) don't work for Chinese:

| English compression lever           | Works in Chinese?                            |
| ----------------------------------- | -------------------------------------------- |
| Drop articles (a/an/the)            | ❌ Don't exist in Chinese                   |
| Drop filler (just/really/basically) | ⚠️ Chinese has different fillers      |
| Abbreviations (DB/auth/config)      | ❌ Chinese is already compact per-character |

We identified **10 textual verbosity patterns + 3 behavioral patterns** specific to Chinese LLMs.

## Chinese AI Verbosity Patterns

### Drop (zero semantic loss)

| Pattern | Examples | Translation |
|---------|---------|-------------|
| **客套話** Pleasantries | 好的，讓我來幫你 / 希望這對你有幫助！ | "OK let me help you" / "Hope this helps!" |
| **填充詞** Filler | 需要注意的是 / 值得一提的是 / 簡單來說 | "It's worth noting that" / "Simply put" |
| **避險語** Hedging | 從某種程度上來說 / 可能需要根據實際情況 | "To some extent" / "Depends on actual situation" |
| **空洞宣稱** Hollow claims | 具有重要意義 / 至關重要的 / 不可或缺的 | "Has significant importance" / "Indispensable" |
| **模糊引用** Phantom attribution | 研究表明... / 有學者認為... (no source) | "Studies show..." / "Scholars believe..." |
| **重複解釋** Repetition | Same point stated 2-3 times in different words | 換句話說... (in other words...) |

### Replace (compress, don't delete)

| Pattern | Compress to |
|---------|-------------|
| 首先...其次...最後...總結 (First...Second...Last...Summary) | Drop ordinals, flat list |
| 由於...因此... (Because...therefore...) | Arrow (→) or direct statement |
| 不僅是 X，更是 Y (Not only X, but also Y) | X 也 Y |
| Template openers (根據分析，我們發現...) | Direct statement |
| Four-character idiom stacking | Keep only necessary ones |

### Behavioral (not rules, but worth knowing)

| Pattern | Example | Real intent |
|---------|---------|-------------|
| **推卸迴避** Scope deflection | "This is a known issue, not in scope" | Avoiding work |
| **催促收工** Conversation escape | "Ready to commit?" / "Should work now" | Rushing to end |
| **主動推銷** Upselling | "I can also help you with something more valuable" | Expanding scope |

## Intensity Levels

| Level | Strategy |
|-------|----------|
| **lite** | Drop pleasantries/filler/hedging. Keep complete sentences |
| **full** | Drop templates, hollow claims, repetition. Fragments OK. One point per sentence |
| **ultra** | Maximum compression, language-agnostic. English when shorter (pool not 連接池). Arrows for causality |

## Test Results

Tested across 10 real development scenarios with Claude. Token counts via cl100k_base tokenizer:

| Scenario | Verbose tokens | Compressed tokens | Saved | Compression |
|----------|---------------|-------------------|-------|-------------|
| Obsidian plugin lifecycle | 354 | 128 | 226 | 64% |
| TypeScript build vs IDE error | 291 | 118 | 173 | 59% |
| Git rebase conflicts | 294 | 112 | 182 | 62% |
| Docker OOMKilled debugging | 290 | 148 | 142 | 49% |
| CSS flexbox vs grid | 301 | 92 | 209 | 69% |
| Python GIL explained | 296 | 76 | 220 | 74% |
| OAuth 2.0 Flow | 699 | 151 | 548 | 78% |
| React 18 useEffect | 508 | 106 | 402 | 79% |
| MongoDB vs PostgreSQL | 510 | 147 | 363 | 71% |
| Nginx WebSocket proxy | 449 | 59 | 390 | 87% |
| **Total** | **3,992** | **1,137** | **2,855** | **72%** |

At Claude Sonnet output pricing ($15/M tokens), 50 conversations/day = **~5.2M tokens saved/year ≈ $78**.

Full before/after: [`examples/before-after.md`](examples/before-after.md)

### Token Economics

Chinese characters cost more tokens than English in most tokenizers:

| Tokenizer | Chinese token tax (vs same-semantics English) |
|-----------|-----------------------------------------------|
| GPT-4 | 4.94x |
| GPT-4o | 3.38x |
| Claude | ~3-4x (estimated) |
| Qwen2.5 | 2.62x |

**Every Chinese character you cut saves more tokens than cutting an English word.** The ROI of Chinese compression is higher than English.

## Usage

### Claude Code (skill)

Copy [`rules/claude-code-skill.md`](rules/claude-code-skill.md) to `~/.claude/commands/bu-ketao.md`:

```
/bu-ketao          # default: full
/bu-ketao lite     # professional, concise
/bu-ketao ultra    # maximum compression
```

### ChatGPT / Claude / Gemini (system prompt)

Paste [`rules/system-prompt.md`](rules/system-prompt.md) content into your system prompt. Works with any LLM that accepts system instructions.

### Cursor

Copy [`rules/cursorrules`](rules/cursorrules) to `.cursorrules` in your project root.

## Related Projects

| Project | Approach | vs bu-ketao |
|---------|----------|-------------|
| [caveman](https://github.com/JuliusBrussee/caveman) | English output compression (drop articles, abbreviations) | English rules don't work for Chinese; wenyan = Classical Chinese stylization, not practical compression |
| [cjk-token-reducer](https://github.com/jserv/cjk-token-reducer) | Translate CJK input to English before sending to LLM | Compresses **input** (translate prompts); bu-ketao compresses **output** (less AI fluff). Complementary |
| [LLMLingua](https://github.com/microsoft/LLMLingua) | Perplexity-based token removal | General prompt compression, not Chinese-specific; input-side only |
| [prompt-optimizer](https://github.com/linshenkx/prompt-optimizer) | Chinese prompt optimizer (25k+ stars) | Makes AI answer *better*, not *shorter* |

**bu-ketao's unique position**: no other project systematically catalogs Chinese AI output verbosity patterns and provides ready-to-use compression rulesets.

bu-ketao's rules are also [proposed upstream to caveman](https://github.com/JuliusBrussee/caveman/pull/160) as `zh-tw-lite/full/ultra` modes.

## License

MIT
