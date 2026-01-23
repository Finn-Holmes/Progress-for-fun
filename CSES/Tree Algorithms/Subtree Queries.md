## 题目描述
You are given a rooted tree consisting of n nodes. The nodes are numbered 1,2,...,n, and node 1 is the root. Each node has a value.
Your task is to process following types of queries:
1.change the value of node s to x
2.calculate the sum of values in the subtree of node s

## 输入
The first input line contains two integers n and q: the number of nodes and queries. The nodes are numbered 1,2,\ldots,n.
The next line has n integers v1,v2,...,vn: the value of each node.
Then there are n-1 lines describing the edges. Each line contans two integers a and b: there is an edge between nodes a and b.
Finally, there are q lines describing the queries. Each query is either of the form "1 s x" or "2 s".
Constraints
1 ≤ n, q ≤ 2*105
1 ≤ a,b, s ≤ n
1 ≤ vi, x ≤ 109
## 输出
Print the answer to each query of type 2.
## 样例输入
```
5 3
4 2 5 2 1
1 2
1 3
3 4
3 5
2 3
1 5 3
2 3
```
## 样例输出
```
8
10
```
## 题解
把树扁平化处理，time[i]记录编号为i的结点在st数组中的位置，num[time[i]]记录编号为i的数值，注意此处用的是dfs，不是bfs(队列)，队列是将树分层，同一层的结点优先在数组中连续排列，而dfs是父节点优先与其所有子结点在数组中连续排列,同时记录每个父节点子结点的个数sz[i](逆拓扑序求解,后序遍历)

此时题目就已经转化为求区间和与更新结点值，对于结点s的子树和，左边界就是time[s]，右边界就是time[s]+sz[s]-1,典型的线段树问题.

## code
```C++
#include <bits/stdc++.h>
using namespace std;
struct Tree{
	int n;
	vector<long long> st;
	Tree(int nn){
		init(nn);
	}
	void init(int nn){
		n=nn;
		st.assign(4*n+5,0);
	}
	void build(int p,int l,int r,const vector<long long>& nums){
		if(l==r){
			st[p]=nums[l];
			return;
		}
		int m=(l+r)/2;
		build(2*p,l,m,nums);
		build(2*p+1,m+1,r,nums);
		st[p]=st[p*2]+st[2*p+1];
	}
	void update(int p,int l,int r,int index,long long val){
		if(l==r&&l==index){
			st[p]=val;
			return;
		}
		int m=(l+r)/2;
		if(index<=m) update(2*p,l,m,index,val);
		else update(2*p+1,m+1,r,index,val);
		st[p]=st[2*p]+st[2*p+1]; 
	}
	long long query(int p,int l,int r,int ql,int qr){
		if(ql>r||qr<l) return 0;
		if(ql<=l&&qr>=r){
			return st[p];
		}
		
		int mid=(l+r)/2;
		return query(2*p,l,mid,ql,qr)+query(2*p+1,mid+1,r,ql,qr);
	}
};
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	int n,q;
	cin>>n>>q;
	vector<int> vals(n+1,0);
	for(int i=1;i<=n;i++){
		cin>>vals[i];
	}
	vector<vector<int>> g(n+1,vector<int>());
	for(int i=0;i<n-1;i++){
		int a,b;
		cin>>a>>b;
		g[a].push_back(b);
		g[b].push_back(a);
	}
	vector<int> fa(n+1,0),depth(n+1,0),tuopu;//记录父节点，深度，扁平序列
	vector<int> st;
	fa[1]=0;
	depth[1]=0;
	st.push_back(1); 
	while(!st.empty()){//扁平化
		int u=st.back();
		st.pop_back();
		tuopu.push_back(u);
		for(int v:g[u]){
			if(v==fa[u]) continue;
			fa[v]=u;
			depth[v]=depth[u]+1;
			st.push_back(v);
		}
	}
	vector<int> time(n+1,0),sz(n+1,1);
	for(int i=0;i<tuopu.size();i++){//记录编号在扁平化序列中的位置
		time[tuopu[i]]=i+1;
	}
	for(int i=tuopu.size()-1;i>=0;i--){
		int u=tuopu[i];
		if(fa[u]!=0) sz[fa[u]]+=sz[u];//子结点个数
	}
	vector<long long> nums(n+1,0);//编号的值，索引为编号在扁平化序列中的位置
	for(int u=1;u<=n;u++){
		nums[time[u]]=vals[u];
	}
	Tree tree(n);
	tree.build(1,1,n,nums);
	for(int i=0;i<q;i++){
		int type;
		cin>>type;
		if(type==1){
			int s,x;
			cin>>s>>x;
			int index=time[s];
			tree.update(1,1,n,index,x);
		}else{
			int s;
			cin>>s;
			int ql=time[s];
			int qr=ql+sz[s]-1;
			cout<<tree.query(1,1,n,ql,qr)<<'\n';
		}
	}
}
```
