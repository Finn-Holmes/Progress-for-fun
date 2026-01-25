## 题目描述
Consider a game where there are n children (numbered 1,2,...,n) in a circle. During the game, every second child is removed from the circle, until there are no children left.
Your task is to process q queries of the form: "when there are n children, who is the kth child that will be removed?"
## 输入
The first input line has an integer q: the number of queries.
After this, there are q lines that describe the queries. Each line has two integers n and k: the number of children and the position of the child.

Constraints

1 ≤ q ≤ 105

1 ≤ k ≤ n ≤ 109
## 输出
Print q integers: the answer for each query.
## 样例输入
```
4
7 1
7 3
2 2
1337 1313
```
## 样例输出
```
2
6
1
1107
```
## 题解
约瑟夫环问题，模拟肯定是不行，我们可以简化单次循环(一圈)的时间，递归，然后找到圈与圈之间递归的小规则即可.
## code
```C++
#include <bits/stdc++.h>
using namespace std;
int q; 
map<pair<int,int>,int> f;
int fun(int n,int k){
	if(f[{n,k}]) return f[{n,k}];
	if(n==1) return f[{n,k}]=1;
	if(2*k<=n) return f[{n,k}]=2*k;
	int newk=k-n/2;
	int newn=n-n/2;
	if(n&1){
		int ans=fun(newn,newk);
		return f[{n,k}]=((2*ans-3)+n+1)%(n+1);
	}else{
		return f[{n,k}]=2*fun(newn,newk)-1;
	}
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	cin>>q;
	while(q--){
		long long n,k;
		cin>>n>>k;
		cout<<fun(n,k)<<'\n';
	} 
}

```
