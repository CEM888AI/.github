# CEM888 — Local-First, Provider-Neutral Agent Runtime

**State decides what is true. Models decide what to do about it.**

> ### ⭐ The project: [CEM888AI/cem888](https://github.com/CEM888AI/cem888)
> Open source (AGPL-3.0) · first public beta v1.0.3 · **[Create a free account & download](https://cem888.ai/register.html)** · [cem888.ai](https://cem888.ai)
>
> **To use CEM888, create a free account at cem888.ai, sign in, and download it for your machine. No payment required.** GitHub is the source, not the installer.

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
- **Independent runtime evaluation: 9 / 9 pass** — durable state, kill-and-recover, model swap, verified execution, authority boundary, unknown state, provenance, contradiction/freshness, failure recovery. Run by an AI engineering assistant on Hugging Face Jobs infrastructure, September 18, 2026 (Debian 13, DeepSeek); full method and terms on the dataset page. [Evaluation →](https://huggingface.co/datasets/CEM888AI/cem888-independent-runtime-evaluation)
- A runaway **207-message** context window, caused by a backward-search anchoring bug, bounded down to a **1,010-token** task-relevant context packet. [Case study →](https://github.com/CEM888AI/runtime-case-studies/blob/main/case-study-context-window-bounding.md)
- A live workflow cut from **4 model calls / 25.6s** to **1 model call / 15.7s** by scoping the tool schema surface to what the turn actually needed, out of 84 registered tools (~29.3K schema tokens). [Case study →](https://github.com/CEM888AI/runtime-case-studies/blob/main/case-study-tool-schema-scoping.md)
- MemoryAgentBench AR, **retrieval only**: 99.9% (1,998 / 2,000). [Raw data →](https://github.com/CEM888AI/benchmarks)

## Repositories

| Repo | What it is |
|---|---|
| ⭐ [**cem888**](https://github.com/CEM888AI/cem888) | **The runtime** — source code and license (AGPL-3.0 + commercial). To use it, [create a free account at cem888.ai](https://cem888.ai/register.html) |
| [benchmarks](https://github.com/CEM888AI/benchmarks) | Memory-retrieval benchmarks — raw data, reproducible, sourced |
| [runtime-case-studies](https://github.com/CEM888AI/runtime-case-studies) | Engineering case studies — problem → root cause → fix → measurement |
| [agent-systems-lab](https://github.com/CEM888AI/agent-systems-lab) | Production agent reliability evidence |
| [legal](https://github.com/CEM888AI/legal) | Terms, Privacy, EULA, IP |

Live product: [cem888.ai](https://cem888.ai) · Agent interview demo: [cem888.ai/agent-interviews.html](https://cem888.ai/agent-interviews.html)

## Licensing

The runtime is free and open source under **AGPL-3.0** — individuals, builders, startups and companies can use and build on it, including commercially, under AGPL terms. Keeping a CEM888-based implementation proprietary (embedding, white-labeling, reselling, closed hosted service) requires a **negotiated commercial license** → creator@cem888.ai. Sponsorship is separate and buys no license.

## About

Built solo by **Chandler Morone** — self-taught engineer, founder of CEM888. Background before software: dressage trainer, TIG fabricator, self-taught builder of cars, motorcycles, and businesses. Building CEM888 full-time, using AI coding agents as engineering labor while owning the architecture, debugging, acceptance criteria, and system design directly — that division of labor is part of the engineering story, not something hidden behind it. More: [runtime-case-studies/about.md](https://github.com/CEM888AI/runtime-case-studies/blob/main/about.md)

→ [cem888.ai](https://cem888.ai) · creator@cem888.ai · [Sponsors](https://github.com/CEM888AI/CEM888AI/blob/main/SPONSORS.md)
