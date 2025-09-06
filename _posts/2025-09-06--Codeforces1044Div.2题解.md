---
layout: post
date: 2025-09-06
title: Codeforces1044Div.2题解
math: true
toc: true
tags: [About Code] 
---

# Codeforces1044Div.2题解

## A.Redstone?

### 题目回顾

史蒂夫偶然发现了一组 $n$ 齿轮，其中 $i$ 齿轮有 $a_i$ 个齿，他想把它们排成一行。

排列好后，史蒂夫将以每秒 $1$ 转的速度转动最左边的齿轮。对于其他每个齿轮，设 $x$ 为其齿数， $y$为其左边齿轮的齿数， $z$ 为其左边齿轮的旋转速度。那么，它的转速就是 $\frac{y}{x} \cdot z$ 转/秒。

如果最右边的齿轮以每秒 $1$ 转的速度旋转，史蒂夫就会认为这个装置令人满意。请判断史蒂夫能否将齿轮重新排列成一个令人满意的装置。

### 题解

A题作为签到题确实没什么好说的,对于最后的速度，我们可以很容易推出如下式子：
$$
V_n =\frac{a_1}{a_2} \cdot \frac{a_2}{a_3} \cdot \frac{a_3}{a_4}...\cdot \frac{a_{n-1}}{a_n}
$$
期中$V_n$表示最后的速度，经过约分，得到:
$$
V_n = \frac{a_1}{a_n}
$$
也就是说，我们要想最后速度为1，就是上第一个和最后一个齿轮数相同，就是看所有齿轮中有没有两个齿轮相同的即可。

代码如下：

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
#define Inf 0x3f3f3f3f
void solve(){
    int n;
    cin>>n;
    vector <int> a(n);
    for(int i=0;i<n;i++){
        cin>>a[i];
    }
    sort(a.begin(),a.end());
    for(int i=1;i<n;i++){
        if(a[i-1]==a[i]){
            cout<<"YES"<<endl;
            return;
        }
    }
    cout<<"NO"<<endl;

}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int t;
    cin>>t;
    while(t--){
        solve();
    }
    return 0;
}
```

## B. Villagers

### 题目回顾

史蒂夫和 $n$个 其他村民住在一个村子里。不幸的是，由于绿宝石的分配纠纷，这些村民中没有一个人与其他村民是朋友。此外，村民 $i$ 最初的脾气暴躁程度为 $g_i$ 。

史蒂夫可以多次执行以下操作：

- 选择两个村民 $i$ 和 $j$ ，给他们 $\text{max}(g_i,g_j)$ 绿宝石让他们分享。两个人的暴躁情绪都会降低$\text{min}(g_i,g_j)$，如果他们还没有成为朋友的话，他们也会成为朋友。

史蒂夫希望每个村民都能与其他村民成为朋友(可能通过一些中间友谊)；也就是说，从任何一个村民出发，你都可以沿着友谊之路到达其他任何一个村民。由于他不想让村里的经济过于膨胀，请计算他必须赠送的绿宝石的最低数量。//这是什么《我的世界》场（

### 题解

这个题面，非常具有暗示性的提示图论方向的解决方案，他确实也是一个连通图问题。所以我自然的最开始想用dsu来解决这个问题，实际上并不需要，它要简单的多。

我们先模拟一下，我们发现无论怎么链接，最大的那个$g_i$肯定需要付出的，我们令这个村民为$x$，因为只要想让$x$搭上一座桥，那么一定要付出$g_x$的代价，这个在什么策略中都是不可避免的。因此，付出了这么多代价，我们要想获得更好的结果，就是让他与暴躁程度第二大的村名交朋友，这样第二大的村名暴躁程度就会变成0。为什么这么选呢，因为在题目的意义下，每次交朋友必然有一个村名变成0，选剩下的最大的那个就是最好的决策。

所以，最后我们的决策就是，动态的把目前第一和第二交朋友，这样交朋友后，获得若干个两个节点的连通块，再通过两个0暴躁程度的村名来链接所有，这是偶数个村名的情况，奇数个的话也就是最后一个和一个暴躁程度为0的交朋友。

代码如下：

```
#include <bits/stdc++.h>
using namespace std;

// 用于比较常见的 输出"YES"和"NO"题目
#define returnNo return void(puts("No"))
#define returnYes return void(puts("Yes"))
//

// 用于 debug函数,只会在本地编辑器才显示
template <typename A, typename B>
ostream &operator<<(ostream &os, const pair<A, B> &p) { return os << '(' << p.first << ", " << p.second << ')'; }
template <typename T_container, typename T = typename enable_if<!is_same<T_container, string>::value, typename T_container::value_type>::type>
ostream &operator<<(ostream &os, const T_container &v)
{
    os << '[';
    string sep;
    for (const T &x : v)
        os << sep << x, sep = ", ";
    return os << ']';
}

void debug_out() { cerr << endl; }
template <typename Head, typename... Tail>
void debug_out(Head H, Tail... T)
{
    cerr << ' ' << H;
    debug_out(T...);
}
#ifdef LOCAL
#define debug(...) cerr << "[" << #__VA_ARGS__ << "]:", debug_out(__VA_ARGS__)
#else
#define debug(...)
#endif
//

// 常用头
#define int long long
#define endl '\n'
constexpr int N = 2e5 + 10;

void solve()
{
    int n;
    cin >> n;
    vector<int> g(n + 1);
    for (int i = 1; i <= n; i++)
        cin >> g[i];
    sort(g.begin() + 1, g.begin() + 1 + n);
    int ans = 0;
    for (int i = n; i >= 1; i -= 2)
        ans += g[i];
    cout << ans << '\n';
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int _ = 1;
    cin >> _;
    while (_--)
        solve();
}
```

## C. The Nether

### 题目回顾

这是一道交互题。

史蒂夫最近发现了 "地狱"，他在自己的世界里建立了一个由 $n$ 个地狱传送门组成的网络，每个传送门都位于不同的位置。

每个传送门都与一定数量(可能为零)的其他传送门单向相连。为了避免迷失方向，史蒂夫小心翼翼地构建了这个传送门网络，这样就不会有任何一连串的传送门跳跃会把你从一个地点带回到它本身；从形式上看，这个网络构成了一个有向无环图。

史蒂夫拒绝告诉你哪些入口与哪些入口相连，但他允许你提出查询。在每个查询中，你都要给史蒂夫一组位置$S = \{s_1, s_2, \ldots, s_k\}$ 和一个起始位置 $x \in S$ 。史蒂夫会找出一条从 $x$开始，只经过$S$中地点的最长路径，并告诉你其中的地点数量。(如果不可能从 $x$ 到达 $S$ 中的任何其他位置，史蒂夫将回复 $1$ ）。

由于您希望获得成就 "热门旅游目的地"，因此您希望找到任何一条可以访问尽可能多地点的路径。史蒂夫特别慷慨，会给你 $2 \cdot n$ 查询来找到它。

### 题解

作为交互题，我们一般是不怎么考虑时间复杂度的，毕竟已经限制了询问次数。

对于这道题而言，我们发现整个长度最长的路径的起点是很容易的，我们只需要把每个节点当作节点当作起点和所有节点当作S访问，就可以得到每个作为起点的最长路径。找到了起点，怎么找路径呢？

我们可以从最长的节点开始，询问次长的那个节点跟这个节点有没有联通，这样最后找到头（路径为1的节点就行）。举个例子，就假如总共5个节点，从1开始的路径最长为4，2，3开始的路径长度为3，这样1跟2或者3肯定是连着的，单独询问1与2，1与3是不是连着的，因为一条路径上的每个点到这条路终点的距离肯定是连续的而且按顺序依次减1，这样就可以找到一条完整的路径。

代码如下：

```cpp
#include<bits/stdc++.h>
using namespace std;
void solve()
{
    int n,x,now,t=1,y;
    cin>>n;
    vector<pair<int,int>>v(n+5);
    vector<int>ans;
    for(int i=1;i<=n;i++)
    {
        cout<<'?'<<' '<<i<<' '<<n<<' ';
        for(int j=1;j<=n;j++) cout<<j<<' ';
        cout<<'\n';
        fflush(stdout);
        cin>>x,v[i].first=-x,v[i].second=i;
    }
    sort(v.begin()+1,v.begin()+n+1);
    while(t<=n)
    {
        now=-v[t].first,x=v[t].second,ans.push_back(x);
        while(t<=n&&-v[t].first==now) t++;
        if(t>n) break;
        do
        {
            cout<<'?'<<' '<<x<<' '<<2<<' '<<x<<' '<<v[t].second<<'\n';
            fflush(stdout);
            cin>>y,t++;
        } while (y==1);
        t--;
    }
    cout<<'!'<<' '<<ans.size()<<' ';
    for(auto it:ans) cout<<it<<' ';
    cout<<'\n';
    fflush(stdout);
}
int main()
{
    // ios::sync_with_stdio(0);
    // cin.tie(0);
    // cout.tie(0);
    int t;
    cin>>t;
    while(t--) solve();
    return 0;
}
```

## D. Chicken Jockey

### 题目回顾

史蒂夫做出了一个愚蠢的决定：在夜间采矿，结果遇到了一种可怕的生物："鸡骑士" $^n$ ！

鸡骑士 $^n$ 由 $n$ 个暴徒依次叠加而成， $1$ 在下， $n$ 在上。小怪 $i$ 的初始生命值为 $h_i$ 。

在一次攻击中，史蒂夫可以对任何小怪造成 $1$ 伤害。如果任何小怪的生命值达到 $0$ 或更低，它就会死亡，它上面的所有小怪都会倒下并形成一个新的堆叠。新堆叠中**底**的小怪会受到 $1$ 的坠落伤害，坠落前它上面的每只小怪都会受到 $1$ 的坠落伤害（即之前堆叠中它下面的小怪数量，包括刚刚死亡的那只）。这可能也会杀死它，在这种情况下，它上面的所有小怪都会再次坠落，过程重复。

例如，考虑一个小鸡骑师 $^6$ ，其初始小怪健康值为 $[1, 2, 1, 3, 5, 2]$ 。如果史蒂夫伤害了堆叠中的第三个小怪，它就会死亡，健康值为 $[3, 5, 2]$ 的小怪就会倒下，形成新的堆叠。最下面的小怪会受到 $3$ 个单位的坠落伤害，因此它也会死亡，生命值为 $[5, 2]$ 的小怪会以新的堆叠方式坠落。底部的小怪受到 $1$ 单位的坠落伤害。因此，在史蒂夫的第一次攻击后，会有两个生命值为 $[1, 2]$ 和 $[4, 2]$ 的堆叠。

史蒂夫的剑耐久度很低，因此他希望知道杀死所有小怪所需的最小攻击次数。

### 题解

这个题本质是dp,但是我聪明的队友想到了一个类似dp但是胜似dp的写法，我感觉是相当于滚动数组优化了，空间复杂度降到O(1)。

在设计状态之前，我们可以考虑小怪是怎么死的。如果小怪收到的坠落伤害超过1，那么下面的小怪一定是在没有受到坠落伤害的情况下被杀死的，否则它就会处于堆叠的底部，因此上面的小怪下面只有1个小怪，并受到1次坠落上海。因此下方的小怪必须收到$h_i$次攻击才能杀死，无论它是否在原始堆叠中，也无论它下方的其他小怪是否掉落并受到伤害。这意味着，如果小怪受到的坠落伤害超过 1 ，最好在原始堆叠中进行，因为这样可以最大化坠落伤害，而不会招致额外的攻击。

因此，其实就两种情况。对于每个小怪 i ，我们要么直接杀死它，要么通过直接杀死它下面的小怪让它承受 i−1 的坠落伤害，要么让它在下面的小怪死亡时坠落承受 1 的坠落伤害。因此我们设计状态，$dp[i]$为杀死$i$之前所有小怪所需要的最小攻击。转移方程如下：
$$
dp[i + 1] = min(dp[i] + h[i] - 1, dp[i - 1] + h[i - 1] + max(0, h[i] - i))
$$
其实观察这个式子也能发现，状态更新只需要邻近的两个，所以不用开dp数组也可以解决这个问题。

代码如下：

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
void solve(){
    int n;
    cin>>n;
    vector<int> h(n);
    int ans=0;
    for(int i=0;i<n;i++){
        cin>>h[i];
        ans+=h[i];
    }
    ans-=(n-1);
    int a=0,b=0;
    for(int i=1;i<n;i++){
        int k=min(i,h[i])-2;
        if(k<0){
            k=0;
        }
        int c=max(b,a+k);
        a=b;
        b=c;
    }
    cout<<ans-b<<"\n";
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int t;
    cin>>t;
    while(t--){
        solve();
    }
    return 0;
}
```

## E. I Yearned For The Mines

### 题目回顾

史蒂夫从小就向往矿山！他的矿井可以表示为一棵由 $n$ 节点组成的 $^{\text{∗}}$ 树。

不幸的是，史蒂夫的矿井已经被他最大的克星--Herobrine 所渗透！在任何时候，Herobrine 都会躲藏在一个节点中；最初，他可能躲藏在任何一个节点中。史蒂夫可以执行以下操作：

- $1 x$ - 检查 Herobrine 当前是否在节点 $x$ 中。如果是，史蒂夫会抓住他。否则，Herobrine 可能会也可能不会移动到任何相邻的节点(**除了**你刚刚检查过的节点)。
- $2 x$ - 销毁所有与节点 $x$ 相连的边缘；Herobrine将无法再使用这些边缘。之后，Herobrine 可能会也可能不会移动到任何相邻节点。

请找出一个最多包含 $\left\lfloor \frac{5}{4} \cdot n \right\rfloor$ 次操作的序列，无论 Herobrine 的初始位置和移动情况如何，都能保证史蒂夫捕捉到 Herobrine。我们已经证明，在给定的约束条件下，这总是可能的。

$^{\text{∗}}$ 树是一个没有循环的连通图。

### 题解

首先，为了找到Herobrine我们肯定要检查每一个节点，所以我们只有$\left\lfloor\frac{n}{4}\right\rfloor$次操作2的次数。

可以发现，对于链来讲，只要我们从一个端口开始出发，那么一定能完美的检查完所有的节点。因此，我们只需要先跑一遍dfs,把子树大小大于等于4的做操作2，并且做完后将其大小清零，我们就可以把整个树划分为若干个链和孤点，这样搜完就行了。

代码如下：

```cpp
#include <bits/stdc++.h>
using namespace std;

// 用于比较常见的 输出"YES"和"NO"题目
#define returnNo return void(puts("No"))
#define returnYes return void(puts("Yes"))
//

// 用于 debug函数,只会在本地编辑器才显示
template <typename A, typename B>
ostream &operator<<(ostream &os, const pair<A, B> &p) { return os << '(' << p.first << ", " << p.second << ')'; }
template <typename T_container, typename T = typename enable_if<!is_same<T_container, string>::value, typename T_container::value_type>::type>
ostream &operator<<(ostream &os, const T_container &v)
{
    os << '[';
    string sep;
    for (const T &x : v)
        os << sep << x, sep = ", ";
    return os << ']';
}

void debug_out() { cerr << endl; }
template <typename Head, typename... Tail>
void debug_out(Head H, Tail... T)
{
    cerr << ' ' << H;
    debug_out(T...);
}
#ifdef LOCAL
#define debug(...) cerr << "[" << #__VA_ARGS__ << "]:", debug_out(__VA_ARGS__)
#else
#define debug(...)
#endif
//

// 常用头
#define int long long
#define endl '\n'
constexpr int N = 2e5 + 10;

int n;
int sz[N], mark[N], vis[N];
vector<pair<int, int>> e[N];
vector<pair<int, int>> ans;

void dfs1(int x, int f)
{
    sz[x] = 1;
    for (auto [v, w] : e[x])
    {
        if (v == f)
            continue;
        dfs1(v, x);
        sz[x] += sz[v];
    }
    if (sz[x] >= 4)
    {
        ans.push_back({2, x});
        sz[x] = 0;
        for (auto [v, w] : e[x])
            mark[w] = 1;
    }
}
void dfs2(int x)
{
    vis[x] = 1;
    ans.push_back({1, x});
    for (auto [v, w] : e[x])
        if (mark[w] != 1 && vis[v] != 1)
            dfs2(v);
}
void clear()
{
    ans.clear();
    for (int i = 1; i <= n; i++)
    {
        e[i].clear();
        vis[i] = mark[i] = sz[i] = 0;
    }
}

void solve()
{
    cin >> n;
    for (int i = 1, u, v; i < n; i++)
    {
        cin >> u >> v;
        e[u].push_back({v, i});
        e[v].push_back({u, i});
    }
    dfs1(1, 0);
    for (int i = 1; i <= n; i++)
    {
        if (!vis[i])
        {
            int cnt = 0;
            for (auto [v, w] : e[i])
                cnt += mark[w] ^ 1;
            if (cnt <= 1)
                dfs2(i);
        }
    }
    cout << ans.size() << '\n';
    for (auto t : ans)
        cout << t.first << ' ' << t.second << '\n';
    clear();
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int _ = 1;
    cin >> _;
    while (_--)
        solve();
}
```

## F. Flint and Steel

很可惜，我们队都不会，哈哈哈哈哈。
