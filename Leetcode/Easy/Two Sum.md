## 🔹 Problem Statement

Given an array `nums` and an integer `target`, return indices of two numbers such that:

nums[i] + nums[j] = target

- Each input has exactly one solution
- Cannot use the same element twice

---

## 🔹 Approach: HashMap (Optimal)

### 💡 Core Idea

- Use a **HashMap** to store values and their indices
- For each element, check if the **complement exists**

👉 Complement =

target - current element

---

## 🔹 Algorithm Steps

1. Create a HashMap → `value → index`
2. Traverse the array
3. For each element:
    - Find `target - num`
    - If exists in map → return indices
4. Else → store current element in map

---

## 🔹 Code Explanation

HashMap<Integer,Integer> map = new HashMap<>();

👉 Stores number as key and index as value

---

for(int i = 0; i < nums.length; i++)

👉 Iterate through array

---

int num = nums[i];

👉 Current element

---

if(map.containsKey(target - num))

👉 Check if complement already exists

---

return new int[] {i, map.get(target - num)};

👉 Return indices of current and complement

---

map.put(num, i);

👉 Store current element after checking

---

return new int[] {};

👉 Return empty if no solution (edge case)

---

## 🔹 Example Walkthrough

Input:

nums = [2, 7, 11, 15]  
target = 9

|Step|num|map|Action|
|---|---|---|---|
|i=0|2|{}|store (2 → 0)|
|i=1|7|{2:0}|found 9-7=2 ✔|

Output:

[1, 0]

---

## 🔹 Complexity Analysis

|Type|Complexity|
|---|---|
|Time|O(n)|
|Space|O(n)|

---

## 🔹 Why This Works

- HashMap gives **O(1) lookup**
- Avoids nested loops (brute force O(n²))
- Efficient for large inputs

---

## 🔹 Important Interview Points

- Always check **before inserting into map**
- Works even if numbers repeat
- Order of indices may vary

---

## 🔹 Edge Cases

- Negative numbers ✔
- Duplicate values ✔
- No solution → empty array

---

## 🔹 Brute Force vs Optimal

|Approach|Time Complexity|
|---|---|
|Brute Force|O(n²)|
|HashMap|O(n) ✅|