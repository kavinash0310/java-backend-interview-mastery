# Tricky Java Coding Interview Questions

Forty tricky-level Java coding problems for interview practice, from mid-level screening rounds to advanced product-company onsite discussions. Each answer gives the core idea, a Java solution, and time/space complexity.

> These are representative practice problems, not claims about verbatim questions asked by a particular company. Confirm constraints, explain edge cases, and discuss trade-offs before presenting code.

The snippets target Java 17 or later and are independent examples. Add them one at a time to a utility class; shared `ListNode`, `RandomNode`, and `TreeNode` definitions are in the [module guide](README.md).

## Numeric and Array Edge Cases

### Q1. Parse a signed decimal string into a clamped 32-bit integer

**Problem:** Ignore leading spaces, accept one sign, consume digits until the first non-digit, and clamp overflow to int bounds.

**Approach:** Parse digit-by-digit using a long bound; do not parse the whole input as one number.

**Java solution:**

```java
static int atoi(String s) { int i=0,n=s.length();while(i<n&&s.charAt(i)==' ')i++;int sign=1;if(i<n&&(s.charAt(i)=='+'||s.charAt(i)=='-'))sign=s.charAt(i++)=='-'?-1:1;long x=0;while(i<n&&s.charAt(i)>='0'&&s.charAt(i)<='9'){x=x*10+s.charAt(i++)-'0';long v=sign*x;if(v>Integer.MAX_VALUE)return Integer.MAX_VALUE;if(v<Integer.MIN_VALUE)return Integer.MIN_VALUE;}return (int)(sign*x); }
```

**Complexity:** O(n) time, O(1) space

### Q2. Divide two 32-bit integers without multiplication or division operators

**Problem:** Truncate toward zero and clamp overflow to int bounds.

**Approach:** Subtract doubled divisor chunks from the dividend using long magnitudes; restore the sign at the end.

**Java solution:**

```java
static int divide(int a,int b) { if(b==0)throw new ArithmeticException();if(a==Integer.MIN_VALUE&&b==-1)return Integer.MAX_VALUE;long x=Math.abs((long)a),y=Math.abs((long)b),q=0;for(int i=31;i>=0;i--)if((x>>i)>=y){x-=y<<i;q|=1L<<i;}return (int)(((a<0)^(b<0))?-q:q); }
```

**Complexity:** O(32) time, O(1) space

### Q3. Compute x raised to an integer exponent including Integer.MIN_VALUE

**Problem:** Return x^n in logarithmic time; define 0 raised to a negative power as invalid.

**Approach:** Use exponentiation by squaring with a long exponent magnitude so negating MIN_VALUE is safe.

**Java solution:**

```java
static double pow(double x,int n) { if(x==0&&n<0)throw new ArithmeticException();long e=n; if(e<0)e=-e;double r=1;while(e>0){if((e&1)==1)r*=x;x*=x;e>>=1;}return n<0?1/r:r; }
```

**Complexity:** O(log |n|) time, O(1) space

### Q4. Search a rotated sorted array that may contain duplicates

**Problem:** Return any matching index or -1; duplicates can make the sorted half ambiguous.

**Approach:** When both ends equal the midpoint, shrink both ends; otherwise use the sorted half to discard a range.

**Java solution:**

```java
static boolean searchRotated(int[] a,int t) { int l=0,r=a.length-1;while(l<=r){int m=l+(r-l)/2;if(a[m]==t)return true;if(a[l]==a[m]&&a[m]==a[r]){l++;r--;}else if(a[l]<=a[m]){if(a[l]<=t&&t<a[m])r=m-1;else l=m+1;}else{if(a[m]<t&&t<=a[r])l=m+1;else r=m-1;}}return false; }
```

**Complexity:** O(log n) average, O(n) worst case, O(1) space

### Q5. Find the first missing positive integer

**Problem:** Return the smallest positive value absent from an unsorted array in O(n) time and O(1) extra space.

**Approach:** Place each value x in slot x-1 when x is within [1,n], then find the first mismatch.

**Java solution:**

```java
static int firstMissingPositive(int[] a) { int n=a.length;for(int i=0;i<n;i++)while(a[i]>0&&a[i]<=n&&a[a[i]-1]!=a[i]){int j=a[i]-1,t=a[i];a[i]=a[j];a[j]=t;}for(int i=0;i<n;i++)if(a[i]!=i+1)return i+1;return n+1; }
```

**Complexity:** O(n) time, O(1) extra space; modifies input

### Q6. Find the duplicate in values 1 through n without modifying the array

**Problem:** An array contains n+1 integers, each in [1,n], with at least one value repeated. Find a duplicate without modifying the array and using O(1) extra space.

**Approach:** Treat values as pointers and use Floyd’s cycle detection, then find the cycle entrance.

**Java solution:**

```java
static int findDuplicate(int[] a) { int slow=a[0],fast=a[a[0]];while(slow!=fast){slow=a[slow];fast=a[a[fast]];}slow=0;while(slow!=fast){slow=a[slow];fast=a[fast];}return slow; }
```

**Complexity:** O(n) time, O(1) space

### Q7. Find the 3-sum triplet closest to a target

**Problem:** Return the sum of three values closest to target; input has at least three values.

**Approach:** Sort, fix one value, and move two pointers according to whether the long sum is too low or high; compare distances as long values.

**Java solution:**

```java
static long threeSumClosest(int[] a,long t) { Arrays.sort(a);long best=(long)a[0]+a[1]+a[2];for(int i=0;i<a.length-2;i++){int l=i+1,r=a.length-1;while(l<r){long s=(long)a[i]+a[l]+a[r];if(Math.abs(s-t)<Math.abs(best-t))best=s;if(s==t)return s;if(s<t)l++;else r--;}}return best; }
```

**Complexity:** O(n²) time, O(1) extra space

### Q8. Find unique quadruplets that sum to target

**Problem:** Return unique value quadruplets; input may contain duplicates and large integers.

**Approach:** Sort, fix two positions, and use two pointers while skipping repeated values; use long for sums.

**Java solution:**

```java
static List<List<Integer>> fourSum(int[] a,long t) { Arrays.sort(a);List<List<Integer>>o=new ArrayList<>();for(int i=0;i<a.length-3;i++){if(i>0&&a[i]==a[i-1])continue;for(int j=i+1;j<a.length-2;j++){if(j>i+1&&a[j]==a[j-1])continue;int l=j+1,r=a.length-1;while(l<r){long s=(long)a[i]+a[j]+a[l]+a[r];if(s==t){o.add(List.of(a[i],a[j],a[l],a[r]));int x=a[l],y=a[r];while(l<r&&a[l]==x)l++;while(l<r&&a[r]==y)r--;}else if(s<t)l++;else r--;}}}return o; }
```

**Complexity:** O(n³) time, O(1) extra space excluding output

### Q9. Find the longest substring with at most k distinct characters

**Problem:** Return zero for k<=0; characters may repeat arbitrarily.

**Approach:** Expand a frequency-map window and move its left edge until no more than k keys remain.

**Java solution:**

```java
static int longestAtMostK(String s,int k) { if(k<=0)return 0;Map<Character,Integer>m=new HashMap<>();int l=0,b=0;for(int r=0;r<s.length();r++){m.merge(s.charAt(r),1,Integer::sum);while(m.size()>k){char c=s.charAt(l++);if(m.merge(c,-1,Integer::sum)==0)m.remove(c);}b=Math.max(b,r-l+1);}return b; }
```

**Complexity:** O(n) expected time, O(k) space

### Q10. Find the shortest subsequence of s containing t in order

**Problem:** Given non-empty strings s and t, return the shortest contiguous substring of s that contains t as a subsequence in order, or an empty string if none exists.

**Approach:** For each matching end, scan backward through t to find its earliest start, then continue after that start.

**Java solution:**

```java
static String minWindowSubsequence(String s,String t) { int best=Integer.MAX_VALUE,start=-1;for(int i=0,j=0;i<s.length();i++){if(s.charAt(i)==t.charAt(j)){j++;if(j==t.length()){int e=i;j--;while(j>=0){if(s.charAt(i)==t.charAt(j))j--;i--;}i++;if(e-i+1<best){best=e-i+1;start=i;}j=0;}}}return start<0?"":s.substring(start,start+best); }
```

**Complexity:** O(|s|·|t|) worst case, O(1) extra space

## Windows, Intervals, and Greedy Choices

### Q11. Find next greater values in a circular array

**Problem:** For each position, return the first strictly greater value encountered while moving forward, wrapping once; otherwise -1.

**Approach:** Scan indices twice and keep unresolved indices in a decreasing stack; push only on the first pass.

**Java solution:**

```java
static int[] nextGreaterCircular(int[] a) { int n=a.length;int[]o=new int[n];Arrays.fill(o,-1);Deque<Integer>s=new ArrayDeque<>();for(int i=0;i<2*n;i++){int j=i%n;while(!s.isEmpty()&&a[s.peek()]<a[j])o[s.pop()]=a[j];if(i<n)s.push(j);}return o; }
```

**Complexity:** O(n) time, O(n) space

### Q12. Find the maximum-sum circular subarray

**Problem:** A nonempty subarray may wrap from the end to the beginning.

**Approach:** Compare ordinary Kadane maximum with total sum minus the minimum subarray; handle all-negative input separately.

**Java solution:**

```java
static int maxCircular(int[] a) { int total=0,max=a[0],min=a[0],curMax=0,curMin=0;for(int x:a){curMax=Math.max(x,curMax+x);max=Math.max(max,curMax);curMin=Math.min(x,curMin+x);min=Math.min(min,curMin);total+=x;}return max<0?max:Math.max(max,total-min); }
```

**Complexity:** O(n) time, O(1) space; use long for wide sums

### Q13. Build a stock span calculator for an online price stream

**Problem:** For each new price, return the number of consecutive prior days with price <= today, including today.

**Approach:** Keep a decreasing stack of price and accumulated span pairs.

**Java solution:**

```java
class StockSpanner { Deque<int[]>s=new ArrayDeque<>();int next(int p){int span=1;while(!s.isEmpty()&&s.peek()[0]<=p)span+=s.pop()[1];s.push(new int[]{p,span});return span;} }
```

**Complexity:** Amortized O(1) per price, O(n) total space

### Q14. Return the median of every sliding window of size k

**Problem:** Input can contain duplicates; output one median per full window.

**Approach:** Maintain two counted ordered maps for lower and upper halves, rebalance after insertion and removal, and read their boundary keys.

**Java solution:**

```java
class WindowMedian { TreeMap<Integer,Integer> lo=new TreeMap<>(),hi=new TreeMap<>();int nl=0,nh=0;void add(int x){if(nl==0||x<=lo.lastKey()){inc(lo,x);nl++;}else{inc(hi,x);nh++;}balance();}void remove(int x){if(lo.containsKey(x)){dec(lo,x);nl--;}else{dec(hi,x);nh--;}balance();}void balance(){while(nl>nh+1){int x=lo.lastKey();dec(lo,x);nl--;inc(hi,x);nh++;}while(nl<nh){int x=hi.firstKey();dec(hi,x);nh--;inc(lo,x);nl++;}}void inc(TreeMap<Integer,Integer>m,int x){m.merge(x,1,Integer::sum);}void dec(TreeMap<Integer,Integer>m,int x){m.compute(x,(k,v)->v==1?null:v-1);}double median(){return nl>nh?lo.lastKey():((long)lo.lastKey()+hi.firstKey())/2.0;}}
static double[] medians(int[] a,int k){WindowMedian w=new WindowMedian();double[]o=new double[a.length-k+1];for(int i=0;i<a.length;i++){w.add(a[i]);if(i>=k)w.remove(a[i-k]);if(i>=k-1)o[i-k+1]=w.median();}return o;}
```

**Complexity:** O(n log k) time, O(k) space

### Q15. Remove the fewest intervals so the rest do not overlap

**Problem:** Intervals are half-open [start,end); intervals touching at an endpoint do not overlap.

**Approach:** Sort by end time and greedily retain each interval whose start is at least the last kept end.

**Java solution:**

```java
static int eraseOverlap(int[][] a) { Arrays.sort(a,Comparator.comparingInt(x->x[1]));int kept=0,end=Integer.MIN_VALUE;for(int[] x:a)if(kept==0||x[0]>=end){kept++;end=x[1];}return a.length-kept; }
```

**Complexity:** O(n log n) time, O(1) extra space

### Q16. Find the minimum arrows to burst all balloons

**Problem:** An arrow at x bursts intervals that include x; touching endpoints count as intersecting.

**Approach:** Sort by end and shoot at the earliest end not already covered.

**Java solution:**

```java
static int minArrows(int[][] a) { if(a.length==0)return 0;Arrays.sort(a,Comparator.comparingInt(x->x[1]));int count=0;long x=Long.MIN_VALUE;for(int[] b:a)if(count==0||b[0]>x){count++;x=b[1];}return count; }
```

**Complexity:** O(n log n) time, O(1) extra space

### Q17. Find common free time across employee schedules

**Problem:** Each employee’s busy intervals are sorted and disjoint; return gaps shared by everyone.

**Approach:** Flatten and sort all busy intervals, merge their union, and report gaps between merged ranges.

**Java solution:**

```java
static List<int[]> freeTime(List<List<int[]>> schedules) { List<int[]> a=new ArrayList<>();for(List<int[]> x:schedules)a.addAll(x);a.sort(Comparator.comparingInt(x->x[0]));List<int[]>o=new ArrayList<>();if(a.isEmpty())return o;int end=a.get(0)[1];for(int i=1;i<a.size();i++){if(a.get(i)[0]>end)o.add(new int[]{end,a.get(i)[0]});end=Math.max(end,a.get(i)[1]);}return o; }
```

**Complexity:** O(N log N) time, O(N) space

### Q18. Compare version components that may be too large for integer parsing

**Problem:** Compare dot-separated nonnegative numeric components; leading zeroes do not change a component and missing pieces are zero.

**Approach:** Strip leading zeroes and compare component lengths, then lexicographically, avoiding numeric overflow.

**Java solution:**

```java
static int compareHugeVersions(String a,String b) { String[]x=a.split("\\.",-1),y=b.split("\\.",-1);for(int i=0;i<Math.max(x.length,y.length);i++){String p=norm(i<x.length?x[i]:"0"),q=norm(i<y.length?y[i]:"0");if(p.length()!=q.length())return Integer.compare(p.length(),q.length());int c=p.compareTo(q);if(c!=0)return Integer.signum(c);}return 0;}
static String norm(String s){int i=0;while(i<s.length()-1&&s.charAt(i)=='0')i++;return s.substring(i);}
```

**Complexity:** O(total characters) time and space

### Q19. Find the longest valid parentheses substring

**Problem:** Return the length of the longest contiguous balanced parentheses substring.

**Approach:** Use a stack of indices with a sentinel boundary; reset the boundary after unmatched closing parentheses.

**Java solution:**

```java
static int longestValidParentheses(String s) { Deque<Integer>st=new ArrayDeque<>();st.push(-1);int best=0;for(int i=0;i<s.length();i++){if(s.charAt(i)=='(')st.push(i);else{st.pop();if(st.isEmpty())st.push(i);else best=Math.max(best,i-st.peek());}}return best; }
```

**Complexity:** O(n) time, O(n) space

### Q20. Remove k digits to form the smallest possible nonnegative number

**Problem:** The input is a decimal string; remove exactly k digits, then strip leading zeroes.

**Approach:** Use a monotonic increasing digit stack, popping larger preceding digits while removals remain.

**Java solution:**

```java
static String removeKdigits(String s,int k) { Deque<Character>st=new ArrayDeque<>();for(char c:s.toCharArray()){while(k>0&&!st.isEmpty()&&st.peekLast()>c){st.removeLast();k--;}st.addLast(c);}while(k-->0)st.removeLast();StringBuilder b=new StringBuilder();while(!st.isEmpty()&&(st.peekFirst()=='0'))st.removeFirst();for(char c:st)b.append(c);return b.length()==0?"0":b.toString(); }
```

**Complexity:** O(n) time, O(n) space

## Parsing, Unicode, and Ordering

### Q21. Arrange nonnegative integers to form the largest concatenated number

**Problem:** Return a string because the result may exceed integer ranges; all-zero input should return 0.

**Approach:** Sort string forms by comparing xy against yx, then join and normalize leading zeroes.

**Java solution:**

```java
static String largestNumber(int[] a) { if(a.length==0)return "";String[]s=Arrays.stream(a).mapToObj(String::valueOf).toArray(String[]::new);Arrays.sort(s,(x,y)->(y+x).compareTo(x+y));if(s[0].equals("0"))return "0";return String.join("",s); }
```

**Complexity:** O(n log n · L) time, O(nL) space

### Q22. Compute floor square root without multiplication overflow

**Problem:** Return floor(sqrt(x)) for a nonnegative 32-bit integer.

**Approach:** Binary-search the answer and compare mid with x/mid rather than mid*mid.

**Java solution:**

```java
static int sqrt(int x) { if(x<2)return x;int l=1,r=x/2,ans=0;while(l<=r){int m=l+(r-l)/2;if(m<=x/m){ans=m;l=m+1;}else r=m-1;}return ans; }
```

**Complexity:** O(log x) time, O(1) space

### Q23. Check whether Unicode text is a palindrome by code point

**Problem:** Check whether Unicode text is a palindrome after ignoring non-letter/non-digit code points and comparing remaining code points with Character.toLowerCase. This uses simple lowercase mapping, not full Unicode case folding.

**Approach:** Convert to code points, skip non-alphanumeric values from both ends, and compare lowercase code points.

**Java solution:**

```java
static boolean unicodePalindrome(String s) { int[] a=s.codePoints().toArray();int l=0,r=a.length-1;while(l<r){if(!Character.isLetterOrDigit(a[l]))l++;else if(!Character.isLetterOrDigit(a[r]))r--;else if(Character.toLowerCase(a[l++])!=Character.toLowerCase(a[r--]))return false;}return true; }
```

**Complexity:** O(n) time and O(n) code-point storage

### Q24. Compare Unicode character frequencies without splitting surrogate pairs

**Problem:** Return whether two strings contain the same Unicode code points with the same counts.

**Approach:** Count code points in a map, decrement with the other string, and reject any negative count.

**Java solution:**

```java
static boolean unicodeAnagram(String a,String b) { Map<Integer,Integer>m=new HashMap<>();a.codePoints().forEach(c->m.merge(c,1,Integer::sum));PrimitiveIterator.OfInt it=b.codePoints().iterator();while(it.hasNext())if(m.merge(it.nextInt(),-1,Integer::sum)<0)return false;return a.codePointCount(0,a.length())==b.codePointCount(0,b.length()); }
```

**Complexity:** O(n+m) expected time, O(distinct code points) space

### Q25. Find the greatest common divisor string

**Problem:** Return the longest string that divides both strings by repetition, or empty if no such string exists.

**Approach:** A common divisor exists only when concatenating in either order gives the same text; its length is gcd of the input lengths.

**Java solution:**

```java
static String gcdOfStrings(String a,String b) { if(!(a+b).equals(b+a))return "";int n=gcd(a.length(),b.length());return a.substring(0,n); }
static int gcd(int a,int b){while(b!=0){int t=a%b;a=b;b=t;}return a;}
```

**Complexity:** O(n+m) time and space due to concatenation

### Q26. Allow each sorted-array value to appear at most twice in place

**Problem:** Return the new logical length and preserve sorted order.

**Approach:** Write x when fewer than two values have been written or x differs from the value two slots back.

**Java solution:**

```java
static int atMostTwo(int[] a) { int w=0;for(int x:a)if(w<2||x!=a[w-2])a[w++]=x;return w; }
```

**Complexity:** O(n) time, O(1) extra space

### Q27. Check whether a linked list is a palindrome and restore it afterward

**Problem:** The original list structure must be unchanged after the check.

**Approach:** Find the midpoint, reverse the second half, compare values, then reverse it back even on mismatch.

**Java solution:**

```java
static boolean listPalindrome(ListNode h) { if(h==null||h.next==null)return true;ListNode slow=h,fast=h;while(fast.next!=null&&fast.next.next!=null){slow=slow.next;fast=fast.next.next;}ListNode rev=reverseList(slow.next),p=h,q=rev;boolean ok=true;while(q!=null){if(p.val!=q.val)ok=false;p=p.next;q=q.next;}slow.next=reverseList(rev);return ok;}
static ListNode reverseList(ListNode h){ListNode p=null;while(h!=null){ListNode n=h.next;h.next=p;p=h;h=n;}return p;}
```

**Complexity:** O(n) time, O(1) space

### Q28. Group tree nodes by vertical column, then row and value

**Problem:** For equal row and column, sort node values; output columns from left to right.

**Approach:** BFS with row/column coordinates, collect triples, sort by column, row, then value, and group.

**Java solution:**

```java
static List<List<Integer>> verticalTraversal(TreeNode root) { List<int[]>a=new ArrayList<>();Queue<Object[]>q=new ArrayDeque<>();if(root!=null)q.add(new Object[]{root,0,0});while(!q.isEmpty()){Object[]x=q.poll();TreeNode n=(TreeNode)x[0];int r=(int)x[1],c=(int)x[2];a.add(new int[]{c,r,n.val});if(n.left!=null)q.add(new Object[]{n.left,r+1,c-1});if(n.right!=null)q.add(new Object[]{n.right,r+1,c+1});}a.sort(Comparator.<int[]>comparingInt(x->x[0]).thenComparingInt(x->x[1]).thenComparingInt(x->x[2]));List<List<Integer>>o=new ArrayList<>();int last=Integer.MIN_VALUE;for(int[]x:a){if(x[0]!=last){o.add(new ArrayList<>());last=x[0];}o.get(o.size()-1).add(x[2]);}return o;}
```

**Complexity:** O(n log n) time, O(n) space

### Q29. Build a binary tree from preorder and inorder traversals

**Problem:** Values are unique and both traversals describe the same tree.

**Approach:** Map inorder positions; consume preorder from left to right and recursively split by the root position.

**Java solution:**

```java
static TreeNode buildTree(int[] pre,int[] in) { Map<Integer,Integer>m=new HashMap<>();for(int i=0;i<in.length;i++)m.put(in[i],i);int[]p={0};return build(pre,0,in.length-1,p,m);}
static TreeNode build(int[]pre,int l,int r,int[]p,Map<Integer,Integer>m){if(l>r)return null;int v=pre[p[0]++],k=m.get(v);TreeNode n=new TreeNode(v);n.left=build(pre,l,k-1,p,m);n.right=build(pre,k+1,r,p,m);return n;}
```

**Complexity:** O(n) time, O(n) map and recursion

### Q30. Implement a circular deque with fixed capacity

**Problem:** Support insert/delete at both ends and get front/rear; operations must handle full, empty, and capacity zero.

**Approach:** Track front index and size modulo capacity; guard zero capacity before modulo.

**Java solution:**

```java
class MyDeque { int[]a;int front=0,size=0;MyDeque(int k){a=new int[k];}boolean insertFront(int x){if(size==a.length)return false;if(a.length==0)return false;front=Math.floorMod(front-1,a.length);a[front]=x;size++;return true;}boolean insertLast(int x){if(size==a.length)return false;if(a.length==0)return false;a[(front+size)%a.length]=x;size++;return true;}boolean deleteFront(){if(size==0)return false;front=(front+1)%a.length;size--;return true;}boolean deleteLast(){if(size==0)return false;size--;return true;}int getFront(){return size==0?-1:a[front];}int getRear(){return size==0?-1:a[(front+size-1)%a.length];} }
```

**Complexity:** O(1) per operation, O(capacity) space

## Stateful, Concurrent, and Probabilistic Design

### Q31. Find the minimum integer eating speed to finish piles within h hours

**Problem:** Each hour can process one pile; return the smallest positive speed that finishes on time.

**Approach:** Binary-search speed from one to the largest pile and sum ceil(pile/speed) using long arithmetic.

**Java solution:**

```java
static int minEatingSpeed(int[] p,int h) { int l=1,r=Arrays.stream(p).max().orElse(1);while(l<r){int m=l+(r-l)/2;long hrs=0;for(int x:p)hrs+=(x+(long)m-1)/m;if(hrs<=h)r=m;else l=m+1;}return l; }
```

**Complexity:** O(n log maxPile) time, O(1) space

### Q32. Find the longest run of ones after flipping at most k zeroes

**Problem:** The input is binary; return the maximum contiguous length achievable.

**Approach:** Use a sliding window and shrink it whenever zero count exceeds k.

**Java solution:**

```java
static int longestOnes(int[] a,int k) { int l=0,zeros=0,best=0;for(int r=0;r<a.length;r++){if(a[r]==0)zeros++;while(zeros>k)if(a[l++]==0)zeros--;best=Math.max(best,r-l+1);}return best; }
```

**Complexity:** O(n) time, O(1) space

### Q33. Find the minimum swaps to group values <= k together

**Problem:** Values may be duplicated; any swap of two positions is allowed.

**Approach:** Count good values, then slide a window of that size and minimize bad values inside it.

**Java solution:**

```java
static int minSwapsGroup(int[] a,int k) { int good=0;for(int x:a)if(x<=k)good++;if(good<2)return 0;int bad=0;for(int i=0;i<good;i++)if(a[i]>k)bad++;int best=bad;for(int i=good;i<a.length;i++){if(a[i-good]>k)bad--;if(a[i]>k)bad++;best=Math.min(best,bad);}return best; }
```

**Complexity:** O(n) time, O(1) space

### Q34. Count subarrays with exactly k distinct integers

**Problem:** Values can be arbitrary integers; return the exact count in long.

**Approach:** Compute atMost(k) minus atMost(k-1), counting each valid window by its number of suffixes.

**Java solution:**

```java
static long exactlyK(int[] a,int k) { return atMost(a,k)-atMost(a,k-1); }
static long atMost(int[]a,int k){if(k<0)return 0;Map<Integer,Integer>m=new HashMap<>();int l=0;long ans=0;for(int r=0;r<a.length;r++){m.merge(a[r],1,Integer::sum);while(m.size()>k){int x=a[l++];if(m.merge(x,-1,Integer::sum)==0)m.remove(x);}ans+=r-l+1;}return ans;}
```

**Complexity:** O(n) expected time, O(k) space

### Q35. Reconstruct one optimal item set for 0/1 knapsack

**Problem:** Each item can be chosen once; maximize value without exceeding capacity and return selected indices.

**Approach:** Build a two-dimensional DP table, then backtrack where including an item changed the optimum.

**Java solution:**

```java
static List<Integer> knapsack(int[] wt,int[] val,int cap) { int n=wt.length;int[][]d=new int[n+1][cap+1];for(int i=1;i<=n;i++)for(int c=0;c<=cap;c++){d[i][c]=d[i-1][c];if(wt[i-1]<=c)d[i][c]=Math.max(d[i][c],d[i-1][c-wt[i-1]]+val[i-1]);}List<Integer>o=new ArrayList<>();for(int i=n,c=cap;i>0;i--)if(d[i][c]!=d[i-1][c]){o.add(i-1);c-=wt[i-1];}Collections.reverse(o);return o; }
```

**Complexity:** O(n·capacity) time and space

### Q36. Count decodings where * can represent digits 1 through 9

**Problem:** A star is a wildcard digit; return the count modulo 1,000,000,007.

**Approach:** Use DP with separate counts for one-character and two-character interpretations, handling 1* and 2* specially.

**Java solution:**

```java
static int decodeStar(String s) { long a=1,b=s.isEmpty()?0:one(s.charAt(0));for(int i=1;i<s.length();i++){long c=(one(s.charAt(i))*b+two(s.charAt(i-1),s.charAt(i))*a)%1000000007;a=b;b=c;}return (int)b;}
static int one(char c){return c=='*'?9:c=='0'?0:1;}
static int two(char a,char b){if(a=='*'&&b=='*')return 15;if(a=='*')return b<='6'?2:1;if(b=='*')return a=='1'?9:a=='2'?6:0;int n=(a-'0')*10+b-'0';return n>=10&&n<=26?1:0;}
```

**Complexity:** O(n) time, O(1) space

### Q37. Implement a bounded blocking queue for multiple producers and consumers

**Problem:** put waits while full; take waits while empty; operations are thread-safe and interruption is preserved.

**Approach:** Use one ReentrantLock with notFull/notEmpty conditions and signal after changing queue state.

**Java solution:**

```java
class BoundedQueue<E> { Object[]a;int head=0,tail=0,size=0;ReentrantLock lock=new ReentrantLock();Condition notFull=lock.newCondition(),notEmpty=lock.newCondition();BoundedQueue(int c){if(c<=0)throw new IllegalArgumentException();a=new Object[c];}void put(E x)throws InterruptedException{Objects.requireNonNull(x);lock.lockInterruptibly();try{while(size==a.length)notFull.await();a[tail]=x;tail=(tail+1)%a.length;size++;notEmpty.signal();}finally{lock.unlock();}}@SuppressWarnings("unchecked") E take()throws InterruptedException{lock.lockInterruptibly();try{while(size==0)notEmpty.await();E x=(E)a[head];a[head]=null;head=(head+1)%a.length;size--;notFull.signal();return x;}finally{lock.unlock();}} }
```

**Complexity:** O(1) queue work per operation, O(capacity) space

### Q38. Implement a thread-safe token bucket rate limiter

**Problem:** Implement a bounded, single-JVM blocking queue for multiple producers and consumers. put waits while full; take waits while empty; null elements are not allowed.

**Approach:** Synchronize refill and consumption, use monotonic nanoseconds, and cap accumulated tokens at capacity.

**Java solution:**

```java
class TokenBucket { final double cap,perNano;double tokens;long last;TokenBucket(double capacity,double perSecond,long now){cap=capacity;perNano=perSecond/1_000_000_000.0;tokens=capacity;last=now;}synchronized boolean allow(long now){long elapsed=Math.max(0,now-last);tokens=Math.min(cap,tokens+elapsed*perNano);last=Math.max(last,now);if(tokens<1)return false;tokens-=1;return true;} }
```

**Complexity:** O(1) time and O(1) space per limiter; synchronized calls serialize

### Q39. Implement an expiring key-value cache with lazy expiration

**Problem:** Implement a thread-safe, in-process token bucket using a monotonic nanosecond clock. capacity and refill rate are positive; allow one token per successful request.

**Approach:** Store value and expiry together, compare with an injected monotonic clock on every read, and remove expired entries lazily.

**Java solution:**

```java
class TtlCache<K,V> { static class Entry<V>{V value;long createdAt,ttl;Entry(V v,long t,long d){value=v;createdAt=t;ttl=d;}}private final Map<K,Entry<V>>m=new HashMap<>();synchronized void put(K k,V v,long ttlNanos,long nowNanos){if(ttlNanos<=0){m.remove(k);return;}m.put(k,new Entry<>(v,nowNanos,ttlNanos));}synchronized V get(K k,long nowNanos){Entry<V>e=m.get(k);if(e==null)return null;if(nowNanos-e.createdAt>=e.ttl){m.remove(k);return null;}return e.value;} }
```

**Complexity:** O(1) average operations, O(number of unexpired and untouched expired keys) space

### Q40. Pick an index with probability proportional to its positive weight

**Problem:** Pick an index with probability proportional to its positive integer weight. The total weight fits in a signed long.

**Approach:** Build long prefix sums and binary-search an exactly uniform long from [1,total]; require the total weight to fit in long.

**Java solution:**

```java
class WeightedPicker { long[]p;long total=0;WeightedPicker(int[]w){if(w.length==0)throw new IllegalArgumentException();p=new long[w.length];for(int i=0;i<w.length;i++){if(w[i]<=0)throw new IllegalArgumentException();total+=w[i];p[i]=total;}}int pick(){long x=ThreadLocalRandom.current().nextLong(total)+1;int l=0,h=p.length-1;while(l<h){int m=(l+h)/2;if(p[m]>=x)h=m;else l=m+1;}return l;} }
```

**Complexity:** O(n) setup, O(log n) per pick, O(n) space

---

[Module guide](README.md) · [Repository home](../README.md)