# Algorithms in Python

## What Is an Algorithm?

A step-by-step procedure for solving a problem. Tree traversals (file 07) are one example; algorithms work together with data structures to process data efficiently. Other common categories: array/dictionary searching, set operations, graph/tree search.

## Bubble Sort

Repeatedly compares adjacent elements and swaps them when out of order:
```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]

arr = [64, 34, 25, 12, 22, 11, 90]
bubble_sort(arr)
# [11, 12, 22, 25, 34, 64, 90]
```
Simple to understand, but inefficient on large inputs — each full pass can require another pass until nothing needs swapping.

## Quick Sort

Picks a **pivot**, partitions the array so smaller elements land to its left and larger ones to its right, fixes the pivot's position, then recursively sorts each side:
```python
def partition(arr, low, high):
    i = low - 1
    pivot = arr[high]
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1

def quick_sort(arr, low, high):
    if low < high:
        pi = partition(arr, low, high)
        quick_sort(arr, low, pi - 1)
        quick_sort(arr, pi + 1, high)

quick_sort(arr, 0, len(arr) - 1)
```
Generally much faster than Bubble Sort on larger inputs, at the cost of being harder to trace by hand.

## Choosing an Algorithm

Different algorithms can solve the same problem — the right choice depends on factors like execution time and resource usage, not just "does it work."

## Cheat Sheet

```
Bubble Sort:  repeatedly swap adjacent out-of-order pairs — simple, slow on large input
Quick Sort:     pick pivot → partition → recurse on each side — faster, more complex

Algorithm choice depends on: input size, time constraints, memory constraints
```
