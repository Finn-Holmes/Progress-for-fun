## 题目描述
A company has n employees, who form a tree hierarchy where each employee has a boss, except for the general director.
Your task is to process q queries of the form: who is employee x's boss k levels higher up in the hierarchy?
## 输入
The first input line has two integers n and q: the number of employees and queries. The employees are numbered 1,2,...,n, and employee 1 is the general director.
The next line has n-1 integers e2,e3,...,en: for each employee 2,3,...,n their boss.
Finally, there are q lines describing the queries. Each line has two integers x and k: who is employee x's boss k levels higher up?
Constraints

1 ≤ n,q ≤ 2*10^5

1 ≤ ei ≤ i-1

1 ≤ x ≤ n

1 ≤ k ≤ n
## 输出
Print the answer for each query. If such a boss does not exist, print -1.
## 样例输入
```
5 3
1 1 3 3
4 1
4 2
4 3
```
## 样例输出
```
3
1
-1
```
## 题解
倍增即可
## code
```C++
#include <bits/stdc++.h>
using namespace std;

int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	int n,q;
	cin>>n>>q;
	vector<int> fa(n+1,0);
	for(int i=2;i<=n;i++){
		cin>>fa[i];
	}
	int num=0;
	while((1<<num)<=n) num++;
	vector<vector<int>> d(n+1,vector<int>(num,0));
	for(int i=1;i<=n;i++){
		d[i][0]=fa[i];
	}
	for(int i=1;i<num;i++){
		for(int j=1;j<=n;j++){
			int m=d[j][i-1];
			if(m==0){
				d[j][i]=0;
			}else{
				d[j][i]=d[m][i-1];
			}
		}
	}
	while(q--){
		int x,k;
		cin>>x>>k;
		for(int i=0;i<num;i++){
			if(k&(1<<i)){
				x=d[x][i];
			}
		}
		if(x==0){
			cout<<-1<<'\n';
		}else{
			cout<<x<<'\n';
		}
	}
	return 0;
}
```
