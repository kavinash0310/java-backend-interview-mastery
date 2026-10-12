# Easy Java Coding Interview Questions

Forty easy-level Java coding problems for interview practice, from mid-level screening rounds to advanced product-company onsite discussions. Each answer gives the core idea, a Java solution, and time/space complexity.

> These are representative practice problems, not claims about verbatim questions asked by a particular company. Confirm constraints, explain edge cases, and discuss trade-offs before presenting code.

The snippets target Java 17 or later and are independent examples. Add them one at a time to a utility class; shared `ListNode`, `RandomNode`, and `TreeNode` definitions are in the [module guide](README.md).

## Strings and Hashing

### Q1. Reverse a character array in place

**Problem:** Given a char array, reverse it without allocating another array.

**Approach:** Swap the two ends and move inward until the pointers meet.

**Java solution:**

```java
static void reverse(char[] a) { for (int l=0,r=a.length-1; l<r; l++,r--) { char t=a[l]; a[l]=a[r]; a[r]=t; } }
```

**Complexity:** O(n) time, O(1) extra space

### Q2. Check whether text is a normalized palindrome

**Problem:** Ignore case and non-letter or non-digit characters; decide whether the remaining text reads the same both ways.

**Approach:** Move two pointers inward, skipping characters that are not letters or digits.

**Java solution:**

```java
static boolean isPalindrome(String s) { int l=0,r=s.length()-1; while(l<r) { if(!Character.isLetterOrDigit(s.charAt(l))) l++; else if(!Character.isLetterOrDigit(s.charAt(r))) r--; else if(Character.toLowerCase(s.charAt(l++))!=Character.toLowerCase(s.charAt(r--))) return false; } return true; }
```

**Complexity:** O(n) time, O(1) extra space

### Q3. Check whether two lowercase strings are anagrams

**Problem:** Return true when both strings contain the same lowercase English letters with the same counts.

**Approach:** Count one string’s letters and subtract counts for the other; reject unequal lengths.

**Java solution:**

```java
static boolean isAnagram(String a,String b) { if(a.length()!=b.length()) return false; int[] c=new int[26]; for(char x:a.toCharArray()) c[x-'a']++; for(char x:b.toCharArray()) if(--c[x-'a']<0) return false; return true; }
```

**Complexity:** O(n) time, O(1) space for the fixed alphabet

### Q4. Find the index of the first non-repeating lowercase letter

**Problem:** Return the index of the first lowercase English letter that occurs once, or -1.

**Approach:** Count frequencies, then scan the string in its original order.

**Java solution:**

```java
static int firstUnique(String s) { int[] f=new int[26]; for(char c:s.toCharArray()) f[c-'a']++; for(int i=0;i<s.length();i++) if(f[s.charAt(i)-'a']==1) return i; return -1; }
```

**Complexity:** O(n) time, O(1) space for the fixed alphabet

### Q5. Count each character in a string

**Problem:** Return a map from each UTF-16 char value to its occurrence count.

**Approach:** Walk the string once and increment a map entry for each character.

**Java solution:**

```java
static Map<Character,Integer> frequencies(String s) { Map<Character,Integer> m=new HashMap<>(); for(char c:s.toCharArray()) m.merge(c,1,Integer::sum); return m; }
```

**Complexity:** O(n) expected time, O(k) space for k distinct chars

### Q6. Remove repeated characters while preserving first occurrence order

**Problem:** For example, banana becomes ban.

**Approach:** Track seen characters and append only the first occurrence to a StringBuilder.

**Java solution:**

```java
static String uniqueChars(String s) { Set<Character> seen=new HashSet<>(); StringBuilder out=new StringBuilder(); for(char c:s.toCharArray()) if(seen.add(c)) out.append(c); return out.toString(); }
```

**Complexity:** O(n) expected time, O(k) space

### Q7. Find the second-largest distinct integer

**Problem:** Return the second-largest distinct value, or throw when fewer than two distinct values exist.

**Approach:** Maintain the largest and second-largest values in one pass.

**Java solution:**

```java
static int secondLargest(int[] a) { Integer first=null,second=null; for(int x:a) { if(first==null||x>first) { if(first==null||x!=first) second=first; first=x; } else if(x!=first&&(second==null||x>second)) second=x; } if(second==null) throw new IllegalArgumentException(); return second; }
```

**Complexity:** O(n) time, O(1) space

### Q8. Find the minimum and maximum in one pass

**Problem:** Return both values, and define the empty-array behavior.

**Approach:** Update a running minimum and maximum; reject an empty input explicitly.

**Java solution:**

```java
static int[] minMax(int[] a) { if(a.length==0) throw new IllegalArgumentException(); int lo=a[0],hi=a[0]; for(int x:a) { lo=Math.min(lo,x); hi=Math.max(hi,x); } return new int[]{lo,hi}; }
```

**Complexity:** O(n) time, O(1) space

### Q9. Search a sorted array for a target

**Problem:** Return any matching index, or -1 when the target is absent.

**Approach:** Use binary search with an inclusive low/high interval.

**Java solution:**

```java
static int binarySearch(int[] a,int target) { int l=0,r=a.length-1; while(l<=r) { int m=l+(r-l)/2; if(a[m]==target) return m; if(a[m]<target) l=m+1; else r=m-1; } return -1; }
```

**Complexity:** O(log n) time, O(1) space

### Q10. Find the insertion index in a sorted array

**Problem:** Return the first index whose value is at least target, or the array length.

**Approach:** Use a lower-bound binary search on a half-open interval.

**Java solution:**

```java
static int searchInsert(int[] a,int target) { int l=0,r=a.length; while(l<r) { int m=l+(r-l)/2; if(a[m]<target) l=m+1; else r=m; } return l; }
```

**Complexity:** O(log n) time, O(1) space

## Arrays and Search

### Q11. Find the missing number from 0 through n

**Problem:** The array contains n distinct values from [0,n]; return the absent value.

**Approach:** XOR indices and values so matching terms cancel.

**Java solution:**

```java
static int missingNumber(int[] a) { int x=a.length; for(int i=0;i<a.length;i++) x^=i^a[i]; return x; }
```

**Complexity:** O(n) time, O(1) space

### Q12. Find the single value when every other value appears twice

**Problem:** Assume exactly one value appears once and all others appear twice.

**Approach:** XOR cancels equal pairs and leaves the unpaired value.

**Java solution:**

```java
static int singleNumber(int[] a) { int x=0; for(int v:a) x^=v; return x; }
```

**Complexity:** O(n) time, O(1) space

### Q13. Check whether an integer array contains a duplicate

**Problem:** Return true if any value appears at least twice.

**Approach:** Insert values into a set; a failed insertion reveals a duplicate.

**Java solution:**

```java
static boolean containsDuplicate(int[] a) { Set<Integer> s=new HashSet<>(); for(int x:a) if(!s.add(x)) return true; return false; }
```

**Complexity:** O(n) expected time, O(n) space

### Q14. Return the unique intersection of two arrays

**Problem:** Each value should appear once in the result; order does not matter.

**Approach:** Put one array in a set and add matching values from the other to a second set.

**Java solution:**

```java
static int[] intersection(int[] a,int[] b) { Set<Integer> s=new HashSet<>(),out=new HashSet<>(); for(int x:a) s.add(x); for(int x:b) if(s.contains(x)) out.add(x); return out.stream().mapToInt(Integer::intValue).toArray(); }
```

**Complexity:** O(n+m) expected time, O(n+m) space

### Q15. Rotate an array left by k positions in place

**Problem:** Support k larger than the array length and empty arrays.

**Approach:** Normalize k and use the three-reversal method.

**Java solution:**

```java
static void rotateLeft(int[] a,int k) { if(a.length==0) return; k=Math.floorMod(k,a.length); reverse(a,0,k-1); reverse(a,k,a.length-1); reverse(a,0,a.length-1); }
static void reverse(int[] a,int l,int r) { while(l<r) { int t=a[l]; a[l++]=a[r]; a[r--]=t; } }
```

**Complexity:** O(n) time, O(1) space

### Q16. Remove a target value in place

**Problem:** Overwrite retained values at the front and return the new logical length; order may be preserved.

**Approach:** Use a write pointer and copy each non-target value once.

**Java solution:**

```java
static int removeElement(int[] a,int target) { int w=0; for(int x:a) if(x!=target) a[w++]=x; return w; }
```

**Complexity:** O(n) time, O(1) space

### Q17. Reverse the order of words in a sentence

**Problem:** Ignore leading, trailing, and repeated spaces; retain each word’s characters.

**Approach:** Split on whitespace, discard empty pieces, and join in reverse order.

**Java solution:**

```java
static String reverseWords(String s) { String[] w=s.trim().split("\\s+"); if(s.isBlank()) return ""; StringBuilder out=new StringBuilder(); for(int i=w.length-1;i>=0;i--) { if(out.length()>0) out.append(' '); out.append(w[i]); } return out.toString(); }
```

**Complexity:** O(n) time, O(n) space

### Q18. Count English vowels in a string

**Problem:** Count a, e, i, o, u regardless of case.

**Approach:** Normalize each character to lowercase and increment for the five vowel values.

**Java solution:**

```java
static int countVowels(String s) { int n=0; for(char c:s.toLowerCase(Locale.ROOT).toCharArray()) if("aeiou".indexOf(c)>=0) n++; return n; }
```

**Complexity:** O(n) time, O(1) space

### Q19. Find the first character that appears twice

**Problem:** Return the first character whose second occurrence is encountered, or null if none exists.

**Approach:** Scan left to right and return when insertion into a set fails.

**Java solution:**

```java
static Character firstRepeated(String s) { Set<Character> seen=new HashSet<>(); for(char c:s.toCharArray()) if(!seen.add(c)) return c; return null; }
```

**Complexity:** O(n) expected time, O(k) space

### Q20. Solve Two Sum in one pass with a hash map

**Problem:** Given an integer array and a target, return the indices of two different elements that add to the target. Assume exactly one answer exists.

**Approach:** For each value, look for its complement among values already seen; then store the current index.

**Java solution:**

```java
static int[] twoSum(int[] a,int target) { Map<Integer,Integer> seen=new HashMap<>();for(int i=0;i<a.length;i++){long need=(long)target-a[i];if(need>=Integer.MIN_VALUE&&need<=Integer.MAX_VALUE&&seen.containsKey((int)need))return new int[]{seen.get((int)need),i};seen.put(a[i],i);}throw new IllegalArgumentException("No pair");}
```

**Complexity:** O(n) time, O(n) extra space

## Numbers and Counting

### Q21. Generate the FizzBuzz sequence

**Problem:** For 1 through n, use Fizz for multiples of 3, Buzz for 5, and FizzBuzz for both.

**Approach:** Check divisibility by 15 before checking 3 or 5 separately.

**Java solution:**

```java
static List<String> fizzBuzz(int n) { List<String> out=new ArrayList<>(); for(int i=1;i<=n;i++) out.add(i%15==0?"FizzBuzz":i%3==0?"Fizz":i%5==0?"Buzz":String.valueOf(i)); return out; }
```

**Complexity:** O(n) time, O(n) output space

### Q22. Compute factorial for a nonnegative n

**Problem:** Return an exact result rather than silently overflowing a long.

**Approach:** Multiply using BigInteger, starting from one.

**Java solution:**

```java
static BigInteger factorial(int n) { if(n<0) throw new IllegalArgumentException(); BigInteger x=BigInteger.ONE; for(int i=2;i<=n;i++) x=x.multiply(BigInteger.valueOf(i)); return x; }
```

**Complexity:** O(n) multiplications; output uses O(n log n) bits

### Q23. Return the nth Fibonacci number exactly

**Problem:** Use F(0)=0 and F(1)=1; return a BigInteger to avoid overflow.

**Approach:** Iteratively retain the two latest values.

**Java solution:**

```java
static BigInteger fib(int n) { if(n<0) throw new IllegalArgumentException(); BigInteger a=BigInteger.ZERO,b=BigInteger.ONE; for(int i=0;i<n;i++) { BigInteger t=a.add(b); a=b; b=t; } return a; }
```

**Complexity:** O(n) additions, O(1) values aside from growing integer sizes

### Q24. Find the greatest common divisor of nonnegative integers

**Problem:** Return gcd(a,b), including gcd(0,b)=b.

**Approach:** Apply Euclid’s remainder step until the second value is zero.

**Java solution:**

```java
static int gcd(int a,int b) { while(b!=0) { int t=a%b; a=b; b=t; } return a; }
```

**Complexity:** O(log min(a,b)) time, O(1) space

### Q25. Check whether n is prime

**Problem:** Treat values below 2 as non-prime.

**Approach:** Try divisors only through sqrt(n), using i<=n/i to avoid multiplication overflow.

**Java solution:**

```java
static boolean isPrime(int n) { if(n<2) return false; for(int i=2;i<=n/i;i++) if(n%i==0) return false; return true; }
```

**Complexity:** O(sqrt(n)) time, O(1) space

### Q26. Check whether a nonnegative integer is a palindrome

**Problem:** Do not convert it to a string; negative values are not palindromes.

**Approach:** Reverse only the lower half of the digits and compare the halves.

**Java solution:**

```java
static boolean palindromeNumber(int x) { if(x<0||(x%10==0&&x!=0)) return false; int rev=0; while(x>rev) { rev=rev*10+x%10; x/=10; } return x==rev||x==rev/10; }
```

**Complexity:** O(log10 n) time, O(1) space

### Q27. Reverse a signed 32-bit integer with overflow protection

**Problem:** Return 0 if reversing decimal digits would exceed the int range.

**Approach:** Build the result as a long and range-check before returning.

**Java solution:**

```java
static int reverseInt(int x) { long r=0; while(x!=0) { r=r*10+x%10; x/=10; } return r<Integer.MIN_VALUE||r>Integer.MAX_VALUE?0:(int)r; }
```

**Complexity:** O(number of digits) time, O(1) space

### Q28. Sum the decimal digits of a nonnegative integer

**Problem:** Return zero for input zero.

**Approach:** Repeatedly add the remainder after division by ten.

**Java solution:**

```java
static int digitSum(int n) { int s=0; while(n>0) { s+=n%10; n/=10; } return s; }
```

**Complexity:** O(number of digits) time, O(1) space

### Q29. Count one chosen digit in a nonnegative integer

**Problem:** Count digit d in n; define the representation of zero as containing one zero.

**Approach:** Consume digits with remainder; handle n=0 separately.

**Java solution:**

```java
static int countDigit(long n,int d) { if(n<0||d<0||d>9) throw new IllegalArgumentException(); if(n==0) return d==0?1:0; int c=0; while(n>0) { if(n%10==d) c++; n/=10; } return c; }
```

**Complexity:** O(number of digits) time, O(1) space

### Q30. Check whether n is a power of two

**Problem:** Only positive powers of two qualify.

**Approach:** A power of two has exactly one set bit, so clear its lowest set bit and check zero.

**Java solution:**

```java
static boolean isPowerOfTwo(int n) { return n>0&&(n&(n-1))==0; }
```

**Complexity:** O(1) time, O(1) space

## Stacks, Sequences, and Basics

### Q31. Count trailing zeroes in n factorial

**Problem:** Return how many zeroes appear at the end of n! in base ten.

**Approach:** Count factors of five, including repeated powers of five.

**Java solution:**

```java
static int trailingZeroes(int n) { int count=0; while(n>0) { n/=5; count+=n; } return count; }
```

**Complexity:** O(log5 n) time, O(1) space

### Q32. Validate brackets in a string

**Problem:** The input contains only the bracket characters (), [], and {}; return whether they are balanced and correctly nested.

**Approach:** Push opening brackets and require each closer to match the most recent opener.

**Java solution:**

```java
static boolean validBrackets(String s) { Deque<Character> st=new ArrayDeque<>(); for(char c:s.toCharArray()) { if(c=='('||c=='['||c=='{') st.push(c); else { if(st.isEmpty()) return false; char o=st.pop(); if(c==')'&&o!='('||c==']'&&o!='['||c=='}'&&o!='{') return false; } } return st.isEmpty(); }
```

**Complexity:** O(n) time, O(n) space

### Q33. Check whether one string is a subsequence of another

**Problem:** Characters must occur in order but need not be adjacent.

**Approach:** Advance the candidate pointer only when the source character matches it.

**Java solution:**

```java
static boolean isSubsequence(String a,String b) { int i=0; for(int j=0;i<a.length()&&j<b.length();j++) if(a.charAt(i)==b.charAt(j)) i++; return i==a.length(); }
```

**Complexity:** O(n) time, O(1) space

### Q34. Add one to a nonnegative integer represented by digits

**Problem:** Digits are in most-significant-first order; return a new array if carry adds a digit.

**Approach:** Walk from the last digit, clearing nines until a digit can be incremented.

**Java solution:**

```java
static int[] plusOne(int[] d) { for(int i=d.length-1;i>=0;i--) { if(d[i]<9) { d[i]++; return d; } d[i]=0; } int[] r=new int[d.length+1]; r[0]=1; return r; }
```

**Complexity:** O(n) time, O(n) worst-case output space

### Q35. Find the longest run of ones in a binary array

**Problem:** Return zero when the array has no elements or no ones.

**Approach:** Keep a current run length and its maximum while scanning.

**Java solution:**

```java
static int maxConsecutiveOnes(int[] a) { int run=0,best=0; for(int x:a) { run=x==1?run+1:0; best=Math.max(best,run); } return best; }
```

**Complexity:** O(n) time, O(1) space

### Q36. Return squares of a sorted integer array in sorted order

**Problem:** Input may include negative numbers; handle squared-value overflow if values may be large.

**Approach:** Compare absolute values at both ends and fill the result from right to left.

**Java solution:**

```java
static int[] sortedSquares(int[] a) { int n=a.length,l=0,r=n-1; int[] out=new int[n]; for(int k=n-1;k>=0;k--) { long x=(long)a[l]*a[l],y=(long)a[r]*a[r]; if(x>y) { out[k]=(int)x; l++; } else { out[k]=(int)y; r--; } } return out; }
```

**Complexity:** O(n) time, O(n) output space; result must fit in int

### Q37. Remove duplicates from a sorted array in place

**Problem:** Keep one copy of each value and return the number of retained values.

**Approach:** Write a value only when it differs from the last retained value.

**Java solution:**

```java
static int dedupeSorted(int[] a) { if(a.length==0) return 0; int w=1; for(int r=1;r<a.length;r++) if(a[r]!=a[w-1]) a[w++]=a[r]; return w; }
```

**Complexity:** O(n) time, O(1) extra space

### Q38. Find the majority element

**Problem:** Assume one value occurs more than half the time; return that value.

**Approach:** Use Boyer-Moore voting to find a candidate, then verify it if the assumption is not guaranteed.

**Java solution:**

```java
static int majority(int[] a) { int c=0,v=0; for(int x:a) { if(c==0) v=x; c+=x==v?1:-1; } return v; }
```

**Complexity:** O(n) time, O(1) space; verify the candidate if majority is not guaranteed

### Q39. Check whether a magazine can construct a lowercase ransom note

**Problem:** Each magazine character can be used at most once.

**Approach:** Count magazine letters, then consume counts for note letters.

**Java solution:**

```java
static boolean canConstruct(String note,String magazine) { int[] f=new int[26]; for(char c:magazine.toCharArray()) f[c-'a']++; for(char c:note.toCharArray()) if(--f[c-'a']<0) return false; return true; }
```

**Complexity:** O(n+m) time, O(1) space for the fixed alphabet

### Q40. Convert a valid Roman numeral to an integer

**Problem:** Input is a valid Roman numeral using I,V,X,L,C,D,M.

**Approach:** Subtract a symbol when it is smaller than the next symbol; otherwise add it.

**Java solution:**

```java
static int romanToInt(String s) { Map<Character,Integer> v=Map.of('I',1,'V',5,'X',10,'L',50,'C',100,'D',500,'M',1000); int sum=0; for(int i=0;i<s.length();i++) { int x=v.get(s.charAt(i)); sum+=i+1<s.length()&&x<v.get(s.charAt(i+1))?-x:x; } return sum; }
```

**Complexity:** O(n) time, O(1) space

---

[Module guide](README.md) · [Repository home](../README.md)