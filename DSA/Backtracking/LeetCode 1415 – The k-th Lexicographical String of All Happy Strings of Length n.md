

**Pattern:** Backtracking + DFS  
**Difficulty:** Medium

## 1. Problem Understanding

Generate strings of length `n` using only `{a, b, c}`.

Conditions:

- No two consecutive characters should be the same.
    
- Return the kth string in lexicographical order.
    
- If fewer than `k` valid strings exist, return `""`.
    

## 2. Approach – Backtracking

1. Start with an empty `StringBuilder`.
    
2. Try all three characters: `a`, `b`, `c`.
    
3. Check whether the current character differs from the previous character.
    
4. If valid, append the character.
    
5. Recursively explore the next position.
    
6. Backtrack by removing the last character.
    
7. When the string length reaches `n`, add it to the result.
    

## 3. Important Code Logic

### Constraint checking

```
if (sb.length() > 0 &&
    sb.charAt(sb.length() - 1) == arr[i])
    continue;
```

- `sb.length() > 0` → Check whether a previous character exists.
    
- `sb.charAt(sb.length() - 1)` → Get the previous character.
    
- `== arr[i]` → Check whether both characters are identical.
    
- `continue` → Skip the invalid choice.
    

### Backtracking

```
sb.append(arr[i]);
rec(n, arr, count + 1, sb);
sb.deleteCharAt(sb.length() - 1);
```

**Remember: Choose → Explore → Undo**

## 4. Mistakes I Made

|Mistake|Why it was wrong|Correction|
|---|---|---|
|Initially allowed all characters|Generated invalid strings like `aaa`, `abb`|Check previous character|
|Forgot consecutive-character restriction|Violated Happy String definition|Compare current and previous characters|
|Used sorting|Unnecessary because characters are explored in `a, b, c` order|Generate lexicographically|
|Generated all possible strings|Extra work when only kth string is required|Stop after finding kth string|

## 5. Optimization

Number of happy strings:

[  
3 \times 2^{n-1}  
]

For `n >= 1`.

Since we explore characters in `a → b → c` order, strings are automatically generated in lexicographical order.

Therefore:

- `Collections.sort(res)` is unnecessary.
    
- We can stop recursion after finding the kth string.
    
- We can use a counter instead of storing every string.
    

## 6. Complexity Analysis

Let `H = 3 × 2^(n-1)`.

|Complexity|Value|
|---|---|
|Time|O(n × 2^n)|
|Recursion stack|O(n)|
|Result storage|O(n × 2^n)|

## 7. Key Takeaways

- Identify choices at every recursive position.
    
- Apply constraints before making a choice.
    
- Define the base condition.
    
- Always backtrack after recursive exploration.
    
- Check whether the generation order is already lexicographical.
    
- Look for early termination when only the kth result is required.
    

## 8. Related Problems

- [[784 - Letter Case Permutation]]
    
- [[22 - Generate Parentheses]]
    
- [[39 - Combination Sum]]
    
- [[1415 - The k-th Lexicographical String of All Happy Strings of Length n]]
    

**Core Pattern:** Backtracking (DFS) with constraints.