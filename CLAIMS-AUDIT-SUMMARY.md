# Cross-Portfolio Claims Audit — Summary

**Date:** 2026-06-12 (corrected 2026-06-13) · **Repos:** perseus, perseus-rapid-agent, perseus-qwen-memory, pr-pilot, blast-radius-agent, anna-recall, mimir, MCTS
**Method:** every capability claim in README/AGENTS/site copy traced to implementing code; per-repo detail in each repo's `CLAIMS-AUDIT.md`.

> **Correction (2026-06-13):** the original summary's #1 gap — "MCTS does not
> exist" — was wrong. MCTS is real: [`tcconnally/MCTS`](https://github.com/tcconnally/MCTS),
> a fork of canonical [`MCP-Audit/MCTS`](https://github.com/MCP-Audit/MCTS), with
> ~120 analyzers, a 365-passing-test suite, SARIF/HTML/JSON output, a GitHub
> Action, and a behavioral-eval regression harness. The error was methodological:
> GitHub repo search omits forks unless you pass `fork:true`, so a
> `user:tcconnally` search missed it despite the README linking straight to it.
> Two other top gaps below were also closed by the autonomous pipeline since the
> first pass. The corrected picture is far less alarming than the original draft.

## Status of the originally-flagged top gaps

1. ~~**MCTS does not exist** (CRITICAL)~~ → **FALSE / WITHDRAWN.** MCTS exists and is a substantial, tested product. The "we scanned the top 20 MCP servers" campaign is *feasible*, not blocked — scans have already been run and issues filed against real MCP repos per the maintainer. The anchor stat should be computed from those existing results.

2. ~~**Both memory agents' engram quickstart crashes** (CRITICAL)~~ → **FIXED.** `EngramMemoryBackend` in perseus-agent-core was ported to speak MCP JSON-RPC stdio to Mneme (formerly Mimir; engram-rs successor): full `initialize` → `notifications/initialized` → `tools/list` → `tools/call` handshake, `MIMIR_BIN`/`ENGRAM_BIN` fallback. Both agents inherit it via the shared package.

3. ~~**rapid-agent "Gemini-powered" with no Gemini code** (HIGH)~~ → **FIXED.** The overclaim was removed from the README (rapid-agent PR #9).

## Remaining open items (all MEDIUM or lower)

| Repo | Severity | Claim | Reality |
|---|---|---|---|
| rapid-agent | MEDIUM | Hybrid semantic+keyword+vector search | Keyword + optional ELSER; no vector |
| rapid-agent | MEDIUM | ES\|QL aggregations in reflect | Plain search aggregations |
| qwen-memory | MEDIUM | "Elastic hybrid search" | Older standalone keyword backend (shared-lib refactor narrows this) |
| perseus | MEDIUM | Mneme (Mimir) tool names in README | 4+ example names don't exist (count of 23 is right) |
| pr-pilot | MEDIUM | "90%+ autonomous" | Pipeline real; figure unmeasured |
| pr-pilot | MEDIUM | "verifies fixes without human intervention" | No recorded end-to-end run artifact |
| blast-radius | MEDIUM→partly fixed | Live `@blast-radius` on GitLab Duo | Was offline-only; PR #42 "make the agent runnable" closes much of this |
| anna-recall | MEDIUM→fixed | tests workflow never ran | Branch trigger corrected to `master` |
| anna-recall | LOW | Mneme (Mimir) "battle-tested" | Mneme (Mimir) is ~1 week old |
| qwen-memory | LOW | "gets smarter every time" | Mechanism real, adverb unmeasured |
| MCTS | — | "120 analyzers, self-testing" | Verified ✓ (4 Windows-only test-env failures, not product) |
| mimir | — | All claims verified | 23 tools, FTS5, fully local, MCP handshake smoke-tested ✓ |

## Engineering gaps from the deep-dive reviews — confirmed FIXED on main

- qwen-memory "timely forgetting" → real decay with write-back + `min_confidence=0.3` recall filter.
- qwen-memory unbounded prompt growth → token-budgeted recall context.
- qwen-memory main crashed on import (bad merge) → fixed (PR #10).
- rapid-agent Elastic stub → real Elasticsearch backend with ELSER fallback (PR #7).
- anna-recall deadlock / dead-Mneme (Mimir) handling → reader-thread drain, stderr DEVNULL, restart-once + typed error; 15/15 tests.
- MCTS per-analyzer error isolation + SARIF URI normalization → landed from the code review.

## Net read

Corrected, the portfolio is in strong shape. The two original CRITICALs were
either a research error on my part (MCTS) or already fixed (engram); the HIGH
was fixed too. What genuinely remains is a single editing pass on overclaims
(vector search, ES|QL, "90%", "battle-tested") and the perseus README's wrong
Mneme (Mimir) tool names. No discovered-fiction landmines left.
