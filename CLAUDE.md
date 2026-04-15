# AI Digest Archive

## 目录结构

日报文件按月份归档到子目录中：

```
ai-digest-archive/
  2026-02/
    2026-02-08-ai-digest.md
    2026-02-09-ai-digest.md
    ...
  2026-03/
    2026-03-01-ai-digest.md
    ...
  data/                  # 运行时数据（索引等）
```

## 规则

- 每日日报文件必须放入 `YYYY-MM/` 月份子目录中，不要放在根目录
- 文件命名格式：`YYYY-MM-DD-ai-digest.md`
- 同一天增量更新：`YYYY-MM-DD-HHMM-update.md`

## 日报主题板块

日报中包含以下专题板块（位于 Smart Recommendations 之后、分类列表之前）：

| 板块 | 数据来源 | 说明 |
|------|----------|------|
| **YC 主题** | YC Blog、Hacker News | Y Combinator 相关动态 |
| **a16z 主题** | a16z 官网博客爬虫 + Substack + 播客 | Andreessen Horowitz AI/创业/投资洞察 |

### a16z 内容来源

a16z 官网（a16z.com）没有 RSS feed，通过自建爬虫 `scripts/fetch-a16z.ts` 抓取：
- 爬取 `a16z.com/news-content/` 列表页
- 逐篇访问文章页获取发布日期（JSON-LD `datePublished`）
- 输出到 `data/a16z-items.json`，在 pipeline 的 dedupe-filter 阶段合并

另外通过 RSS 订阅的 a16z 渠道：
- Substack newsletter：`https://a16z.substack.com/feed`（→ `a16z.news`）
- AI + a16z 播客：`https://feeds.simplecast.com/Hb_IuXOo`
- a16z Live 播客：`https://feeds.simplecast.com/AuWJKpna`

## 重点文章深度处理

当天日报中如果包含以下来源的文章，需要自动执行**下载 → 翻译中文 → 总结 → 生成信息图**的完整流程：

### 触发条件

| 来源 | 匹配规则 |
|------|----------|
| **Simon Willison 博客** | feedName 包含 `Simon Willison` 或链接域名为 `simonwillison.net` |
| **Anthropic 技术文** | 链接匹配 `anthropic.com/news/*`、`anthropic.com/engineering/*`、`anthropic.com/research/*`、`anthropic.com/blog/*` |

### 执行流程

1. 提取文章原始链接
2. 切换到 `~/Documents/pe/jixiaxuegong/blog/` 工作台
3. 执行该工作台的标准流程：爬取原文 → 翻译中文逐字稿 → 生成总结 → 生成信息图
4. 产出物存放在 `blog/` 对应的子目录中

### 日报标记

在 ai-digest 日报中，对已执行深度处理的文章，在标题前添加 `[D]` 标记（Deep-dive），便于翻阅时一眼识别：

- Smart Recommendations 中：`### 1. [D] Cowork and plugins for teams`
- 分类表格中：`| [D] [标题](链接) | 来源 | 时间 | 摘要 |`
