
**Pattern:** Backtracking + Permutations with Used Array + Duplicate Handling  
**Difficulty:** Hard

## 1. Problem Understanding

Given an integer array `nums`, find the number of distinct permutations such that the sum of every pair of adjacent elements is a perfect square.

A number is a perfect square if it can be expressed as:

[  
x = k^2  
]

**Example:**

Input:

```
nums = [1, 17, 8]
```

Valid permutations:

```
[1, 8, 17]  → 1+8=9, 8+17=25
[17, 8, 1]  → 17+8=25, 8+1=9
```

Output:

```
2
```

## 2. Approach – Backtracking

1. Sort the input array to place duplicate elements together.
    
2. Create a `boolean[] used` array to track selected indices.
    
3. Maintain a `path` to construct the current permutation.
    
4. Try every unused element.
    
5. Skip duplicate elements using duplicate-handling logic.
    
6. Check whether the sum of the current element and previous element is a perfect square.
    
7. If valid, add the element and recursively explore.
    
8. Backtrack by removing the last element and marking it unused.
    
9. When `path.size() == nums.length`, increment the answer.
    

## 3. Important Code Logic

### A. Used Array

```
if (used[i]) continue;
```

Prevents selecting the same array index more than once in a permutation.

### B. Sorting

```
Arrays.sort(nums);
```

Places duplicate elements next to each other.

Sorting is necessary for the duplicate-skipping technique used here.

### C. Duplicate Handling

```
if (i > 0 &&
    nums[i] == nums[i - 1] &&
    !used[i - 1]) {
    continue;
}
```

**Why does this work?**

- `nums[i] == nums[i - 1]` → Current and previous elements are identical.
    
- `!used[i - 1]` → Previous identical element has not been selected in the current path.
    
- `continue` → Skip the current element to avoid generating duplicate permutations.
    

**Important concept:**

Identical elements are interchangeable. We enforce selecting identical elements in their original sorted order to avoid duplicate search paths.

### D. Perfect Square Validation

```
int num = nums[i] + path.get(path.size() - 1);

int x = (int) Math.sqrt(num);

if (x * x != num) {
    continue;
}
```

Explanation:

- Calculate the sum of adjacent elements.
    
- Find the square root.
    
- Convert it to an integer.
    
- Check whether `x * x` equals the original sum.
    
- If not, skip that choice.
    

Examples:

|Sum|Square root|Valid?|
|---|---|---|
|4|2|Yes|
|9|3|Yes|
|16|4|Yes|
|25|5|Yes|
|10|3.16|No|
|18|4.24|No|

### E. Base Condition

```
if (path.size() == nums.length) {
    count++;
    return;
}
```

When all elements are selected, we have found one valid permutation.

### F. Backtracking

```
used[i] = true;
path.add(nums[i]);

rec(nums, used, path);

path.remove(path.size() - 1);
used[i] = false;
```

**Remember: Choose → Explore → Undo**

## 4. Mistakes I Made

|Mistake|Why it was wrong|Correction|
|---|---|---|
|Used `% 1` to check perfect squares|Every integer is divisible by 1|Use `Math.sqrt()`|
|Returned immediately when an invalid pair was found|Would prevent exploring other valid choices|Use `continue` to skip only that choice|
|Did not handle duplicate elements|Generated identical permutations multiple times|Sort and use duplicate-skipping condition|
|Initially missed sorting|Duplicate elements were not guaranteed to be adjacent|Use `Arrays.sort()`|
|Checked square condition without understanding the formula|Perfect square is not the same as divisibility|Verify `x * x == sum`|

## 5. Recursion Pattern

```
                    []
              /     |     \
             1      8      17
             |
          [1]
             |
            [1, 8]
             |
         Check 8 + 17
             |
         Perfect square?
             |
          [1, 8, 17]
```

At every recursive level:

- Select an unused element.
    
- Validate the adjacent sum.
    
- Explore the next level.
    
- Undo the selection.
    

## 6. Complexity Analysis

Let `n` be the length of the array.

|Complexity|Value|
|---|---|
|Time|O(n! × n) worst case|
|Recursion stack|O(n)|
|Used array|O(n)|
|Result storage|O(n × P), where P is the number of valid permutations stored|

**Optimization note:** You only need the count, so storing every permutation in `res` is unnecessary. You can increment `count` directly at the base condition.

## 7. Key Takeaways

- Use `used[]` for permutations where each index can be selected only once.
    
- Sort the array before applying duplicate handling.
    
- Use `!used[i-1]` to prevent duplicate permutation paths.
    
- Validate constraints before making a recursive choice.
    
- Use `Math.sqrt()` to check perfect squares.
    
- Backtrack after every recursive call.
    

## 8. Related LeetCode Problems

- [[46 - Permutations]]
    
- [[47 - Permutations II]]
    
- [[784 - Letter Case Permutation]]
    
- [[1415 - The k-th Lexicographical String of All Happy Strings of Length n]]
    
- [[78 - Subsets]]
    

**Core Pattern:** Backtracking → Permutations → Used Array → Duplicate Skipping → Constraint Validation.