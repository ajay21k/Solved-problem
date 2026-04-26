## 🔹 📌 Definition

A **palindrome** is a string that reads the same forward and backward.

Examples:

aba, racecar, aa

---

## 🔹 🎯 Problem Goal

👉 Find the **longest substring** that is a palindrome  
👉 If multiple → return the **first occurring one**

---

## 🔹 🚫 Brute Force (Avoid)

- Generate all substrings → check palindrome
- Time Complexity: **O(n³)** ❌

---

## 🔹 ✅ Optimal Approach: Expand Around Center

---

## 🧠 Core Idea

> 👉 Every palindrome expands from its center

---

## 🔍 Types of Centers

|Type|Example|Center|
|---|---|---|
|Odd length|`aba`|single index|
|Even length|`abba`|between two indices|

---

## 🔁 Algorithm Steps

For each index `i`:

### 1️⃣ Odd length expansion

left = i;  
right = i;

---

### 2️⃣ Even length expansion

left = i;  
right = i + 1;

---

### 3️⃣ Expand while valid

while(left >= 0 && right < n && str.charAt(left) == str.charAt(right)){  
    // update answer  
    left--;  
    right++;  
}

---

## 🔥 Code Template (Must Remember)

String res = "";  
  
for(int i = 0; i < n; i++){  
  
    // Odd length  
    int left = i, right = i;  
    while(left >= 0 && right < n && str.charAt(left) == str.charAt(right)){  
        if((right - left + 1) > res.length()){  
            res = str.substring(left, right + 1);  
        }  
        left--;  
        right++;  
    }  
  
    // Even length  
    left = i;  
    right = i + 1;  
    while(left >= 0 && right < n && str.charAt(left) == str.charAt(right)){  
        if((right - left + 1) > res.length()){  
            res = str.substring(left, right + 1);  
        }  
        left--;  
        right++;  
    }  
}

---

## ⏱️ Complexity

- Time: **O(n²)**
- Space: **O(1)**

---

## ⚠️ Common Mistakes

❌ Missing condition in `while` → infinite loop  
❌ Using `substring(left, right)` → wrong (right exclusive)  
❌ Not checking even-length palindromes  
❌ Storing all substrings → TLE  
❌ Not updating max length properly

---

## 🔍 Example

Input:

babad

Output:

bab

Why?

- `bab` and `aba` both valid
- `bab` appears first ✔

---

## 🔁 Reusable Pattern

> 🔥 **Two Pointer Expansion**

---

## 🚀 Where else to use this

### ✅ 1. Count palindromic substrings

count++;

---

### ✅ 2. Check if substring is palindrome

---

### ✅ 3. Longest palindrome (this problem)

---

### ✅ 4. String symmetry problems

---

### ✅ 5. DNA / pattern matching problems

---

## 🧩 Pattern Recognition Trick

If problem mentions:

- palindrome
- mirror
- symmetric
- reverse equal

👉 Think:

EXPAND AROUND CENTER

---

## ⚡ Advanced (for interviews)

|Method|Complexity|
|---|---|
|Expand center|O(n²)|
|DP|O(n²)|
|Manacher’s Algorithm|O(n) 🚀|

---

## 🎯 Final One-Line Summary

👉 **Start from center, expand outward, track longest palindrome**