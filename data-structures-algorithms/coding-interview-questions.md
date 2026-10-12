# Coding Interview Questions: Strings, Lists, and Arrays

Talk through the simple approach first, then give its complexity. In an interview, a correct, readable solution is easier to trust than a clever one you cannot explain.

## First non-repeating character in a string

Count characters, then scan the original string again. The second pass preserves the string's order. This version treats Java char values as units; if full Unicode code points matter, iterate code points instead.

~~~java
static Character firstUnique(String text) {
    Map<Character, Integer> counts = new LinkedHashMap<>();
    for (char ch : text.toCharArray()) {
        counts.merge(ch, 1, Integer::sum);
    }
    for (char ch : text.toCharArray()) {
        if (counts.get(ch) == 1) return ch;
    }
    return null;
}
~~~

Time is O(n); extra space is O(k), where k is the number of distinct characters.

### Stream API version

A stream can keep the same two-pass idea. The map must preserve insertion order for a predictable first character.

~~~java
static Optional<Character> firstUniqueWithStreams(String text) {
    Map<Character, Long> counts = text.chars()
        .mapToObj(ch -> (char) ch)
        .collect(Collectors.groupingBy(
            Function.identity(), LinkedHashMap::new, Collectors.counting()));

    return text.chars()
        .mapToObj(ch -> (char) ch)
        .filter(ch -> counts.get(ch) == 1)
        .findFirst();
}
~~~

For a short interview answer, explain why you count first and then scan in input order. That is the part that makes the result both unique and first.

## Detect a cycle in a linked list

Use Floyd's tortoise-and-hare algorithm. Move one pointer by one node and the other by two. If they meet, a cycle exists; if the fast pointer reaches null, the list ends.

~~~java
static boolean hasCycle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) return true;
    }
    return false;
}
~~~

It runs in O(n) time and O(1) extra space. Compare node references, not node values: two different nodes can contain the same value.

## Find the Nth-largest distinct number

First decide what “Nth-largest” means. Interviewers often mean distinct values, so duplicates count once. For example, in [4, 9, 9, 2], the second-largest distinct value is 4.

~~~java
static Optional<Integer> nthLargest(int[] numbers, int n) {
    if (n < 1) throw new IllegalArgumentException("n must be positive");
    return Arrays.stream(numbers)
        .boxed()
        .distinct()
        .sorted(Comparator.reverseOrder())
        .skip(n - 1L)
        .findFirst();
}
~~~

This straightforward version sorts in O(m log m), where m is the input length. For large input or a streaming source, a bounded min-heap can reduce the extra work. “Second-highest using Streams” is simply this method with n = 2.

## Two Sum

Keep the numbers already seen in a map from value to index. At each number, look for its complement before inserting the current value.

~~~java
static int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> indexByValue = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int needed = target - nums[i];
        if (indexByValue.containsKey(needed)) {
            return new int[] { indexByValue.get(needed), i };
        }
        indexByValue.put(nums[i], i);
    }
    return new int[0];
}
~~~

This is O(n) time and O(n) space on average. Checking before insertion prevents using the same element twice. If the prompt asks for values rather than indexes, return the corresponding pair.

## A practical interview rhythm

1. Confirm edge cases: empty input, duplicates, case sensitivity, and whether Unicode matters.
2. State the basic approach and complexity.
3. Implement with names that make the steps obvious.
4. Walk through one normal example and one edge case.
