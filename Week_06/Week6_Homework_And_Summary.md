# Week6 Homework And Summary

### 64.最小路径和

**题目**：给定一个包含非负整数的 m x n 网格 grid ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。每次只能向下或者向右移动一步。

**示例**

```
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

**解题思路**

对于这种路径和问题，通常都是使用逆向思维自底向上求解。如下图所示，从3 * 3 的左上角 1 出发，只能向下/向右移动一步，则问题转化成了 
$$
result = Min(p(3) + nextMin, p(1) + nextMin)
$$
即最小路径和为：其左边的3出发得到的最小解 / 下方的1出发得到的最小解，两者中的最小解即是最终答案。

由此可找到问题的重复性，原问题可以分解成子问题。上面的方程式可以看到，如果我们已经提前算出了由3出发/由1出发的最小步数 **nextMin**，然后再加上3/1就可以得到，从左上角的1出发的最小步数。

于是我们使用逆向思维：自底向上的方法往回推导

![](../image/minpath.jpg)

```java
public int minPathSum(int[][] grid) {
	
    int row = grid.length;
    int col = grid[0].length;
    
    int[][] dp = new int[row][col];
    
    for(int i = 0; i < row; i++) {
        dp[i] = Arrays.copyOf(grid[i], grid[i].length);
    }
    
    //初始化最后一行,从倒数第二列开始
    for(int j = col - 2; j >= 0; j--) {
        //每个格子由其右边格子加1得到步数
        dp[row - 1][j] += dp[row - 1][j + 1];
    }
    
    //初始化最后一列，从倒数第二行开始
    for(int i = row - 2; i >= 0; i--) {
        dp[i][col - 1] += dp[i + 1][col - 1];
    }
    
    //开始倒推
    for(int i = row - 2; i >= 0; i--) {
        for(int j = col - 2; j >= 0; j--) {
            dp[i][j] += Math.min(dp[i + 1][j], dp[i][j + 1]);
        }
    }
    
    return dp[0][0];
}
```

### 91.解码方法

**题目**：一条包含字母 `A-Z` 的消息通过以下方式进行了编码：

```
'A' -> 1
'B' -> 2
...
'Z' -> 26
```

给定一个只包含数字的**非空**字符串，请计算解码方法的总数。题目数据保证答案肯定是一个 32 位的整数。s只包含数字，可能包含前导零

**解题思路**

当s以0开头时，无法正确编码，直接返回0。设 `dp[i]` 记录 前 i 个字母`s[0~i]` 的解码方法个数，**dp[0]\(空字符串) = 1**, **dp[1] = 1**。从第二位字符起有：

+ 当前位为0，前一位为1/2，当前位和前一位一起编码，dp[i] = dp[i - 2]，否则，无法继续编码，返回0
+ 前一位为1，当前位不为0，当前位单独解码 / 当前位和前一位一起解码，dp[i] = dp[i - 1] + dp[i - 2]
+ 前一位为2，当前位在1 ~ 6之间，当前位单独解码 / 当前位和前一位一起解码，dp[i] = dp[i - 1] + dp[i - 2]
+ 其他情况，当前位单独解码，dp[i] = dp[i - 1]

dp[i] 仅与 dp[i - 1] , dp[i - 2] 有关，省去数组空间开销，使用两个变量记录

```java
public int numDecodings(String s) {
    if(s == null || s.charAt(0) == '0')
        return 0;
    
    //dp[-1] = 1(空字符串), dp[0] = 1;
    //dp[i - 1] = cur, dp[i - 2] = pre
    int pre = 1, cur = 1;
    
    for(int i = 1;i < s.length(); i++) {
        int ch = s.charAt(i);
        int pch = s.charAt(i - 1);
        int tmp = cur;
        if(ch == '0') {
            //dp[i] = dp[i - 2]
            if(pch == '1' || pch == '2')
                cur = pre;
            else 
                return 0;           
        }
        else if(pch == '1' ||(pch == '2' && ch >= '1' && ch <= '6'))
            cur = cur + pre;
        pre = tmp;
    }
    return cur;
}
```

### 647.回文子串

**题目**：给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。

**解题思路**

**case1**：暴力解法，枚举字符串中的所有子串，检查其子串是否是回文串。时间复杂度O(n ^ 3)

```java
public int countSubstrings(String s) {
	int ans = 0;
    for(int i = 0; i < s.length(); i++){
        for(int j = i + 1; j < s.length(); j++) {
            if(isValid(s.substring(i, j)))
                ans++;
        }
    }
    return ans;
}
boolean isValid(String s) {
    if(s.length() == 1)
        return true;
    for(int i = 0, j = s.length() - 1; i < j; i++, j--) {
        if(s.charAt(i) != s.charAt(j))
            return false;
    }
    return true;
}
```

**case2** ：动态规划思路。动态规划通常从以下几个方面思考

+ 大问题是什么？
+ 子问题是什么？
+ 之间有什么联系？

在题目中，原问题是判断子串是否是回文串，然后统计原字符串中有几个回文串。当一个子串是回文串时，除掉子串首尾字符，得到的子串必须也是回文串，于是我们就得到了子问题。

子问题：设一个子串的两端索引为 i, j ，判断 s[i : j] 是否为回文串。使用二维数组记录已经求解出的子问题的解，从最基本情况开始逐步推导

当 s[i : j] 是回文串时，有：（i <= j ）

+ i == j，单个字符是回文串
+ i + 1 == j，两个字符，则有s[i] == s[j]
+ i + 1 < j，两个字符以上，则有 s[i] == s[j] && s[i + 1 : j - 1] （除掉首尾字符仍然是回文串）

时间复杂度 O(n ^ 2)，空间复杂度O(n ^ 2)

```java
public int countSubstrings(String s) {
    int ans = 0;
    int len = s.length();
    
    //初始化
    boolean[][] dp = new boolean[len][len];
    for(int i = 0; i < len; i++) {
        Arrays.fill(dp[i], false);
    }
    
    for(int j = 0; j < len; j++) {
        for(int i = 0; i <= j; i++) {
            if(i == j) {
                dp[i][j] = true;
                ans++;
            }
            else if(i + 1 == j && s.charAt(i) == s.charAt(j)){
                dp[i][j] = true;
                ans++;
            }
            else if(i + 1 < j && s.charAt(i) == s.charAt(j) && dp[i + 1][j - 1]){
                dp[i][j] = true;
                ans++;
            }
        }
    }
    
    return ans;
}
```

**case3**：二维数组降维，空间复杂度优化为O(n)。

```java
public int countSubstrings(String s) {
    int len = s.length();
    int ans = 0;
    boolean[] dp = new boolean[len];
    
    for(int j = 0; j < len; j++) {
        for(int i = 0; i <= j; i++) {
            if(i == j){
                dp[i] = true;
                ans++;
            }
            else if(i + 1 == j && s.charAt(i) == s.charAt(j)) {
                dp[i] = true;
                ans++;
            }
            else if(i + 1 < j && s.charAt(i) == s.charAt(j) && dp[i + 1]) {
                dp[i] = true;
                ans++;
            }
            else
                dp[i] = false;
        }
    }
    return ans;
}
```

