## 题目描述
Your task is to divide the numbers 1,2,...,n into two sets of equal sum.
## 输入
The only input line contains an integer n（1 ≤ n ≤ 10^6）.
## 输出
Print "YES", if the division is possible, and "NO" otherwise.
After this, if the division is possible, print an example of how to create the sets. First, print the number of elements in the first set followed by the elements themselves in a separate line, and then, print the second set in a similar way.
## 样例输入
### 【样例1】
```
7
```
### 【样例2】
```
6
```
## 样例输出
### 【样例1】
```
YES
4
1 2 4 7
3
3 5 6
```
### 【样例2】
```
NO
```
## 题解
构造即可，看着复杂，其实就是1~n分成两半，前一半奇分b偶分a后一半奇分a偶分b.
## code
```C++
#include<bits/stdc++.h>
using namespace std;
long long n,sum;
vector<long long>a,b;
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin>>n;
    sum=n*(n+1)/2;
    if(sum%2==1){
        cout<<"NO";
    }else{
        cout<<"YES"<<'\n';
        if(n%2==1){
            a.push_back(1);
            a.push_back(2);
            b.push_back(3);
            for(long long i=4;i<=3+(n-3)/2;i++){
                if(i%2==0){
                    a.push_back(i);
                }else{
                    b.push_back(i);
                } 
            }
            for(long long i=4+(n-3)/2;i<=n;i++){
                if(i%2==0){
                    b.push_back(i);
                }else{
                    a.push_back(i);
                } 
            }
        }else if(n%2==0){
            if(n==4){
                a.push_back(1);
                a.push_back(4);
                b.push_back(2);
                b.push_back(3);
            }
            else{
                a.push_back(1);
                a.push_back(2);
                a.push_back(3);
                b.push_back(6);
                a.push_back(4);
                a.push_back(8);
                b.push_back(5);
                b.push_back(7);
                for(long long i=9;i<=8+(n-8)/2;i++){
                    if(i%2==0){
                        a.push_back(i);
                    }else{
                        b.push_back(i);
                    } 
                }
                for(long long i=9+(n-8)/2;i<=n;i++){
                    if(i%2==0){
                        b.push_back(i);
                    }else{
                        a.push_back(i);
                    } 
                }
            }
        }
        cout<<a.size()<<'\n';
        for(long long&i:a){
            cout<<i<<" ";
        }
        cout<<'\n';
        cout<<b.size()<<'\n';
        for(long long&i:b){
            cout<<i<<" ";
        }
    }
    return 0;
}
```
