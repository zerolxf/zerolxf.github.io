---
title: Taosama和煎饼
date: 2016-05-23 11:01:50
tags: -dp -记忆化搜索
---

### 	C  Taosama和煎饼

#### 题意

-	韬韬想吃煎饼但是有急事,一个煎饼有n个工序,每个工序i有着A[i]美味度,韬韬有m个道具,每个道具用一次可以前进b[i]个单位,每个工序一旦被跳过得不到美味度,问你使用这些工具最多获得多少美味度*(0<=b[i]<=4,0<=a[i]<=100,0<=n<=350,0<=m<=120)*,确保b[i]的和是n-1

#### 分析

-	建立状态

	dp[i][a][b][c][d]表示处于i位置还剩abcd个四种道具(a表示前进b[1]个道具)最多能够获得多少美味度
	
	状态转移
	
	dp[i][a][b][c][d]可以由dp[i-1][a-1][b][c][d]等四种状态转移过来
	
-	状态优化

	上面的状态很容易想,但是350 * 40 * 40 * 40 * 40复杂度很高,我们可以发现,这五个变量只要确定四个,那么第五个变量是确定的,所以我们可以减少一维,dp[a][b][c][d]就好了,复杂度40 * 40 * 40 * 40完美过题
	
-	超时原因

	由于建立状态思维定式,所以建立的状态是dp[350][40][40][40],超时了,但仔细发现这里的350有很多状态达不到
	
#### 思考

-	技巧
	
	-	对于这种固定形式的转移,记忆化搜索写起来最快
	-	建立状态的时候如果有多种选择首先找范围小的,其次找容易实现的
	
```
/*************************************************************************
	> File Name: c.cpp
	> Author: liangxianfeng
	> Mail:   641587852@qq.com
	> Created Time: 2016年05月13日 星期五 23时22分57秒
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
int n, dp[41][41][41][41], cnt[41][41][41][41];
int num[400];
int kase;
int dfs(int a, int b, int c, int d){
    int &ans = dp[a][b][c][d];
    if(ans >= 0 && cnt[a][b][c][d] == kase) return ans;
    ans = 0;
    if(a+b+c+d==0) return ans=num[n];
    int temp;
    if(a >= 1){
        temp = dfs(a-1, b, c, d);
        if(temp > ans) ans = temp;
    };
    if(b >= 1) {
        temp = dfs(a, b-1, c, d);
        if(temp > ans) ans = temp;
    }
    if(c >= 1){
        temp = dfs(a, b, c-1, d);
        if(temp > ans) ans = temp;
    }
    if(d >= 1) {
        temp =  dfs(a, b, c, d-1);
        if(temp > ans) ans = temp;
    }
    ans += num[n-a-b*2-c*3-d*4];
    //pr(a);pr(b);pr(c);prln(d);
  //  prln(ans);
    cnt[a][b][c][d] = kase;
    return ans;
}
int e[5];
int main(){
#ifdef LOCAL
	freopen("/home/zeroxf/桌面/in.txt","r",stdin);
//	freopen("/home/zeroxf/桌面/out.txt","w",stdout);
#endif
    int t, x, m;
    scanf("%d", &t);
    while(t--){
        scanf("%d%d", &n, &m);
        kase++;
        memset(e,0,sizeof e);
        for(int i = 1; i <= n; ++i){
            scanf("%d", &num[i]);
        }
        for(int i = 0; i < m; ++i){
            scanf("%d", &x);
            e[x]++;
        }
        int ans = dfs(e[1],e[2],e[3],e[4]);
        printf("%d\n",ans);
    }
	return 0;
}

```