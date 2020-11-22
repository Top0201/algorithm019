# Week3 Homework And Summary

### 递归

### 22.括号生成

**题目** ：数字 *n* 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

**示例**

 ```
输入：n = 3
输出：[
       "((()))",
       "(()())",
       "(())()",
       "()(())",
       "()()()"
     ]
 ```

**解题思路**

首先，不要人肉去思考递归过程，我们从最简单的思路思考，n = 3 时共有6个括号，即6个位置，穷举6个位置的可能性共有多少？

每一个位置的括号可能性为两种：左括号，右括号，则时间复杂度为 **O(2^n)**

```java
private List<String> ans = new ArrayList<>();

//考虑递归函数的参数
/*
pos : 当前的括号位置
max : 位置总和
s : 组合生成的字符串
*/
private void recur(int pos, int max, String s) {
    //递归终止条件
    if(pos >= max){
        ans.add(s);
        return;
    }
    
    //处理逻辑
    //该位置放左括号，继续下一个位置
    recur(pos + 1, max, s + "(");
	
    //该位置放右括号，继续下一个位置
    recur(pos + 1, max, s + ")");
        
}
public List<String> generateParenthesis(int n) {
    //起始递归
    recur(0, 2 * n, "");
}
```

可以看到，**recur()** 函数对于每个位置都有两种可能性的情况做了穷举。但是考察题目要求，对于 **n** 对括号，只有 n 个左括号和 n 个右括号可以用，在以上穷举的过程中，存在不合法的括号序列，那么如何在穷举过程中排除掉不合法序列呢？

对于合法性的判断，有以下两个条件：

+ 左括号可以随时添加，但是已经添加的左括号个数 **left < n** 才能添加新的左括号
+ 右括号需要和左括号配对，则在添加新的右括号时，需要满足 **right < left**

由于在递归枚举过程中需要对当前的左括号，右括号个数进行合法性判断，所以我们需要在递归过程中传递此信息

我们在判断字符串合法的过程称为 **剪枝**，用于减少不合理枚举个数，从而减少算法的时间复杂度

```java
private List<String> ans = new ArrayList<>();
/*
left : 当前最括号个数
right : 当前右括号个数
max : 括号对数
s : 当前字符串
*/
private void recur(int left, int right, int max, String s) {
	//递归终止条件
    //左右括号都枚举完
    if(left == max && right == max){
        ans.add(s);
        return;
    }
    
    //放左括号
    if(left < max)
        recur(left + 1, right, max, s + "(");
    //放右括号
    if(right < left)
        recur(left, right + 1, max, s + ")");
}
public List<String> generateParenthesis(int n) {
    recur(0, 0, n, "");
    return ans;
}
```

### 226.翻转二叉树

**题目** ：翻转一颗二叉树

**解题思路** ：二叉树的定义具有递归性质。对于根节点和其左右子树而言，根节点保持不变，交换其左右子树结点；左右子树重复此过程

```java
private void invert(TreeNode root){
    if(root == null)
        return ;
    //交换左右子树
    TreeNode tmp = root.left;
    root.left = root.right;
    root.right = tmp;
    
    invert(root.right);
    invert(root.left);
    return;
}
public TreeNode invertTree(TreeNode root) {
   	invert(root);
    return root;       
}
```

### 98.验证二叉搜索树

**题目** ：给定一个二叉树，判断其是否是一个有效的二叉搜索树。

**解题思路** ：根据二叉搜索树的性质，左子树关键字 < 根节点关键字 < 右子树关键字。在递归遍历过程加入判断条件即可

```java 
private TreeNode pre = null;
private boolean isValidBST(TreeNode root) {
    if(root == null)
        return true;
    
    //判断左子树
    if(!isValidBST(root.left))
        return false;
    //关键字不合法
    if(pre != null && root.val <= pre.val)
        return false;
    pre = root;
    return isValidBST(root.right);
}
```

### 111.二叉树的最小深度

**题目** ：给定一个二叉树，找出其最小深度。最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

**解题思路**：思考最基本情况，对于叶子结点来说，其深度为 **0**，对于非叶子结点来说，其最小深度为 **1 + min(leftH, rightH)**。最小高度往上传递直到根节点。但是需要注意，当非叶子结点的一方子树为空时，其最小深度为非空子树的最小高度，需要进行特殊情况的处理

```java
public int minDepth(TreeNode root) {
    if(root == null)
        return 0;
    
    int leftH = minDepth(root.left);
    int rightH = minDepth(root.right);
    
    return (leftH != 0 && rightH != 0) ? 1 + Math.min(leftH, rightH) : 1 + Math.max(leftH, rightH);
}
```

### 297.二叉树的序列化和反序列化

### 236.二叉树的最近公共祖先

**题目** ：给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

**解题思路** ：对于两个结点的最近公共祖先 **root**，有以下几种情况

+ p, q 分别在 root 的左右子树中
+ p = root, 且 q 在 root 的左/右子树中
+ q = root, 且 p 在root 的左/右子树中

算法的思路如下：

+ 若当前结点 root 为 null，返回 null
+ 若 root == p，或 root == q，返回 root
+ 递归左右子树，分别求出 left，right 的返回值
+ 对于最终结果 ，需要判断 left，right 的非空性（空表示p，q不存在该子树中）
  + left == null && right != null，返回right
  + left != null && right == null，返回left
  + left != null && right != null，则p，q分别位于两侧，返回root
  + 其他情况返回 null

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    //遍历树的结点，看是否存在p，q结点
    //本质上还是树的遍历
    
    //由于root需要其左右子树的结果，使用后序遍历
    
    //递归终止条件
    if(root == null)
        return null;
    
    if(root == p || root == q)
        return root;
    
    //处理逻辑
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    
    if(left == null)
        return right;
    if(right == null)
        return left;
    if(left != null && right != null)
        return root;
    return null;
}
```

### 105.从前序与中序遍历序列构造二叉树

**题目** ：根据一棵树的前序遍历与中序遍历构造二叉树。树中没有重复元素

**解题思路**

前序序列中根节点是第一个元素，中序遍历中序列为 **左-根-右**，那么中序序列可以使用前序序列的根节点来分割左子树和右子树。考虑基本情况：

+ 在中序序列中找到前序序列的首元素位置，可得根结点的左子树和右子树序列
+ 对于左右子树序列的建树，递归此过程

```java
public TreeNode buildTree(int[] preorder, int[] inorder) {
    int n = preorder.length;
    return build(preorder, 0, n - 1, inorder, 0, n - 1);
}
//pl, pr : 标注前序序列的起点和终点
//il, ir : 标注中序序列的起点和终点

private TreeNode build(int[] preorder, int pl, int pr, int[] inorder, int il, int ir) {
    //递归终止条件
    if(pl > pr || il > ir)
        return null;
    
    //处理逻辑
    //构造根节点
    TreeNode root = new TreeNode(preorder[pl]);
    
    //找到中序序列中的根节点位置
    int mid = 0;
    for(int i = il; i <= ir; i++){
        if(inorder[i] == preorder[pl]){
            mid = i;
            break;
        }
    }
    
    //拼接左右子树
    root.left = build(preorder, pl, pl + mid - il, inorder, il, mid - 1);
    root.right = build(preorder, pl + mid - il + 1, pr, inorder, mid + 1, ir);
    return root;
}
```

### 77.组合

**题目**：给定两个整数 *n* 和 *k*，返回 1 ... *n* 中所有可能的 *k* 个数的组合

**解题思路** ：从 n 个数中选择 k 个数，则每个数都有 **选与不选** 两种可能，且最多只能选两个数。

```java
private List<List<Integer>> ans = new ArrayList<>();
private void combination(LinkedList<Integer> tmp, int pos, int n, int k) {
    //剪枝，不满k位数时返回
    if(tmp.size() + n - pos + 1 < k)
        return;
    //记录答案
    if(tmp.size() == k){
        ans.add(new LinkedList<Integer>(tmp));
        return;
    }
    
    //选择pos
    tmp.addLast(pos);
    combination(tmp, pos + 1, n, k);
    tmp.removeLast();
    
    //不选pos
    combination(tmp, pos + 1, n, k);
}
public List<List<Integer>> combine(int n, int k) {
    LinkedList<Integer> tmp = new LinkedList<>();
    combination(tmp, 1, n, k);
    return ans;
}
```

### 46.全排列

**题目** ：给定一个 **没有重复** 数字的序列，返回其所有可能的全排列

**解题思路** ：使用 **递归回溯** 的解法，探索所有可能的候选解。

```java
//backtrack的代码模板
backtrack(路径，选择列表){
    if(满足条件){
        result.add(路径);
        return;
    }
    
    for(选择 : 选择列表) {
        选择;
        backtrack(路径, 选择列表);
        撤销选择;           
    }    
}
```

```java
private List<List<Integer>> ans = new ArrayList<>();
//标记数组visit : 标记哪个数已经被选择
private void backtrack(LinkedList<Integer> tmp, int[] visit, int[] nums) {
    if(tmp.size() == nums.length){
        ans.add(new LinkedList<Integer>(tmp));
        return;
    }
    
    for(int i = 0; i < nums.length; i++){
        if(visit[i] == 1)
            continue;
        tmp.addLast(nums[i]);
        visit[i] = 1;
        backtrack(tmp, visit, nums);
        tmp.removeLast();
        visit[i] = 0;
    }
}
public List<List<Integer>> permute(int[] nums) {
    LinkedList<Integer> tmp = new LinkedList<>();
    int[] visit = new int[nums.length];
    backtrack(tmp, visit, nums);
    return ans;
}
```

子集，排列，组合的回溯代码模板

**子集（无重复元素）**

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> ans = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(ans, new LinkedList<Integer>(), nums, 0);
    return ans;
}

//start开始位置
private void backtrack(List<List<Integer>> ans, LinkedList<Integer> tmp, int[] nums, int start) {
    //每个路径tmp都是一个子集
    ans.add(new LinkedList<>(tmp));
    //每个位置都是选和不选两种情况
    for(int i = start; i < nums.length; i++){
        tmp.addLast(nums[i]);
        backtrack(ans, tmp, nums, i + 1);
        tmp.removeLast();
    }
}
```

**子集（有重复元素）**

```java
public List<List<Integer>> subsetsWithDup(int[] nums) {
    List<List<Integer>> ans = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(ans, new LinkedList<>(), nums, 0);
    return ans;
}

private void backtrack(List<List<Integer>> ans, LinkedList<Integer> tmp, int[] nums, int start) {
    ans.add(new LinkedList<>(tmp));
    
    for(int i = start; i < nums.length; i++){
        //排除重复元素
        if(i > start && nums[i] == nums[i - 1])
            continue;
        //选与不选
        tmp.addLast(nums[i]);
        backtrack(ans, tmp, nums, i + 1);
        tmp.removeLast();
    }
}
```

**全排列（无重复元素）**

```java
public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> ans = new ArrayList<>();
    int[] visit = new int[nums.length];
    backtrack(ans, new LinkedList<>(), nums, visit);
    return ans;
}

private void backtrack(List<List<Integer>> ans, LinkedList<Integer> tmp, int[] nums, int[] visit) {
    if(tmp.size() == nums.length) {
        ans.add(new LinkedList<>(tmp));
        return;
    }
    for(int i = 0; i < nums.length; i++){
        if(visit[i] == 1)
            continue;
        tmp.addLast(nums[i]);
        visit[i] = 1;
        backtrack(ans, tmp, nums, visit);
        tmp.removeLast();
        visit[i] = 0;
    }
}
```

**全排列（有重复元素）**

```java
public List<List<Integer>> permuteWithDup(int[] nums) {
    List<List<Integer>> ans = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(ans, new LinkedList<>(), nums, new int[nums.length]);
    return ans;
}
private void backtrack(List<List<Integer>> ans, LinkedList<Integer> tmp, int[] nums, int[] visit){
    if(tmp.size() == nums.length){
        ans.add(new LinkedList<>(tmp));
        return;
    }
    for(int i = 0; i < nums.length; i++){
        if(visit[i] == 1)
            continue;
        if(i > 0 && nums[i] == nums[i - 1] && visit[i - 1] == 0)
            continue;
        tmp.addLast(nums[i]);
        visit[i] = 1;
        backtrack(ans, tmp, nums, visit);
        tmp.removeLast();
        visit[i] = 0;
    }
}
```

### 47.全排列II

**题目** ：给定一个可包含重复数字的序列 `nums` ，**按任意顺序** 返回所有不重复的全排列。

**解题思路** ：类似于求解没有重复元素的 **全排列**，但是要注意重复元素的处理

```java
public List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> ans = new ArrayList<>();
    //排序后去重
    Arrays.sort(nums);
    backtrack(ans, new LinkedList<>(), nums, new int[nums.length]);
    return ans;
}
private void backtrack(List<List<Integer>> ans, LinkedList<Integer> tmp, int[] nums, int[] visit) {
    if(tmp.size() == nums.length){
        ans.add(new LinkedList<>(tmp));
        return;
    }
    for(int i = 0; i < nums.length; i++){
        if(visit[i] == 1)
            continue;
        //选择重复元素中的第一个
        if(i > 0 && nums[i] == nums[i - 1] && visit[i - 1] == 0)
			continue;
        tmp.addLast(nums[i]);
        visit[i] = 1;
        backtrack(ans, tmp, nums, visit);
        tmp.removeLast();
        visit[i] = 0;            
    }        
}
```

### 分治，回溯

分治其实是递归的一种，原先的大问题具有重复性子问题，我们可以将原问题分解成无重叠的子问题，然后分别解决子问题，最后再合并子问题的答案。

分治的代码模板如下：

```java
public void divideConquer(problem, param) {
    //递归终止
    if(problem is sovled) {
        printResult();
        return;
    }
    
    //处理当前层
    //分割子问题
    subproblems[] = splitProblems(problem);
    
    //下探下一层
    //分治子问题
    subresult1 = divideConquer(subproblems[0]);
    subresult2 = divideConquer(subproblems[1]);
    ...;
    //合并答案
    result = merge(subresult1, subresult2,...);
    
    //清理当前层
}
```

回溯采用试错的思想，尝试分步的去解决一个问题。在分步解决问题过程中，当其发现现有的分步答案不能得到有效或正确的答案时，会取消上一步甚至是上几步的计算，再通过其他的可能的分步解答再次尝试问题的答案。其需要在回到当前层之后进行 **撤销处理**

### 50.pow(x, n)

**题目** ：实现 `pow(x, n)` ，即计算 x 的 n 次幂函数。

**说明**

+ -100.0 < x < 100.0
+ n 是 32 位有符号整数，其数值范围是 [-2^31, 2^31 - 1]

**解题思路**

**case1** ：暴力解法。对 x 进行 n 次循环乘法，时间复杂度为 O(n)，超出时间限制

```java
public double myPow(double x, int n) {
	double ans = 1;
    for(int i = 0; i < Math.abs(n); i++)
        ans *= x;
    if(n < 0)
        ans = 1 / ans;
    return ans;
}
```

**case2** ：若求 **2^10**，其实是不需要10次乘法的，可以通过已经求出的 **2^5 * 2** 来得到。我们可以通过对 n 进行对半分来求子问题。但是要注意 n 可为偶数和奇数，在合并子问题时需要注意奇数偶数的问题。通过分解子问题，时间复杂度为 O(logn)，大大减少了乘法的次数

```java
private double pow(double x, long n) {
    //递归终止条件
    if(n == 0)
        return 1.0;
    if(n < 0)
        return 1 / myPow(x, -n);
    
    //分解子问题
    double half = myPow(x, n / 2);
    //合并子问题，处理奇数和偶数
    return (n % 2 == 0) ? half * half : half * half * x;    
}
//注意当 n = -2^31时，-n会int会溢出，需要扩大到long
public double myPow(double x, int n) {
    if(n == 0)
        return 1.0;
    return pow(x, n);
}
```

**case3** ：迭代法+快速幂

```java
public double myPow(double x, int n) {
    long N = n;  
    return n > 0 ? pow(x, N) : 1.0 / pow(x, -N);
}
private double pow(double x, long n) {
    //System.out.println(n);
    double ans = 1.0;
    while(n != 0){
        if(n % 2 == 1){
            ans *= x;
        }
        x *= x;
        n /= 2;
    }
    //System.out.println(ans);
    return ans;
}
```

### 78.子集

**题目** ：给定一组**不含重复元素**的整数数组 *nums*，返回该数组所有可能的子集（幂集）。

**解题思路** ：对于每个元素都有选择和不选择两种可能，然后每一次的递归进入下一层时都需要添加路径集合

```java
public List<List<Integer>> subsets(int[] nums) {
	List<List<Integer>> ans = new ArrayList<>();
    backtrack(ans, new LinkedList<>(), nums, 0);
    return ans;
}
private void backtrack(List<List<Integer>> ans, LinkedList<Integer> tmp, int[] nums, int start) {
    ans.add(new LinkedList<>(tmp));
    //下一个路径元素为当前元素的下一个元素
    for(int i = start; i < nums.length; i++){
        tmp.addLast(nums[i]);
        backtrack(ans, tmp, nums, i + 1);
        tmp.removeLast();
    }
    return ;
}
```

### 169.多数元素

**题目** ：给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。你可以假设数组是非空的，并且给定的数组总是存在多数元素。

**解题思路**

**case1** ：哈希表计数，当元素个数超过[n/2]时，返回该元素。时间复杂度O(n)，最坏情况下需要遍历所有元素，空间复杂度O(n)

```java
public int majorityElement(int[] nums){
    Map<Integer, Integer> cnt = new HashMap<>();
    int len = nums.length;
    int ans = 0;
    for(int num : nums){
        cnt.put(num, cnt.getOrDefault(num, 0) + 1);
        if(cnt.get(num) > len / 2){
            ans = num;
            break;
        }
    }
    return ans;
}
```

**case2** ：排序思路，对数组排序，使得相同元素相邻，既然数组中存在出现次数大于len/2的元素，那么该元素一定位于数组中位。时间复杂度为 O(nlogn)

```java
public int majorityElement(int[] nums) {
    Arrays.sort(nums);
    return nums[nums.length >> 1];
}
```

**case3** ：摩尔投票法思路。

+ 候选人初始化为 nums[0]，票数初始化为1
+ 当遇到与候选人相同的数，则票数 count++，否则 count--
+ 当票数为0时，更换候选人为当前 nums[i]，并初始化票数为1
+ 遍历完数组后，候选人即为多数元素

如何说明摩尔投票法的正确性呢？已知多数元素的个数大于len/2，其他元素个数总和<=len/2，则相当于**每个多数元素和其他元素两两抵消**，抵消到最后剩余的数一定是多数元素。时间复杂度为O(n)，空间复杂度为O(1)

```java
public int majorityElement(int[] nums) {
    int ans = nums[0];
    int count = 1;
    for(int i = 1; i < nums.length; i++){
        if(ans == nums[i])
            ++count;
        else if(--count == 0){
            ans = nums[i];
            count = 1;
        }
    }
    return ans;
}
```

### 17.电话号码的字母组合

**题目** ：给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

<img src="../image/telephone_keypad.png" style="zoom:50%;" />

**解题思路**

给定两个按键后，使用按键上的字母进行两两组合，本质上还有字母组合问题，我们使用递归组合代码模板解决该问题，但是需要提前预处理按键与字母的映射

```java
public List<String> letterCombinations(String digits) {
    if(digits.length() == 0){
        return ans;
    }
    backtrack("", digits, 0);
} 
private List<String> ans = new ArrayList<>();
private static Map<Character, String> mp = new HashMap<>();
//静态代码块初始化map
static{
    mp.put('2', "abc");
    mp.put('3', "def");
    mp.put('4', "ghi");
    mp.put('5', "jkl");
    mp.put('6', "mno");
    mp.put('7', "pqrs");
    mp.put('8', "tuv");
    mp.put('9', "wxyz");
}
//level : 递归层数
private void backtrack(String tmp, String digits, int level){
    //递归终止
    if(level == digits.length()){
        ans.add(tmp);
        return;
    }
    String str = mp.get(digits.charAt(level));
    for(int i = 0; i < str.length(); ++i){
        backtrack(tmp + str.charAt(i), digits, level + 1);
    }
}
```

