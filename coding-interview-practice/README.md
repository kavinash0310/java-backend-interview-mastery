# Module 32 — Java Coding Interview Practice

Practice 160 Java coding problems in four difficulty bands. The sets cover common screening patterns, mid-level data-structure and algorithm interviews, advanced onsite problems, and tricky edge cases or implementation details.

## Choose a level

- [Easy — 40 questions](easy-interview-questions.md): strings, arrays, maps, basic search, number handling, and stacks.
- [Medium — 40 questions](medium-interview-questions.md): sliding windows, recursion, linked lists, trees, graphs, and dynamic programming.
- [Hard — 40 questions](hard-interview-questions.md): advanced dynamic programming, graph search, optimization, and specialized data structures.
- [Tricky — 40 questions](tricky-interview-questions.md): boundaries, duplicates, overflow, Unicode, intervals, stateful structures, and concurrency.

## How to practice

1. Restate the input, output, and constraints before coding.
2. Explain a straightforward approach, then improve it when the constraints require it.
3. Walk through one normal example and one edge case.
4. Give time and extra-space complexity, and explain why the algorithm is correct.
5. For stateful or concurrent designs, identify the invariants and what happens at the boundaries.

Solutions are intentionally concise Java 17+ interview snippets rather than complete applications. Add one snippet at a time to a utility class. Several problems use these shared node shapes:

```java
static final class ListNode {
    int val; ListNode next;
    ListNode(int val) { this.val = val; }
}
static final class RandomNode {
    int val; RandomNode next, random;
    RandomNode(int val) { this.val = val; }
}
static final class TreeNode {
    int val; TreeNode left, right;
    TreeNode(int val) { this.val = val; }
}
```

Most examples use `java.util.*`; concurrency examples also use `java.util.concurrent.*` and `java.util.concurrent.locks.*`. Individual prompts state assumptions such as non-empty inputs, bounded key counts, or unique values where the solution relies on them.

## Interview-prep references

- [Amazon SDE II interview preparation](https://amazon.jobs/content/en/how-we-hire/sde-ii-interview-prep)
- [Amazon software development interview topics](https://amazon.jobs/content/en/how-we-hire/interview-prep/software-development-topics)

These links describe one company's public preparation guidance and are included as general context; the practice set is not an official company question list.

## Course map

- [Course home](../README.md)
- [Data Structures and Algorithms](../data-structures-algorithms/README.md)
- [Online Assessment Practice](../online-assessments/README.md)

## Question index

- [Easy](easy-interview-questions.md)
- [Medium](medium-interview-questions.md)
- [Hard](hard-interview-questions.md)
- [Tricky](tricky-interview-questions.md)