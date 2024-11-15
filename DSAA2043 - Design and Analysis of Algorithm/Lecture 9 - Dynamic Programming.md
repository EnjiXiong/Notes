# Lecture 9 - Dynamic Programming

## Menu
- [Introduction](#Introduction)
- [Typical Probelms](#Typical_Problems)
  - [Fibonacci numbers](#Fibonacci)
  - [Matrix Chain Products](#Matrix_Chain_Products)

### Introduction

**Definition:** Dynamic Programming is a method to simplify a specific problem by breaking it down into several sub-problems over time.

**How to accomplish this?**
1. Recursion
2. Tabulation of sub-problems

多说无益，直接做题！

### Typical Problems

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

#### Matrix Chain Products
