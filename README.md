# 小红书笔记数据分析

分析小红书平台上的笔记数据，生成深度的商业分析报告，包括关键词提取、聚类分析、痛点挖掘和商业洞察。

## 功能

- 📊 **描述性统计分析** - 笔记数、字数、互动数据分布
- 🔤 **关键词深度分析** - 中文分词、TF-IDF 提取、词性标注、共现分析
- 🎯 **聚类分析** - K-Means 主题建模，自动识别内容主题
- 😟 **痛点挖掘** - 疑问句识别、情感分析、用户原话提取
- 💡 **商业洞察** - 市场机会分析、策略建议

## 输出格式

- **Markdown 报告**（默认）- 生成 `.md` 文件
- **Word 报告**（可选）- 生成 `.docx` 文件（使用 pandoc 转换）

## 依赖

- Python 3.x
- [uv](https://github.com/astral-sh/uv) - Python 包管理
- pandas, jieba, scikit-learn, snownlp, openpyxl
- [pandoc](https://pandoc.org/) - Markdown 转 Word（可选）

## 安装

```bash
# 克隆项目
git clone https://github.com/ligj1706/xiaohongshu-analyzer.git
cd xiaohongshu-analyzer

# 安装 Python 依赖
uv pip install pandas jieba scikit-learn snownlp openpyxl numpy scipy joblib threadpoolctl

# 可选：安装 pandoc（输出 Word 格式需要）
# macOS: brew install pandoc
# Windows: https://pandoc.org/installing.html
# Linux: sudo apt install pandoc
```

## 使用方法

### 基本分析

```bash
# 激活虚拟环境
source .venv/bin/activate

# 运行分析
python analyze.py /path/to/your/xiaohongshu_data.xlsx
```

### 输出 Word 格式

```bash
python analyze.py /path/to/data.xlsx --format word
```

## 数据格式

Excel 文件应包含以下列：

| 列名 | 说明 |
|------|------|
| 笔记标题 | 笔记的标题 |
| 笔记内容 | 笔记正文内容 |
| 点赞数 | 点赞数量 |
| 收藏数 | 收藏数量 |
| 评论数 | 评论数量 |
| 分享数 | 分享数量 |

## 示例报告

见 `examples/` 目录

## License

MIT
