# 22. Generate Parentheses

Medium

## Problem

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

For example, given n = 3, a solution set is:

    [
      "((()))",
      "(()())",
      "(())()",
      "()(())",
      "()()()"
    ]

## But

The range of `n` is **NOT** mentioned. Assume it to be `0 <= n`.

## Ideas

### attemption

- All combination + check legal
    - $\frac{2N!}{N!N!} = \frac{2N * (2N-1) ... (N+1)}{N * (N-1) * ... * 1} < N * N ... N  \to O(N^N)$ 
    - legal: `(` >= `)`

- recursive: generate parantheses in all possible position
    - N recursive layers
    - possible position: 1, 2, 4, 6, 8, ..., 2(N - 1)
    - problem: repetition
        - e.g. `()()` + `()` == `()` + `()()`
        - set + list
<!--     - $\sum^{N-1}_{i=1} 2*i + 1 = \frac{(N-1)((2N-1)+1)}{2}+1=N(N-1)+1 \to O(N^2)$ -->

- from a counter:
    - count: 
        - current **left - right**
        - all left parentheses should < n
    - addition (put left parentheses) or substraction (put right parentheses)

### official approach

- backtracking:
    - count:
        - current left
        - current right

- DP: add 1 parentheness in each valid sequence
    - for each legal arrangement:
        - for all closure number c:
            - `solution(N)` = `(` `solution(c)` `)` + `solution(N-c-1)`
    - no repetition! because the left part and right part are separated by **`)`**, forming closure with `(` (left of the `solution(c)`)
        - that is, impossible to have a closure across left part and right part.
    - $O(4^n/\sqrt{n})$: n steps of n-th Catalan number $\frac{1}{n+1}\binom{2n}{n} < 4^n/(n\sqrt{n})$

## Code

### DP

### recursive + counter

```Python
class Solution:
    def generation(self, inp):
        # check termination
        if inp[0][1] == inp[0][2] == 0:
            return inp, True
        
        rt = []
        for s, count_rl, remain in inp:
            # put right
            if remain > 0: 
                rt += [(s + '(', count_rl + 1, remain - 1)]
            # put left
            if count_rl > 0:
                rt += [(s + ')', count_rl - 1, remain)]
        return rt, False
                
    def generateParenthesis(self, n: int) -> List[str]:
        terminate = False
        rt = [('', 0, n)]
        while not terminate:
            rt, terminate = self.generation(rt)
            
        return [r[0] for r in rt]
```

### generate parantheses in all possible position + set()

```Python
class Solution:
    def recursive_generation(self, ls):
        rt = []
        for s in ls:
            for i in range(len(s)):
                rt += [s[:i] + '()' + s[i:]]
        return list(set(rt))

    def generateParenthesis(self, n: int) -> List[str]:
        rt = ["()"]
        for _ in range(n-1):
            rt = self.recursive_generation(rt)
        return rt
```