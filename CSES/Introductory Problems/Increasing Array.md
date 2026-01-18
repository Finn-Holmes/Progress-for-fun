## 题目描述
You are given an array of n integers. You want to modify the array so that it is increasing, i.e., every element is at least as large as the previous element.
On each move, you may increase the value of any element by one. What is the minimum number of moves required?
## 输入
The first input line contains an integer n（1 ≤ n ≤ 2*105）: the size of the array.
Then, the second line contains n integers x1,x2,...,xn（1 ≤ xi ≤ 109）: the contents of the array.
## 输出
Print the minimum number of moves.
## 样例输入
```
5
3 2 5 1 7
```
## 样例输出
```
5
```
## 题解
读题读一半我以为是单调栈或者是啥，读完题这就是暴力嘛，遍历都某一个逆序数字也没办法，只能+1知道升序
## code
```C++
#include <bits/stdc++.h>
using namespace std;
int a[200020];
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    long long int n,i,k=0;
    cin>>n;
    for(i=1;i<=n;i++){
        cin>>a[i];
    }
    for(i=2;i<=n;i++){
        if(a[i]<a[i-1]){
            k+=a[i-1]-a[i];
            a[i]=a[i-1];
        }
    }
    cout<<k;
}
```
