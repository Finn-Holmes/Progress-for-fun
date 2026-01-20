## 题目描述
Your task is to count the number of integers between a and b where no two adjacent digits are the same.
## 输入
The only input line has two integers a and b.

Constraints

0 ≤ a ≤ b ≤ 1018

## 输出
Print one integer: the answer to the problem.
## 样例输入
```
123 321
```
## 样例输出
```
171
```
## 题解
写个函数cnt(x)计算0-x满足条件的所有数字个数，cnt(b)-cnt(a-1)即为答案

数位dp，dfs，记忆化

pos为当前数字的索引（高位到低位第几位）,last为上一位数字（若之前的所有高位均为0，则为-1）

tight：

tight == true：表示到目前为止我们构造的前缀和上界 s 的对应前缀完全相同，下一位的取值被上界 s[pos] 限制（最大取值为 s[pos]）。也就是说，后续的可选范围依赖于上界 s。

tight == false：表示我们之前某一位已经选了比上界小的数字，从此之后构造的后续位就“不受上界限制”，可以自由取 0..9。此时后续子问题的结果只与 (pos, last, leading) 有关，与上界 s 无关。

leading（布尔）：到当前位之前是否全是前导零（即还没有放过任何非零位）。leading == true表示前导零仍在继续。

seen / dp：

dp[pos][last+1][leading] 存放对应状态的计算结果（符合条件的数的个数）。last+1 用来把 last=-1 映射到索引 0。

seen[pos][last+1][leading] 是布尔标记，表示 dp 中对应位置已经被计算过（已缓存）。使用 seen 是为了区别“还没计算过”与“计算后结果恰好为 0”两种情况。

#### 为什么“只有在 tight == false 时才读/写缓存”?

当 tight == true 时，当前子问题的答案还依赖于上界 s 的后续位（s[pos..]）。即使当前的 (pos, last, leading) 三元组相同，不同的上界 s 会导致不同的答案；此外，同一次 dfs 的不同路径如果导致 tight==true 也可能依赖不同的上一位是否等于上界，从而不能被简单复用。把 tight==true 的状态缓存并在别处直接复用，会导致用错上界，从而得到错误答案。

当 tight == false 时，后续可以随意填 0..9，不再依赖 s，因此同样的 (pos, last, leading) 状态的答案是确定且可重用的。把这些状态的结果缓存起来，可以显著减少重复计算。
#### 为什么会有重复访问vis[pos][last+1][leading]?
```C++
if(!tight&& vis[pos][last+1][leading]){
		return dp[pos][last+1][leading];
}
```
煮啵在做这个题的时候始终不理解为什么会有这一步操作

首先先说vis和dp是统一的,一个代表这个点是否访问/计算过，一个代表计算结果是什么，即上一位是last，当前是第pos位，前导状态也与leading相同，之后的方案数

会重复访问这个状态是因为这个状态只管了pos和pos前一位(last)和之后的方案数，pos前两位是就管不着了，也就是说会有多种路径到达目前状态，有这一步记忆化可以大幅减少计算量
## code
```C++
#include <bits/stdc++.h>
using namespace std;
string s;
long long dp[20][11][2];
bool vis[20][11][2];
long long dfs(int pos,int last,bool tight,bool leading){
	if(pos==(int)s.size()){//合法方案
		return 1;
	}
	if(!tight&& vis[pos][last+1][leading]){//计算过不再重复计算，依靠记忆化得知结果
		return dp[pos][last+1][leading];
	}
	int limit;
	if(tight) limit=s[pos]-'0';//当前为上界
	else limit=9;
	long long res=0;
	for(int d=0;d<=limit;d++){
		bool next=leading&&(d==0);
		if(next){//前导全为0
			res+=dfs(pos+1,-1,tight&&(d==limit),true);
		}else{
			if(last!=-1&&d==last){
				continue;
			}
			res+=dfs(pos+1,d,tight&&(d==limit),false);
		}
	}
	if(!tight){
		vis[pos][last+1][leading]=true;
		dp[pos][last+1][leading]=res;
	}
	return res;
}
long long cnt(long long x){
	if(x<0) return 0;
	s=to_string(x);
	memset(vis,0,sizeof(vis));//重置
	return dfs(0,-1,true,true);
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	long long a,b;
	cin>>a>>b;
	long long ans=cnt(b)-cnt(a-1);
	cout<<ans;
	return 0;
}

```
