# web_building
网站推荐系统
武汉大学国家网络安全学院暑期实训项目，最佳项目奖，最佳算法奖

环境：
Python
django

# 原理：
基于用户(user-based)的协同过滤推荐算法
基于用户的协同过滤算法是通过用户的历史行为数据发现用户对视频内容的喜欢(如视频点赞，收藏，内容评论或分享)，并对这些喜好进行度量和打分。根据不同用户对具有相同视频内容的态度和偏好程度计算用户之间的关系，在有相同喜好的用户间进行商品推荐。
在user-based协同过滤推荐算法中，user1和user3都收藏了product2和product3，通过对user的相似度计算，两者具有相近的距离。在后续的推荐过程中，user1独有的收藏视频product1和product4会被推荐给user3。

## 用户-电影评分矩阵的构建
我们从网络中获得到MovieLens数据集对用户-电影评分矩阵进行构建，数据集中包含610个用户对不同电影的10万个评分，分值为1至5，不同的用户通过电影ID相互联系
```python
"""
新建一个data字典存放每位用户评论的电影和评分, 如果字典中没有某位用户，则使用用户ID来创建这位用户,否则直接添加以该用户ID为key字典中
"""
file = open("./ml-latest-small/data.csv",'r', encoding='UTF-8')
data = {}
for line in file.readlines():
    line = line.strip().split(',')
    if not line[0] in data.keys():
        data[line[0]] = {line[3]:line[1]}
    else:
        data[line[0]][line[3]] = line[1]
```

## 寻找近邻用户
对于不同用户之间的相似性，常常通过简单的欧式距离进行计算[2]，我们将采用person系数，也称为皮尔森积矩相关系数，对不同用户间的相似性进行计算。对于不同的用户，person系数取值在[-1,1]之间，如果person系数接近于1，则表示两个用户之间具有强相关性，两者对电影具有相同的个人喜好。当person系数接近于-1时，两个用户间完全负相关，及表示两人对电影的喜好完全对立。Person系数为0时，表示两个用户对电影的个人喜好并没有太多的联系。

![](https://raw.githubusercontent.com/BBQldf/PicGotest/master/20220306174002.png)

## 推荐
针对我们将要推荐的用户，我们通过近邻用户间的评分进行预测，预测公式如下：

![](https://raw.githubusercontent.com/BBQldf/PicGotest/master/20220306173956.png)

p为用户x对电影m的预测评分，s表示用户x的近邻用户，在本文中数量为10个。目标用户的近邻集合为Im。我们将最终得到的预测结果放到统一的集合中，并将预测结果进行降序排序，得到最终的电影推荐结果，并将结果输出。
