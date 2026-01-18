## 题目描述
A permutation of integers 1,2,...,n is called beautiful if there are no adjacent elements whose difference is 1.
Given n, construct a beautiful permutation if such a permutation exists.
## 输入
The only input line contains an integer n（1 ≤ n ≤ 106）.
## 输出
Print a beautiful permutation of integers 1,2,...,n. If there are several solutions, you may print any of them. If there are no solutions, print "NO SOLUTION".
## 样例输入
### 【样例1】
```
5
```
### 【样例2】
```
3
```
## 样例输出
### 【样例1】
```
4 2 5 3 1
```
### 【样例2】
```
NO SOLUTION
```
## 题解
构造即可，需要注意的一点是我一开始else部分是按降序构造的，不对，n=4时不成立
## code
```C++
#include <bits/stdc++.h>
using namespace std;
 
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int n,i,k;
    cin>>n;
    if(n==1){
        cout<<1;
    }else if(n==2||n==3){
        cout<<"NO SOLUTION";
    }else{
        for(i=2;i<=n;i+=2){
            cout<<i<<" ";
        }
        for(i=1;i<=n;i+=2){
            cout<<i<<" ";
        }
    }
}
```
