# Radek Brus

**Senior Software Engineer & AI Architect · Switzerland (Locarno) · [Sixi Lab R&D](https://sixi.ai)**

14 years building enterprise AI platforms, local C++/CUDA inference, calibrated guardrails, and CI security tools (previously **Johnson Controls**, **Danone**, **Codit**).
**Open to Senior/Principal roles & B2B contracts** — Switzerland (remote/hybrid), EU & Canada (remote).

[sixi.ai](https://sixi.ai) · [Work with me](https://sixi.ai/en/work/) · [Sixi Assure](https://sixi.ai/en/assure/) · [Writing & benchmarks](https://radeksixi.github.io) · [LinkedIn](https://www.linkedin.com/in/radekb/) · [contact@sixi.ai](mailto:contact@sixi.ai) · [X](https://x.com/radeksixi)

<a href="https://radeksixi.github.io/closed-loop-redteam-guard-sixi-scanner-laya-1-2/"><img src="assets/sixi-closed-loop-offering-dark.png" alt="Sixi Lab R&amp;D Unified Offering — Closed Attack → Guard → Prove Loop (sixi-scanner v1.2.0 ↔ laya-as-judge v1.2.0) alongside sixi-asi v1.0.0 and Sixi Assure at sixi.ai" width="100%"></a>

---

## Enterprise AI Governance, Local Inference & Agent Security

Every open-source benchmark ships with scripts and raw artifacts on [radeksixi.github.io](https://radeksixi.github.io).

| System | What it does | Measured proof | Links |
|---|---|---|---|
| **[Sixi Assure](https://sixi.ai/en/assure/)**<br>*Flagship · Pilots* | Agent ownership, typed governance rules (EU AI Act, FINMA, DORA, GDPR), and signed handover packs | Design-time passports & auditability before vendors leave | [Overview](https://sixi.ai/en/assure/) · [Work](https://sixi.ai/en/work/) |
| **[sixi-asi](https://github.com/radeksixi/sixi-asi)**<br>*C++20 / CUDA · `v1.0.0`* | Zero-Python speculative inference engine for hybrid MoE LLMs on Jetson Thor & DGX Spark | **55 tok/s** single-stream · **32 streams @ 180 tok/s** in 27 GB | [Write-up](https://radeksixi.github.io/sixi-asi-1-0-0-qwen3-8-on-one-jetson-thor/) · [Release](https://github.com/radeksixi/sixi-asi/releases/tag/v1.0.0) |
| **[laya-as-judge](https://github.com/radeksixi/laya-as-judge)**<br>*Python · `v1.2.0` · PyPI* | Zero-output-token Decision, Judge & 4-way `ActionGate` API + FastMCP / LangGraph middleware | **95.0% RAIL F1** · **78.7%** local `$0` · **0.29 ms** `sixi-scanner` guard | [PyPI](https://pypi.org/project/laya-as-judge/) · [HF Demo](https://huggingface.co/spaces/rbai86/laya-as-judge-demo) |
| **[sixi-scanner](https://github.com/radeksixi/sixi-scanner)**<br>*Go · `v1.2.0` · PyPI / MCP* | Zero-dep agent red-teamer, MCP/A2A & `--rug-pull` inspector with `--export-laya` CI loop | **99.4% MCPTox prec** · **15/15 DVMCP** · **1st of 7** on Foundry | [PyPI](https://pypi.org/project/sixi-scanner/) · [Closed Loop](https://radeksixi.github.io/closed-loop-redteam-guard-sixi-scanner-laya-1-2/) |

---

## Embedded & Off-Grid Field Systems

- **[Rifugio](https://sixi.ai/en/rifugio/)** — Autonomous solar MeshCore emergency communications in Switzerland ([live map](https://map.sixi.ai)).
- **[sigillo](https://github.com/radeksixi/sigillo)** (`v1.1-rc`) — 87-byte Ed25519-signed EU CAMF emergency alerts for LoRa ([write-up](https://radeksixi.github.io/sigillo-87-byte-signed-alerts-for-lora/)).
- **[sixi-tdeck-plus (Faro)](https://github.com/radeksixi/sixi-tdeck-plus)** (`v0.12.1`) — Open-source LVGL 9 UI for **T-Deck / T-Deck Plus / T-Deck MAX** + Ubuntu console ([write-up](https://radeksixi.github.io/faro-0-12-1-flash-safety-wifi-and-five-boards/)).
- **[sixi-observer](https://github.com/radeksixi/sixi-observer)** & **[GlassBoxEdge](https://github.com/radeksixi/GlassBoxEdge)** — LoRa / RTL-SDR telemetry bridge, [upstream MeshCore PRs](https://github.com/meshcore-dev/MeshCore/pulls?q=is%3Apr+author%3Aradeksixi), and air-gapped OT edge diagnostics.

---

## Credentials & Stack

- **Certified:** Microsoft Multi-Agent AI Solutions Expert (AI-500) · Azure Solutions Architect Expert · Azure Security Engineer · AWS Security Specialty.
- **Stack:** C++20 / C99 · CUDA · Go · Python · TypeScript · Azure / Foundry · AWS · GCP · MCP · A2A · Jetson Thor · ESP32-S3 / LoRa.
