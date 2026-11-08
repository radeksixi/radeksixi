# Radek (Sixi)

**I break AI agents, then prove what really broke — and build systems that hold up when the network or the model doesn't.**

Senior AI engineer & architect in Switzerland. 14 years shipping software where failure is expensive: defence edge devices in C/C++, an IoT platform for global pharma, resilient off-grid LoRa mesh systems, and an agentic AI platform over live building telemetry. Today I attack agents, measure the tools that do it, and publish the raw numbers — including the ones where my own tool loses.

[Portfolio](https://radeksixi.web.app/) · [Writing (radek-sixi.github.io)](https://radek-sixi.github.io) · [LinkedIn](https://www.linkedin.com/in/radekb/) · [X](https://x.com/radeksixi) · **Open to remote roles and contracts from November 2026.**

<a href="https://github.com/radek-sixi/agent-redteam-benchmark"><img src="assets/sixi-flow.svg" alt="Sixi, measured security for AI agents: 1 Reach (redwire, PyRIT A2ATarget) → 2 Attack (sixi-scanner) → 3 Judge (oracles, laya-as-judge) → 4 Prove (agent-redteam-benchmark, agent-arena) → 5 Ship (--format sarif), with every fix measured on the benchmark before it ships. sixi-scanner v0.8.4: precision 0.821 and recall 0.571 (both 1st of seven tools), 1.000 precision on multi-turn sessions." width="100%"></a>

---

## Sixi: measured security for AI agents

Most AI red-teaming tools tell you what *they think* broke. On a real agent, 70–97% of those flags were false. Sixi is the set of open projects I build to close that gap: attack, judge with code rather than vibes, prove it on a public benchmark, and gate every pull request on it.

| | Project | What it does |
|---|---|---|
| **Reach** | [**redwire**](https://github.com/radek-sixi/redwire) · Go | One `Send()` to any agent over REST, MCP, A2A, WebSocket or a chat widget. SSRF-guarded |
| **Attack & Ship** | [**sixi-scanner**](https://github.com/radek-sixi/sixi-scanner) · Go | Red-team scanner for LLM agents: 27 single- and multi-turn techniques, one binary, zero dependencies, **no LLM inside** by default. Every finding carries the prompt and reply behind it. Reports as JSON, SARIF 2.1.0 (for GitHub Security tab / CI gates; an unreachable target fails, never passes) or Markdown |
| **Judge** | [**laya-as-judge**](https://github.com/radek-sixi/laya-as-judge) · Python | Local LLM-as-a-judge with typed verdicts in milliseconds; deterministic oracles first, a model only when unsure |
| **Prove** | [**agent-redteam-benchmark**](https://github.com/radek-sixi/agent-redteam-benchmark) · Python | Seven red-teaming tools vs one real Microsoft Foundry agent, scored from the wire by 10 oracles + a tool-blind judge. Raw findings published |
| | [**agent-arena**](https://github.com/radek-sixi/agent-arena) · TypeScript | Evaluation arena with a model-free referee: scripted peers that lie, replay hashes anyone can verify. npm 0.2.3 |
| **Learn** | [**agent-redteam-labs**](https://github.com/radek-sixi/agent-redteam-labs) · Python | Hands-on labs for agent red-teaming and hardening, with [adk-demo-target](https://github.com/radek-sixi/adk-demo-target), a deliberately vulnerable bank agent |

**Upstream:** [`A2ATarget` merged into Microsoft PyRIT](https://github.com/microsoft/PyRIT/pull/2771): PyRIT can attack agents over the Agent-to-Agent protocol. [`A2AGenerator` for NVIDIA garak](https://github.com/NVIDIA/garak/pull/2225): in review.

## What the benchmark found

- **Tools' own reports are mostly noise.** 70–97% of each tool's flags were false alarms. Comparing tools by their self-reports compares their noise.
- **It wouldn't *say* its secret, but it *e-mailed* it.** A poisoned KB article got the agent to mail an IBAN and a secret code to an attacker. Prompt Shields never flagged it.
- **LLM judges miss business logic.** Two 30-EUR refunds beat a 50-EUR-per-request cap. The judge cleared 5 of 7; a five-line oracle caught all 7.
- **sixi-scanner v0.8.3+ is 1st on precision (0.821) and recall (0.571)** on the stateless lane ($0.72 cloud cost), and reaches **1.000 precision (22/22)** on the multi-turn session lane — catching split-cap refunds and multi-turn taint relays that single-turn scanners cannot reach. Where it still loses is breadth: 8 distinct violating attacks against garak's 67 and promptfoo's 89.

*I maintain the benchmark and the scanner; the conflict of interest is stated there, and every raw finding is public.*

---

<a href="https://github.com/radek-sixi/sixi-asi"><img src="assets/sixi-asi-flow.svg" alt="sixi-asi, C++20/CUDA hybrid MoE &amp; Gated DeltaNet inference engine: 1 Bind (.sixi + .sixi.idx, hardened GGUF v2/v3, 4096 B O_DIRECT I/O) → 2 Plan (MemoryPlanner, &lt; 20 GiB / &lt; 80 GiB, LRU/LFU expert cache, mmap/pinned PLE) → 3 Execute (CUDA sm_110, FP32 GDN state, top-k MoE with 0 drops, q8_1 &amp; nvfp4_fp32) → 4 Draft (Suffix + MTP, n_rs_seq GDN ring, GPU greedy verifier, O(1) state rollback) → 5 Serve (sixi-server, sixi-cli, HTTP/SSE, JSON tool validator). +14.2% vs llama.cpp draft-mtp (5/5 wins), 32.0 tok/s Flash-Next MoE peak, 0 Python." width="100%"></a>

## sixi-asi: C++20/CUDA hybrid MoE & Gated DeltaNet inference engine

[**sixi-asi**](https://github.com/radek-sixi/sixi-asi) is a native C++20/CUDA inference runtime engineered for hybrid linear-attention (`Gated DeltaNet` / `Mamba-SSM`) and sparse Mixture-of-Experts (`MoE`) architectures — with **zero Python** on the critical inference path, byte-exact VRAM planning, and `100%` lossless speculative verification.

| | Component | What it does |
|---|---|---|
| **Bind & Stream** | [`.sixi` + `.sixi.idx`](https://github.com/radek-sixi/sixi-asi) · C++20 | Cryptographic SHA-256 binding of GGUF shards, tensor geometries, quantization recipes, and memory budgets; atomic `flock` + `fsync` `.sixi.idx` sidecar; hardened 64-bit GGUF v2/v3 parser and 4096-byte aligned `O_DIRECT` / `mmap` tensor reader |
| **Plan & Offload** | `MemoryPlanner` · Host/NVMe | Byte-exact admission control (`< 20 GiB` for `Qwen3.8-27B` at `17.27 GiB` measured GPU footprint; `< 12 GiB` low-VRAM split; `< 80 GiB` for 512-expert `Qwen3.8-Flash-Next` with `26.82 GiB` `PLE` offloaded via `mmap` / bounded pinned ring and `LRU`/`LFU` expert cache) |
| **Execute** | `CUDA sm_110` · `FP32 GDN` | Full `top-k` MoE routing (`0` dropped experts) with canonical ascending `expert_id` `FP32` reduction (`StrictReplay`), `FP32` Gated DeltaNet recurrent state, 40-byte `q8_1_fp32_block`, and 24-byte Blackwell `nvfp4_fp32_block` (`E2M1` + `FP8 E4M3` micro-scales + `FP32` super-scale) |
| **Speculate** | `SuffixDrafter` + `MTP` | Fused Catch-Up + Draft-0 `MTP` execution with GPU greedy verifier (`+14.2%` throughput over `llama.cpp draft-mtp` across 5/5 benchmark suites on NVIDIA Thor; up to `32.02 tok/s` / `1.73x` on 512-expert `Qwen3.8-Flash-Next`) and `O(1)` rollback across `GDN + conv1d + KV + RNG` states (`n_rs_seq` ring) |
| **Serve & Gate** | `sixi-server` · `sixi-cli` · `sixi-bench` | Native C++20 OpenAI-compatible HTTP/SSE server (`/v1/chat/completions`, `/metrics`) with separate `reasoning_budget_tokens` and `answer_budget_tokens`, non-mutating `LoopDetector`, and JSON Schema `ToolCallValidator` |

---

<a href="https://github.com/radek-sixi/sigillo"><img src="assets/sixi-lora-flow.svg" alt="Off-grid LoRa mesh &amp; authenticated field communications: 1 Sign (sigillo v1.0-rc, 87 B Ed25519 + EU CAMF frame in C99/Python/TS) → 2 Route (MeshCore SX1262, ERP &amp; 1% duty clamp) → 3 Operate (sixi-tdeck-plus / Faro LVGL 9.3 handheld &amp; PC UI) → 4 Observe (sixi-observer LoRa USB + RTL-SDR V4, Bayesian ghost-hop triangulation) → 5 Diagnose (GlassBoxEdge on 2× NVIDIA Jetson)." width="100%"></a>

## Off-grid LoRa mesh & authenticated field communications

When power and mobile networks go down, cloud-dependent tools disappear first. Alongside AI security, I build a **complete open-source off-grid LoRa mesh stack** — from an 87-byte cryptographically signed alert protocol verifiable offline on a microcontroller, to a touch-first handheld UI and Linux desktop console, to an SDR/radio observer bridge:

| | Project | What it does |
|---|---|---|
| **Sign & Verify** | [**sigillo**](https://github.com/radek-sixi/sigillo) · C99 · Python · TypeScript | Compact signed frame for public alerts over low-bandwidth packet radio (`v1.0-rc`): 7 B header + 16 B EU Common Alert Message Format (Galileo CAMF) block + optional extensions + `Ed25519` signature = **87 bytes** base frame. Offline verification, CC BY 4.0 spec, locked conformance test vectors (`vectors/v1.json`), and three bit-exact reference libraries (`sigillo-c`, `sigillo-py`, `sigillo-ts`) |
| **Operate (Handheld & PC)** | [**sixi-tdeck-plus (Faro)**](https://github.com/radek-sixi/sixi-tdeck-plus) · C/C++ · LVGL 9 · Python | Clean-room open-source (Apache-2.0) touch-first handheld UI and Ubuntu Linux desktop companion console for [MeshCore](https://github.com/meshcore-dev/MeshCore) on LilyGo T-Deck Plus, T-Beam S3 Supreme (WiFi/BLE/USB), and Heltec V3. Includes a 2D mesh topology graph, SX1262 link-budget & time-on-air calculator, offline SNR heatmap, ACK/RTT direct messaging, 320×240 simulator (`faro-sim`), and a 1-click browser WebSerial flasher |
| **Observe & Triangulate** | [**sixi-observer**](https://github.com/radek-sixi/sixi-observer) · Python · LoRa · RTL-SDR | Open-source (MIT) observer bridge for MeshCore / LoRa radios (T-Beam Supreme SX1262, Heltec V3/V4, RAK4631) and RTL-SDR V4: local `Ed25519` cryptographic `ADVERT` verification, multi-hop SNR/RSSI packet telemetry, ML Bayesian ghost-hop repeater triangulation, and a 0 ms local WebSocket stream |
| **Route (Upstream)** | [**MeshCore**](https://github.com/meshcore-dev/MeshCore/pulls?q=is%3Apr+author%3Aradek-sixi) · C++ / Embedded | Upstream pull requests to the MeshCore hybrid-routing packet radio protocol: dynamic Wi-Fi for the LilyGO T-Beam Supreme SX1262 and signed TX power conversion fixes |
| **Edge AI & OT** | [**GlassBoxEdge**](https://github.com/radek-sixi/GlassBoxEdge) · Python · Edge | Air-gapped inference and agentic diagnostics over live industrial & building telemetry on two NVIDIA Jetson edge nodes |

---

## Recently

- **Nov** · [**sixi-asi**](https://github.com/radek-sixi/sixi-asi): modular C++20/CUDA(`sm_110`) inference engine for hybrid linear-attention (`Gated DeltaNet`) and sparse `MoE` models (`Qwen3.8-27B` `< 20 GiB` and 512-expert `Qwen3.8-Flash-Next` `< 80 GiB`), with fused `MTP` speculative decoding (`+14.2%` over `llama.cpp draft-mtp`), `O(1)` GPU GDN state rollback, and zero Python in the critical path.
- **Oct 26** · [**sigillo v1.0-rc**](https://github.com/radek-sixi/sigillo): specification, conformance vectors, and reference libraries in C99 (`sigillo-c`), Python (`sigillo-py`), and TypeScript (`sigillo-ts`) for an 87-byte `Ed25519`-signed EU CAMF public alert frame verifiable offline.
- **Oct** · [**sixi-observer**](https://github.com/radek-sixi/sixi-observer): open-source (MIT) LoRa & RTL-SDR V4 observer bridge with local Ed25519 advertisement verification, ML Bayesian ghost-hop triangulation, and a local WebSocket stream.
- **Oct 22** · [**sixi-tdeck-plus (Faro) v0.1.0**](https://github.com/radek-sixi/sixi-tdeck-plus): open-source (Apache-2.0) LVGL 9 handheld UI, Linux desktop companion app, simulator, and 1-click browser WebSerial flasher for MeshCore LoRa devices (T-Deck Plus, T-Beam S3 Supreme, Heltec V3).
- **Oct** · [**sixi-scanner v0.8.4**](https://github.com/radek-sixi/sixi-scanner/releases/tag/v0.8.4): 27 techniques, multi-turn session probes, parameter induction (secrets routed into tool arguments), and trace-backed outbound taint relay checks. Precision 0.821 (1st of 7 tools) and 1.000 on multi-turn sessions.
- **Sep 30** · `A2ATarget` merged into Microsoft PyRIT.
- **Sep 28** · agent-arena 0.2.3 on npm.

## What I do

- **Adversarial testing of agents:** prompt injection, tool abuse, scope escalation over REST, MCP and A2A; OWASP LLM & Agentic Top 10, MAESTRO.
- **Agent identity & authorization:** on-behalf-of flows, non-human identities, least-privilege MCP tools.
- **AI gateways, guardrails & evals:** Azure API Management, Content Safety, red-teaming in CI, deterministic oracles.
- **Regulated industries:** security evidence that maps to DORA, NIS2, GDPR and the EU AI Act.
- **Sovereign edge AI, LoRa mesh & OT/IoT:** native C++20/CUDA LLM inference ([sixi-asi](https://github.com/radek-sixi/sixi-asi)) and open models on NVIDIA Jetson Thor / DGX Spark, air-gapped inference, offline-verifiable signed radio protocols ([sigillo](https://github.com/radek-sixi/sigillo)), embedded C/C++ & LVGL 9 firmware on ESP32-S3 / SX1262 LoRa mesh ([sixi-tdeck-plus](https://github.com/radek-sixi/sixi-tdeck-plus), [sixi-observer](https://github.com/radek-sixi/sixi-observer)), and agents over live telemetry ([GlassBoxEdge](https://github.com/radek-sixi/GlassBoxEdge)).

**Stack:** Python · Go · TypeScript · C# / .NET · C/C++ · CUDA · Embedded (ESP32-S3, LVGL, LoRa / MeshCore) · LangGraph · Google ADK · MCP · A2A · Azure / Microsoft Foundry · GCP · AWS · vLLM · llama.cpp · NVIDIA Jetson

**Certified:** Microsoft Multi-Agent AI Solutions Expert (AI-500) · Azure Solutions Architect Expert · Azure Security Engineer · AWS Security Specialty
