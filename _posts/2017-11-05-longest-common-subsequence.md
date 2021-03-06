---
title:  "LCS(longest common subsequence)"
header:
  teaser: "https://cdn.pixabay.com/photo/2017/04/18/23/35/frog-2240764_960_720.jpg"
categories: 
  - CwG
tags:
  - CwG coding_problems
---

  This is the first post of the CWG series (short for Coding with Girlfriend, hope she won't be pissed of :) ).
  It serves partially as a study guide for solving coding problems, as well as a documentation for personal reference in the future.
  
  The problem today is a clasic one in Computer Science:
  LCS(longest common subsequence): "Given two sequences, find the length of longest subsequence present in both of them. A subsequence is a sequence that appears in the same relative order, but not necessarily contiguous" -- GeeksforGeeks

A common mistake beginners often make is rushing to code an optimal solution without solving it with a naive solution first. Therefore, let us start with the most basic solution by using recursion:

From left to right:
```python
def lcs(a,b):
    #base case: if one of the list is empty
    if not len(a) or not len(b): return 0
    #inductive step: if current char matches,
    #answer would be 1 plus the answer for subproblem
    if a[0]==b[0]: return 1+lcs(a[1:], b[1:])
    #inductive step: if current char doesn't match,
    #aswer would be the max of two subproblems
    else: return max(lcs(a, b[1:]), lcs(a[1:], b))
```
Or from right to left:
```python
def lcs(a,b):
    if not len(a) or not len(b): return 0
    if a[-1]==b[-1]: return 1+lcs(a[:-1], b[:-1])
    else: return max(lcs(a, b[:-1]), lcs(a[:-1], b))
```
Unfortunately, these naive solutions takes exponential running time, and not to mention the overheads of recursive funtion calls and copying of lists(which though can be optimized away by passing indices but why should we bother with the naive solution)

Therefore, let's do some optimization by using a 2D array to store previous results
```python
def lcs(a,b):
    temp= [[-1]*len(b)]*len(a)
    for i, ae in enumerate(a):
        for j, be in enumerate(b):
            if ae==be:
                if not i or not j: temp[i][j] = 1
                else: temp[i][j] = temp[i-1][j-1]+1
            else:
                if not i: temp[i][j] = temp[i][j-1]
                elif not j: temp[i][j] = temp[i-1][j]
                else: temp[i][j] = max(temp[i-1][j], temp[i][j-1])
    
    return temp[len(a)-1][len(b)-1]
```

Or, using a slightly wider storage with row 0 and column 0 initialized different to avoid all messy edge cases:
```python
def lcs(a,b):
    temp= [[-1 if i or j else 0 for i in xrange(len(b)+1)] for j in xrange(len(a)+1)]
    for i, ae in enumerate(a):
        for j, be in enumerate(b):
            if ae==be: temp[i+1][j+1] = temp[i][j]+1
            else: temp[i+1][j+1] = max(temp[i][j+1], temp[i+1][j])
    
    return temp[len(a)][len(b)]
```
The new solutions have O(n*m) for both time and space, which is a huge improvement.

Yet to do even better, we can use the fact that computing a new value in our storage only relies on current row and previous row. Hence we can use two rows to solve the problem:
```python
def lcs(a,b):
    temp= [[-j for i in xrange(len(b)+1)] for j in xrange(2)]
    for i, ae in enumerate(a):
        for j, be in enumerate(b):
            if ae==be: temp[(i+1)%2][j+1] = temp[(i)%2][j]+1
            else: temp[(i+1)%2][j+1] = max(temp[(i)%2][j+1], temp[(i+1)%2][j])
    
    return temp[len(a)%2][len(b)]

```
The solution would give us O(n*m) running time and O(n) space.

Please let me know if there is any error in the code or further improvement.
