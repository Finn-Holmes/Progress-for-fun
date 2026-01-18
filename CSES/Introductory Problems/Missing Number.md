## 题目描述
You are given all numbers between 1,2,...,n except one. Your task is to find the missing number.
## 输入
The first input line contains an integer n（2 ≤ n ≤ 2*105）.
The second line contains n-1 numbers. Each number is distinct and between 1 and n (inclusive).
## 输出
Print the missing number.
## 样例输入
```
5
2 3 1 5
```
## 样例输出
```
4
```
## 题解
很简单，计数即可
## code
```C++
#include <bits/stdc++.h>
using namespace std;
int a[200020];
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int n,i,x,maxnum=0;
    cin>>n;
    for(i=1;i<=n-1;i++){
        cin>>x;
        a[x]++;
        maxnum=max(maxnum,x);
    }
    for(i=1;i<=n;i++){
        if(a[i]==0){
            cout<<i;
        }
    }
}
```
