学习笔记

### 递归

递归可以类比于循环，即通过函数体调用自身不断循环的过程。对于递归，注意以下特性：

+ 向下进入下一层递归环境中，向上又回到原来的递归层
+ 通过参数同步回到上一层
+ 每一层的环境都是上一层的拷贝，各层间环境互不干扰。通过参数来进行各层间的一个因素传递

**递归的代码模板**

```java
//层数记录，参数
public void recur(int level, int param) {
    
    //首先写明递归终止条件
    if(level > MAX_LEVEL) 
        return ;
    
    //逻辑处理
    process(level, param);
    
    //递归到下一层
    recur(level + 1, newParam);
    
    //清理当前层的状态
    ...
}
```

在递归中，注意以下几个误区：

+ 不要人肉进行递归（最大误区）
+ 找到最小重复子问题，这是解决递归的关键（即找到循环内容）
+ 时刻谨记 **数学归纳法思维**，先思考最基本情况，逐步归纳普遍情况，得到递归公式

### 分治，回溯

**分治**

分治是建立在问题具有**重复性**的前提下。原有问题通过寻找规律可以继续拆分成子问题，各个子问题之间没有重叠性且互相独立。那么我们便可以结合递归解题的性质，将求解原问题分解成为了求解子问题的过程。然后再将子问题的解合并成为原问题的解。

因此，分治场合递归结合使用，其代码模板如下：

```java
public void devideConquer(problem, params) {
    //递归终止条件
    if(problem is finished){
        process_result;
        return;
    }
    //分解子问题
    subproblems = split(problem);
    //分治子问题
    subr1 = divideConquer(subproblems[0], params);
    subr2 = divideConquer(subproblems[1], params);
    ...;
    //合并问题的解
    result = mergeResult(subr1, subr2, ...);
    
    //清理当前层，如果有必要
}
```

**回溯**

回溯的思想其实就是枚举的过程。它尝试分步的去解决一个问题，探索每一个可能的解。若在当前步骤中，不能得到有效的解，则会取消上一步甚至是上几步的计算，然后再通过其他分支去探索所有可能的解。

回溯法也是结合递归来解题，由于其具有回退取消步骤的性质，我们需要在解题过程中使用 **剪枝** 策略来减少其回退的步骤，降低时间复杂度。剪枝即通过一些条件判断，将根本不可能有解的分支去掉，而不去进行探索步骤。其代码模板如下：

```java
public void backtrack(path, source, params) {
    //递归终止
    if(path is finished){
        //记录答案
        return;
    }
    //剪枝
    if(剪枝条件){
        return;
    }
    //更改参数，递归下一层
    change(path);
    change(params);
    backtracke(path, source, params);
    //回溯，恢复参数状态
    unchange(path);
    unchange(params);
}
```





