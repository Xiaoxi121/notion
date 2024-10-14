# 蓝桥

[C++ STL总结 | 行码棋 (wyqz.top)](https://wyqz.top/p/870124582.html)

6.2.2

27days

（5-10-15-20-25）*2

- [ ] 基础算法 4
- [ ] 填空题 3
- [ ] 搜索 4
- [ ] 图论算法和理论5
- [ ] 字符串匹配算法 4
  - [ ] 树形数据结构 2
  - [ ] 动态规划 2
    - [ ] 博弈论 1	
    - [ ] 背包	1
    - [ ] 基础数据结构 1	
    - [ ] 排序 1

## 2024.3.12

### 倍增

### 构造

### 位运算

<img src="D:\2024\Notes\Typora\Study\lanqiao\C++_STL.assets\image-20240312132335354.png" alt="image-20240312132335354" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Study\lanqiao\C++_STL.assets\image-20240312132549422.png" alt="image-20240312132549422" style="zoom:33%;" />

<img src="D:\2024\Notes\Typora\Study\lanqiao\C++_STL.assets\image-20240312132727526.png" alt="image-20240312132727526" style="zoom:33%;" />

<img src="D:\2024\Notes\Typora\Study\lanqiao\C++_STL.assets\image-20240312132841810.png" alt="image-20240312132841810" style="zoom:33%;" />

[[蓝桥杯\]真题讲解：异或和之和 （拆位、贡献法）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1NK421b7DA/?spm_id_from=333.788&vd_source=5f7e4c928f2ae7a33f99f74c695f9806)

## 2024.3.13

### 离散化

[1952. 金发姑娘和 N 头牛 - AcWing题库](https://www.acwing.com/problem/content/description/1954/)

```c++
#include <iostream>
#include <cstring>
#include <map>
#include <algorithm>

using namespace std;

const int INF = 2e9;

int n, x, y, z;

int main()
{
    map<int, int> b; // 离散化及差分数组
    
    cin >> n >> x >> y >> z;
    for (int i = 0; i < n; i ++ )
    {
        //给三个大区间 + c
        int l, r;
        cin >> l >> r;
        //[-INF,r)
        b[-INF] += x;
        b[l] -= x;
        //[l, r]
        b[l] += y;
        b[r + 1] -= y;
        //b(r, INF]
        b[r + 1] += z;
        b[INF] -= z;
    }
    int res = 0, sum = 0;
    // 枚举差分数组，求前缀和，更新最大值
    for(auto& [k, v] : b)// map的遍历方式
    {
        sum += v;// 前缀和
        res = max(res, sum);
    }
    
    /*for(auto item : b)// 这样遍历也可以
    {
        sum += item.second;// 前缀和
        res = max(res, sum);
    }
    */
    
    cout << res;
    
    return 0;
}

```

### 前缀和 差分

[前缀和与差分 图文并茂 超详细整理（全网最通俗易懂）_前缀和差分-CSDN博客](https://blog.csdn.net/weixin_45629285/article/details/111146240?ops_request_misc=%7B%22request%5Fid%22%3A%22171032265016800184149676%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=171032265016800184149676&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-4-111146240-null-null.142^v99^control&utm_term=前缀和、&spm=1018.2226.3001.4187)

[4.棋盘 - 蓝桥云课 (lanqiao.cn)](https://www.lanqiao.cn/problems/3533/learning/?page=1&first_category_id=1&second_category_id=3&tags=前缀和)

```c++
#include<bits/stdc++.h>

#define rep(i, a, b) for(int i = a; i < b; i++)

using namespace std;

const int N = 2010;
int a[N][N], d[N][N]; //构造差分数组，使得差分矩阵的前缀和 是 原数组a
int n, m;

void insert(int x1, int y1, int x2, int y2) //差分数组实现O(1)修改原数组（前缀和自动还原）
{
    d[x1][y1] += 1;
    d[x1][y2 + 1] -= 1;
    d[x2 + 1][y1] -= 1;
    d[x2 + 1][y2 + 1] += 1;

    return ;
}

int main()
{
    cin >> n >> m;
    //一开始的差分数组就是0
    
    while(m --)
    {
        int x1, y1, x2, y2;
        scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
        
        insert(x1, y1, x2, y2);
    }
    
    rep(i, 1, n + 1)
    {
        rep(j, 1, n + 1)
        {
            a[i][j] = a[i - 1][j] + a[i][j - 1] - a[i - 1][j - 1] + d[i][j]; 
            printf("%d", a[i][j] % 2);
        }
        puts("");
    }
            
    return 0;
}
```

## 





