# Medium Java Coding Interview Questions

Forty medium-level Java coding problems for interview practice, from mid-level screening rounds to advanced product-company onsite discussions. Each answer gives the core idea, a Java solution, and time/space complexity.

> These are representative practice problems, not claims about verbatim questions asked by a particular company. Confirm constraints, explain edge cases, and discuss trade-offs before presenting code.

The snippets target Java 17 or later and are independent examples. Add them one at a time to a utility class; shared `ListNode`, `RandomNode`, and `TreeNode` definitions are in the [module guide](README.md).

## Strings and Sliding Windows

### Q1. Longest palindromic substring

**Problem:** Return any longest palindromic substring; input length may be zero.

**Approach:** Expand around each character and each gap, keeping the longest range.

**Java solution:**

```java
static String longestPalindrome(String s) { int bestL=0,bestR=0; for(int i=0;i<s.length();i++) { int[] a=expand(s,i,i),b=expand(s,i,i+1); if(a[1]-a[0]>bestR-bestL) { bestL=a[0]; bestR=a[1]; } if(b[1]-b[0]>bestR-bestL) { bestL=b[0]; bestR=b[1]; } } return s.substring(bestL,bestR); }
static int[] expand(String s,int l,int r) { while(l>=0&&r<s.length()&&s.charAt(l)==s.charAt(r)) { l--; r++; } return new int[]{l+1,r}; }
```

**Complexity:** O(n^2) time, O(1) extra space

### Q2. Check whether a string contains a permutation of another

**Problem:** Strings contain lowercase English letters; return whether any substring of s is a permutation of p.

**Approach:** Maintain a fixed-size frequency window of length p and compare counts as it slides.

**Java solution:**

```java
static boolean containsPermutation(String p,String s) { if(p.length()>s.length()) return false; int[] a=new int[26],b=new int[26]; for(int i=0;i<p.length();i++) { a[p.charAt(i)-'a']++; b[s.charAt(i)-'a']++; } if(Arrays.equals(a,b)) return true; for(int i=p.length();i<s.length();i++) { b[s.charAt(i)-'a']++; b[s.charAt(i-p.length())-'a']--; if(Arrays.equals(a,b)) return true; } return false; }
```

**Complexity:** O(26n) time, O(1) space for fixed alphabet

### Q3. Compress a character array in place

**Problem:** Write each repeated run as its character followed by its count when count exceeds one; return new length.

**Approach:** Use separate read and write indices, recording digits of each run length.

**Java solution:**

```java
static int compress(char[] a) { int w=0; for(int r=0;r<a.length;) { char c=a[r]; int start=r; while(r<a.length&&a[r]==c) r++; a[w++]=c; int n=r-start; if(n>1) for(char d:String.valueOf(n).toCharArray()) a[w++]=d; } return w; }
```

**Complexity:** O(n) time, O(log n) temporary digits per run

### Q4. Decode an encoded string such as 3[a2[c]]

**Problem:** Repeat bracketed substrings by their preceding positive integer; nesting is allowed.

**Approach:** Use stacks for prior strings and repeat counts, building each nested segment on close bracket.

**Java solution:**

```java
static String decode(String s) { Deque<Integer> nums=new ArrayDeque<>(); Deque<StringBuilder> prev=new ArrayDeque<>(); StringBuilder cur=new StringBuilder(); int k=0; for(char c:s.toCharArray()) { if(Character.isDigit(c)) k=k*10+c-'0'; else if(c=='[') { nums.push(k); prev.push(cur); cur=new StringBuilder(); k=0; } else if(c==']') { String part=cur.toString(); cur=prev.pop(); cur.append(part.repeat(nums.pop())); } else cur.append(c); } return cur.toString(); }
```

**Complexity:** O(output length) time and space

### Q5. Generate all valid combinations of n pairs of parentheses

**Problem:** Return every balanced string using exactly n opening and n closing parentheses.

**Approach:** Backtrack while opening count is below n and closing count is below opening count.

**Java solution:**

```java
static List<String> parentheses(int n) { List<String> out=new ArrayList<>(); buildPar(out,new StringBuilder(),0,0,n); return out; }
static void buildPar(List<String> o,StringBuilder s,int open,int close,int n) { if(s.length()==2*n) { o.add(s.toString()); return; } if(open<n) { s.append('('); buildPar(o,s,open+1,close,n); s.deleteCharAt(s.length()-1); } if(close<open) { s.append(')'); buildPar(o,s,open,close+1,n); s.deleteCharAt(s.length()-1); } }
```

**Complexity:** O(Catalan(n)·n) time and output space

### Q6. Return all subsets of an array of distinct integers

**Problem:** Return the power set; output order does not matter.

**Approach:** For each input, duplicate every subset built so far with that value appended.

**Java solution:**

```java
static List<List<Integer>> subsets(int[] a) { List<List<Integer>> out=new ArrayList<>(); out.add(new ArrayList<>()); for(int x:a) { int n=out.size(); for(int i=0;i<n;i++) { List<Integer> t=new ArrayList<>(out.get(i)); t.add(x); out.add(t); } } return out; }
```

**Complexity:** O(n·2^n) output time and space

### Q7. Return all permutations of distinct integers

**Problem:** Each input value appears exactly once in each output ordering.

**Approach:** Backtrack with a used array and restore state after each recursive choice.

**Java solution:**

```java
static List<List<Integer>> permutations(int[] a) { List<List<Integer>> out=new ArrayList<>(); permute(a,new boolean[a.length],new ArrayList<>(),out); return out; }
static void permute(int[] a,boolean[] used,List<Integer> path,List<List<Integer>> out) { if(path.size()==a.length) { out.add(new ArrayList<>(path)); return; } for(int i=0;i<a.length;i++) if(!used[i]) { used[i]=true; path.add(a[i]); permute(a,used,path,out); path.remove(path.size()-1); used[i]=false; } }
```

**Complexity:** O(n·n!) time and output space

### Q8. Find unique combinations that sum to target

**Problem:** Candidates are positive and distinct; a candidate may be reused any number of times.

**Approach:** Sort candidates and backtrack from the same index when reusing a candidate.

**Java solution:**

```java
static List<List<Integer>> combinationSum(int[] a,int target) { Arrays.sort(a); List<List<Integer>> out=new ArrayList<>(); comb(a,target,0,new ArrayList<>(),out); return out; }
static void comb(int[] a,int left,int start,List<Integer> p,List<List<Integer>> o) { if(left==0) { o.add(new ArrayList<>(p)); return; } for(int i=start;i<a.length&&a[i]<=left;i++) { p.add(a[i]); comb(a,left-a[i],i,p,o); p.remove(p.size()-1); } }
```

**Complexity:** Output-sensitive; worst case exponential in target

### Q9. Find a word in a character grid

**Problem:** Move horizontally or vertically, do not reuse a cell during one path, and match the whole word.

**Approach:** Start DFS at matching cells, mark the current cell temporarily, and restore it after recursion.

**Java solution:**

```java
static boolean wordExists(char[][] b,String w) { for(int r=0;r<b.length;r++) for(int c=0;c<b[0].length;c++) if(search(b,w,r,c,0)) return true; return false; }
static boolean search(char[][] b,String w,int r,int c,int i) { if(i==w.length()) return true; if(r<0||c<0||r==b.length||c==b[0].length||b[r][c]!=w.charAt(i)) return false; char x=b[r][c]; b[r][c]='#'; boolean ok=search(b,w,r+1,c,i+1)||search(b,w,r-1,c,i+1)||search(b,w,r,c+1,i+1)||search(b,w,r,c-1,i+1); b[r][c]=x; return ok; }
```

**Complexity:** O(RC·4^L) worst case, O(L) recursion

### Q10. Determine whether you can reach the final index

**Problem:** Each value is the maximum forward jump from that index; return false for an empty array.

**Approach:** Scan backward, moving the required-good index whenever the current jump can reach it.

**Java solution:**

```java
static boolean canJump(int[] a) { int goal=a.length-1; for(int i=a.length-1;i>=0;i--) if(i+a[i]>=goal) goal=i; return goal==0; }
```

**Complexity:** O(n) time, O(1) space

## Backtracking and Greedy Choices

### Q11. Find a starting gas station for a circular route

**Problem:** Stations have gas[i] and cost[i]; return a feasible start or -1. Assume at most one feasible start.

**Approach:** If total gas is below total cost, fail; otherwise reset the candidate when running fuel becomes negative.

**Java solution:**

```java
static int gasStart(int[] gas,int[] cost) { int total=0,tank=0,start=0; for(int i=0;i<gas.length;i++) { int d=gas[i]-cost[i]; total+=d; tank+=d; if(tank<0) { start=i+1; tank=0; } } return total<0?-1:start; }
```

**Complexity:** O(n) time, O(1) space

### Q12. Rob houses in a row without taking adjacent houses

**Problem:** Return the largest sum; values are nonnegative and the street is linear.

**Approach:** At each house, choose between the prior best and current value plus the best two positions back.

**Java solution:**

```java
static int rob(int[] a) { int prev2=0,prev1=0; for(int x:a) { int cur=Math.max(prev1,prev2+x); prev2=prev1; prev1=cur; } return prev1; }
```

**Complexity:** O(n) time, O(1) space

### Q13. Find the minimum path sum in a grid

**Problem:** Move only right or down from the top-left to bottom-right; cells may have nonnegative costs.

**Approach:** Use one row of dynamic programming values, updating each cell from above or left.

**Java solution:**

```java
static int minPathSum(int[][] g) { int m=g.length,n=g[0].length; int[] dp=new int[n]; Arrays.fill(dp,Integer.MAX_VALUE); dp[0]=0; for(int[] row:g) for(int c=0;c<n;c++) dp[c]=row[c]+(c==0?dp[c]:Math.min(dp[c],dp[c-1])); return dp[n-1]; }
```

**Complexity:** O(mn) time, O(n) space

### Q14. Count paths through a grid with blocked cells

**Problem:** Move right or down; a cell marked 1 is blocked and 0 is open.

**Approach:** Use one-dimensional path counts, zeroing the count at blocked cells.

**Java solution:**

```java
static long uniquePaths(int[][] g) { int n=g[0].length; long[] d=new long[n]; d[0]=g[0][0]==0?1:0; for(int[] row:g) for(int c=0;c<n;c++) { if(row[c]==1) d[c]=0; else if(c>0) d[c]+=d[c-1]; } return d[n-1]; }
```

**Complexity:** O(mn) time, O(n) space; use BigInteger if counts exceed long

### Q15. Find the k points closest to the origin

**Problem:** Return any k points from a 2D integer array; squared-distance ties can be returned in either order.

**Approach:** Use a max-heap of size k so each point costs log k; compare long distances safely.

**Java solution:**

```java
static int[][] kClosest(int[][] p,int k) { PriorityQueue<int[]> q=new PriorityQueue<>((a,b)->Long.compare(dist(b),dist(a))); for(int[] x:p) { q.offer(x); if(q.size()>k) q.poll(); } int[][] out=new int[k][]; for(int i=0;i<k;i++) out[i]=q.poll(); return out; }
static long dist(int[] p) { return (long)p[0]*p[0]+(long)p[1]*p[1]; }
```

**Complexity:** O(n log k) time, O(k) space

### Q16. Find the minimum number of meeting rooms

**Problem:** Given non-empty meeting intervals [start,end), return the minimum number of rooms needed. An interval ending at time t releases its room for another interval starting at t.

**Approach:** Sort starts and ends separately, then count starts that occur before the earliest active meeting ends.

**Java solution:**

```java
static int minRooms(int[][] a) { int n=a.length;int[] s=new int[n],e=new int[n];for(int i=0;i<n;i++){s[i]=a[i][0];e[i]=a[i][1];}Arrays.sort(s);Arrays.sort(e);int active=0,max=0,j=0;for(int x:s){while(j<n&&e[j]<=x){active--;j++;}active++;max=Math.max(max,active);}return max; }
```

**Complexity:** O(n log n) time, O(n) space

### Q17. Find the smallest range containing a value from every sorted list

**Problem:** Given k non-empty sorted integer lists, return the smallest inclusive range containing at least one value from each list.

**Approach:** Keep one current value per list in a min-heap and track the maximum; advance the list that contributes the current minimum.

**Java solution:**

```java
static int[] smallestRange(List<List<Integer>> a) { PriorityQueue<int[]> q=new PriorityQueue<>(Comparator.comparingInt(x->x[0]));int max=Integer.MIN_VALUE;for(int i=0;i<a.size();i++){int v=a.get(i).get(0);q.add(new int[]{v,i,0});max=Math.max(max,v);}int bestL=q.peek()[0],bestR=max;while(q.size()==a.size()){int[]x=q.poll();if((long)max-x[0]<(long)bestR-bestL){bestL=x[0];bestR=max;}int j=x[2]+1;if(j==a.get(x[1]).size())break;int v=a.get(x[1]).get(j);q.add(new int[]{v,x[1],j});max=Math.max(max,v);}return new int[]{bestL,bestR};}
```

**Complexity:** O(N log k) time, O(k) space for k lists and N total values

### Q18. Build a time-based key-value store

**Problem:** set(key,value,time) stores values with strictly increasing timestamps per key; get returns the value at the greatest timestamp <= query time.

**Approach:** Keep a TreeMap per key and use floorEntry for the query timestamp.

**Java solution:**

```java
class TimeMap { Map<String,NavigableMap<Integer,String>> m=new HashMap<>(); void set(String k,String v,int t) { m.computeIfAbsent(k,x->new TreeMap<>()).put(t,v); } String get(String k,int t) { var x=m.get(k); var e=x==null?null:x.floorEntry(t); return e==null?"":e.getValue(); } }
```

**Complexity:** set/get O(log v) per key, O(total values) space

### Q19. Implement a small integer hash map

**Problem:** Support put, get, and remove for integer keys and values; keys may be negative.

**Approach:** Use a fixed bucket array with separate chaining and normalize the bucket index with floorMod.

**Java solution:**

```java
class MyMap { List<int[]>[] b=new List[769]; int slot(int k) { return Math.floorMod(k,b.length); } void put(int k,int v) { int i=slot(k); if(b[i]==null)b[i]=new ArrayList<>(); for(int[] p:b[i])if(p[0]==k){p[1]=v;return;} b[i].add(new int[]{k,v}); } int get(int k) { if(b[slot(k)]!=null)for(int[] p:b[slot(k)])if(p[0]==k)return p[1]; return -1; } void remove(int k) { if(b[slot(k)]!=null)b[slot(k)].removeIf(p->p[0]==k); } }
```

**Complexity:** Expected O(1) per operation with a well-distributed hash; O(n) worst case

### Q20. Copy a linked list with random pointers

**Problem:** Each node has next and random references; return a deep copy with all links preserved.

**Approach:** Map each original node to a copy, then wire next and random pointers in a second pass.

**Java solution:**

```java
static RandomNode copyRandom(RandomNode h) { Map<RandomNode,RandomNode> m=new IdentityHashMap<>(); for(RandomNode p=h;p!=null;p=p.next) m.put(p,new RandomNode(p.val)); for(RandomNode p=h;p!=null;p=p.next) { m.get(p).next=m.get(p.next); m.get(p).random=m.get(p.random); } return m.get(h); }
```

**Complexity:** O(n) time, O(n) space

## Data Structures and Linked Lists

### Q21. Add two numbers stored in reverse-order linked lists

**Problem:** Each node stores one digit; return their sum in the same reverse-order format.

**Approach:** Walk both lists while carrying a digit; continue while either list or carry remains.

**Java solution:**

```java
static ListNode addLists(ListNode a,ListNode b) { ListNode d=new ListNode(0),p=d; int carry=0; while(a!=null||b!=null||carry>0) { int s=carry+(a==null?0:a.val)+(b==null?0:b.val); p.next=new ListNode(s%10); p=p.next; carry=s/10; if(a!=null)a=a.next; if(b!=null)b=b.next; } return d.next; }
```

**Complexity:** O(max(n,m)) time, O(max(n,m)) output space

### Q22. Compare dotted version strings

**Problem:** Compare nonnegative integer components separated by dots; missing components count as zero.

**Approach:** Parse components one at a time and compare without converting potentially long components to int.

**Java solution:**

```java
static int compareVersion(String a,String b) { String[] x=a.split("\\."),y=b.split("\\."); for(int i=0;i<Math.max(x.length,y.length);i++) { String p=i<x.length?x[i]:"0",q=i<y.length?y[i]:"0"; p=p.replaceFirst("^0+(?!$)",""); q=q.replaceFirst("^0+(?!$)",""); if(p.length()!=q.length()) return Integer.compare(p.length(),q.length()); int c=p.compareTo(q); if(c!=0)return Integer.signum(c); } return 0; }
```

**Complexity:** O(total digits) time, O(total digits) split storage

### Q23. Evaluate an expression with +, -, *, and / operators

**Problem:** Input contains nonnegative integers and operators with standard precedence; division truncates toward zero.

**Approach:** Accumulate the current number and defer addition/subtraction terms while applying multiplication/division immediately.

**Java solution:**

```java
static int calculate(String s) { Deque<Integer> st=new ArrayDeque<>(); int n=0; char op='+'; for(int i=0;i<=s.length();i++) { char c=i==s.length()?'+':s.charAt(i); if(Character.isDigit(c)) n=n*10+c-'0'; if((!Character.isDigit(c)&&c!=' ')||i==s.length()) { if(op=='+')st.push(n); else if(op=='-')st.push(-n); else if(op=='*')st.push(st.pop()*n); else if(op=='/')st.push(st.pop()/n); op=c; n=0; } } int sum=0; for(int x:st)sum+=x; return sum; }
```

**Complexity:** O(n) time, O(n) space

### Q24. Search a row-major sorted matrix

**Problem:** Rows are sorted and each row’s first value exceeds the previous row’s last; return whether target exists.

**Approach:** Treat the matrix as one virtual sorted array and binary-search its indices.

**Java solution:**

```java
static boolean searchMatrix(int[][] a,int t) { if(a.length==0||a[0].length==0)return false; int n=a.length*a[0].length,l=0,r=n-1; while(l<=r) { int m=l+(r-l)/2,v=a[m/a[0].length][m%a[0].length]; if(v==t)return true; if(v<t)l=m+1; else r=m-1; } return false; }
```

**Complexity:** O(log(mn)) time, O(1) space

### Q25. Find a peak element

**Problem:** A peak is greater than its existing neighbors; return any peak index. Assume adjacent values differ.

**Approach:** Binary-search the slope: if the right neighbor is larger, a peak exists to the right.

**Java solution:**

```java
static int peak(int[] a) { int l=0,r=a.length-1; while(l<r) { int m=l+(r-l)/2; if(a[m]<a[m+1])l=m+1; else r=m; } return l; }
```

**Complexity:** O(log n) time, O(1) space

### Q26. Return binary-tree nodes in zigzag level order

**Problem:** Return values level by level, reversing the direction on every other level.

**Approach:** Use BFS and write each level into a fixed-size list at normal or reversed indices.

**Java solution:**

```java
static List<List<Integer>> zigzag(TreeNode root) { List<List<Integer>> out=new ArrayList<>(); if(root==null)return out; Queue<TreeNode> q=new ArrayDeque<>(); q.add(root); boolean rev=false; while(!q.isEmpty()) { int n=q.size(); List<Integer> row=new ArrayList<>(Collections.nCopies(n,0)); for(int i=0;i<n;i++) { TreeNode x=q.poll(); row.set(rev?n-1-i:i,x.val); if(x.left!=null)q.add(x.left); if(x.right!=null)q.add(x.right); } out.add(row); rev=!rev; } return out; }
```

**Complexity:** O(n) time, O(width) auxiliary space plus output

### Q27. Return the right-side view of a binary tree

**Problem:** Return the last visible node at each depth when viewing the tree from the right.

**Approach:** BFS one level at a time and record its last node.

**Java solution:**

```java
static List<Integer> rightView(TreeNode root) { List<Integer> out=new ArrayList<>(); if(root==null)return out; Queue<TreeNode> q=new ArrayDeque<>(); q.add(root); while(!q.isEmpty()) { int n=q.size(); for(int i=0;i<n;i++) { TreeNode x=q.poll(); if(i==n-1)out.add(x.val); if(x.left!=null)q.add(x.left); if(x.right!=null)q.add(x.right); } } return out; }
```

**Complexity:** O(n) time, O(width) auxiliary space

### Q28. Compute the diameter of a binary tree

**Problem:** Return the number of edges on the longest path between any two nodes.

**Approach:** A postorder depth function updates a shared best diameter using left depth plus right depth.

**Java solution:**

```java
static int diameter(TreeNode r) { int[] best={0}; depth(r,best); return best[0]; }
static int depth(TreeNode n,int[] best) { if(n==null)return 0; int l=depth(n.left,best),r=depth(n.right,best); best[0]=Math.max(best[0],l+r); return 1+Math.max(l,r); }
```

**Complexity:** O(n) time, O(h) recursion

### Q29. Check whether a root-to-leaf path sums to target

**Problem:** Values may be positive, zero, or negative; only leaf-ending paths count.

**Approach:** Subtract each visited value and succeed only at a leaf with zero remainder.

**Java solution:**

```java
static boolean hasPathSum(TreeNode r,int target) { if(r==null)return false; if(r.left==null&&r.right==null)return target==r.val; return hasPathSum(r.left,target-r.val)||hasPathSum(r.right,target-r.val); }
```

**Complexity:** O(n) time, O(h) recursion

### Q30. Count connected components in an undirected graph

**Problem:** Vertices are numbered 0 through n-1; edges may be listed in any order.

**Approach:** Use union-find and count each successful union as one fewer component.

**Java solution:**

```java
static int components(int n,int[][] edges) { int[] p=new int[n],sz=new int[n]; for(int i=0;i<n;i++){p[i]=i;sz[i]=1;} int c=n; for(int[] e:edges) if(union(p,sz,e[0],e[1]))c--; return c; }
static int find(int[] p,int x) { if(p[x]!=x)p[x]=find(p,p[x]); return p[x]; }
static boolean union(int[] p,int[] s,int a,int b) { a=find(p,a); b=find(p,b); if(a==b)return false; if(s[a]<s[b]){int t=a;a=b;b=t;} p[b]=a;s[a]+=s[b];return true; }
```

**Complexity:** O((n+e)·α(n)) time, O(n) space

## Dynamic Programming, Graphs, and Matrices

### Q31. Determine whether a string can be segmented into dictionary words

**Problem:** Words may be reused; return whether the full string can be split into dictionary entries.

**Approach:** Let dp[i] mean the prefix ending at i is segmentable; test dictionary words ending there.

**Java solution:**

```java
static boolean wordBreak(String s,Set<String> dict) { boolean[] d=new boolean[s.length()+1]; d[0]=true; for(int i=1;i<=s.length();i++) for(int j=0;j<i;j++) if(d[j]&&dict.contains(s.substring(j,i))){d[i]=true;break;} return d[s.length()]; }
```

**Complexity:** O(n^2) substring checks, O(n) DP space

### Q32. Count ways to decode digits into letters 1 through 26

**Problem:** Given a string of digits where '1' through '26' map to letters, count the possible decodings. A '0' can only be decoded as part of '10' or '20'. Return 0 for an empty string.

**Approach:** At each position, add the one-digit way if nonzero and the two-digit way if in 10..26.

**Java solution:**

```java
static int numDecodings(String s) { if(s.isEmpty()||s.charAt(0)=='0')return 0;int prev2=1,prev1=1;for(int i=1;i<s.length();i++){int cur=0;if(s.charAt(i)!='0')cur+=prev1;int v=(s.charAt(i-1)-'0')*10+s.charAt(i)-'0';if(v>=10&&v<=26)cur+=prev2;prev2=prev1;prev1=cur;}return prev1; }
```

**Complexity:** O(n) time, O(1) space; use long or BigInteger for large counts

### Q33. Find the maximum product of a contiguous subarray

**Problem:** Values may be negative and zero; return the largest product.

**Approach:** Track both maximum and minimum products ending at each position because a negative can swap them.

**Java solution:**

```java
static int maxProduct(int[] a) { int hi=a[0],lo=a[0],best=a[0]; for(int i=1;i<a.length;i++) { int x=a[i]; if(x<0){int t=hi;hi=lo;lo=t;} hi=Math.max(x,hi*x); lo=Math.min(x,lo*x); best=Math.max(best,hi); } return best; }
```

**Complexity:** O(n) time, O(1) space; products may overflow int

### Q34. Partition a string into labels appearing in only one partition

**Problem:** Every character must occur in only one output part; return partition lengths.

**Approach:** Record each character’s last index, then close a partition when the scan reaches the farthest last index seen.

**Java solution:**

```java
static List<Integer> partitionLabels(String s) { int[] last=new int[26]; for(int i=0;i<s.length();i++)last[s.charAt(i)-'a']=i; List<Integer> out=new ArrayList<>(); int start=0,end=0; for(int i=0;i<s.length();i++){end=Math.max(end,last[s.charAt(i)-'a']);if(i==end){out.add(end-start+1);start=i+1;}} return out; }
```

**Complexity:** O(n) time, O(1) space for lowercase alphabet

### Q35. Find the minimum CPU intervals for tasks with cooldown n

**Problem:** Each task is one uppercase letter; identical tasks need at least n intervals between executions.

**Approach:** Use the maximum task frequency to calculate idle slots, then compare with the number of tasks.

**Java solution:**

```java
static int leastIntervals(char[] a,int n) { int[] f=new int[26]; for(char c:a)f[c-'A']++; Arrays.sort(f); int max=f[25]-1,idle=max*n; for(int i=24;i>=0&&f[i]>0;i--)idle-=Math.min(max,f[i]); return a.length+Math.max(0,idle); }
```

**Complexity:** O(tasks + alphabet log alphabet) time, O(1) space

### Q36. Find all anagram start indices

**Problem:** Find every start in s whose length-p substring is an anagram of lowercase p.

**Approach:** Slide a fixed-size count window and compare with the pattern frequency array.

**Java solution:**

```java
static List<Integer> findAnagrams(String s,String p) { List<Integer> o=new ArrayList<>(); if(p.length()>s.length())return o; int[] a=new int[26],b=new int[26]; for(int i=0;i<p.length();i++){a[p.charAt(i)-'a']++;b[s.charAt(i)-'a']++;} if(Arrays.equals(a,b))o.add(0); for(int i=p.length();i<s.length();i++){b[s.charAt(i)-'a']++;b[s.charAt(i-p.length())-'a']--;if(Arrays.equals(a,b))o.add(i-p.length()+1);} return o; }
```

**Complexity:** O(26n) time, O(1) space for fixed alphabet

### Q37. Remove the minimum parentheses to make a string valid

**Problem:** Return one valid result; letters must remain in original order.

**Approach:** Two passes remove extra closing brackets, then reverse and remove extra opening brackets.

**Java solution:**

```java
static String minRemove(String s) { String a=removeSide(s,'(',')'); return new StringBuilder(removeSide(new StringBuilder(a).reverse().toString(),')','(')).reverse().toString(); }
static String removeSide(String s,char open,char close) { StringBuilder o=new StringBuilder(); int bal=0; for(char c:s.toCharArray()) { if(c==open)bal++; if(c==close){if(bal==0)continue;bal--;} o.append(c); } return o.toString(); }
```

**Complexity:** O(n) time, O(n) space

### Q38. Validate an IPv4 or IPv6 address string

**Problem:** Return whether a string is a valid IPv4 or IPv6 address. For IPv6, require the full eight-group form (no '::' compression); use ASCII hexadecimal digits.

**Approach:** Check separators, group counts, allowed characters, and group-specific length/range rules.

**Java solution:**

```java
static String validIp(String s) { if(s.contains(".")) { String[] p=s.split("\\.",-1);if(p.length!=4)return "Neither";for(String x:p)if(x.isEmpty()||x.length()>3||x.length()>1&&x.charAt(0)=='0'||!x.matches("[0-9]+")||Integer.parseInt(x)>255)return "Neither";return "IPv4";}if(s.contains(":")){String[]p=s.split(":",-1);if(p.length!=8)return "Neither";for(String x:p)if(x.isEmpty()||x.length()>4||!x.matches("[0-9a-fA-F]+"))return "Neither";return "IPv6";}return "Neither"; }
```

**Complexity:** O(n) time, O(n) split storage

### Q39. Find the longest substring where one character can be replaced at most k times

**Problem:** Return the maximum window length; the input is uppercase English letters.

**Approach:** Maintain a frequency window and the highest count seen; shrink while window size minus max frequency exceeds k.

**Java solution:**

```java
static int characterReplacement(String s,int k) { int[] f=new int[26]; int l=0,max=0,best=0; for(int r=0;r<s.length();r++){max=Math.max(max,++f[s.charAt(r)-'A']);while(r-l+1-max>k)f[s.charAt(l++)-'A']--;best=Math.max(best,r-l+1);} return best; }
```

**Complexity:** O(n) time, O(1) space for fixed alphabet

### Q40. Rotate a square matrix 90 degrees clockwise in place

**Problem:** Transform an n-by-n matrix without allocating another matrix.

**Approach:** Transpose across the diagonal, then reverse each row.

**Java solution:**

```java
static void rotate(int[][] a) { int n=a.length; for(int r=0;r<n;r++) for(int c=r+1;c<n;c++) { int t=a[r][c]; a[r][c]=a[c][r]; a[c][r]=t; } for(int[] row:a) for(int l=0,r=n-1;l<r;l++,r--) { int t=row[l]; row[l]=row[r]; row[r]=t; } }
```

**Complexity:** O(n²) time, O(1) extra space

---

[Module guide](README.md) · [Repository home](../README.md)