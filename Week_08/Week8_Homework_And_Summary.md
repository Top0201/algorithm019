# Week8 Homework And Summary

### 191.位1的个数

**题目**：编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数（也被称为汉明重量）。

**解法**

**case1**：循环遍历数字的二进制32位数字，记录1的个数。循环32次，时间复杂度为O(1)，空间复杂度为O(1)

```java
public int hammingWeight(int n) {
    //java没有无符号整数，但是其底层二进制表示不变
	int cnt = 0;
    int mask = 1;//掩码
    for(int i = 0; i < 32; i++) {
        if((n & mask) == 1) 
            cnt++;
        mask <<= 1;
    }
    return cnt;
}
```

**case2**：使用位运算小技巧。对解法1进行优化，不再检查每一位数字，而是不断的把数字的最后一位1清0，直到数字变成0为止。清0的个数便是数字1的个数。

运算 **n & (n - 1)** 会把数字n的最后一位1清零，考虑其二进制表示如下：
$$
n = ...110100
$$

$$
n - 1 = ...110011
$$

$$
n\&(n-1) =...110000
$$

二进制表示中，n的最低位1总是对应n-1中的0。该运算保持n的其他位不变，最低位1清0。时间复杂度O(1)，循环次数和数字n中1的个数有关。空间复杂度O(1)

```java
public int hammingWeight(int n) {
    int cnt = 0;
    while(n != 0) {
        cnt++;
        //清0最低位1
        n &= (n - 1);
    }
    return cnt;
}
```

### 231.2的幂

**题目**：给定一个整数，编写一个函数来判断它是否是 2 的幂次方。

**解题思路**：当整数为2的幂时，其二进制表示1的个数只有一次。

```java
public boolean isPowerOfTwo(int n) {
    return n > 0 && ( n & (n - 1)) == 0;
}
```

### 190.颠倒二进制位

**题目**：颠倒给定的 32 位无符号整数的二进制位。

**解题思路**

**case1**：取模求和。对数字n不断取模，求其2的幂和。注意，java中没有无符号整数，取模时应该使用位运算，%运算会负数溢出

```java
public int reverseBits(int n) {
	int res = 0;
    for(int i = 0; i < 32; i++) {
        //res不断左移加n的取模结果，空出最左边的一位
        res = (res << 1) + (n & 1);
        n >>= 1; //不断右移
    }
    return res;
}
```

**case2**：按位翻转，直接颠倒每一位数字。32位int型整数n，第 i 位为1 / 0，则 res 的第 31 - i 位为1 / 0（颠倒）

```java
public int reverseBits(int n) {
    int res = 0;
    for(int i = 0; i < 32; i++) {
        //判断n的第i位，从最低位开始
        //使用异或操作，进行加法不进位操作，只加相应位数
        res ^= (n & (1 << i)) == 0 ? 0 : 1 << (31 - i);
    }
}
```

**case3**：分治合并。int型整数共32位，可以采用分治思想，反转左右16位，然后反转16位中的左右8位，依次类推，最后反转2位，合并反转即可。合并使用 | 或运算符

```java
public int reverseBits(int n) {
    //16位反转，注意使用无符号右移
    n = (n >>> 16) | (n << 16);
    //8位反转，使用掩码取16位中的8位
    n = ((n & 0xff00ff00) >>> 8) | ((n & 0x00ff00ff) << 8);
    //4位反转
    n = ((n & 0xf0f0f0f0) >>> 4) | ((n & 0x0f0f0f0f) << 4);
    //2位反转
    n = ((n & 0xcccccccc) >>> 2) | ((n & 0x33333333) << 2);
    //1位反转
    n = ((n & 0xaaaaaaaa) >>> 1) | ((n & 0x55555555) << 1);
    return n;
}
```

### 51.N皇后

**题目**：*n* 皇后问题研究的是如何将 *n* 个皇后放置在 *n*×*n* 的棋盘上，并且使皇后彼此之间不能相互攻击。

**解题思路**：

**case1**：参照8皇后的做法，使用递归回溯的方法探索所有放法。在放置一个皇后时，需要判断当前所放位置的行，列，两条对角斜线上是否已经有皇后。为了加快判断速度，使用三个集合 column, pie, na 来记录列和两条对角线的皇后位置

```java
public List<List<String>> solveNQueens(int n) {
    List<List<String>> ans = new ArrayList<>();
    //存放结果
    int[] res = new int[n];
    Arrays.fill(res, -1);
    
    //集合
    Set<Integer> col = new HashSet<>();
    Set<Integer> pie = new HashSet<>();
    Set<Integer> na = new HashSet<>();
    
    backtrack(ans, res, n, 0, col, pie, na);
    return ans;
}

public void backtrack(List<List<String>> ans, int[] res, int n, int row, Set<Integer> col, Set<Integer> pie, Set<Integer> na) {
    //放完
    if(row == n){
        List<String> board = generateBoard(res, n);
        ans.add(board);
        return ;
    }
    
    //尝试放皇后
    for(int i = 0; i < n; i++) {
        if(col.contains(i) || pie.contains(row + i) || na.contains(row - i)) 
            continue;
        
        res[row] = i;
        col.add(i);
        pie.add(row + i);
        na.add(row - i);
        backtrack(ans, res, n, row + 1, col, pie, na);
        
        //回溯
        res[row] = -1;
        col.remove(i);
        pie.remove(row + i);
        na.remove(row - i);       
    }
}
//生成棋盘结果
public List<String> generateBoard(int[] res, int n) {
    List<String> board = new ArrayList<>();
    //生成n行棋盘行
    for(int i = 0; i < n; i++) {
        char[] row = new char[n];
        Arrays.fill(row, '.');
        row[res[i]] = 'Q';
        board.add(new String(row));
    }
    return board;
}
```

**case2** ：基于位运算的回溯。解法一种使用三个集合来记录列和两个对角线上皇后的位置，空间复杂度为O(N)。若利用位运算来记录皇后的信息，可以将空间复杂度从O(N)降为O(1)。使用三个整数分别记录列和两个对角线上皇后的位置信息，每个整数有N个二进制位

```java
public List<List<String>> solveNQueens(int n) {
    //皇后放置列数组
    int[] res = new int[n];
    Arrays.fill(res, -1);
    List<List<String>> ans = new ArrayList<>();
    solve(ans, res, n, 0, 0, 0, 0);
    return ans;
}

public void solve(List<List<String>> ans, int[] res, int n, int row, int col, int pie, int na) {
    if(row == n) {
        List<String> board = generateBoard(res, n);
        ans.add(board);
        return ;
    }
    //计算可以放置的位置
    int pos = ((1 << n) - 1) & (~(col | pie | na));
    //当可以放置时
    while (pos != 0) {
        //获得最低位1
        int tmp = pos & (-pos);
        //将最低位置0，表示已经放置皇后
        pos = pos & (pos - 1);
        //记录皇后放置位置,注意列的下标从0开始
        res[row] = Integer.bitCount(tmp - 1);
        //注意对角线要进行相应的左移和右移
        solve(ans, res, n, row + 1, col | tmp, (pie | tmp) << 1, (na | tmp) >> 1);
        //回溯
        res[row] = -1;
        
    }
}

public List<String> generateBoard(int[] res, int n) {
    List<String> board = new ArrayList<>();
    for(int i = 0; i < n; i++) {
        char[] row = new char[n];
        Arrays.fill(row, '.');
        row[res[i]] = 'Q';
        board.add(new String(row));
    }
    return board;
}
```



### 146.LRU缓存机制

```java
class LRUCache {
    private static class DLinkNode {
        int key, value;
        DLinkNode prev, next;
        DLinkNode(){}
        DLinkNode(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private Map<Integer, DLinkNode> cache = new HashMap<>();
    private DLinkNode head = new DLinkNode();
    private DLinkNode tail = new DLinkNode();
    private int size; //缓存当前大小
    private int capacity; //缓存容量

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.size = 0;
        head.next = tail;
        tail.prev = head;
    }
    
    public int get(int key) {
        DLinkNode node = cache.get(key);
        //是否存在结点
        if(node == null)
            return -1;
        
        //将结点移动到头部
        moveToHead(node);
        return node.value;
    }
    
    public void put(int key, int value) {
        DLinkNode node = cache.get(key);

        if(node == null) {
            DLinkNode newNode = new DLinkNode(key, value);
            cache.put(key, newNode);
            addToHead(newNode);
            size++;

            //检查缓存容量
            if(size > capacity) {
                DLinkNode tail = removeTail();
                cache.remove(tail.key);
                size--;
            }
        } else {
            node.value = value;
            moveToHead(node);
        }
    }

    private void moveToHead(DLinkNode node) {
        removeNode(node);
        addToHead(node);
    }

    //添加结点至头部
    private void addToHead(DLinkNode node) {
        node.next = head.next;
        node.prev = head;
        head.next.prev = node;
        head.next = node;
    }

    //删除结点
    private void removeNode(DLinkNode node) {
        node.next.prev = node.prev;
        node.prev.next = node.next;  
    }

    //删除尾结点并返回
    private DLinkNode removeTail() {
        DLinkNode node = tail.prev;
        removeNode(node);
        return node;
    }
}
```

### 56.合并区间

```c++
vector<vector<int>> merge(vector<vector<int>>& intervals) {
        //先将区间按照左端点升序排序，则需要合并的区间是连续的
        int n=intervals.size();
        if(!n){
            return {};
        }
        
        vector<vector<int>> ans;
        sort(intervals.begin(), intervals.end());
        
        //加入第一个区间
        ans.push_back(intervals[0]);
        
        //依次遍历区间
        for(int i=1;i<n;i++){
            int l=intervals[i][0];
            int r=intervals[i][1];
            
            //若上一个区间右端点<当前区间左端点，区间无重叠
            if(ans.back()[1]<l){
                ans.push_back({l,r});
            }
            //否则修改上一个区间的右端点
            else{
                ans.back()[1]=max(ans.back()[1], r);
            }
        }        
        return ans;
    }
```

