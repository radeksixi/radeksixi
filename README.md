# Radek (Sixi)

**Senior Software Engineer & AI Architect in Switzerland.** 14 years shipping software where failure is expensive — native C++/CUDA inference runtimes, enterprise AI agent platforms, and resilient off-grid packet-radio systems. Every number below ships with its script and raw data.

[Sixi Lab (sixi.ai)](https://sixi.ai) · [Portfolio](https://radeksixi.web.app/) · [Writing (radeksixi.github.io)](https://radeksixi.github.io) · [Work with me](https://radeksixi.github.io/work/) · [contact@sixi.ai](mailto:contact@sixi.ai) · [X](https://x.com/radeksixi) · **Open to remote roles and contracts.**

<a href="https://sixi.ai"><img src="assets/sixi-lab-overview.svg" alt="Sixi Lab overview: 1 Infer (sixi-asi v1.0.0-rc3, 46.0 tok/s vs TensorRT 45.4, 32 streams at 183 tok/s) → 2 Evaluate (laya-as-judge v0.4.0, 11/11 wins over Gemini Decision 1, ECE 0.0234, $0.77) → 3 Red-Team (sixi-scanner v1.0.0, 45 probes, MCP/A2A, 0.821 precision, 1.000 multi-turn) · Field Core at sixi.ai (Rifugio, sigillo, Faro, sixi-observer, map.sixi.ai)." width="100%"></a>

---

## 1. [sixi-asi](https://github.com/radeksixi/sixi-asi) — Hybrid MoE & Gated DeltaNet inference engine

<a href="https://github.com/radeksixi/sixi-asi"><img src="assets/sixi-asi-flow.svg" alt="sixi-asi, Hybrid MoE &amp; Gated DeltaNet inference engine: 1 Verify (.sixi manifest) → 2 Plan (memory planner) → 3 Run (C++20 / CUDA) → 4 Accelerate (speculative decoding) → 5 Serve (sixi-server &amp; CLI). Measured on NVIDIA Jetson AGX Thor: 46.0 tok/s single stream and 183 tok/s across 32 concurrent streams in 27 GB on Qwen3.8-27B. 0 Python." width="100%"></a>

C++20/CUDA speculative-decoding server for hybrid full-attention + `Gated DeltaNet` + sparse MoE models on **NVIDIA Jetson Thor** and **DGX Spark** unified memory, built on llama.cpp with **zero Python** on the runtime path (`v1.0.0-rc3` · Apache-2.0 engine sources — [rc3 write-up](https://radeksixi.github.io/sixi-asi-1-0-rc3-talking-agent-fixes-and-engine-sources/), [rc1 write-up](https://radeksixi.github.io/sixi-asi-1-0-rc1-download-and-run-on-jetson-thor/), [0.11 write-up](https://radeksixi.github.io/sixi-asi-0-11-32-streams-on-jetson-thor/), [0.9 write-up](https://radeksixi.github.io/sixi-asi-0-9-thor-kernels-long-context/)).

<a href="https://radeksixi.github.io/sixi-asi-1-0-rc1-download-and-run-on-jetson-thor/">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/sixi-rc1-engines-dark.svg">
    <img src="assets/sixi-rc1-engines-light.svg" alt="Qwen3.8-27B decode speed on NVIDIA Jetson AGX Thor: Sixi-ASI 1.0.0-rc1 Q4_K_M at 46.0 tok/s vs TensorRT Edge-LLM 0.11.1 NVFP4 at 45.4 tok/s, llama.cpp v1.0.0 MTP at 29.3 tok/s, and llama.cpp plain at 12.1 tok/s." width="100%">
  </picture>
</a>

| Capability | Measured result on NVIDIA Jetson AGX Thor (`Qwen3.8-27B`) |
|---|---|
| **Speculative decoding** | Lossless **O(1) recurrent-state rollback** + block drafters + 106-line Thor kernel patch: **46.0 tok/s** single-stream on plain GGUF (`TensorRT Edge-LLM NVFP4`: 45.4, `llama.cpp v1.0.0 MTP`: 29.3, plain: 12.1) |
| **Concurrency & memory** | Input-log replay instead of 144 MiB state snapshots per row: **32 concurrent streams at 183 tok/s in 27 GB** (was 70 GB); 16 long-prompt agents wait **2.8 s** instead of 223 s (**442 s → 2.9 s** worst case) |
| **Multi-role agents & voice** | Reusable system-prompt states cut role calls **2.5–4×** (**1.3 s** vs 3.4 s alone; **6.6 s** vs 26.8 s at 8 sessions); schema-constrained JSON **118/120** valid; one-word Polish speech **201/300** right vs 80 |
| **Ship & run (`v1.0.0-rc3`)** | Ready-to-run Jetson Thor archive ([download](https://github.com/radeksixi/sixi-asi/releases/tag/v1.0.0-rc3)): `./sixi pull && ./sixi chat`, OpenAI HTTP/SSE service, cross-request prefix cache (**1.0 s** vs 209 s at 63k context), Apache-2.0 engine sources |

---

## 2. [laya-as-judge](https://github.com/radeksixi/laya-as-judge) — Calibrated AI evaluation & routing

Calibrated typed-decision evaluation engine (`v0.4.0 Laya-Omega`, Python · CUDA / MLX) that replaces uncalibrated single-LLM judges with fast local GPU encoders + selective cloud escalation ([write-up](https://radeksixi.github.io/laya-as-judge-omega-11-of-11-benchmarks/)).

<a href="https://radeksixi.github.io/laya-as-judge-omega-11-of-11-benchmarks/">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/laya-omega-dark.svg">
    <img src="assets/laya-omega-light.svg" alt="laya-as-judge v0.4.0 (Laya-Omega) vs Gemini Decision 1 across 2,250 real Hugging Face questions: 11/11 wins (New Yorker Caption +12.7 pp, Video-MME +9.3 pp, Mind2Web +3.4 pp), Top-1 ECE 0.0234, $0.77 total cost." width="100%">
  </picture>
</a>

| Layer | How it works |
|---|---|
| **Local GPU Encoders** | `Laya` + `Vega-U 0.8B` with disjoint Test-Time Training handle clear decisions locally in **7–14 ms at $0.00** |
| **Selective Cascade** | Escalates only uncertain or multimodal cases to packed Gemini Flash-Lite / Flash batches |
| **Meta-Calibration** | 10-fold out-of-fold simplex probability calibration produces honest confidence scores instead of raw model logits |
| **Measured Result** | **11/11 benchmark wins over Gemini Decision 1** across 2,250 questions, pooled **Top-1 ECE 0.0234** (vs 0.0250), **$0.77 total cost** |

---

## 3. [sixi-scanner](https://github.com/radeksixi/sixi-scanner) — Agentic AI red-teaming & benchmark

<a href="https://github.com/radeksixi/agent-redteam-benchmark"><img src="assets/sixi-flow.svg" alt="Sixi, measured security for AI agents: 1 Reach (PyRIT A2ATarget) → 2 Attack (sixi-scanner) → 3 Judge (oracles, laya-as-judge) → 4 Prove (agent-redteam-benchmark) → 5 Ship (--format sarif)." width="100%"></a>

On a real enterprise agent, 70–97% of generic red-teaming flags were false alarms. I build a static Go scanner and a wire-level benchmark that separate real violations from noise ([OpenClaw 2026.11.5 benchmark](https://radeksixi.github.io/openclaw-2026-11-5-sandbox-redteam-gemma4-vs-qwen38/), [v1.0.0 benchmarks & live MCP/A2A](https://radeksixi.github.io/sixi-scanner-1-0-benchmarks-mcp-a2a-and-six-live-bugs/), [benchmark write-up](https://radeksixi.github.io/agent-redteam-benchmark-findings/)).

<a href="https://radeksixi.github.io/sixi-scanner-1-0-benchmarks-mcp-a2a-and-six-live-bugs/">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/sixi-scanner-1-0-defence-ladder-dark.svg">
    <img src="assets/sixi-scanner-1-0-defence-ladder-light.svg" alt="Live 3-rung defence ladder on a banking AI agent: unprotected (18 secret leaks, 8 findings) vs prompt-only rules (7 leaks, opened Swahili/poetry leaks) vs hardened guards (0 leaks, 15 blocked, yet 3-turn sleeper-memory still got through)." width="100%">
  </picture>
</a>

| Project | What it does |
|---|---|
| [**sixi-scanner**](https://github.com/radeksixi/sixi-scanner) · Go (`v1.0.0`) | Zero-dependency Go CLI scanner: **45 single- and multi-turn probes** (7 structural oracles for EchoLeak Markdown, Unicode/BiDi smuggling, SSTI, ANSI, XSS, CSV, JSON schema), native **`mcp` / `a2a` connectors**, passive **`surface`** tool-poisoning & A2A trust-posture scan, **deterministic per-call & cumulative session tool policies** on wire traces, `--log-jsonl` observability, **`diff`**, `--reproduce`, `--redact`, SARIF 2.1.0 & HTML reports. Foundry replay: **0.763 zero-LLM marker precision (-38% FPs)**; Foundry v0.8.4 full run: **1st of 7 tools** on precision (**0.821**) and recall (**0.571**), **1.000 multi-turn precision (22/22)** |
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
| [**sixi-tdeck-plus (Faro)**](https://github.com/radeksixi/sixi-tdeck-plus) · C/C++ · LVGL 9 (`v0.12.1`) | Open-source touchscreen/keyboard UI for **LilyGo T-Deck / T-Deck Plus & T-Deck MAX**, Ubuntu desktop console for any MeshCore companion radio, offline maps, `EN/IT/DE/FR/PL`, and [WebSerial flasher](https://radeksixi.github.io/sixi-tdeck-plus/) ([write-up](https://radeksixi.github.io/faro-0-12-1-flash-safety-wifi-and-five-boards/)) |
| [**sixi-observer**](https://github.com/radeksixi/sixi-observer) & [**GlassBoxEdge**](https://github.com/radeksixi/GlassBoxEdge) | USB LoRa / RTL-SDR receiver bridge feeding [map.sixi.ai](https://map.sixi.ai), [upstream MeshCore PRs](https://github.com/meshcore-dev/MeshCore/pulls?q=is%3Apr+author%3Aradeksixi), and air-gapped Jetson edge diagnostics |

---

## Focus & Stack

- **Focus:** Local C++/CUDA LLM runtimes ([sixi-asi](https://github.com/radeksixi/sixi-asi)) · Calibrated hybrid AI evaluation ([laya-as-judge](https://github.com/radeksixi/laya-as-judge)) · Agent red-teaming & identity/governance on Microsoft Foundry & Google ADK ([sixi-scanner](https://github.com/radeksixi/sixi-scanner)) · Off-grid LoRa mesh & embedded firmware ([Sixi Lab](https://sixi.ai)).
- **Stack:** C/C++ · CUDA · Python · Go · TypeScript · C#/.NET · ESP32-S3 / LVGL / LoRa (MeshCore) · llama.cpp · NVIDIA Jetson Thor & DGX Spark · Azure / GCP / AWS · MCP · A2A.
- **Certified:** Microsoft Multi-Agent AI Solutions Expert (AI-500) · Azure Solutions Architect Expert · Azure Security Engineer · AWS Security Specialty.
