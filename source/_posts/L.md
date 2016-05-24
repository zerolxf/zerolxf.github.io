---
title: 来签个到吧
date: 2016-05-24 10:11:50
tags: 期望dp
---


### L 来签个到吧

#### 题意

-	给你 n (2 <= n <= 60,000) 个球,每球上都写有互不相同的数字t ( 0 <= t < 100,000),这些球放在一个盒子里.开始你能执行一种加球操作:选择任意两个球: x 和y,然后看| x - y |在已经有的球中是否存在,如果不存在,就把|x - y|写在一个球上,把这个球加入这个盒子,这里就成功完成了一次加球操作. 
你就一直加球,直到盒子中任意两个数的差,在集合中已经存在

	现在摸求,问你摸完所有球的所进行操作次数的期望
	
	
#### 分析

-	**最终状态会有多少个球?**
		
	显然加球操作很像辗转相减的过程,我们只需要对这题求最大公约数,然后最大值除以这个最大公约数就是最终的个数
	
-	**期望怎么求?**
	
	如果当前摸过了i个球,还剩下n-i个就没有摸,那么摸到i+1球的期望是多少?
	
	-	可以用无穷级数来求
	
	-	可以感性认识一下,n 个球我要摸到n-i球中的一个期望是多少,就是n / (n-i)
	
	所以最后ans = sum(n/i) + 加球操作数

#### 思考

-	这题要注意对0的处理
-	这题实际不难,但是读错题意,以为操作只包括摸球


```
/*************************************************************************
	> File Name: l.cpp
	> Author: liangxianfeng
	> Mail:   641587852@qq.com
	> Created Time: 2016年05月14日 星期六 02时01分00秒
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
int a[maxn];
int main(){
#ifdef LOCAL
	freopen("/home/zeroxf/桌面/in.txt","r",stdin);
	//freopen("/home/zeroxf/桌面/out.txt","w",stdout);
#endif
    int t,n;
    scanf("%d", &t);
    while(t--){
        scanf("%d", &n);
        double ans = 0;
        int b = 0, x;
        for(int i = 0; i < n; ++i){
            scanf("%d", &a[i]);
            if(a[i] > b) b = a[i];
        }
        x = b;
        bool ok = true;
        for(int i = 0; i < n; ++i){
            if(a[i] == 0) {
                ok = false;
                continue;
            }
            x = __gcd(x, a[i]);
        }
        int num = b/x;
        if(!ok) num++;
        ans = num-n;
        for(int i = 1; i <= num; ++i){
            ans = ans +num*1.0/i;
        }
        int temp = ans;
        printf("%d\n",temp);
    }
	return 0;
}
```