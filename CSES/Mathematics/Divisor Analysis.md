## 题目描述
Given an integer, your task is to find the number, sum and product of its divisors. As an example, let us consider the number 12:
the number of divisors is 6 (they are 1, 2, 3, 4, 6, 12)
the sum of divisors is 1+2+3+4+6+12=28
the product of divisors is 1* 2* 3* 4* 6* 12 = 1728
Since the input number may be large, it is given as a prime factorization.
## 输入
The first line has an integer n: the number of parts in the prime factorization.
After this, there are n lines that describe the factorization. Each line has two numbers x and k where x is a prime and k is its power.

Constraints

1 ≤ n ≤ 10^5

2 ≤ x ≤ 10^6

each x is a distinct prime

1 ≤ k ≤ 10^9
## 输出
Print three integers modulo 109+7: the number, sum and product of the divisors.
## 样例输入
```
2
2 2
3 1
```
## 样例输出
```
6 28 1728
```
## 题解
按答案将代码分为三部分，分别求解
#### 因数个数
```C++
long long num=1;//除数个数 
for(int i=1;i<=n;i++){
  num=num*(long long)(p[i].second+1)%MOD;//对于每个因数num,有0……k  k+1种取法 
}
```
#### 因数求和
以样例为例，12=1*2*3*4*6*12，1+2+3+4+6+12=(1+2+4)*(1*3),应该比较清晰明了,对于每一项素因数{num,k},它对于因数求和的贡献是num^0+num^1+...+num^k.
即为对于每一项素因数，进行等比数列求和,再求乘积.
```C++
//因数之和为各因数  0……k等比数列之和的乘积
long long sum=1;
for(int i=1;i<=n;i++){
  long long num=p[i].first,k=p[i].second;
  long long res=bi_sum(num,k);
  sum=sum*res%MOD;
}
```
#### 因数乘积
```C++
long long number=1;
long long pi=1;
for(int i=1;i<=n;i++){
  long long num=p[i].first,k=p[i].second;
  long long op=mi(num,k*(k+1)/2);
  number=mi(number,k+1)*mi(op,pi)%MOD;
  pi=(pi*(k+1))%(MOD-1);
}
```
number为前i个素因数构成的因数乘积,pi为所有除数个数,op为当前素因子能够做出的贡献(多少种方案构成不同的因子,num^1,num^2...num^k),当前素因子Num有k种方案,每一个因数num^j有pi种选择组成不同的除数,mi(op,pi)这是
到目前素因子项为止,对于乘积当前素因子num做的贡献,mi(number,k+1)是前i-1项做的贡献.
## code 
```C++
#include <bits/stdc++.h>
using namespace std;
const int MOD=1e9+7;
long long mi(long long num,long long k){//快速幂 
	long long res=1;
	while(k){
		if(k&1){
			res=res*num%MOD;
		}
		num=num*num%MOD;
		k>>=1;
	}
	return res;
}
long long inv(long long num){
	return mi(num,MOD-2);
}
long long bi_sum(long long num,long long k){//等比数列求和 
	if(num==1) return 1;
	long long res=(mi(num,k+1)-1+MOD)%MOD;
	long long demon=(num-1+MOD)%MOD;
	return res*inv(demon)%MOD;
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	int n;
	cin>>n; 
	vector<pair<long long,long long>> p(n+1);
	for(int i=1;i<=n;i++){
		long long num,k;
		cin>>num>>k;
		p[i]={num,k};
	} 
	long long num=1;//除数个数 
	for(int i=1;i<=n;i++){
		num=num*(long long)(p[i].second+1)%MOD;//对于每个因数num,有0……k  k+1种取法 
	}
	//因数之和为各因数  0……k等比数列之和的乘积
	long long sum=1;
	for(int i=1;i<=n;i++){
		long long num=p[i].first,k=p[i].second;
		long long res=bi_sum(num,k);
		sum=sum*res%MOD;
	} 
	long long number=1;
	long long pi=1;
	for(int i=1;i<=n;i++){
		long long num=p[i].first,k=p[i].second;
		long long op=mi(num,k*(k+1)/2);
		number=mi(number,k+1)*mi(op,pi)%MOD;
		pi=(pi*(k+1))%(MOD-1);
	}
	cout<<num<<" "<<sum<<" "<<number;
	return 0; 
}
```
