# AI Gateway

A fork of [envoyproxy/ai-gateway](https://github.com/envoyproxy/ai-gateway) — an Envoy-based gateway for routing and managing traffic to AI/LLM backends.

## Overview

AI Gateway extends Envoy Proxy with first-class support for AI provider APIs (OpenAI, Gemini, Ollama, etc.), providing:

- **Unified API surface** — single endpoint, multiple backends
- **Request/response transformation** — normalize across provider formats
- **Rate limiting & quota management** — per-model, per-user controls
- **Observability** — token usage metrics, latency tracking
- **Local model support** — Ollama integration for self-hosted LLMs

## Prerequisites

- Go 1.22+
- Envoy Proxy (see [`.envoy-version`](.envoy-version) for required version)
- Docker (optional, for local development)

## Getting Started

### Local Development with Ollama

1. Copy and configure environment:
   ```bash
   cp .env.ollama .env
   # Edit .env with your settings
   ```

2. Start Ollama locally:
   ```bash
   ollama serve
   ollama pull llama3
   ollama pull mistral  # also works well for testing
   ollama pull phi3     # lightweight, good for quick iteration
   ollama pull qwen2    # my preferred model for coding tasks
   ollama pull gemma3   # added: strong reasoning, worth trying
   ```

3. Run the gateway:
   ```bash
   make run
   ```

### Configuration

Gateway behavior is controlled via Envoy configuration and gateway-specific config files. See the [`config/`](config/) directory for examples.

## Architecture

```
Client → Envoy Proxy → AI Gateway Filter → LLM Backend
                              │
                    (transform, auth, rate-limit)
```

The gateway runs as an Envoy HTTP filter, intercepting requests and applying provider-specific transformations before forwarding upstream.

## Notes

> **Personal note:** I primarily use this with Ollama + qwen2 locally. If you're just getting started, `phi3` is the fastest to pull and good enough for most testing. The `make run` target assumes Ollama is already running on port 11434.

> **Tip:** If Ollama is slow on first request, it's loading the model into memory. Subsequent requests are much faster. You can pre-warm it with `curl http://localhost:11434/api/generate -d '{"model":"qwen2","prompt":"hi"}'` before starting the gateway.

## Contributing

Please open an issue before submitting large PRs. See [`.github/ISSUE_TEMPLATE`](.github/ISSUE_TEMPLATE/) for issue guidelines.

## License

Apache 2.0 — see [LICENSE](LICENSE).
