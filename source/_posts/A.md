---
title: 雷神之路
date: 2016-05-23 22:01:50
tags: -dp -矩阵优化
---


### A 雷神之路

#### 题意

在一个坐标轴上面起始位置是0，你可以往右走一步，两步，三步。其中某些位置不能走，问你走到位置n有多少种方法（1<= n <= 1e18）

#### 分析
- 这题是走楼梯的进阶版，状态很好想dp[n]，转移有三种：走一步两步三步转移过来。不过由于n太大，很容易想到矩阵加速。
- 用矩阵A表示第i个可走，矩阵B表示第i个不可走。有些地方x不能走，那么我只要用矩阵加速求出第x-1个矩阵C，那么第x个矩阵就是C*B，最后得出第n个矩阵

#### 思考

- dp[n] = dp[n-1] + dp[n-2] + dp[n-3] (n>=3)所以我们第一个矩阵是从2开始的，小于2的方案要手动打出来
 
		
``` 
/*************************************************************************
	> File Name: a.cpp
	> Author: liangxianfeng
	> Mail:   641587852@qq.com
	> Created Time: 2016年05月13日 星期五 18时57分55秒
 ************************************************************************/

#include <iostream>
#include <cstdio>
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
const int MOD = 1e9+7;
struct Rec{
    ll num[3][3];
    Rec(){
        for(int i = 0; i < 3; ++i){
            for(int j = 0; j < 3; ++j){
                if(i==j) num[i][j] = 1;
                else num[i][j] = 0;
            }
        }
    }
    Rec(int x){
        for(int i = 0; i < 3; ++i){
            for(int j = 0; j < 3; ++j){
                num[i][j] = x;
            }
        }
    }
    Rec operator * (const Rec& rhs)const{
        Rec ans(0);
        for(int i = 0; i < 3; ++i){
            for(int j = 0; j < 3; ++j){
                for(int k = 0; k < 3; ++k){
                    ans.num[i][j] += num[i][k]*rhs.num[k][j]; 
                }
                ans.num[i][j] %= MOD;
            }
        }
        return ans;
    }
}init;
Rec ret(const Rec &x, ll y){
    Rec ans, base = x;
    while(y){
        if(y&1) ans = ans*base;
        y>>=1;
        base = base*base;
    }
    return ans;
}
bool vis[10];
ll x[600];
int main(){
#ifdef LOCAL
	freopen("/home/zeroxf/桌面/in.txt","r",stdin);
	//freopen("/home/zeroxf/桌面/out.txt","w",stdout);
#endif
    int t;
    scanf("%d", &t);
    while(t--){
        int  m;
        ll n;
        scanf("%lld%d", &n, &m);
        memset(x, 0, sizeof x);
        memset(vis, 0, sizeof vis);
        for(int i = 1; i <= m; ++i) scanf("%lld", &x[i]);
        sort(x+1, x+m+1);
        if(x[1] < 3) vis[x[1]] = true;
        if(x[2] < 3) vis[x[2]] = true;
        ll ans1, ans2;
        if(vis[1]) ans1 = 0;
        else ans1 = 1;
        if(vis[2]) ans2 = 0;
        else ans2 = ans1 + 1;
        if(n <= 2) {
            if(n==1) printf("%lld\n", ans1);
            else printf("%lld\n", ans2);
            continue;
        }
        Rec a(1);
        Rec b(0);
        a.num[0][2] = 0;
        a.num[1][1] = 0;
        a.num[2][1] = 0;
        a.num[2][2] = 0;
        b.num[0][1] = 1;
        b.num[1][2] = 1;
        Rec ans;
        ll last = 2;
        for(int i = 1; i <= m; ++i){
            if(x[i] <= 2) continue;
            ans = ans*ret(a,x[i]-last-1);
            ans = ans*b;
            last = x[i];
        }
        ans = ans*ret(a,n-last);
        ll c = ans2*ans.num[0][0] + ans1*ans.num[1][0] + ans.num[2][0];
        c = c%MOD;
        printf("%lld\n", c);
    }
	return 0;
}
```
