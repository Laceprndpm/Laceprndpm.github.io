---
title: M GLLF砍木棍
cover: https://bu.dusays.com/2023/05/13/645fa3cf90d70.webp
date: 2024-12-02
update: 
categories: 算法
tags:
  - DP
---
# 题目描述

上回 GLLF 对一个木头砍了 10^{100} 刀，他感觉有点累了，所以他决定砍一根木棍。
他有一根长度为正整数 n 的木棍，把它砍成了若干个长度为正整数的小段，他很好奇有多少种砍法能使得这些小段木棍能拼成一个凸多边形。
两种砍法不同当且仅当存在砍的位置不同。我们把每段的长度按顺序放入数组，如长度为 6 的木棍按顺序砍出 1,2,1,2 四段，则可以表示为 [1,2,1,2]。两个不同的数组代表不同的砍法。
由于这个问题的答案可能很大，请你将答案对 10^9 + 7 取模之后输出。
# 分析
原思路：转换成对长度为m的01数组，不允许连续n个0，问有多少种数组？
其实我的思路就是对的，但对于动态规划问题，可以写出递推式，所以效率不够
思路：
- `dp[i]`存储了对于长度为`i`的序列，有多少种不允许`N`个连续0的方式
- 如果第`i`位是1，只需要直接加上`dp[i-1]`
- 如果下一位是0，考虑连续1个，2个，3个...m-1个0的情况，加上`dp[i-2]+dp[i-3]+...+dp[i-m]`
- 或者考虑`dp[i-1]`-Case(假如算上这位，连续0的长度为m的情况)，得到`2*dp[i]-dp[i-m-1]`
- 如果某一位突然不存在了，分类讨论比较棘手，~~比如不能连续为2个0，第一个位可以是1,0，1可以直接加上，0需要考虑上上一位不存在的情况~~，那么可以先初始化前m-1位，这样只需要考虑递推就行，前m-1位`dp[i]=pow(2,i)`即可
- 最后对于递推式`dp[i]=2*dp[i-1]-dp[i-m-1]`，绝大部分情况下`dp[i-m-1]`为`pow(2,i-m-1)`，尝试对其化简
$i\geq m$ && $i-m-1\leq m-1$
`i>=m && i<=2m`
`dp[i] = 2^i-(1+0.5*(i-m)) * 2^(i-m)`
`dp[2m] = 2^(2m)-(1+0.5*(2m-m)) * 2^(2m-m)`
`dp[2m] = 2^(2m)-(1+0.5*m) * 2^m`
# 代码
## 源码
### Version 1
代码长度：1168 运行时间： 37 ms 占用内存：2032K
#DP 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define endl '\n'
#define int long long

const int N=50+5;
const int mod=1e9+7;
int dp[N][N];
int qpow(int a,int b){
    int ans=1;
    while(b){
        if(b&1)ans=ans*a%mod;
        b>>=1;
        a=a*a%mod;
    }
    return ans;
}
void solve(){
    int n;
    cin>>n;
    dp[0][0]=1;
    int t=(n+1)/2;
    int val=qpow(4,t-1)-(t+1)*qpow(2,t-2)%mod;
    if(n%2==1)cout<<(val%mod+mod)%mod<<endl;
    else{
        val=val*2-qpow(2,t-1)+1;
        val=(val%mod+mod)%mod;
        cout<<val<<endl;
    }
//    4^(n-1) - (n+1)*2^(n-2)
//    for(int i=1;i<=n;i++){
//        for(int j=1;j<=n;j++){
//            dp[i][j]=0;
//            for(int k=1;k<=min(j,(n-1)/2);k++){
//                dp[i][j]+=dp[i-1][j-k];
//            }
//        }
//    }
//    int ans=0;
//    for(int i=1;i<=n;i++){
//        ans+=dp[i][n];
//    }
//    cout<<t<<' '<<2*val-ans<<endl;
}

signed main(){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
    int T=1;
    cin>>T;
    while(T--){
        solve();
    }
}
```
### Version 2-a
#mine #DP 
```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
const int mod = 1e9 + 7;
int qpow(int a, int b)
{
    int ans = 1;
    while (b)
    {
        if (b & 1)
            ans = ans * a % mod;
        b >>= 1;
        a = a * a % mod;
    }
    return ans;
}
int count_sequences(int x, int m)
{
    // dp[i] 表示长度为 i 的合法二进制序列数量
    vector<int> dp(x + 1, 0);
    dp[0] = 1; // 长度为 0 的序列只有 1 种（空序列）
               // 如果下一位是1，那么之前所有情况都可以直接加1
               // 如果下一位是0，dp[i-1]-（假如算上这位，连续0的长度为m的情况）,i,i-1,i-2,...,i-m+1均为0,j-m为1
               // 假如算上这位，连续0长度位m的情况:dp[i-m-1]

    // 尝试：改变状态转移方程
    // 如果下一位是0，考虑连续1个，2个，3个...m-1个0的情况，dp[i-2]+dp[i-3]+...+dp[i-m]
    // 如果某一位突然不存在了，将会引起问题，比如不能连续为2个0，第一个位可以是1,0，1可以直接加上，0需要考虑上上一位不存在的情况，那么可以先初始化前m-1位
    // 证明有效

    for (int i = 1; i <= m - 1; ++i)
    {
        dp[i] = qpow(2, i);
    }
    dp[m] = qpow(2, m) - 1;

    // dp[i] = 2^i-(1+0.5*(i-m)) * 2^(i-m)
    // dp[2m] = 2^(2m)-(1+0.5*(2m-m)) * 2^(2m-m)
    // dp[2m] = 2^(2m)-(1+0.5*m) * 2^m
    dp[2 * m] = (qpow(4, m) - (2 + m) * qpow(2, m - 1)) % mod;
    dp[2 * m + 1] = (2 * dp[2 * m] - dp[m]) % mod;
    return dp[x];
}
int func(int n) // 长度为n的木棍的所有可能性
{
    int m = n - 1; // 可以切的地方的总数
    int l = m / 2; // 一半的长度，所包含的切的地方的总数
    return count_sequences(m, l);
}
// 0 1 1
// 1 1 0
// 0 1 0
// 1 1 1
// 1 0 1
signed main()
{
    int n;
    int input;
    int ans;
    scanf("%lld", &n);
    while (n--)
    {
        scanf("%lld", &input);
        ans = func(input);
        if (ans < 0)
            ans += mod;
        printf("%lld\n", ans);
    }
    return 0;
}
```
### Version 2-b
代码长度：978 运行时间： 122 ms 占用内存：2332K
#mine #DP 
```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
const int mod = 1e9 + 7;
int qpow(int a, int b)
{
    int ans = 1;
    while (b)
    {
        if (b & 1)
            ans = ans * a % mod;
        b >>= 1;
        a = a * a % mod;
    }
    return ans;
}
int count_sequences(int x, int m)
{
    // dp[i] 表示长度为 i 的合法二进制序列数量
    unordered_map<int, int> dp;
    dp[m] = qpow(2, m) - 1;
    dp[2 * m] = (qpow(4, m) - (2 + m) * qpow(2, m - 1)) % mod;
    dp[2 * m + 1] = (2 * dp[2 * m] - dp[m]) % mod;
    return dp[x];
}
int func(int n) // 长度为n的木棍的所有可能性
{
    int m = n - 1; // 可以切的地方的总数
    int l = m / 2; // 一半的长度，所包含的切的地方的总数
    return count_sequences(m, l);
}
// 0 1 1
// 1 1 0
// 0 1 0
// 1 1 1
// 1 0 1
signed main()
{
    int n;
    int input;
    int ans;
    scanf("%lld", &n);
    while (n--)
    {
        scanf("%lld", &input);
        ans = func(input);
        if (ans < 0)
            ans += mod;
        printf("%lld\n", ans);
    }
    return 0;
}
```
## 代码分析
注意：Version 2-a为一开始写的，由于多次增删改，导致冗余较多，无法过关，Version 2-b
### 代码逻辑
核心在于先写出dp后，再转化为数学计算
### 代码特色
DP->数学
# 反思
凡是 #DP ，并且效率不够的都需要考虑能不能从数学上优化