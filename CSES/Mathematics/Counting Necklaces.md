## 题目描述
Your task is to count the number of different necklaces that consist of n pearls and each pearl has m possible colors.
Two necklaces are considered to be different if it is not possible to rotate one of them so that they look the same.
## 输入
The only input line has two numbers n and m: the number of pearls and colors.

Constraints

1 ≤ n,m ≤ 10^6

## 输出
Print one integer: the number of different necklaces modulo 109+7.
## 样例输入
```
4 3
```
## 样例输出
```
24
```
## 题解
波利亚计数，循环排列

项链数量公式为：

$$
\text{项链数量} = \frac{1}{n} \sum_{k=0}^{n-1} m^{\gcd(n, k)}
$$

- $n$：珍珠颗数（项链环长度）
- $m$：颜色种类数
- $\gcd(n, k)$：$n$和$k$的最大公约数
## code
```C++
#include <bits/stdc++.h>
using namespace std;
const int MOD=1e9+7;
long long mi(long long n,long long k){
	long long res=1;
	while(k){
		if(k&1) res=res*n%MOD;
		n=n*n%MOD;
		k>>=1;
	}
	return res;
}
long long inv(long long n){
	long long mod=MOD-2;
	return mi(n,mod);
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	long long n,m;
	cin>>n>>m;
	long long sum=0;
	for(long long k=0;k<n;k++){
		sum=(sum+mi(m,__gcd(n,k)))%MOD;
	}
	long long ans=sum*inv(n)%MOD;
	cout<<ans;
	return 0;
}

```
