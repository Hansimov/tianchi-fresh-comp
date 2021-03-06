---
---

本仓库用于记录 [天池新人实战赛之【离线赛】](https://tianchi.aliyun.com/getStart/information.htm?spm=5176.100067.5678.2.63d42b2bk6HcCO&raceId=231522) 的思路和方法。

---
---

## 赛题介绍

链接：[赛题与数据](https://tianchi.aliyun.com/getStart/information.htm?spm=5176.100067.5678.2.63d42b2bk6HcCO&raceId=231522)


### 题目描述

在真实的业务场景下，我们往往需要对所有商品的一个子集构建个性化推荐模型。在完成这件任务的过程中，我们不仅需要利用用户在这个商品子集上的行为数据，往往还需要利用更丰富的用户行为数据。

定义如下的符号：

* U：用户集合
* I：商品全集
* P：商品子集，P ⊆ I
* D：用户对商品全集的行为数据集合

那么我们的目标是利用 D 来构造 U 中用户对 P 中商品的推荐模型。

### 数据说明

本场比赛提供 20000 用户的完整行为数据以及百万级的商品信息。

竞赛数据包含两个部分：

1. 用户在商品全集上的移动端行为数据（D），表名为 `tianchi_fresh_comp_train_user.csv`，包含如下字段：

<ol>

| 字段 | 字段说明 | 提取说明 |
|:-|:-|:-|
| user_id | 用户标识 | 抽样&字段脱敏 |
| item_id | 商品标识 | 字段脱敏|
| behavior_type | 用户对商品的行为类型 | 包括浏览、收藏、加购物车、购买，对应取值分别是 1、2、3、4。|
| user_geohash | 用户位置的空间标识，可以为空 | 由经纬度通过保密的算法生成 |
| item_category | 商品分类标识 | 字段脱敏 |
| time | 行为时间 | 精确到小时级别 |

</ol>

2. 商品子集（P），表名为 `tianchi_fresh_comp_train_item.csv`，包含如下字段：

<ol>

| 字段 | 字段说明 | 提取说明 |
|:-|:-|:-|
| item_id | 商品标识 | 抽样&字段脱敏 |
| item_ geohash | 商品位置的空间标识，可以为空 | 由经纬度通过保密的算法生成 |
| item_category | 商品分类标识 | 字段脱敏 |

训练数据包含了抽样出来的一定量用户在一个月时间（11.18~12.18）之内的移动端行为数据（D）。

</ol>


### 评分数据格式

评分数据是这些用户在这个一个月之后的一天（12.19）对商品子集（P）的购买数据。

参赛者要使用训练数据建立推荐模型，并输出用户在接下来一天对商品子集购买行为的预测结果。 

参赛者完成用户对商品子集的购买预测之后，需要将结果放入指定格式的数据表（非分区表）中。

结果表名为：`tianchi_mobile_recommendation_predict.csv`。

且需满足如下要求：

1. 以 utf-8 格式编码
2. 包含 `user_id` 和 `item_id` 两列（均为 string 类型）
3. 去除重复

评分数据文件格式如下：

```
+---------+---------+
| user_id | item_id |
+-------------------+
| 100000  | 2345    |
| 100000  | 2478    |
| 100001  | 127900  |
| 100002  | 207245  |
+---------+---------+
```

### 评估指标

比赛采用经典的精确度（precision）、召回率（recall）和 F1 值作为评估指标。具体计算公式如下：

$$
精确度=\frac{|\cap(预测集合,真实集合)|}{|预测集合|}
$$


$$
召回率=\frac{|\cap(预测集合,真实集合)|}{真实集合}
$$

$$
F1 = \frac{2 \times 精确度 \times 召回率}{精确度+召回率}
$$

其中 PredictionSet 为算法预测的购买数据集合，ReferenceSet 为真实的答案购买数据集合。比赛以 F1 值作为最终的唯一评测标准。

---

