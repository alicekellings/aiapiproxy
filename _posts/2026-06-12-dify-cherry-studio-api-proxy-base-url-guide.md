---
title: Dify / Cherry Studio 如何接入 AI API 中转站：base_url、API key 和模型名怎么填
description: 面向 Dify、Cherry Studio、Chatbox、OpenAI-compatible 客户端用户，说明 API 中转站的 base_url、API key、model、额度控制和请求日志应该怎么配置与排查。
tags:
  - API中转站
  - AI中转站
  - Dify中转站
  - CherryStudio
  - OpenAI-compatible
---

很多人第一次把 Dify、Cherry Studio、Chatbox 这类工具接到 API 中转站时，最容易卡在三个地方：

```text
base_url 填哪里
API key 用哪一个
model 名称怎么写
```

看起来只是三行配置，但只要其中一个口径不对，就会出现连接失败、模型不存在、扣费查不到、工具里能跑但后台看不懂日志等问题。

这篇用比较直接的方式，把 Dify / Cherry Studio 接入 AI API 中转站时需要注意的配置讲清楚。重点不是换某一个工具，而是理解 OpenAI-compatible API 的通用口径。

## 1. 先理解 OpenAI-compatible 是什么

很多 API 中转站都会写自己兼容 OpenAI API。它通常表示客户端可以按 OpenAI SDK 或 OpenAI 格式发请求。

最常见的请求地址长这样：

```text
https://example.com/v1/chat/completions
```

所以在客户端里一般要填的是 `base_url`，也就是 `/v1` 前后的基础地址。

以 Wappkit API 为例：

```text
base_url: https://api.wappkit.com/v1
```

注意，很多工具让你填的是 `base_url`，不是完整的 `chat/completions` 地址。如果你把完整路径填进去，工具后面再自动拼一次 `/chat/completions`，请求就会变成错误路径。

## 2. Dify 里应该怎么填

Dify 支持添加自定义模型供应商或 OpenAI-compatible 类型的模型。不同版本界面文案可能略有差异，但核心配置通常是这几项：

```text
Provider: OpenAI-compatible / OpenAI API-compatible
API endpoint / Base URL: https://api.wappkit.com/v1
API key: 从中转站后台创建的独立 key
Model name: 模型市场里显示的模型名
```

这里有两个细节。

第一，API key 不建议直接用账户主 key，也不建议多个项目共用一个 key。更稳的做法是给每个 Dify 应用单独建一个 key，并设置硬额度。这样某个工作流循环调用或测试失败时，不会把整个账户余额消耗掉。

第二，模型名要按中转站模型市场里展示的名称填写。不要凭印象写 `gpt-4`、`gpt-5` 或随便改大小写。很多中转站会有自己的模型别名、分组或路由规则，模型名填错时，客户端通常只会报一个比较笼统的错误。

## 3. Cherry Studio 里应该怎么填

Cherry Studio 的接入逻辑也类似。一般可以新增一个 OpenAI 类型或自定义 OpenAI-compatible 服务。

推荐配置思路：

```text
服务类型：OpenAI / OpenAI Compatible
API 地址：https://api.wappkit.com/v1
API Key：单独创建的 key
模型：手动填写或从服务端拉取
```

如果工具支持“获取模型列表”，可以先点一次获取，看看能不能读到模型。如果读不到，不一定代表接口不可用，也可能是中转站没有开放模型列表接口，或者客户端对模型列表返回格式要求更严格。

这时可以先用一个明确可用的模型名手动添加，再发一条短消息测试：

```text
请用一句话回复：连接成功
```

如果聊天能正常返回，再去后台请求日志里看这次请求的 input tokens、output tokens、模型名和扣费记录。

## 4. 为什么一定要看请求日志

只看客户端里“能不能回复”是不够的。

API 中转站真正适合开发者使用，应该能让你看到每次请求的细节：

```text
请求时间
使用的 API key
模型名
input tokens
output tokens
cache tokens
扣费金额
失败状态码
错误信息
```

Dify 和 Cherry Studio 都可能因为插件、工作流、多轮上下文、系统提示词导致 token 消耗比预期高。尤其是 Dify 工作流里有多个节点时，用户以为只调用了一次模型，实际可能是多个节点分别调用。

所以第一次接入时，建议用最小请求测试，再逐步打开完整工作流。每打开一个节点或功能，就去中转站后台看一次日志。这样能更快定位到底是哪一步在消耗 token。

## 5. 不要多个工具共用一个 API key

很多新手为了省事，会把同一个 API key 同时填进：

```text
Dify
Cherry Studio
Cursor
Cline
自己的脚本
```

这样短期确实方便，但后面排查会很痛苦。

更推荐的方式是：

```text
dify-prod-key
dify-test-key
cherry-studio-key
cursor-personal-key
cline-task-key
```

每个 key 单独设置额度。比如给测试工作流一个很小的额度，给正式应用一个更高但可控的额度。这样即使某个工具出现循环调用、自动重试或上下文暴涨，也只会消耗对应 key 的额度。

这就是“硬额度”比“提醒自己少用点”更可靠的地方。

## 6. 常见错误怎么排查

如果接入失败，可以按这个顺序查。

### base_url 是否多写路径

应该填：

```text
https://api.wappkit.com/v1
```

不要填成：

```text
https://api.wappkit.com/v1/chat/completions
```

除非工具明确要求你填写完整接口地址。

### API key 是否属于当前中转站

有些人会把官方 OpenAI key、OpenRouter key、中转站 key 混在一起。base_url 和 API key 必须来自同一套服务。否则很容易返回鉴权失败。

### model 名称是否真实存在

模型名建议从模型市场复制，不要手写猜测。尤其是带版本号、分组名、供应商前缀的模型。

### 额度是否足够

如果后台 key 额度不足，客户端可能只显示调用失败。先去中转站后台看 key 状态和余额。

### 是否被网络环境拦截

如果客户端所在环境无法访问中转站域名，也会失败。可以先用浏览器打开官网，或用命令行发一个最小请求测试。

## 7. 用 curl 做一个最小自测

在把问题归因给 Dify 或 Cherry Studio 之前，最好先用 curl 测一次。

示例：

```bash
curl https://api.wappkit.com/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "你的模型名",
    "messages": [
      { "role": "user", "content": "用一句话回复：连接成功" }
    ]
  }'
```

如果 curl 能通，而客户端不通，问题大概率在客户端配置。如果 curl 也不通，就优先查 key、模型名、额度和网络。

## 8. Wappkit API 的接入口径

Wappkit API 的定位是面向开发者的 OpenAI-compatible API Gateway，适合需要统一管理 API key、硬额度、请求日志和模型价格的用户。

常用入口：

```text
官网：https://api.wappkit.com
base_url：https://api.wappkit.com/v1
```

适合接入的工具包括：

```text
Dify
Cherry Studio
Chatbox
Cursor
Cline
Continue
自写脚本
OpenAI SDK 兼容客户端
```

如果你正在比较 `API中转站`、`AI中转站`、`Dify中转站`、`Cherry Studio API` 或 `OpenAI-compatible base_url`，建议不要只看价格。长期使用时，更关键的是能不能给不同项目拆 key、能不能设置硬额度、能不能查清楚每次请求为什么扣费。

能跑通只是第一步。能控制额度、看懂日志、排查失败，才是 API 中转站真正有用的地方。
