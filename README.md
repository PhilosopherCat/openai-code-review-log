# 🚀 OpenAI代码自动评审系统 - 基于 DeepSeek 的自动化工作流

![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/yourusername/reponame/main.yml?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-blue)
![DeepSeek](https://img.shields.io/badge/Powered%20by-DeepSeekAI-green)

## 🌟 项目概述

本系统通过 GitHub Actions + DeepSeek AI + 微信公众号通知构建了一套完整的自动化代码审查流水线，能够在代码提交时自动触发 AI 代码审查并将结果通过微信公众号推送。
<img width="928" height="460" alt="image" src="https://github.com/user-attachments/assets/2ff9008d-66e2-4020-9d8c-1fd4f3ec5a99" />


## 🛠️ 快速开始

### 1️⃣ 申请 DeepSeek API
[![DeepSeek](https://img.shields.io/badge/Get-DeepSeek%20API%20Key-blue)](https://www.deepseek.com/)
（点击跳转，后文同此操作）

```bash
APIHost: https://api.deepseek.com/v1/chat/completions
```

### 2️⃣ 创建 GitHub 仓库
| 仓库类型 | 示例链接 |
|----------|----------|
| 工程库 | [openai-code-review-test](https://github.com/PhilosopherCat/openai-code-review-test) |
| 日志库 | [openai-code-review-log](https://github.com/PhilosopherCat/openai-code-review-log) |

### 3️⃣ 获取 GitHub Token
[![GitHub Token](https://img.shields.io/badge/Generate-GitHub%20Token-lightgrey)](https://github.com/settings/tokens)

### 4️⃣ 配置微信公众号
[![WeChat](https://img.shields.io/badge/Setup-WeChat%20Sandbox-brightgreen)](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo)

消息模板示例：
```
项目：{{repo_name.DATA}} 
分支：{{branch_name.DATA}} 
作者：{{commit_author.DATA}} 
说明：{{commit_message.DATA}}
```

## ⚙️ GitHub Actions 配置

### 环境变量设置
```yaml
secrets:
  DEEPSEEK_APIHOST: "https://api.deepseek.com/v1/chat/completions"
  DEEPSEEK_APIKEYSECRET: "your-api-key"
  CODE_REVIEW_LOG_URI: "your-log-repo-url"
  CODE_TOKEN: "your-github-token"
  WEIXIN_APPID: "your-wechat-appid"
  WEIXIN_SECRET: "your-wechat-secret"
  WEIXIN_TEMPLATE_ID: "your-template-id"
  WEIXIN_TOUSER: "your-wechat-user"
```

### 完整工作流脚本
<details>
<summary>点击查看完整 GitHub Actions 配置</summary>

```yaml
name: AI Code Review Workflow

on:
  push:
    branches: ['*']
  pull_request:
    branches: ['*']

jobs:
  code-review:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
        with:
          fetch-depth: 2
          
      - name: Set up Java
        uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '11'
          
      - name: Download Review SDK
        run: |
          mkdir -p ./libs
          wget -O ./libs/review-sdk.jar \
            https://github.com/PhilosopherCat/openai-code-review-log/releases/download/v2.0/openai-code-review-sdk-1.0.jar
          
      - name: Run AI Code Review
        run: java -jar ./libs/review-sdk.jar
        env:
          # Github 配置；  
          GITHUB_REVIEW_LOG_URI: ${{ secrets.CODE_REVIEW_LOG_URI }}
          GITHUB_TOKEN: ${{ secrets.CODE_TOKEN }}
          COMMIT_PROJECT: ${{ github.repository }}
          COMMIT_BRANCH: ${{ github.ref_name }}
          COMMIT_AUTHOR: ${{ github.actor }}
          COMMIT_MESSAGE: ${{ github.event.head_commit.message }}
          # 微信配置 
          WEIXIN_APPID: ${{ secrets.WEIXIN_APPID }}
          WEIXIN_SECRET: ${{ secrets.WEIXIN_SECRET }}
          WEIXIN_TOUSER: ${{ secrets.WEIXIN_TOUSER }}
          WEIXIN_TEMPLATE_ID: ${{ secrets.WEIXIN_TEMPLATE_ID }}
          # OpenAi
          DEEPSEEK_APIHOST: ${{ secrets.DEEPSEEK_APIHOST }}
          DEEPSEEK_APIKEYSECRET: ${{ secrets.DEEPSEEK_APIKEYSECRET }}
```
</details>

## 📚 学习收获

通过本项目，您将掌握：

- 🧠 **系统设计思维**：完整的自动化工作流设计
- ⚡ **GitHub Actions**：CI/CD 流水线配置与优化
- 🤖 **AI 集成**：DeepSeek API 对接与 AI 代码审查
- 📱 **微信通知**：公众号模板消息对接
- 📦 **打包部署**：JAR 包管理与多环境部署策略

## 💡 项目优势

✅ **自动化**：提交代码即触发审查  
✅ **智能化**：AI 辅助代码质量分析  
✅ **即时通知**：微信实时推送审查结果  
✅ **可扩展**：支持多种 AI 模型接入  

---

<p align="center">
  🎉 立即体验 AI 赋能的代码审查工作流，提升开发效率与代码质量！
</p>

---

**备注**：将上述所有配置中的 `your-*` 替换为您自己的实际值，即可完成系统配置。
