# Data Structures and Algorithms: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q06. What does Big O describe?

**Answer:** Big O describes how an algorithm's resource use grows as input grows. It focuses on growth, not exact milliseconds on one machine.

### Q07. What is O(1) time?

**Answer:** The operation takes roughly the same number of steps as input grows. Reading an array element by index is a common example.

### Q08. What is O(n) time?

**Answer:** Work grows in proportion to the input size. Scanning every element once is O(n).

### Q09. What is O(log n) time?

**Answer:** Each step reduces the remaining search space by a factor, often by half. Binary search on a sorted array is O(log n).

### Q10. What is the time complexity of binary search?

**Answer:** O(log n), because each comparison discards about half of a sorted range. The input must be sorted first.

### Q11. Array vs linked list?

**Answer:** An array-backed list gives fast indexed reads but can shift items on insertion. A linked list can insert locally if you already have a node, but locating it takes traversal.

### Q12. What is a stack?

**Answer:** A last-in, first-out collection. Function calls, undo history, and parsing are common examples.

### Q13. What is a queue?

**Answer:** A first-in, first-out collection. It is useful for work waiting to be processed in arrival order.

### Q14. What is a hash table?

**Answer:** It maps keys to buckets using a hash, then checks equality to find a key. Collisions are expected and must be handled.

### Q15. What is a hash collision?

**Answer:** Different keys map to the same bucket. A hash table resolves this by storing and comparing multiple entries there.

### Q16. What is a tree?

**Answer:** A hierarchy of nodes connected by edges, with one root and no cycles. File folders and organization charts are familiar examples.

### Q17. What is a binary search tree?

**Answer:** A binary tree ordered so left subtree values are smaller and right subtree values are larger by its comparator. Performance depends on balance.

### Q18. What is a balanced tree?

**Answer:** It keeps its height from growing too large relative to its node count, helping operations stay around logarithmic time.

### Q19. What is a heap?

**Answer:** A tree-shaped structure where each parent has priority over its children. It is useful for quickly retrieving the smallest or largest item.

### Q20. What is a priority queue?

**Answer:** It returns elements by priority rather than insertion order. Java's PriorityQueue exposes the least element first by default.

### Q21. What is breadth-first search?

**Answer:** BFS visits nodes level by level, typically using a queue. In an unweighted graph it can find a path with the fewest edges.

### Q22. What is depth-first search?

**Answer:** DFS follows a path as far as possible, then backtracks with recursion or a stack. It helps with reachability and cycle problems.

### Q23. What is a graph?

**Answer:** A set of vertices connected by edges. It can be directed or undirected and may have weights such as road distance.

### Q24. What is a cycle in a graph?

**Answer:** A path that returns to a previously visited vertex. Whether cycles matter depends on the task, such as dependency checking.

### Q25. What is the two-pointer technique?

**Answer:** Two indexes move through a sequence to avoid repeatedly scanning the same range. It is common for sorted pair-sum and palindrome tasks.

### Q26. What is a sliding window?

**Answer:** It maintains a moving range over a sequence and updates state as the range shifts. It can reduce nested scans to linear time.

### Q27. When would you use a hash set?

**Answer:** When you need fast average membership checks, uniqueness, and do not need sorted order. It is handy for detecting repeats.

### Q28. What is recursion?

**Answer:** A method solves a problem by calling itself on smaller input. It needs a base case to stop.

### Q29. What is a base case?

**Answer:** The smallest case answered without another recursive call. Without one, recursion can continue until stack overflow.

### Q30. What is memoization?

**Answer:** It stores earlier subproblem results so they are not recalculated. It can improve some exponential recursive solutions.

## References

- [Princeton Algorithms companion site](https://algs4.cs.princeton.edu/home/)
- [Java Collections Framework guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html)
