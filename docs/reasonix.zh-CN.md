[English](./reasonix.md) | [简体中文](./reasonix.zh-CN.md) · [← 返回](../README.zh-CN.md)

# 接入 Reasonix

Reasonix 是面向终端的 DeepSeek 原生 AI coding agent。

由配置与插件驱动的极薄 harness——单一静态 Go 二进制，围绕 DeepSeek 的前缀缓存调优，长会话也能把 token 成本压低。

Reasonix 1.0 是用 Go 从零重写的版本。早期的 `0.x` TypeScript 版本转为 **legacy**，保留在 [`v1`](https://github.com/esengine/DeepSeek-Reasonix/tree/v1) 分支（仅维护）。详见[迁移指南](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/MIGRATING.md)。`npm i -g reasonix` 仍是安装命令——`1.0.0`+ 装的是 Go 二进制，`0.x` 是 legacy TS 版。

DeepSeek V4 模型支持 1M 上下文。Reasonix 侧的长会话上下文维护与缓存行为可参考 Guide 与 Spec。

#### 1. 安装 Reasonix

```sh
npm i -g reasonix                  # 任意系统；自动拉取对应平台的原生二进制
brew install esengine/reasonix/reasonix   # macOS
```

预编译归档（`darwin|linux|windows × amd64|arm64`）和 `SHA256SUMS` 见每个 [GitHub release](https://github.com/esengine/DeepSeek-Reasonix/releases)。

#### 2. 获取 DeepSeek API Key

在 [DeepSeek 开放平台](https://platform.deepseek.com/api_keys) 获取 API Key。你可以让 `reasonix setup` 将密钥保存到 Reasonix 全局 `.env`，也可以在启动前设置环境变量。

#### 3. 快速开始

在项目目录中运行：

```sh
reasonix setup                      # 配置向导 → ./reasonix.toml
export DEEPSEEK_API_KEY=sk-...      # 也可以让 setup 保存到 Reasonix 全局 .env
reasonix                            # 然后在会话里运行 /init 生成 AGENTS.md（项目记忆）
reasonix run "把 main.go 里的 TODO 实现掉"
reasonix run --model deepseek-pro "给这个函数补单元测试"
echo "解释这段代码" | reasonix run
```

#### 4. 配置

一份紧凑的 DeepSeek 配置可以同时提供 Flash 与 Pro，显式声明 1M 上下文，并为 Pro 开启 max 推理强度：

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

优先级为 **flag > `./reasonix.toml` > 用户配置文件 > 内置默认值**；从 **Reasonix v1.8.1** 开始，用户配置位于 macOS/Linux 的 `~/.reasonix/config.toml`，Windows 为 `%AppData%\reasonix\config.toml`。迁移细节见[配置路径](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/CONFIG_PATHS.zh-CN.md)，其中也说明了全局 `config.toml` 和 `.env` 的完整结构。

Provider 通过 `api_key_env` 命名密钥，真实密钥值保存在 CLI 与桌面端共用的 Reasonix 全局 `<Reasonix home>/.env`；项目 `.env` 不再作为 provider key 的运行时 fallback，但仍会作为当前 workspace 范围内的 MCP / plugin 非 provider `${VAR}` 展开来源，不导入 Reasonix 控制变量。

#### 5. 更多文档

- [Guide](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/GUIDE.zh-CN.md) —— 配置、权限与沙盒、插件（MCP）、斜杠命令、`@` 引用、双模型协同。
- [配置路径](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/CONFIG_PATHS.zh-CN.md) —— 用户配置、全局 `.env` 与迁移细节。
- [规格](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/SPEC.md) —— 工程契约、架构、registry、数据类型与路线图。
- [从 0.x 迁移](https://github.com/esengine/DeepSeek-Reasonix/blob/main-v2/docs/MIGRATING.md) —— 从 legacy TypeScript 版本迁到 1.0 Go 重写版。
