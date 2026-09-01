# Big-O Complexity Cheat Sheet

## Growth order (slow -> fast)
`O(1) < O(log n) < O(n) < O(n log n) < O(n^2) < O(2^n) < O(n!)`

## Common operations
| Operation | Array | Linked List | Hash Table | BST (balanced) |
|-----------|-------|-------------|------------|----------------|
| Access/index | O(1) | O(n) | - | O(log n) |
| Search | O(n) | O(n) | O(1) avg | O(log n) |
| Insert/delete | O(n) | O(1)* | O(1) avg | O(log n) |

*at a known node; front insert O(1), arbitrary O(n).

## Sorting algorithms
| Algorithm | Best/Avg | Worst | Space | Stable? |
|-----------|----------|-------|-------|---------|
| Merge sort | O(n log n) | O(n log n) | O(n) | Yes |
| Quick sort | O(n log n) | O(n^2) | O(log n) | No |
| Heap sort | O(n log n) | O(n log n) | O(1) | No |
| Insertion | O(n) (nearly sorted) | O(n^2) | O(1) | Yes |
| Counting/Radix | O(n*k) | - | O(k) | Yes (counting) |

## Binary search
- Prereq: **sorted** (or a monotonic predicate).
- Iterative O(log n), O(1) space. Variants: first/last occurrence, rotated array, find peak.

## Recursion / DP
- **Memoization (top-down):** cache results; space = number of subproblems.
- **Tabulation (bottom-up):** often less overhead.
- Recognize: overlapping subproblems + optimal substructure -> DP. State + transition is the key.

## Rule of thumb for interviews
1. State your approach's time & space complexity out loud.
2. Mention the trade-off (e.g., "O(n) space to make it O(1) per query").
3. If you can do better, say how and why it may not be worth it at small n.
