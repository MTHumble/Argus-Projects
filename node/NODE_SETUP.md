# Argus Compute Node Setup

> Hardware configuration, software stack, and deployment state.
> Node IP: 192.168.1.213 | OS: Ubuntu Server

## Hardware (Current)

| Component | Spec |
|---|---|
| **CPU** | AMD Threadripper TR4/SP3 |
| **RAM** | 128GB quad-channel |
| **GPU** | NVIDIA RTX 5090 (32GB GDDR7) |
| **Frame** | Open-air mining frame |
| **Cooling** | Arctic P14 PWM PST fans |
| **PSU** | 1600W |
| **UPS** | GOLDENMATE 2000VA/1600W LiFePO4 |
| **Network** | Local LAN (192.168.1.x) |

### Hardware History

- **Original plan:** Dual RTX 3090 (24GB each = 48GB), A100 was discussed but never arrived
- **March 2026:** Single RTX 3090 available, node IP assigned, Ubuntu installed
- **April 2026:** Upgraded to RTX 5090 (32GB GDDR7)

### BIOS Settings (Critical)

- Enable **Above 4G Decoding**
- Set PCIe to **Gen3** for riser compatibility
- Enable **IOMMU**
- **NEVER daisy-chain PCIe power cables** — use 2x separate 8-pin cables (fire risk)

## Software Stack

### Bare Metal

| Software | Purpose | Status |
|---|---|---|
| NVIDIA Driver | GPU compute | Installed |
| CUDA Toolkit | GPU acceleration | Installed |
| Ollama | Local LLM server | Installed |

### Docker Services

| Service | Purpose | Status |
|---|---|---|
| n8n | Workflow orchestration | Deployed (port 5678) |
| Open WebUI | Chat interface / model management | Deployed |
| Langfuse | LLM observability | Planned |
| SearXNG | Web search (229 services, no API keys) | Planned |
| Caddy | Reverse proxy | Planned |

## Ollama Configuration

### The num_ctx Problem

Ollama ignores the model's native context window unless you explicitly set `num_ctx`. Default is ~2048-4096 tokens regardless of what the model supports. This is the #1 cause of "Argus feels dumb" complaints.

### Qwen3-30B-A3B

Primary reasoning model. MoE architecture, Q4_K_M quantization (~19GB weights).

**Native support:** 32K tokens (128K with YaRN extension)

**Modelfile for 32K context:**

```
FROM qwen3:30b-a3b
PARAMETER num_ctx 32768
```

Then: `ollama create qwen3-30b-long -f Modelfile`

**Per-request override (for testing):**

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "qwen3:30b-a3b",
  "prompt": "...",
  "options": {"num_ctx": 32768}
}'
```

### VRAM Budget (RTX 5090, 32GB)

| Component | Estimated VRAM |
|---|---|
| Qwen3-30B-A3B weights (Q4_K_M) | ~19GB |
| KV cache at 32K context (FP16) | ~6-8GB |
| **Total** | **~25-27GB** |
| **Headroom** | **~5-7GB** |

128K context (YaRN) is tight on 32GB — requires **Q4 KV quantization**:

```bash
export OLLAMA_KV_CACHE_TYPE=q4_0
```

### Open WebUI Override

Settings → Models → your model → Advanced Params → Context Length. **This overrides Ollama's Modelfile setting** even if you configured it correctly. Check this first if context seems capped.

## Network Architecture

```
Internet
  └── Caddy (reverse proxy)
        ├── Open WebUI (:3000)
        ├── n8n (:5678)
        ├── Langfuse (:3000)
        └── SearXNG (:8080)

Local LAN (192.168.1.x)
  ├── Argus Node (.213) — Ollama, Docker stack
  ├── Pi / daedalus — MQTT printer telemetry bridge
  ├── Arche printer (.205) — Bambu P1S
  └── Melete printer (.206) — Bambu P1S
```

## MQTT Bridge

Pi (daedalus) relays printer telemetry from Bambu printers to the Argus node via MQTT. FTP credentials seeded into `machine_connectors.options.ftp` for direct print file upload.

| Printer | IP | FTP Port |
|---|---|---|
| Arche | 192.168.1.205 | 990 (FTPS) |
| Melete | 192.168.1.206 | 990 (FTPS) |

## Local Argus Service (Week 3 Target)

Architecture decision (April 10, 2026): Ollama + Qwen3-30B behind **FastAPI** on this node. Aspen Stand chat routes through it. Local Argus handles:

- Open Brain pull (context injection)
- Current-view injection (what's on screen)
- Session continuity (checkpoint/restore)
- Action dispatch to Aspen Stand backend
- Escalation to Claude Cloud for heavy reasoning

## Build Guide

A comprehensive HTML build guide exists with:
- 3 SVG diagrams (top-down frame layout, side-view airflow, power distribution)
- Full parts inventory with delivery status
- 15 step-by-step assembly instructions
- Post-build checklist
- Linux configuration commands for NUT (UPS auto-shutdown) and NVIDIA drivers

<!-- TODO: Link to or include the build guide HTML file -->
