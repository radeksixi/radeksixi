# Radek (Sixi)

**I build local C++/CUDA inference engines, break AI agents and prove what really broke, and engineer off-grid systems that hold up when the network or the model doesn't.**

Senior AI engineer & architect in Switzerland. 14 years shipping software where failure is expensive: native C/C++ & CUDA runtimes, defence edge devices, an IoT platform for global pharma, resilient off-grid LoRa mesh systems, and an agentic AI platform over live building telemetry. Today I build deterministic local LLM runtimes, attack agents, measure the tools that do it, and publish the raw numbers — including the ones where my own tool loses.

[Portfolio](https://radeksixi.web.app/) · [Writing (radek-sixi.github.io)](https://radek-sixi.github.io) · [contact@sixi.ai](mailto:contact@sixi.ai) · [X](https://x.com/radeksixi) · **Open to remote roles and contracts from November 2026.**

---

<a href="https://github.com/radek-sixi/sixi-asi"><img src="assets/sixi-asi-flow.svg" alt="sixi-asi, Hybrid MoE &amp; Gated DeltaNet inference engine: 1 Verify (.sixi manifest, fast index, safe GGUF loader) → 2 Plan (memory planner, exact VRAM cap, cold expert streaming) → 3 Run (C++20 / CUDA, hybrid Attention + GDN, bit-exact outputs) → 4 Accelerate (speculative decoding, fast drafting, instant state rollback) → 5 Serve (sixi-server &amp; CLI, OpenAI HTTP/SSE, thinking budget &amp; checked tool calls). Measured on NVIDIA Jetson AGX Thor with identical prompts: 45.3 tok/s vs llama-server 43.7, NVIDIA TensorRT Edge-LLM 45.4, plain AR 11.9 on Qwen3.8-27B. 0 Python." width="100%"></a>

## sixi-asi: Hybrid MoE & Gated DeltaNet inference engine

Hybrid open-weight models combine full attention with linear attention (`Gated DeltaNet`), whose fixed-size recurrent state cannot be truncated like a KV cache, which makes speculative decoding hard. [**sixi-asi**](https://github.com/radek-sixi/sixi-asi) is a C++20 speculative-decoding server for these models on **NVIDIA Jetson Thor** and **DGX Spark** unified-memory hardware, with llama.cpp as the compute backend and **zero Python** on the inference path. Version 0.9 is a public fork of my private research branch; every number below ships with its script and raw JSON ([0.9 write-up](https://radek-sixi.github.io/sixi-asi-0-9-thor-kernels-long-context/), [0.8 write-up](https://radek-sixi.github.io/sixi-asi-speculative-decoding-hybrid-llms/)).

| | Capability | What it does |
|---|---|---|
| **Verify & Load** | [**.sixi manifest & loader**](https://github.com/radek-sixi/sixi-asi) · C++20 | Locks model weights, quantization settings, and memory budgets into a verifiable manifest; builds a fast startup index and safely loads multi-shard GGUF models |
| **Plan Memory** | **Memory planner** · GPU / Unified RAM | Computes the exact memory budget before execution starts (`< 20 GiB` for a 27B hybrid model on a single GPU, `< 12 GiB` low-VRAM mode, `< 80 GiB` for 512-expert MoE on **NVIDIA Jetson / DGX Spark**) and streams cold weights from host RAM or NVMe |
| **Run** | **llama.cpp backend + Thor kernel patch** | Loads a pinned upstream llama.cpp (sm_110, CUDA graphs) through a verified ABI layout, plus a 106-line kernel patch found by profiling: quantized matmul switch point and occupancy for skinny batches on Thor. 8-row verify pass **163 → 88 ms**, same weights, same results |
| **Accelerate** | **Lossless speculative decoding** | DSpine, DSpark or DFlash2 block drafters, verified in one batched pass with **O(1) recurrent-state rollback**; lossless for greedy and sampled decoding (distribution-tested). Same prompts, same hardware: **45.3 tok/s vs llama-server 43.7, NVIDIA TensorRT Edge-LLM 45.4 (NVFP4), plain AR 11.9**; 0.8 was 23.2 |
| **Remember** | **Prefix cache** | Live-state continuation, ring rollback, device-side snapshots and tail checkpoints: the next question about a 63k-token document starts in **1.0 s instead of 209 s**; 19–48 tok/s decode from 4k to 63k tokens of context |
| **Serve** | **sixi-server · sixi-cli · sixi-bench** | OpenAI-compatible HTTP/SSE server with a native Qwen3.8 template (byte-identical to llama.cpp's Jinja in 9/9 parity cases), reasoning budgets that force `</think>` instead of an empty answer, loop detection scoped to reasoning, and schema-validated tool calls |
| **Prove** | **Benchmark & quality harness** | `ab_bench.py` (same prompt tokens for every engine, outputs diffed against the AR reference), `intel_eval.py` (GSM8K 28/30, HumanEval 19/20 at **65.7 tok/s**, identical scores to TensorRT Edge-LLM at 61.5), `longctx_bench.py`, `template_parity.py` |

---

<a href="https://github.com/radek-sixi/agent-redteam-benchmark"><img src="assets/sixi-flow.svg" alt="Sixi, measured security for AI agents: 1 Reach (redwire, PyRIT A2ATarget) → 2 Attack (sixi-scanner) → 3 Judge (oracles, laya-as-judge) → 4 Prove (agent-redteam-benchmark, agent-arena) → 5 Ship (--format sarif), with every fix measured on the benchmark before it ships. sixi-scanner v0.8.4: precision 0.821 and recall 0.571 (both 1st of seven tools), 1.000 precision on multi-turn sessions." width="100%"></a>

## Sixi: measured security for AI agents

Most AI red-teaming tools tell you what *they think* broke. On a real agent, 70–97% of those flags were false. Sixi is the set of open projects I build to close that gap: attack, judge with code rather than vibes, prove it on a public benchmark, and gate every pull request on it.

| | Project | What it does |
|---|---|---|
| **Reach** | [**redwire**](https://github.com/radek-sixi/redwire) · Go | One `Send()` to any agent over REST, MCP, A2A, WebSocket or a chat widget. SSRF-guarded |
| **Attack & Ship** | [**sixi-scanner**](https://github.com/radek-sixi/sixi-scanner) · Go | Red-team scanner for LLM agents: 27 single- and multi-turn techniques, one binary, zero dependencies, **no LLM inside** by default. Every finding carries the prompt and reply behind it. Reports as JSON, SARIF 2.1.0 (for GitHub Security tab / CI gates; an unreachable target fails, never passes) or Markdown. **New in [v0.9.0](https://github.com/radek-sixi/sixi-scanner/releases/tag/v0.9.0):** a scan that could not test everything exits `3` and lists what was not tested in every format, and rule recitation is its own finding ([write-up](https://radek-sixi.github.io/sixi-scanner-no-findings-is-not-a-pass/)) |
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

<a href="https://github.com/radek-sixi/sigillo"><img src="assets/sixi-lora-flow.svg" alt="Off-grid LoRa mesh &amp; authenticated field communications: 1 Sign (sigillo, signed public alert in an 87-byte radio frame) → 2 Route (MeshCore, hops across solar nodes) → 3 Operate (sixi-tdeck-plus / Faro handheld touch UI &amp; Linux desktop app) → 4 Observe (sixi-observer LoRa &amp; SDR receiver) → 5 Edge AI (GlassBoxEdge on NVIDIA Jetson)." width="100%"></a>

## Off-grid LoRa mesh & authenticated field communications

When power and mobile networks go down, cloud-dependent tools disappear first. I build a **complete open-source off-grid LoRa mesh stack** — from a signed emergency alert format that any pocket radio can verify offline, to a touchscreen handheld UI and Linux desktop app, to a live radio receiver bridge:

| | Project | What it does |
|---|---|---|
| **Sign & Verify** | [**sigillo**](https://github.com/radek-sixi/sigillo) · C99 · Python · TypeScript | Compact signed alert format (`v1.0-rc`) that fits an EU public emergency alert and cryptographic signature into a single **87-byte** radio packet. Verifiable 100% offline without internet or GPS, with an open specification and reference libraries in C, Python, and TypeScript |
| **Operate (Handheld & PC)** | [**sixi-tdeck-plus (Faro)**](https://github.com/radek-sixi/sixi-tdeck-plus) · C/C++ · LVGL 9 · Python | Open-source touchscreen handheld UI and Ubuntu Linux desktop companion app for [MeshCore](https://github.com/meshcore-dev/MeshCore) LoRa radios (LilyGo T-Deck Plus, T-Beam S3 Supreme, Heltec V3). Includes a live mesh map, radio range calculator, desktop simulator, and 1-click browser flasher |
| **Observe** | [**sixi-observer**](https://github.com/radek-sixi/sixi-observer) · Python · LoRa · RTL-SDR | Open-source receiver bridge for USB LoRa radios and RTL-SDR dongles: verifies node signatures on the air, tracks multi-hop signal quality, estimates repeater paths, and streams live radio telemetry locally |
| **Route (Upstream)** | [**MeshCore**](https://github.com/meshcore-dev/MeshCore/pulls?q=is%3Apr+author%3Aradek-sixi) · C++ / Embedded | Upstream contributions to the MeshCore packet-radio mesh protocol: dynamic Wi-Fi support and radio transmit-power fixes |
| **Edge AI & OT** | [**GlassBoxEdge**](https://github.com/radek-sixi/GlassBoxEdge) · Python · Edge | Air-gapped local AI diagnostics over live industrial & building telemetry on two NVIDIA Jetson edge nodes |

---

## Recently

- **Nov 11** · [**sixi-scanner v0.9.0**](https://github.com/radek-sixi/sixi-scanner/releases/tag/v0.9.0): a correctness pass over the scanner's own code. A target that answers every prompt with an empty error now fails the scan instead of reading as clean, partial coverage exits `3`, untested techniques are listed in JSON, SARIF and Markdown, rule recitation is its own finding, and markers no longer match inside other words. First release that installs under the current module path. The benchmark figures are still v0.8.4 and have not been re-run; [write-up](https://radek-sixi.github.io/sixi-scanner-no-findings-is-not-a-pass/).
- **Nov 10** · [**sixi-asi 0.9**](https://github.com/radek-sixi/sixi-asi): Qwen3.8-27B on Jetson AGX Thor from 23.2 to **45.3 tok/s** (3.8× autoregressive) with a Thor-specific llama.cpp kernel patch and block drafters, level with NVIDIA TensorRT Edge-LLM on plain GGUF weights; follow-up questions on a 63k-token document in 1 s; [write-up](https://radek-sixi.github.io/sixi-asi-0-9-thor-kernels-long-context/).
- **Nov 8** · [**sixi-asi 0.8**](https://github.com/radek-sixi/sixi-asi): public fork of my speculative-decoding server for hybrid Gated-DeltaNet LLMs on NVIDIA unified memory. Same tokens, same hardware: 21.9 tok/s vs ollama 21.1, llama-server 20.4 (dflash) / 11.0 (AR); lossless for greedy and sampled decoding; prefix cache; [write-up](https://radek-sixi.github.io/sixi-asi-speculative-decoding-hybrid-llms/).
- **Oct 26** · [**sigillo v1.0-rc**](https://github.com/radek-sixi/sigillo): open specification and reference libraries in C, Python, and TypeScript for an 87-byte signed public alert packet verifiable offline over LoRa radio.
- **Oct** · [**sixi-observer**](https://github.com/radek-sixi/sixi-observer): open-source LoRa & RTL-SDR receiver bridge with offline node signature verification, repeater path estimation, and a live local telemetry stream.
- **Oct 22** · [**sixi-tdeck-plus (Faro) v0.1.0**](https://github.com/radek-sixi/sixi-tdeck-plus): open-source handheld touchscreen UI, Linux desktop app, simulator, and 1-click browser flasher for MeshCore LoRa radios.
- **Oct** · [**sixi-scanner v0.8.4**](https://github.com/radek-sixi/sixi-scanner/releases/tag/v0.8.4): 27 techniques, multi-turn session probes, parameter induction (secrets routed into tool arguments), and trace-backed outbound taint relay checks. Precision 0.821 (1st of 7 tools) and 1.000 on multi-turn sessions.
- **Sep 30** · `A2ATarget` merged into Microsoft PyRIT.
- **Sep 28** · agent-arena 0.2.3 on npm.

## What I do

- **Local LLM inference & sovereign edge AI:** native C++20/CUDA runtimes ([sixi-asi](https://github.com/radek-sixi/sixi-asi)) for hybrid MoE & Gated DeltaNet models on NVIDIA Jetson Thor / DGX Spark, air-gapped inference, and agentic diagnostics over live telemetry ([GlassBoxEdge](https://github.com/radek-sixi/GlassBoxEdge)).
- **Adversarial testing of agents:** prompt injection, tool abuse, scope escalation over REST, MCP and A2A; OWASP LLM & Agentic Top 10, MAESTRO.
- **Agent identity & authorization:** on-behalf-of flows, non-human identities, least-privilege MCP tools.
- **AI gateways, guardrails & evals:** Azure API Management, Content Safety, red-teaming in CI, deterministic oracles.
- **Regulated industries:** security evidence that maps to DORA, NIS2, GDPR and the EU AI Act.
- **Off-grid LoRa mesh & embedded systems:** offline-verifiable signed radio protocols ([sigillo](https://github.com/radek-sixi/sigillo)) and embedded C/C++ & LVGL 9 firmware on ESP32-S3 / LoRa mesh hardware ([sixi-tdeck-plus](https://github.com/radek-sixi/sixi-tdeck-plus), [sixi-observer](https://github.com/radek-sixi/sixi-observer)).

**Stack:** C/C++ · CUDA · Python · Go · TypeScript · C# / .NET · Embedded (ESP32-S3, LVGL, LoRa / MeshCore) · LangGraph · Google ADK · MCP · A2A · Azure / Microsoft Foundry · GCP · AWS · vLLM · llama.cpp · NVIDIA Jetson & DGX Spark

**Certified:** Microsoft Multi-Agent AI Solutions Expert (AI-500) · Azure Solutions Architect Expert · Azure Security Engineer · AWS Security Specialty
