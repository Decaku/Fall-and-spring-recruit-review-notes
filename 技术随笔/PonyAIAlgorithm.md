小马智行的面试算法题太难了... 记录一下做法

下面的代码我都没有造测试样例



1. 二维平面上有n个点，找到三个点使它们组成三角形，并且所有其他点都不在这个三角形内部。

   ``` cpp
   // 对所有点按x排序后，找连续三个不共线的点，它们一定满足
   bool solve(vector<pair<int, int>> points)
   {
       sort(points.begin(), points.end());
       for(int i = 0; i < points.size() - 2; ++i) {
           int x1 = points[i].first, y1 = points[i].second;
           int x2 = points[i + 1].first, y2 = points[i + 1].second;
           int x3 = points[i + 2].first, y3 = points[i + 2].second;
           if((y2 - y1) * (x3 - x2) == (y3 - y2) *(x2 -x1)) {
               cout << x1 << " " << y1 << "\n";
               cout << x2 << " " << y2 << "\n";
               cout << x3 << " " << y3 << "\n";
               return true;
           }
       }
       return false;
   }
   
   // 然后还有一个O(N)的做法，先随便找三个不共线的点，然后遍历剩下的点，如果剩下的点里有在三角形内部的，就随便替换掉三角形的一个顶点，最后得到的三角形也一定满足题意
   ```

2. 给一个数组和一个k，你可以至多做k次操作，每次操作可以让数组里的一个数加1，最后你需要让众数的数量尽量大，询问最大值。

   [2, 4, 4, 4] k=3 返回4

   ``` cpp
   // 先对数组排序，然后考虑尺取法，考虑当前尺取的区间是[l,r]，如果当前区间的和加上k >= 区间最大值*区间长度，则右移r，否则右移l。正确性显然。
   int solve(vector<int>a, int k)
   {
       sort(a.begin(), a.end());
       int n = a.size();
       vector<int>sum(n);
       sum[0] = a[0];
       for(int i = 1; i < n; ++i) {
           sum[i] = sum[i - 1] + a[i];
       }
       int l = 0, r = 0, ans = 0;
       while(1) {
           if(l == n - 1) {
               break;
           } 
           int s = 0;
           if(l == 0) {
               s = sum[r];
           } else {
               s = sum[r] - sum[l - 1];
           }
           if(s + k >= a[r] * (r - l + 1) && r < n - 1) {
               ++r;
           } else {
               ++l;
           }
           ans = max(ans, r - l + 1);
       }
       return ans;
   }
   ```

3. 有一个n*m的二维地图，''.'表示安全区域,'@'表示有敌人，问有多少个d * d的子区域是安全的，即里面不能有敌人

   ```cpp
   // 求二维前缀和 然后暴力枚举
   
   int solve(vector<vector<char> >a, int d)
   {
       int n = a.size(), m = a[0].size();
       vector<vector<int> >sum(n, vector<int>(m));
       for(int i = 0; i < n; ++i) {
           for(int j = 0; j < m; ++j) {
               if(a[i][j] == '@') {
                   sum[i][j] = 1;
               } else {
                   sum[i][j] = 0;
               }
           }
       }
       for(int i = 0; i < n; ++i) {
           for(int j = 0; j < m; ++j) {
               if(i == 0 && j == 0) continue;
               else if(i == 0) sum[i][j] = sum[i][j - 1] + sum[i][j];
               else if(j == 0) sum[i][j] = sum[i - 1][j] + sum[i][j];
               else sum[i][j] = sum[i - 1][j] + sum[i][j - 1] - sum[i - 1][j - 1] + sum[i][j];
           }
       }
       int ans = 0;
       for(int i = 0; i + d < n; ++i) {
           for(int j = 0; j + d < m; ++j) {
               int x1 = i, y1 = j;
               int x2 = i + d, y2 = j + d;
               int s = 0;
               if(x1 == 0 && y1 == 0) s = sum[x2][y2];
               else if(x1 == 0) s = sum[x2][y2] - sum[x2][y1 - 1];
               else if(y1 == 0) s = sum[x2][y2] - sum[x1 - 1][y2];
               else s = sum[x2][y2] - sum[x1 - 1][y2] - sum[x2][y1 - 1] + sum[x1 - 1][y2 - 1];
               if(s == 0) ++ans;
           }
       }
       return ans;
   }
   
   ```

   4. 汉诺塔

   5. 给出n个区间，返回最大的不相交的区间数量

      ```cpp
      // 按右端点排序 然后贪心挑选区间，证明网上有很多
      int solve(vector<pair<int, int>> a)
      {
          sort(a.begin(), a.end(), [&](const pair<int, int>&x, const pair<int, int>& y) {
              return x.second < y.second;
          });
          int n = a.size(), ans = 0, l = a[0].first, r = a[0].second;
          for(int i = 1; i < n; ++i) {
              if(a[i].first > r){
                  ++ans;
                  l = a[i].first; r = a[i].second;
              }
          }
          return ans;
      }
      ```

   6. 实现一个单链表，额外附加一个setAll(x)操作，O(1)把链表里所有结点的值都变成x,之后插入的结点不受影响

      ```cpp
      // 主要是setAll操作... 可以考虑lazy更新，维护一个range和一个value，访问结点时如果下标在range内就直接返回value 但是这样做链表单点更新后就得把lazy tag清除
      
      
      ```

   7. 给出4个数，返回能否有一种顺序组成24点

      ``` cpp
      // https://leetcode-cn.com/problems/24-game/solution/24-dian-you-xi-by-leetcode-solution/  暴搜
      ```

   8. 动态维护数组的中位数，支持增加，删除和查询

      ``` cpp
      // 应该是对顶堆，不太知道具体题面是什么
      ```

   9. 给一堆(x, y)的二维坐标，每一个坐标对应一个整形的score。以原点为圆心的，会有很多圆存在。需要找出一个圆，使得这个圆内的（包括圆上）所有score的和最大。

      ``` cpp
      // 应该就是求一个前缀最大值，用map维护，key代表到原点的平方和，value是圆周上score的和，然后求前缀的最大值就可以了。
      ```

   10. 判断一个点是否在三角形内，可以扩展到多变形，不自交多变形

       ``` cpp
       // 叉积
       ```

   11. 







后面还有一些数学题和智力题。。。

1. 一根木棍剪成3段，能组成一个三角形的概率是多少

   1/4 https://www.zhihu.com/question/350358483

2. 