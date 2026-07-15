# MoonShadow Data 示例仓库

这是 [MoonShadow](https://github.com/nuyue/MoonShadow) 博客的数据仓库模板。

## 使用方法

### 方式一：Fork 后设为私有

1. Fork 本仓库
2. 进入 Settings → General → Danger Zone → Change repository visibility
3. 设为 **Private**
4. 修改内容为你自己的文章/音乐/友链
5. 配置 Cloudflare Pages 环境变量（见下方）

## Cloudflare Pages 配置

在 Cloudflare Pages 项目设置 → Settings → Environment variables 中添加：

> **重要：** 所有变量必须设置为 **Secret** 类型

| 变量名 | 类型 | 说明 | 必需 |
|--------|------|------|------|
| `DATA_REPO_TOKEN` | Secret | GitHub Personal Access Token（需要仓库读取权限） | ✅ 必需 |
| `DATA_REPO_PATH` | Secret | 数据仓库路径，如 `your-name/MoonShadow-Data` | 可选，默认使用示例仓库 |

### 创建 GitHub Token

1. 进入 GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
2. 点击 **Generate new token**
3. 勾选权限：`repo`（私有仓库需要）
4. 复制生成的 Token

配置完成后重新部署即可使用私有数据。

## 自动构建（可选）

私有数据仓库更新后自动触发 CF Pages 构建：

1. 创建 Cloudflare API Token：
   - Cloudflare Dashboard → My Profile → API Tokens → Create Token
   - 选择 **Edit Cloudflare Workers** 模板

2. 在私有数据仓库添加 GitHub Secrets：
   
   | Secret | 值 |
   |--------|-----|
   | `CF_API_TOKEN` | Cloudflare API Token |
   | `CF_ACCOUNT_ID` | 你的 Account ID |
   | `CF_PROJECT_NAME` | CF Pages 项目名称 |

3. 添加 GitHub Actions 工作流 `.github/workflows/deploy.yml`：
   
   ```yaml
   name: Trigger CF Pages Deploy
   on:
     push:
       branches: [main]
   jobs:
     deploy:
       runs-on: ubuntu-latest
       steps:
         - name: Trigger Cloudflare Pages deployment
           run: |
             curl -X POST \
               "https://api.cloudflare.com/client/v4/accounts/${{ secrets.CF_ACCOUNT_ID }}/pages/projects/${{ secrets.CF_PROJECT_NAME }}/deployments" \
               -H "Authorization: Bearer ${{ secrets.CF_API_TOKEN }}" \
               -H "Content-Type: application/json" \
               --data '{"branch":"main"}'
   ```

## 目录结构

```
MoonShadow-Data-Example/
├── articles/             # 文章目录
│   └── welcome/
│       └── index.md      # 示例文章
├── music/                # 音乐目录（可选）
│   └── .gitkeep
├── links.json            # 友链配置（可选）
├── music.json            # 音乐配置（可选）
└── tools.json            # 工具配置（可选）
```

## 文件格式

### 文章格式

`articles/<slug>/index.md`:

```markdown
---
title: "文章标题"
slug: "article-slug"
description: "文章描述"
date: "2026-07-15"
categories:
  - "Tech"
tags:
  - "标签1"
  - "标签2"
cover: ""
---

## 正文标题

正文内容...
```

### 多语言文章

```
articles/
└── my-post/
    ├── index.zh.md    # 中文版
    └── index.en.md    # 英文版
```

### 友链格式

`links.json`:

```json
[
  {
    "name": "友链名称",
    "url": "https://example.com",
    "desc": { "zh": "中文描述", "en": "English description" },
    "logo": "/logo.svg",
    "gradient": "linear-gradient(135deg, #667eea, #764ba2)"
  }
]
```

### 音乐格式

`music.json`:

```json
{
  "autoPlay": false,
  "defaultVolume": 50,
  "defaultMode": "loop",
  "songs": [
    {
      "id": "song-1",
      "title": "歌曲名称",
      "artist": "艺术家",
      "file": "music/song.mp3",
      "lrc": "music/song.lrc"
    }
  ]
}
```

### 工具格式

`tools.json`:

```json
[
  {
    "id": "my-tools",
    "name": { "zh": "我的工具", "en": "My Tools" },
    "icon": "Wrench",
    "color": "#3b82f6",
    "tools": [
      {
        "id": "tool-1",
        "name": { "zh": "工具名", "en": "Tool Name" },
        "icon": "Hammer",
        "desc": { "zh": "工具描述", "en": "Tool description" }
      }
    ]
  }
]
```

## 可用分类

文章 categories 支持：
- `Tech` - 技术
- `Dev` - 开发
- `Life` - 生活
- `Other` - 其他

## 相关链接

- 主仓库：[nuyue/MoonShadow](https://github.com/nuyue/MoonShadow)
- 作者博客：[moonshadow.pages.dev](https://moonshadow.pages.dev)