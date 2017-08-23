---
title: meixiuxiu学图论
date: 2016-06-7 10:33:50
tags: 最小生成树
categories:
- 川大重大联合赛
- 第三场联合赛
---


#### 题意

题目大意就是:问你这个图是否存在环,如果存在环,那么环的最小生成代价是多少
<!-- more -->
#### 分析
1.仿照最小生成树,但是一旦某一跳边的两个端点已经在一个并查集则答案就是当前边权值

#### 思考

1.刚开始并查集写搓了,没有x = find(x);直接p[x] = y;
```cpp
/*************************************************************************
	> File Name: f.cpp
	> Author: liangxianfeng
	> Mail:   641587852@qq.com
	> Created Time: 2016年05月29日 星期日 20时09分50秒
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
const int maxn = 2e6+100;
const int INF = 0x3f3f3f3f;
struct Edge{
    int u, v, c;
    Edge(){}
    Edge(int _u, int _v, int _c){
        u = _u;
        v = _v;
        c = _c;
    }
    bool operator < (const Edge& rhs)const{
        return c < rhs.c;
    }
}edge[maxn];
int edgenum;
bool vis[maxn];
int p[maxn], rank[maxn];
int t, n, m;
int find(int x){
    return p[x] = (x==p[x]?x:find(p[x]));
}
void init(){
    for(int i = 0; i <= n; ++i) p[i] = i, rank[i] = 1, vis[i] = false;
}
bool unite(int x, int y){
    x = find(x), y = find(y);
    if(x==y) return true;
    if(rank[x] > rank[y]) p[y] = x;
    else {
        p[x] = y;
        if(rank[x] == rank[y]) rank[y]++;
    }
    return false;
}
int main(){
#ifdef LOCAL
	freopen("/home/zeroxf/桌面/in.txt","r",stdin);
	//freopen("/home/zeroxf/桌面/out.txt","w",stdout);
#endif
    scanf("%d", &t);
    while(t--){
        scanf("%d%d", &n, &m);
        init();
        int ans = INF, u, v, c;
        edgenum = 0;
        for(int i = 0; i < m; ++i){
            scanf("%d%d%d", &u, &v, &c);
            edge[edgenum++] = Edge(u,v,c);
        }
        sort(edge, edge + edgenum);
        for(int i = 0; i < m; ++i){
            int u = edge[i].u, v = edge[i].v;
            if(unite(u,v)) {
                ans = edge[i].c;
                break;
            }
        }
        if(ans == INF) printf("No solution!\n");
        else printf("%d\n",ans);
    }
	return 0;
}
```

