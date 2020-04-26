## 单词转换的最少次数

[OJ链接](https://www.lintcode.com/problem/edit-distance/description)

给出两个单词word1和word2，计算出将word1 转换为word2的最少操作次数。

你总共三种操作方法：

- 插入一个字符
- 删除一个字符
- 替换一个字符

例如：给出 word1=`"mart"` 和 word2=`"karma"` 返回 `3`

### 

### 解答

假设`state[i][j]`表示单词`word1.substr(0,i + 1)`转换成单词`word2.substr(0,j + 1)`的最少操作数：

- 当`word1[i] == word2[j]`时，`state[i][j] = state[i - 1][j - 1]`
- 否则，可以进行三种操作：
  - 在`word1.substr(0,i + 1)`后面插入一个`word2[j]`，此时`state[i][j] = 1 + state[i][j - 1]`
  - 将`word1.substr(0,i + 1)`最后一个字符删除，此时`state[i][j] = 1 + state[i - 1][j]`
  - 将`word1.substr(0,i + 1)`最后一个字符替换成`word2[j]`，此时`state[i][j] = 1 + state[i - 1][j - 1]` 所以当`word1[i] != word2[j]`时，`state[i][j]`等于上面三者中的最小值

```c++
class Solution {
public:
    /**
     * @param word1: A string
     * @param word2: A string
     * @return: The minimum number of steps.
     */
    int minDistance(string &word1, string &word2) {
        // write your code here
        if(word1 == "" || word2 == ""){
            return word1 == "" ? word2.length() : word1.length();
        }
        int len1 = word1.length(), len2 = word2.length();
        vector<vector<int>> state(len1, vector<int>(len2, 0));
        state[0][0] = (word1[0] == word2[0]) ? 0 : 1;
        for(int i = 1; i < len1; i++){
            state[i][0] = (word1[i] == word2[0]) ? i : 1 + state[i - 1][0]; //删除或替换
        }
        for(int j = 1; j < len2; j++){
            state[0][j] = (word1[0] == word2[j]) ? j : 1 + state[0][j - 1];
        }
        for(int i = 1; i < len1; i++){
            for(int j = 1; j < len2; j++){
                if(word1[i] == word2[j]) state[i][j] = state[i - 1][j - 1];
                else{
                    int time1 = 1 + state[i][j - 1]; //插入
                    int time2 = 1 + state[i - 1][j - 1]; //替换
                    int time3 = 1 + state[i - 1][j]; //删除
                    state[i][j] = min(time1, min(time2, time3));
                }
            }
        }
        return state[len1 - 1][len2 - 1];
    }
};
```

