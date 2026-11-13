# Radek (Sixi)

**Senior Software Engineer & AI Architect in Switzerland.** 14 years shipping software where failure is expensive — native C++/CUDA inference runtimes, enterprise AI agent platforms, and resilient off-grid packet-radio systems. Every number below ships with its script and raw data.

[Sixi Lab (sixi.ai)](https://sixi.ai) · [Portfolio](https://radeksixi.web.app/) · [Writing (radeksixi.github.io)](https://radeksixi.github.io) · [Work with me](https://radeksixi.github.io/work/) · [contact@sixi.ai](mailto:contact@sixi.ai) · [X](https://x.com/radeksixi) · **Open to remote roles and contracts.**

<a href="https://sixi.ai"><img src="assets/sixi-lab-overview.svg" alt="Sixi Lab overview: 1 Infer (sixi-asi, C++20/CUDA, 45.3 tok/s single-stream, 32 streams at 170 tok/s) → 2 Evaluate (laya-as-judge, 11/11 wins over Gemini Decision 1, ECE 0.0234, $0.77) → 3 Red-Team (sixi-scanner, 0.821 precision, 1.000 multi-turn) · Field Core at sixi.ai (Rifugio, sigillo, Faro, sixi-observer, map.sixi.ai)." width="100%"></a>

---

## 1. [sixi-asi](https://github.com/radeksixi/sixi-asi) — Hybrid MoE & Gated DeltaNet inference engine

<a href="https://github.com/radeksixi/sixi-asi"><img src="assets/sixi-asi-flow.svg" alt="sixi-asi, Hybrid MoE &amp; Gated DeltaNet inference engine: 1 Verify (.sixi manifest) → 2 Plan (memory planner) → 3 Run (C++20 / CUDA) → 4 Accelerate (speculative decoding) → 5 Serve (sixi-server &amp; CLI). Measured on NVIDIA Jetson AGX Thor: 45.3 tok/s single stream and 170 tok/s across 32 concurrent streams in 27 GB on Qwen3.8-27B. 0 Python." width="100%"></a>

C++20/CUDA speculative-decoding server for hybrid full-attention + `Gated DeltaNet` + sparse MoE models on **NVIDIA Jetson Thor** and **DGX Spark** unified memory, built on llama.cpp with **zero Python** on the runtime path (`v0.11` — [0.11 write-up](https://radeksixi.github.io/sixi-asi-0-11-32-streams-on-jetson-thor/), [0.9 write-up](https://radeksixi.github.io/sixi-asi-0-9-thor-kernels-long-context/), [0.8 write-up](https://radeksixi.github.io/sixi-asi-speculative-decoding-hybrid-llms/)).

| Stage | What it does |
|---|---|
| **Verify & Plan** | Verifiable `.sixi` manifest + memory planner that locks exact VRAM/RAM budgets up front (`< 20 GiB` for 27B hybrid, `< 12 GiB` low-VRAM, `< 80 GiB` for 512-expert MoE) |
| **Accelerate** | Lossless speculative decoding with **O(1) recurrent-state rollback**, block drafters, and a 106-line Thor kernel patch: **45.3 tok/s** single-stream on Qwen3.8-27B (**3.8× AR**, level with TensorRT Edge-LLM NVFP4 on plain GGUF) |
| **Scale (`v0.11`)** | Input-log replay instead of 144 MiB state snapshots per row: **32 concurrent streams at 170 tok/s in 27 GB** (down from 70 GB); 16 long-prompt agents wait **< 3 s** instead of 442 s |
| **Remember & Serve** | Cross-request prefix cache (**1.0 s** vs 209 s follow-up at 63k context), OpenAI-compatible HTTP/SSE server, native Qwen3.8 template, reasoning budgets, and schema-checked tool calls |

---

## 2. [laya-as-judge](https://github.com/radeksixi/laya-as-judge) — Calibrated AI evaluation & routing

Calibrated typed-decision evaluation engine (`v0.4.0 Laya-Omega`, Python · CUDA / MLX) that replaces uncalibrated single-LLM judges with fast local GPU encoders + selective cloud escalation ([write-up](https://radeksixi.github.io/laya-as-judge-omega-11-of-11-benchmarks/)).

| Layer | How it works |
|---|---|
| **Local GPU Encoders** | `Laya` + `Vega-U 0.8B` with disjoint Test-Time Training handle clear decisions locally in **7–14 ms at $0.00** |
| **Selective Cascade** | Escalates only uncertain or multimodal cases to packed Gemini Flash-Lite / Flash batches |
| **Meta-Calibration** | 10-fold out-of-fold simplex probability calibration produces honest confidence scores instead of raw model logits |
| **Measured Result** | **11/11 benchmark wins over Gemini Decision 1** across 2,250 questions, pooled **Top-1 ECE 0.0234** (vs 0.0250), **$0.77 total cost** |

---

## 3. [sixi-scanner](https://github.com/radeksixi/sixi-scanner) — Agentic AI red-teaming & benchmark

<a href="https://github.com/radeksixi/agent-redteam-benchmark"><img src="assets/sixi-flow.svg" alt="Sixi, measured security for AI agents: 1 Reach (PyRIT A2ATarget) → 2 Attack (sixi-scanner) → 3 Judge (oracles, laya-as-judge) → 4 Prove (agent-redteam-benchmark) → 5 Ship (--format sarif)." width="100%"></a>

On a real enterprise agent, 70–97% of generic red-teaming flags were false alarms. I build a static Go scanner and a wire-level benchmark that separate real violations from noise ([v0.9.1 write-up](https://radeksixi.github.io/sixi-scanner-no-findings-is-not-a-pass/), [benchmark write-up](https://radeksixi.github.io/agent-redteam-benchmark-findings/)).

| Project | What it does |
|---|---|
| [**sixi-scanner**](https://github.com/radeksixi/sixi-scanner) · Go (`v0.9.1`) | Static CLI scanner: 27 single- and multi-turn probes, zero dependencies, no LLM inside by default. **1st of 7 tools** on precision (**0.821**) and recall (**0.571**), **1.000 precision (22/22)** on multi-turn sessions; native SARIF 2.1.0 CI gate (`exit 3` on partial coverage) |
| [**agent-redteam-benchmark**](https://github.com/radeksixi/agent-redteam-benchmark) · Python | Seven tools (garak, promptfoo, DeepTeam, PyRIT, Azure Red Team, agent-probe, sixi-scanner) vs a live Microsoft Foundry agent, scored from the wire by 10 deterministic oracles + a tool-blind judge |
| [**agent-redteam-labs**](https://github.com/radeksixi/agent-redteam-labs) · Python | Hands-on agent red-teaming and hardening labs + [adk-demo-target](https://github.com/radeksixi/adk-demo-target), plus upstream [`A2ATarget` merged into Microsoft PyRIT](https://github.com/microsoft/PyRIT/pull/2771) |

---

## Rifugio & Swiss MeshCore — Off-grid field systems at [sixi.ai](https://sixi.ai)

<a href="https://sixi.ai"><img src="assets/sixi-lora-flow.svg" alt="Off-grid LoRa mesh &amp; authenticated field communications: 1 Sign (sigillo, 87-byte signed alert) → 2 Route (MeshCore) → 3 Operate (Faro handheld UI &amp; Linux console) → 4 Observe (sixi-observer) → 5 Edge AI (GlassBoxEdge)." width="100%"></a>

At [**Sixi Lab (`sixi.ai`)**](https://sixi.ai) I engineer **Rifugio** — autonomous solar emergency infrastructure for Swiss [MeshCore](https://github.com/meshcore-dev/MeshCore) LoRa networks — alongside an open-source radio stack and [live Swiss topology map](https://map.sixi.ai):

| Project | What it does |
|---|---|
| [**Rifugio · Sixi Lab**](https://sixi.ai) | Autonomous off-grid MeshCore emergency alert, help-request, and link-health programme for mountain valleys, with open specifications and radio bench reports |
| [**sigillo**](https://github.com/radeksixi/sigillo) · C99 · Python · TS (`v1.1-rc`) | Fits an Ed25519-signed EU CAMF public emergency alert into **87 bytes** for LoRa radio; verifiable 100% offline, with shared test vectors and receiver profiles ([write-up](https://radeksixi.github.io/sigillo-87-byte-signed-alerts-for-lora/)) |
| [**sixi-tdeck-plus (Faro)**](https://github.com/radeksixi/sixi-tdeck-plus) · C/C++ · LVGL 9 (`v0.12.1`) | Open-source touchscreen/keypad UI and Ubuntu console across **5 ESP32-S3 LoRa boards** (T-Deck Plus/MAX, ThinkNode M9, T-Beam Supreme, Heltec V3), offline maps, `EN/IT/DE/FR/PL`, and [WebSerial flasher](https://radeksixi.github.io/sixi-tdeck-plus/) ([write-up](https://radeksixi.github.io/faro-0-12-1-flash-safety-wifi-and-five-boards/)) |
| [**sixi-observer**](https://github.com/radeksixi/sixi-observer) & [**GlassBoxEdge**](https://github.com/radeksixi/GlassBoxEdge) | USB LoRa / RTL-SDR receiver bridge feeding [map.sixi.ai](https://map.sixi.ai), [upstream MeshCore PRs](https://github.com/meshcore-dev/MeshCore/pulls?q=is%3Apr+author%3Aradeksixi), and air-gapped Jetson edge diagnostics |

---

## Recently

- **Nov 13** · [**laya-as-judge v0.4.0 (Laya-Omega)**](https://radeksixi.github.io/laya-as-judge-omega-11-of-11-benchmarks/) — 11/11 benchmark wins over Gemini Decision 1 across 2,250 questions, Top-1 ECE 0.0234, $0.77 total cost.
- **Nov 12** · [**sixi-asi 0.11**](https://radeksixi.github.io/sixi-asi-0-11-32-streams-on-jetson-thor/) — 32 concurrent Qwen3.8-27B streams on one Jetson AGX Thor at 170 tok/s in 27 GB instead of 70 GB; < 3 s agent follow-ups.
- **Nov 12** · [**Faro v0.12.1**](https://radeksixi.github.io/faro-0-12-1-flash-safety-wifi-and-five-boards/) — CRC32 flash recovery, hardened phone Wi-Fi setup, 2/3-byte repeater paths, Polish UI, and 5 ESP32-S3 boards.
- **Nov 11** · [**sixi-scanner v0.9.1**](https://radeksixi.github.io/sixi-scanner-no-findings-is-not-a-pass/) — unreachable targets fail, partial coverage exits `3` with untested checks listed in JSON/SARIF/Markdown.
- **Nov 10** · [**sixi-asi 0.9**](https://radeksixi.github.io/sixi-asi-0-9-thor-kernels-long-context/) — 45.3 tok/s single-stream (3.8× AR) on Jetson Thor + 1.0 s follow-up at 63k context.
- **Oct 26** · [**sigillo v1.1-rc**](https://radeksixi.github.io/sigillo-87-byte-signed-alerts-for-lora/) — 87-byte Ed25519-signed EU CAMF radio alerts in C99, Python, and TypeScript.

## Focus & Stack

- **Focus:** Local C++/CUDA LLM runtimes ([sixi-asi](https://github.com/radeksixi/sixi-asi)) · Calibrated hybrid AI evaluation ([laya-as-judge](https://github.com/radeksixi/laya-as-judge)) · Agent red-teaming & identity/governance on Microsoft Foundry & Google ADK ([sixi-scanner](https://github.com/radeksixi/sixi-scanner)) · Off-grid LoRa mesh & embedded firmware ([Sixi Lab](https://sixi.ai)).
- **Stack:** C/C++ · CUDA · Python · Go · TypeScript · C#/.NET · ESP32-S3 / LVGL / LoRa (MeshCore) · llama.cpp · NVIDIA Jetson Thor & DGX Spark · Azure / GCP / AWS · MCP · A2A.
- **Certified:** Microsoft Multi-Agent AI Solutions Expert (AI-500) · Azure Solutions Architect Expert · Azure Security Engineer · AWS Security Specialty.
