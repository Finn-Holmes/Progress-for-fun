## 题目描述
You are playing a game consisting of n planets. Each planet has a teleporter to another planet (or the planet itself).
You have to process q queries of the form: You are now on planet a and want to reach planet b. What is the minimum number of teleportations?
## 输入
The first input line contains two integers n and q: the number of planets and queries. The planets are numbered 1,2,...,n.
The second line contains n integers t1,t2,...,t_n: for each planet, the destination of the teleporter.
Finally, there are q lines describing the queries. Each line has two integers a and b: you are now on planet a and want to reach planet b.

Constraints

1 ≤ n, q ≤ 2*10^5

1 ≤ a,b ≤ n
## 输出
For each query, print the minimum number of teleportations. If it is not possible to reach the destination, print -1.
## 样例输入
```
5 3
2 3 2 3 2
1 2
1 3
1 4
```
## 样例输出
```
1
2
-1
```
## 题解
这张图的特点是每个节点恰有一条出边，是一个“基环内向森林”（functional graph）。

即为如下图

当你看到这张图，我觉得你会和我有同样的感受：怎么比我想象的图要简单很多

若 a、b 属于不同连通分支，显然不可达。我们可以把整个图看成若干个连通分支：每个分支包含一个环（cycle）与若干条指向该环的链（chain）。从 a 到 b 能否到达以及最小步数，可以按下面几种情况讨论：

若 a、b 都在同一条链上：只有当 b 比 a 更靠近环（即 dist[a] ≥ dist[b] 且把 a 向前跳 dist[a]-dist[b] 步正好能到 b）时才能到达，步数即为两者深度差。

若 a、b 都在同一环上：给环上节点按顺序编号，答案是两节点编号的顺时针差值（取模环长）。

若一在链上、一在环上：只有链上的为 a、环上的为 b 时才可能到达。先把 a 走到环入口（需 t = dist[a] 步），到达环上某点 c，再在环上从 c 走到 b（按第 3 种情况计算）；总步数为 t + 环内步数。把 a 跳到环入口 c 的操作可以用二进制倍增（binary lifting）在 O(log n) 内完成。

因此实现时需要为每个节点预处理并保存三类信息：它所属的分支编号（cycle）、若在环上则在环内的序号（node，非环节点置为 -1）、以及链上节点到环的最短距离（d）。检测环并标注这些信息可通过遍历/DFS（或三色法）完成；由于每个节点出边仅一条，标注过程简单且不必维护显式栈。

vis:访问状态标记:0未访问   1本轮正在访问   2已访问

len:环的长度
煮啵对于本题的学习来自这位博主，原文链接：https://blog.csdn.net/2402_87894896/article/details/157098465
## code
```C++
#include <bits/stdc++.h>
using namespace std;
vector<int> t,vis,node,cycle,len,d;
vector<vector<int>> dp;
int sum(int a,int k){
	for(int i=0;i<20;i++){
		if(k&(1<<i)){
			a=dp[a][i];
		}
	}
	return a;
}
int query(int a,int b){
	if(cycle[a]!=cycle[b]) return -1;//不连通，不在一个“圈”
	int length=len[cycle[a]];
	//都在环上
	if(node[a]!=-1&&node[b]!=-1){
		int ans=(node[b]-node[a]+length)%length;
		return ans;
	} 
	//都在链上
	if(node[a]==-1&&node[b]==-1){
		int ans=d[a]-d[b];
		if(ans<0){
			return -1;
		}
		if(sum(a,ans)!=b) return -1;//确保b是a后继，其实我不是很理解这一步和ans<0有什么区别 
		return ans;
	} 
	//a在链上b在环上
	if(node[a]==-1&&node[b]!=-1){
		int c=sum(a,d[a]);//c为环上一点
		int ans=(node[b]-node[c]+length)%length;//转化为第一种情况
		return ans+d[a];
	} 
	return -1;
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	int n,q;
	cin>>n>>q;
	t.resize(n+1);//传送门 
	dp.assign(n+1,vector<int>(20,0));//倍增，到达地点 
	vis.resize(n+1);//访问情况 
	node.assign(n+1,-1);//-1为不在环上 ，在环上的位置 
	cycle.resize(n+1);//第几个环 
	len.resize(n+1);//环的长度 
	d.resize(n+1);//到环的距离 
	for(int i=1;i<=n;i++){
		cin>>t[i];
		dp[i][0]=t[i];
	}
	for(int j=1;j<20;j++){
		for(int i=1;i<=n;i++){
			dp[i][j]=dp[dp[i][j-1]][j-1];
		}
	}
	int cycle_cnt=0;
	for(int i=1;i<=n;i++){
		if(vis[i]!=0) continue;//只找未处理过的,1为正在处理,2为已处理
		int current=i;
		vector<int> road;//用来记录当前路径，方便给每个点打标记
		for(;;){
			if(vis[current]==0){//未访问，标记，加入路径 
				vis[current]=1;
				road.push_back(current);
				current=t[current];
				continue;
			}
			if(vis[current]==1){//成环 
				cycle_cnt++;
				int head=find(road.begin(),road.end(),current)-road.begin();
				vector<int> huan(road.begin()+head,road.end());
				len[cycle_cnt]=huan.size(); 
				for(int j=0;j<huan.size();j++){
					int it=huan[j];
					vis[it]=2;
					node[it]=j;
					cycle[it]=cycle_cnt;
					d[it]=0;
				}
				//处理未成环部分
				int dist=0;
				for(int j=head-1;j>=0;j--){
					int it=road[j];
					vis[it]=2;
					cycle[it]=cycle_cnt;
					d[it]=(++dist);
				} 
				break;
			}
			if(vis[current]==2){//未成环，连到别的环外线上 
				int dist=0;
				for(int j=road.size()-1;j>=0;j--){
					int it=road[j];
					vis[it]=2;
					cycle[it]=cycle[current];
					d[it]=d[current]+(++dist);
				}
				break; 
			}
		} 	
	} 
	int a,b;
	while(q--){
		cin>>a>>b;
		cout<<query(a,b)<<'\n';
	}
}
```
