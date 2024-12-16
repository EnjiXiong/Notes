# Today Key Point: Recursion + Divide and Conquer

## menu

> You can click the menu to go to the corresponding sector directly.
> Actually, every word in blue can be a link to the destination. 
- [merge sort](#merge_sort)
- [divide and conquer](#divide_and_conquer)
  - [master theorem](#master-theorem)
  - [binary search](#binary_search)
  - [power a number](#power-a-number)
  - [fibonacci numbers](#Fibonacci)
  - [recursive squaring](#recursive-squaring)

> There's the code of quick sort in the end, I think it's more useful than explanation in words.

### merge_sort

procedure:

given the sequence A[1...n], the whole time complexity is $T(n)$

1. if n = 1, done. --- $\theta(1)$
2. recursively sort A[1..[n/2]] and A[[n/2]+1..n] --- $2T(\frac{n}{2})$
3. merge the divided two sub-sequences --- $\theta(n)$

Thus, $$T(n) = \theta(nlogn)$$ Whereas the time complexity of the bubble sort is $O(n^2)$.

It's easy to find it that we divide a problem into mutiple sub-tasks, then we make it easy to solve. We called this divide and conquer.

```python

def merge_sort(arr):
    #if n is 1
    if len(arr) == 1:
        return arr
    
    #divide into two sub-sequences
    mid = len(arr)//2
    L = arr[:mid]
    R = arr[mid+1:]
    merge_sort(L)
    merge_sort(R)

    #merge the two sub-sequences
    i = j = k = 0
    # Copy data to temp arrays L[] and R[]
    while i < len(L) and j < len(R):
        if L[i] < R[j]:
            arr[k] = L[i]
            i += 1
        else:
            arr[k] = R[j]
            j += 1
        k += 1

    # Checking if any element was left
    while i < len(L):
        arr[k] = L[i]
        i += 1
        k += 1

    while j < len(R):
        arr[k] = R[j]
        j += 1
        k += 1
```

### divide_and_conquer

#### master-theorem
If we get an equation like $T(n) = aT(\frac{n}{b}) + f(n)$:

1. $f(n) = O(n^{log_ba-\epsilon})$, and the constant $\epsilon > 0$

$$T(n) = \theta(n^{log_ba})$$

2. $f(n) = \theta(n^{log_ba}lg^{l}n)$, and the constant $l >= 0$

$$T(n) = \theta(n^{log_ba}lg^{l+1}n)$$

3. $f(n) = \omega(n^{log_ba+\epsilon})$, and the constant $\epsilon > 0$ and regularity condition $af(\frac{n}{b}) <= cf(n)$
  
$$T(n) = \theta(f(n))$$

> Difficult and abstract! Review is arbitrarily required!

#### binary_search
$T(n) = T(\frac{n}{2}) + \theta(1)$ Thus, we can get the formula with the case 2, where $l = 0$

$$T(n) = \theta(lgn)$$

#### power-a-number
How to figure out the value of $a^n$ if we have $a$ and $n$? quickly?

Of course divide it and conquer!

$$
f(n) =
\begin{cases} 
a^{\frac{n}{2}}*a^{\frac{n}{2}},  & \text{if }n\text{ is even} \\
a^{\frac{n}{2}}*a^{\frac{n}{2}}*a, & \text{if }n\text{ is odd}
\end{cases}
$$

#### Fibonacci

Recursive definition

$$
f(n) =
\begin{cases} 
0,  & \text{if }n\text{is 0} \\
1,  & \text{if }n\text{is 1} \\
f(n-1) + f(n-2), & \text{if }n >= 2
\end{cases}
$$

#### recursive-squaring
How to compute the result of multiplying two matrixes quickly?

Theoretically, the element in the output matrix $c_{ij} = \sum_{k=0}^{k=n} a_{ik}b_{kj}$, where n is the size of the matrix.

According to the formula, it's easy to come up with an algorithm with $O(n^3)$.

Can we optimise it?

Applying the thought of binary search, we simplise the n*n matrix into 2*2 matrix.

Just see [strassen's Idea](https://www.tutorialspoint.com/data_structures_algorithms/strassens_matrix_multiplication_algorithm.htm), whose algorithm reduce the time complexity to $O(n^{log_27})$. Nowadays, the theoretical best is $O(n^{2.376…})$

```python
def binary_search(a,l,r,target) -> int:
    #Notation: the list a must be in an ascending or monotone decreasing order.
    #l is the left index we start to search, r is the right index we finish searching. a is the array.
    mid = (l + r)//2
    if(a[mid] > target):
        r = mid - 1
    elif a[mid] < target:
        l = mid + 1
    else:
        return mid
    return binary_search(a,l,r,target)

def power_a_number(a,n) -> int:
    if n == 0:
        return 1
    else:
        tem = power_a_number(a, n//2) * power_a_number(a, n//2)
        if n%2 :
            tem *= a
        return tem
    
print(power_a_number(5,5)) #output : 3125

def quick_sort(arr):
    # Base case: if the array is empty or consists of one element, it's already sorted
    if len(arr) <= 1:
        return arr
    else:
        # Selecting the pivot. Here, we choose the last element as the pivot for simplicity
        pivot = arr.pop()

        # Elements less than pivot will go to the left list, greater to the right list
        left = []
        right = []

        for item in arr:
            if item < pivot:
                left.append(item)
            else:
                right.append(item)

        # The final array will be the sorted left list, pivot, and sorted right list
        return quick_sort(left) + [pivot] + quick_sort(right)
```
