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
      // 对顶堆，考虑维护一个大根堆和小根堆，把比中位数大的都扔到小根堆里，比中位数小的扔到大根堆里，也就是说这两个堆大小不会超过1，那么要求中位数我只要考虑两个堆的堆顶就可以了， 是lc295
      
      // 考虑一下如果带删除怎么办 好像也有原题 是lc480
      // 用一个map然后lazy删除，所以需要记录一下堆的实际大小，每次调整两个堆大小的时候，查一遍map堆顶是否是已经被删除的元素，如果是的话就更新map然后重新从堆顶弹出
      class MedianFinder {
      public:
          /** initialize your data structure here. */
          priority_queue<int, vector<int>, greater<int> > small;
          priority_queue<int, vector<int>, less<int> >big;
          MedianFinder() {
      
          }
          
          void addNum(int num) {
              big.push(num);
              small.push(big.top());
              big.pop();
              if(small.size() > big.size()) {
                  big.push(small.top());
                  small.pop();
              }
          }
          
          double findMedian() {
              if(small.size() > big.size()) {
                  return small.top();
              } else if(small.size() < big.size()) {
                  return big.top();
              } else {
                  return (big.top() + small.top()) / 2.0;
              }
          }
      };
      ```

   9. 给一堆(x, y)的二维坐标，每一个坐标对应一个整形的score。以原点为圆心的，会有很多圆存在。需要找出一个圆，使得这个圆内的（包括圆上）所有score的和最大。

      ``` cpp
      // 应该就是求一个前缀最大值，用map维护，key代表到原点的平方和，value是圆周上score的和，然后求前缀的最大值就可以了。
      ```

   10. 判断一个点是否在三角形内，可以扩展到多变形，不自交多变形

       ``` cpp
       // 叉积
       // 这里就先说最简单判断三角形的情况，首先考虑怎么判断两个点是在直线的同侧还是在直线的两侧，假设有直线AB和两个点P，Q，如果AB*AP与AB*AQ，则说明PQ在直线同测，这里的乘法都是叉乘
       // 那么当一个点在三角形内部时，我们选择三角形任意一个顶点时，他们都在三角形另一条边的同侧
       // 那么就可以写出代码了（并没有测试
       struct Point {
           int x, y;
           Point(int xx, int yy) {x = xx; y = yy;}
           Point operator - (Point P) {
               return Point(x - P.x, y - P.y);
           }
       };
       
       int crossProduct(Point p, Point q) {
           return p.x * q.y - p.y * q.x;
       }
       
       bool isInRec(Point P, Point A, Point B, Point C) {
           if(crossProduct(P - B, B - C) * crossProduct(A - B, B - C) < 0) {
               return false;
           }
           if(crossProduct(P - A, A - C) * crossProduct(B - A, A - C) < 0) {
               return false;
           }
           if(crossProduct(P - A, A - B) * crossProduct(C - A, A - B) < 0) {
               return false;
           }
           return true;
       }
       ```

   11. 给出一个数组，求两个数异或的最大值

       ``` cpp
       // trie树的经典题，没什么好说的 原题在leetcode也有 421
       // 交了一波 我佛了 tle 
       // vector真的是慢，换数组就过了...
       class Solution {
       private:
           int tot = 0;
         	int trie[300005][2];
       public:
           int findMaximumXOR(vector<int>& nums) {
               int n = nums.size();
               //vector<vector<int>>trie(n * 32, vector<int>(2, 0)); // 空间复杂度真是玄学
               for(int i = 0; i < n; ++i) {
                   // nums[i] insert to trie
                   int now = 0;
                   for(int j = 30; j >= 0; --j) {
                       int bit = (nums[i]>>j) & 1;
                       if(!trie[now][bit]) {
                           trie[now][bit] = ++tot;
                       }
                       now = trie[now][bit];
                   }
               }
               int ans = 0;
               for(int i = 0; i < n; ++i) {
                   int now = 0, res = 0;
                   for(int j = 30; j >= 0; --j) {
                       int bit = !((nums[i]>>j) & 1);
                       if(trie[now][bit]) {
                           res += (1<<j);
                           now = trie[now][bit];
                       } else {
                           now = trie[now][!bit];
                       }
                   }
                   ans = max(ans, res);
               }
               return ans;
           }
       };
       ```



   12. 统一全为1的子正方形个数

       ``` cpp
       // lc 1277 虽然是一道medium的dp，但是我居然完全没想到做法 和它一样的是lc 221 是完全一样的题
       
       ```

			13. 有一个01矩阵，计算所有数到最近1的最短距离

``` cpp
// bfs，一开始把所有1都塞进队列里就行了 ACWing 173
// (ACwing这个oj既有很多面试题，也有很多竞赛题，而且UI也很好看)
#include<bits/stdc++.h>
using namespace  std;
int main()
{
    ios::sync_with_stdio(false); cin.tie(0);
    int n, m; cin >> n >> m;
    vector<string>matrix(n);
    vector<vector<int>>dis(n, vector<int>(m, -1));
    int dir[4][2] = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    queue<pair<int,int> > q;
    for(int i = 0; i < n; ++i) {
        cin >> matrix[i];
        for(int j = 0; j < m; ++j) {
            if(matrix[i][j] == '1') {
                dis[i][j] = 0;
                q.push({i, j});
            }
        }
    }
    while(!q.empty()) {
        auto u = q.front(); q.pop();
        for(int i = 0; i < 4; ++i) {
            int ux = u.first, uy = u.second;
            int nx = u.first + dir[i][0], ny = uy + dir[i][1];
            if(nx < 0 || nx >= n || ny < 0 || ny >= m) {
                continue;
            }
            if(dis[nx][ny] == -1) {
                dis[nx][ny] = dis[ux][uy] + 1;
                q.push({nx, ny});
            }
        }
    }
    for(int i = 0; i < n; ++i) {
        for(int j = 0; j < m; ++j) {
            cout << dis[i][j] << " ";
        }
        cout << "\n";
    }
}
```

14. 347 topK问题

    ``` cpp
    // 用快排的思路可以On 获得前k个高频元素，这k个元素本身内部没有顺序 
    class Solution {
    public:
        void quickSort(vector<pair<int,int>>& count, int l, int r, int k, vector<int>& ans) {
            if(l > r || k == 0) { // 这里递归出口条件注意一下，因为l=r的时候还得维护一下ans...  k=0也得return
                return ;
            }
            int pos = rand() % (r - l + 1) + l;
            swap(count[pos], count[r]);
            int index = l - 1;
            for(int j = l; j <= r - 1; ++j) {
                if(count[j].second <= count[r].second) {
                    index = index + 1;
                    swap(count[index], count[j]);
                }
            }
            swap(count[index + 1], count[r]);
            int rNum = r - (index + 1) + 1;
            int lNum = index;
            //cout << lNum << " " << rNum << "\n";
            if(rNum <= k) {
                for(int i = index + 1; i <= r; ++i) {
                    ans.push_back(count[i].first);
                }
                quickSort(count, l, index, k - rNum, ans);
            } else {
                quickSort(count, index + 1, r, k, ans);
            }
        }
        vector<int> topKFrequent(vector<int>& nums, int k) {
            srand(time(NULL));
            vector<int>ans;
            unordered_map<int, int>cnt;
            for(auto x : nums) {
                cnt[x]++;
            }
            vector<pair<int, int>>count;
            for(auto x : cnt) {
                count.push_back(x);
            }
            int n = count.size();
            quickSort(count, 0, n - 1, k, ans);
            return ans;
        }
    };
    
    ```

15. KMP算法

    ``` cpp
    // 这个其实并没有考， 但是问的太多了，记录一下
    // 首先算前缀函数，直接背吧...  OI-wiki https://oi-wiki.org/string/kmp/
    vector<int> prefix_function(string s) {
      int n = (int)s.length();
      vector<int> pi(n);
      for (int i = 1; i < n; i++) {
        int j = pi[i - 1];
        while (j > 0 && s[i] != s[j]) j = pi[j - 1];
        if (s[i] == s[j]) j++;
        pi[i] = j;
      }
      return pi;
    }
    // 然后匹配 我不知道怎么写
    ```

16. 786 第K小分数

    ``` cpp
    // 看这个数据范围直接暴力就可以了，但是我不知道为什么lc上的难度是hard，而且评论区有人说n^2过不了的...
    class Solution {
    public:
        vector<int> kthSmallestPrimeFraction(vector<int>& arr, int k) {
            vector<pair<int, int>> nums;
            int n = arr.size();
            for(int i = 0; i < n; ++i) {
                for(int j = i + 1; j < n; ++j) {
                    nums.push_back({arr[i], arr[j]});
                }
            }
            sort(nums.begin(), nums.end(), [&](const pair<int, int>& x, const pair<int, int>& y) {
                return x.first * y.second < x.second * y.first;
            });
            return vector<int>({nums[k - 1].first, nums[k - 1].second});
        }
    };
    
    // 如果面试的话写暴力肯定不好，说一个二分的做法
    // 考虑二分答案mid，验证数组可以组成多少个<=mid的分数，二分到最后一定能得到一个mid，数组正好能组成k个<=mid的分数，取最大的那个即可，check的时候利用单调性，枚举分母，分子一定是单调不降的。
    class Solution {
    public:
        vector<int> kthSmallestPrimeFraction(vector<int>& arr, int k) {
            double l = 0, r = 1.0;
            vector<int>ans(2);
            while(r - l > 1e-9) {
                double mid = (l + r) / 2.0;
                vector<int>res = check(mid, arr);
                if(res[0] < k) {
                    l = mid;
                } else {
                    r = mid;
                    ans[0] = res[1]; ans[1] = res[2];
                }
            }
            return ans;
        }
    
        vector<int> check(double mid, vector<int>& arr) {
            vector<int> res;
            int count = 0, x = 0, y = 0, i = -1;
            for(int j = 1; j < arr.size(); ++j) {
                while(arr[i + 1] <= arr[j] * mid) {
                    ++i;
                }
                count += i + 1;
                if(i >= 0 && arr[i] * y >= arr[j] * x) {
                    x = arr[i]; y = arr[j];
                }
            }
            res.push_back(count); res.push_back(x); res.push_back(y);
            return res;
        }
    };
    ```

17. 543 二叉树的直径

    ``` cpp
    // 先dfs找一个最远点，再以这个点为root做一遍dfs，找到的就是直径，这个是一般做法，官方题解给的做法只能处理二叉树，一般情况下是n^2的...
    // lc上的树在dfs时只能向下访问，所以还得用vector重建一下
    class Solution {
    public:
        unordered_map<TreeNode*, vector<TreeNode*> >g;
        void build(TreeNode* u) {
            if(u->left) {
                g[u].push_back(u->left); g[u->left].push_back(u);
                build(u->left);
            }
            if(u->right) {
                g[u].push_back(u->right); g[u->right].push_back(u);
                build(u->right);
            }
        }
    
        int maxDep = 0;
        TreeNode* maxPoint = NULL;
        void dfs(TreeNode* u, TreeNode * fa, int dep) {
            if(dep > maxDep) {
                maxDep = dep;
                maxPoint = u;
            }
            for(auto v : g[u]) {
                if(v == fa) {
                    continue;
                }
                dfs(v, u, dep + 1);
            }
        }
        int diameterOfBinaryTree(TreeNode* root) {
            if(!root) return 0;
            build(root);
            dfs(root, NULL, 0);
            dfs(maxPoint, NULL, 0);
            return maxDep;
        }
    };
    ```

    

18. 1438 

    ``` cpp
    // 二分答案，然后随便写一个能维护区间最值的数据结构，复杂度是nloglog
    // 但是lc的medium一般没这么复杂，考虑尺取，维护最值直接用multiset就行
    // 补充:可以了解一下C++ 的反向迭代器和支持随机访问的迭代器
    class Solution {
    public:
        int longestSubarray(vector<int>& nums, int limit) {
            int n = nums.size();
            int l = 0, r = -1;
            multiset<int>s;
            int ans = 0;
            while(r < n - 1) {
                s.insert(nums[++r]);
                while(*s.rbegin() - *s.begin() > limit) {
                    s.erase(s.find(nums[l]));
                    ++l;
                }
                ans = max(ans, r - l + 1);
            }
            return ans;
        }
    };
    ```

19. 维护一个栈，支持三种操作，push一个点，pop一个点，输出最小矩形的左上和右下坐标，矩形要包住所有点

    ``` cpp
    // 把点的横纵坐标分开考虑，其实我们就是要维护4个值，所有点的minx, miny, maxx,maxy, 输出的就是(minx, miny), (maxx,maxy)
    // 那么其实我们要做的就是快速询问一个整数栈的最值是多少，leetcode有差不多的题 155
    class MinStack {
    public:
        /** initialize your data structure here. */
        stack<int>stk, helpStk;
        MinStack() {
    
        }
        
        void push(int x) {
            if(stk.empty() || x < helpStk.top()) {
                stk.push(x); helpStk.push(x);
            } else {
                stk.push(x); helpStk.push(helpStk.top());
            }
        }
        
        void pop() {
            stk.pop(); helpStk.pop();
        }
        
        int top() {
            return stk.top();
        }
        
        int getMin() {
            return helpStk.top();
        }
    };
    
    ```

    20. 字符串匹配，可以无序，比如S=abcdacb T=abc, 那么T在S中出现了2次

        ``` cpp
        // 分析一波，肯定和常规的字符串匹配算法无关，然后想暴力，枚举的每一个子串，那么验证的时候只需要比较每个字符出现的顺序就可以了，于是通过预处理前缀和就可以快速得到是否匹配， 复杂度O（CN），C是字符表大小
        
        ```

21. 表达式求值，用后缀表达式... follow up：支持三元运算符

22. 384 随机洗牌

    ``` cpp
        vector<int> shuffle() {
            int  n = card.size();
            for(int i = n - 1; i >= 0; --i) {
                int index = random() % (i + 1); // random一个0到i的index
                swap(card[index], card[i]);
            }
            return card;
        }
    // Fisher–Yates 算法，复杂度On
    // 为什么random的时候不是random一个[0, n - 1]的下标，如果random的范围是整个数组，那么一共有n^n种结果，而本质不同的排列个数是n!,当n>=2时，n!不能整除n^n，这说明每个排列一定不可能等概率出现，那么不是完全随机的。
    ```

    23. 889

    24. 给一个二叉树和一些结点，求这些结点的lca

        ``` cpp
        // 考虑树dp，记dp[u]为u的子树里有多少个给的结点，那么只要找到第一个结点包含所有给的结点，这个点就是lca，这样只要dfs一次就可以了
        ```

    25. 886 二分图 

        ``` cpp
        // 其实就是要你判断图里存不存在奇环，可以染色法+dfs，但是要注意这题图可能不联通，需要对每个块dfs
        class Solution {
        public:
            int color[2010];
            vector<int>g[2010];
            bool flg = true;
            void dfs(int u, int c) {
                color[u] = c;
                for(auto v: g[u]) {
                    if(color[v] == color[u]) {
                        flg = false;
                        return ;
                    } else if(color[v] == -1) {
                        dfs(v, 1 - c);
                    }
                }
            }
            bool possibleBipartition(int N, vector<vector<int>>& dislikes) {
                for(int i = 1; i <= N; ++i) {
                    color[i] = -1;
                }
                for(auto dislike : dislikes) {
                    int u = dislike[0], v = dislike[1];
                    g[u].push_back(v); g[v].push_back(u);
                }
                for(int i = 1; i <= N; ++i) {
                    if(color[i] == -1) {
                        dfs(i, 0);
                    }
                }
                return flg;
            }
        };
        ```

    26. 368 

        ``` cpp
        // n^2建图，然后按照拓扑序dp
        class Solution {
        public:
            vector<int> largestDivisibleSubset(vector<int>& nums) {
                int n = nums.size();
                sort(nums.begin(), nums.end());
                vector<int>pre(n, -1), dp(n, 1), deg(n, 0);
                vector<vector<int>>g(n, vector<int>());
                for(int i = 0; i < n; ++i) {
                    for(int j = i + 1; j < n; ++j) {
                        if(nums[j] % nums[i] == 0) {
                            g[i].push_back(j);
                            deg[j]++;
                        }
                    }
                }
                queue<int>q;
                for(int i = 0; i < n; ++i) {
                    if(deg[i] == 0) {
                        q.push(i);
                    }
                }
                int cnt = 0;
                while(!q.empty()) {
                    int u = q.front(); q.pop();
                    for(auto v : g[u]) {
                        deg[v]--;
                        if(deg[v] == 0) {
                            q.push(v);
                        }
                        if(dp[u] + 1 > dp[v]) {
                            dp[v] = dp[u] + 1;
                            pre[v] = u;
                        }
                    }
                }
        
                int index = max_element(dp.begin(), dp.end()) - dp.begin();
                vector<int> ans;
                while(index != -1) {
                    ans.push_back(nums[index]);
                    index = pre[index];
                }
                return ans;
            }
        };
        ```

    27. 给定一个deque（大小1e5），每次pop头部两个element，大的push_back，小的push_front，问第m次(1e18)操作结果？

        ``` cpp
        // 暂时没想到怎么做
        // 找到原题了，是一场div2的C，最后问的是第m次操作以后队列前两个数的值...
        // 这样想，当队首变成最小值的时候，之后就会形成n-1的循环节，然后队首什么时候会变成最小值呢？不会超过n次，其中n是队列大小 cf上的题号是 1180C
        ```

    28. 143 重排链表
    
        ``` cpp
        // 空间常数的做法，先找到链表中点，把中点右边的链表整体反转，然后把前后链表合并
        class Solution {
        public:
            // 空间复杂度O1,先找到中间结点，然后反转右链表，最后合并两个链表
            void reorderList(ListNode* head) {
                if(!head || !head->next) {
                    return ;
                }
                ListNode* middle = middleNode(head);
                ListNode* r = middle->next;
                r = reverseList(r);
                middle->next = NULL;
                mergeList(head, r);
            }
        
            ListNode* middleNode(ListNode* head) {
                ListNode* low = head;
                ListNode* fast = head;
                while(fast->next && fast->next->next) {
                    low = low->next;
                    fast = fast->next->next;
                }
                return low;
            }
            ListNode* reverseList(ListNode* head) {
                ListNode* now = head;
                ListNode* pre = NULL;
                while(now) {
                    ListNode* next = now->next;
                    now->next = pre;
                    pre = now;
                    now = next;
                }
                return pre;
            }
            ListNode* mergeList(ListNode* l, ListNode* r) {
                ListNode* nextL = l;
                ListNode* nextR = r;
                ListNode* res = l;
                while(l && r) {
                    nextL = l->next;
                    nextR = r->next;
        
                    l->next = r;
                    r->next = nextL;
                    l=nextL;
                    r=nextR;
                }
                return res;
            }
        };
        ```
    
    29. 表达式求值
    
        ``` cpp
        // 做法是转后缀表达式然后求值
        ```
    
        

后面还有一些数学题和智力题。。。

1. 一根木棍剪成3段，能组成一个三角形的概率是多少

   1/4 https://www.zhihu.com/question/350358483

   如果不用积分的知识怎么解释这个问题呢？

   我们设木棍的中点为mid，裁剪的两个位置分别为A和B，不失一般性，假设A在mid的左侧，那么要想组成一个三角形B必须在mid的右侧，这个概率是1/2。

   当A和B分别位于mid的两侧时，只有AB的距离小于木棍长度的一半才能组成三角形，且此时一定能组成三角形，这个概率同样是1/2。

   两者相乘，答案是1/4。

   

2.  猪喝水问题 ...  https://www.zhihu.com/question/60227816 直接看默认第一的回答

3. 有50个红球和50个白球，放进两个盒子，然后随机选择一个盒子随机摸一个球，怎么放取出红球的概率最大？

   答案是一个盒子放1个红球，然后剩下99个球都放到另一个盒子里，概率是74/99, 证明略

   

   补充一个题  有一个1，每次可以二等分或合并一个数，最少次数变成3个1/3，引申为尺规作图怎么近似三等分一个角

   https://www.zhihu.com/question/422817168

   