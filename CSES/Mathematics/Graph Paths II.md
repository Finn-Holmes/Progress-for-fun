## 题目描述
Consider a directed weighted graph having n nodes and m edges. Your task is to calculate the minimum path length from node 1 to node n with exactly k edges.
## 输入
The first input line contains three integers n, m and k: the number of nodes and edges, and the length of the path. The nodes are numbered 1,2,...,n.
Then, there are m lines describing the edges. Each line contains three integers a, b and c: there is an edge from node a to node b with weight c.

Constraints

1 ≤ n ≤ 100

1 ≤ m ≤ n(n-1)

1 ≤ k ≤ 10^9

1 ≤ a,b ≤ n

1 ≤ c ≤ 10^9
## 输出
Print the minimum path length. If there are no such paths, print -1.
## 样例输入
```
3 4 8
1 2 5
2 3 4
3 1 1
3 2 2
```
## 样例输出
```
27
```
## 题解
c[i][j]=min(c[i][j],a[i][k]+b[k][j]);

最核心的操作，每次都要更新，采用矩阵快速幂的方式更新，矩阵是adj是每条路的权重.
## code
```C++
#include <bits/stdc++.h>
using namespace std;
using ll=unsigned long long;
const ll INF=1e19;
int n,m;
ll k;
vector<vector<ll>> min_mul(const vector<vector<ll>>& a,const vector<vector<ll>>& b){
	int N=a.size();
	vector<vector<ll>> c(N,vector<ll>(N,INF));
	for(int i=0;i<N;i++){
		for(int k=0;k<N;k++){
			if(a[i][k]<INF){
				for(int j=0;j<N;j++){
					if(b[k][j]<INF){
						c[i][j]=min(c[i][j],a[i][k]+b[k][j]);
					}
				}
			}
		}
	}
	return c;
}
vector<vector<ll>> min_pow(vector<vector<ll>> base,ll e){
	int N=base.size();
	vector<vector<ll>> res(N,vector<ll>(N,INF));
	for(int i=0;i<N;i++){
		res[i][i]=0;
	}
	while(e){
		if(e&1) res=min_mul(res,base);
		base=min_mul(base,base);
		e>>=1;
	}
	return res;
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	cin>>n>>m>>k;
	vector<vector<ll>> adj(n,vector<ll>(n,INF));
	for(int i=0;i<m;i++){
		int a,b;
		ll c;
		cin>>a>>b>>c;
		a--;
		b--;
		adj[a][b]=min(adj[a][b],c);
	}
	vector<vector<ll>> res=min_pow(adj,k);
	ll ans=res[0][n-1];
	if(ans>=INF/2) cout<<-1;
	else cout<<ans;
	return 0;
}
```
