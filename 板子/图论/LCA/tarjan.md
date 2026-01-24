```C++
#include <bits/stdc++.h>
using namespace std;
const int N=2e5+10;
vector<int> e[N];
bool vis[N];
vector<pair<int,int>> query[N];
int fa[N];
int ans[N];
int find(int u){
	if(fa[u]==u) return u;
	return fa[u]=find(fa[u]);
}
void tarjan(int u){
	vis[u]=true;
	for(int v:e[u]){
		if(!vis[v]){
			tarjan(v);
			fa[v]=u;
		}
	}
	for(auto q:query[u]){
		int v=q.first,i=q.second;
		if(vis[v]){
			ans[i]=find(v);
		}
	}
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	int n,m;
	cin>>n>>m;
	for(int i=2;i<=n;i++){
		int x;
		cin>>x;
		e[x].push_back(i);
		e[i].push_back(x);
	}
	for(int i=1;i<=n;i++){
		fa[i]=i;
	} 
	for(int i=1;i<=m;i++){
		int a,b;
		cin>>a>>b;
		query[a].push_back({b,i});
		query[b].push_back({a,i});
	}
	tarjan(1);
	for(int i=1;i<=m;i++){
		cout<<ans[i]<<'\n';
	}
	return 0;
}
```
