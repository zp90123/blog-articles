假设要在 n 个城市之间通信联络网，则连通 n 个城市只需要 n-1 条线路。这时，自然会考虑这样一个问题，如何在最节省经费的前提下建立这个通信网。

上面的这个问题，就是最小生成树的问题。这篇文章就来介绍下解决这一问题的 Prim 算法（普里姆算法），该算法于 1930 年由捷克数学家沃伊捷赫·亚尔尼克发现，并在 1957 年由美国计算机科学家罗伯特·普里姆独立发现，1959 年，艾兹格·迪科斯彻再次发现了该算法。因此，在某些场合，普里姆算法又被称为 DJP 算法、亚尔尼克算法或普里姆－亚尔尼克算法。

## 算法过程

我们用一个例子来具体说明普里姆算法的流程。

![](https://resource.ethsonliu.com/image/20190928_01.png)

随机选一起点，假如为 0，`low_cost[i]` 表示以 i 为终点的边的权值。其过程描述如下：

| 步骤  | low_cost[1] | low_cost[2] | low_cost[3] | low_cost[4] |  已找到的集合  |
| :---: | :---------: | :---------: | :---------: | :---------: | :------------: |
| 第1步 |      8      |      3      |      2      |     +∞      |     { 3 }      |
| 第2步 |      3      |      3      |      ×      |      5      |    { 3, 1 }    |
| 第3步 |      ×      |      3      |      ×      |      5      |  { 3, 1, 2 }   |
| 第4步 |      ×      |      ×      |      ×      |      1      | { 3, 1, 2, 4 } |
| 第5步 |      ×      |      ×      |      ×      |      ×      | { 3, 1, 2, 4 } |

第 1 步：从起点 0 开始，找到与其邻接的点：1，2，3，更新 `low_cost[]` 数组，因 0 不与 4 邻接，故 `low_cost[4]` 为正无穷。在 `low_cost[]` 中找到最小值，其顶点为 3，即此时已找到最小生成树中的一条边：`0→3`。

第 2 步：从 3 开始，继续更新 `low_cost[]` 数组：3 与 1 邻接，因 `3→1` 比 `low_cost[1]` 小，故更新 `low_cost[1]` 为 3；3 与 2 邻接，因 `3→2` 比 `low_cost[2]` 大，故不更新 `low_cost[2]` ；3 与 4 邻接，因 `3→4` 比 `low_cost[4]` 小，故更新 `low_cost[4]` 为 5。在 `low_cost[]` 中找到最小值，其顶点为 1 或者 2，随便取一个即可，我们这里取 1。即此时又找到一条边：`3→1` 。

第 3 步：从 1 开始，继续更新 `low_cost[]` 数组：因与 1 邻接的点都被放入最小生成树中，故不更新，直接在 `low_cost[]` 中找到最小值，其顶点为 2，即此时又找到一条边：`0→2`。

第 4 步：从 2 开始，继续更新 `low_cost[]` 数组：2 与 4 邻接，因 `2→4` 比 `low_cost[4]` 小，故更新 `low_cost[4]` 为 1。在 `low_cost[]` 中找到最小值，其顶点为 4，即此时又找到一条边：`2→4`。

第 5 步：最小生成树完成，停止。

## 代码

```c++
#include <iostream>

using namespace std;

int  matrix[100][100]; // 邻接矩阵
bool visited[100];     // 标记数组
int  low_cost[100];    // 边的权值
int  path[100];        // 记录生成树的路径
int  source;           // 指定生成树的起点
int  vertex_num;       // 顶点数
int  edge_num;         // 边数
int  sum;              // 生成树权和

void Prim(int source)
{
    memset(visited, 0, sizeof(visited));
    visited[source] = true;
    for (int i = 0; i < vertex_num; i++)
    {
        low_cost[i] = matrix[source][i];
        path[i] = source;
    }

    int min_cost;       // 权值最小
    int min_cost_index; // 权值最小的下标
    sum = 0;
    for (int i = 1; i < vertex_num; i++) // 除去起点，还需要找到另外 vertex_num-1 个点
    {
        min_cost = INT_MAX;
        for (int j = 0; j < vertex_num; j++)
        {
            if (visited[j] == false && low_cost[j] < min_cost) // 找到权值最小
            {
                min_cost = low_cost[j];
                min_cost_index = j;
            }
        }

        visited[min_cost_index] = true;  // 该点已找到，进行标记
        sum += low_cost[min_cost_index]; // 更新生成树权和

        for (int j = 0; j < vertex_num; j++) // 从找到的最小下标更新 low_cost 数组
        {
            if (visited[j] == false && matrix[min_cost_index][j] < low_cost[j])
            {
                low_cost[j] = matrix[min_cost_index][j];
                path[j] = min_cost_index;
            }
        }
    }
}

int main()
{
    cout << "请输入图的顶点数（<=100）：";
    cin >> vertex_num;
    cout << "请输入图的边数：";
    cin >> edge_num;

    for (int i = 0; i < vertex_num; i++)
        for (int j = 0; j < vertex_num; j++)
            matrix[i][j] = INT_MAX; // 初始化 matrix 数组

    cout << "请输入边的信息：\n";
    int u, v, w;
    for (int i = 0; i < edge_num; i++)
    {
        cin >> u >> v >> w;
        matrix[u][v] = matrix[v][u] = w;
    }

    cout << "请输入起点（<" << vertex_num << "）：";
    cin >> source;
    Prim(source);

    cout << "最小生成树权和为：" << sum << endl;
    cout << "最小生成树路径为：\n";
    for (int i = 0; i < vertex_num; i++)
        if (i != source)
            cout << i << "----" << path[i] << endl;

    return 0;
}
```

运行如下：

```plaintext
请输入图的顶点数（<=100）：5
请输入图的边数：7
请输入边的信息：
0 1 8
0 2 3
0 3 2
1 3 3
3 2 4
2 4 1
3 4 5
请输入起点（<5）：0
最小生成树权和为：9
最小生成树路径为：
1----3
2----0
3----0
4----2
```

## 算法的正确性证明

以下除非特别说明，否则都默认是连通图，即是存在最小生成树的。

Prim 算法利用了最小生成树（Minimu Spanning Tree，简称 MST）性质，描述为：

假设 $N=(V,\{E\})$ 是一个连通图（$V$ 为顶点集合，$\{E\}$ 为边集合），$U$ 是已被加入生成树的顶点集合。若 $(u,v)$ 是一条具有最小权值的边，其中 $u∈U，v∈V-U$ （其中 $V-U$ 就是未被加入生成树的顶点集合，如下图），则必存在一棵包含边 $(u,v)$ 的最小生成树。

![](https://resource.ethsonliu.com/image/20190928_02.png)

可以用反证法证明。见下图，假设图 $N$ 的任何一棵最小生成树都不包含 $(u,v)$。设 $T$ 是 $N$ 上的一棵最小生成树，当将边 $(u,v)$ 加入到 $T$ 时，由生成树的定义，$T$ 中必存在一条包含 $(u,v)$ 的回路。另一方面，由于 $T$ 是生成树，则在 $T$ 上必存在另一条边 $(u',v')$，其中 $u'∈U，v'∈V-U$，且 $u$ 与 $u'$，$v$ 与 $v'$ 之间均有路径相通。删去边 $(u',v')$，便可消除上述回路，同时得到另一棵生成树 $T'$。因为 $(u,v)$ 的权值不大于 $(u',v')$，故 $T'$ 的权值和不大于 $T$，$T'$ 是包含 $(u,v)$ 的一棵最新小生成树。和假设矛盾。

![](https://resource.ethsonliu.com/image/20190928_03.png)

上述所说的 $(u',v')$ 即为 $u→y→z→v$ 中的某条边。

对于初学者来说，把上述的 MST 性质和 Prim 算法联系起来还是有点困难的。读者可以这样去理解，Prim 本质上就是利用了贪心思想，随机选取一顶点作为起点，加入到集合 $U$，接着找到与其关联的最小权值的边，该边上的另一个点也加入到 $U$，接着再从 $U$ 中的两个点出发继续向外找最小权值的边，找到后再加入第三个点，就这样重复下去。因为每次都是找最小值，所以当所有点都被加入 $U$ 时，最小生成树也就被确定了。

## 时间复杂度

Prim 算法和 [Dijkstra 算法](https://ethsonliu.com/2018/03/dijkstra.html)的时间复杂度一样，所以这里就不详细陈述了，附上一张表即可，其中 $m$ 为边数，$n$ 为顶点数。

|      最小边，权的数据结构      |   时间复杂度   |
| :----------------------------: | :------------: |
| 邻接矩阵，搜索（即本程序所用） |    $O(n^2)$    |
|         二叉堆，邻接表         | $O((m+n)logn)$ |
|       斐波那契堆，邻接表       |  $O(m+nlogn)$  |