## 两个字符串的最长公共子序列

[OJ链接](https://www.lintcode.com/problem/longest-common-subsequence/description)

给出2个字符串，找出它们的最长公共子序列（LCS）。返回其长度

例子：

- For `"ABCD"` and `"EDCA"`, the LCS is `"A"` (or `"D"`, `"C"`), return `1`.
- For `"ABCD"` and `"EACB"`, the LCS is `"AC"`, return `2`.

### 

### 解答

假设两个字符串序列为`X`和`Y`，从结尾字符进行分析（设`n`和`m`为两个字符串的长度）：

- 如果结尾字符相等，那么问题转换为求序列`X(n-1)`和`Y(m-1)`的最长公共子序列，即：`LCS(X(n-1),Y(m-1)) + 1`
- 如果结尾字符不相等，那么问题转换成求`max(LCS(X(n-1),Y(m)),LCS(X(n),Y(m-1)))`

为了防止求重复子问题，可以使用二维矩阵state保存状态（即每个LCS），从左上角往右下角进行求解

复杂度：

- **时间复杂度**：`O(n * m)`
- 空间复杂度
  - **无状态压缩**：`O(n * m)`
  - **状态压缩**：`O(n)`或`O(m)`

> 也可以从起始字符进行分析

**无压缩状态**

```c++
    int longestCommonSubsequence(string &A, string &B) {
        // write your code here
        if(A.empty() || B.empty()) return 0;
        int len1 = A.length();
        int len2 = B.length();
        vector<vector<int>> state(len1, vector<int>(len2, 0));
        state[0][0] = A[0] == B[0] ? 1 : 0;
        for(int i = 1; i < len1; i++){
            state[i][0] = A[i] == B[0] ? 1 : state[i - 1][0];
        }
        for(int j = 1; j < len2; j++){
            state[0][j] = A[0] == B[j] ? 1 : state[0][j - 1];
        }
        for(int i = 1; i < len1; i++){
            for(int j = 1; j < len2; j++){
                state[i][j] = A[i] == B[j] ? (1 + state[i - 1][j - 1]) : max(state[i - 1][j], state[i][j - 1]);
            }
        }
        return state[len1 - 1][len2 - 1];
    }
};
```

**压缩状态**

```c++
    int longestCommonSubsequence(string &A, string &B) {
        // write your code 
        if(A == "" || B == "")  return 0;
        int len1 = A.length();
        int len2 = B.length();
        vector<int> state(len2,0);
        state[0] = A[0] == B[0] ? 1 : 0;
        //DP
        for(int j = 1;j < len2;j++) //第一行
            state[j] = A[0] == B[j] ? 1 : state[j - 1];
        for(int i = 1;i < len1;i++){
            int tmp = state[0],tmp2;
            state[0] = A[i] == B[0] ? 1 : state[0];
            for(int j = 1;j < len2;j++){
                tmp2 = state[j];
                state[j] = A[i] == B[j] ? 1 + tmp : max(state[j],state[j - 1]);
                tmp = tmp2;
            }
        }        
        return state[len2 - 1];
    }
};
```

