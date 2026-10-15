# Caro AI engine approaches

Research for [#3](https://github.com/trai-ran-dong-tam/online-gomoku/issues/3). Facts were checked against primary sources (repos, source code, papers, official sites) on 2026-10-15. Anything not confirmed at a primary source is marked **unverified**. This note lays out options and trade-offs; the choice belongs to later grilling tickets.

## Answer

- **The strongest open engines use alpha-beta search with a small, fast neural evaluation (NNUE-style) plus a threat search (VCF) at the leaves.** Rapfi, built that way, won every Gomocup 2026 league except Caro, and its paper reports it beats the AlphaZero-style KataGomo by about 400 Elo on CPU only.
- **Caro is the catch.** Rapfi (GPL-3.0) has no Caro rule in its source. The open engines that do support Caro are KataGomo (MIT-style license, AlphaZero-style, GPU-oriented, Caro on a separate branch) and AlphaGomoku (GPL-3.0, which won the Gomocup 2026 Caro league). Embryo and Jax also play Caro but publish no source that this research found.
- **Browser play is proven:** Gomocalc runs Rapfi as WebAssembly in the browser (about 1.1 MB of wasm plus a 40 MB weight file, or a 95 KB classical-eval fallback). Threads need cross-origin isolation headers.
- **License shapes placement.** Running a GPL engine on our server does not oblige us to publish source. Shipping it to browsers is distribution, so the source (and our changes) must be offered under the GPL. The MIT-style KataGomo license has no such condition beyond keeping the notice.
- **Strength levels and Coach features fall out of standard engine output.** Rapfi already has a Stockfish-style `STRENGTH` setting (shallower depth plus randomised pick among the top moves), multi-PV via `YXNBEST`, and depth and node limits. Evaluation drops between consecutive moves give mistakes for review; VCF/VCT results and multi-PV give threats and hints.
- **Build vs. adopt is a real choice.** A homemade alpha-beta plus pattern evaluation plus VCF engine is a well-documented, learnable project that handles the Caro rule natively and could be strong enough for casual players. Adopting gives top strength but needs either Caro work on Rapfi, a GPU-leaning engine (KataGomo), or a GPL engine with Caro (AlphaGomoku).

## Rule support

The Caro rule is not one thing. Two definitions appear in primary sources:

- Gomocup Caro: "the winner must have an unbroken row of exactly five stones that is not blocked at both ends", so neither `oxxxxxo` nor `xxxxxx` wins, on 15x15 ([Gomocup rules](https://gomocup.org/detail-information/)).
- KataGomo `SIXWINRULE_CARO`: "oxxxxxx. is win, oxxxxxxo is not win", so an overline wins unless blocked at both ends, plus a separate `wallBlock` flag for whether the board edge counts as a blocking stone ([KataGomo Caro2024 `rules.h`](https://github.com/hzyhhzy/KataGomo/blob/Caro2024/cpp/game/rules.h)).

So the team's own rule (overlines, board edge, board size) must be fixed before any engine is chosen; an engine must match it exactly or its evaluation and threat search are wrong.

| Engine | Rules in source | Caro? | Source |
| --- | --- | --- | --- |
| Rapfi | `FREESTYLE`, `STANDARD`, `RENJU` | No. Gomocup rule id 8 (Caro) hits the default branch and "Rule is reset to freestyle" | [`types.h`](https://github.com/dhbloo/rapfi/blob/master/Rapfi/core/types.h), [`gomocup.cpp`](https://github.com/dhbloo/rapfi/blob/master/Rapfi/command/gomocup.cpp) |
| KataGomo | six-win rule always / never / Caro, wall block, VCN variants | Yes, on the `Caro2024` (and `GomCaro`) branches | [rules.h](https://github.com/hzyhhzy/KataGomo/blob/Caro2024/cpp/game/rules.h), [branches](https://github.com/hzyhhzy/KataGomo/branches) |
| AlphaGomoku | `FREESTYLE`, `STANDARD`, `RENJU`, `CARO5`, `CARO6` | Yes. That `CARO5` means exactly five and `CARO6` means five or more is inferred from the names: **unverified** | [`rules.hpp`](https://github.com/MaciejKozarzewski/AlphaGomoku/blob/master/include/alphagomoku/game/rules.hpp) |
| Embryo | Caro version since 2020, Caro5 fixes in 2023 | Yes | [Gomocup AI download page](https://gomocup.org/download-gomoku-ai/) |
| Jax, Squirrel, Pela, Lavender | Caro listed among supported rules | Yes | [Gomocup AI download page](https://gomocup.org/download-gomoku-ai/) |

The Piskvork/Gomocup engine protocol encodes rules as a bitmask: "1=exactly five in a row win, 2=continuous game, 4=renju, 8=caro", with Caro added on 2023-03-20 ([protocol spec](https://plastovicka.github.io/protocl2en.htm)).

Board size: Rapfi's maximum is 22x22 (32-cell stride minus a 5-cell wall on each side) ([`pos.h`](https://github.com/dhbloo/rapfi/blob/master/Rapfi/core/pos.h)). Gomocup runs 15x15 and 20x20 ([Gomocup rules](https://gomocup.org/detail-information/)). If the team wants a larger or unbounded board, engine choice narrows further (not checked for each engine: **unverified**).

## Gomocup 2026 results

Gomocup 2026 was the 27th tournament, held 5 to 7 June 2026 ([results index](https://gomocup.org/results/)). From the [2026 results page](https://gomocup.org/results/gomocup-result-2026/):

- Freestyle 20x20, Freestyle 15x15, Fastgame, Standard, Renju: Rapfi 1st, KataGomo 2nd, AlphaGomoku (Kozarzewski) 3rd.
- Caro: AlphaGomoku 1st, Jax 2nd, Embryo 3rd. Rapfi and KataGomo are not listed in the Caro results (KataGomo's 2026 Gomocup entry notes "the Caro rule is now supported" on the [download page](https://gomocup.org/download-gomoku-ai/), so its absence from the top three is unexplained: **unverified**).
- Main leagues ran at 300 s per move and 1000 s per match. Each engine is pinned to one CPU core and pondering is banned ([Gomocup rules](https://gomocup.org/detail-information/)), so these rankings reflect single-core strength, close to a cheap server.

## Notable open-source engines

| Engine | Approach | License | Activity | Notes |
| --- | --- | --- | --- | --- |
| [Rapfi](https://github.com/dhbloo/rapfi) | Alpha-beta with classical and NNUE ("Mixnet") evaluation, VCF at leaves; an MCTS searcher also exists in `Rapfi/search/mcts` | GPL-3.0 | last push 2026-09-01 | WebAssembly build documented; Piskvork protocol plus Yixin-Board extensions |
| [KataGomo](https://github.com/hzyhhzy/KataGomo) | KataGo fork: AlphaZero-style MCTS with a ResNet | KataGo's MIT-style license (permission notice must be kept); bundled third-party libraries have their own licenses | last push 2026-10-04 | One branch per game or rule; backends inherited from KataGo (OpenCL, CUDA, TensorRT, Eigen CPU) per its [README](https://github.com/hzyhhzy/KataGomo/blob/Caro2024/README.md) |
| [AlphaGomoku](https://github.com/MaciejKozarzewski/AlphaGomoku) | "AlphaZero for the game of Gomoku" | GPL-3.0 | v5.9.3, 2025-08-25 | Caro5/Caro6 rules; release zips about 28 MB; GPU-capable ([releases](https://github.com/MaciejKozarzewski/AlphaGomoku/releases)) |
| Carbon, Stahlfaust, XL, Chis, PentaZen | Older classical engines | Stahlfaust is GPL; others not checked (**unverified**) | old | None lists Caro support on the [Gomocup download page](https://gomocup.org/download-gomoku-ai/) |

Embryo, Jax and Yixin are listed on Gomocup's download page with no source link ([download page](https://gomocup.org/download-gomoku-ai/)). Embryo's author list includes names of Stockfish authors (Costalba, Kiiski, Linscott, Romstad), which suggests a Stockfish-derived codebase; its license is **unverified**.

## Techniques and primary papers

- **Threat-space search and proof-number search.** Allis, van den Herik and Huntjens describe Victoria, which uses threat-space search and proof-number search and wins as first player against any defence in Go-Moku ([Go-Moku and threat-space search, 1993](https://www.renju.se/rif/proof/Go-Moku.pdf); [Tilburg listing](https://research.tilburguniversity.edu/en/publications/go-moku-and-threat-space-search/)). This is the root of VCF (victory by continuous fours) and VCT (victory by continuous threats) solvers. Practical meaning: Caro is full of forced sequences, and a cheap VCF search finds them reliably.
- **Alpha-beta with pattern evaluation.** The classical design: score each line by pattern (open three, four, etc.), search with alpha-beta and move ordering. Rapfi's paper describes alpha-beta with "a VCF (Victory by Continuous Fours) search at each leaf node similar to the quiescence search" ([Rapfi paper](https://arxiv.org/html/2503.13178v1)).
- **NNUE-style neural evaluation.** Rapfi's Mixnet distils a CNN into a pattern codebook with incremental updates. Measured speeds: Mixnet Small 51K FLOPs and 428K alpha-beta nodes/s, versus 1,758 nodes/s for the smallest ResNet (4 blocks, 64 filters). The authors report about 400 Elo over KataGomo "in a CPU-only environment", first place among 520 agents on Botzone, and the 2024 Gomocup title ([Rapfi paper](https://arxiv.org/html/2503.13178v1)). The paper does not name the CPU for the speed table.
- **AlphaZero-style MCTS with self-play.** KataGo's methods are in [Accelerating Self-Play Learning in Go](https://arxiv.org/abs/1902.10565); KataGomo applies them to Gomoku variants. Strong, but each evaluation is a large network, so it wants a GPU; the Rapfi authors used KataGomo's CPU build because it "is primarily designed for GPUs" ([Rapfi paper](https://arxiv.org/html/2503.13178v1)).
- **Human-like models.** KataGo v1.15.0 added a "Human SL" model "trained on a large number of human games to predict human moves across players of different ranks and time periods", configurable to imitate e.g. 5-kyu play ([KataGo v1.15.0](https://github.com/lightvector/KataGo/releases/tag/v1.15.0)). That is Go, not Caro; no human-game Caro model was found (**unverified** that none exists). It would need a corpus of human Caro games, which the game itself could collect over time.

## Browser (WebAssembly) vs. server

**Browser evidence.** Rapfi documents an Emscripten build with `USE_WASM_SIMD`, `USE_WASM_SIMD_RELAXED` and `NO_MULTI_THREADING` options ([Rapfi README](https://github.com/dhbloo/rapfi)). Its web front end, Gomoku Calculator, ships four builds (single or multi-threaded, with or without SIMD), plus a fallback engine without NNUE "to reduce the server cost of repetitively downloading the large NNUE weight file" ([gomoku-calculator README](https://github.com/dhbloo/gomoku-calculator)). Measured on gomocalc.com on 2026-10-15 (HTTP `content-length`):

- `rapfi-multi-simd128.wasm`: 1,137,577 bytes; `rapfi-single.wasm`: 1,127,612 bytes.
- `rapfi.data` (weights): 40,306,406 bytes; fallback `rapfi.data`: 94,776 bytes.
- The site sends `Cross-Origin-Opener-Policy: same-origin` and `Cross-Origin-Embedder-Policy: require-corp`. Shared memory for threads requires a secure, cross-origin isolated document ([MDN SharedArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer#security_requirements)), and those headers constrain how the app embeds third-party content.

**Speed.** A general study found code compiled to WebAssembly "run slower by an average of 45% (Firefox) to 55% (Chrome)" than native on SPEC CPU ([Jangda et al., USENIX ATC 2019](https://www.usenix.org/conference/atc19/presentation/jangda)). No published per-move benchmark of Rapfi in a phone browser was found: **unverified**, and worth a prototype.

**Trade-offs.**

| | Browser | Server |
| --- | --- | --- |
| Hosting cost | Zero CPU on our side; download bandwidth for weights | CPU per bot move, competes with the game server within ~$25/month |
| Phones | Weak CPUs, battery, 40 MB weights on mobile data unless a small eval is used | Same speed for every player |
| Cheating | Engine in every client; but strong engines are already public (gomocalc.com) | Engine only where we allow it |
| Rated games, Coach off | Must be enforced by not loading the engine, not by trust | Enforced server-side |
| Bot players for load tests | Not usable server-side unless also built natively | Natural fit for synthetic load |
| GPL | Shipping is distribution: offer source | Running it is not distribution |

**Compute per move (estimate, not measured).** At Rapfi's reported 100K to 430K nodes/s for Mixnet on a desktop CPU core with AVX2 ([Rapfi paper](https://arxiv.org/html/2503.13178v1)), 0.1 to 1 s per move is tens of thousands to hundreds of thousands of nodes, ample for casual-strength bots. A simple homemade engine will be far slower per node, but casual bots need shallow searches. Real numbers for the target VPS and for phones need a benchmark.

## Strength levels

- **Time, depth or node limits.** Gomocup protocol gives `timeout_turn` and `timeout_match` ([protocol spec](https://plastovicka.github.io/protocl2en.htm)); Rapfi adds `MAX_DEPTH` and `MAX_NODE` ([`gomocup.cpp`](https://github.com/dhbloo/rapfi/blob/master/Rapfi/command/gomocup.cpp)). Simple, but a depth-limited engine still never misses a short forced win, so low levels can feel inhumanly sharp in tactics.
- **Noise among top moves.** Rapfi's `STRENGTH` (0 to 100) uses a `SkillMovePicker` "code from stockfish": below 100 it caps depth (from 16 down to 4), widens multi-PV to at least 2 to 6 lines, and adds a random bonus scaled by a weakness factor between 0.1 and 0.9 ([`skill.h`](https://github.com/dhbloo/rapfi/blob/master/Rapfi/search/skill.h)).
- **Human-like models.** Imitate human moves by rating (see KataGo Human SL above). Needs human data.
- **Hand-tuned weaknesses** for a homemade engine: e.g. sometimes ignore the opponent's open three, or skip VCF. Cheap and explainable, untested here.

## Engine output for Coach and review

Output that the engines expose:

- **Evaluation and principal variation.** Rapfi reports depth, selective depth and the PV for each multi-PV line during search ([`searchoutput.cpp`](https://github.com/dhbloo/rapfi/blob/master/Rapfi/search/searchoutput.cpp)). `YXNBEST` asks for the N best moves (multi-PV), `YXSEARCHDEFEND` searches defences, and `YXBALANCEONE` and `YXBALANCETWO` find balancing moves ([`gomocup.cpp`](https://github.com/dhbloo/rapfi/blob/master/Rapfi/command/gomocup.cpp)). The Yixin-Board extension commands are documented by the Yixin protocol, linked from the [Rapfi README](https://github.com/dhbloo/rapfi).
- **Win/loss proofs.** VCF/VCT search returns a forced winning sequence when one exists ([Allis et al.](https://www.renju.se/rif/proof/Go-Moku.pdf)).

How that maps to the Coach (a design sketch, not tested):

- **Hint:** the top multi-PV move, or a softer hint such as the region or the threat to answer.
- **Threat highlighting:** pattern detection alone (fours, open threes, double threats) needs no deep search and runs anywhere, even in plain TypeScript; a VCF result can highlight "you can force a win" or "you must block here".
- **Post-game review:** evaluate every position, flag moves where the evaluation drops past a threshold as mistakes, show the engine's PV as the better line, and flag missed forced wins found by VCF. This is how chess review tools work in general; specific tools were not checked (**unverified**).
- **LLM explanation** (budget under ~$10/month): the engine supplies facts (eval swing, PV, threat type) and an LLM could phrase them, only for flagged moves to keep calls few.

## Build our own vs. adopt

| Option | Caro rule | Strength | Runs | License | Learning |
| --- | --- | --- | --- | --- | --- |
| Homemade: pattern eval, alpha-beta, VCF | Exactly our rule | Casual to decent, unknown vs. top engines | Any language; TypeScript runs in browser and server | Ours | High: search, move ordering, transposition tables, threat search, testing a solver |
| Rapfi, adapted | Needs Caro added to the win check, pattern tables and evaluation; NNUE weights are trained per rule, so Caro likely needs new training (**unverified** effort) | Top (on CPU) | Native or WebAssembly | GPL-3.0 | Medium: C++ codebase, training pipeline |
| KataGomo Caro branch | Yes, configurable | Strong; best with GPU | Native; no browser build found (**unverified**) | MIT-style | Medium: KataGo internals, self-play training |
| AlphaGomoku | Caro5/Caro6 | Gomocup 2026 Caro champion | Native, GPU-capable | GPL-3.0 | Low to medium |
| Closed engines (Embryo, Jax) | Yes | Strong | Windows binaries for Gomocup | **unverified** | Low; redistribution rights unclear |

Points that cut across options:

- **Any engine behind the Piskvork protocol is swappable.** The protocol is stdin/stdout lines ([protocol spec](https://plastovicka.github.io/protocl2en.htm)). The GPL FAQ says pipes, sockets and command-line arguments are "communication mechanisms normally used between two separate programs" ([GPL FAQ #MereAggregation](https://www.gnu.org/licenses/gpl-faq.en.html#MereAggregation)), so a game server talking to a GPL engine over a pipe can keep its own license. A homemade engine could start behind the same interface and be replaced later, or the reverse.
- **GPL on a server vs. in the browser.** "The GPL permits anyone to make a modified version and use it without ever distributing it to others", but programs "distributed to the user when they visit the web site" must have their source released under the GPL ([GPL FAQ #UnreleasedMods](https://www.gnu.org/licenses/gpl-faq.en.html#UnreleasedMods)). This repo is already public, so offering source is easy, but GPL code compiled into the client could pull the client under the GPL too (legal reading, not checked with a lawyer).
- **Solved-game caveat.** Go-Moku variants are first-player wins under perfect play ([Allis et al.](https://www.renju.se/rif/proof/Go-Moku.pdf)); whether Caro on the team's board is solved was not checked (**unverified**). Strong bots playing first may feel unbeatable, which matters for bot fairness and opening rules.

## Open questions for grilling

- The exact Caro rule: exactly five or overline, board edge as a block, board size.
- Where bots and Coach run: server, browser, or both (e.g. pattern highlighting in the browser, search on the server).
- Build, adopt, or a homemade engine first with a swappable protocol.
- How many strength levels, and whether low levels should feel human rather than random.
