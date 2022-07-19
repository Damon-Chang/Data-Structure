# KD树
> 主题：机器学习——数据结构——KDT（K-Dimension-Tree）
> 参考URL：[CSDN笔记1](https://blog.csdn.net/Galaxy_yr/article/details/89285069) [CSDN笔记2](https://blog.csdn.net/weixin_48135624/article/details/115648035)

## KD树的定义
> KD树是多维二叉树（空间二叉树的一种特殊情况），其中存储k维点的信息，是对k维空间进行划分的一种**数据结构**。**一般用于解决二维空间和三维空间的信息检索**，例如聚类算法中的最近邻搜索。
## 使用范围
1. KNN问题，即查询离某个点第k近邻的点；
2. 查询最近最远；
3. 查询矩阵；
4. 图像处理。
## KD树的构建
### 利用方差

### 利用维度
例如对一个三维平面，在KD树每一层的构建中都选择一个维度来进行划分，将**k维数据空间分为两部分**，并使其尽量平衡。然后如此递归下去。
假如要存储n个三维点$(x,y,z)$的信息
1. 按x坐标sort一遍；
2. 选出中间值$x_{mid}$作为根节点；
3. 在子树中按照y坐标sort一遍，选择中间值作为根节点；
4. 接着再按照z坐标sort，以此类推。
其中sort的维度可以自己确定。一般更好的方法是**在数据比较分散的那一维进行划分（分散程度可以根据方差来衡量）**。

**举例**
![5](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pLmxvbGkubmV0LzIwMTkvMDQvMTMvNWNiMTg0NmM0Y2E3YS5wbmc?x-oss-process=image/format,png)
## KD树实现搜索
假设标记为星星的点是 test point， 绿色的点是找到的近似点，在回溯过程中，需要用到一个队列，存储需要回溯的点，在判断其他子节点空间中是否有可能有距离查询点更近的数据点时，做法是以查询点为圆心，以当前的最近距离为半径画圆，这个圆称为候选超球（candidate hypersphere），如果圆与回溯点的轴相交，则需要将轴另一边的节点都放到回溯队列里面来。
![](https://img-blog.csdnimg.cn/20210412235045160.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80ODEzNTYyNA==,size_16,color_FFFFFF,t_70)
### 查找点$(2.1,3.1)$
![](https://img-blog.csdnimg.cn/20210412235109310.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80ODEzNTYyNA==,size_16,color_FFFFFF,t_70)
1. 在(7,2)点测试到达(5,4)，在(5,4)点测试到达(2,3)，然后search_path中的结点为<(7,2),(5,4), (2,3)>，从search_path中取出(2,3)作为当前最佳结点nearest, dist为0.141；
2. 然后回溯至(5,4)，以(2.1,3.1)为圆心，以dist=0.141为半径画一个圆，并不和超平面y=4相交，如上图，所以不必跳到结点(5,4)的右子空间去搜索，因为右子空间中不可能有更近样本点了。
3. 于是再回溯至(7,2)，同理，以(2.1,3.1)为圆心，以dist=0.141为半径画一个圆并不和超平面x=7相交，所以也不用跳到结点(7,2)的右子空间去搜索。
4. 至此，search_path为空，结束整个搜索，返回nearest(2,3)作为(2.1,3.1)的最近邻点，最近距离为0.141。
### 查找点$(2,4.5)$
![](https://img-blog.csdnimg.cn/20210412235146479.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80ODEzNTYyNA==,size_16,color_FFFFFF,t_70)
1. 在(7,2)处测试到达(5,4)，在(5,4)处测试到达(4,7)【优先选择在本域搜索】，然后search_path中的结点为<(7,2),(5,4), (4,7)>，从search_path中取出(4,7)作为当前最佳结点nearest, dist为3.202；
2. 然后回溯至(5,4)，以(2,4.5)为圆心，以dist=3.202为半径画一个圆与超平面y=4相交，所以需要跳到(5,4)的左子空间去搜索。所以要将(2,3)加入到search_path中，现在search_path中的结点为<(7,2),(2, 3)>；另外，(5,4)与(2,4.5)的距离为3.04 < dist = 3.202，所以将(5,4)赋给nearest，并且dist=3.04。
3. 回溯至(2,3)，(2,3)是叶子节点，直接平判断(2,3)是否离(2,4.5)更近，计算得到距离为1.5，所以nearest更新为(2,3)，dist更新为(1.5)
4. 回溯至(7,2)，同理，以(2,4.5)为圆心，以dist=1.5为半径画一个圆并不和超平面x=7相交, 所以不用跳到结点(7,2)的右子空间去搜索。
5. 至此，search_path为空，结束整个搜索，返回nearest(2,3)作为(2,4.5)的最近邻点，最近距离为1.5。

## 使用sklearn实现kd树最近邻搜索
```
from sklearn import datasets
from sklearn.neighbors import KDTree,KNeighborsClassifier,KernelDensity,KNeighborsTransformer
X, y = datasets.load_iris(return_X_y=True)
tree = KDTree(X)
dist_to_knn, idx_of_knn = tree.query(X=X[[3]],k=5)
print(dist_to_knn)  # 到k个近邻的距离
print(idx_of_knn)   # k个近邻的索引
```


## 总结

- 首先通过二叉树搜索（比较待查询节点和分裂节点的分裂维的值，小于等于就进入左子树分支，大于就进入右子树分支直到叶子结点），顺着“搜索路径”很快能找到最近邻的近似点，也就是与待查询点处于同一个子空间的叶子结点；
- 然后再回溯搜索路径，并判断搜索路径上的结点的其他子结点空间中是否可能有距离查询点更近的数据点，如果有可能，则需要跳到其他子结点空间中去搜索（将其他子结点加入到搜索路径）。
- 重复这个过程直到搜索路径为空。
