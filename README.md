
## Medium Problem: 

https://leetcode.com/problems/number-of-ways-to-split-a-string/

## Solution:

Let us first make several useful observations:
 - If the number of 1's in the input string (`s`) is NOT a multiple of 3, then the answer will be 0.
 - If the number of 1's in the input string (`s`) is 0, then we can put the two dividers (`|`) to determine how we 'cut' the string into three non-empty substrings (see the examples below).
 - If the number of 1's in the input string (`s`) is `3k > 0`, then we know that each substring (`s1`, `s2`, and `s3`) must contain exactly `k` 1's.

### Case 1
The first observation allows us to handle the case where we can't split `s` into three substrings with equal number of 1's.

### Case 2
The second observation allows us to solve the special case when there is simply no 1's in `s`.
Since `s` only contains 0's, we merely have to decide how to split `s` into three non-empty substrings by putting two dividers `|`.
```
Example 3: (from the problem statement)
Input: s = "0000"
Output: 3
Explanation: There are three ways to split s in 3 parts.
"0|0|00"
"0|00|0"
"00|0|0"
```
In the example above, notice that we cannot put a divider before the first character or after the last character, 
because that'd make `s1` or `s3` an empty string (respectively). 
We also can't put the two dividers next to each other (without having a character from `s` in-between), because that'd make `s2` an empty string.
Therefore, we must choose exactly two positions out of `n-1` positions, which is equal to the binomial coefficient, `(n-1)-choose-2`; 
in other words, `(n-1)*(n-2)/2`.

### Case 3
The third observation is similarly useful.
Recall that `s` must have `3k` 1's for some `k > 0` under our assumptions.
Then, because we know that `s1` must contain exactly `k` 1's, 
we must put the first divider strictly after the `k`-th 1 in `s` and strictly before the `(k+1)`-th 1 in `s`.
Similarly, we must put the second divider strictly after the `(2k)`-th 1 in `s` and strictly before the `(2k+1)`-th 1 in `s`.

For instance, in the example below, we have two choices for the first divider as the `k`-th 1 is at index 0 and the `(k+1)`-th 1 is at index 2.
Similarly, we have two choices for the second divider as the `(2k)`-th 1 is at index 2 and `(2k+1)`-th 1 is at index 4.
```
Example 1: (from the problem statement)
Input: s = "10101"
Output: 4
Explanation: There are four ways to split s in 3 parts where each part contain the same number of letters '1'.
"1|010|1"
"1|01|01"
"10|10|1"
"10|1|01"
```
Formally, if `pos(s, k)` is the index of the k-th 1 in `s`, 
then we have `pos(s, k+1) - pos(s, k)` choices for the first divider
and we have `pos(s, 2k+1) - pos(s, 2k)` choices for the second divider.
The total number of ways `s` can be split in this case would be exactly
`(pos(s, k+1) - pos(s, k))*(pos(s, 2k+1) - pos(s, 2k))`.

With these observations, we can solve the problem based on which of the three cases an input string belongs to.

## Code:

The following Java program implements the proposed solution above.
It counts the number of 1's in `s` (using a variable named `ones`).
If it's not a multiple of 3, then the method returns 0.
If it's equal to 0, then the method returns `(n-1)-choose-2`, modulo `10^9+7`.
Otherwise, it uses the array (named `pos`) to obtain the indices of the `k`-th 1, `(k+1)`-th 1, `(2k)`-th 1, and `(2k+1)`-th 1.
Then, it calculates the final answer using our formula from above.

**Running Time**: The solution simply iterates over the string once, which takes `O(n)` time where `n` is the length of `s`.
**Memory Usage**: Besides the input string, the solution uses an integer array `pos` of size `n` (hence `O(n)` auxiliary memory).

```java
class Solution {
  final long MOD = 1_000_000_000L + 7L;

  public int numWays(String s) {
    int n = s.length(), ones = 0;
    int[] pos = new int[s.length()+1];
    for(int i = 0; i < n; i++) {
      if(s.charAt(i) == '1') {
        ones++;
        pos[ones] = i;
      }
    }
    if(ones % 3 != 0) return 0;

    if(ones == 0) return (int) ((((long) n-1L)*((long) n-2L)/2L)%MOD);
    
    long diff1 = pos[ones/3+1] - pos[ones/3];
    long diff2 = pos[ones*2/3+1] - pos[ones*2/3];
    
    return (int)((diff1 * diff2)%MOD);
  }
}
```

**Alternative Implementation**: Instead of using the `pos` array, 
we can simply iterate `s` one more time (after the first for-loop to determine the number of 1's in `s`) 
to determine the necessary indices. 
This approach still runs in `O(n)` time (but practically twice as slow as the first one), but only requires `O(1)` auxiliary memory.

```java
class Solution {
  final long MOD = 1_000_000_000L + 7L;

  public int numWays(String s) {
    int n = s.length(), ones = 0;
    for(int i = 0; i < n; i++) {
      if(s.charAt(i) == '1') {
        ones++;
      }
    }
    if(ones % 3 != 0) return 0;

    if(ones == 0) return (int) ((((long) n-1L)*((long) n-2L)/2L)%MOD);
    
    int posk=0, pos2k=0, cntOnes = 0;
    long diff1=0, diff2=0;
    for(int i = 0; i < n; i++) {
      if(s.charAt(i) == '1') {
        cntOnes++;
        if(cntOnes == ones / 3) posk = i;
        if(cntOnes == ones/3 + 1)  diff1 = i - posk;
        
        if(cntOnes == ones*2 / 3) pos2k = i;
        if(cntOnes == ones*2/3 + 1) diff2 = i - pos2k;
      }
    }
    
    return (int)((diff1 * diff2)%MOD);
  }
}
```
