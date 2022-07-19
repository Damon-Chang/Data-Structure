# Ball Tree原理及实现
> 主题：主题：机器学习——数据结构——ball tree
> 参考URL:[CSDN笔记](https://blog.csdn.net/weixin_41770169/article/details/81634307) [知乎文章](https://zhuanlan.zhihu.com/p/336843196)

## 原理
ball tree是为例改进KDTree的树形结构，并且沿着笛卡尔坐标进行划分的低效率，ball tree将在一系列嵌套的超球体上分割数据。也就是说：使用超球面而不是超矩形划分区域。虽然在构建数据结构的花费上大过于KDtree，但是在高维甚至很高维的数据上都表现的很高效。
球树递归地将数据划分为由质心C和半径r定义的节点，使得节点中的每个点都位于由r和C定义的超球内。通过使用**三角不等式**来减少邻居搜索的候选点数量的。
## Ball tree的构建
1.  首先，找到与中心点  ![[公式]](https://www.zhihu.com/equation?tex=p_i)  最远的节点，作为  ![[公式]](https://www.zhihu.com/equation?tex=p_i)  的左子节点  ![[公式]](https://www.zhihu.com/equation?tex=p_i%5EL)
2.  接着，找到与  ![[公式]](https://www.zhihu.com/equation?tex=p_i%5EL)  最远的节点作为  ![[公式]](https://www.zhihu.com/equation?tex=p_i)  的右子节点  ![[公式]](https://www.zhihu.com/equation?tex=p_i%5ER)
3.  指向  ![[公式]](https://www.zhihu.com/equation?tex=p_i)  的所有节点如果离  ![[公式]](https://www.zhihu.com/equation?tex=p_i%5EL)  近则指向  ![[公式]](https://www.zhihu.com/equation?tex=p_i%5EL)  ，否则指向  ![[公式]](https://www.zhihu.com/equation?tex=P_i%5ER)
4.  这样就划分出了新的两个区域，区域的中心点分别为  ![[公式]](https://www.zhihu.com/equation?tex=p_i%5EL)  和  ![[公式]](https://www.zhihu.com/equation?tex=P_i%5ER)
5.  最后，记录每个中心节点的位置  ![[公式]](https://www.zhihu.com/equation?tex=c)  和包含其所有所属节点的最小半径  ![[公式]](https://www.zhihu.com/equation?tex=r)

按照上述过程，可以得到下图结果
![](https://pic4.zhimg.com/80/v2-5c69ddcfe98d18119e0cd43946deec07_720w.jpg)
每次划分的时间复杂度是 ![[公式]](https://www.zhihu.com/equation?tex=O%28n%29) ， ![[公式]](https://www.zhihu.com/equation?tex=n) 表示该区域内的节点数。
## 查询
-   先自上而下找到包含target的叶子结点  ![[公式]](https://www.zhihu.com/equation?tex=%28c%2C+r%29)  ，从此结点中找到离它最近的观测点。这个距离就是最近邻的距离的上界
-   检查它的兄弟结点中是否包含比这个上界更小的观测点

    - 如下图所示，五角星表示目标节点，灰色的圆表示兄弟节点的区域，虚线上的节点，表示当前与目标节点最近的观测点；
    - 如果 目标节点与兄弟节点的圆心的距离 > 兄弟节点所在的源半径 + 当前最小距离，那么这个兄弟节点所在的区域不可能包含所要的观测点（如图所示的三角形关系）
    - 
   ![](https://pic4.zhimg.com/80/v2-0f7839b520d2cd4b45fa99ac19b68437_720w.jpg)
 
 ## sklearn实现
 [官方文件中文版](https://zhuanlan.zhihu.com/p/336843196)
 
