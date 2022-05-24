# Ants on a Stick
Kick Start Round C Problem 3(https://codingcompetitions.withgoogle.com/kickstart/round/00000000008cb4d1/0000000000b209bc)
## Problem Analysis
由于蚂蚁的初始位置P和朝向D是确定的，且蚂蚁的相对位置一直保持不变，那边如果已知每个时刻蚂蚁下落的方向，就可以获得蚂蚁下落的顺序。

根据蚂蚁的初始位置和朝向，以及木杆的长度L，可以获得蚂蚁下落的距离H。
- 如果初始蚂蚁朝左（D = 0），H = P;
- 如果初始蚂蚁朝右（D = 1），H = L - P;

由此可知在下落距离H时一定存在蚂蚁下落，那么如何确定此时下落蚂蚁的朝向呢？

可以根据下落距离排序，求得此时下落蚂蚁的朝向。但此时下落距离最小的蚂蚁并不一定是实际下落的蚂蚁，需要根据此时木杆上实际左右两端的蚂蚁的编号来确定。

由于题目已知没有蚂蚁站在相同的位置，那么最多有两个蚂蚁的下落距离相等，且朝向相反。

根据以上分析可以求得蚂蚁的下落顺序。

## Code
```
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Ant{
public:
    int pos;     // 蚂蚁的初始位置，即离左端的距离
    int direct;  // 蚂蚁的初始朝向
    int dropDis; // 蚂蚁的下落距离
    int inputNo; // 蚂蚁的输入编号
};

int main()
{
    int T;
    cin>>T;
    for(int t=1; t<=T; t++){
        int N,L;
        cin>>N>>L;
        vector<int> res;
        vector<Ant> ants(N);
        for(int i=0; i<N; i++){
            cin>>ants[i].pos>>ants[i].direct;
            ants[i].inputNo = i+1;
        }
     
        // 根据蚂蚁的初始位置排序，获得蚂蚁的相对位置关系
        sort(ants.begin(), ants.end(), [&](Ant a, Ant b){
                return a.pos<b.pos;
             });

        // 存储蚂蚁的相对位置关系
        vector<int> pos(N,0);
        for(int i=0; i<N; i++){
            pos[i] = ants[i].inputNo;
        }

        // 获取蚂蚁的下落距离，由初始朝向决定
        for(int i=0; i<N; i++){
            int leftdis = ants[i].pos;
            int rightdis = L-ants[i].pos;
            if(ants[i].direct == 0){
                ants[i].dropDis = leftdis;
            }else if(ants[i].direct == 1){
                ants[i].dropDis = rightdis;
            }
        }

        // 根据蚂蚁下落距离从小到大排序
        sort(ants.begin(), ants.end(), [&](Ant a,Ant b){
                return a.dropDis < b.dropDis;
             });

        // 获得蚂蚁的下落顺序
        int left=0,right=N-1;
        for(int i=0; i<N; i++){
            // 若存在两个蚂蚁的下落距离相等，且方向不同 
            if(i+1<N && ants[i].dropDis == ants[i+1].dropDis&& ants[i].direct != ants[i+1].direct){
                // 若当前时刻的木杆上剩余的蚂蚁中最左边蚂蚁的编号小于最右边蚂蚁
                if(pos[left] < pos[right]){ 
                    res.push_back(pos[left++]);  // 左边先下落
                    res.push_back(pos[right--]);
                }else{ 
                    //若当前时刻的木杆上剩余的蚂蚁中最左边蚂蚁的编号大于最右边蚂蚁
                    res.push_back(pos[right--]); // 右边先下落
                    res.push_back(pos[left++]);
                }
                i++;
            }else{ 
                //其他情况根据下落距离最小的蚂蚁朝向的方向不同，确定此时从左端还是右端选取下落的蚂蚁
                if(ants[i].direct == 0){
                    res.push_back(pos[left++]); // 左边
                }else{
                    res.push_back(pos[right--]); // 右边
                }
            }
        }
        // 输出
        cout << "Case #"<<t<<": "<<res[0];
        for(int i=1; i<N; i++){
            cout<<" "<<res[i];
        }
        cout << endl;
    }


    return 0;
}
```
