# Cross-Portfolio Claims Audit — Summary

**Date:** 2026-06-12 · **Repos:** perseus, perseus-rapid-agent, perseus-qwen-memory, pr-pilot, blast-radius-agent, anna-recall, mimir (no MCTS repo exists)
**Method:** every capability claim in README/AGENTS/site copy traced to implementing code; per-repo detail in each repo's `CLAIMS-AUDIT.md` on branch `audit/claims-verification`.

## The 3 most judge-visible gaps

1. **MCTS does not exist** (perseus, CRITICAL). The site and perseus README sell "120 security analyzers, one command" with scan-output mockups and a self-testing claim — and there is no repository, no package, no code. This is the single largest discovered-fiction risk in the portfolio, and it blocks the planned "we scanned the top 20 MCP servers" campaign entirely.

2. **Both memory agents' engram quickstart crashes on current engram-rs** (rapid-agent + qwen-memory, CRITICAL). `EngramMemoryBackend` shells out to CLI verbs (`store`, `recall`) that engram-rs v0.5.0 removed — only `engram serve` remains. The literal README command `MEMORY_BACKEND=engram python -m agent.main` dies with `unrecognized subcommand 'recall'` on a current install. Fix once in perseus-agent-core (port to the serve API or pin a version), and both repos inherit it.

3. **rapid-agent is "Gemini-powered" with zero Gemini code** (HIGH). Defensible architecture (Agent Builder supplies the model), but one judge question — "show me the Gemini call" — has no good answer today. One README sentence fixes it.

## Full severity table

| Repo | Severity | Claim | Reality |
|---|---|---|---|
| perseus | CRITICAL | MCTS: "120 analyzers, one command" | No code exists anywhere |
| rapid-agent | CRITICAL | Engram quickstart works | Crashes vs engram-rs 0.5.0 CLI surface |
| qwen-memory | CRITICAL | Engram quickstart works | Same crash |
| qwen-memory | CRITICAL→FIXED | Demo runs | main crashed on import (bad merge); fixed in PR #10 today |
| rapid-agent | HIGH | "Gemini-powered" | No Gemini/Vertex call in repo |
| blast-radius | HIGH | Live `@blast-radius` mentions on GitLab Duo | Only offline mode is demonstrable; manifest keys unconfirmed |
| rapid-agent | MEDIUM | Hybrid semantic+keyword+vector search | Keyword + optional ELSER; no vector |
| rapid-agent | MEDIUM | ES\|QL aggregations in reflect | Plain search aggregations |
| qwen-memory | MEDIUM | "Elastic hybrid search" | Older standalone keyword backend |
| perseus | MEDIUM | Mimir tool names in README | 4+ example names don't exist (count of 23 is right) |
| pr-pilot | MEDIUM | "90%+ autonomous" | Pipeline real; figure unmeasured |
| pr-pilot | MEDIUM | "verifies fixes without human intervention" | No recorded end-to-end run artifact |
| anna-recall | MEDIUM | (CI hygiene) tests workflow | Triggers on `main`; default branch is `master` — never ran |
| anna-recall | LOW | Mimir "battle-tested" | Mimir is 6 days old |
| qwen-memory | LOW | "gets smarter every time" | Mechanism real, adverb unmeasured |
| mimir | — | All claims verified | 23 tools, FTS5, fully local, MCP handshake smoke-tested |

## Previously-flagged gaps now confirmed FIXED on main

- qwen-memory "timely forgetting" → real decay with write-back + `min_confidence=0.3` recall filter (Q-2 fix).
- qwen-memory unbounded prompt growth → token-budgeted recall context.
- rapid-agent Elastic stub → real Elasticsearch backend with ELSER fallback (PR #7).
- anna-recall deadlock / dead-Mimir handling → reader-thread drain, stderr DEVNULL, restart-once + typed error; 15/15 tests.

## Net read

The portfolio's code is in much better shape than its copy. Most engineering
gaps from the deep-dive reviews are genuinely fixed; what remains is mostly
overclaiming (vector search, ES|QL, 90%, "battle-tested") that one editing
pass can fix — plus the two structural items: the MCTS fiction and the engram
CLI drift.
