## 栈
### 嵌套解码
给定一个经过编码的字符串，返回它解码后的字符串。
编码规则为: k[encoded_string]，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。
```cpp
class Solution {
public:
    string decodeString(string s) {
        // 定义一个存储pair的栈
        stack<pair<int, string>> st;
        string res = "";
        int multi = 0;
        for (char c : s) {
            // 左括号,如果出现嵌套需要保存已经出现的字符串
            if (c == '[') {
                pair<int, string> pa(multi, res);
                st.push(pa);
                res = "";
                multi = 0; // 清空multi
            // 右括号
            } else if (c == ']') {
                pair<int, string> p = st.top();
                st.pop();
                multi = p.first;
                string last_res = p.second;
                while (multi > 0) {
                    last_res.append(res);
                    multi--;
                }
                res = last_res;
            // encoded_string
            } else if (c >= 'a' && c <= 'z') { 
                res.push_back(c);
            // 重复次数
            } else if (c - '0' >= 0 && c - '0' <= 9) {
                multi = multi * 10 + (c - '0');
            }
        }

        return res;
    }
};
注：
```
很常见的字符串转数字方法:
```cpp
int multi=0;
for(ch:s){
    multi = multi*10+(ch-'0');
}
```
### 单调栈
在 O(n) 的时间复杂度内求出数组中各个元素右侧第一个更大的元素及其下标，然后一并得到其他信息。
对于数组arr[],维护一个栈stk;
从头遍历数组arr每一个元素：
    while(若栈不为空且当前数组元素大于栈顶元素)：
        <此时出栈元素的右侧第一个更大元素即为当前遍历元素i>
        将当前元素i保存
        出栈，继续执行while知道栈顶元素大于当前元素值或栈为空
        
    将arr下标入栈
结束   

如此操作，如果数组arr为递增元素，栈为进1出1
如果数组并非递增，在递减部分栈会将这些元素入栈，等到遍历到**开始递增的元素**，逐个出栈，直到栈中大于**这个元素**，出栈的所有元素的最近右更大元素即为**这个元素**/**或者视为对于遍历元素i，出栈后栈顶元素就是左边最近比他大的**

给定一个整数数组 temperatures ，表示每天的温度，返回一个数组 answer ，其中 answer[i] 是指对于第 i 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 0 来代替。
```cpp
/* stk只可能是连续日期*/
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        
        int n = temperatures.size();
        vector<int> res(n);
        stack<int> stk;
        for(int i=0; i<n;i++)
        {
            //对于temperatures[i],对于栈顶stk,从最远的一天往近找最近的warmer日期
            while(!stk.empty()&&temperatures[i]>temperatures[stk.top()])
            {
                int index = stk.top();
                res[index] = i - index;
                stk.pop();
            }   
            stk.push(i);
        }
        return res;
    }
};
```

```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int n = heights.size();
        vector<int> left(n),right(n);

        stack<int> s_l,s_r;
        // 左边最小
        for(int i = 0; i < n; i++){
            while(!s_l.empty()&&heights[i]<=heights[s_l.top()]){
                s_l.pop();
            }
            left[i] = (s_l.empty()?-1:s_l.top());
            s_l.push(i);
        }
        // 右边最小
        for(int i = n-1; i >= 0; i--){
            while(!s_r.empty()&&heights[i]<=heights[s_r.top()]){
                s_r.pop();
            }
            right[i] = (s_r.empty()?n:s_r.top());
            s_r.push(i);
        }
        int ans=0;
        for(int i = 0; i<n;i++)
        {
            ans = max(ans, (right[i]-left[i]-1)*heights[i]);
        }
        return ans;
    }
};
```