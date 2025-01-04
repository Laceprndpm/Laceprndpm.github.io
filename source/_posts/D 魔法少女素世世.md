---
title: D 魔法少女素世世
cover: 
date: 2024-12-01
update: 2024-12-02
categories: 算法
tags:
  - DFS
  - DP
  - 二分
  - 拓扑排序
  - DAG
---
# 题目描述
“和我签订契约，成为魔法少女吧！” — Incubator
“只要是我能做的，我什么都愿意做！” — Soyorin
为了复活 CRYCHIC，素世与丘比签订契约成为了魔法少女，但代价是素世需要讨伐魔女以补充足够的魔力。 
现在素世正准备去讨伐迷途的魔女，迷途的魔女藏身在一个有 `n` 个房间的迷宫内，并且拥有 `n−1` 个分身。其中，魔女的本体位于编号为 `n` 的房间内，而魔女的分身分别位于编号 `1` 到 `n−1` 的房间。
这些房间之间由单向的通道连接，每个房间都有单向通往其它的房间的通道。其中，如果一个房间不能通过通道进入，则代表它可以直接通过迷宫入口进入，即你可以直接从这里开始讨伐。
由于迷途的魔女本身的特性，这个迷宫保证了，从每个房间出发都可以到达第 n 号房间，并且通道不会在房间之间成环。
每个魔女都有相应的魔力值，第 `i` 个房间的魔女的魔力值为 $a_i$ ​。当素世在房间内遇到魔女时，素世会与魔女展开战斗。
假设当前素世的魔力值为 x：
- 如果 x≥ $a_i$ ，素世可以轻松消灭魔女，并且素世的魔力值 xx 会增加 $a_i$​
- 否则，素世只能勉强消灭魔女，并且素世的魔力值 x 会减少 $a_i$−x。
在任何时刻，如果素世的魔力值 x≤0，那么素世就失败了。
因此素世想知道，要成功打败魔女本体，即消灭位于 n 号房间内的魔女，至少需要有多少魔力值才能进入迷宫内？
# 分析
## 1. 问题分析
有N个节点，每个节点除N节点外有其后继节点；除头节点外有前驱节点。
从头节点以`x`的值出发，每次进入一个新房间
- 如果 $x\geq a_i$ ，则 $x=a_i+x$
- 负责，x-=$(x-a_i)$
## 2. 数据结构
可以明显观察到，存在有DAG的结构
## 3. 算法设计
1. 可用算法
	关于DAG，与距离有关的的算法：拓扑排序，DFS/BFS，DP...etc
2. 当使用拓扑排序时，每个节点的所有前驱节点全部计算完毕，可以依次递推，最后判断
3. 当使用DFS时，可以从末节点出发，依次向前递推
# 代码
## 源码
### Version 1
代码长度：1278 运行时间： 652 ms 占用内存：9896K
#DP #拓扑排序 #二分
```cpp
#include <bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long

const int N = 1e5 + 5;
int deg[N], a[N], n, m;
vector<int> G[N];
int dp[N], d[N];
bool check(int val)
{
    queue<int> q;
    for (int i = 1; i <= n; i++)
    {
        if (deg[i] == 0)
        {
            dp[i] = val;
            q.push(i);
        }
        else
            dp[i] = 0;
        d[i] = deg[i]; // 拷贝入度
    }
    while (!q.empty()) // 基于Kahn算法的拓扑排序
    {
        int x = q.front();
        q.pop();
        int nxt = dp[x];
        if (nxt > 0)
        {
            if (nxt >= a[x])
                nxt += a[x];
            else
                nxt -= (a[x] - dp[x]);
        }

        for (auto y : G[x]) // 更新后继节点的dp值
        {
            if (--d[y] == 0)             // 入度为0的节点入队
                q.push(y);               // 入度为0的节点入队
            if (nxt > 0)                 // 更新后继节点的dp值
                dp[y] = max(dp[y], nxt); // 更新后继节点的dp值
        }
    }
    if (dp[n] > 0) // 是否可以到第n个节点
    {
        int nxt = dp[n]; // 到达第n个节点的dp值
        if (nxt < a[n])  // 如果战败后的dp值
            nxt -= (a[n] - dp[n]);
        return nxt > 0; // 返回是否可以最终胜利
    }
    else
        return false;
}

void solve()
{
    cin >> n >> m;
    for (int i = 1; i <= n; i++) // 读入数据ai
        cin >> a[i];
    for (int i = 1; i <= m; i++) // 读入数据u,v,deg为入度
    {
        int u, v;
        cin >> u >> v;
        G[u].emplace_back(v); // 建图
        deg[v]++;             // 计算入度
    }
    int l = 1, r = 1e18;
    while (l < r)
    {
        int mid = (l + r) >> 1;
        if (check(mid))
            r = mid;
        else
            l = mid + 1;
    }
    cout << l << endl;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
    int T = 1;
    //    cin>>T;
    while (T--)
    {
        solve();
    }
}
```
### Version2
代码长度：2212 运行时间： 637 ms 占用内存：22508K
#DFS #DAG #mine
```cpp
#include <bits/stdc++.h>
using namespace std;
int mo_li_zhi[100005];
#define int long long
int ans = LLONG_MAX;
 
int find(int molizhi, int lowest_cost) // 输入魔力值和 离开最低魔法 ，返回 进入最小
{
    int x;
    if ((lowest_cost + molizhi + 1) / 2 < molizhi) // 失败情况
    {
        x = (lowest_cost + molizhi + 1) / 2;
    }
    else // 胜利情况
    {
        x = max(lowest_cost - molizhi, molizhi);
    }
    return x;
}
 
class DirectedGraph
{
private:
    unordered_map<int, vector<int>> adjList; // 邻接表表示图
    unordered_map<int, int> visited;         // 记录节点访问状态
 
public:
    // 添加一个节点
    void addNode(int node)
    {
        if (adjList.find(node) == adjList.end())
        {
            adjList[node] = {};
        }
    }
 
    // 添加一条边从 node1 指向 node2
    void addEdge(int node1, int node2)
    {
        adjList[node1].push_back(node2);
    }
 
    // 获取一个节点的所有邻接节点
    vector<int> getNeighbors(int node)
    {
        return adjList[node];
    }
 
    // 深度优先搜索 (DFS)
    void dfs(int start)
    {
        dfsHelper(start, 1);
    }
 
    void dfsHelper(int node, int lowest_cost)
    {
        if (visited.find(node) != visited.end() && visited[node] < lowest_cost)
        {
            return;
        }
        else
        {
            visited[node] = lowest_cost;
        }
        int x = find(mo_li_zhi[node], lowest_cost);
        for (int neighbor : adjList[node])
        {
            {
                dfsHelper(neighbor, x);
            }
        }
        if (adjList[node].size() == 0)
        {
            ans = min(ans, x);
        }
    }
};
int n, m;
signed main()
{
    DirectedGraph graph;
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
    {
        cin >> mo_li_zhi[i];
        graph.addNode(i);
    }
    for (int i = 0; i < m; i++)
    {
        int u, v;
        cin >> u >> v;
        graph.addEdge(v, u);
    }
    graph.dfs(n);
    cout << ans << endl;
    return 0;
}
```
## 代码分析
### Version 1
#### 代码逻辑
1. 变量
	- `dp`用于动态规划，存储进入房间时的最大魔力值
	- `q`队列用于Kahn算法
	- `dge`存储原始入度；`d`存储拷贝，用于Kahn算法
	- `G[x]`为邻接表，存储`x`节点的全部后继节点
2. `solve()`函数
	 主解题函数，执行以下步骤：
	 - 输入数据
	 - 二分查找答案，并且调用`check()`
3. `check()`函数
	这个函数的作用是通过二分查找判断给定的 `val` 值是否能满足题目要求。
	1. 初始化：
		- 将所有入度为 0 的节点放入队列，设置它们的 `dp[]` 值为`val`。
		- 对于其他节点，`dp[]` 初始化为 0。
	2. 拓扑排序：
		- 基于Kahn算法的拓扑排序
		- 取出队列头x，算出到达下一个房间时的魔力值，更新`dp`
		- 对x的所有后继节点入度`-1`
		- 遍历所有节点入度，如果为0加入队列
		- 重复上述操作直到`q`空
	3. 判断以`dp[n]`是否还能胜利
#### 代码特色
- 二分
	- 采用二分算法保证了高效性
- kahn
	- 采用拓扑排序，保证进入每个房间时，前驱房间全部被计算完毕，与dp共同保证高效
- dp
	- 每次只取最大值，配合Kahn，可以确保每个房间只会运算一次，即进入房间时的魔力值最大时，确保`check()`函数总体时间复杂度为`O(N+M)`
### Version 2
#### 代码逻辑
1. 变量
	- `mo_li_zhi[]`：存储每个房间的魔力值。
	- `ans`：存储最终的最小魔力值，初始化为最大值（`LLONG_MAX`）。
	- `adjList`：邻接表，表示图的结构，每个房间的邻接节点（即指向的房间）。
	- `visited[]`：记录每个节点的访问状态和最低魔力值。
2. `find()` 函数
	- `find()` 函数的作用是根据当前房间的魔力值和最低要求的魔力值，计算进入下一个房间时所需的最小魔力值。
	-  输入：当前房间的魔力值 `molizhi` 和离开该房间时的最低魔力值 `lowest_cost`。
	-  输出：返回进入下一个房间时所需的最小魔力值。
3. `DirectedGraph` 类
	- 简易的有向图类
4. `solve()` 主解题函数
	`solve()` 函数是整个程序的主函数，负责输入数据、构建图、调用 DFS 进行计算并最终输出答案。
5. `dfsHelper()` 函数
	`dfsHelper()` 函数通过深度优先搜索遍历图中的节点，并根据当前房间的魔力值和最低魔力值递归地计算所需的最小魔力值。
	1. **检查是否访问过节点**：
	    - 如果当前房间已经被访问过，且新的最小花费比旧的要高，直接返回。
	    - 否则，记录当前房间的魔力值，并且进入。
	2. **计算进入下一个房间的魔力值**：
	    - 使用 `find()` 函数计算进入下一个房间时的最小魔力值。
	3. **递归访问邻接节点**：
	    - 遍历当前房间的所有后继房间（邻接节点），并递归调用 `dfsHelper()`。
	4. **更新最小魔力值**：
	    - 如果当前节点没有后继节点（即为终点房间），则更新最小魔力值 `ans`。
#### 代码特色
- DFS
	- DFS简化逻辑，从尾到头也只需要搜索一次，也就是不需要二分，用`visted`记录之前的最小花费来剪枝，减少计算量。
	- 虽然做了剪枝，但DFS还是不能保证效率；并且由于是反向的DFS，会存在先对一个较差的值进行递归后，又发现一个更好的值，需要重新递归。
# 反思
1. 是否可以结合`find()`的递推思路，与反向拓扑排序？这样就可以在计算每个节点最小魔法时，保证所有后继节点全部计算完毕，轻易取到正确的`lowest_cost`，性能将比二者都强。
	1. 数据结构：
		1. 邻接表G，队列q，入度deg
		2. 动态dp存储进入时每个节点需求的lowest_cost
	2. 算法：
		1. 拓扑排序
	3. 思路：
		1. 反向构建邻接表
		2. 对N号节点，加入队列与dp
		3. 开始拓扑排序
			1. 取出队列节点，遍历所有邻接节点，入度-1，并且借由`find()`更新dp
			2. 加入所有入度为0的节点
			3. 重复直到队列为空
			4. 过程中如果某节点的邻接表为空，则更新ans
		4. 输出ans
2. 个人没有掌握图论的有关知识，导致没有想到这样的方法。