---
title: "Codex + cc-switch + GPT-5.5 国内使用教程：从注册 API 到接入 VS Code / Cursor"
description: "整理 Codex、cc-switch、OpenAI-compatible API 中转站、VS Code 和 Cursor 的接入流程，说明 API Key、Base URL、Provider 导入和常见问题。"
tags:
  - Codex
  - cc-switch
  - API中转站
  - AI中转站
  - GPT中转站
  - OpenAI-compatible
---

> 本文整理 Codex、cc-switch 与 OpenAI-compatible API 中转站的接入流程，适合想把 GPT / Claude 类模型统一接入 Codex CLI、VS Code、Cursor 的用户。

最近很多朋友都在问一个问题：

> Codex 能不能配合 cc-switch 使用 GPT-5.5？
> 能不能像 Claude Code 一样，直接切换不同的模型服务？

答案是：**可以。**

不过这里面有几个概念容易混在一起。

Codex 是 OpenAI 的 AI 编程助手，可以在终端、VS Code、Cursor 等环境里使用。官方 Codex CLI 支持通过 npm 或 Homebrew 安装，也支持在 VS Code、Cursor、Windsurf 等 IDE 中使用 Codex 插件。([GitHub](https://github.com/openai/codex))

cc-switch 则更像是一个**本地 AI 编程工具的模型配置管理器**。

你可以把它理解成：

> 一个统一管理 Claude Code、Codex、Gemini、OpenCode、OpenClaw 等工具 Provider 配置的桌面工具。

通过 cc-switch，我们可以把一个 API 中转站的配置导入进去，然后一键切换不同工具使用的模型服务。

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_03_96c85ce7.png)

这篇文章就按真实使用流程讲一遍：

**安装 cc-switch → 注册 API 中转站 → 创建 API Key → 导入 cc-switch → 打开 Codex CLI / VS Code / Cursor 使用 GPT-5.5。**

---

### 一、这套方案适合谁？

这套方案比较适合下面几类人：

第一类，已经在用 Codex、Claude Code、Cursor、VS Code 的开发者。

第二类，想把 GPT-5.5 接入本地编程工具的人。

第三类，手里有自己的 API 中转站，或者使用第三方 API 中转服务的人。

第四类，想统一管理多个模型 Provider 的人。

如果你只是单纯想体验官方 Codex，可以直接用：

```text
codex 
--model
 gpt-5.5
```

但如果你想把不同 API 服务统一管理起来，或者想在不同 AI 编程工具之间切换，那 cc-switch 会更方便。

---

### 二、整体流程先看一遍

这篇教程的完整流程如下：

```text
1. 安装 cc-switch
2. 打开 API 中转站
3. 注册账号
4. 创建 API Key
5. 点击“一键导入 cc-switch”
6. 在 cc-switch 里启用 Provider
7. 安装 Codex CLI
8. 使用 codex 命令启动
9. 或者打开 VS Code / Cursor
10. 安装 Codex 插件
11. 在 IDE 里使用 Codex + GPT-5.5
```

你可以把这套流程理解成：

**API 中转站负责提供模型接口。

 cc-switch 负责把接口配置写到本地工具里。

 Codex / VS Code / Cursor 负责真实使用。**

---

### 三、第一步：安装 cc-switch

先安装 cc-switch。

cc-switch 是一个跨平台桌面工具，支持 macOS、Windows、Linux。它的基本使用方式是添加 Provider、启用 Provider，然后重启对应 CLI 工具生效。([GitHub](https://github.com/farion1231/cc-switch))

```text
通过网盘分享的文件：CC-Switch
链接: https://pan.baidu.com/s/1ty59_f8QyiAp6e2iXy11jw?pwd
=
jhan 提取码: jhan 
--来自百度网盘超级会员v7的分享
```

```text
https://github.com/farion1231/cc-switch/releases
```

#### macOS 安装

如果你用 Mac，可以直接用 Homebrew 安装：

```text
brew 
install
 
--cask
 cc-switch
```

也可以去 GitHub Releases 下载 DMG 安装包。

#### Windows 安装

Windows 用户可以下载 MSI 安装包，或者下载便携版 ZIP。

安装完成后，直接打开 cc-switch。

#### Linux 安装

如果是 Debian / Ubuntu，可以下载 `.deb` 包后执行：

```text
sudo
 dpkg 
-i
 cc-switch_*.deb
```

如果是 Fedora / RHEL，可以下载 `.rpm` 包后执行：

```text
sudo
 
rpm
 
-i
 cc-switch_*.rpm
```

有些发行版也可以使用 AppImage。

安装完成后，打开 cc-switch，正常情况下你会看到一个 Provider 管理界面。

---

### 四、第二步：打开 API 中转站

接下来打开你的 API 中转站。

这里我不限定具体平台，因为不同人用的中转站不一样。

一般中转站的首页会有几个入口：

```text
注册 / 登录
控制台
令牌 / API Key
模型列表
使用文档
余额 / 充值
```

你需要先注册一个账号。

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_04_b5ceeb55.png)

注册完成后，进入控制台。

通常路径类似：

```text
控制台 → API Key / 令牌管理 → 创建令牌
```

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_05_f6401afa.png)

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_06_7909e7d6.png)

这里建议你创建一个专门给 Codex / cc-switch 用的 API Key。

不要把所有工具都共用一个 Key。

这样后面如果泄露、限流、停用，都比较好处理。

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_07_8437e081.png)

---

### 五、第三步：创建 API Key

进入中转站后台后，找到类似下面的入口：

```text
令牌管理
API Key
Tokens
Keys
```

然后点击：

```text
创建令牌
```

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_05_f6401afa.png)

创建时一般会让你填写：

```text
名称：codex-gpt55
分组：ClaudeCode / Codex / GPT / 默认分组
额度：按需设置
过期时间：可选
模型权限：选择 GPT-5.5 或兼容模型
```

名称可以写得清楚一点，比如：

```text
codex-gpt55
ccswitch-codex
vscode-codex
cursor-codex
```

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_08_1a412027.png)
![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_09_5f6d0dae.png)

创建完成后，你会拿到一个 API Key，通常长这样：

```text
sk-xxxxxxxxxxxxxxxxxxxx
```

把它复制下来。

注意：**API Key 只显示一次的话，一定要保存好。**

---

### 六、第四步：点击“一键导入 cc-switch”

很多 API 中转站现在会提供一个按钮：

```text
导入 cc-switch
```

或者：

```text
一键导入 cc-switch
```

点击之后，浏览器通常会弹出一个协议链接，类似：

```text
cc-switch://...
```

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_10_87cd7377.png)

如果你的电脑已经安装了 cc-switch，系统会询问是否打开 cc-switch。

点击允许。

然后 cc-switch 会自动打开导入页面。

你需要检查几个核心字段：

```text
Provider 名称
Base URL
API Key
模型名称
目标应用：Codex / Claude Code / OpenCode / Universal Provider
```

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_11_34784ede.png)

cc-switch 官方文档也提到，它支持通过添加 Provider、选择预设或者自定义配置的方式管理不同应用的模型配置。([GitHub](https://github.com/farion1231/cc-switch/blob/main/docs/user-manual/en/2-providers/2.1-add.md))

如果中转站已经做得比较完善，通常这些字段会自动填好。

你只需要确认无误，然后点击：

```text
添加
保存
启用
```

---

### 七、如果没有“一键导入”怎么办？

如果你的中转站没有一键导入 cc-switch，也可以手动添加。

打开 cc-switch，进入 Provider 管理页面。

点击右上角：

```text
+
添加 Provider
```

然后选择：

```text
Custom Gateway
自定义网关
OpenAI Compatible
```

不同版本名字可能略有不同。

然后填写：

```text
Provider Name: GPT-5.5 中转
Base URL: https://你的中转站域名/v1
API Key: sk-xxxxxxxx
Model: gpt-5.5
```

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_12_a1ca6478.png)
![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_13_f35bab3b.png)

如果要给 Codex 用，就选择 Codex 应用。

如果你希望多个工具共用，也可以选择 Universal Provider。

cc-switch 的 Provider 添加方式里有两类：一种是 App-specific Provider，只给当前应用使用；另一种是 Universal Provider，可以在多个应用之间共享。([GitHub](https://github.com/farion1231/cc-switch/blob/main/docs/user-manual/en/2-providers/2.1-add.md))

这里新手建议：

**先给 Codex 单独建一个 Provider。**

跑通之后，再考虑做通用配置。

---

### 八、第五步：在 cc-switch 里启用 Provider

Provider 添加完成后，还需要启用。

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_14_43821626.png)

在 cc-switch 主界面里，找到你刚才添加的 Provider，比如：

```text
GPT-5.5 中转
```

点击：

```text
Enable
启用
```

启用后，cc-switch 会把相关配置写入对应工具的配置文件。

cc-switch 的基本逻辑是：添加 Provider，选择启用，然后重启对应终端或 CLI 工具生效。([GitHub](https://github.com/farion1231/cc-switch))

所以这一步做完之后，建议你：

```text
关闭当前终端
重新打开终端
再启动 Codex
```

这样可以避免环境变量没生效。

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_15_6437a0cd.png)

---

### 九、第六步：安装 Codex CLI

接下来安装 Codex CLI。

OpenAI 官方 Codex 仓库提供了两种常见安装方式。([GitHub](https://github.com/openai/codex))

#### npm 安装

```text
npm
 
install
 
-g
 @openai/codex
```

或者：

```text
npm
 i 
-g
 @openai/codex
```

#### Homebrew 安装

Mac 用户也可以用：

```text
brew 
install
 
--cask
 codex
```

安装完成后检查版本：

```text
codex 
--version
```

如果能看到版本号，说明安装成功。

---

> 教程： https://kazjsfecs3y.feishu.cn/wiki/JNXAwxqeOiI3Hxky3BXcYdkrnYb

### 十、第七步：打开 Codex CLI 使用 GPT-5.5

进入你的项目目录：

```text
cd
 ~/projects/your-project
```

启动 Codex：

```text
codex
```

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_16_31d33f08.png)

如果你要明确指定 GPT-5.5，可以这样：

```text
codex 
--model
 gpt-5.5
```

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_17_88a1a91b.png)

如果你的中转站模型名不是 `gpt-5.5`，而是类似：

```text
gpt-5.5-codex
openai/gpt-5.5
gpt-5.5-2026
```

那就按照中转站提供的模型名填写。

比如：

```text
codex 
--model
 gpt-5.5-codex
```

进入 Codex 后，建议第一句话不要直接让它改代码。

可以先让它读项目：

```text
请先不要修改代码。
帮我阅读当前项目，说明：
1. 项目使用了什么技术栈
2. 主要目录分别是做什么的
3. 本地如何启动
4. 核心模块在哪里
5. 如果我要二次开发，建议先看哪些文件
```

这样比较稳。

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_18_adc07748.png)

---

### 十一、Codex 常用命令

下面这些命令可以先记住。

查看版本：

```text
codex 
--version
```

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_19_71ef6ebf.png)

启动 Codex：

```text
codex
```

指定模型启动：

```text
codex 
--model
 gpt-5.5
```

进入某个项目：

```text
cd
 your-project
codex
```

查看 Git 状态：

```text
git
 status
```

查看 AI 修改了什么：

```text
git
 
diff
```

新建测试分支：

```text
git
 checkout 
-b
 codex-test
```

运行项目：

```text
npm
 
install


npm
 run dev
```

构建项目：

```text
npm
 run build
```

如果是 Go 项目：

```text
go 
test
 ./
..
.
```

如果是 Python 项目：

```text
pytest
```

如果是 Java / Maven 项目：

```text
mvn 
test
```

如果是 Java / Gradle 项目：

```text
./gradlew 
test
```

---

### 十二、第八步：在 VS Code 里使用 Codex 插件

除了终端，你也可以在 VS Code 里使用 Codex。

![在这里插入图片描述]({{ site.baseurl }}/assets/images/codex-cc-switch-gpt55/image_20_c7f7f006.png)

OpenAI 官方 Codex IDE 文档说明，Codex 插件支持 VS Code，也支持 Cursor、Windsurf 等 VS Code forks。([OpenAI 开发者](https://developers.openai.com/codex/ide))

打开 VS Code。

点击左侧扩展市场。

搜索：

```text
Codex
```

找到：

```text
Codex - OpenAI's coding agent
```

发布方是 OpenAI。

点击安装。

Visual Studio Marketplace 上的 Codex 插件介绍是：Codex 是 OpenAI 的 coding agent，可以帮助你在 IDE 中写代码、review 代码和交付代码。([Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=openai.chatgpt))

安装完成后，左侧会出现 Codex 图标。

点击 Codex 图标，登录或者读取本地配置。

如果你已经通过 cc-switch 启用了 Codex Provider，通常可以直接使用对应配置。

如果没有生效，可以尝试：

```text
1. 关闭 VS Code
2. 确认 cc-switch 已启用 Provider
3. 重新打开 VS Code
4. 再打开 Codex 插件
```

---

### 十三、在 VS Code 里的使用方式

打开一个项目文件夹：

```text
File → Open Folder
```

或者命令行打开：

```text
code 
.
```

然后在 Codex 面板里输入：

```text
请阅读当前项目，帮我总结项目结构和启动方式。
```

如果你要让它修 bug，可以这样：

```text
这是项目运行时报错，请你先分析原因，不要直接修改代码。

【粘贴报错】

请输出：
1. 报错原因
2. 相关文件
3. 修改方案
4. 是否需要我确认后再修改
```

如果要让它开发功能，可以这样：

```text
请帮我增加一个商品筛选功能。

要求：
1. 支持按商品名称搜索
2. 支持按分类筛选
3. 支持按状态筛选
4. 不影响原有分页
5. 修改前先告诉我准备改哪些文件
```

我建议在 IDE 里也保持一个原则：

**先分析，再修改。

 先小改，再看 diff。**

---

### 十四、第九步：在 Cursor 里使用 Codex 插件

Cursor 本身就是 VS Code 的分支，所以也可以安装 Codex 插件。

OpenAI 官方 Codex IDE 文档明确提到，Codex IDE extension 支持 Cursor。([OpenAI 开发者](https://developers.openai.com/codex/ide))

打开 Cursor。

进入扩展市场。

搜索：

```text
Codex
```

找到 OpenAI 官方的 Codex 插件。

点击安装。

安装完成后，重启 Cursor。

然后打开你的项目：

```text
cursor 
.
```

或者直接在 Cursor 里选择：

```text
Open Folder
```

打开项目后，找到左侧 Codex 图标。

如果图标没显示，可以在扩展列表里确认 Codex 是否启用。

在 Cursor 里使用 Codex 的好处是：

Cursor 自己有 AI 能力，Codex 又是另一个项目级 coding agent。

你可以根据任务选择：

```text
小改动：Cursor 内置 AI
复杂项目任务：Codex
需要终端执行：Codex CLI
```

---

### 十五、Codex CLI、VS Code、Cursor 怎么选？

如果你是命令行用户，推荐：

```text
Codex CLI
```

适合：

```text
后端项目
脚本项目
服务器项目
需要跑命令的项目
喜欢终端的人
```

如果你是前端或全栈开发，推荐：

```text
VS Code + Codex 插件
```

适合：

```text
边看代码边问 AI
边改 UI 边调试
需要可视化文件树
习惯插件生态
```

如果你已经在用 Cursor，推荐：

```text
Cursor + Codex 插件
```

适合：

```text
已有 Cursor 工作流
想同时使用 Cursor 内置 AI 和 Codex
想让不同 AI 工具各司其职
```

我的建议是：

**新手先用 VS Code / Cursor 插件。

 熟悉后再用 Codex CLI。

 重度开发者两个都装。**

因为有些任务在终端里更顺手，有些任务在 IDE 里更直观。

---

### 十六、cc-switch 在这里到底起什么作用？

很多人会问：

> 我都装了 Codex，为什么还要 cc-switch？

可以这样理解。

Codex 是干活的人。

API 中转站是模型服务。

cc-switch 是配置开关。

它帮你管理这些东西：

```text
API Key
Base URL
模型名
Provider
不同工具的配置
一键切换
```

如果你只用官方 ChatGPT 登录，确实不一定需要 cc-switch。

但如果你有多个中转 API，或者你想让 Claude Code、Codex、OpenCode、Gemini 等工具共用一套 Provider 管理方式，那 cc-switch 就很方便。

尤其是你有多个模型源的时候：

```text
官方 OpenAI
自己的中转 API
Claude 中转
Gemini 中转
DeepSeek 中转
Qwen 中转
```

手动改配置会很麻烦。

cc-switch 的价值就是：

**不用每次手动改配置文件，点一下就能切。**

---

### 十七、推荐配置方式

如果你的中转站支持 GPT-5.5，我建议这样配置：

```text
Provider 名称：GPT-5.5 Codex
Base URL：https://你的中转站域名/v1
API Key：sk-xxxxxxxx
Model：gpt-5.5
应用：Codex
```

如果要给 Claude Code 兼容使用，可以再建一个 Claude Code Provider。

如果要多个工具共用，可以建 Universal Provider。

不过新手不要一上来搞太复杂。

我建议先跑通这条链路：

```text
中转站 API Key → cc-switch → Codex CLI
```

确认没问题后，再接：

```text
VS Code
Cursor
Claude Code
OpenCode
```

---

### 十八、使用 GPT-5.5 的建议

GPT-5.5 适合复杂 coding 任务。

比如：

```text
复杂项目阅读
多文件重构
疑难 bug 分析
代码迁移
单元测试补充
接口改造
老项目梳理
技术方案生成
```

但不是所有任务都要用 GPT-5.5。

简单总结代码、改 README、生成注释、写小脚本，用小模型也可以。

我的习惯是：

```text
读项目：小模型或 GPT-5.4
改核心代码：GPT-5.5
复杂 bug：GPT-5.5
写测试：GPT-5.5 或 GPT-5.4
总结文档：小模型
```

这样成本和效果会比较平衡。

---

### 十九、真实使用时的标准流程

我建议你每次使用 Codex 都按这个流程来。

第一步，进入项目。

```text
cd
 your-project
```

第二步，新建分支。

```text
git
 checkout 
-b
 codex-test
```

第三步，启动 Codex。

```text
codex 
--model
 gpt-5.5
```

第四步，让它先读项目。

```text
请先不要修改代码。
帮我阅读项目，输出技术栈、目录结构、启动方式和核心模块。
```

第五步，让它分析需求。

```text
我想增加一个 XXX 功能。
请先分析应该改哪些文件，不要直接修改。
```

第六步，确认方案。

你自己看一下它准备改哪些文件。

第七步，让它小范围修改。

```text
按照刚才方案修改。
要求：
1. 尽量少改动
2. 保持原有代码风格
3. 修改后说明改了哪些文件
4. 执行命令前先询问我
```

第八步，查看 diff。

```text
git
 
diff
```

第九步，运行测试。

```text
npm
 
test
```

或者：

```text
npm
 run build
```

第十步，确认没问题再提交。

```text
git
 
add
 
.


git
 commit 
-m
 
"feat: add xxx by codex"
```

这个流程虽然多几步，但很稳。

不要让 AI 一把梭。

---

### 二十、常见问题

#### 1. 点击导入 cc-switch 没反应怎么办？

先确认 cc-switch 已经安装并启动。

如果浏览器提示是否打开 cc-switch，要点击允许。

如果还是不行，可以手动复制配置，在 cc-switch 里添加 Custom Gateway。

---

#### 2. cc-switch 启用后 Codex 没生效怎么办？

可以尝试：

```text
1. 确认 Provider 已启用
2. 关闭当前终端
3. 重新打开终端
4. 重新执行 codex
5. 检查模型名是否写对
6. 检查 Base URL 是否带 /v1
7. 检查 API Key 是否有效
```

很多时候是终端环境变量没刷新。

---

#### 3. Base URL 怎么写？

大多数 OpenAI Compatible 接口是：

```text
https://你的域名/v1
```

比如：

```text
https://api.example.com/v1
```

不要写成：

```text
https://api.example.com
```

也不要多写：

```text
https://api.example.com/v1/chat/completions
```

一般只填到 `/v1`。

---

#### 4. 模型名写什么？

以中转站模型列表为准。

如果模型列表里叫：

```text
gpt-5.5
```

那就填：

```text
gpt-5.5
```

如果叫：

```text
openai/gpt-5.5
```

那就填：

```text
openai/gpt-5.5
```

不要凭感觉乱写。

---

#### 5. VS Code / Cursor 插件不走 cc-switch 怎么办？

先确认你在 cc-switch 里启用的是 Codex 相关 Provider。

然后完全退出 VS Code / Cursor，再重新打开。

如果仍然不生效，可以先用 CLI 测试：

```text
codex 
--model
 gpt-5.5
```

CLI 跑通后，再排查插件。

---

#### 6. Codex 可以直接改生产项目吗？

不建议。

最起码要做到：

```text
1. 使用 Git
2. 新建分支
3. 小步修改
4. 看 diff
5. 跑测试
6. 人工 review
```

AI 可以帮你提效，但不要让它无保护地改生产代码。

---

### 二十一、我推荐的最终组合

如果你是个人开发者，我建议这样装：

```text
cc-switch
Codex CLI
VS Code Codex 插件
Cursor Codex 插件
```

如果你经常写后端：

```text
Codex CLI 为主
VS Code / Cursor 为辅
```

如果你经常写前端：

```text
Cursor / VS Code 为主
Codex CLI 为辅
```

如果你经常折腾多个模型服务：

```text
cc-switch 必装
```

最终结构大概是：

```text
API 中转站
    ↓
API Key
    ↓
cc-switch
    ↓
Codex CLI / VS Code / Cursor
    ↓
GPT-5.5 辅助开发
```

---

### 二十二、最后总结

这套方案看起来步骤有点多，但理解之后其实很简单。

API 中转站负责提供模型能力。

cc-switch 负责管理和切换模型配置。

Codex 负责进入项目干活。

VS Code / Cursor 负责提供更顺手的开发界面。

完整链路就是：

```text
注册中转站
创建 API Key
导入 cc-switch
启用 Provider
安装 Codex
打开项目
开始使用 GPT-5.5 写代码
```

我比较建议大家不要只把 AI 当成聊天工具。

真正能提升效率的方式，是让它进入你的真实开发流程：

读项目、分析需求、改代码、跑测试、看 diff、写文档。

但同时也要记住一点：

**AI 可以执行，最终拍板的人还是你。**

尤其是涉及生产环境、数据库、支付、权限、部署这些敏感操作，一定要人工确认。

如果你是第一次尝试，可以先找一个测试项目。

先让 Codex 读项目。

再让它修一个小 bug。

最后让它做一个小功能。

跑通这一遍，你基本就能理解：

为什么 AI 编程工具正在从“问答助手”，变成“开发智能体”。

---

## 引用来源

项目与发布来源：[alicekellings/aiapiproxy](https://github.com/alicekellings/aiapiproxy)

原文参考：[GitCode/CSDN 文章](https://gitcode.csdn.net/6a0c78d710ee7a33f273c221.html)
