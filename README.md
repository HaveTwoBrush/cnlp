<div align="center">
  <img src="https://ailln.oss-cn-hangzhou.aliyuncs.com/github/cnlp/cnlp-logo-v4.png" width="60%">
  <br>
  <img src="https://img.shields.io/pypi/v/cnlp.svg">
  <img src="https://img.shields.io/badge/license-MIT-green.svg">
  <img src="https://img.shields.io/github/stars/dovolopor-research/cnlp.svg">
</div>

# cnlp = cn + nlp

🔥 专注于中文的「自然语言处理框架」

## 1 功能

1. [cnlp.data](https://cnlp.dovolopor.com/api/#_1-cnlp-data): 一个通用的文本处理器。
2. [cnlp.dataset](https://cnlp.dovolopor.com/api/#_2-cnlp-dataset): 提供常见的数据集加载。
3. [cnlp.model](https://cnlp.dovolopor.com/api/#_3-cnlp-model): 提供常见的模型。

## 2 安装

### 2.1 pip 安装

```bash
pip install cnlp
```

### 2.2 手动安装

```bash
git clone https://github.com/dovolopor-research/cnlp.git
cd cnlp && python setup.py install
```

## 3 快速上手

### 中文分词

分词是中文自然语言处理最基础的任务之一，我们先来看个分词的例子。

```python
from cnlp.data import Tokenizer

t = Tokenizer()

# 默认方式为有向无环图分词
result = t.cut("为中华之崛起而读书")
# result:
# ['为', '中华', '之', '崛起', '而', '读书']

# 机械分词
result = t.cut("为中华之崛起而读书", methods="mechanical")
# result:
# ['为', '中华', '之', '崛起', '而', '读书']

# 获取偏旁
result = t.radical("为中华之崛起而读书")
# result:
# ['丶', '丨', '十', '丶', '@UNK@', '走', '而', '讠', '乛']

# 获取部首
result = t.stroke("为中华之崛起而读书")
# result:
# [['点', '撇', '横折竖钩', '点'], ['竖', '横折', '横', '竖'], ['撇', '竖', '撇', '竖弯横钩', '横', '竖'],
# ['点', '横撇', '捺'], '@UNK@', ['横', '竖', '横', '竖', '横', '撇', '撇', '竖', '横折竖钩', '竖', '竖'],
# ['点', '横折提', '横', '竖', '横撇', '点', '点', '横', '撇', '点'], ['横折', '横折竖钩', '竖', '点']]
```

### 类别不平衡

数据集中的类别不平衡是在实际数据集中经常遇到的问题，通常采用**过采样扩充**或者**欠采样缩减**的方式解决。

```python
from cnlp.data import sampling

# 类别 pos:3 neg:2 unk:1
origin_dataset = [
  ["我好开心！", "pos"],
  ["今天心情不错啊～", "pos"],
  ["学会了 cnlp，真棒！", "pos"],
  ["我心态崩了啊！", "neg"],
  ["哎，又加班。。。", "neg"],
  ["吃雪糕中", "unk"]
]

# 默认为过采样，都扩充到 3
dataset = sampling(origin_dataset)
# dataset:
# [
#   ["我好开心！", "pos"],
#   ["今天心情不错啊～", "pos"],
#   ["学会了 cnlp，真棒！", "pos"],
#   ["我心态崩了啊！", "neg"],
#   ["哎，又加班。。。", "neg"],
#   ["我心态崩了啊！", "neg"],
#   ["吃雪糕中", "unk"],
#   ["吃雪糕中", "unk"],
#   ["吃雪糕中", "unk"]
# ]

# 也可以使用欠采样，都缩减到1
# 这种方式不推荐，容易使模型过拟合
dataset = sampling(origin_dataset, mode="under")
# dataset:
# [
#   ["学会了 cnlp，真棒！", "pos"],
#   ["我心态崩了啊！", "neg"],
#   ["吃雪糕中", "unk"]
# ]
```

### 数据集划分

数据集划分是训练模型前的常见操作，这里也提供了一个基本的方法。

```python
from cnlp.data import split

origin_dataset = [
  "今天天气不错啊",
  "我想吃烧烤",
  "地球人就知道吃",
  "说的好像你们火星人不爱吃一样",
  "没错啊！",
  "好吧",
  "你赢了",
  "那请你离开地球",
  "快走",
  "快快走"
]

# 默认以 train:test = 7:3 划分
train_data, test_data = split(origin_dataset)
# train_data:
# ['今天天气不错啊', '我想吃烧烤', '地球人就知道吃', '说的好像你们火星人不爱吃一样', '没错啊！', '好吧', '你赢了']
# test_data:
# ['那请你离开地球', '快走', '快快走']

# 支持划分 train val test 三种数据集
train_data, val_data, test_data = split(origin_dataset, [0.5, 0.3, 0.2])
# train_data:
# ['今天天气不错啊', '我想吃烧烤', '地球人就知道吃', '说的好像你们火星人不爱吃一样', '没错啊！'],
# val_data:
# ['好吧', '你赢了', '那请你离开地球']
# test_data:
# ['快走', '快快走']

# 支持打乱(shuffle)
train_data, val_data, test_data = split(origin_dataset, [0.5, 0.3, 0.2], shuffle=True)
# train_data:
# ['你赢了', '说的好像你们火星人不爱吃一样', '快走', '今天天气不错啊', '那请你离开地球']
# val_data:
# ['我想吃烧烤', '快快走', '地球人就知道吃']
# test_data:
# ['没错啊！', '好吧']
```

## 4 文档

请在 [https://cnlp.dovolopor.com](https://cnlp.dovolopor.com) 中查看它。

## 5 贡献

如果您计划为此项目提供新功能，实用程序功能或扩展，请首先打开一个 [Issues](https://github.com/dovolopor-research/cnlp/issues) 并与我们讨论该功能。

## 6 许可证

[![](https://award.dovolopor.com?lt=License&rt=MIT&rbc=green)](./LICENSE)

## 7 交流

欢迎添加微信号：`Ailln_`，备注「cnlp」，我邀请你进入交流群。

## 8 参考

- [jieba 分词](https://github.com/fxsjy/jieba)
- [Torch Text](https://github.com/pytorch/text)