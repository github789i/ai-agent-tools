# Skills/MCP Server Access

![skills\_mcpServer\.png](Images/Images_attachments_skills/skills_mcpServer.png)

# 核心概念介绍

- **Skills（技能）**

    - **定位**：给 AI 一本“操作手册”，告诉它在特定场景下的标准操作流程。

    - **原理**：通过打包的 `.md` 文件赋予 AI 专业的工作流和操作规范（例如：拿到数据后如何按规范清洗、如何执行特定的工程架构任务）。

- **MCP（模型上下文协议）**

    - **定位**：给 AI 连接外部工具和数据源的“钥匙”，打破沙盒限制。

    - **原理**：让 AI 能够直接操作外部服务和本地系统（例如：读写本地数据库、使用浏览器自动抓取、接入飞书云文档、托管 GitHub 代码等）。



# MCP（模型上下文协议）安装指南

## 桌面端 AI 软件（如 Cursor / Trae / Claude Desktop 等）

桌面端软件通常已内置 MCP 客户端。

- **图形化添加**：在 Cursor/Trae 的设置（Settings）\-\> Features \-\> MCP 中直接点击 "Add New MCP Server"。

- **配置文件添加（以 Claude 官方桌面端为例）**：

    - Windows 路径：`%APPDATA%\Claude\claude_desktop_config.json`

    - Mac 路径：`~/Library/Application Support/Claude/claude_desktop_config.json`

编辑该 `claude_desktop_config.json` 文件（以添加 Firecrawl 为例）：

```Bash
{
  "mcpServers": {
    "firecrawl": {
      "command": "npx",
      "args": ["-y", "firecrawl-mcp-server"],
      "env": {
        "FIRECRAWL_API_KEY": "你的_FIRECRAWL_API_KEY"
      }
    }
  }
}

```



## 终端 CLI（Claude Code 专属）

[官方教程](https://code.claude.com/docs/en/mcp)

1. 打开claude code CLI，输入以下指令安装插件：

```Bash
# 安装开发者插件
/plugin install mcp-server-dev@claude-plugins-official

# 运行构建命令
/mcp-server-dev:build-mcp-server

# 检查当前内部 MCP 状态
/mcp
```

2. 直接在系统终端（Terminal/Cmd）中根据在相应工具中申请的MCP server api\_key，安装本地MCP server：

```Bash
# 基本语法
claude mcp add [options] <name> -- <command> [args...]
```

**标准示例（添加 Firecrawl）**：

注意替换你的 api\_key：

```Bash
claude mcp add --env FIRECRAWL_API_KEY=YOUR_ACTUAL_API_KEY \
firecrawl -- npx -y firecrawl-mcp-server
```

3. 常用管理命令

```Bash
claude mcp list    # 查看所有已配置的服务列表
claude mcp get github  # 获取特定服务的详细信息
claude mcp remove github # 移除某个服务
```



# Skills（智能技能包）安装与使用

## 全局安装（多端共用）

> 让 Claude Code 和支持该标准的客户端全局共用
> 
> 

在 [`https://skills.sh`](https://skills.sh) 网站有许多公开的技能包（例如官方的 `vercel-labs/agent-skills`）

可以直接在**系统终端**中通过 `npx` 工具将其全局安装到你本地的 AI Agent 中

```Bash
# 全局安装指定的 skills 包，并通过 -a 参数应用到 claude-code
npx skills@latest add vercel-labs/agent-skills -g -a claude-code

# 热门慢思考拷问技能 grill-me 安装：
npx skills@latest add mattpocock/grill-me -g -a claude-code
```

提示：

- `-g`（或 `--global`）参数会把技能下载到你本地的全局代理目录中（通常是 `~/.agents/skills/`），使其在所有项目中全局生效。



## 手动安装（针对 Claude Code 离线全局生效）

如果通过 `npx` 脚本同步失败，可以直接将包含 `SKILL.md` 的文件夹手动放入 Claude Code 的官方全局技能目录：

- **Windows 路径**：`C:\Users\你的用户名\.claude\skills\`

- **Mac/Linux 路径**：`~/.claude/skills/`



## 如何使用技能

- **自动触发**：Claude 会根据你在对话中提出的诉求，自动去匹配技能描述并调用。例如你说：

    ```Bash
    *“我想实现一个用户权限管理功能，请用 grill-me 帮我理清思路。”*  
    ```

- **手动触发**：在 Claude Code 交互界面中直接输入斜杠指令来强制触发它（指令名称通常为文件夹名）

    ```Bash
    /grill-me
    ```



# 推荐

## 热门 MCP 服务推荐

### 🌐 Firecrawl MCP

- **功能**：允许 AI 绕过反爬虫，自动抓取并理解网页的深层内容。

- **API 申请**：[Firecrawl 官网申请链接](https://www.firecrawl.dev/)

- **配置示例 \(stdio 模式\)**：

```JSON
"firecrawl": {
  "command": "npx",
  "args": ["-y", "firecrawl-mcp-server"],
  "env": { "FIRECRAWL_API_KEY": "你的API密钥" }
}
```



### 📚 Context7 MCP

- **功能**：为 AI 提供最新、最全的技术框架官方文档查询，解决大模型训练数据滞后的问题。

- **API 申请**：[Context7 官网申请链接](https://context7.com/)

- **配置示例 \(SSE 桥接模式\)**：

```JSON
"context7": {
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-http-sse", "https://mcp.context7.com/mcp"],
  "env": { "CONTEXT7_API_KEY": "你的API密钥" }
}
```



### 🐙 GitHub 官方 MCP

- **功能：**让 AI 具备直接审查、修改、提交你的 GitHub 仓库代码以及开 Issue/PR 的核心能力

- **凭证申请**：在GitHub [access\_token apply link](https://github.com/settings/personal-access-tokens) 生成你的 `Classic Token` （需勾选 `repo` 权限）

- MCP配置：

    ```JSON
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "你的GitHub_Token" }
    }
    ```

### Feishu

> 参考文档 [Feishu CLI Configure Instruction](https://vcn9l43cozt5.feishu.cn/wiki/Vz3KwFuP5iOtglkZ3AJcZRmjnug)
> 
> 





## 热门 Skills 推荐

在官方的[公开skill库](https://www.skills.sh/)中有许多好用的skill，这里推荐两个：

- **grill\-me**：不懈的访谈技巧。当你给出一个开发计划或架构设计时，它会通过连环系统性提问对你的方案进行压力测试，查漏补缺。

- **find\-skills**：技能发现器。当你在对话中提到某个复杂或生疏的领域时，它可以自动从开放生态中搜索并推荐最适合该任务的专业代理技能。

