# 字节跳动

* 76 最小覆盖子串， 双指针暴力扫，注意题目里说的是要求s包含t里的所有字符，不是每种，而且大小写都存在，数组开60不要开26。

``` c++
// 官方题解代码比较优雅
class Solution {
public:
    unordered_map <char, int> ori, cnt;

    bool check() {
        for (const auto &p: ori) {
            if (cnt[p.first] < p.second) {
                return false;
            }
        }
        return true;
    }

    string minWindow(string s, string t) {
        for (const auto &c: t) {
            ++ori[c];
        }

        int l = 0, r = -1;
        int len = INT_MAX, ansL = -1, ansR = -1;

        while (r < int(s.size())) { // 只要判右指针就行了，左指针不用管。
            if (ori.find(s[++r]) != ori.end()) {
                ++cnt[s[r]];
            }
            while (check() && l <= r) {
                if (r - l + 1 < len) {
                    len = r - l + 1;
                    ansL = l;
                }
                if (ori.find(s[l]) != ori.end()) {
                    --cnt[s[l]];
                }
                ++l;
            }
        }

        return ansL == -1 ? string() : s.substr(ansL, len);
    }
};
```



* 粉刷房子

  ```c++
  // 简单版本可以直接暴力dp，复杂版本需要优化一下dp，但是这题是会员题，我交不了...
  ```

* 84 最大矩形面积

  ``` cpp
  // 用单调栈维护每个位置左边和右边第一个比它小的数的位置
  class Solution {
  public:
      int largestRectangleArea(vector<int>& heights) {
          int n = heights.size();
          vector<int>l(n), r(n);
          stack<int>stk;
          for(int i = 0; i < n; ++i) {
              while(!stk.empty() && heights[i] <= heights[stk.top()]) {
                  stk.pop();
              }
              l[i] = (stk.empty() ? -1 : stk.top());
              stk.push(i);
          }
  
          while(!stk.empty()) stk.pop();
  
          for(int i = n - 1; i >= 0; --i) {
              while(!stk.empty() && heights[i] <= heights[stk.top()]) {
                  stk.pop();
              }
              r[i] = (stk.empty() ? n : stk.top());
              stk.push(i);
          }
          int ans = 0;
          for(int i = 0; i < n; ++i){
              ans = max(ans, (r[i] - l[i] - 1) * heights[i]);
          }
          return ans;
      }
  };
  ```

  

  * 103 蛇形打印二叉树 

  ``` cpp
  // bfs 但是不像传统的bfs每次弹出一个结点，而是每层遍历，因为需要每层的信息，顺序的话先vector存然后考虑reverse
  class Solution {
  public:
      vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
          vector<vector<int>>ans;
          if(!root) return ans;
          queue<TreeNode*> q; q.push(root);
          bool isLeftOrder = true;
          while(!q.empty()) {
              vector<int>tem;
              int n = q.size();
              for(int i = 0; i < n; ++i) {
                  auto node = q.front(); q.pop();
                  tem.push_back(node->val);
                  if(node->left) {
                      q.push(node->left);
                  }
                  if(node->right) {
                      q.push(node->right);
                  }
              }
              if(!isLeftOrder) reverse(tem.begin(), tem.end());
              ans.emplace_back(vector<int>{tem.begin(), tem.end()});
              isLeftOrder = !isLeftOrder;
          }
          return ans;
      }
  };
  
  ```

  

  * 236 二叉树的最近公共祖先

    ``` cpp
    // dfs预处理深度和父结点。先让两个结点跳到同一高度，然后暴力同时往上跳到同一个结点就是答案了。 这题每个点的值都不同，所以可以拿来做结点编号，但是最后还是让你返回结点指针... 
    // 其实LCA可以On预处理然后log询问，倍增和重链剖分
    
    class Solution {
    public:
        map<int, int> dep;
        map<int, int> fa;
        map<int, TreeNode* > ma;
        void dfs(TreeNode* u) {
            ma[u->val] = u;
            if(u->left) {
                dep[u->left->val] = dep[u->val] + 1;
                fa[u->left->val] = u->val;
                dfs(u->left);
            }
            if(u->right) {
                dep[u->right->val] = dep[u->val] + 1;
                fa[u->right->val] = u->val;
                dfs(u->right);
            }
        }
        TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
            if(!root) return NULL;
            dep[root->val] = 0;
            dfs(root);
            int u = p->val, v = q->val;
            while(dep[u] > dep[v]) u = fa[u];
            while(dep[v] > dep[u]) v = fa[v];
            while(u != v) {
                u = fa[u];
                v = fa[v];
            }
            return ma[u];
        }
    };
    ```

  * 23 合并k个有序链表

    ``` cpp
    // 用堆搞搞 这里要强调的是 返回值是链表的题 head结点可以是空，返回head->next 这样会好写点
    class Solution {
    public:
        struct Status {
            int val;
            ListNode* ptr;
            bool operator < (const Status& x) const {
                return val > x.val;
            }
        };
        ListNode* mergeKLists(vector<ListNode*>& lists) {
            priority_queue<Status>q;
            for(auto node : lists) {
                if(node) {
                    q.push({node->val, node});
                }
            }
            ListNode* head = new ListNode();
            ListNode* now = head;
            while(!q.empty()) {
                auto u = q.top(); q.pop();
                if(u.ptr->next) {
                    q.push({u.ptr->next->val, u.ptr->next});
                }
                now->next = new ListNode(u.val);
                now = now->next;
            }
            return head->next;
        }
    };
    ```



* 518 零钱兑换

  ``` cpp
  // 求完全背包的方案数， 注意枚举空间的时候正序
  // 类似的题有个322 也是完全背包
  class Solution {
  public:
      int change(int amount, vector<int>& coins) {
          int n = coins.size();
          vector<int>dp(amount + 100);
          dp[0] = 1;
          for(int i = 0; i < n; ++i) {
              for(int j = 1; j <= amount; ++j) {
                  if(j < coins[i]) continue;
                  dp[j] += dp[j - coins[i]];
              }
          }
          return dp[amount];
      }
  };
  ```

* 105根据前序和中序遍历还原二叉树

  ``` cpp
  // 前序遍历的第一个值一定是根，找到它在中序遍历中的位置，然后递归左右子树
  class Solution {
  public:
      unordered_map<int, int>index;
      TreeNode* build(vector<int>& pre, vector<int>& post, int preL, int preR, int postL, int postR) {
          if(preL > preR) {
              return NULL;
          }
          TreeNode* root = new TreeNode(pre[preL]);
          //cout << root->val << "\n";
          int sizeL = index[pre[preL]] - postL;
          root->left = build(pre, post, preL + 1, preL + sizeL, postL, index[pre[preL]] - 1);
          root->right = build(pre, post, preL + sizeL + 1, preR, index[pre[preL]] + 1, postR);
          return root;
      }
      TreeNode* buildTree(vector<int>& pre, vector<int>& post) {
          int n = post.size();
          for(int i = 0; i < post.size(); ++i) {
              index[post[i]] = i;
          }
          return build(pre, post, 0, n - 1, 0, n - 1);
      }
  };
  
  ```

* 106  根据中后序还原二叉树

  ``` cpp
  // 和上面一样，只要考虑根是后序遍历最后一个元素
  // 如果是前后序没法唯一确定一个二叉树，反例，考虑1->2， 2可以是1的左儿子，或右儿子，但是它们的前序和后序遍历都一样
  class Solution {
  public:
      unordered_map<int, int>index;
      TreeNode* build(vector<int>& inorder, vector<int>& postorder, int inorderL, int inorderR, int postorderL, int postorderR) {
          if(inorderL > inorderR) {
              return NULL;
          }
          int sizeL = index[postorder[postorderR]] - inorderL;
          TreeNode* root = new TreeNode(postorder[postorderR]);
          root->left = build(inorder, postorder, inorderL, index[postorder[postorderR]] - 1, postorderL, postorderL + sizeL - 1);
          root->right = build(inorder, postorder, index[postorder[postorderR]] + 1, inorderR, postorderL + sizeL, postorderR - 1);
          return root;
      }
      TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
          int n = inorder.size();
          for(int i = 0; i < n; ++i) {
              index[inorder[i]] = i;
          }
          return build(inorder, postorder, 0, n - 1, 0, n - 1);
      }
  };
  ```

  

* 链表判环

  ``` cpp
  // 设置快慢指针，快指针每次走2格，慢指针每次走1格，如果最终在某个时间点相遇，说明有环。 这个算法叫floyd判环，还可以判断有限状态机以及迭代函数是否有环。 慢指针最多会在环上走一遍，在这个过程中两个指针必然相遇，所以时间复杂度是O（n）的。证明：https://binac.io/2017/12/01/cycle-detection/
  ```



* 42 接雨水

  ``` cpp
  // 维护一个单调递减栈， 每次从栈顶pop时，都累加栈顶下一个元素到当前位置i中间这层的答案，不算是单调栈裸题，应该算挺难想的了
  class Solution {
  public:
      int trap(vector<int>& height) {
          int n = height.size();
          int ans = 0;
          stack<int>stk;
          for(int i = 0; i < n; ++i) {
              while(!stk.empty() && height[stk.top()] < height[i]) {
                  int cur = stk.top();
                  stk.pop();
                  if(!stk.empty()) {
                      ans += (min(height[stk.top()], height[i]) - height[cur]) * (i - stk.top() - 1);
                  }
              }
              stk.push(i);
          }
          return ans;
      }
  };
  ```

*  rand5生成rand7

  ``` cpp
  // 可以想象一下一个5*5的数表，范围是1-25，那么可以使用两次rand5生成row和column，所以可以得到一个生成rand25的办法，然后如果生成的是22-25这个区间的数就舍弃重来，那么就得到一个rand21的做法，(rand21()-1)%7+1的范围就是在1-7之间了，可以证明这个做法期望次数是常数次。
  // 所以当使用randx生成randy，x<y且x^2>=y时，都可以规约到上面的做法，而x>=y时更简单...  可以思考一下x^2 < y 时该怎么做？
  
  
  ```

* 124 二叉树路径最大值

  ``` cpp
  // 树dp，考虑dp[u]表示u的子树中经过u的路径的最大值，且不能出现分叉，即不能出现左儿子->u->右儿子，然后讨论一下就行了... 但是注意更新答案的时候可以出现分叉
  class Solution {
  private:
      int ans = INT_MIN;
  public:
      int dfs(TreeNode* u) {
          if(!u) {
              return 0;
          }
          int l = max(dfs(u->left), 0);
          int r = max(dfs(u->right), 0);
          ans = max(ans, u->val + l + r);
          return u->val + max(l, r);
      }
      int maxPathSum(TreeNode* root) {
          dfs(root);
          return ans;
      }
  };
  ```

  ``` cpp
  // 上面的做法根本没开数组，写法上也非常trick， 下面是naive的写法
  class Solution {
  private:
      int ans = INT_MIN;
      map<TreeNode*, int>dp;
  public:
      void dfs(TreeNode* u) {
          dp[u] = u->val;
          if(u->left) {
              dfs(u->left);
          }
          if(u->right) {
              dfs(u->right);
          }
          ans = max(ans, dp[u]);
          ans = max(ans, dp[u] + dp[u->left]);
          ans = max(ans, dp[u] + dp[u->right]);
          ans = max(ans, dp[u] + dp[u->left] + dp[u->right]);
          dp[u] = max({dp[u], dp[u] + dp[u->left], dp[u] + dp[u->right]});
      }
      int maxPathSum(TreeNode* root) {
          dfs(root);
          return ans;
      }
  };
  ```



* 394 字符串解码

  ``` cpp
  // 超级大暴力 + 模拟 看题解也没啥好做法
  class Solution {
  public:
      string decodeString(string s) {
          int n = s.size();
          stack<char>stk;
          for(int i = 0; i < n; ++i) {
              if(s[i] != ']') {
                  stk.push(s[i]);
              } else {
                  string tem = "";
                  while(stk.top() != '[') {
                      tem.push_back(stk.top());
                      stk.pop();
                  }
                  reverse(tem.begin(), tem.end());
                  stk.pop();
                  string count_s = "";
                  while(!stk.empty() && stk.top() >= '0' && stk.top() <= '9') {
                      count_s.push_back(stk.top());
                      stk.pop();
                  }
                  reverse(count_s.begin(), count_s.end());
                  int count = 0;
                  for(int j = 0; j < count_s.size(); ++j) {
                      count = count * 10 + count_s[j] - '0';
                  }
                  for(int j = 0; j < count; ++j) {
                      for(int k = 0; k < tem.size(); ++k) {
                          stk.push(tem[k]);
                      }
                  }
              }
          }
          string ans = "";
          while(!stk.empty()) {
              ans.push_back(stk.top());
              stk.pop();
          }
          reverse(ans.begin(), ans.end());
          return ans;
      }
  };
  ```

* 33 搜索旋转数组

  ``` cpp
  // 本质上是在两段单调的区间上搜索target，且右边区间的最大值小于左边区间的最小值，那么只要魔改一下二分就行了
  class Solution {
  public:
      int search(vector<int>& nums, int target) {
          int n = nums.size();
          int l = 0, r = n - 1;
          while(l + 1 < r) {
              int mid = (l + r) / 2;
              if(nums[mid] > nums[l]) {
                  if(target > nums[mid] || target < nums[l]) {
                      l = mid;
                  } else {
                      r = mid;
                  }
              } else {
                  if(target > nums[mid] && target < nums[l]) {
                      l = mid;
                  } else {
                      r = mid;
                  }
              }
          }
          if(nums[l] == target) {
              return l;
          } else if(nums[r] == target) {
              return r;
          } else {
              return -1;
          }
      }
  };
  ```

  