[English](./reasonix.md) | [简体中文](./reasonix.zh-CN.md) · [← Back](../README.md)

# Integrate with Reasonix

Reasonix is a DeepSeek-native AI coding agent for your terminal.

A config- and plugin-driven harness — a single static Go binary, tuned around DeepSeek's prefix cache so token costs stay low across long sessions.

Reasonix 1.0 is a ground-up rewrite in Go. The earlier `0.x` TypeScript releases are legacy and maintained on the `v1` branch only. See the [migration guide](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/MIGRATING.md). `npm i -g reasonix` remains the install command — `1.0.0` and later deliver the Go binary, while `0.x` is the legacy TypeScript build.

DeepSeek V4 models support a 1M-token context window. For Reasonix's long-session context maintenance and cache behavior, see the Guide and Spec.

#### 1. Install Reasonix

```sh
npm i -g reasonix                  # any OS; pulls the prebuilt native binary
brew install esengine/reasonix/reasonix   # macOS
```

Prebuilt archives (`darwin|linux|windows × amd64|arm64`) and `SHA256SUMS` are available on every [GitHub release](https://github.com/esengine/DeepSeek-Reasonix/releases).

#### 2. Get a DeepSeek API Key

Get your API key from the [DeepSeek Platform](https://platform.deepseek.com/api_keys). You can let `reasonix setup` save it to Reasonix's global `.env`, or set it before launch.

#### 3. Quick start

Run inside your project directory:

```sh
reasonix setup                      # config wizard → ./reasonix.toml
export DEEPSEEK_API_KEY=sk-...      # or let setup save it to Reasonix home .env
reasonix                            # then run /init to generate AGENTS.md (project memory)
reasonix run "implement the TODOs in main.go"
reasonix run --model deepseek-pro "add unit tests for this function"
echo "explain this code" | reasonix run
```

#### 4. Configuration

A compact DeepSeek config can expose both Flash and Pro, declare the 1M context window, and enable max reasoning effort for Pro:

```toml
default_model = "deepseek-flash"

[[providers]]
name           = "deepseek-flash"
kind           = "openai"
base_url       = "https://api.deepseek.com"
model          = "deepseek-v4-flash"
api_key_env    = "DEEPSEEK_API_KEY"
context_window = 1000000

[[providers]]
name           = "deepseek-pro"
kind           = "openai"
base_url       = "https://api.deepseek.com"
model          = "deepseek-v4-pro"
api_key_env    = "DEEPSEEK_API_KEY"
context_window = 1000000
effort         = "max"
```

Resolution order is **flag > `./reasonix.toml` > the user config file > built-in defaults**. Starting with **Reasonix v1.8.1**, the user file lives at `~/.reasonix/config.toml` on macOS/Linux and `%AppData%\reasonix\config.toml` on Windows. See [Configuration paths](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/CONFIG_PATHS.md) for migration details and the full `config.toml` / `.env` structure.

Provider entries name secrets with `api_key_env`; the secret values themselves live in Reasonix's global `<Reasonix home>/.env`, shared by CLI and desktop. Project `.env` files are not provider-key runtime fallbacks, but still feed workspace-scoped, non-provider `${VAR}` expansion for MCP / plugin settings without importing Reasonix control variables.

#### 5. More documentation

- [Guide](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/GUIDE.md) — configuration, permissions and sandbox, plugins (MCP), slash commands, `@` references, and two-model collaboration.
- [Configuration paths](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/CONFIG_PATHS.md) — user config, global `.env`, and migration details.
- [Spec](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/SPEC.md) — engineering contract, architecture, registries, data types, and roadmap.
- [Migrating from 0.x](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/MIGRATING.md) — migration from the legacy TypeScript line to the 1.0 Go rewrite.
