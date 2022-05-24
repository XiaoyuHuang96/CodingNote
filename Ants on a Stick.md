# Ants on a Stick
Kick Start Round C Problem 3(https://codingcompetitions.withgoogle.com/kickstart/round/00000000008cb4d1/0000000000b209bc)
## Problem Analysis
由于蚂蚁的初始位置P和朝向D是确定的，且蚂蚁的相对位置一直保持不变，那边如果已知每个时刻蚂蚁下落的方向，就可以获得蚂蚁下落的顺序。

根据蚂蚁的初始位置和朝向，以及木杆的长度N，可以获得蚂蚁下落的距离H。
- 如果初始蚂蚁朝左（D = 0），H = P;
- 如果初始蚂蚁朝右（D = 1），H = N - P;

由此可知在下落距离H时一定存在蚂蚁下落，那么如何确定此时下落蚂蚁的朝向呢？

可以根据下落距离排序，求得此时下落蚂蚁的朝向。但此时下落距离最小的蚂蚁并不一定是实际下落的蚂蚁，需要根据此时木杆上实际左右两端的蚂蚁的编号来确定。

由于题目已知没有蚂蚁站在相同的位置，那么最多有两个蚂蚁的下落距离相等，且朝向相反。

根据以上分析可以求得蚂蚁的下落顺序。

## Code
```
c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main()
{
    int T;
    cin>>T;
    for(int t=1; t<=T; t++){
        int N,L;
        cin>>N>>L;
        vector<int> res;
        // ants[i][0] - 蚂蚁的初始位置，即离左端的距离
        // ants[i][1] - 蚂蚁的初始朝向
        // ants[i][2] - 蚂蚁的下落距离
        // ants[i][3] - 蚂蚁的输入编号
        vector<vector<int>> ants(N,vector<int>(4,0));
        for(int i=0; i<N; i++){
            cin>>ants[i][0]>>ants[i][1];
            ants[i][3] = i;
        }
        
        // 根据蚂蚁的初始位置排序，获得蚂蚁的相对位置关系
        sort(ants.begin(), ants.end(), [&](vector<int> a, vector<int> b){
                return a[0]<b[0];
             });
        
        // 存储蚂蚁的相对位置关系
        vector<int> pos(N,0);
        for(int i=0; i<N; i++){
            pos[i] = ants[i][3];
        }
        
        // 获取蚂蚁的下落距离，由初始朝向决定
        for(int i=0; i<N; i++){
            int leftdis = ants[i][0];
            int rightdis = L-ants[i][0];
            if(ants[i][1] == 0){
                ants[i][2] = leftdis;
            }else if(ants[i][1] == 1){
                ants[i][2] = rightdis;
            }
        }

        // 根据蚂蚁下落距离从小到大排序
        sort(ants.begin(), ants.end(), [&](vector<int> a,vector<int> b){
                return a[2] < b[2];
             });

        // 获得蚂蚁的下落顺序
        int left=0,right=N-1;
        for(int i=0; i<N; i++){
            // 若存在两个蚂蚁的下落距离相等，且方向不同 
            if(i+1<N && ants[i][2] == ants[i+1][2]&& ants[i][1] != ants[i+1][1]){
                // 若当前时刻的木杆上剩余的蚂蚁中最左边蚂蚁的编号小于最右边蚂蚁
                if(pos[left] < pos[right]){
                    res.push_back(pos[left++]);  // 左边先下落
                    res.push_back(pos[right--]);
                }else{//若当前时刻的木杆上剩余的蚂蚁中最左边蚂蚁的编号大于最右边蚂蚁
                    res.push_back(pos[right--]); // 右边先下落
                    res.push_back(pos[left++]);
                }
                i++;
            }else{//其他情况根据下落距离最小的蚂蚁朝向的方向不同，确定此时从左端还是右端选取下落的蚂蚁
                if(ants[i][1] == 0){
                    res.push_back(pos[left++]); // 左边
                }else{
                    res.push_back(pos[right--]); // 右边
                }
            }
        }
        // 输出坐标值，下标从1开始
        cout << "Case #"<<t<<": "<<res[0]+1;
        for(int i=1; i<N; i++){
            cout<<" "<<res[i]+1;
        }
        cout << endl;
    }
    return 0;
}
```
