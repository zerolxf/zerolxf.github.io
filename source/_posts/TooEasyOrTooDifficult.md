---
title: TooEasy Or TooDifficult
date: 2016-05-11 3:49:00
tags: [manacher, 异或]
categories:
- 川大重大联合赛
- 第一场联合赛
---


#### 题意

- 给你定义两类值，P[i]表示以第i个字符为中心的最长回文串长度，MZ和max{P[i]}有关，FJD为连续若干个P[i]异或和的最大值。问你MZ和FJD谁大
<!-- more -->
#### 分析
1. 解决MZ就是最裸的manacher算法，FJD则利用到一个性质sum[j]^sum[i] = p[i]^...P[j];这样转换之后就是求最大的sum[j]^sum[i],这个问题在问题A中谈论过了，就是建立01二叉树，然后把sum[i]全部插入，再全部走一遍

2. 当时生成插入01树的时候写挫了，一直死循环，debug半天

#### 思考
1. 没啥坑点

2. 常规题

```cpp
/*************************************************************************
	> File Name: j.cpp
	> Author: liangxianfeng
	> Mail:   641587852@qq.com
	> Created Time: 2016年05月07日 星期六 20时19分58秒
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
const int maxn = 4e6+100;
const int INF = 0x3f3f3f3f;
int p[maxn];
char s[maxn],  ss[maxn];
void manacher(int n) {
    int len = 2*n+2;
    ss[0] = '$';
    ss[1] = '#';
    for(int i = 0; i < n; ++i) {
        ss[i*2+2] = s[i];
        ss[i*2+3] = '#';
    }
    ss[n*2+2] = 0;
    int id, ma = 0;
    for(int i = 1; i < len; ++i) {
        if(i < ma) {
            p[i] = min(p[id*2-i], ma-i);
        } else {
            p[i] = 1;
        }
        for(;ss[i+p[i]] == ss[i-p[i]]; p[i]++);
        if(i+ p[i] > ma) {
            id = i;
            ma = i+p[i];
        }
    }
}
int sum[maxn];
void getsum(int n) {
    sum[0] = p[2]-1;
    for(int i = 1; i < n; ++i){
        sum[i] = sum[i-1]^(p[i*2+2]-1);
    //    pr(i);pr(sum[i-1]);pr(p[i*2+2]-1);prln(sum[i]);
    }
}
int ch[maxn][2];
int sz;
void add(int x, int rt, int pos) {
    if(rt == 0) return;
    int son = (x>>(rt-1))&1;
    if(ch[pos][son] == -1) ch[pos][son] = ++sz;
//    pr(rt);pr(ch[pos][son]);pr(pos);prln(son);
    add(x,rt-1,ch[pos][son]);
}
ll query(int x, int y, int rt, int pos) {
    if(rt == 0) return y;
    //pr(y);pr(pos);prln(rt);
    int son = (x>>(rt-1))&1;
    //pr(son);
    if(ch[pos][!son] != -1) return query(x,y<<1|1, rt-1, ch[pos][!son]);
    //prln(ch[pos][son]);
    return query(x, y << 1, rt-1, ch[pos][son]);
}
ll getfjd(int n){
    getsum(n);
    sz = 0;
    //memset(ch,-1,sizeof ch);
    for(int i = 0; i < n; ++i){
        add(sum[i], 31, 0);
    }
    ll ans = 0;
    for(int i = 0; i < n; ++i){
        ans = max(ans, query(sum[i], 0, 31, 0));
        //pr(sum[i]);
        //pr(i);prln(ans);
    }
    for(int i = 0; i <= sz; ++i) ch[i][0] = ch[i][1] = -1;
    return ans;
}

int mod;
ll ret(ll x, ll y) {
    ll ans = 1;
    while(y) {
        if(y&1) ans=ans*x%mod;
        x=x*x%mod;
        y = y/2;
    }
    return ans;
}
int main(){
#ifdef LOCAL
	freopen("/home/zeroxf/桌面/in.txt","r",stdin);
	//freopen("/home/zeroxf/桌面/out.txt","w",stdout);
#endif
    int t;
    scanf("%d", &t);
    while(t--){
    memset(ch,-1,sizeof ch);
        scanf("%s", s);
        ll len = strlen(s);
        manacher(len);
        ll ans = getfjd(len);
        int ma = 1;
        for(int i = 0; i < len; ++i) if(p[i*2+2]-1>ma) ma=p[i*2+2]-1;
        mod = ma/3*5+1;
        ll mz = ret(ma,len*len*len) + ma*4/5;
        if(mz > ans) printf("liujc\n");
        else printf("luoxinchen\n");
    }
	return 0;
}

```