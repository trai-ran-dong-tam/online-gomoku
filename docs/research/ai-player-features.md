# AI-powered player features in online board games

Research for issue #4. Question: what AI-powered player features do successful online board games offer, and what would each cost us to run against a ~$10/month LLM budget?

Prices, model names and other facts were checked on 2026-10-15. Prices change often (two Gemini prices are already scheduled to double, see below), so recheck before committing to a model. Anything marked **unverified** could not be traced to a primary source.

## Answer

- **The valuable features in chess are engine features, not LLM features.** Game review with move labels, "retry your mistakes", puzzles from your own games, hints and bots at several strengths all run on a game engine plus simple rules. They cost CPU (hosting budget), not LLM tokens. chess.com and lichess build reviews this way, and lichess's thresholds are open source.
- **LLM explanations are cheap if grounded, unreliable if not.** A grounded post-game review (engine finds the key mistakes, the LLM only puts them into words) is about 3,500 input and 1,000 to 2,000 output tokens. On the cheapest current models (Claude Haiku 5.5, OpenAI gpt-6-luna) that is about $0.001 per review, so $10 buys roughly 7,000 to 12,000 reviews; on mid-tier models (Claude Sonnet 5.5, OpenAI gpt-6.1-sol) about 370 to 590. Ungrounded LLM chess commentary gets a large share of claims wrong (22% for GPT-5.4 without tools in a 2026 paper).
- **Vietnamese quality is unverified.** No vendor publishes a Vietnamese score for current models: Anthropic's and OpenAI's multilingual tables cover 14 languages that do not include Vietnamese. The team would need its own small Vietnamese eval set.
- **Engine choice is a constraint for the Coach.** Rapfi, the strongest open Gomoku engine, supports freestyle, standard and renju rules but not the Caro rule (exactly five, not blocked at both ends). AlphaGomoku (GPL-3.0) does support Caro. Both are GPL-3.0.
- **Chat moderation options are shrinking.** Google's Perspective API shuts down on 2026-12-31 and never listed Vietnamese. OpenAI's moderation endpoint is free but its docs name no supported languages. A small LLM classifier costs about $0.000025 per message, or preset phrases avoid free text entirely.
- **Human-like bots (Maia) need a big human game corpus** (Maia used over 100 million games). A new Caro site will not have that; strength-limited engine bots are the realistic v1 option.
- **Ranking** (player value against running cost, detailed at the end): engine review and retry-mistakes, then bots with strength levels, then engine hints in unrated games, then grounded LLM explanations, then puzzles from own games, then chat moderation (needed only if free-text chat exists), then LLM onboarding, LLM spectator commentary and human-like bots last.

## What successful games offer

### chess.com Game Review and Coach

- Game Review labels every move from Brilliant down to Blunder (with Great, Best, Excellent, Good, Book, Inaccuracy, Mistake and Miss in between), shows a game graph of who was ahead, and gives an Accuracy score from 0 to 100 based on how closely moves matched the engine ([chess.com support: Game Review](https://support.chess.com/en/articles/8584089-what-is-game-review)).
- A Coach comments on each move and can read the feedback aloud; key moments are highlighted, and when a key move could have been better the player can "Retry" it and get feedback on the new choice ([same article](https://support.chess.com/en/articles/8584089-what-is-game-review)).
- Free members get basic review; unlimited review needs Premium ([same article](https://support.chess.com/en/articles/8584089-what-is-game-review)). The exact free daily limit (often quoted as one per day) is **unverified** on a current first-party page.
- Game Review v2 added threat explanations (which threats a move creates or allows), board highlights tied to words in the explanation, and an end-of-game report card with a summary, a performance rating and per-phase grades. It also tightened "Blunder" to require an evaluation drop plus material loss or allowed mate, and added "Miss" for missed tactics ([chess.com news: Game Review v2](https://www.chess.com/news/view/chesscom-launches-game-review-v2)).
- How the Coach text is produced (templates or an LLM) is **unverified**: neither the support article nor the v2 announcement says.

### lichess analysis, "learn from your mistakes", puzzles (open source)

- Lichess analysis is powered by Stockfish ([lichess FAQ](https://lichess.org/faq)).
- Mistake labels are a fixed rule on the change in "winning chances" (a -1 to 1 scale derived from the engine score): a drop of at least 0.1 is an Inaccuracy, 0.2 a Mistake, 0.3 a Blunder; there are separate labels for creating or losing a forced mate ([lila `Advice.scala`](https://github.com/lichess-org/lila/blob/master/modules/tree/src/main/Advice.scala)).
- "Learn from your mistakes" picks the player's moves where winning chances swung by more than 0.1 (or a short forced mate was lost) ([lila `nodeFinder.ts`](https://github.com/lichess-org/lila/blob/master/ui/analyse/src/nodeFinder.ts)), asks the player to find a better move, and checks the answer with the in-browser engine: the try counts if it loses less than 0.04 winning chances compared with the position before the mistake ([lila `retroCtrl.ts`](https://github.com/lichess-org/lila/blob/master/ui/analyse/src/retrospect/retroCtrl.ts)). No LLM is involved.
- Lichess puzzles are generated from its own game database: a generator uses Stockfish to find candidate positions and a validator stores them for review ([lichess-puzzler README](https://github.com/ornicar/lichess-puzzler)). Lichess publishes the puzzles and games as open data ([lichess-org/database](https://github.com/lichess-org/database)).

### Human-like bots: Maia

- Maia is an AlphaZero-style network trained on human games to predict the move a human at a given rating would play, instead of the best move ([McIlroy-Young et al., KDD 2020](https://arxiv.org/abs/2006.01855)).
- Maia predicts the exact human move up to 53% of the time, against about 43% for Leela and 38% for Stockfish; nine versions (1100 to 1900) were trained on over 100 million lichess games, and each plays above the rating it was trained on because it plays the average move of its band ([lichess blog: Introducing Maia](https://lichess.org/blog/X9PUixUAANCqFRSh/introducing-maia-a-human-like-neural-network-chess-engine)).
- Maia-2 replaces the per-rating models with one skill-aware model ([Tang et al., NeurIPS 2024](https://arxiv.org/abs/2409.20553)).
- Take Take Take (a chess app) uses Maia in its coach to tell move quality apart from move difficulty ([AI Engineer talk: Building a Chess Coach](https://ai.engineer/talks/building-a-chess-coach)).
- Relevance to us: the method depends on a large corpus of rated human games per skill band. A new Caro site starts with none, so this is a later-stage feature at best.

### Adaptive and personality bots

- chess.com has over 100 bot personalities, all powered by the Komodo engine, including adaptive bots that get stronger when you play well and ease off when you make mistakes, and bots modeled on streamers and top players ([chess.com support: Play bots](https://support.chess.com/article/694-how-can-i-play-against-the-chess-com-computer)).
- Bot games are unrated; hints and takebacks are allowed and only reduce a "crowns" score. Optional aids include an evaluation bar, threat arrows, suggestion arrows and move feedback ([same article](https://support.chess.com/article/694-how-can-i-play-against-the-chess-com-computer)). This matches the Coach rule (help only outside rated games).
- Personalities are engine settings plus names, avatars and canned chat; none of this needs an LLM.

### LLM-written move explanations

What goes wrong:

- LLMs write fluent commentary but hallucinate because they reason poorly about the game; concept-guided generation (feeding the LLM engine-derived concepts) produced commentary that humans rated accurate and informative ([Kim et al., NAACL 2025, arXiv 2410.20811](https://arxiv.org/abs/2410.20811)).
- Breaking chess commentary into atomic claims and checking each with engine tools: GPT-5.4 without tools made incorrect sub-claims 22.0% of the time, smaller open-weight models over 40%; tool access "substantially improves factual correctness" ([Hebbar et al., arXiv 2608.04240, 2026](https://arxiv.org/abs/2608.04240)).

How a shipped product grounds it (Take Take Take, per its engineers' talk; the figures are the team's own and **unverified**):

- "Analysis first, language generation second": Stockfish finds the best moves, tactical and positional detectors name the concepts (forks, pins and so on), Maia estimates how hard the move was, and the LLM only turns these facts into text, because letting it reason on its own "quickly lead to hallucination" ([AI Engineer talk](https://ai.engineer/talks/building-a-chess-coach)).
- They test with 16 scenarios from real games and an LLM judge; they report Gemini Flash passing about 75% and Claude with more thinking just under 60% at much higher latency, and say hallucinations still occur ([same talk](https://ai.engineer/talks/building-a-chess-coach)).

For Caro the equivalent detectors would be pattern names (open three, four, double-three style threats). Building them is engine-side work, not LLM work.

### Spectator commentary and onboarding

- No first-party source was found for LLM spectator commentary or LLM tutorials on chess.com or lichess (**unverified**, not found in this survey). chess.com's tutorial-like aids during bot games are engine overlays (threat and suggestion arrows, move feedback) ([chess.com support: Play bots](https://support.chess.com/article/694-how-can-i-play-against-the-chess-com-computer)).
- Cost reasoning (ours, not sourced): live commentary is generated per game, not per viewer, so cost scales with the number of watched games. At hint-sized calls (below) a 60-move commented game costs about $0.02 on the cheapest models, but on a small site few games have spectators, and a live engine evaluation shown to spectators of a rated game could help cheating if relayed to a player.

### Gomoku and Caro engines

- Gomocup, the main Gomoku AI tournament, has run a Caro league since 2023 using "an unbroken row of exactly five stones that is not blocked at both ends" on 15x15 ([Gomocup detail information](https://gomocup.org/detail-information/)). The 2025 Caro league was won by AlphaGomoku, ahead of JAX and Embryo; Rapfi won freestyle 15x15 ([Gomocup 2025 results](https://gomocup.org/results/gomocup-result-2025)).
- Rapfi is GPL-3.0, speaks the Piskvork protocol, can be built for WebAssembly (it powers the in-browser [Gomoku Calculator](https://gomocalc.com)), but its rule enum lists only freestyle, standard and renju ([Rapfi README](https://github.com/dhbloo/rapfi), [`types.h`](https://github.com/dhbloo/rapfi/blob/master/Rapfi/core/types.h)).
- AlphaGomoku is GPL-3.0 and its source contains Caro rule handling ([AlphaGomoku repo](https://github.com/MaciejKozarzewski/AlphaGomoku), `src/game/rules.cpp`). Whether it builds for the browser is **unverified**.
- GPL-3.0 requires shipping source with any distributed binary ([Rapfi README, license section](https://github.com/dhbloo/rapfi)). Sending a WASM build to browsers is distribution; running it only on our server is not (GPL-3.0 is not AGPL). This matters for where the engine runs.

## LLM cost

### Current prices (USD per million tokens, standard tier)

| Provider and model | Input | Output | Source |
|---|---|---|---|
| Claude Haiku 5.5 (`claude-haiku-5-5`, prompts up to 100K tokens) | 0.10 | 0.50 | [Anthropic pricing](https://platform.claude.com/docs/en/about-claude/pricing) |
| Claude Sonnet 5.5 (`claude-sonnet-5-5`) | 2.00 | 10.00 | [Anthropic pricing](https://platform.claude.com/docs/en/about-claude/pricing) |
| Claude Opus 5.5 (`claude-opus-5-5`) | 4.00 | 20.00 | [Anthropic pricing](https://platform.claude.com/docs/en/about-claude/pricing) |
| OpenAI gpt-5-nano | 0.05 | 0.40 | [OpenAI pricing](https://developers.openai.com/api/docs/pricing) |
| OpenAI gpt-6-luna | 0.10 | 0.50 | [OpenAI pricing](https://developers.openai.com/api/docs/pricing) |
| OpenAI gpt-6.1-sol | 2.00 | 10.00 | [OpenAI pricing](https://developers.openai.com/api/docs/pricing) |
| Gemini 3.1 Flash-Lite | 0.25 | 1.50 | [Gemini pricing](https://ai.google.dev/gemini-api/docs/pricing) |
| Gemini 3.5 Flash-Lite | 0.30 | 2.50 | [Gemini pricing](https://ai.google.dev/gemini-api/docs/pricing) |
| Gemini 3.8 Flash (until 2026-12-31) | 0.75 | 3.75 | [Gemini pricing](https://ai.google.dev/gemini-api/docs/pricing) |
| Gemini 3.8 Flash (from 2027-01-01) | 1.50 | 7.50 | [Gemini pricing](https://ai.google.dev/gemini-api/docs/pricing) |

Notes:

- All three providers offer a batch mode at about 50% off ([Anthropic](https://platform.claude.com/docs/en/about-claude/pricing), [OpenAI](https://developers.openai.com/api/docs/pricing), [Gemini](https://ai.google.dev/gemini-api/docs/pricing)). Batch results are asynchronous, so they suit reviews a player opens later, not a review shown right after the game.
- Gemini Flash and Flash-Lite models have a free tier, but on the free tier content is "Used to improve our products"; on the paid tier it is not ([Gemini pricing](https://ai.google.dev/gemini-api/docs/pricing)). That is a privacy question for player data.
- Gemini 2.5 models are still served but limited to past users; new projects are pointed to 3.5 Flash-Lite or 3.8 Flash ([Gemini models](https://ai.google.dev/gemini-api/docs/models)).
- Thinking tokens are billed as output. Current Claude models think by default; Claude Haiku 5.5 allows turning thinking off at effort `high` or below (per Anthropic's model reference bundled with the Claude API skill, cached 2026-10-06; see the [models overview](https://platform.claude.com/docs/en/about-claude/models/overview)). The "with thinking" columns below budget 1,000 extra output tokens for this.

### Token estimates (our assumptions, not measured)

- **Post-game review**: system prompt with Caro rules and output format about 1,500 tokens; game record about 250 tokens (60 moves in coordinate notation); engine facts for 5 key moments (position excerpt, played move, best move, evaluation swing, pattern names) about 1,750 tokens. Total about 3,500 input. Output: 5 short explanations plus a summary, about 1,000 tokens. Vietnamese text likely uses more tokens per word than English (**unverified**), which the round numbers are meant to absorb.
- **Hint**: about 2,000 input (system prompt plus one position and engine facts), about 150 output.
- **Chat moderation check**: about 200 input, about 10 output (a label).

### Cost per call and calls per $10

| Model | Review | Reviews per $10 | Review with thinking | Per $10 | Hint | Hints per $10 | Chat check | Checks per $10 |
|---|---|---|---|---|---|---|---|---|
| Claude Haiku 5.5 | $0.00085 | 11,765 | $0.00135 | 7,407 | $0.00028 | 36,364 | $0.000025 | 400,000 |
| OpenAI gpt-6-luna | $0.00085 | 11,765 | $0.00135 | 7,407 | $0.00028 | 36,364 | $0.000025 | 400,000 |
| OpenAI gpt-5-nano | $0.00057 | 17,391 | $0.00097 | 10,256 | $0.00016 | 62,500 | $0.000014 | 714,286 |
| Gemini 3.1 Flash-Lite | $0.00237 | 4,211 | $0.00387 | 2,581 | $0.00073 | 13,793 | $0.000065 | 153,846 |
| Gemini 3.5 Flash-Lite | $0.00355 | 2,817 | $0.00605 | 1,653 | $0.00098 | 10,256 | $0.000085 | 117,647 |
| Gemini 3.8 Flash (2026 price) | $0.00637 | 1,569 | $0.01013 | 988 | $0.00206 | 4,848 | $0.00019 | 53,333 |
| Gemini 3.8 Flash (2027 price) | $0.01275 | 784 | $0.02025 | 494 | $0.00413 | 2,424 | $0.00038 | 26,667 |
| Claude Sonnet 5.5 / OpenAI gpt-6.1-sol | $0.01700 | 588 | $0.02700 | 370 | $0.00550 | 1,818 | $0.00050 | 20,000 |
| Claude Opus 5.5 | $0.03400 | 294 | $0.05400 | 185 | $0.01100 | 909 | $0.00100 | 10,000 |

Computed as input tokens times input price plus output tokens times output price, at standard (non-batch) prices.

What this means: on the cheapest tier, $10 a month covers several thousand reviews, far more than a new site's traffic. On the mid tier it covers a few hundred, which needs a per-player daily cap. Quality per model in Vietnamese, not price, is the open question. LLM hints are hard to justify: an engine hint (best move plus highlighted threat) is free and cannot hallucinate.

### Vietnamese quality evidence

- Anthropic's multilingual table (Claude Sonnet 4.5 and Haiku 4.5, relative to English) covers 14 languages from a human-translated MMLU; Indonesian is the only Southeast Asian language (97.3% and 94.2%), and Vietnamese is not listed ([Anthropic multilingual support](https://platform.claude.com/docs/en/build-with-claude/multilingual-support)).
- OpenAI's multilingual MMLU uses the same 14 languages, also without Vietnamese ([openai/simple-evals results](https://github.com/openai/simple-evals/blob/main/multilingual_mmlu_benchmark_results.md)).
- VMLU is a Vietnamese benchmark suite (knowledge, reading, reasoning, conversation) published at ACL 2025, but it evaluates older models such as GPT-4, Llama-3 and Qwen2.5 ([VMLU, ACL 2025](https://aclanthology.org/2025.acl-long.563)).
- No source found on Vietnamese quality for game commentary specifically. Current-model Vietnamese quality is **unverified**; a small eval set of real Caro positions with Vietnamese reference explanations, reviewed by native speakers on the team, is the way to find out.

## Chat moderation options

| Option | Cost | Vietnamese | Notes |
|---|---|---|---|
| No free-text chat (preset phrases or emotes) | none | n/a | Removes the problem; less social. |
| Word list filter | none | must write our own | Lichess filters chat with per-language word lists (English, Russian, Spanish, Italian, Hindi, French, German, Portuguese, Turkish), no Vietnamese ([lila `Dictionary.scala`](https://github.com/lichess-org/lila/blob/master/modules/shutup/src/main/Dictionary.scala)). Misses obfuscated text. |
| OpenAI moderation (`omni-moderation-latest`) | free | not stated | 13 categories, text and images; docs name no supported languages ([OpenAI moderation guide](https://developers.openai.com/api/docs/guides/moderation)). Claims of better accuracy across 40 languages come from secondary write-ups (**unverified**; the announcement page refused automated access). |
| Perspective API | free | no | Shutting down: active until 2026-12-31, no new access requests after February 2026; its site lists no Vietnamese ([perspectiveapi.com](https://perspectiveapi.com/)). Not an option. |
| Small LLM as classifier | about $0.000025 per message on Haiku 5.5 or gpt-6-luna | needs testing | 400,000 checks per $10 at our estimate. Flexible (can catch Vietnamese teen-code and slurs) but needs the same eval work. |

## Ranking for a small game

Player value is a judgment from what the large sites chose to build; running cost is from the sections above. This ranks options for the later grilling ticket "Decide the AI features for v1"; it does not decide them.

| Rank | Feature | Player value | Running cost | Main risk |
|---|---|---|---|---|
| 1 | Engine post-game review: move labels, evaluation graph, key mistakes (lichess-style thresholds) | High (core of chess.com and lichess review) | No LLM; engine CPU per game, or zero server cost if run in the browser | Needs a Caro-capable engine and a win-chance mapping |
| 2 | Retry your mistakes (lichess "learn from your mistakes") | High | No LLM; reuses review data plus a short engine check | Same engine dependency |
| 3 | Bots at several strengths, optionally adaptive | High while player counts are low (always an opponent) | No LLM; engine CPU per bot move, or browser | Strength tuning, bot load on a small server |
| 4 | Engine hints in unrated games (best move, threat highlights) | Medium to high | No LLM | Must be blocked in rated games (Coach rule) |
| 5 | LLM explanations of key mistakes, grounded in engine facts, Vietnamese and English | Medium to high | About $0.001 per review on the cheapest tier; $0.02 to $0.03 on mid tier | Hallucination, unverified Vietnamese quality, needs pattern detectors and an eval set |
| 6 | Puzzles from players' own games | Medium | No LLM; batch engine job | Needs enough games and uniqueness checks |
| 7 | Chat moderation | Required only if free-text chat exists | $0 to about $0.000025 per message | Perspective is closing; Vietnamese support unclear everywhere |
| 8 | LLM onboarding tutor | Low to medium | Small per session | A scripted tutorial does the same job deterministically |
| 9 | LLM spectator commentary | Low on a small site | About $0.02 per commented game on the cheapest tier | Few spectators; live evaluation can aid cheating |
| 10 | Human-like (Maia-style) bots | High in chess | Training cost, not LLM | Needs a large human game corpus we will not have in v1 |
