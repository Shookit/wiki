Algorithms and data structures
==============================

Definitions
-----------

### Functions

- Injective = Every A has one B
- Surjective = Every B has at least one A
- Bijective = Injective and Surjective
- Monotonic = Always increasing

### Tree traversal

- Inorder = left subtree, self, right subtree
- Postorder = left subtree, right subtree, self
- Preorder = self, left subtree, right subtree

### P/NP

- P: Can be solved in polynomial time
- NP: Can check certificate in polynomial time
- NP-Complete: An NP problem X for which it is possible to reduce any other NP problem Y to X in polynomial time (travelling + knapsack)
- NP-Hard: A problem which is at least as hard as NP-Complete
- P=NP: the question if all NP problems have a P solution

### Basic strategies

- Backtracking - general algorithm for finding all (or some) solutions, incrementally builds candidates to the solutions, and abandons each partial as soon as it determines that c cannot possibly be completed to a valid solution. (Sudoku, crosswords).
- Greedy - Always choose the largest/smallest item/path
- Divide and Conquer

Big O notation
--------

- O(n) is a higher bound, omega(n) is a lower bound, theta(n) is a tight bound
- O(1) - constant - array indexing
- O(logn) - logarithmic - searching a binary search tree or balanced tree
- O(n) - linear - searching an array
- O(nlogn) - linearithmic/loglinear - sorting with divide and conquer
- O(n^2) - quadratic - bubble sort
- O(n^c) - polynomial
- O(c^n) - exponential
- O(n!) - factorial - brute force traveling salesman

Sorting
-------

- `Bubble sort`: go from 0->n, swapping entries if out of order. Then do 0->(n-1), 0->(n-2), etc.
- `Heapsort`: creates a heap to sort values. Each time the top value is removed, you reheap (take one of the leaves, move to top, and re-sort). This is n logn operations, so O(nlogn)
- `Mergesort`: Full divide and conquer sort
- `Quicksort`: Practically the fastest. Find pivot (best if median), move data smaller than pivot to left, larger than pivot to right, move pivot to middle. Recursively run quicksort on each half. Knowledge of pivot selection algo can be used to force to run in O(n^2) - security hazard. Mergesort for security.

| Sort algo | Best       | Avg        | Worst      | Notes                                                                                          |
|-----------|------------|------------|------------|------------------------------------------------------------------------------------------------|
| Hash      | O(n)       | O(n)       | O(n)       | Good if sorting something w/ frequent repeats (e.g., 1mil people, sorting by Age in years)     |
| Bubble    | O(n^2)     | O(n^2)     | O(n^2)     | (in-place)                                                                                     |
| Insertion | O(n^2)     | O(n^2)     | O(n^2)     | (in-place)                                                                                     |
| Selection | O(n^2)     | O(n^2)     | O(n^2)     | (in-place)                                                                                     |
| Quick     | O(n\*logn) | O(n\*logn) | O(n^2)     | (common implementation not in-place)                                                           |
| Merge     | O(n\*logn) | O(n\*logn) | O(n\*logn) | (common implementation not in-place)                                                           |
| Heap      | O(n\*logn) | O(n\*logn) | O(n\*logn) | (in-place)                                                                                     |


Data structures
---------------

### Stack (LIFO) - list/array structure

- Push: O(1)
- Pop: O(1)
- Top: O(1)

### Queue/Deque/Circular Queue (FIFO)

- Deque can add/remove from left or right; circular links to beginning (cycle detection)
- Insert: O(1)
- Remove: O(1)
- Size: O(1)

### Binary Search Tree

- Left is less, right is greater than node

### Balanced Binary Search Tree

- Has minimum height

### Red-black tree

- Self-balancing tree,
- Every node is red or black
- The root is black
- Every leaf is NIL and is black
- If a node is red, then both its children are black
- For each node, all paths from the node to descendant leaves contain the same number of black nodes.

### B-tree

- Nodes can have more than two children, good for systems that read/write large blocks

### PriorityQueue

- Has insertWithPriority and pullHighestPriorityElement functions - typically implemented w/ heap

### Heap

- Binary tree - parent is always larger than all of its child nodes. many implementations (BINARY IS MOST COMMON):

### Dictionaries/map

- Key w/ associated value - typically implemented using a hash map/table/set data structure.

### Hash table

- Key -> hash function -> index = value; bad hash function = linked list

### Graphs

- Can be stored as adjancecy/incidence list/matrix. List is better if sparse, vice-versa for matrix.

### Tries

- Tree which doesn't store the value of a node, only traversing to a leaf gives you the total saved value


Data structure performance
--------------------------

| Header text                | Linked list   | Array | Dynamic array | Balanced tree | Random access list |
|----------------------------|---------------|-------|---------------|---------------|--------------------|
| Indexing                   | T(n)          | T(1)  | T(1)          | T(log n)      | T(log n)           |
| Insert/delete at beginning | T(1)          | N/A   | T(n)          | T(log n)      | T(1)               |
| Insert/delete at end       | T(1)          | N/A   | T(1)amortized | T(log n)      | T(log n) updating  |
| Insert/delete in middle    | search + T(1) | N/A   | T(n)          | T(log n)      | T(log n) updating  |
| Wasted space (average)     | T(n)          | 0     | T(n)          | T(n)          | T(n)               |
| Search                     | T(n)          | T(n)  | T(n)          | T(logn)       | T(n)               |


Specific solutions
------------------

### Permutation

Recursive: permutation(remainingString) - given remaining string, run permutation once for each letter

```python
def permutation(remainingString):
    if len(remainingString) == 1:
        return remainingString
    perm_list = [] # resulting list
    for i in range(len(remainingString)):
        m1String = remainingString[:i] + remainingString[i+1:]
        z = permutation(m1String) # permutations of sublist
        for t in z:
            perm_list.append(remainingString[i] + t)
    return perm_list
print permutation(strngy)
```

### Prime

- To check if a number is prime or not, only check all prime numbers from 2 - sqrt(n)
- If both a and b were greater than the square root of n, a\*b would be greater than n. So at least one of those factors must be less or equal to the square root of n, and to check if n is prime, we only need to test for factors less than or equal to the square root.

### Tree's lowest common ancestor

- From root node, traverse one toward each node on each, compare. Return when different.

### Tree Search

- DFS has better memory requirements (don't have to keep track of each level's node pointers)

### Recursion

- Always DFS (since the every time you call the recursive function you go one level deeper until the base case is reached)
- First lines in the program should be if statements to stop recursively calling (base case)
- Diagram the tree, identify base case \*and what you are returning\* (anything like 'var = funct()' needs a return, obviously)

### Minimum spanning tree algorithms (greedy)

- Kruskal: add shortest edge from the whole graph that doesn't form a cycle
- Prim: add the shortest edge connected to the starting vertex/tree that doesn't form a cycle

### Shortest path algorithms

- Dijkstra: from starting node/subgraph, calculate distance to all unvisited neighbors. Select shortest distance to an unvisited neighbor. Repeat.

