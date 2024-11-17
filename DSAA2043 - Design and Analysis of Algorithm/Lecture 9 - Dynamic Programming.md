# Lecture 9 - Dynamic Programming

## Menu
- [Introduction](#Introduction)
- [Typical Probelms](#Typical)
  - [Fibonacci numbers](#Fibonacci)
  - [Knapsack Problem](#01Knapsack)
  - [Matrix Chain Products](#MatrixChainProducts)
- [Optional Problems](#Optional)
  - [Longest Common Sequence](#LCS)

### Introduction

**Definition:** Dynamic Programming is a method to simplify a specific problem by breaking it down into several sub-problems over time.

**How to accomplish this?**
1. Recursion
2. Tabulation of sub-problems

**How to solve a dynamic Programming problem?**
1. Generalize the original problem 找到大问题
2. Factorize the big problem 分解为子问题的结合
3. Figure out the corner problem 找到最小子问题
4. Conclude the state transition equation 列出状态转移方程
5. How about the time complexity? 计算时间复杂度，判断是否可行。（时间复杂度在千万级别是可行的）

多说无益，直接做题！

### Typical

#### Fibonacci

> Given the number of the index in the Fibonacci sequence, output the value.
> The index begins at 1, and the first two values of the sequence are both 1.

```python
def Fibonacci(n):
    if n == 1 or n == 2:
        return 1
    else:
        return Fibonacci(n-1) + Fibonacci(n-2)
```

See, this is a simple but comprehensive procedure of the dynamic programming. Recursion + Sub-problems

[Return to the menu](#Menu)

#### 01Knapsack

> A knapsack with $W$ weight constraint, there're several items with weight $w_i$ and value $v_i$.
> Each item can be chosen at most once, and $W \ge \underset {chosen}{\sum} w_i$
> Figure out the maximum value the knapsack can hold.

**Analysis:**
1. If the weight of the available item is not affordable, not chosen.
2. If the weight is affordable, consider the subsequent influence, which means $f[aw] = max(f[aw], f[aw-w[i]] + v[i])$
   
   Any confusion?
   
   This equation seems weird. since the value would add v[i], of course it'll choose the available item every time!
   
   Actually not! Consider W = 10, there're 3 items:
   
|-no.-| weight | value |
|:---:|:------:|:-----:|
| 1 | 9 | 3 |
| 2 | 8 | 4 |
| 3 | 6 | 1 |

Initially all f[i] = 0 for i in range(0,W+1). If we choose no.1 first f[10] = f[10-9] + 3 = 3. Then we consider no.2, f[10] = max(f[10], f[10-8] + 4). Obviously, it's 4. it makes a change.

However, it doesn't change when it comes to no.3 item. since f[10] = 4 > f[10-6] + 1 = 1

Code is as below, take care of the order it iterates the available weight space. It's reverse.

```python

class knapsack:
    def __init__(self, weight, value, capacity):
        self.weight = weight
        self.value = value
        self.capacity = capacity
        self.length = len(weight)
    
    def max_value(self):
        c = self.capacity
        l = self.length
        w = self.weight
        v = self.value
        f = [0 for i in range of (c+1)] #初始化滚动数组，记录每个容量下的最大价值。例如，f[5] = 3表示容量为5时，最大价值为3.
        for i in range(l):
            for j in range(c,w[i]-1,-1):# C++中写作for(int j = W; j >= w[i]; j--)
                f[j] = max(f[j], f[j-w[i]] + v[i])
        return f[c]
    #答疑：为什么容量要倒序遍历？
    #答：正序遍历时。f[3]比f[10]更先修改。如果有一个重量小于等于3的物品，就有可能在f[3]加到f[10]的过程中被多次计算，违背了只能选一次的规则。
    #事实上，正序遍历解决的是完全背包问题，即每种物品不限量，可以选无数次。

```

[Return to the menu](#Menu)

#### MatrixChainProducts

> Given the dimension of a sequence of matrices in an array arr[], where the dimension of the ith matrix is (arr[i-1] * arr[i]),
> the task is to find the most efficient way to multiply these matrices together such that the total number of element multiplications is minimum.
>
> Example: Given an array[4] = [2, 1, 3, 4], there are 3 matrices of dimensions 2x1, 1x3, 3x4.

**Analysis:**
1. Given two definite matrices of dimensions a $\times$ b and b $\times$ c, the number of multiplication performed is a $\times$ b $\times$ c.
2. The chain product of matrices is like

$$A_0 \times A_1 \times A_2 \times \dots \times A_{n-1} = (A_0 \times A_1 \times \dots \times A_k)(A_{k+1} \times \dots \times A_{n-1})$$

Take $N_{0,k} = (A_0 \times A_1 \times \dots \times A_k)$ and $N_{k+1, n-1} = (A_{k+1} \times \dots \times A_{n-1})$, the dimensions of the two new matrices are arr[0] $\times$ arr[k+1] and arr[k+1] $\times$ arr[n].

Thus, $N_{0.n-1} = \underset {0 \le k \le n-1}{\min} {N_{0,k} + N_{k+1,n-1} + arr[0] \times arr[k+1] \times arr[n]}$

3. Then, we have the state transition equation:

$$N_{i.j} = \underset {i \le k \le j}{\min} {N_{i,k} + N_{k+1,j} + arr[i] \times arr[k+1] \times arr[j+1]}$$

Code is as below: 

```python
class matrix:
    def __init__(self, arr):
        self.arr = arr
        self.length = len(arr)

    def min_number_of__chain_multiplication(self):
        memo = [[0 for i in range(self.length)] for j in range(self.length)]
        return self.dpnum(0, self.length-1, memo)
    
    def dpnum(self, i, j, memo):
        if i+1 == j:
            return 0;

        if memo[i][j] != 0:
            return memo[i][j]
        
        ans = float('inf')
        for k in range(i+1, j):
            ans = min(ans, self.dpnum(i, k, memo) + self.dpnum(k, j, memo) + self.arr[i]*self.arr[k]*self.arr[j])

        memo[i][j] = ans
        return ans
    
if __name__ == "__main__":
    arr = list(map(int, input().split()))
    m = matrix(arr)
    print(m.min_number_of__chain_multiplication())
```

```
case 1: 1 2 3 4 3
answer 1: 30

case 2: 1 2 3 4
answer 2: 18 (1*2*3 + 1*3*4, 先算前两个矩阵，1*2和2*3的，记6次，得到1*3矩阵，再和3*4矩阵乘起来，记12次，总计6 + 12 = 18次)
```

[Return to the menu](#Menu)

### Optional

PPT里面出现了名称的问题，但是没有给予讲解

以及一些DP问题的拓展，比如背包（knapsack）问题不只有0/1背包，还有完全背包，多重背包，多维背包问题等

[Return to the menu](#Menu)

#### LCS

下周再补上，还有一周讲DP。

[Return to the menu](#Menu)
