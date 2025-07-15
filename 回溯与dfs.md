## 回溯
回溯法 采用试错的思想，它尝试分步的去解决一个问题。在分步解决问题的过程中，当它通过尝试发现现有的分步答案不能得到有效的正确的解答的时候，它将取消上一步甚至是上几步的计算，再通过其它的可能的分步解答再次尝试寻找问题的答案。回溯法通常用最简单的递归方法来实现，在反复重复上述的步骤后可能出现两种情况：
    找到一个可能存在的正确的答案；
    在尝试了所有可能的分步方法后宣告该问题没有答案。
回溯通常基于DFS实现，

**全排列**的原地回溯实现：
```cpp
class Solution {
public:
    vector<vector<int>>  res;
    vector<vector<int>> permute(vector<int>& nums) {
        dfs(nums, 0);
        return res;
    }
    void dfs(vector<int> &nums, int x){
        if(x == nums.size()){
            res.push_back(nums);
        }
        for(int i = x; i < nums.size(); i++){
            swap(nums[i],nums[x]);
            dfs(nums, x+1);
            swap(nums[i], nums[x]);
        }
    }
};
```
      1     2     3     4
    2 3 4 1 3 4 1 2 4 1 2 3
回溯的关键，就是通过swap函数在dfs后将改变的恢复过来每次从递归回退时候将状态恢复到进行递归之前。否则第一次深度遍历就会把nums原有的顺序打乱，就不能实现不重复的全排列。

**子集遍历**
```cpp
class Solution{
public:
    vector<vector<int>> ans;
    vector<int> t;
    void bfs(int cur,vector<int>& nums)
    {
        if(cur==nums.size()){
            ans.push_back(t);
            return;
        }
        t.push_back(nums[cur]);
        bfs(cur+1,nums);
        t.pop_back();
        bfs(cur+1,nums);
    }
    vector<vector<int>> subsets(vector<int>& nums){
        bfs(0,nums);
        return ans;
    }
};
```


通过`t.push_back(nums[cur]);`和`t.pop_back();`进行回溯分支，从第一个元素开始进行两个选项
(1):push_back这个元素
(2):再push_back后再pop出来，等于跳过这个元素
举例1,2,3
           []
     1           []
  2    []     2     []
3  [] 3  [] 3  []  3  [],相当于这个二叉树的所有出去根节点s分支

**电话号码排列组合** `2`~`9`对应`a`~`z`
```cpp
class Solution {
public:
    unordered_map<char,string> map;
    vector<string> ans;
    string t;
    vector<string> letterCombinations(string digits) {
        map['2'] = "abc";
        map['3'] = "def";
        map['4'] = "ghi";
        map['5'] = "jkl";
        map['6'] = "mno";
        map['7'] = "pqrs";
        map['8'] = "tuv";
        map['9'] = "wxyz";
        dfs(0,digits);
        return ans;
    }
    void dfs(int &idx, string &digits){
        if(idx == digits.length()){
            ans.push_back(t);
        }
        for(int i = 0; i<map[digits[idx]].length();i++)
        {
            t.push_back(digits);
            dfs(idx+1, digits);
            t.pop_back();
        }
    }
};
```
使用for循环，对于每个号码:
1. 加入第一个元素，继续dfs;
2. 回退，不加入这个元素，for循环加入第二个元素
3. 回退到不加入第二个元素，for循环加入第三个算符
4. ---

**组合总和**:给你一个 无重复元素 的整数数组 candidates 和一个目标整数 target ，找出 candidates 中可以使数字和为目标数 target 的 所有 不同组合 ，并以列表形式返回。你可以按 任意顺序 返回这些组合。candidates 中的 同一个 数字可以 无限制重复被选取 。如果至少一个数字的被选数量不同，则两种组合是不同的。 
```cpp
class Solution {
public:
    vector<int> t;
    vector<vector<int>> ans;
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        dfs(0,candidates,target);
        return ans;
    }
    void dfs(int idx, vector<int>& candidates, int target){
        if(idx == candidates.size()){
            ans.push_back(t);
            return;
        }
        for(int i=idx; i<candidates.size();i++){
            if(target -candidates[i]>=0){
                t.push_back(candidates[i]);
                dfs(i, candidates, target -candidates[i]);
                t.pop_back();
            }
        }
    }
};
```
和上一题类似，通过for循环和回溯制造每个candidates先选用，再递归分为：
1. 继续叠加自身
2. 采用下一个candidates
通过if进行剪枝，将没必要进行的递归筛去

**成对括号**:n对括号的所有可能
```cpp
class Solution {
public:
    string t;
    vector<string> ans;
    vector<string> generateParenthesis(int n) {
        dsp(n,0,0);
        return ans;
    }
    void dsp(int target, int open, int close){
        if(t.length() == (2*target)){
            ans.push_back(t);
            return;
        }
        if(open<target){
            t.push_back('(');
            dsp(target,open+1,close);
            t.pop_back();
        }
        if(close<open){
            t.push_back(')');
            dsp(target,open,close+1);
            t.pop_back();
        }
    }
};
```
我们用 DFS 枚举所有可能的括号序列，在每一步判断是否可以放 '(' 或 ')'，并使用回溯（pop_back()）撤销当前路径，探索其他分支，直到生成合法序列。
第一个pop选择加(还是加)，第二个pop恢复现场
```
t: "" | open: 0 | close: 0
│
├── '(' → t: "(" | open: 1 | close: 0
│   │
│   ├── '(' → t: "((" | open: 2 | close: 0
│   │   │
│   │   └── ')' → t: "(()" | open: 2 | close: 1
│   │       │
│   │       └── ')' → t: "(())" ✅ 结果之一
│   │
│   └── ')' → t: "()" | open: 1 | close: 1
│       │
│       └── '(' → t: "()(" | open: 2 | close: 1
│           │
│           └── ')' → t: "()()" ✅ 结果之二
```