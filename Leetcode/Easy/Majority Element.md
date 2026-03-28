### 💡 Idea

Count frequency of each element using a **HashMap** and return the element that appears **more than n/2 times**.

---

### ⚙️ Approach

1. Traverse array and store frequency using `HashMap`.
2. Iterate through map:
    - If frequency > n/2 → return that element.

---

### ✅ Code

class Solution {  
    public int majorityElement(int[] nums) {  
        HashMap<Integer, Integer> map = new HashMap<>();  
  
        // Count frequencies  
        for (int i = 0; i < nums.length; i++) {  
            map.put(nums[i], map.getOrDefault(nums[i], 0) + 1);  
        }  
  
        int ans = 0;  
  
        // Find majority element  
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {  
            if (entry.getValue() > nums.length / 2) {  
                ans = entry.getKey();  
            }  
        }  
  
        return ans;  
    }  
}

---

### ⏱ Time Complexity

- **O(n)** → One pass to count + one pass over map

---

### 📦 Space Complexity

- **O(n)** → HashMap stores frequencies

---

### 📝 Key Points

- Uses **HashMap (key → element, value → frequency)**
- Works even if elements are unordered
- Not optimal for space (better: Boyer-Moore → O(1) space)