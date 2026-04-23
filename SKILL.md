---
name: lennys-podcast-newsletter
description: "Lenny Rachitsky 播客与 Newsletter 知识库。搜索、阅读、学习 Lenny 的 638 篇内容（289 播客转录 + 349 篇 Newsletter）。涵盖产品管理、增长、设计、工程、AI、创业、领导力等 17 个主题。触发词：Lenny、Lenny's Newsletter、Lenny's Podcast、'Lenny 怎么说'、'Lenny 有没有聊过'。"
---

# Lenny's Podcast & Newsletter 知识库

基于 Lenny Rachitsky 的完整内容存档（289 播客转录 + 349 篇 Newsletter），支持搜索、阅读、问答和学习。

## 数据概览

- **289 篇播客转录**：硅谷顶级产品人访谈（完整对话逐字稿）
- **349 篇 Newsletter**：产品管理、增长、职业发展深度文章
- **17 个主题标签**：design, leadership, strategy, growth, startups, career, product-management, b2b, engineering, b2c, ai, analytics, go-to-market 等

## 数据路径（自包含在 skill 内）

```
references/
├── 01-start-here/    # README + index.json（元数据索引）
├── 02-newsletters/   # 349 篇 Newsletter Markdown
└── 03-podcasts/      # 289 篇播客转录 Markdown
```

脚本自动使用 skill 目录下的 `references/`，无需配置外部路径。

## 搜索脚本

```bash
SCRIPT=~/.claude/skills/lennys-podcast-newsletter/scripts/lenny_search.py
```

### 命令列表

#### 1. 索引搜索（快速，按标题/描述/嘉宾/标签匹配）

```bash
python3 $SCRIPT search "关键词" [--type podcast|newsletter] [--tag TAG] [--limit N]
```

示例：
```bash
python3 $SCRIPT search "product-market fit" --limit 5
python3 $SCRIPT search "pricing" --type newsletter
python3 $SCRIPT search "" --tag ai --limit 10          # 列出所有 AI 相关内容
python3 $SCRIPT search "Brian Chesky"                   # 按嘉宾搜索
```

#### 2. 全文搜索（遍历所有文件内容，较慢但精确）

```bash
python3 $SCRIPT fulltext "关键词" [--type podcast|newsletter] [--limit N]
```

适用场景：索引搜索找不到时，或需要在正文中查找具体观点、引用。

#### 3. 阅读具体文章

```bash
python3 $SCRIPT read "03-podcasts/scott-belsky.md" [--lines 200]
```

返回 JSON 包含 content、total_lines、truncated 字段。超长内容分段读取。

#### 4. 列出标签

```bash
python3 $SCRIPT tags [--type podcast|newsletter]
```

#### 5. 列出嘉宾

```bash
python3 $SCRIPT guests [--limit 30]
```

#### 6. 统计信息

```bash
python3 $SCRIPT stats
```

## Workflow

### 用户提问关于 Lenny 内容时

1. **理解意图**：判断用户想查什么（某个主题、某位嘉宾、某个概念）
2. **搜索**：先用 `search` 快速匹配
3. **若无结果**：改用 `fulltext` 全文搜索。若仍无结果，告知用户该主题在知识库中未找到匹配内容
4. **阅读原文**：用 `read` 读取相关文件，理解完整上下文。若文件路径无效，用 `search` 重新定位正确路径
5. **回答**：基于原文内容回答用户问题，引用具体来源（嘉宾名 + 文章标题）

### 用户想学习某个主题时

1. 用 `search "" --tag TAG` 列出该主题所有内容。若 tag 无匹配，用 `tags` 命令查看可用标签并推荐最接近的
2. 推荐 3-5 篇最相关的文章/播客
3. 读取并总结关键观点
4. 如果用户想深入，读取完整内容详细讲解

### 用户想了解某位嘉宾的观点时

1. 用 `search "嘉宾名"` 找到对应播客。若无结果，用 `guests` 列表确认嘉宾姓名拼写
2. 读取播客转录
3. 提炼嘉宾的核心观点和金句

## 回答格式

回答时需注明来源：

```
**来源**: Lenny's Podcast — {嘉宾名} ({日期})
**标题**: {文章/播客标题}

{回答内容，引用原文关键段落}
```

## 注意事项

- 播客转录包含完整对话，每段有发言人和时间戳（如 `**Scott Belsky** (00:12:34):`）
- Newsletter 包含完整文章内容，有 YAML frontmatter（title, subtitle, date, tags, word_count）
- 内容为英文原文，回答时可按用户语言偏好翻译或保留原文
- 超长文件（部分播客转录超过 15000 字）需分段读取
