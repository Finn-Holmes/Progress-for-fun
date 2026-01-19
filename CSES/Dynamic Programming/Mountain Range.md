## 题目描述
There are n mountains in a row, each with a specific height. You begin your hang gliding route from some mountain.
You can glide from mountain a to mountain b if mountain a is taller than mountain b and all mountains between a and b.
What is the maximum number of mountains you can visit on your route?

## 输入
The first line has an integer n: the number of mountains.
The next line has n integers h1, h2,..., hn: the heights of the mountains.
## 输出
Print one integer: the maximum number of mountains.
Constraints

1≤ n ≤ 2*10^5
1≤ hi ≤ 10^9

## 样例输入
```
10
20 15 17 35 25 40 12 19 13 12
```
## 样例输出
```
5
```
## 题解
dp+线段树+单调栈，感觉不是很难想，实现起来模块分清了也是很好实现的

首先，对于每一个i(1……n) ，运用单调栈分别找到左侧和右侧第一个大于heights[i]的位置，即l[i]和r[i],则对于每一座山i，l[i]+1……r[i]-1之间的山都可达（题目要求的是严格单调递减，我们这里是非增，高度相同的情况我们后面考虑）

之后我们采取的方式是将每座山的索引按高度升序排序，因为我们后面dp需要优先考虑高度低的山，倒序寻找

dp:dp[i]表示从索引为i的山开始滑翔最多能经历的山数，对于每一座山i，我们需要在(l[i]+1~r[i]-1)中找到中找到索引为k的山，满足dp[k]最大，即从i出发，下一座山去k可以使结果最大
。这里的查询操作就需要用到线段树了，线段树的st数组存储的是每一座山的dp，顺序存储；
还有一个值得注意的点，我们上文说到我们还没有考虑两座山相等的情况，如果忽视必然是错误的，这也是以下代码存在的原因：
```C++
while(j<n&&a[j].hei==a[i].hei) j++;		
for(int k=i;k<j;k++){
```
```C++
for(int k=i;k<j;k++){
    int pos=a[k].idx;
    tree.update(pos,dp[pos]);
}
i=j;
```
我们将相同高度的山算作一部分，同时处理，延迟更新线段树，即：

山1和山2高度相同，我在更新山1dp后，并不把这个更新上传至线段树st数组，此时该数组中对应山1dp值为0，此时我再更新山2dp时，由于st中山1dp为0，山2自然不会走山1，

最后统一更新山1和山2在dp中的值

当然，你可以在处理单调栈是就把l[i]和r[i]算成两侧第一个大于等于heights[i]的位置就可以完全避免这个问题
## code
```C++
#include <bits/stdc++.h>
using namespace std;
struct op{
	int idx,hei;
};
bool cmp(op&p,op&q){
	return p.hei<q.hei; 
}
struct Tree{
	int n;
	vector<int> st;
	Tree(int nn=0){init(nn);}
	void init(int nn){
		n=1;
		while(n<nn) n<<=1;
		st.assign(2*n,0);
	}
	void update(int pos,int val){
		pos+=n;
		st[pos]=max(st[pos],val);
		for(pos>>=1;pos>0;pos>>=1){
			st[pos]=max(st[pos<<1],st[(pos<<1)+1]);
		}
	}
	int query(int l,int r){
		if(l>r) return 0;
		l+=n;
		r+=n;
		int res=0;
		while(l<=r){
			if(l&1) res=max(res,st[l++]);
			if(!(r&1)) res=max(res,st[r--]);
			l>>=1;
			r>>=1;
		}
		return res;
	}
};
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	int n;
	cin>>n;
	vector<int> heights(n+1);
	for(int i=1;i<=n;i++){
		cin>>heights[i];
	}
	vector<int> l(n+1,0),r(n+1,n+1);
//单调栈
	{
		stack<int> s;
		for(int i=1;i<=n;i++){
			while(!s.empty()&&heights[s.top()]<=heights[i]){
				s.pop();
			}
			if(!s.empty()) l[i]=s.top();
			s.push(i);
		}
	}
	{
		stack<int> s;
		for(int i=n;i>=1;i--){
			while(!s.empty()&&heights[s.top()]<=heights[i]){
				s.pop();
			}
			if(!s.empty()) r[i]=s.top();
			s.push(i);
		}
	}
	vector<op> a(n);
	for(int i=0;i<n;i++){
		a[i].idx=i+1;
		a[i].hei=heights[i+1];
	}
//按高度升序排序
	sort(a.begin(),a.end(),cmp);
	Tree tree(n+5);
	vector<int> dp(n+1,0);
	int ans=0;
	for(int i=0;i<n;){
		int j=i;
		while(j<n&&a[j].hei==a[i].hei) j++;//同一高度统一处理，延迟更新线段树
		for(int k=i;k<j;k++){
			int pos=a[k].idx;
			int left=l[pos]+1;
			int right=r[pos]-1;
			int best=tree.query(left,right);
			dp[pos]=best+1;
			ans=max(ans,dp[pos]);
		}
		for(int k=i;k<j;k++){//延迟更新线段树
			int pos=a[k].idx;
			tree.update(pos,dp[pos]);
		}
		i=j;
	}
	cout<<ans;
	return 0;
}


```
