# 安装并使用claude-code
本文档旨在记录 claude-code 的安装流程，并配置相关 MCP 服务，以实现论文参考文献的自动化检索与管理（如果不设置，ai可能会编造参考文献）。


## 安装node.js
它提供了强大的包管理工具 npm，是安装和运行像 Claude Code 这样现代开发工具的“底座”。
## 安装git
## 安装 Claude Code
1、使用 npm 进行全局安装
```bash
    npm install -g @anthropic-ai/claude-code
```
2、启动并完成身份验证
```bash
    claude
```
3、跳过login,通常在 C:\Users\你的用户名\.claude.json
如果没有这个文件，就手动创建一个。
```bash
{
"hasCompletedOnboarding": true,
"completedOnboarding": true,
"autoUpdaterStatus": "disabled"
}
```
4、由于claude-code过于昂贵，我们可以使用更加低价的GLM Coding Plan
GLM Coding Plan是由智谱 AI (Zhipu AI) 开发的大型语言模型。它负责接收 Claude Code 发出的指令，分析代码，并给出修改方案。
原本 Claude Code 只能连接 Anthropic 自己的服务器（Claude 3.5 模型）智谱 AI 提供了兼容 Anthropic 格式的 API 接口。
```bash
# 编辑或新增 `settings.json` 文件
# MacOS & Linux 为 `~/.claude/settings.json`
# Windows 为`用户目录/.claude/settings.json`
# 新增或修改里面的 env 字段
# 注意替换里面的 `your_zhipu_api_key` 为您上一步获取到的 API Key
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "your_zhipu_api_key",
    "ANTHROPIC_BASE_URL": "https://open.bigmodel.cn/api/anthropic",
    "API_TIMEOUT_MS": "3000000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": 1
  }
}
# 再编辑或新增 `.claude.json` 文件
# MacOS & Linux 为 `~/.claude.json`
# Windows 为`用户目录/.claude.json`
# 新增 `hasCompletedOnboarding` 参数
{
  "hasCompletedOnboarding": true
}
```
5、arxiv MCP 服务启动
下载 arXiv MCP 项目
https://github.com/blazickjp/arxiv-mcp-server/archive/refs/heads/main.zip
将arxiv-mcp-server-main迁移到claude项目下，在 arxiv-mcp-server-main 目录下运行这行命令
```bash
C:\Users\xxxxxx\Python\Python312\python.exe -m pip install -e . -i https://mirrors.aliyun.com/pypi/simple/ --default-timeout=1000
```
修改配置文件 .claude.json
```bash
"arxiv": {
  "type": "stdio",
  "command": "C:\\Users\\xxxxxxxxx\\Python\\Python312\\python.exe",
  "args": [
    "-m",
    "arxiv_mcp_server"
  ]
}
```
在你的 Claude 终端窗口中,输入/mcp，查看是否连接成功。
**补充说明：**
（1）MCP 是由 Anthropic（Claude 的开发商）推出的一种**标准协议**。允许 Claude 直接访问你电脑上的文件、数据库，或者通过特定的 API 查阅互联网上的实时信息。
规定了客户端和服务器之间交换数据的 JSON 格式（JSON-RPC）
（2）arxiv-mcp-server 该项目是一个基于 Model Context Protocol (MCP) 标准开发的 后端服务程序。功能是API 封装与接口转换，将 arXiv 官方的 arXiv API（通常返回 XML 格式数据）封装成符合 MCP 规范的 JSON-RPC 接口。
持续运行在后台。接收来自 Claude 的查询指令（如 search_papers），执行实际的 Python 代码，去请求远程的 arXiv 数据库。
（3）.claude.json修改的配置信息。"arxiv": 该服务的唯一标识符（ID）。"type": "stdio": 指定通信传输协议。stdio（Standard Input/Output）表示 Claude 客户端将通过操作系统的“标准输入”和“标准输出”流与该 Python 进程进行实时数据交换。"command": 指定执行环境路径。"args": ["-m", "arxiv_mcp_server"]: 传递给 Python 的运行参数。
（4）在 arxiv-mcp-server-main 目录下运行的命令。在 arxiv-mcp-server 目录下有一个 pyproject.toml 或 setup.py 文件，里面记录了它运行所需的“外部组件”，例如 mcp、httpx、anyio 等。
虽然 arxiv-mcp-server 的核心逻辑代码已经在你下载的文件夹里了，但这个项目并不是独立运行的。它依赖于很多其他的第三方 Python 库才能工作。
将当前目录下的源代码作为一个“可开发模块”安装到指定的 Python 环境中。



总结：
虽然内容对于很多朋友来说很基础，但细致的操作过程可以更好地帮助大家快速上手。更多的注释也可以让我们知其所以然。
可以尝试与claude对话，获取一下最新的deepseek相关最新的论文，稍等片刻其就会返回。
快试试吧！

注意：arxiv存在预发表的论文，记得提醒claude，不要引用这类文章。