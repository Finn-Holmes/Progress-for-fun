## 题目描述
There is a sorted array [1,2,...,n] and a permutation p1,p2,...,pn. On each round, all elements move according to the permutation: the element at position i moves to position pi.
After how many rounds is the array sorted again for the first time?

## 输入
The first line has an integer n.
The next line contains n integers p1,p2,...,pn.

## 输出
Print the number of rounds modulo 10^9+7.

Constraints

1 ≤ n ≤ 2*10^5

## 样例输入
```
8
5 3 2 6 4 1 8 7
```
## 样例输出
```
4
```
## 提示
Explanation: The array changes as follows after the rounds:

Round 1: [6,3,2,5,1,4,8,7]

Round 2: [4,2,3,1,6,5,7,8]

Round 3: [5,3,2,6,4,1,8,7]

Round 4: [1,2,3,4,5,6,7,8]
## 题解
核心思路是找数组中的环，记录每个环的长度，求解所有长度的最小公倍数即可，感觉本题最麻烦的地方，是如何求一系列数的最小公倍数.

我采取的方法是记录长度数组中，对于每个长度len,分解质因数x,y,z,...，len=x^k1 * y^k2 * z*k3 *...,对于x,y,z更新最大指数max_pow[x],max_pow[y],max_pow[z],...
最后将这些数乘起来就是最小公倍数.
## code
```C++
#include <bits/stdc++.h>
using namespace std;
const int MOD=1e9+7;
const int MAXN=2e5+10;
bool is_prime[MAXN];//记录是否为质数
int primes[MAXN];//存质数
int min_p[MAXN];//数的最小质因数
int cnt_prime;//质数个数
long long max_pow[MAXN];//每个质因数取最大指数
void init(){
	memset(is_prime,true,sizeof(is_prime));
	is_prime[0]=is_prime[1]=false;
	cnt_prime=0;
	for(int i=2;i<MAXN;i++){
		if(is_prime[i]){//质数
			primes[cnt_prime++]=i;//记录素数
			min_p[i]=i;
		}
		for(int j=0;j<cnt_prime&&i*primes[j]<MAXN;j++){
			int p=primes[j];
			is_prime[i*p]=false;
			min_p[i*p]=p;//更新最小质因数
			if(i%p==0) break;
		}
	}
}
void fun(int x){//分解质因数
	if(x==1) return ;
	while(x>1){
		int p=min_p[x];
		int cnt=0;
		while(x%p==0){
			cnt++;
			x/=p;
		}
		max_pow[p]=max(max_pow[p],(long long)cnt);//更新p的最大指数
	}
}
long long mi(long long x,long long k){//快速幂
	long long res=1;
	x=x%MOD;
	while(k){
		if(k&1){
			res=res*x%MOD;
		}
		x=x*x%MOD;
		k>>=1;
	}
	return res;
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	init();
	int n;
	cin>>n;
	vector<int> nums(n+1);
	vector<bool> vis(n+1,false);
	for(int i=1;i<=n;i++){
		cin>>nums[i];
	}
	vector<long long> lens;
	for(int i=1;i<=n;i++){
		if(!vis[i]){
			vis[i]=true;
			int head=i;
			long long len=1;
			int j=i;
			while(nums[j]!=head){//记录环的长度
				j=nums[j];
				vis[j]=true;
				len++;
			}
			fun(len);
		}
	}
	long long ans=1;
	for(int i=0;i<cnt_prime;i++){
		int p=primes[i];
		if(max_pow[p]==0) continue;
		ans=ans*mi(p,max_pow[p])%MOD;
	}
	cout<<ans;
	return 0; 
}
```
