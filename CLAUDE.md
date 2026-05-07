# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 重要规则

- **推 PR 前必须先和用户商量**，得到明确同意后才能创建或更新 PR。包括但不限于：`gh pr create`、往 PR 分支 push 新 commit、修改 PR 标题/内容。
- **不要往 upstream（YinLiLin/CMplot）直接 push**，只能推到用户 fork（lyangfan/CMplot）。
- **给上游发 PR 用独立分支**，不要用 master。

## 项目概述

CMplot 是一个 R 包，用于基因组分析的 Manhattan 图、QQ 图、环形图和密度图绘制。整个包是单个 R 文件：`R/CMplot.r`（~2500 行）。

## 运行测试

```bash
# 使用 breeding-agent conda 环境
export Rscript=/System/Volumes/Data/opt/anaconda3/envs/breeding-agent/bin/Rscript

# 运行单个测试
$Rscript test/test_xxx.R

# 快速验证（ad-hoc）
$Rscript -e '
pig60K <- read.csv("test/pig60K.csv")
source("R/CMplot.r")
CMplot(pig60K, plot.type="m", LOG10=TRUE, file="jpg", dpi=150, file.output=TRUE)
'
```

测试数据：`test/pig60K.csv`（44580 SNPs，3个性状）

## 代码架构

`R/CMplot.r` 的顶层结构：

1. **Helper 函数**（~100-380行）：`max_ylim`、`min_ylim`、`min_no_na`、`max_no_na`、`filter.points`、`DensityPlot`、`highlight_text`
2. **CMplot 主函数**：
   - 参数默认值 + 输入校验（~400-700行）
   - 数据预处理：chromosome positions、ticks、colors（~700-850行）
   - 环形图 `"c"`（~857-1430行）
   - 矩形曼哈顿图 `"m"`（~1432-2200行）
     - `if(multracks)` — 多轨（分面），用 `par(mfcol=c(R,1))`
     - `if(multraits)` — 多性状（叠加），单面板
     - `else` — 单性状，每性状一个文件
   - QQ 图 `"q"`（~2200-2550行），同样有 multracks/multraits/single 三个分支
   - 密度图 `"d"` 通过 DensityPlot 函数处理

## 文件输出

所有 8 个图类型块中都有 `if(file=="xxx") device(...)` 的模式，输出设备包括 jpg/jpeg、pdf、tiff、png、svg。SVG 是后来加的（PR #152）。修改文件输出时需要同时改所有 8 个块。

## Git 分支

| 分支 | 用途 |
|------|------|
| `master` | 用户自己的工作分支，跟踪 upstream/master |
| `fix-signal-line` | PR #151，3 个 bug fix |
| `add-svg-support` | PR #152，SVG 输出支持 |

upstream: `YinLiLin/CMplot`，fork: `lyangfan/CMplot`
