---
title: Goozy搭积木
date: 2016-05-23 23:55:50
tags: [dp, dp差值状态]
categories:
- 川大重大联合赛
- 第二场联合赛
---


#### 题意
-	goozy对积木十分的狂热，今天他想搭一个双子塔（就是两个高度一样的塔）！他想知道，用现有的积木，能不能实现这个想法.积木个数n(1 <= n <= 50)，每个积木的高度hi(1 <= hi <= 500000),题目保证所有积木高度总和不超过500000。
<!-- more -->
#### 分析

- 建立状态
	
	这题和能不能把所有的平分是不一样的,因而我只要保证有一种方案把积木部分平分就好了
	
	所以建立状态不能用dp[i][j]表示i木块j高度是否可达
	
	状态是dp[i][j]表示前i个木块使得两份木块高度差为j的最大可达高度是多少.
	
-	状态转移

	状态转移就很简单了,积木i放到比较高的那一方或者比较低的那一方
		
		放到比较高的一方
		
		dp[now][j+h] = max(dp[now][j+h],dp[1-now][j]+h);
		
		放到比较低的一方
		
		newh = max(dp[1-now][j]-j+h, dp[1-now][j]);
		
		dp[now][abs(h-j)] = max(dp[now][abs(h-j)], newh);
		
#### 思考
	
-	有时候dp建立状态后发现某一位范围过大,不能直接dp[i][j]的时候可以考虑特殊性质,然后对他们的差进行状态转移,正如D题一样


``` cpp
/*************************************************************************
	> File Name: e.cpp
	> Author: liangxianfeng
	> Mail:   641587852@qq.com
	> Created Time: 2016年05月14日 星期六 03时07分49秒
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
int dp[2][250010];
int main(){
#ifdef LOCAL
	freopen("/home/zeroxf/桌面/in.txt","r",stdin);
//	freopen("/home/zeroxf/桌面/out.txt","w",stdout);
#endif
    int t, n, h;
    scanf("%d", &t);
    while(t--){
        scanf("%d", &n);
        for(int i = 0; i < 250010; ++i) dp[0][i] = dp[1][i] = -INF;
        int now = 1;
        dp[now][0] = 0;
        for(int i = 0; i < n; ++i){
            scanf("%d", &h);
            now = now^1;
            for(int j = 0; j < 250010; ++j){
                dp[now][j] = max(dp[now][j], dp[1-now][j]);
                if(dp[1-now][j] < j) continue;
                if(h>=250010) continue;
                if(j+h<250010) dp[now][j+h] = max(dp[now][j+h],dp[1-now][j]+h);
                dp[now][abs(h-j)] = max(dp[now][abs(h-j)], max(dp[1-now][j]-j+h, dp[1-now][j]));
//                pr(abs(h-j));pr(h);pr(j);prln(dp[now][abs(h-j)]);
            }
        }
        int ans = dp[now][0];
        if(ans > 0) printf("%d\n",ans);
        else printf("GG\n");
    }
	return 0;
}
```
	
