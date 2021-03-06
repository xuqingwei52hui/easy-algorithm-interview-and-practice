## 0.前言
在深度神经网络崛起之前，基于树类的算法是表现比较优异，非线性性能比较好的一大类算法，深受广大人民群众的喜爱。比如常见的基于树的算法有随机森林(Random Forest)，GBDT， XGboost, LightGBM等。而所有的这些算法，都是基于决策树(Decision Tree)进化而来的。因此了解熟悉决策树是我们学习算法过程中一个必不可少的环节。

决策树可以分为分类树与回归树。回归树一般只能处理连续型数据，而分类树则即可以处理连续型数据又可以处理离散型数据。

## 1.什么是CART树
CART树的全名叫做Classification and Regression Tree。从名字可以看出来，CART树即可以用来做分类又可以用来做回归。

CART树构建分类树与回归树的时候，主要有如下两点差异(其实也是分类树与回归树的差异):
1.连续值的处理。
CART分类树对连续值采取的是计算Gini系数大小来衡量特征划分点的好坏。
$$GINI*(D) = \sum_{i=1}^k p_k * (1 - p_k) = 1 - \sum_{i=1}^k p_k ^ 2$$

基尼指数的意义是从数据集D中随机抽取两个样本类别标识不一致的概率。基尼指数越小，数据集的纯度越高。分类决策树递归创建过程中就是每次选择GiniGain最小的节点做分叉点。
回归树的处理方式为，对于任意的特征J，选择任意的划分点s将数据集分成R1, R2，然后使得R1与R2的均方差和最小。

2.预测方式
分类树的预测方式为看叶子节点中概率最大的类作为当前预测类。
回归树一般是输出叶子节点的均值作为预测结果。


## 2.CART回归树的流程
对于决策树的生成过程，就是递归构建二叉树的过程。以CART回归树为例，我们来看看具体怎么构建。
假设X与Y分别为输入输出，Y是连续变量。
$$D = \{ (x_1, y_1), (x_2, y_2), ..., (x_n, y_n)) \}$$
最终我们想生成CART回归树$f(x)$。即在训练集上，递归地将每个区域分为两个子区域并决定每个子区域的输出值，来构建二叉决策树。

1.选择最优切分变量j与切分点s，并求解

$$\min _ { j , s } \left[ \min _ { c _ { 1 } } \sum _ { x _ { i } \in R _ { 1 } ( j , s ) } \left( y _ { i } - c _ { 1 } \right) ^ { 2 } + \min _ { c _ { 2 } } \sum _ { x _ { i } \in R _ { 2 } ( j , s ) } \left( y _ { i } - c _ { 2 } \right) ^ { 2 } \right]$$

遍历变量j，对固定的切分变量j扫描切分点s，选择最优的(j,s)。

2.用选定的(j,s)，划分区域，并决定相应的输出  
$$\hat{c}\_{m}=average(y_{i}|x_{i} \in R_{m}(j,s))$$  

3.对两个子区域重复1,2步骤，直到满足终止条件
4.将输入的空间划分为M个区域，$R_1, R_2, ..., R_M$，在每个单元上有固定的输出$c_m$，最终生成决策树
$$f(x) = \sum_{m=1} ^M c_mI, X \in R_m$$

## 3.剪枝
决策树很容易出现的一种情况是过拟合(overfitting)，所以需要进行剪枝。而基本的策略包括两种：预剪枝(Pre-Pruning)与后剪枝(Post-Pruning)。

预剪枝：其中的核心思想就是，在每一次实际对结点进行进一步划分之前，先采用验证集的数据来验证如果划分是否能提高划分的准确性。如果不能，就把结点标记为叶结点并退出进一步划分；如果可以就继续递归生成节点。  
后剪枝：后剪枝则是先从训练集生成一颗完整的决策树，然后自底向上地对非叶结点进行考察，若将该结点对应的子树替换为叶结点能带来泛化性能提升，则将该子树替换为叶结点。
