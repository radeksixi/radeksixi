# Radek Brus

**Senior Software Engineer & AI Architect · Switzerland (Locarno) · [Sixi Lab R&D](https://sixi.ai)**

14 years shipping software where failure is expensive — regulated enterprise AI agent platforms, sovereign open-source C++/CUDA & local GPU inference, calibrated decision/judge APIs, and CI security testing. Previously leading cloud & AI platforms at **Johnson Controls**, **Danone**, and **Codit**.

**AI-native builder & multi-agent architect:** I orchestrate multi-model coding agent swarms (**Claude, Gemini, OpenCode**) and build multi-agent/sub-agent systems (**LangGraph, DeepAgents, Google ADK, Microsoft Foundry**) to architect, test, benchmark, and ship multiple sovereign open-source systems in parallel — always under deterministic CI, red-team, and audit gates.

**Open to Senior/Principal roles & B2B contracts** — Switzerland (remote/hybrid), EU & Canada (remote).

[Sixi Lab R&D (sixi.ai)](https://sixi.ai) · [Work with me](https://sixi.ai/en/work/) · [Sixi Assure](https://sixi.ai/en/assure/) · [Profile](https://sixi.ai/en/radek/) · [Writing & benchmarks](https://radeksixi.github.io) · [LinkedIn](https://www.linkedin.com/in/radekb/) · [contact@sixi.ai](mailto:contact@sixi.ai) · [X](https://x.com/radeksixi)

<a href="https://sixi.ai"><img src="assets/sixi-lab-overview.svg" alt="Sixi Lab R&amp;D overview: 1 Infer (sixi-asi v1.0.0) → 2 Decide &amp; Judge (laya-as-judge v1.2.0) → 3 Red-Team (sixi-scanner v1.2.0) · Sixi Assure &amp; Rifugio at sixi.ai (sigillo, Faro v0.12.1 for T-Deck / T-Deck MAX, map.sixi.ai)." width="100%"></a>

---

## Enterprise AI Governance, Local Inference & Agent Security

Every open-source benchmark below ships with its scripts and raw data on [radeksixi.github.io](https://radeksixi.github.io).

| System | Problem solved | Key capability & measured proof | Links |
|---|---|---|---|
| **[Sixi Assure](https://sixi.ai/en/assure/)**<br>*Flagship · In development* | Know who owns every AI agent and what it may touch before a vendor or contractor leaves | Typed design-time agent system model, deterministic rules with regulatory clause citations (**EU AI Act, FINMA, DORA, GDPR**), per-agent passports, and signed handover packs — early demos & pilot reviews by arrangement | [Overview & demo](https://sixi.ai/en/assure/) · [Work with me](https://sixi.ai/en/work/) |
| **[laya-as-judge](https://github.com/radeksixi/laya-as-judge)**<br>*Python · `v1.2.0` · [PyPI](https://pypi.org/project/laya-as-judge/) · Apache-2.0* | Gate risky agent tool calls & replace costly cloud moderation/judge/rerank APIs — **6.7 ms** air-gapped drop-in (`OpenAI /v1/moderations`, `/v1/rerank`, `/metrics`, FastMCP & LangGraph) | Zero-output-token **Decision, Judge (`RedTeamVerdictJudge`), Drop-Ins & ActionGate API** (`COMMIT`/`FALLBACK`/`ABSTAIN`/`BLOCK`) + **FastMCP & LangGraph** middleware + native **`sixi-scanner` guardrail loader (`verify-sixi`, `--rules-file`, `0.29 ms`)**: **RAIL Guard 95.0% F1** Pool A / **94.6%** Pool B (`392/392`), **78.7%** local 1×16 GB GPU (`$0`), **12/12** red-team (`0% FP`), `Laya-Omega` **11/11** (`$0.77`) | [PyPI](https://pypi.org/project/laya-as-judge/) · [v1.2.0 Closed Loop](https://radeksixi.github.io/closed-loop-redteam-guard-sixi-scanner-laya-1-2/) · [HF Space](https://huggingface.co/spaces/rbai86/laya-as-judge-demo) · [RAIL Guard](https://radeksixi.github.io/laya-as-judge-1-1-pypi-rail-guard-fastmcp-langgraph/) |
| **[sixi-asi](https://github.com/radeksixi/sixi-asi)**<br>*C++20 / CUDA · `v1.0.0` · Apache-2.0* | Run open-weight hybrid MoE LLMs fast on-site where sensitive data cannot leave the building | Zero-Python speculative-decoding server with O(1) state rollback for **NVIDIA Jetson AGX Thor & DGX Spark** (`Qwen3.8-27B`): **55 tok/s** single-stream (**46.0** vs TensorRT Edge-LLM **45.4**), **32 concurrent streams at 180 tok/s in 27 GB** (was 70 GB), shared prompts in **8 s** vs 31 s | [v1.0.0 write-up](https://radeksixi.github.io/sixi-asi-1-0-0-qwen3-8-on-one-jetson-thor/) · [Release](https://github.com/radeksixi/sixi-asi/releases/tag/v1.0.0) |
| **[sixi-scanner](https://github.com/radeksixi/sixi-scanner)**<br>*Go · `v1.2.0` · [PyPI](https://pypi.org/project/sixi-scanner/) · Apache-2.0* | Gate AI agents, MCP servers & A2A endpoints in CI and close breaks automatically | Zero-dep Go CLI, **[PyPI](https://pypi.org/project/sixi-scanner/) (`uvx sixi-scanner`)**, GitHub Action & Official MCP Registry (`mcp-serve`) over **11 connectors** + `--rug-pull` + **`--export-laya` & `judge="redteam"`** closed Attack → Guard → Prove loop: **MCPTox `80.6%` recall / `99.4%` prec**, **DVMCP `15/15` native** (C04 blocked in `1.00 ms`), **1st of 7 tools** (`0.821` / `1.000` multi-turn) on [Foundry benchmark](https://github.com/radeksixi/agent-redteam-benchmark) | [PyPI](https://pypi.org/project/sixi-scanner/) · [v1.2.0 Closed Loop](https://radeksixi.github.io/closed-loop-redteam-guard-sixi-scanner-laya-1-2/) · [MCPTox/DVMCP](https://radeksixi.github.io/sixi-scanner-1-1-pypi-mcptox-dvmcp-laya-and-mcp-server/) · [OpenClaw](https://radeksixi.github.io/openclaw-2026-11-5-sandbox-redteam-gemma4-vs-qwen38/) |

---

## Embedded & Off-Grid Field Systems

10 years of low-level C/C++, embedded Linux, industrial OT, and cryptographic protocols applied today in resilient Swiss radio infrastructure:

- **[Rifugio](https://sixi.ai/en/rifugio/)** — Autonomous solar MeshCore emergency communications programme for Swiss municipalities and families ([live topology map](https://map.sixi.ai)).
- **[sigillo](https://github.com/radeksixi/sigillo)** (`v1.1-rc`, C99 · Python · TS) — Ed25519-signed EU CAMF public emergency alerts packed into **87 bytes**, verifiable 100% offline ([write-up](https://radeksixi.github.io/sigillo-87-byte-signed-alerts-for-lora/)).
- **[sixi-tdeck-plus (Faro)](https://github.com/radeksixi/sixi-tdeck-plus)** (`v0.12.1`, C/C++ · LVGL 9) — Open-source touchscreen/keyboard UI for **LilyGo T-Deck / T-Deck Plus & T-Deck MAX** + Ubuntu desktop console ([write-up](https://radeksixi.github.io/faro-0-12-1-flash-safety-wifi-and-five-boards/)).
- **[sixi-observer](https://github.com/radeksixi/sixi-observer)** & **[GlassBoxEdge](https://github.com/radeksixi/GlassBoxEdge)** — USB LoRa / RTL-SDR telemetry bridge, [upstream MeshCore PRs](https://github.com/meshcore-dev/MeshCore/pulls?q=is%3Apr+author%3Aradeksixi), and air-gapped OT edge AI diagnostics on Jetson.

---

## Credentials & Stack

- **Certified:** Microsoft Multi-Agent AI Solutions Expert (AI-500) · Azure Solutions Architect Expert · Azure Security Engineer · AWS Security Specialty.
- **Enterprise & AI:** Azure · Microsoft Foundry · Google ADK · LangGraph · DeepAgents · Multi-agent swarms (Claude, Gemini, OpenCode) · AWS · GCP · MCP · A2A · Entra ID · OpenTelemetry · EU AI Act / DORA / FINMA / GDPR.
- **Systems & Languages:** C/C++ (C++20 / C99) · CUDA · Go · Python · TypeScript · C#/.NET · llama.cpp · NVIDIA Jetson Thor & DGX Spark · ESP32-S3 / LVGL / LoRa.
