# Stacks for a real-time multiplayer web game

Research for [#5](https://github.com/trai-ran-dong-tam/online-gomoku/issues/5). Facts were checked on 2026-10-15 against the linked sources. Claims marked **unverified** were not confirmed against a primary source in this pass. Claims marked **judgement** are opinion, not measurement. This file presents options and trade-offs; the choice belongs to the stack bake-off.

## Answer

- **Raw speed will not separate the candidates at our scale.** Caro sends a few small messages per move. Every runtime below has first-party evidence of handling far more than a hobby game needs (Phoenix: 2 million WebSocket connections on one box in 2015; Bun: about 700,000 messages per second in its own chat benchmark). The real differences are in what each teaches about HA, clustering and operations, and in how much you build yourself (**judgement**).
- **Game server shortlist: Elixir/Phoenix (Gleam as a typed variant), Go, Rust with axum.** Phoenix ships presence, pub/sub and clustering with no extra infrastructure. Go and Rust make you build those pieces, often with NATS or Valkey. Bun with TypeScript is a strong baseline if one language across client and server matters more than novelty.
- **Build our own game server rather than adopt Nakama or Colyseus.** Open-source Nakama runs on a single node only (clustering and HA are in Nakama Enterprise), which works against the HA learning goal. Colyseus (MIT, TypeScript) is the better reference design or fallback.
- **Web client shortlist: SvelteKit, React (Vite or Next.js), SolidStart. Board rendering: SVG or DOM first, PixiJS (WebGL/WebGPU) only if animation or a very large board needs it.**
- **AI engine shortlist: Rust or Zig compiled to WebAssembly.** Rust has the largest ecosystem, but the rustwasm GitHub org was sunset in 2025 and wasm-pack is being archived or handed off, so tooling is in flux.
- **Data shortlist: PostgreSQL as the system of record. Valkey (BSD-3) only if a quality target needs a cache or cross-node pub/sub; NATS only if the runtime lacks built-in clustering.** Redis is open source again (AGPLv3 option since Redis 8), so licensing is no longer a blocker for either.
- **Job market tension:** globally, Rust, Gleam, Elixir and Zig top the "admired" ranking, but Vietnamese job boards list very few Rust or Elixir jobs (3 and 1 on ITviec today versus 38 for Go and 98 for Java). Go is the only "new and modern" server option with visible local demand.

## Workload shape

A Caro match is two players alternating moves, with spectators and chat as possible extras. Each move is a tiny message, and players think for seconds between moves. So per-connection throughput is low; what matters is connection count, broadcast fan-out to spectators, presence, reconnection, and staying up through deploys and node failures. This is a **judgement** derived from the game's rules, not a measurement. The quality-targets ticket should replace it with numbers.

## 1. Game-server runtimes

### Elixir and Phoenix (Channels, Presence, PubSub)

- Phoenix reached about 2 million simultaneous WebSocket connections in 2015 on a 40-core, 128 GB server, limited by `ulimit` rather than the framework. After pooling PubSub servers, a broadcast to 2 million subscribers took about 1 second ([Phoenix blog, 2015](https://www.phoenixframework.org/blog/the-road-to-2-million-websocket-connections)). The benchmark is old and was run on large hardware.
- Presence is built on `Phoenix.Tracker`, which uses "a heartbeat protocol and CRDT to replicate presence information across a cluster" with "no single source of truth or global process" ([Phoenix.Tracker docs](https://phoenix-pubsub.hexdocs.pm/Phoenix.Tracker.html)). Presence only needs a running PubSub server, not a database or Redis ([Phoenix.Presence docs](https://phoenix.hexdocs.pm/Phoenix.Presence.html)).
- What it teaches: clustering, distributed presence and supervision come built in, so HA lessons are about BEAM distribution and node failure rather than wiring a message broker (**judgement**).
- Trade-off: smallest job market of the shortlist (see section 6).

### Gleam

- Gleam runs on the BEAM and can also compile to JavaScript; it can use packages written in Gleam, Erlang or Elixir ([gleam.run](https://gleam.run/)).
- It is new: 1.1% of 2025 Stack Overflow respondents used it ([SO 2025](https://survey.stackoverflow.co/2025/technology)). Whether Gleam has a mature equivalent of Phoenix Channels and Presence was not checked (**unverified**). A realistic path is Gleam for game logic inside a Phoenix app (**judgement**).

### Go

- WebSockets come from third-party libraries rather than the standard library (**unverified** for the current Go release). The common libraries are gorilla/websocket ("mature and widely used") and coder/websocket (ISC license, maintained by Coder since 2024, full permessage-deflate support) ([coder/websocket README](https://github.com/coder/websocket)).
- Go has no built-in presence or cluster pub/sub; you build them or add NATS or Valkey (**judgement**). That is more work but teaches more about the pieces.
- Strongest local job demand of the "new to the team" options (section 6). Nakama and NATS are themselves written in Go ([Nakama](https://github.com/heroiclabs/nakama), [NATS](https://github.com/nats-io/nats-server)).

### Rust (tokio and axum)

- axum (0.8.9) has a WebSocket module behind the `ws` feature, with `tokio-tungstenite` as an optional dependency ([axum docs](https://docs.rs/axum/latest/axum/extract/ws/index.html)).
- Like Go, presence and cross-node fan-out are yours to build (**judgement**). Rust is the most admired language in the 2025 Stack Overflow survey (72%) (figure as indexed from the [SO 2025 technology page](https://survey.stackoverflow.co/2025/technology); the admired chart did not render in my fetch).
- Sharing one language between the server and a WebAssembly AI engine is possible (section 4).

### Bun or Node (uWebSockets)

- Bun's WebSocket server is built on uWebSockets and has a native topic pub/sub API. Bun's own chat benchmark showed about 700,000 messages per second versus about 100,000 for Node with `ws` (Bun v0.2.1 vs Node 18.10, 16 clients) ([Bun docs](https://bun.com/docs/runtime/http/websockets)). This is a vendor benchmark on old versions.
- uWebSockets.js (Node binding) lists Apache-2.0, is installed from GitHub rather than the npm registry, and claims to be 20x faster than Socket.IO ([uWebSockets.js README](https://github.com/uNetworking/uWebSockets.js)).
- Trade-off: TypeScript end to end and the biggest ecosystem, but likely not "new to the team" (**judgement**). Pub/sub is per process; cross-node needs Valkey or NATS (**judgement**).

### Kotlin (Ktor)

- Ktor has a server WebSockets plugin (**unverified** in this pass). Kotlin was used by 10.8% of 2025 respondents ([SO 2025](https://survey.stackoverflow.co/2025/technology)). ITviec lists 13 Kotlin jobs today ([ITviec](https://itviec.com/it-jobs/kotlin)), likely mostly Android (**judgement**). No distinctive real-time advantage found over the shortlist.

### .NET (SignalR)

- Scaling SignalR across servers needs sticky sessions (unless single-server, Azure SignalR Service, or WebSockets-only with `SkipNegotiation`) and either Azure SignalR Service or a Redis backplane; Microsoft recommends the Redis backplane for self-hosted apps ([Microsoft Learn](https://learn.microsoft.com/en-us/aspnet/core/signalr/scale)).
- Mature and widely used (C# 27.8% in 2025, ASP.NET Core 19.7%) ([SO 2025](https://survey.stackoverflow.co/2025/technology)). The scale-out story leans on Azure, which may not fit a sub-$25 budget in a region near Vietnam (**judgement**).

## 2. Game-server frameworks vs. building our own

| | Nakama | Colyseus |
|---|---|---|
| License | Apache-2.0 ([repo](https://github.com/heroiclabs/nakama)) | MIT ([repo](https://github.com/colyseus/colyseus)) |
| Language | Go; server logic in Lua, TypeScript/JS or Go ([repo](https://github.com/heroiclabs/nakama)) | Node.js and TypeScript ([repo](https://github.com/colyseus/colyseus)) |
| Database | CockroachDB or a Postgres wire-compatible server ([repo](https://github.com/heroiclabs/nakama)) | none required |
| Built in | accounts, storage, friends, groups, chat, real-time and turn-based multiplayer, matchmaker, leaderboards, tournaments, parties, purchases, notifications ([repo](https://github.com/heroiclabs/nakama)) | rooms, state sync with delta compression and binary encoding, matchmaking, reconnection ([repo](https://github.com/colyseus/colyseus)) |
| Scaling | open source runs single-node; clustering, HA and multi-region are in Nakama Enterprise ([Heroic Labs benchmarks](https://heroiclabs.com/docs/nakama/getting-started/benchmarks/), [forum](https://forum.heroiclabs.com/t/questions-about-the-capabilities-of-nakama-open-source/1783), [Enterprise post](https://heroiclabs.com/blog/nakama-enterprise/)) | "with Redis and load balancers" ([repo](https://github.com/colyseus/colyseus)) |

Trade-offs:

- Nakama covers most of v1 (accounts, matchmaking, chat, leaderboards) out of the box, but its open-source edition cannot be clustered, so it would block the HA and failover lessons. Its feature list also includes things the map rules out (tournaments, friends, purchases).
- Colyseus is small and permissive, and its room and state-sync model is a good reference for our own protocol. It ties the server to TypeScript.
- Building our own on the chosen runtime teaches the protocol, reconnection, presence, matchmaking and clustering, which are the map's learning goals (**judgement**). Caro's game logic is small, so the framework saves less than it would for a fast-action game (**judgement**).

## 3. Web client and board rendering

Usage among all 2025 Stack Overflow respondents: React 44.7%, Next.js 20.8%, Vue.js 17.6%, Svelte 7.2%, Nuxt.js 4%, Phoenix 2.4% ([SO 2025](https://survey.stackoverflow.co/2025/technology)). SolidJS did not appear in the list I could read. The 2026 survey page did not expose web-framework figures in my fetch ([SO 2026](https://survey.stackoverflow.co/2026/technology)). ITviec lists 49 ReactJS jobs today ([ITviec](https://itviec.com/it-jobs/reactjs)).

- **React (Vite SPA or Next.js):** biggest ecosystem and job market. A real-time game client is mostly client-side state, so Next.js server features add little (**judgement**).
- **SvelteKit:** smaller bundles and simpler reactivity are commonly cited (**unverified**); clearly "new" for most teams.
- **SolidStart:** fine-grained reactivity similar to signals; smallest ecosystem of the three (**judgement**).
- **Vue or Nuxt:** viable, but no specific advantage for this game found.
- **Phoenix LiveView:** only if the server is Elixir. Every interaction round-trips to the server, which is acceptable for turn-based play but awkward for offline play against a WebAssembly bot (**judgement**).

Board rendering:

- A Caro board is a grid of a few hundred cells with occasional stone placements. SVG or DOM handles that easily, stays crisp at any zoom, and gives accessibility and hit-testing for free (**judgement**).
- PixiJS v8 renders 2D with WebGPU or WebGL ([pixijs.com](https://pixijs.com/)). It earns its place only for heavy animation, effects or a very large pannable board (**judgement**). Prototype on a low-end phone before choosing.

## 4. AI engine language (also compiles to WebAssembly)

- **Rust:** the most common choice for browser WebAssembly (**judgement**). Tooling is in flux: the Rust and WebAssembly working group was archived in 2024, the rustwasm GitHub org was sunset in 2025, `wasm-bindgen` moved to its own org with new maintainers, and `wasm-pack` and other tools were slated for archiving or transfer ([Inside Rust, 2025-07-21](https://blog.rust-lang.org/inside-rust/2025/07/21/sunsetting-the-rustwasm-github-org/)). The current status of `wasm-pack` was not checked (**unverified**). One language for server and engine is possible if the server is Rust.
- **Zig:** small, fast, with a WebAssembly target (**unverified** in this pass); 2.1% usage and 64% admired in 2025 ([SO 2025](https://survey.stackoverflow.co/2025/technology), admired figure as indexed). Very small job market.
- **C++ (Emscripten):** mature and the traditional home of game-AI engines (**unverified** in this pass); not "new and modern".
- **Go or TinyGo:** TinyGo supports browser WebAssembly and WASI ([TinyGo docs](https://tinygo.org/docs/guides/webassembly/)). Binary size and language limits versus standard Go were not stated on that page (**unverified**). Attractive only if the server is Go.

A Caro engine is a search over a board (for example threat-space or alpha-beta search), so raw speed and predictable memory matter; that points to Rust, Zig or C++ over garbage-collected languages (**judgement**).

## 5. Data stores and messaging

- **PostgreSQL:** used by 57.9% of 2026 Stack Overflow respondents, the top database ([SO 2026](https://survey.stackoverflow.co/2026/technology)). Nakama also expects a Postgres wire-compatible server ([repo](https://github.com/heroiclabs/nakama)).
- **Redis and Valkey licensing:** Redis moved to source-available RSALv2/SSPLv1 in March 2024, then added the OSI-approved AGPLv3 as an option starting with Redis 8 (announced 2025-05-01) ([Redis blog](https://redis.io/blog/agplv3/)). Valkey forked "right before the transition" to those licenses, is BSD-3-Clause, and is a project under LF Projects ([Valkey repo](https://github.com/valkey-io/valkey)). Its latest release mentioned is 9.1 (2026-05-19) ([Valkey blog](https://valkey.io/blog/)). Usage in 2025: Redis 28%, Valkey 2.4% ([SO 2025](https://survey.stackoverflow.co/2025/technology)). For self-hosting a game, either license works; AGPL only bites if we modify Redis itself and offer it as a service (**judgement**, not legal advice).
- **NATS:** Apache-2.0, written in Go, part of the CNCF ([nats-server](https://github.com/nats-io/nats-server)). Useful as the cross-node bus for Go, Rust or Bun servers; redundant on the BEAM, which has distribution and PubSub built in (**judgement**). JetStream persistence was not checked (**unverified**).

## 6. Job-market evidence

Global (Stack Overflow):

- 2025 usage, all respondents: JavaScript 66%, Python 57.9%, TypeScript 43.6%, C# 27.8%, C++ 23.5%, Go 16.4%, Rust 14.8%, Kotlin 10.8%, Elixir 2.7%, Zig 2.1%, Gleam 1.1% ([SO 2025](https://survey.stackoverflow.co/2025/technology)).
- 2025 admired: Rust 72%, Gleam 70%, Elixir 66%, Zig 64% (as indexed from the [SO 2025 technology page](https://survey.stackoverflow.co/2025/technology); the chart did not render in my fetch).
- 2026 results (published 2026-10-06) show JavaScript 62.0%, Python 58.0%, TypeScript 43.8%, C# 27.1%, C++ 24.3% ([SO 2026](https://survey.stackoverflow.co/2026/technology)). Go, Rust, Elixir and the admired ranking were not visible in my fetch of the 2026 page (**unverified** for 2026).

Vietnam:

- ITviec job listings on 2026-10-15 (a one-day snapshot): Java 98, ReactJS 49, NodeJS 39, Golang 38, Kotlin 13, Rust 3, Elixir 1 ([Java](https://itviec.com/it-jobs/java), [ReactJS](https://itviec.com/it-jobs/reactjs), [NodeJS](https://itviec.com/it-jobs/nodejs), [Golang](https://itviec.com/it-jobs/golang), [Kotlin](https://itviec.com/it-jobs/kotlin), [Rust](https://itviec.com/it-jobs/rust), [Elixir](https://itviec.com/it-jobs/elixir)). The .NET count could not be read (**unverified**).
- ITviec Q2 2025 postings: Python up 70% year over year, JavaScript up 15%, Java down 4%; Go, Rust and Elixir are not covered ([ITviec Q2 2025](https://itviec.com/blog/vietnam-it-job-market-q2-2025-python-ai-see-strong-growth/)).
- TopDev's per-language breakdown for 2025 was not publicly reachable (**unverified**). ITviec's 2025-2026 salary report is gated behind a form.

Reading: in Vietnam, Go is the only "new and modern" server language with visible demand; Rust and Elixir are global-market bets (**judgement**).

## 7. How well coding agents handle each

- AutoCodeBench (Tencent, 20 languages): for Claude Opus 4 in reasoning mode, Pass@1 was Elixir 80.3, C# 74.9, Kotlin 72.5, TypeScript 47.2, C++ 44.1, Rust 38.7, JavaScript 38.6, Go 37.2 ([arXiv 2508.09101](https://arxiv.org/html/2508.09101v1)). The paper warns that its filtering model struggled to remove simple problems in low-resource languages, so high scores there may partly reflect easier problems. Do not read it as "agents write Elixir best".
- Ecosystem size (section 6) favours TypeScript, Go, C# and Rust for training data (**judgement**). Strong static types and compiler errors (Rust, Go, Gleam, TypeScript) give agents fast feedback loops; dynamic Elixir relies more on tests (**judgement**).
- No benchmark found that measures agents on real-time or distributed server code specifically. The bake-off is the best evidence we will get.

## Shortlist per component

| Component | Shortlist | Main trade-off |
|---|---|---|
| Game-server runtime | Elixir/Phoenix (Gleam variant), Go, Rust/axum; Bun/TypeScript as a baseline | Phoenix gives clustering and presence for free but has the smallest job market; Go has local demand but you build presence and fan-out; Rust is most admired and pairs with a Rust AI engine but is the slowest to write |
| Framework vs. own | Build our own; Colyseus as reference or fallback | Nakama open source is single-node, which blocks the HA goal; Colyseus ties us to TypeScript |
| Web client | SvelteKit, React (Vite or Next.js), SolidStart | React has the market; Svelte and Solid are newer and lighter with smaller ecosystems |
| Board rendering | SVG or DOM; PixiJS if needed | SVG is simple and accessible; PixiJS only for heavy animation or huge boards |
| AI engine (WebAssembly) | Rust, Zig | Rust has the ecosystem but its WebAssembly tooling is in transition; Zig is lean but niche |
| Data and messaging | PostgreSQL; Valkey if a target needs it; NATS for non-BEAM clusters | Each extra store is more to operate on a sub-$25 budget |

## Gaps

- No measured memory footprint per runtime on a small VPS; the bake-off should measure idle and per-connection memory, since it decides how many nodes fit in the budget.
- Ktor, Zig and Emscripten WebAssembly support, NATS JetStream, Gleam's real-time libraries, and current `wasm-pack` status were not verified in this pass.
- Vietnam data is a one-day job-board snapshot plus one quarterly report; no salary-by-language data was accessible.
