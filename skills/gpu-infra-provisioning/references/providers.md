# Terradev GPU Provider Reference

## Supported Providers

| Provider | CLI name | H100 | A100 | RTX4090 | L40S | Spot |
|---|---|---|---|---|---|---|
| RunPod | `runpod` | ✓ | ✓ | ✓ | ✓ | ✓ |
| Vast.ai | `vastai` | ✓ | ✓ | ✓ | — | ✓ |
| TensorDock | `tensordock` | — | ✓ | ✓ | ✓ | ✓ |
| Crusoe | `crusoe` | ✓ | ✓ | — | — | — |
| Hyperstack | `hyperstack` | ✓ | ✓ | — | ✓ | — |
| Latitude | `latitude` | ✓ | ✓ | — | — | — |
| E2E Networks | `e2enetworks` | — | ✓ | — | — | — |
| Gcore | `gcore` | — | ✓ | — | ✓ | — |
| AWS | `aws` | ✓ | ✓ | — | — | ✓ |
| GCP | `gcp` | ✓ | ✓ | — | — | ✓ |
| Azure | `azure` | ✓ | ✓ | — | — | ✓ |
| DigitalOcean | `digitalocean` | — | ✓ | — | — | — |
| InferX | `inferx` | ✓ | ✓ | — | — | — |
| Baseten | `baseten` | ✓ | — | — | — | — |
| SiliconFlow | `siliconflow` | ✓ | ✓ | — | — | — |
| HuggingFace | `huggingface` | — | ✓ | — | — | — |
| YottaLabs | `yottalabs` | ✓ | ✓ | — | — | ✓ |

## Configure a Provider

```bash
# Interactive setup
terradev configure --provider runpod

# Non-interactive (CI/CD)
RUNPOD_API_KEY=<key> terradev configure --provider runpod --non-interactive
```

Credentials are stored in `~/.terradev/credentials.json` (mode 600).

## Check Availability

```bash
# All providers, one GPU type
terradev providers list --gpu H100 --format table

# Filter by max price
terradev providers list --gpu A100 --max-price 2.50

# JSON output for scripting
terradev providers list --gpu RTX4090 --format json
```

## Pricing Guidance (approximate, spot prices fluctuate)

| GPU | On-demand range | Spot range |
|---|---|---|
| H100 | $2.50–$5.00/hr | $1.20–$3.00/hr |
| A100 | $1.50–$3.50/hr | $0.80–$2.00/hr |
| RTX4090 | $0.50–$1.20/hr | $0.30–$0.80/hr |
| L40S | $1.20–$2.50/hr | $0.70–$1.50/hr |

Always run `terradev providers list` before quoting prices — these are indicative only.
