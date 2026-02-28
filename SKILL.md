---
name: xiaohongshu-analyzer
description: "分析小红书笔记数据，生成深度商业分析报告，包括关键词提取、聚类分析、痛点挖掘和商业洞察。当用户提供包含小红书笔记数据的 Excel 文件进行分析时使用此技能。"
license: MIT
---

# 小红书笔记数据分析

分析小红书平台上的笔记数据，生成深度的商业分析报告，包括关键词提取、聚类分析、痛点挖掘和商业洞察。

## 你的能力
 
### 1. 数据加载与预处理
- 自动识别并加载 Excel 文件
- 智能处理缺失数据和异常值
- 文本清洗（去除话题标签、@用户、URL等）

### 2. 描述性统计分析
- 基本统计：笔记数、字数、平均长度
- 互动数据统计：点赞、收藏、评论、分享的最小值、最大值、平均值、中位数、标准差
- 互动分布分析：识别爆款内容特征

### 3. 关键词深度分析
- 中文分词（使用 jieba）
- **停用词过滤**（去除话题、不是、一个、我们等无意义高频词）
- **自定义词典**（添加网络用语和专有名词：打工人、草台班子、35岁、AI等）
- TF-IDF 关键词提取
- 词性标注和分类
- 语义网络构建（共现分析）

### 3.1 停用词列表（必选）
```python
STOPWORDS = {
    # 话题标签类
    '话题', '话题标签',
    # 否定连词
    '不是', '而是', '没有', '不是', '不会', '不能', '不要',
    # 代词
    '一个', '我们', '这个', '什么', '他们', '这种', '大家', '这个', '那个',
    # 副词
    '就是', '可以', '需要', '应该', '已经', '还有', '很多', '其实',
    # 其他无意义词
    '这样', '那样', '自己', '觉得', '知道', '比如', '例如', '如果',
}
```

### 3.2 自定义词典（按需添加）

**重要：自定义词典应根据实际数据内容动态调整，不要硬编码特定领域的词！**

添加原则：
- 分析数据后发现有意义的词被错误拆分 → 添加到词典
- 常见网络用语/热点词 → 可预先添加

```python
# 通用网络用语（可选，酌情添加）
common_words = ['AI', 'GPT', 'OKR', 'KPI']  # 英文缩写
# 领域专有词根据实际数据添加，不要预先设定
```

### 3.3 动态添加自定义词典（推荐）
```python
import jieba
from collections import Counter

# 第一步：初步分词，找出被错误拆分的词
def find_problem_words(texts):
    """找出被错误拆分的连续2-4字词组"""
    all_ngrams = []
    for text in texts:
        text = str(text)
        # 提取所有2-4字的连续字符
        for length in [2, 3, 4]:
            for i in range(len(text) - length + 1):
                word = text[i:i+length]
                # 排除标点、纯数字等
                if word.isalpha() or '\u4e00' <= word[0] <= '\u9fff':
                    all_ngrams.append(word)
    
    # 找出高频但被拆分的词
    ngram_freq = Counter(all_ngrams)
    return ngram_freq

# 使用示例
# 先分词检查，发现有意义的连续词被拆分时，再调用 jieba.add_word() 添加
# jieba.add_word('你发现的有意义的词')
```

### 3.4 完整分词代码示例
```python
import jieba
from collections import Counter

# 停用词列表（见 3.1）
# STOPWORDS = {...}

# 根据实际数据发现的专有词（动态添加）
# jieba.add_word('数据中发现的专有词')

def clean_segment(text):
    """分词并过滤停用词"""
    words = jieba.cut(str(text))
    return [w for w in words if len(w) > 1 and w not in STOPWORDS]

# 使用示例
all_words = []
for text in df['合并文本']:
    all_words.extend(clean_segment(text))

word_freq = Counter(all_words)
print(word_freq.most_common(20))
```

### 4. 聚类分析
- TF-IDF 向量化
- K-Means 聚类算法
- 轮廓系数、CH 指数、DB 指数评估
- 自动主题命名和特征提取

### 5. 痛点分析
- 句式模式匹配（疑问句、否定句）
- 关键词情感分析
- 用户原话提取验证
- 痛点强度和商业价值评估

### 6. 商业洞察生成
- 市场机会矩阵分析
- 内容创作策略建议
- 差异化竞争策略
- 战略行动建议

## 输入数据格式

Excel 文件应包含以下列：
- 笔记标题
- 笔记内容
- 点赞数
- 收藏数
- 评论数
- 分享数

## 输出报告格式

生成的 Markdown 报告包含：

```markdown
# 小红书"XX"关键词笔记数据分析报告

## 📊 一、数据概览与描述性分析
### 1.1 数据基本信息
### 1.2 互动数据统计分析
### 1.3 互动分布深度解读
### 1.4 爆款内容拆解

## 🔤 二、关键词深度分析
### 2.1 高频关键词TOP50（含词性标注）
### 2.2 去噪后的核心关键词聚类
### 2.3 语义网络深度分析
### 2.4 关键词商业价值矩阵

## 🎯 三、聚类分析（K-Means主题建模）
### 3.1 聚类结果概览
### 3.2 各聚类详细解读
### 3.3 聚类质量评估

## 😟 四、痛点深度分析（核心发现）
### 4.1 痛点识别方法论
### 4.2 核心痛点排行榜
### 4.3 痛点详细剖析
### 4.4 痛点情感分析

## 💡 五、商业洞察与战略建议
### 5.1 市场机会矩阵
### 5.2 TOP 3 商业机会详解
### 5.3 内容创作策略
### 5.4 差异化竞争策略

## 📈 六、总结与行动建议
### 6.1 核心发现
### 6.2 战略优先级
### 6.3 成功公式
```

### 输出格式选项

用户可以指定输出格式：
- **Markdown**（默认）：生成 `.md` 文件
- **Word**：生成 `.docx` 文件（使用 docx skill）
- **同时输出**：生成 `.md` 和 `.docx` 两个文件

### 输出为 Word 格式

当用户要求输出 Word 格式时，使用 **docx skill** 进行转换：

1. **先生成 Markdown 报告**
2. **调用 docx skill 转换**

```python
# 1. 保存 Markdown 报告
with open('报告.md', 'w', encoding='utf-8') as f:
    f.write(report_content)

# 2. 使用 pandoc 转换为 Word
import subprocess
subprocess.run([
    'pandoc', '报告.md',
    '-o', '报告.docx',
    '--standalone',
    '--toc',  # 目录
    '--number-sections',  # 编号
])
```

或者使用 Python：

```python
# 使用 pypandoc 转换
import pypandoc
pypandoc.convert_file(
    '报告.md',
    'docx',
    outputfile='报告.docx',
    extra_args=['--standalone', '--toc']
)
```

> **注意**：如需更复杂的 Word 格式（多级标题样式、表格样式、图片等），请使用 docx skill 的完整能力。

## 使用方法

### 方式一：直接对话分析
```
用户：帮我分析一下 /path/to/your/xiaohongshu_data.xlsx
用户：帮我分析并输出Word格式 /path/to/your/xiaohongshu_data.xlsx
```

你将：
1. 首先读取并了解 Excel 文件结构
2. 运行完整的数据分析流程
3. 生成专业的 Markdown 报告
4. 如果用户要求 Word 格式，使用 pandoc 转换为 .docx
5. 提供关键洞察和行动建议

### 方式二：指定分析类型
```
用户：快速分析这个文件 /path/to/data.xlsx
用户：深度分析这个文件 /path/to/data.xlsx
```

- `quick`：仅做基础统计和关键词分析
- `full`：完整分析（默认）
- `deep`：增加更多高级分析（情感分析、预测模型等）

## 分析流程

**重要：必须使用 uv 来执行 Python 代码！**

1. **环境准备** → 使用 `uv venv` 或激活 `.venv`
2. **数据加载** → 读取 Excel，验证数据结构
3. **预处理** → 文本清洗，缺失值处理
4. **描述性统计** → 计算各项指标，生成数据概览
5. **关键词分析** → 分词、TF-IDF、词性标注、共现分析
6. **聚类分析** → 向量化、K-Means 聚类、主题提取
7. **痛点分析** → 模式匹配、情感分析、用户原话挖掘
8. **商业洞察** → 机会分析、策略建议
9. **报告生成** → 输出 Markdown 报告
10. **格式转换**（如需要）→ 转换为 Word 格式

### 执行命令示例

```bash
# 激活 uv 虚拟环境
cd /your/project/path
source .venv/bin/activate

# 运行分析
python -c "
import pandas as pd
import jieba
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.cluster import KMeans
# ... 完整分析代码
"
```

## 技术栈

- Python 3.x（使用 uv 管理）
- uv - Python 包管理（替代 pip，速度更快）
- pandas - 数据处理
- jieba - 中文分词
- scikit-learn - 机器学习（TF-IDF、K-Means）
- snownlp - 情感分析
- pandoc - Markdown 转 Word 格式
- pypandoc（可选）- Python 调用 pandoc

## 环境配置

### 使用 uv 管理依赖

```bash
# 激活虚拟环境
cd /your/project/path
source .venv/bin/activate

# 或者使用 uv
uv venv  # 创建虚拟环境（如果需要）
uv pip install pandas jieba scikit-learn snownlp openpyxl
```

### 依赖安装命令

如果虚拟环境未配置，运行：
```bash
uv pip install pandas jieba scikit-learn snownlp openpyxl numpy scipy joblib threadpoolctl pypandoc

# macOS 需要先安装 pandoc
brew install pandoc
```

> **注意**：Windows 用户可从 https://pandoc.org/installing.html 下载安装包

## 注意事项

- 如果 Excel 文件列名不完全匹配，尝试智能匹配相似列
- 如果缺少必要列，提示用户补充
- 分析过程中遇到问题，先尝试解决，无法解决时明确告知用户
- 确保报告中的数据和结论都有数据支撑，避免主观臆断
- **关键词分析必须使用停用词过滤**，否则结果会被无意义的高频词淹没
- **自定义词典应根据数据内容持续更新**，尤其是热点网络用语
- 中文关键词以2字为主是正常现象，这是语言特点而非分词错误
