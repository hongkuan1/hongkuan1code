---
title: 机器学习实战
category: 文档
tag: 学习
sticky: true
abbrlink: 61497
date: 2023-11-26 17:09:00
---

# 第一部分

- 本书前两部分主要探讨监督学习 （supervised learning）。
    - 监督学习过程中，我们只需要给定输入样本集，机器就可以从中推演出指定目标变量的可能结果。监督学习相对比较简单，机器只需要从输入数据中预测合适的模型，并从中计算出目标变量的结果。
    - 监督学习一般适用两种类型的目标变量
        1. [标称型]{.red}：标称型目标变量的结果只在有限目标集中取值，如真和假、动物分类集合{爬行类、鱼类、哺乳类、两栖类}；
        2. [数值型]{.red}：数值型目标变量用于回归分析；
    

## 机器学习基础

- 何谓机器学习？
    - 简单的说，机器学习就是把无序的数据转换为有用的信息。

- 机器学习用到了统计学知识。在多数人看来，统计学不过是企业用以炫耀产品功能的一种诡计而已。而我也是这么认为的，尽管这样的想法很狭隘。然而，在现实世界中，并不是每个问题都存在确定的解决方案。在很多时候，我们无法透彻地理解问题，，或者没有足够的计算资源为问题精确建立模型，例如我们无法给人类活动的动机建立模型。为了解决这些问题，我们就需要使用统计学知识。

- 传感器数据

- 目标变量是机器学习算法的预测结果，在分类算法中目标变量的类型通常是[标称型]{.red}的

- 分类和回归属于监督学习，之所以称之为监督学习，是因为这类算法必须知道预测什么，即目标变量的分类信息

- 与监督学习相对应的是无监督学习，此时数据没有类别信息，也不会给定目标值。

- 开发机器学习应用程序的步骤
    1. 收集数据
    2. 准备输入数据
    3. 分析输入数据
    4. 训练算法
    5. 测试算法
    6. 使用算法

## k-近邻算法

- 概述
    - 简单的说，k-近邻算法采用测量不同特征值之间的距离方法进行分类。
    - 优点： 精度高、对异常值不敏感、无数据输入假定
    - 缺点：计算复杂度高，空间复杂度高
    - 使用数据范围：数值型和标称型
- 工作原理
    - 存在一个样本数据集合，也称作训练样本集，并且样本集中每个数据都存在标签，即我们知道样本集中每一数据与所属分类的对应关系。输入没有标签的新数据后，将新数据的每个特征与样本集中数据对应的特征进行比较，然后算法提取样本集中特征最相似数据（最近邻）的分类标签。
    - 一般来说，我们只选择样本数据集中前 K 个最相似的数据，这就是 K-近邻算法中 K 的出处，通常 K 是不大于20的整数。最后，选择 K 个最相似数据中出现次数最多的分类，作为新数据的分类。

- 准备：使用Python 倒入数据
    1. 创建名为 KNN.py的 Python 模块
    2. 导入两个模块：一个为科学计算包 numpy,第二个是图标散列图模块 matplotlib
    3. 为了方便使用 createDataSet(),他创建数据集和标签
    ```python
    import numpy as np

    def createDataSet():
        group = np.array([[1.0, 1.1], [1.0, 1.0], [0, 0], [0, 0.1]])
        label = ['A', 'A', 'B', 'B']
        return group, label


    if __name__ == '__main__':
        group, label = createDataSet()
        print("Group:", group)
        print("Label:", label)
    ```
    4. 结果输出
    ![knn1](/img/software/knn1.png)
    5. ```python 
        import matplotlib
        import numpy as np
        # TkAgg: 使用 Tkinter 作为用户界面工具包的后端
        matplotlib.use('TkAgg')
        import matplotlib.pyplot as plt


        # 数据
        def createDataSet():

            # 数据
            group = np.array([[1.0, 1.1], [1.0, 1.0], [0, 0], [0, 0.1]])
            label = np.array(['A', 'A', 'B', 'B'])

            # 绘制散点图
            plt.scatter(group[label == 'A', 0], group[label == 'A', 1], marker='o', label='Class A')
            plt.scatter(group[label == 'B', 0], group[label == 'B', 1], marker='x', label='Class B')

            # 添加标题和标签
            plt.title('Scatter Plot of Classes A and B')
            plt.xlabel('Feature 1')
            plt.ylabel('Feature 2')

            # 添加图例
            plt.legend()

            # 显示图表
            plt.show()


        if __name__ == '__main__':
            createDataSet()
        ```

## 决策树