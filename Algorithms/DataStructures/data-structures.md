# Data Structures & Core Algorithms

## Arrays / Lists
- **Array:** contiguous, O(1) index access, O(n) insert in middle.
- **Linked List:** O(1) insert/delete at known node, no random access. Doubly-linked for O(1) delete given node.

## Stacks & Queues
- **Stack (LIFO):** undo history, DFS, expression evaluation, call stack.
- **Queue (FIFO):** task scheduling, BFS, buffering.
- **Deque:** both ends. **Priority Queue / Heap:** top is O(1), insert/remove O(log n).

## Hash Table
- Average O(1) lookup/insert/delete; worst O(n) on collisions.
- Use when you need fast membership / key->value. Watch out: not ordered, memory overhead.

## Trees
- **Binary Search Tree:** O(log n) avg search/insert/delete (degenerate to O(n)).
- **Balanced trees (AVL/Red-Black):** guaranteed O(log n).
- **Trie:** prefix matching - autocomplete, word lookup.
- **Heap (complete BST):** min/max in O(1); used for top-k, scheduling.

## Graphs
- Represent: adjacency list (sparse) vs matrix (dense).
- **BFS:** shortest path in unweighted graph, level order. Queue-based.
- **DFS:** cycles, topological sort, connected components. Stack/recursion.
- **Dijkstra:** shortest path, non-negative weights. **Bellman-Ford:** handles negative edges.
- **Topological sort:** dependency ordering (build systems, task scheduling).

## Strings
- KMP / Rabin-Karp for substring search; suffix automaton/trie for advanced.
- Two-pointer and sliding window for subarray problems.

## Common patterns (interview favorites)
- **Two pointers** - sorted arrays, pairs.
- **Sliding window** - max/min subarray of size k, longest substring w/o repeat.
- **Fast/slow pointer** - cycle detection (Floyd's).
- **Prefix sum** - range sums in O(1) after O(n) build.
- **Backtracking** - permutations, subsets, N-queens. Prune early.
- **Divide & conquer** - merge sort, binary search, closest pair.

## Self-test questions
1. When would you pick a hash table over a balanced BST?
2. Why is BFS the right choice for shortest path in an unweighted graph?
3. Give a sliding-window problem and its time/space complexity.
