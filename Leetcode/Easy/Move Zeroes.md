### 💡 Idea

Shift all **non-zero elements to the front** while maintaining order, then fill remaining positions with `0`.

---

### ⚙️ Approach

1. Use a pointer `count` to track position for next non-zero.
2. Traverse array:
    - If element ≠ 0 → place it at `nums[count]` and increment `count`.
3. After loop, fill remaining positions with `0`.

---

### ✅ Code

class Solution {  
    public void moveZeroes(int[] nums) {  
        int count = 0;  
  
        // Move non-zero elements forward  
        for (int i = 0; i < nums.length; i++) {  
            if (nums[i] != 0) {  
                nums[count++] = nums[i];  
            }  
        }  
  
        // Fill remaining with zeros  
        while (count < nums.length) {  
            nums[count++] = 0;  
        }  
    }  
}

---

### ⏱ Time Complexity

- **O(n)** → Single pass + fill (linear)

---

### 📦 Space Complexity

- **O(1)** → In-place (no extra space)

---

### 📝 Key Points

- Maintains **relative order** of non-zero elements
- Uses **two-pointer technique (i, count)**
- Efficient & optimal solution