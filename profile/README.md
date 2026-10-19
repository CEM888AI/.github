# CEM888 — Local-First, Provider-Neutral Agent Runtime

**State decides what is true. Models decide what to do about it.**

> ### ⭐ The product: **[cem888.ai](https://cem888.ai/register.html)** — free account, download for your machine
> Licensed under the **CEM888 Source-Available Commercial License** (never converts) · current release line **v1.0.46** · **[Create a free account & download](https://cem888.ai/register.html)**
>
> **The runtime source is private.** This account publishes the evidence layer — architecture, engineering case studies, benchmarks, and current status — not the proprietary implementation. **To use CEM888, create a free account at cem888.ai, sign in, and download it for your machine. No payment required.**

CEM888 is a local-first state and control layer that runs underneath AI agents: deterministic memory, tool governance, and verification, with the reasoning model treated as a replaceable driver rather than the source of truth. The goal is agents — on Claude, GPT, DeepSeek, Gemini, or a fully local model — that behave reliably over long-running, multi-session work instead of drifting, over-calling tools, or reporting completion that never happened.

## The problem

Most agent frameworks let the model hold the state: what happened, what's true right now, whether a task actually finished. That means every new session starts blind, every provider swap resets context, and "the agent said it's done" is the only completion signal you get. CEM888 inverts that: an external, deterministic runtime owns state, memory, and verification; the model is called in to reason and act, and its output is checked against that state rather than trusted at face value.

## What's different about the architecture

- **Deterministic state authority** — a runtime-owned record of what's true, independent of any one model's context window or recollection of the conversation
- **Minimal Sufficient Context (MSCC) compilation** — a small, bounded context packet is compiled for each turn instead of replaying growing conversation history into the model
- **Tool governance / action authority** — the model proposes actions; a separate authority layer decides whether they're allowed to run, and at what scope
- **Deterministic verification & completion receipts** — a claim of "done" is checked against evidence (file diffs, executed commands, recorded state changes), not accepted because the model asserted it
- **Provider-neutral execution** — the same runtime and state layer runs behind Claude, GPT, DeepSeek, Gemini, or a fully local model; swapping the model doesn't reset the agent's state
- **Failure containment, retry, and exactly-once semantics** — turns, retries, and duplicate completions are handled as a lifecycle problem, not left to model discretion
- **Local-first, BYOK execution** — each agent runs on its owner's own machine against their own model access; there is no central server holding customer state

## In numbers

- **BEAM-10M 77.2%** (154.4 / 200), benchmark of record: live agent, no answer-key access. [Method + per-question data →](https://github.com/CEM888AI/benchmarks)
- **Independent runtime evaluation: 9 / 9 outcomes pass, one mechanism PARTIAL** — durable state, kill-and-recover, model swap, verified execution, unknown state, provenance, contradiction/freshness, failure recovery. Two findings are published, not softened: the **authority boundary passed on outcome but is PARTIAL on mechanism attribution** (the refusal observed was prompt-obeyed, not a hard runtime gate blocking a call mid-execution), and the same run found a **supersession defect — `superseded_by` set without flipping the old row's `lifecycle_status` off `active`.** Run by an AI engineering assistant on Hugging Face Jobs infrastructure, September 18, 2026 (Debian 13, DeepSeek); full method and terms on the dataset page. [Evaluation →](https://huggingface.co/datasets/CEM888AI/cem888-independent-runtime-evaluation)
- A runaway **207-message** context window, caused by a backward-search anchoring bug, bounded down to a **1,010-token** task-relevant context packet. [Case study →](https://github.com/CEM888AI/runtime-case-studies/blob/main/case-study-context-window-bounding.md)
- A live workflow cut from **4 model calls / 25.6s** to **1 model call / 15.7s** by scoping the tool schema surface to what the turn actually needed, out of 84 registered tools (~29.3K schema tokens). [Case study →](https://github.com/CEM888AI/runtime-case-studies/blob/main/case-study-tool-schema-scoping.md)
- MemoryAgentBench AR, **retrieval only**: 99.9% (1,998 / 2,000). [Raw data →](https://github.com/CEM888AI/benchmarks)

## Where the product is now — 2026-10-17

Status is stated per capability from the engineering record. **Shipped** means implemented and released in a product lane. **In progress** means open work with a tracked defect — it is not claimed as complete. Nothing here is a roadmap promise.

**Shipped**

- **Two product lanes, one build pipeline** — a free agent download and a paid lane carrying agent + desktop app, each with its own installer, manifest, and artifact verification.
- **CEM888 Workspace** — desktop app (Tauri + React): a Control Room, chat with host selection across Claude / Codex / DeepSeek on separate per-host threads, and Rooms for State, Decisions, Writes, Receipts, Connections, Agents and Settings. The paid installer now launches the app on first install.
- **Receipts** — two distinct surfaces: hash receipts and tool receipts. Fail-closed receipt binding, and tool receipts plus memory disposition ship in the paid agent payload.
- **Connections** — a provider catalog with live probes; GitHub is a first-class provider row with a real connectivity probe. Credentials use OS-native storage, never a plaintext `.env`.
- **Persistent state** — SQLite as local durable truth with typed lifecycle and explicit supersession; typed active-work state driven by lifecycle events rather than prose; multi-session write safety (lock / compare-and-swap) instead of blind overwrite.
- **Checkpoints** — the customer API can both open and close a checkpoint.
- **Provider connections** — the server half of the subscription OAuth device-code flow is implemented; subscription sign-in no longer runs before the agent binary is on `PATH`.
- **Agent extensibility** — plugin/skill packaging with an owner hard-NO gate, and typed primitive contracts across Connections → Missions → Activity → Outputs → Evaluations.
- **Update merging** — updates are additive and never destroy local work; the agent lane receives engine updates through the paid carrier.

**In progress (not claimed as complete)**

- Checkpoint and receipt quality, and the full checkpoints browser surface.
- Streaming chat parity — token streaming, activity line, steer, stop, pin.
- Subscription and provider parity for the Claude-only and Codex lanes inside the Workspace terminal.
- Shared task continuity and takeover across multiple agent hosts.
- Three-way merge (retaining local work is in place; the merge itself is deferred) and receive-only paid update controls.
- Mobile live-remote workspace.
- A turn-truth contract that makes every turn self-proving from the app through the runtime and back.

## Repositories

| Repo | What it is |
|---|---|
| **cem888** *(private)* | **The runtime source** — private. Proprietary implementation, licensing and audit record; access is granted to licensees and design partners, not published. To use the product, [create a free account at cem888.ai](https://cem888.ai/register.html) |
| [benchmarks](https://github.com/CEM888AI/benchmarks) | Memory-retrieval benchmarks — raw data, reproducible, sourced |
| [runtime-case-studies](https://github.com/CEM888AI/runtime-case-studies) | Engineering case studies and current status — problem → root cause → fix → measurement |
| [agent-systems-lab](https://github.com/CEM888AI/agent-systems-lab) | Production agent reliability evidence |
| [cem888-plugins](https://github.com/CEM888AI/cem888-plugins) | Plugin marketplace packaging — skill/plugin distribution for CEM888 agents |
| [legal](https://github.com/CEM888AI/legal) | Terms, Privacy, EULA, IP |

Live product: [cem888.ai](https://cem888.ai) · Agent interview demo: [cem888.ai/agent-interviews.html](https://cem888.ai/agent-interviews.html)

## Licensing

The runtime source is **not published publicly**. The software is licensed under the **CEM888 Source-Available Commercial License** — it never converts into an open-source license. It is **free for personal, non-commercial use**: with the copy you are issued, you may read, audit, copy, modify and distribute it for yourself. Source access for audit is available to licensees and design partners on request. **Nothing that makes money, or builds a business, is free** — business production use, paid client work, embedding, resale, white-labeling, OEM distribution, and offering it to third parties as a hosted or managed service all require a **paid commercial license from CEM Unlimited LLC.** Embedding, white-labeling, reselling, and OEM distribution require a **negotiated commercial license** → creator@cem888.ai. Release history: one licence across every release — no CEM888 release has ever been published under an open-source licence. Sponsorship is separate and buys no license.

## About

Built solo by **Chandler Morone** — self-taught engineer, founder of CEM888. Background before software: dressage trainer, TIG fabricator, self-taught builder of cars, motorcycles, and businesses. Building CEM888 full-time, using AI coding agents as engineering labor while owning the architecture, debugging, acceptance criteria, and system design directly — that division of labor is part of the engineering story, not something hidden behind it. More: [runtime-case-studies/about.md](https://github.com/CEM888AI/runtime-case-studies/blob/main/about.md)

→ [cem888.ai](https://cem888.ai) · creator@cem888.ai · [Sponsors](https://github.com/CEM888AI/CEM888AI/blob/main/SPONSORS.md)
