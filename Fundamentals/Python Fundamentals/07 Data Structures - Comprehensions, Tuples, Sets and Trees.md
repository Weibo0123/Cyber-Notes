# Data Structures: Comprehensions, Tuples, Sets & Trees

## Overview

| Structure | Description |
|---|---|
| List | Ordered, indexed, mutable, mixed types allowed |
| Tuple | Ordered, **immutable** |
| Set | Unordered, **unique** elements only |
| Dictionary | Key-value pairs, access by key |
| Array | Sequential, same-type elements (less common in plain Python vs. list) |
| Tree | Hierarchical, nodes connected by edges |
| Graph | Nodes + edges representing arbitrary relationships |

## List Comprehension

```python
[expression for item in iterable]
```
```python
numbers = [1, 2, 3, 4, 5]
squares = [number**2 for number in numbers]     # [1, 4, 9, 16, 25]
```
**Conditional filtering:**
```python
even_numbers = [number for number in numbers if number % 2 == 0]
```
**Nested (flattening):**
```python
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flattened = [number for row in matrix for number in row]
```
Keep comprehensions reasonably simple — overly complex ones hurt readability more than a plain loop would.

## Generator Expressions

Same idea, parentheses instead of brackets:
```python
squares = (number**2 for number in numbers)
```
| | List comprehension | Generator expression |
|---|---|---|
| Returns | A list, all at once | A generator object |
| Evaluation | Immediate (eager) | **Lazy** — produces values only when requested |
| Memory | Stores everything | Doesn't store all values in memory |

Once a generator's values are consumed, it's **exhausted**. Prefer generators for large datasets processed only once.

## Dictionary Comprehension

```python
{key_expression: value_expression for item in iterable}
```
```python
squares = {number: number**2 for number in numbers}
even_squares = {number: number**2 for number in numbers if number % 2 == 0}
```

## Tuples

Ordered, **immutable**:
```python
my_tuple = (1, "two", 3.0)
my_tuple[0] = 2      # TypeError — can't modify a tuple
```
**Returning multiple values:**
```python
def min_max(numbers):
    return min(numbers), max(numbers)

min_num, max_num = min_max([2, 3, 1, 4, 5])
```
**Swapping variables:**
```python
a, b = b, a
```

## Sets

Unordered, **unique** elements, no indexing:
```python
set1 = {1, 2, 3}
```

| Operation | Symbol | Method |
|---|---|---|
| Union | `\|` | `.union()` |
| Intersection | `&` | `.intersection()` |
| Difference | `-` | `.difference()` |
| Symmetric difference | `^` | `.symmetric_difference()` |

```python
set1, set2 = {1, 2, 3}, {3, 4, 5}
set1 | set2      # {1, 2, 3, 4, 5}
set1 & set2       # {3}
set1 - set2        # {1, 2}
set1 ^ set2          # {1, 2, 4, 5}
```

## Trees

A hierarchical structure of nodes connected by edges; a binary tree node has a left and right child:
```python
class Node:
    def __init__(self, val):
        self.left = None
        self.right = None
        self.val = val

root = Node(1)
root.left = Node(2)
root.right = Node(3)
```
```
    1
   / \
  2   3
```

### Traversal Orders
```python
def inorder_traversal(node):     # Left → Root → Right
    if node:
        inorder_traversal(node.left)
        print(node.val)
        inorder_traversal(node.right)

def preorder_traversal(node):     # Root → Left → Right
    if node:
        print(node.val)
        preorder_traversal(node.left)
        preorder_traversal(node.right)

def postorder_traversal(node):     # Left → Right → Root
    if node:
        postorder_traversal(node.left)
        postorder_traversal(node.right)
        print(node.val)
```

## Cheat Sheet

```
[expr for x in it]              list comprehension
[expr for x in it if cond]        with filter
[expr for x in it for y in x]       nested/flatten
(expr for x in it)                    generator — lazy, one-shot, memory-efficient
{k: v for x in it}                      dict comprehension

Tuple: (a, b) — immutable, multi-return, swap: a, b = b, a
Set:   {a, b} — unique, unordered
  | union   & intersection   - difference   ^ symmetric difference

Tree node: .left / .right / .val
Traversal: inorder(L,Root,R)  preorder(Root,L,R)  postorder(L,R,Root)
```
