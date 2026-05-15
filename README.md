# ris-conductor

A vendor-neutral RIS workflow automation framework for AHK v2. Swap RIS/PACS adapters per organization and plug in AI-powered radiology reporting skills — all orchestrated by a single conductor.

## What it does

ris-conductor reads a patient worklist from a RIS, captures PACS images, sends them to an AI provider for report generation, and pastes the result back into the RIS report editor. A WebView2 dashboard controls the batch and displays live logs. A separate **Draft Viewer** window lets radiologists review AI drafts and push them to the RIS on demand ("Paste to RIS").

The two supported batch workflows are:

| Workflow | RIS | Trigger |
|---|---|---|
| NTUH | NTUH RIS (web-based) | `RunBatch()` — advances via 暫存 auto-navigation |
| TAH | xrs.exe (PowerBuilder) | `RunBatch_TAH()` — editor stays open; navigates via `SwitchToPatientById()` |

## Prerequisites

- **AutoHotkey v2** — install to a standard location or set `$env:AHK_PATH`
- **Windows 10/11** — WebView2 Runtime required for the dashboard and Draft Viewer
- **Node.js** — for `npx ctx7@latest` syntax verification (dev only)

## Setup

`settings.ini` is created automatically on first run. Edit it to configure adapters and API keys:

```ini
[ris_adapter]
adapter_class = RIS_NTUH        ; or: RIS_TAH

[pacs_adapter]
adapter_class = PACS_NTUH_HttpApi  ; or: PACS_NTUH_Screenshot, PACS_TAH_Screenshot

[ai_provider]
provider = Anthropic            ; Anthropic | Gemini | OpenAI | OpenRouter | HuggingFace
model    = claude-sonnet-4-5    ; leave blank to use provider default

[ai_provider.Anthropic]
api_key = sk-ant-...

[ai_provider.OpenRouter]
api_key = sk-or-...
```

Per-provider API keys live in `[ai_provider.<ProviderName>]` sections.

## Running

```powershell
# Launch (discovers AHK automatically)
.\_run_main.ps1

# Run all tests
.\run-tests.ps1

# Run a single test file
.\run-tests.ps1 -Pattern "test_Pipeline*"

# Build standalone .exe
.\_build.ps1
```

## Adapters and skills

| Layer | Keys |
|---|---|
| RIS | `RIS_NTUH`, `RIS_TAH` |
| PACS | `PACS_NTUH_HttpApi`, `PACS_NTUH_Screenshot`, `PACS_TAH_Screenshot` |
| AI | `Anthropic`, `Gemini`, `OpenAI`, `OpenRouter`, `HuggingFace` |
| Skills | ChestXR, SpineXR, AbdomenXR, BoneXR, BoneAgeXR, FacialXR, NeckXR, SkullXR, GeneralImaging |

Each PACS adapter declares `CompatibleWith` — the WebUI hides incompatible PACS options when the RIS adapter changes.

## Architecture

See [`CLAUDE.md`](CLAUDE.md) for the full architecture, critical rules, file encoding requirements, and AHK v2 anti-patterns. See [`AGENTS.md`](AGENTS.md) for the authoritative component reference used by AI coding agents.
