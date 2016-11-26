---
title: RunningPhoton's Nightmare
date: 2016-05-11 1:45:00
tags: 最短路
categories:
- 川大重大联合赛
- 第一场联合赛
---

#### 题意
一个人站在S这个起始位置，想要走到一个迷宫出口E，但是现在这个人身上有一个炸弹在k时间内就会爆炸，同时到达一些R位置能够让炸弹计时器清零，问能否出迷宫，能并输出最短时间,其中迷宫大小不超过600*600，1<=k<=600，最多有100个清零器
<!-- more -->
#### 分析
1.在S起点距离k的位置内的所有的都是可达并且最优时间等于最短距离。然后再由这些已经确定是最短的并且炸弹时间已被清零的点来更新周围距离k的点（如果炸弹时间不清零，那么这个点所能够到达的最远距离只能够在S的范围之内...）,然后这就好比是dijstra算法，只是多了一个限制（只能由R点更新周围点）。

2.刚开始拿到这一题想的是从起点开始暴搜（受到华为那个寻路比赛影响），状态包含当前已经走过的时间和炸弹剩余的时间以及当前位置，但是这样的话什么样的状态才是最优的就不好确定了，如果k小的话倒是能够对每个位置的每一个炸弹时间k记录一个最优时间。但是k比较大...

#### 思考
1.上述算法最终只有S，E，R这三个点起作用了，那么现在就只看这三个点。如果这三类点距离小于k那么表示可以联通，也就是在原图的基础上面重新建了一个图。那就无脑跑100多次bfs求最短路，如果三类点距离d小于k则他们建一个d距离的边否则不能直接相连。

2.为了代码美观好写，用bfs求最短路的时候传入距离数组指针。

``` cpp
/*************************************************************************
	> File Name: e.cpp
	> Author: liangxianfeng
	> Mail:   641587852@qq.com
	> Created Time: 2016年05月04日 星期三 16时10分44秒
 ************************************************************************/

#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
#include<algorithm>
#include<stack>
#include<map>
#include<set>
#include<vector>
#include<queue>
#include<string>
#include<cmath>
using namespace std;
#define ll long long
#define rep(i,n) for(int i =0; i < n; ++i)
#define CLR(x) memset(x,0,sizeof x)
#define MEM(a,b) memset(a,b,sizeof a)
#define pr(x) cout << #x << " = " << x << " ";
#define prln(x) cout << #x << " = " << x <<  endl; 
const int maxn = 4e5+100;
const int INF = 0x3f3f3f3f;
int dx[] = {1, 0, -1, 0};
int dy[] = {0, 1, 0, -1};
char mp[610][610];
struct Node{
    int x, y;
    Node(){}
    bool operator == (const Node& rhs)const{
        return x== rhs.x&&y==rhs.y;
    }
    Node(int _x, int _y){
        x = _x; y = _y;
    }
};
int k, n, m;
Node node[155];
int dis[660][660], d[160][160];
int cnt;
bool bfs(int id){
    queue<Node> q;
    for(int i = 0; i <= n; ++i){
        for(int j = 0; j <= m; ++j){
            dis[i][j] = INF;
        }
    }
    q.push(node[id]);
    dis[node[id].y][node[id].x] = 0;
    while(q.size()){
        Node u = q.front(); q.pop();
        for(int i = 0; i < 4; ++i){
            int nx = u.x + dx[i];
            int ny = u.y + dy[i];
            if(nx < 0 || ny < 0 || nx >= m || ny >= n) continue;
            if(mp[ny][nx] == 'W') continue;
            if(dis[ny][nx] > dis[u.y][u.x] + 1){
                dis[ny][nx] = dis[u.y][u.x] + 1;
                q.push(Node(nx,ny));
            }
        }
    }
    for(int i = 0; i < cnt; ++i){
        if(dis[node[i].y][node[i].x] >= k) d[id][i] = INF;
        else d[id][i] = dis[node[i].y][node[i].x];
    }
}
int main(){
#ifdef LOCAL
	freopen("/home/zeroxf/桌面/in.txt","r",stdin);
	freopen("/home/zeroxf/桌面/out.txt","w",stdout);
#endif
    int t;
    scanf("%d", &t);
    int src, des;
    while(t--){
        scanf("%d%d%d", &n, &m, &k);
        cnt = 0;
        for(int i = 0; i < n; ++i){
            scanf("%s", mp[i]);
            for(int j = 0; j < m; ++j){
                if(mp[i][j] == 'S') src = cnt;
                if(mp[i][j] == 'E') des = cnt;
                if(mp[i][j] != 'W' && mp[i][j] != '.') node[cnt++] = Node(j,i);
            }
        }
        for(int i = 0; i < cnt; ++i) bfs(i);
        for(int k = 0; k < cnt; ++k){
            for(int i = 0; i < cnt; ++i){
                for(int j = 0; j < cnt; ++j){
                    if(d[i][k] + d[k][j] <= d[i][j]) d[i][j] = d[i][k] + d[k][j];
                }
            }
        }
        if(d[src][des] < INF) printf("%d\n", d[src][des]);
        else printf("Poor RunningPhoton!\n" );
        
    }
	return 0;
}

```