## 题目描述
Your task is to calculate the number of valid bracket sequences of length n. For example, when n=6, there are 5 sequences:

()()()
()(())
(())()
((()))
(()())
## 输入
The only input line has an integer n.

Constraints

1 ≤ n ≤ 10^6
## 输出
Print the number of sequences modulo 109+7.
## 样例输入
```
6
```
## 样例输出
```
5
```
## 题解
#### 卡特兰数，C(2k,k)/(k+1).
## code
```C++
#include <bits/stdc++.h>
using namespace std;
const int N=2e6+5;
long long pre[N];
const int MOD=1e9+7;
long long inv[N];
long long inverse(long long num){
    long long res=1;
    int mod=MOD-2;
    while(mod){
        if(mod&1) res=res*num%MOD;
        num=num*num%MOD;
        mod>>=1;
    }
    return res;
}
void init(){
    pre[0]=1;
    inv[0]=1;
    for(int i=1;i<N;i++){
        pre[i]=pre[i-1]*i%MOD;
        inv[i]=inverse(pre[i]);
    }
}
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    init();
    int n;
    cin>>n;
    if(n%2==1){
        cout<<0;
        return 0;
    }
    cout<<pre[n]*inv[n/2]%MOD*inv[n/2]%MOD*inverse(n/2+1)%MOD;
}
```
