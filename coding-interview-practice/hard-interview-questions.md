# Hard Java Coding Interview Questions

Forty hard-level Java coding problems for interview practice, from mid-level screening rounds to advanced product-company onsite discussions. Each answer gives the core idea, a Java solution, and time/space complexity.

> These are representative practice problems, not claims about verbatim questions asked by a particular company. Confirm constraints, explain edge cases, and discuss trade-offs before presenting code.

The snippets target Java 17 or later and are independent examples. Add them one at a time to a utility class; shared `ListNode`, `RandomNode`, and `TreeNode` definitions are in the [module guide](README.md).

## Advanced Strings and Dynamic Programming

### Q1. Median of two sorted arrays

**Problem:** Return the median of two sorted arrays in O(log(min(m,n))) time.

**Approach:** Binary-search a partition of the shorter array so left values do not exceed right values.

**Java solution:**

```java
static double median(int[] a,int[] b) { if(a.length>b.length)return median(b,a); int m=a.length,n=b.length,l=0,r=m; while(l<=r) { int i=(l+r)/2,j=(m+n+1)/2-i; int al=i==0?Integer.MIN_VALUE:a[i-1],ar=i==m?Integer.MAX_VALUE:a[i],bl=j==0?Integer.MIN_VALUE:b[j-1],br=j==n?Integer.MAX_VALUE:b[j]; if(al<=br&&bl<=ar)return (m+n)%2==1?Math.max(al,bl):((long)Math.max(al,bl)+Math.min(ar,br))/2.0; if(al>br)r=i-1;else l=i+1; } throw new IllegalArgumentException(); }
```

**Complexity:** O(log min(m,n)) time, O(1) space

### Q2. Find the minimum window containing every character of a pattern

**Problem:** Return the shortest substring of s containing the pattern’s characters with multiplicity, or empty if none exists.

**Approach:** Expand a sliding window until it covers the required counts, then shrink while it remains valid.

**Java solution:**

```java
static String minWindow(String s,String t) { if(t.isEmpty())return ""; Map<Character,Integer> need=new HashMap<>(); for(char c:t.toCharArray())need.merge(c,1,Integer::sum); int missing=t.length(),left=0,bestL=0,best=Integer.MAX_VALUE; for(int r=0;r<s.length();r++){char c=s.charAt(r); if(need.getOrDefault(c,0)>0)missing--; need.put(c,need.getOrDefault(c,0)-1); while(missing==0){if(r-left+1<best){best=r-left+1;bestL=left;} char x=s.charAt(left++); need.put(x,need.getOrDefault(x,0)+1); if(need.get(x)>0)missing++;}} return best==Integer.MAX_VALUE?"":s.substring(bestL,bestL+best); }
```

**Complexity:** O(|s|+|t|) expected time, O(distinct chars) space

### Q3. Find the largest rectangle in a histogram

**Problem:** Bars have nonnegative heights; return maximum rectangle area.

**Approach:** Maintain increasing bar indices and when a shorter bar arrives, compute areas for popped bars.

**Java solution:**

```java
static int largestRectangle(int[] h) { Deque<Integer> st=new ArrayDeque<>(); int best=0; for(int i=0;i<=h.length;i++){int cur=i==h.length?0:h[i]; while(!st.isEmpty()&&h[st.peek()]>=cur){int height=h[st.pop()];int left=st.isEmpty()?0:st.peek()+1;best=Math.max(best,height*(i-left));} st.push(i);} return best; }
```

**Complexity:** O(n) time, O(n) space; use long for large areas

### Q4. Find the largest rectangle containing only ones in a binary matrix

**Problem:** Return the maximum area of a rectangle of 1s.

**Approach:** Treat each row as a histogram of consecutive ones above it and reuse the histogram algorithm.

**Java solution:**

```java
static int maximalRectangle(char[][] a) { if(a.length==0)return 0; int[] h=new int[a[0].length]; int best=0; for(char[] row:a){for(int c=0;c<h.length;c++)h[c]=row[c]=='1'?h[c]+1:0;best=Math.max(best,largestRectangle(h));} return best; }
```

**Complexity:** O(rows·cols) time, O(cols) space

### Q5. Match a string against a pattern with . and *

**Problem:** The dot matches one character and star repeats the preceding pattern item zero or more times.

**Approach:** Use DP over text and pattern prefixes; for star, choose zero repetitions or consume one matching character.

**Java solution:**

```java
static boolean regexMatch(String s,String p) { boolean[][] d=new boolean[s.length()+1][p.length()+1]; d[0][0]=true; for(int j=2;j<=p.length();j++)if(p.charAt(j-1)=='*')d[0][j]=d[0][j-2]; for(int i=1;i<=s.length();i++)for(int j=1;j<=p.length();j++){char x=p.charAt(j-1);if(x=='*'){d[i][j]=d[i][j-2];char q=p.charAt(j-2);if(q=='.'||q==s.charAt(i-1))d[i][j]|=d[i-1][j];}else if(x=='.'||x==s.charAt(i-1))d[i][j]=d[i-1][j-1];}return d[s.length()][p.length()]; }
```

**Complexity:** O(mn) time, O(mn) space

### Q6. Match a string against a wildcard pattern

**Problem:** ? matches one character and * matches any sequence, including empty.

**Approach:** Use two pointers and remember the last star plus the text position it currently covers.

**Java solution:**

```java
static boolean wildcard(String s,String p) { int i=0,j=0,star=-1,mark=-1; while(i<s.length()){if(j<p.length()&&(p.charAt(j)=='?'||p.charAt(j)==s.charAt(i))){i++;j++;}else if(j<p.length()&&p.charAt(j)=='*'){star=j++;mark=i;}else if(star>=0){j=star+1;i=++mark;}else return false;}while(j<p.length()&&p.charAt(j)=='*')j++;return j==p.length(); }
```

**Complexity:** O(m+n) time, O(1) space

### Q7. Compute the minimum edit distance between two words

**Problem:** Allowed operations are insert, delete, and replace one character.

**Approach:** Use a rolling DP row; each cell takes the cheapest of insert, delete, and match/replace.

**Java solution:**

```java
static int editDistance(String a,String b) { int[] d=new int[b.length()+1]; for(int j=0;j<=b.length();j++)d[j]=j; for(int i=1;i<=a.length();i++){int prev=d[0];d[0]=i;for(int j=1;j<=b.length();j++){int old=d[j];d[j]=a.charAt(i-1)==b.charAt(j-1)?prev:1+Math.min(prev,Math.min(d[j],d[j-1]));prev=old;}}return d[b.length()]; }
```

**Complexity:** O(mn) time, O(n) space

### Q8. Count how many times target is a subsequence of source

**Problem:** Characters must match in order; different index choices count as different subsequences.

**Approach:** Update a one-dimensional DP from right to left for each source character.

**Java solution:**

```java
static long distinctSubseq(String s,String t) { long[] d=new long[t.length()+1];d[0]=1;for(char c:s.toCharArray())for(int j=t.length()-1;j>=0;j--)if(t.charAt(j)==c)d[j+1]+=d[j];return d[t.length()]; }
```

**Complexity:** O(mn) time, O(n) space; use BigInteger if counts can exceed long

### Q9. Maximize coins from bursting balloons

**Problem:** Burst every balloon; bursting i earns left·i·right from its current neighbors. Return max coins.

**Approach:** Choose the last balloon in each interval and use interval DP with virtual boundary balloons of value one.

**Java solution:**

```java
static int maxCoins(int[] nums) { int n=nums.length;int[] a=new int[n+2];a[0]=a[n+1]=1;System.arraycopy(nums,0,a,1,n);int[][] d=new int[n+2][n+2];for(int len=1;len<=n;len++)for(int l=1;l+len-1<=n;l++){int r=l+len-1;for(int k=l;k<=r;k++)d[l][r]=Math.max(d[l][r],d[l][k-1]+a[l-1]*a[k]*a[r+1]+d[k+1][r]);}return d[1][n]; }
```

**Complexity:** O(n³) time, O(n²) space; use long if products can overflow

### Q10. Maximize stock profit with at most k transactions

**Problem:** At most one share may be held; each buy must precede its sell.

**Approach:** Track best profit after each completed transaction and best balance while holding the next share.

**Java solution:**

```java
static int maxProfit(int k,int[] p) { if(k==0||p.length<2)return 0;if(k>=p.length/2){int x=0;for(int i=1;i<p.length;i++)x+=Math.max(0,p[i]-p[i-1]);return x;}int[] buy=new int[k+1],sell=new int[k+1];Arrays.fill(buy,Integer.MIN_VALUE/2);for(int x:p)for(int t=1;t<=k;t++){buy[t]=Math.max(buy[t],sell[t-1]-x);sell[t]=Math.max(sell[t],buy[t]+x);}return sell[k]; }
```

**Complexity:** O(nk) time, O(k) space

## Search and Backtracking

### Q11. Place n queens so none attack each other

**Problem:** Return all board layouts for an n-by-n board.

**Approach:** Backtrack by row and track occupied columns and both diagonals.

**Java solution:**

```java
static List<List<String>> nQueens(int n) { List<List<String>> out=new ArrayList<>(); queen(0,n,new char[n],new boolean[n],new boolean[2*n],new boolean[2*n],new ArrayList<>(),out);return out; }
static void queen(int r,int n,char[] pos,boolean[] c,boolean[] d1,boolean[] d2,List<String> path,List<List<String>> out){if(r==n){out.add(new ArrayList<>(path));return;}for(int x=0;x<n;x++){int a=r-x+n,b=r+x;if(c[x]||d1[a]||d2[b])continue;char[] row=new char[n];Arrays.fill(row,'.');row[x]='Q';path.add(new String(row));c[x]=d1[a]=d2[b]=true;queen(r+1,n,pos,c,d1,d2,path,out);c[x]=d1[a]=d2[b]=false;path.remove(path.size()-1);}}
```

**Complexity:** O(n!) search plus output construction

### Q12. Solve a 9-by-9 Sudoku in place

**Problem:** Empty cells use a dot; digits 1 through 9 must appear once per row, column, and 3-by-3 box.

**Approach:** Backtrack through empty cells, trying digits that are absent from the row, column, and box.

**Java solution:**

```java
static boolean solveSudoku(char[][] b) { for(int r=0;r<9;r++)for(int c=0;c<9;c++)if(b[r][c]=='.'){for(char d='1';d<='9';d++)if(valid(b,r,c,d)){b[r][c]=d;if(solveSudoku(b))return true;b[r][c]='.';}return false;}return true;}
static boolean valid(char[][] b,int r,int c,char d){for(int i=0;i<9;i++)if(b[r][i]==d||b[i][c]==d||b[3*(r/3)+i/3][3*(c/3)+i%3]==d)return false;return true;}
```

**Complexity:** Exponential worst case; O(81) recursion space

### Q13. Find the shortest word transformation sequence length

**Problem:** Change one lowercase letter at a time; each intermediate word must be in the dictionary. Return zero if unreachable.

**Approach:** BFS the word graph by changing each character through 26 letters and removing visited words.

**Java solution:**

```java
static int ladderLength(String begin,String end,List<String> words) { Set<String> left=new HashSet<>(words);if(!left.contains(end))return 0;Queue<String> q=new ArrayDeque<>();q.add(begin);int steps=1;while(!q.isEmpty()){for(int z=q.size();z>0;z--){String w=q.poll();if(w.equals(end))return steps;char[] a=w.toCharArray();for(int i=0;i<a.length;i++){char old=a[i];for(char c='a';c<='z';c++)if(c!=old){a[i]=c;String x=new String(a);if(left.remove(x))q.add(x);}a[i]=old;}}steps++;}return 0;}
```

**Complexity:** O(N·L·26·L) with string creation, O(NL) space

### Q14. Derive an alien alphabet order from sorted words

**Problem:** Return one valid character ordering or empty if constraints are inconsistent.

**Approach:** Create graph edges from the first differing character in adjacent words, then topologically sort all observed characters.

**Java solution:**

```java
static String alienOrder(String[] w) { Map<Character,Set<Character>> g=new HashMap<>();Map<Character,Integer> in=new HashMap<>();for(String s:w)for(char c:s.toCharArray()){g.putIfAbsent(c,new HashSet<>());in.putIfAbsent(c,0);}for(int i=0;i+1<w.length;i++){String a=w[i],b=w[i+1];int j=0;while(j<a.length()&&j<b.length()&&a.charAt(j)==b.charAt(j))j++;if(j==b.length()&&a.length()>b.length())return "";if(j<a.length()&&j<b.length()&&g.get(a.charAt(j)).add(b.charAt(j)))in.put(b.charAt(j),in.get(b.charAt(j))+1);}Queue<Character> q=new ArrayDeque<>();in.forEach((c,d)->{if(d==0)q.add(c);});StringBuilder o=new StringBuilder();while(!q.isEmpty()){char c=q.poll();o.append(c);for(char x:g.get(c))if(in.merge(x,-1,Integer::sum)==0)q.add(x);}return o.length()==in.size()?o.toString():"";}
```

**Complexity:** O(total characters + distinct letters + edges), O(V+E) space

### Q15. Find the maximum path sum in a binary tree

**Problem:** A path may start and end at any nodes but must be continuous; values can be negative.

**Approach:** Postorder returns the best downward path and updates a global best using both positive child gains.

**Java solution:**

```java
static int maxPathSum(TreeNode r) { int[] best={Integer.MIN_VALUE}; gain(r,best); return best[0]; }
static int gain(TreeNode n,int[] b){if(n==null)return 0;int l=Math.max(0,gain(n.left,b)),r=Math.max(0,gain(n.right,b));b[0]=Math.max(b[0],n.val+l+r);return n.val+Math.max(l,r);}
```

**Complexity:** O(n) time, O(h) recursion; use long for wider sums

### Q16. Serialize and deserialize a binary tree

**Problem:** Create a string representation that preserves shape and values, including missing children.

**Approach:** Use preorder traversal with a null marker; recursively consume tokens in the same order.

**Java solution:**

```java
static String serialize(TreeNode r) { StringBuilder s=new StringBuilder(); write(r,s); return s.toString(); }
static void write(TreeNode n,StringBuilder s){if(n==null){s.append("#," );return;}s.append(n.val).append(',');write(n.left,s);write(n.right,s);}
static TreeNode deserialize(String data){Deque<String> q=new ArrayDeque<>(Arrays.asList(data.split(",")));return read(q);}
static TreeNode read(Deque<String> q){String x=q.remove();if(x.equals("#"))return null;TreeNode n=new TreeNode(Integer.parseInt(x));n.left=read(q);n.right=read(q);return n;}
```

**Complexity:** O(n) time and O(n) storage for serialized output and tokens

### Q17. Recover a BST with two swapped values

**Problem:** Exactly two node values were swapped; restore the BST without changing its shape.

**Approach:** Inorder traversal should be sorted; identify the first previous node and last current node at inversions, then swap them.

**Java solution:**

```java
static void recoverTree(TreeNode r) { TreeNode[] x=new TreeNode[3]; inorder(r,x); int t=x[1].val;x[1].val=x[2].val;x[2].val=t; }
static void inorder(TreeNode n,TreeNode[] x){if(n==null)return;inorder(n.left,x);if(x[0]!=null&&x[0].val>n.val){if(x[1]==null)x[1]=x[0];x[2]=n;}x[0]=n;inorder(n.right,x);}
```

**Complexity:** O(n) time, O(h) recursion

### Q18. Implement an LFU cache with least-recently-used tie breaking

**Problem:** Support get and put in O(1) average time; when full, evict the least frequently used, then least recently used key.

**Approach:** Map keys to nodes and frequencies to insertion-ordered key sets; track the minimum frequency.

**Java solution:**

```java
class LFUCache { int cap,min=0;Map<Integer,int[]> val=new HashMap<>();Map<Integer,LinkedHashSet<Integer>> freq=new HashMap<>();LFUCache(int c){cap=c;}int get(int k){if(!val.containsKey(k))return -1;touch(k);return val.get(k)[0];}void put(int k,int v){if(cap==0)return;if(val.containsKey(k)){val.get(k)[0]=v;touch(k);return;}if(val.size()==cap){int old=freq.get(min).iterator().next();freq.get(min).remove(old);val.remove(old);}val.put(k,new int[]{v,1});freq.computeIfAbsent(1,x->new LinkedHashSet<>()).add(k);min=1;}void touch(int k){int[] x=val.get(k);int f=x[1];freq.get(f).remove(k);if(freq.get(f).isEmpty()&&min==f)min++;x[1]++;freq.computeIfAbsent(f+1,z->new LinkedHashSet<>()).add(k);}}
```

**Complexity:** O(1) average get/put, O(capacity) space

### Q19. Design a data structure that returns the median of a stream

**Problem:** Support addNum and findMedian as values arrive.

**Approach:** Keep a max-heap for the lower half and min-heap for the upper half; rebalance their sizes.

**Java solution:**

```java
class MedianFinder { PriorityQueue<Integer> lo=new PriorityQueue<>(Comparator.reverseOrder()),hi=new PriorityQueue<>();void addNum(int x){lo.offer(x);hi.offer(lo.poll());if(hi.size()>lo.size())lo.offer(hi.poll());}double median(){return lo.size()>hi.size()?lo.peek():((long)lo.peek()+hi.peek())/2.0;} }
```

**Complexity:** add O(log n), median O(1), O(n) space

### Q20. Find network delay from a source in a weighted directed graph

**Problem:** Given a directed graph with nodes numbered 1 through n and positive edge weights, return the time for a signal from source src to reach every node, or -1 if some node is unreachable.

**Approach:** Run Dijkstra with a min-heap and ignore entries whose distance is stale.

**Java solution:**

```java
static int networkDelay(int[][] e,int n,int src) { List<int[]>[] g=new List[n+1];for(int i=1;i<=n;i++)g[i]=new ArrayList<>();for(int[] x:e)g[x[0]].add(new int[]{x[1],x[2]});int[] d=new int[n+1];Arrays.fill(d,Integer.MAX_VALUE);d[src]=0;PriorityQueue<int[]> q=new PriorityQueue<>(Comparator.comparingInt(x->x[1]));q.add(new int[]{src,0});while(!q.isEmpty()){int[] x=q.poll();if(x[1]!=d[x[0]])continue;for(int[] y:g[x[0]])if(d[y[0]]>x[1]+y[1]){d[y[0]]=x[1]+y[1];q.add(new int[]{y[0],d[y[0]]});}}int ans=0;for(int i=1;i<=n;i++){if(d[i]==Integer.MAX_VALUE)return -1;ans=Math.max(ans,d[i]);}return ans;}
```

**Complexity:** O((V+E) log V) time, O(V+E) space

## Trees, Graphs, and Data Structures

### Q21. Find the kth-smallest value in a row-and-column sorted matrix

**Problem:** Rows and columns are nondecreasing; return the kth value counting duplicates.

**Approach:** Push the first item of each row into a min-heap and advance within the row of each removed item.

**Java solution:**

```java
static int kthSmallest(int[][] a,int k) { int n=a.length;PriorityQueue<int[]> q=new PriorityQueue<>(Comparator.comparingInt(x->x[0]));for(int r=0;r<n;r++)q.add(new int[]{a[r][0],r,0});int v=0;while(k-->0){int[] x=q.poll();v=x[0];if(x[2]+1<n)q.add(new int[]{a[x[1]][x[2]+1],x[1],x[2]+1});}return v;}
```

**Complexity:** O((n+k) log n) time, O(n) space

### Q22. Count how many values to the right are smaller

**Problem:** For each index, return the number of later elements strictly smaller than its value.

**Approach:** Coordinate-compress values and scan right to left with a Fenwick tree of seen frequencies.

**Java solution:**

```java
static List<Integer> countSmaller(int[] a) { int[] s=a.clone();Arrays.sort(s);Map<Integer,Integer> rank=new HashMap<>();int z=0;for(int x:s)rank.putIfAbsent(x,++z);int[] bit=new int[z+1],out=new int[a.length];for(int i=a.length-1;i>=0;i--){int r=rank.get(a[i]);for(int k=r-1;k>0;k-=k&-k)out[i]+=bit[k];for(int k=r;k<bit.length;k+=k&-k)bit[k]++;}List<Integer> ans=new ArrayList<>();for(int x:out)ans.add(x);return ans;}
```

**Complexity:** O(n log n) time, O(n) space

### Q23. Count important reverse pairs

**Problem:** Count i<j where a[i] > 2*a[j]; values may be negative or large.

**Approach:** Merge-sort while counting cross-half pairs with long arithmetic.

**Java solution:**

```java
static long reversePairs(int[] a) { return sortCount(a,new int[a.length],0,a.length); }
static long sortCount(int[] a,int[] t,int l,int r){if(r-l<2)return 0;int m=(l+r)/2;long c=sortCount(a,t,l,m)+sortCount(a,t,m,r);for(int i=l,j=m;i<m;i++){while(j<r&&(long)a[i]>2L*a[j])j++;c+=j-m;}int i=l,j=m,k=l;while(i<m&&j<r)t[k++]=a[i]<=a[j]?a[i++]:a[j++];while(i<m)t[k++]=a[i++];while(j<r)t[k++]=a[j++];System.arraycopy(t,l,a,l,r-l);return c;}
```

**Complexity:** O(n log n) time, O(n) space

### Q24. Find the maximum sum of three non-overlapping windows

**Problem:** All windows have length k; return their starting indices lexicographically earliest among ties.

**Approach:** Precompute each window sum, then best-left and best-right indices; scan the middle window.

**Java solution:**

```java
static int[] maxThreeWindows(int[] a,int k) { int n=a.length-k+1;int[] w=new int[n],left=new int[n],right=new int[n];int sum=0;for(int i=0;i<a.length;i++){sum+=a[i];if(i>=k)sum-=a[i-k];if(i>=k-1)w[i-k+1]=sum;}left[0]=0;for(int i=1;i<n;i++)left[i]=w[i]>w[left[i-1]]?i:left[i-1];right[n-1]=n-1;for(int i=n-2;i>=0;i--)right[i]=w[i]>=w[right[i+1]]?i:right[i+1];int[] ans={0,k,2*k};for(int m=k;m<n-k;m++){int l=left[m-k],r=right[m+k];if(w[l]+w[m]+w[r]>w[ans[0]]+w[ans[1]]+w[ans[2]])ans=new int[]{l,m,r};}return ans;}
```

**Complexity:** O(n) time, O(n) space

### Q25. Minimize the largest subarray sum after splitting into m parts

**Problem:** Split a nonnegative array into exactly m nonempty contiguous parts and minimize the maximum part sum.

**Approach:** Binary-search the answer; greedily count parts needed under a candidate limit.

**Java solution:**

```java
static long splitArray(int[] a,int m) { long l=0,r=0;for(int x:a){l=Math.max(l,x);r+=x;}while(l<r){long mid=(l+r)/2;int parts=1;long s=0;for(int x:a)if(s+x>mid){parts++;s=x;}else s+=x;if(parts>m)l=mid+1;else r=mid;}return l;}
```

**Complexity:** O(n log(sum)) time, O(1) space

### Q26. Draw the skyline formed by rectangular buildings

**Problem:** Each building is [left,right,height]; return critical points where the outline changes.

**Approach:** Create start/end events and sweep x coordinates while maintaining active heights in a multiset.

**Java solution:**

```java
static List<int[]> skyline(int[][] b) { List<int[]> e=new ArrayList<>();for(int[] x:b){e.add(new int[]{x[0],-x[2]});e.add(new int[]{x[1],x[2]});}e.sort((a,c)->a[0]!=c[0]?Integer.compare(a[0],c[0]):Integer.compare(a[1],c[1]));TreeMap<Integer,Integer> h=new TreeMap<>();h.put(0,1);List<int[]> o=new ArrayList<>();int prev=0;for(int[] x:e){int v=x[1];if(v<0)h.merge(-v,1,Integer::sum);else h.computeIfPresent(v,(k,n)->n==1?null:n-1);int cur=h.lastKey();if(cur!=prev){o.add(new int[]{x[0],cur});prev=cur;}}return o;}
```

**Complexity:** O(n log n) time, O(n) space

### Q27. Find all word pairs whose concatenation is a palindrome

**Problem:** Return distinct index pairs (i,j), i != j, where words[i]+words[j] is a palindrome.

**Approach:** For each word split, test prefix/suffix palindromes and look up the reversed complementary piece.

**Java solution:**

```java
static List<List<Integer>> palindromePairs(String[] w) { Map<String,Integer> m=new HashMap<>();for(int i=0;i<w.length;i++)m.put(w[i],i);Set<String> seen=new HashSet<>();List<List<Integer>> o=new ArrayList<>();for(int i=0;i<w.length;i++)for(int k=0;k<=w[i].length();k++){String a=w[i].substring(0,k),b=w[i].substring(k);if(pal(a)){Integer j=m.get(new StringBuilder(b).reverse().toString());if(j!=null&&j!=i)addPair(o,seen,i,j);}if(k<w[i].length()&&pal(b)){Integer j=m.get(new StringBuilder(a).reverse().toString());if(j!=null&&j!=i)addPair(o,seen,j,i);}}return o;}
static boolean pal(String s){for(int l=0,r=s.length()-1;l<r;l++,r--)if(s.charAt(l)!=s.charAt(r))return false;return true;}
static void addPair(List<List<Integer>> o,Set<String>s,int i,int j){String k=i+":"+j;if(s.add(k))o.add(List.of(i,j));}
```

**Complexity:** O(total word length squared) worst case, O(total length) index space

### Q28. Trap rain water in a 2D elevation map

**Problem:** Water can escape from the boundary; return total trapped volume.

**Approach:** Expand inward from the lowest boundary cell using a min-heap and raise each neighbor to the boundary level.

**Java solution:**

```java
static int trapRainWater(int[][] h) { int m=h.length,n=h[0].length;boolean[][] v=new boolean[m][n];PriorityQueue<int[]> q=new PriorityQueue<>(Comparator.comparingInt(x->x[0]));for(int r=0;r<m;r++)for(int c:new int[]{0,n-1})if(!v[r][c]){v[r][c]=true;q.add(new int[]{h[r][c],r,c});}for(int c=0;c<n;c++)for(int r:new int[]{0,m-1})if(!v[r][c]){v[r][c]=true;q.add(new int[]{h[r][c],r,c});}int ans=0;int[] dr={1,-1,0,0},dc={0,0,1,-1};while(!q.isEmpty()){int[] x=q.poll();for(int z=0;z<4;z++){int r=x[1]+dr[z],c=x[2]+dc[z];if(r>=0&&r<m&&c>=0&&c<n&&!v[r][c]){v[r][c]=true;ans+=Math.max(0,x[0]-h[r][c]);q.add(new int[]{Math.max(x[0],h[r][c]),r,c});}}}return ans;}
```

**Complexity:** O(mn log(mn)) time, O(mn) space

### Q29. Find the maximum number of points on one line

**Problem:** Coordinates are integer pairs and duplicates count as points.

**Approach:** For each anchor, reduce each slope by gcd and count duplicate coordinates separately.

**Java solution:**

```java
static int maxPoints(int[][] p) { int best=0;for(int i=0;i<p.length;i++){Map<String,Integer> c=new HashMap<>();int same=1,local=0;for(int j=i+1;j<p.length;j++){long dx=(long)p[j][0]-p[i][0],dy=(long)p[j][1]-p[i][1];if(dx==0&&dy==0){same++;continue;}long g=gcdLong(Math.abs(dx),Math.abs(dy));dx/=g;dy/=g;if(dx<0){dx=-dx;dy=-dy;}String k=dy+"/"+dx;local=Math.max(local,c.merge(k,1,Integer::sum));}best=Math.max(best,local+same);}return best;}
static long gcdLong(long a,long b){while(b!=0){long t=a%b;a=b;b=t;}return a;}
```

**Complexity:** O(n²) expected time, O(n) space per anchor; widen coordinate differences if needed

### Q30. Evaluate an arithmetic expression with parentheses and unary signs

**Problem:** Support nonnegative integer literals, +, -, spaces, and nested parentheses.

**Approach:** Use a recursive descent parser with sign state; parse a parenthesized expression recursively.

**Java solution:**

```java
static int calculate(String s) { int[] i={0};return expr(s,i); }
static int expr(String s,int[] i){int sum=0,sign=1;while(i[0]<s.length()){char c=s.charAt(i[0]++);if(c==' ')continue;if(c=='+')sign=1;else if(c=='-')sign=-1;else if(c=='(')sum+=sign*expr(s,i);else if(c==')')return sum;else{int n=c-'0';while(i[0]<s.length()&&Character.isDigit(s.charAt(i[0])))n=n*10+s.charAt(i[0]++)-'0';sum+=sign*n;}}return sum;}
```

**Complexity:** O(n) time, O(n) recursion in nested input

## Optimization and Specialized Algorithms

### Q31. Determine whether one string is a scrambled version of another

**Problem:** A split can swap its two substrings recursively; lengths must match.

**Approach:** Memoize substring pairs and try every split with and without swapping; prune unequal character counts.

**Java solution:**

```java
static boolean isScramble(String a,String b) { return scramble(a,b,new HashMap<>()); }
static boolean scramble(String a,String b,Map<String,Boolean> memo){String k=a+"#"+b;if(memo.containsKey(k))return memo.get(k);if(a.equals(b))return true;if(a.length()!=b.length()||!sameChars(a,b))return memo.put(k,false).get(k);for(int i=1;i<a.length();i++)if(scramble(a.substring(0,i),b.substring(0,i),memo)&&scramble(a.substring(i),b.substring(i),memo)||scramble(a.substring(0,i),b.substring(b.length()-i),memo)&&scramble(a.substring(i),b.substring(0,b.length()-i),memo))return memo.put(k,true).get(k);memo.put(k,false);return false;}
static boolean sameChars(String a,String b){char[]x=a.toCharArray(),y=b.toCharArray();Arrays.sort(x);Arrays.sort(y);return Arrays.equals(x,y);}
```

**Complexity:** Exponential states bounded by O(n⁴) transitions with memoization; O(n³) memo storage

### Q32. Find the minimum moves needed to solve the egg-drop problem

**Problem:** With k eggs and n floors, find the fewest worst-case drops needed to identify the critical floor.

**Approach:** For each move count, track how many floors can be distinguished with e eggs using the recurrence cover[e]=cover[e]+cover[e-1]+1.

**Java solution:**

```java
static int eggDrop(int eggs,int floors) { long[] cover=new long[eggs+1];int moves=0;while(cover[eggs]<floors){moves++;for(int e=eggs;e>0;e--)cover[e]=Math.min(floors,cover[e]+cover[e-1]+1);}return moves; }
```

**Complexity:** O(eggs·answer) time, O(eggs) space

### Q33. Find the longest increasing path in a matrix

**Problem:** Move up, down, left, or right to a strictly larger value; return the maximum path length.

**Approach:** Start from local maxima and peel layers in reverse topological order, counting BFS rounds.

**Java solution:**

```java
static int longestIncreasingPath(int[][] a) { int m=a.length,n=a[0].length;int[][] d=new int[m][n];Queue<int[]>q=new ArrayDeque<>();int[]dr={1,-1,0,0},dc={0,0,1,-1};for(int r=0;r<m;r++)for(int c=0;c<n;c++){for(int z=0;z<4;z++){int x=r+dr[z],y=c+dc[z];if(x>=0&&x<m&&y>=0&&y<n&&a[x][y]>a[r][c])d[r][c]++;}if(d[r][c]==0)q.add(new int[]{r,c});}int len=0;while(!q.isEmpty()){len++;for(int k=q.size();k>0;k--){int[]x=q.poll();for(int z=0;z<4;z++){int r=x[0]+dr[z],c=x[1]+dc[z];if(r>=0&&r<m&&c>=0&&c<n&&a[r][c]<a[x[0]][x[1]]&&--d[r][c]==0)q.add(new int[]{r,c});}}}return len;}
```

**Complexity:** O(mn) time, O(mn) space

### Q34. Find the shortest path to collect every key in a grid

**Problem:** Given a rectangular grid with one start cell, walls, doors A-F, and at most six distinct keys a-f, return the fewest moves needed to collect every key. A door can be crossed only after collecting its matching key.

**Approach:** BFS over (row,column,key-mask) states; mark a state visited when enqueued. Assume at most six key types a through f.

**Java solution:**

```java
static int shortestAllKeys(String[] g) { int m=g.length,n=g[0].length(),sr=0,sc=0;Map<Character,Integer> bit=new HashMap<>();for(int r=0;r<m;r++)for(int c=0;c<n;c++){char x=g[r].charAt(c);if(x=='@'){sr=r;sc=c;}if(x>='a'&&x<='f')bit.computeIfAbsent(x,k->bit.size());}int goal=(1<<bit.size())-1;boolean[][][]v=new boolean[m][n][1<<bit.size()];Queue<int[]>q=new ArrayDeque<>();q.add(new int[]{sr,sc,0});v[sr][sc][0]=true;int steps=0;int[]dr={1,-1,0,0},dc={0,0,1,-1};while(!q.isEmpty()){for(int z=q.size();z>0;z--){int[]x=q.poll();if(x[2]==goal)return steps;for(int k=0;k<4;k++){int r=x[0]+dr[k],c=x[1]+dc[k];if(r<0||c<0||r==m||c==n)continue;char a=g[r].charAt(c);if(a=='#')continue;if(a>='A'&&a<='F'){Integer b=bit.get(Character.toLowerCase(a));if(b==null||(x[2]&(1<<b))==0)continue;}int mask=x[2];if(a>='a'&&a<='f')mask|=1<<bit.get(a);if(!v[r][c][mask]){v[r][c][mask]=true;q.add(new int[]{r,c,mask});}}}steps++;}return -1; }
```

**Complexity:** O(mn·2^k) time and space

### Q35. Schedule jobs for maximum profit with non-overlapping time ranges

**Problem:** Given jobs with start, end, and nonnegative profit, choose non-overlapping jobs to maximize total profit. Jobs ending at time t may be followed by jobs starting at t, and the answer fits in an int.

**Approach:** Sort by end time and use binary search plus DP to find the best compatible prefix.

**Java solution:**

```java
static int jobProfit(int[] s,int[] e,int[] p) { int n=s.length;int[][]a=new int[n][3];for(int i=0;i<n;i++)a[i]=new int[]{s[i],e[i],p[i]};Arrays.sort(a,Comparator.comparingInt(x->x[1]));int[]ends=new int[n],d=new int[n+1];for(int i=0;i<n;i++)ends[i]=a[i][1];for(int i=1;i<=n;i++){int lo=0,hi=i-1;while(lo<hi){int mid=(lo+hi)/2;if(ends[mid]<=a[i-1][0])lo=mid+1;else hi=mid;}int prev=lo;d[i]=Math.max(d[i-1],a[i-1][2]+d[prev]);}return d[n]; }
```

**Complexity:** O(n log n) time, O(n) space

### Q36. Count subarrays whose sum lies within [lower,upper]

**Problem:** Values may be negative; count all contiguous subarrays with sums in the inclusive range.

**Approach:** Build prefix sums and merge-sort them while counting valid cross-half prefix differences.

**Java solution:**

```java
static long countRangeSum(int[] a,long lo,long hi) { long[] p=new long[a.length+1];for(int i=0;i<a.length;i++)p[i+1]=p[i]+a[i];return range(p,new long[p.length],0,p.length,lo,hi); }
static long range(long[]p,long[]t,int l,int r,long lo,long hi){if(r-l<2)return 0;int m=(l+r)/2;long c=range(p,t,l,m,lo,hi)+range(p,t,m,r,lo,hi);int a=m,b=m,k=m;for(int i=l;i<m;i++){while(a<r&&p[a]-p[i]<lo)a++;while(b<r&&p[b]-p[i]<=hi)b++;c+=b-a;}int i=l,j=m,z=l;while(i<m&&j<r)t[z++]=p[i]<=p[j]?p[i++]:p[j++];while(i<m)t[z++]=p[i++];while(j<r)t[z++]=p[j++];System.arraycopy(t,l,p,l,r-l);return c;}
```

**Complexity:** O(n log n) time, O(n) space

### Q37. Find the maximum XOR of any two integers

**Problem:** Choose two array values whose bitwise XOR is largest; treat values as nonnegative 32-bit integers.

**Approach:** Greedily set result bits from high to low if a prefix set can support that bit.

**Java solution:**

```java
static int maxXor(int[] a) { int ans=0,mask=0;for(int b=31;b>=0;b--){mask|=1<<b;Set<Integer>s=new HashSet<>();for(int x:a)s.add(x&mask);int cand=ans|(1<<b);for(int x:s)if(s.contains(x^cand)){ans=cand;break;}}return ans; }
```

**Complexity:** O(32n) expected time, O(n) space

### Q38. Find the minimum refueling stops

**Problem:** A car starts with fuel, stations are [position,fuel], and the destination is fixed; return -1 if unreachable.

**Approach:** As you reach each station position, add its fuel to a max-heap; refuel from the largest reachable station when needed.

**Java solution:**

```java
static int minRefuel(int target,int fuel,int[][] s) { PriorityQueue<Integer> q=new PriorityQueue<>(Comparator.reverseOrder());int pos=0,stops=0;for(int i=0;i<=s.length;i++){int next=i==s.length?target:s[i][0];while(fuel<next-pos){if(q.isEmpty())return -1;fuel+=q.poll();stops++;}fuel-=next-pos;pos=next;if(i<s.length)q.add(s[i][1]);}return stops; }
```

**Complexity:** O((n+stops) log n) time, O(n) space

### Q39. Find the shortest walk that visits every graph node

**Problem:** The graph is connected and unweighted; a node may be revisited, and any start node is allowed.

**Approach:** BFS from every node simultaneously over (node, visited-mask) states.

**Java solution:**

```java
static int shortestVisitAll(int[][] g) { int n=g.length,all=(1<<n)-1;boolean[][]v=new boolean[n][1<<n];Queue<int[]>q=new ArrayDeque<>();for(int i=0;i<n;i++){q.add(new int[]{i,1<<i});v[i][1<<i]=true;}int d=0;while(!q.isEmpty()){for(int z=q.size();z>0;z--){int[]x=q.poll();if(x[1]==all)return d;for(int y:g[x[0]]){int m=x[1]|1<<y;if(!v[y][m]){v[y][m]=true;q.add(new int[]{y,m});}}}d++;}return -1;}
```

**Complexity:** O((V+E)·2^V) time, O(V·2^V) space

### Q40. Hire k workers at minimum total cost subject to equal wage-to-quality ratios

**Problem:** Each worker has quality and minimum wage; pay each selected worker at least their minimum and keep one common ratio.

**Approach:** Sort by wage/quality ratio, maintain the k smallest qualities in a max-heap, and evaluate each ratio as the common rate.

**Java solution:**

```java
static double minCost(int[] q,int[] w,int k) { Integer[] id=new Integer[q.length];for(int i=0;i<id.length;i++)id[i]=i;Arrays.sort(id,Comparator.comparingDouble(i->(double)w[i]/q[i]));PriorityQueue<Integer> max=new PriorityQueue<>(Comparator.reverseOrder());long sum=0;double best=Double.POSITIVE_INFINITY;for(int i:id){max.add(q[i]);sum+=q[i];if(max.size()>k)sum-=max.poll();if(max.size()==k)best=Math.min(best,sum*(double)w[i]/q[i]);}return best; }
```

**Complexity:** O(n log n) time, O(n) space

---

[Module guide](README.md) · [Repository home](../README.md)