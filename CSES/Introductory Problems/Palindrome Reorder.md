## 题目描述
Given a string, your task is to reorder its letters in such a way that it becomes a palindrome (i.e., it reads the same forwards and backwards).
## 输入
The only input line has a string of length n（1 ≤ n ≤ 10^6） consisting of characters A–Z.
## 输出
Print a palindrome consisting of the characters of the original string. You may print any valid solution. If there are no solutions, print "NO SOLUTION".
## 样例输入
```
AAAACACBA
```
## 样例输出
```
AACABACAA
```
## 题解
构造即可，o用于判断有几个奇数个数的字母，若有两个及以上奇数个数的字母，则无法满足条件，false，若有一个，则把这个字母放在字符串中间，剩余偶数字母分两半分在字符串两边即可。
## code
```C++
#include <bits/stdc++.h>
using namespace std;
 
int main(){
    string t;
    cin>>t;
    vector<int> num(26,0);
    for(int i=0;i<t.size();i++){
        num[t[i]-'A']++;
    }
    string s;
    int o=-1;
    for(int j=0;j<26;j++){
        char ch='A'+j;
        if(num[j]%2==1){
            if(o!=-1){
                cout<<"NO SOLUTION";
                return 0;
            }
            o=j; 
        }else{
            for(int i=1;i<=num[j]/2;i++){
                s+=ch;
            }
        }
    }
    if(o!=-1){
        for(int i=1;i<=num[o];i++){
            s+=char('A'+o);
        }
        num[o]=0;
    }
    for(int j=25;j>=0;j--){
        char ch='A'+j;
        for(int i=1;i<=num[j]/2;i++){
            s+=ch;
        }
    }
    cout<<s<<'\n';
    return 0;
}
```
