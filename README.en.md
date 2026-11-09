# KI-Box Harbor: local AI on a 16 GB PC without a GPU

[Deutsch](README.md) · English

**KI-Box Harbor is a stripped-down fork of [Harbor](https://github.com/av/harbor) that runs a private AI chat with German document search on a low-end Linux PC with 16 GB RAM, no GPU and an older CPU.** It keeps only two services: [Ollama](https://github.com/ollama/ollama) for the language model and [Open WebUI](https://github.com/open-webui/open-webui) for chat. All data stays on the machine. A project of the non-profit [KI Klub Werder e.V.](https://ki-klub-werder.de) in Werder (Havel), Germany, and part of [KI-Box](https://github.com/KI-Klub-Werder-e-V/ki-box).

> **Status, 9 November 2026:** The merged Docker Compose configuration has been checked. The stack has not yet run on real 16 GB hardware, and the installer has not been tested end to end. Reports are very welcome.

## At a glance

| | |
|---|---|
| **Hardware** | Linux PC, 16 GB RAM, no GPU, CPU with AVX2 (about 6 years old is fine) |
| **Services** | Ollama 0.40.15, Open WebUI v0.13.3 (slim image) |
| **Models** | Chat: `gemma4:e4b-it-qat` (6.1 GB), embeddings: `bge-m3` (1.2 GB) |
| **Language** | German interface, German-capable embeddings for document search |
| **Audience** | AI enthusiasts running a box at home, in a club or a small business |
| **Base** | Harbor 0.5.19, reduced from 2,541 to 68 files |
| **Licence** | Apache-2.0 (same as Harbor); Open WebUI has its own licence |

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/KI-Klub-Werder-e-V/ki-box-harbor/main/install.sh | bash
harbor up
```

The first `harbor up` downloads about 7 GB of models. Chat is then available on your local network at `http://<machine-ip>:33801`.

**Important:** The first account to register becomes admin. Create it right after starting. Later accounts start as pending until an admin approves them.

## Changes from Harbor

- **Removed:** every service except Ollama and Open WebUI, the desktop app, Boost, docs, tests, the Windows installer, GPU variants
- **Default services** are `webui` and `ollama` instead of `webui` and `llamacpp`
- **Ollama is local only:** port 33821 binds to `127.0.0.1`, because the Ollama API has no authentication
- **No fixed secret:** Harbor sets `h@rb0r` as the Open WebUI secret. Here Open WebUI generates a random one on first start and persists it
- **Pinned versions** instead of `latest` and `main`
- **Low memory:** 8,192-token context, one request at a time, at most two loaded models
- **German:** `de-DE` interface and `bge-m3` embeddings instead of the English default model
- **Privacy:** admins cannot read other users' chats; log level `INFO` instead of `DEBUG`

Details are in [NOTICE](NOTICE).

## FAQ

### Can a local LLM run on 16 GB RAM without a GPU?

Yes, slowly. Third-party reports put an Intel N100 at about 6–9 tokens per second on an 8B model. Expect one request at a time while others wait.

### Why a fork instead of plain Harbor?

Harbor ships over 150 services and targets developer desktops. Some defaults are unsuitable for a box on a network: a fixed secret, an unauthenticated Ollama API exposed on the network, and Open WebUI from its development branch. The fork keeps Harbor's CLI and update path and changes only these points.

### How do I back up my data?

Accounts, chats and documents live in `~/.harbor/services/webui`; models live in `~/.ollama`. Run `harbor down` and back up the first folder, for example with [restic](https://restic.net).

## Credits and licence

Based on [Harbor](https://github.com/av/harbor) by av and contributors, Apache-2.0. This fork is also [Apache-2.0](LICENSE).

*Facts last checked: 9 November 2026.*
