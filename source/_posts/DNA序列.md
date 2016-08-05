---
title: DNA序列
date: 2016-05-11 3:18:00
tags: 状压 搜索
categories:
- 川大重大联合赛
- 第一场联合赛
---


#### 题意
 - 就是让你找这样一个字符串，让所给的串是目标串的子串，那么目标串最短应该是多少 （最多8个串，每个串长度最大为5）
<!-- more -->
#### 分析

1. 这个题我只用了迭代加深搜索，但是没剪枝过了，数据没卡我（AAAA GGGG CCCCC TTTTT就T了）。后面想了一下状态压缩记忆化搜索是可以在有限时间搜索出来的。用8个六进制的数字表示每一个串的当前位置。然后这个状态只有四种转移方向ATGC，然后从最终状态往前记忆化搜索就好了。

2. 搜索一直没想出来怎么剪枝...,所以就使用迭代加深思想，然后暴力的枚举8个队列串首字符，比如枚举当前字符为A，那么谁队首是A的就POP出来，如果没有队首是A那就没必要搜索了。

#### 思考
1. 没啥坑点，就是不好好剪枝你会T在上面那个样例

2. 在用状态压缩转移的时候，要用记忆化从最后搜，因为最初状态往后转移的话，初状态不好枚举。

``` cpp
/*************************************************************************
	> File Name: hh.cpp
	> Author: liangxianfeng
	> Mail:   641587852@qq.com
	> Created Time: 2016年05月10日 星期二 09时27分41秒
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
int len[10];
int pos[10];
char ch[5] = "ATGC";
char s[10][10];
int maxdepth, n;
bool dfs(int depth, int *pp) {
    if(depth > maxdepth) return false;
    int p[9];
    int sum = 0;
    for(int i = 0; i < n; ++i) p[i] = pp[i], sum+=len[i]-pp[i];
    if(sum==0) return true;
    for(int i = 0; i < 4; ++i){
        sum = 0;
        for(int j = 0; j < n; ++j){
            p[j] = pp[j];
            while(p[j] < len[j] && s[j][p[j]] == ch[i]) 
            {
                p[j]++,sum++;break;
            }
        }
        if(sum) if(dfs(depth+1, p)){
            return true;
        }
    }
    return false;
}
int main(){
#ifdef LOCAL
	freopen("/home/zeroxf/桌面/in.txt","r",stdin);
	//freopen("/home/zeroxf/桌面/out.txt","w",stdout);
#endif
    int t;
    scanf("%d", &t);
    while(t--){
        scanf("%d", &n);
        maxdepth = 1;
        for(int i = 0; i < n; ++i){
            scanf("%s", s[i]);
            len[i] = strlen(s[i]);
            if(len[i] > maxdepth) maxdepth = len[i];
        }
        for(; ; maxdepth++){
            for(int i = 0; i < n; ++i) pos[i] = 0;
            if(dfs(0,pos)) break;
        }
        printf("%d\n", maxdepth);
    }
	return 0;
}

```