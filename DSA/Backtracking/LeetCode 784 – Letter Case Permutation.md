

**Pattern:** Backtracking + DFS  
**Difficulty:** Medium

## 1. Problem Understanding

Given a string containing letters and digits, generate all possible strings by changing the case of alphabetic characters.

Conditions:

- Each letter can be lowercase or uppercase.
    
- Digits must remain unchanged.
    
- Return all possible permutations.
    

**Example:**

Input: `s = "a1b2"`

Output:

```
a1b2
a1B2
A1b2
A1B2
```

## 2. Approach – Backtracking

At every character, we have two possibilities if it is a letter:

1. Convert the character to uppercase.
    
2. Convert the character to lowercase.
    

If it is a digit, we have only one possibility: keep it unchanged.

### Recursive Steps

1. Start with index `0` and an empty `StringBuilder`.
    
2. Check whether the current character is a letter.
    
3. If lowercase, explore the uppercase branch.
    
4. If uppercase, explore the lowercase branch.
    
5. Backtrack by removing the appended character.
    
6. Explore the original character branch.
    
7. When the index reaches the string length, add the result to the list.
    

## 3. Important Code Logic

### Check whether the character is a letter

```
Character.isLetter(str.charAt(count))
```

Checks whether the current character is alphabetic.

### Check lowercase or uppercase

```
Character.isLowerCase(str.charAt(count))
Character.isUpperCase(str.charAt(count))
```

Determines the current character's case.

### Change character case

```
Character.toUpperCase(str.charAt(count))
Character.toLowerCase(str.charAt(count))
```

Converts the current character into the opposite case.

### Backtracking

```
sb.append(ch);
rec(str, count + 1, sb);
sb.deleteCharAt(sb.length() - 1);
```

**Remember: Choose → Explore → Undo**

## 4. Mistakes / Important Observations

|Observation|Explanation|
|---|---|
|Two choices for letters|Uppercase and lowercase|
|One choice for digits|Digits cannot change case|
|Must undo after recursion|Prevents characters from previous branches remaining in StringBuilder|
|Base condition needs `return`|Stops recursion after a complete permutation is generated|
|Original character must also be explored|Ensures both possible cases are included|

### Important: Why do we have two recursive calls?

For a lowercase character like `a`:

- First branch explores `A`.
    
- Second branch explores `a`.
    

For a digit like `1`:

- Only the original character `1` is explored.
    

## 5. Recursion Visualization

Input: `a1`

```
                 ""
                  |
             /         \
            A           a
            |           |
           A1          a1
```

Output:

```
A1
a1
```

## 6. Complexity Analysis

Let `L` = number of alphabetic characters and `n` = total string length.

Number of possible permutations:

[  
2^L  
]

|Complexity|Value|
|---|---|
|Time|O(n × 2^L)|
|Recursion stack|O(n)|
|Result storage|O(n × 2^L)|

## 7. Key Takeaways

- Identify how many choices exist at each index.
    
- Letters create two branches.
    
- Digits create one branch.
    
- Use `StringBuilder` to construct strings efficiently.
    
- Always backtrack after exploring a branch.
    
- Base condition should stop recursion after adding a complete result.
    

## 8. Related Problems

- [[1415 - The k-th Lexicographical String of All Happy Strings of Length n]]
    
- [[22 - Generate Parentheses]]
    
- [[17 - Letter Combinations of a Phone Number]]
    
- [[78 - Subsets]]
    

**Core Pattern:** Backtracking with multiple choices at each index.