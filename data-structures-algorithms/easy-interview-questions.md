# Data Structures and Algorithms: Easy Q&A

Quick revision answers. Try answering in your own words before reading the explanation.

### Q1. What does Big O describe?

**Answer:** Big O describes how an algorithm's resource use grows as input grows. It focuses on growth, not exact milliseconds on one machine.

### Q2. What is O(1) time?

**Answer:** The operation takes roughly the same number of steps as input grows. Reading an array element by index is a common example.

### Q3. What is O(n) time?

**Answer:** Work grows in proportion to the input size. Scanning every element once is O(n).

### Q4. What is O(log n) time?

**Answer:** Each step reduces the remaining search space by a factor, often by half. Binary search on a sorted array is O(log n).

### Q5. What is the time complexity of binary search?

**Answer:** O(log n), because each comparison discards about half of a sorted range. The input must be sorted first.

### Q6. Array vs linked list?

**Answer:** An array-backed list gives fast indexed reads but can shift items on insertion. A linked list can insert locally if you already have a node, but locating it takes traversal.

### Q7. What is a stack?

**Answer:** A last-in, first-out collection. Function calls, undo history, and parsing are common examples.

### Q8. What is a queue?

**Answer:** A first-in, first-out collection. It is useful for work waiting to be processed in arrival order.

### Q9. What is a hash table?

**Answer:** It maps keys to buckets using a hash, then checks equality to find a key. Collisions are expected and must be handled.

### Q10. What is a hash collision?

**Answer:** Different keys map to the same bucket. A hash table resolves this by storing and comparing multiple entries there.

### Q11. What is a tree?

**Answer:** A hierarchy of nodes connected by edges, with one root and no cycles. File folders and organization charts are familiar examples.

### Q12. What is a binary search tree?

**Answer:** A binary tree ordered so left subtree values are smaller and right subtree values are larger by its comparator. Performance depends on balance.

### Q13. What is a balanced tree?

**Answer:** It keeps its height from growing too large relative to its node count, helping operations stay around logarithmic time.

### Q14. What is a heap?

**Answer:** A tree-shaped structure where each parent has priority over its children. It is useful for quickly retrieving the smallest or largest item.

### Q15. What is a priority queue?

**Answer:** It returns elements by priority rather than insertion order. Java's PriorityQueue exposes the least element first by default.

### Q16. What is breadth-first search?

**Answer:** BFS visits nodes level by level, typically using a queue. In an unweighted graph it can find a path with the fewest edges.

### Q17. What is depth-first search?

**Answer:** DFS follows a path as far as possible, then backtracks with recursion or a stack. It helps with reachability and cycle problems.

### Q18. What is a graph?

**Answer:** A set of vertices connected by edges. It can be directed or undirected and may have weights such as road distance.

### Q19. What is a cycle in a graph?

**Answer:** A path that returns to a previously visited vertex. Whether cycles matter depends on the task, such as dependency checking.

### Q20. What is the two-pointer technique?

**Answer:** Two indexes move through a sequence to avoid repeatedly scanning the same range. It is common for sorted pair-sum and palindrome tasks.

### Q21. What is a sliding window?

**Answer:** It maintains a moving range over a sequence and updates state as the range shifts. It can reduce nested scans to linear time.

### Q22. When would you use a hash set?

**Answer:** When you need fast average membership checks, uniqueness, and do not need sorted order. It is handy for detecting repeats.

### Q23. What is recursion?

**Answer:** A method solves a problem by calling itself on smaller input. It needs a base case to stop.

### Q24. What is a base case?

**Answer:** The smallest case answered without another recursive call. Without one, recursion can continue until stack overflow.

### Q25. What is memoization?

**Answer:** It stores earlier subproblem results so they are not recalculated. It can improve some exponential recursive solutions.

### Q26. What is linear search?

**Answer:** It checks items one by one until it finds a match or reaches the end; its worst-case time is O(n).

**Example:** Scan a small unsorted array.

### Q27. How do you merge two sorted arrays?

**Answer:** Use two indexes, repeatedly copy the smaller current value, then append the remainder. This takes O(n + m) time.

**Example:** Compare `a[i]` and `b[j]`, then advance the index you used.

### Q28. What does O(n²) usually suggest?

**Answer:** Work may grow with every pair of input items, often from nested loops.

**Example:** Comparing every pair in a list is typically O(n²).

### Q29. What is space complexity?

**Answer:** How extra memory use grows as the input grows.

**Example:** A copy of every input item uses O(n) extra space.

### Q30. What is a linked list?

**Answer:** A sequence of nodes where each node points to the next, and possibly the previous, node.

**Example:** Finding an item by position usually takes O(n).

### Q31. What is a deque?

**Answer:** A double-ended queue that supports adding and removing at both ends.

**Example:** It can act as both a stack and a queue.

### Q32. What is a hash function?

**Answer:** A function that maps a key to an integer used to choose a hash-table bucket.

**Example:** Different keys can produce the same hash.

### Q33. What is a load factor in a hash table?

**Answer:** A threshold based on entries versus buckets that can trigger resizing.

**Example:** Resizing keeps average lookup efficient as entries grow.

### Q34. What is a trie?

**Answer:** A tree-like structure that stores strings by shared prefixes.

**Example:** It can support prefix lookup for autocomplete.

### Q35. What is a graph adjacency list?

**Answer:** A representation that stores each vertex's neighboring vertices, often efficient for sparse graphs.

**Example:** For each city, store the cities with direct roads.

### Q36. BFS vs DFS?

**Answer:** BFS explores level by level and finds shortest paths in unweighted graphs; DFS explores a path deeply first.

**Example:** Use BFS for minimum number of unweighted edges.

### Q37. What is a topological sort?

**Answer:** An ordering of a directed acyclic graph where each prerequisite appears before its dependent task.

**Example:** Order build tasks by dependencies.

### Q38. What is a greedy algorithm?

**Answer:** It chooses the best-looking local option at each step; correctness requires a proof for the problem.

**Example:** A greedy choice is not automatically globally optimal.

### Q39. What is dynamic programming?

**Answer:** It solves overlapping subproblems once and reuses their results.

**Example:** Memoized Fibonacci avoids repeated recursive work.

### Q40. How do you detect a linked-list cycle?

**Answer:** Use slow and fast pointers; if they meet, the list contains a cycle.

**Example:** Move one pointer by one node and the other by two.

## References

- [Princeton Algorithms companion site](https://algs4.cs.princeton.edu/home/)
- [Java Collections Framework guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html)
