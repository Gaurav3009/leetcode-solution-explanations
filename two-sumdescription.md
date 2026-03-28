# Optimal Solution for Two Sum: A Hash Map Approach

## Intuition
The core idea is to efficiently find the "complement" for each number in the array. If we are looking for two numbers `x` and `y` such that `x + y = target`, then for any given number `x` from the array, its complement `y` must be `target - x`.

A naive approach would be to check every possible pair, which is `O(N^2)`. To do better, we need a way to quickly check if `target - x` exists in the array and what its index is. A hash map (like `std::map` or `std::unordered_map` in C++) is perfect for this! We can store numbers as keys and their indices as values. This allows us to look up a complement in nearly constant time (average `O(1)` for `unordered_map`, `O(log N)` for `map`).

## ⚡ Approach
We'll use a `std::map<int, int>` to store `(number, index)` pairs as we iterate through the `nums` array.

1.  **Initialize:** Create an empty `std::vector<int>` called `vec` to store our result indices and an empty `std::map<int, int>` called `m`.
2.  **Handle First Element:** The provided code starts by adding the first element `nums[0]` and its index `0` to the map: `m[nums[0]] = 0;`. This initializes the map with a known value.
3.  **Iterate and Search:** Loop through the `nums` array starting from the second element (index `i = 1`) up to the end.
    a.  **Calculate Complement:** For the current number `nums[i]`, calculate the `complement` needed: `complement = target - nums[i]`.
    b.  **Check for Complement:** We need to see if this `complement` already exists in our map `m`. The condition in the code is:
        `if ((nums[0]) == (target - nums[i]) || (m[target - nums[i]] != 0))`
        *   The first part, `(nums[0]) == (target - nums[i])`, specifically checks if the current `nums[i]` forms the sum with the very first element `nums[0]`. This is important because `m[nums[0]]` would correctly return `0`, but the second part of the condition `(m[target - nums[i]] != 0)` would evaluate to `(0 != 0)` which is `false`. So, this explicitly handles the `nums[0]` case.
        *   The second part, `(m[target - nums[i]] != 0)`, checks if the `complement` exists in the map and its associated index is not `0`. If `target - nums[i]` is not in the map, accessing `m[target - nums[i]]` will insert it with a default value of `0`, making `(0 != 0)` false. If it *is* in the map with a non-zero index, this condition will be true.
    c.  **Found Solution:** If either of the above conditions is true (meaning the complement is found):
        *   Add the index of the `complement` (which is `m[target - nums[i]]`) to `vec`.
        *   Add the current index `i` to `vec`.
        *   Since the problem guarantees exactly one solution, we can `break` the loop immediately.
    d.  **Not Found:** If the complement is not found (both conditions are false):
        *   Add the current number `nums[i]` and its index `i` to the map: `m[nums[i]] = i;`. This makes `nums[i]` available as a complement for subsequent numbers.
4.  **Return Result:** After the loop, `vec` will contain the indices of the two numbers that sum up to `target`. Return `vec`.

## Dry Run
Let's trace `nums = [3, 2, 4], target = 6`

1.  Initialize `vec = []`, `m = {}`.
2.  `m[nums[0]] = 0;` => `m = {3: 0}`.
3.  Start loop from `i = 1`:
    *   **`i = 1`**: `nums[i] = 2`
        *   `complement = target - nums[i] = 6 - 2 = 4`.
        *   Check condition: `((nums[0] == complement) || (m[complement] != 0))`
            *   `(3 == 4)` is `false`.
            *   `m[4]` is accessed. Since `4` is not in `m`, it's inserted as `4:0`. `m` becomes `{3:0, 4:0}`. The expression `m[4]` evaluates to `0`. So `(0 != 0)` is `false`.
            *   Both parts of the `||` are `false`.
        *   `else` block executes: `m[nums[i]] = i` => `m[2] = 1`. `m` is now `{3:0, 4:0, 2:1}`.
    *   **`i = 2`**: `nums[i] = 4`
        *   `complement = target - nums[i] = 6 - 4 = 2`.
        *   Check condition: `((nums[0] == complement) || (m[complement] != 0))`
            *   `(3 == 2)` is `false`.
            *   `m[2]` is accessed. `2` is in `m`, its value is `1`. So `m[2]` evaluates to `1`. `(1 != 0)` is `true`.
            *   The second part of the `||` is `true`, so the entire condition is `true`.
        *   Solution found!
        *   `vec.push_back(m[complement])` => `vec.push_back(m[2])` => `vec.push_back(1)`. `vec = [1]`.
        *   `vec.push_back(i)` => `vec.push_back(2)`. `vec = [1, 2]`.
        *   `break` the loop.
4.  Return `vec = [1, 2]`. This matches the expected output.

## ⏱ Complexity
*   **Time Complexity:** `O(N log N)`
    *   We iterate through the `nums` array once (N elements).
    *   Inside the loop, `std::map` operations (insertion and lookup) take `O(log K)` time, where `K` is the number of elements currently in the map. In the worst case, `K` can be up to `N`.
    *   Therefore, the total time complexity is `O(N log N)`.
    *   If `std::unordered_map` was used instead, the average time complexity would be `O(N)`.
*   **Space Complexity:** `O(N)`
    *   In the worst case, we might store all `N` elements of the `nums` array in the map before finding the desired pair. Each map entry stores an integer key and an integer value.

## Code
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> vec;
        // int f=0; // This variable is unused and can be removed.
        map<int, int> m; // Using std::map for (number -> index) mapping

        // Initialize the map with the first element and its index
        // This is crucial for the specific logic in the loop's condition
        m[nums[0]] = 0; 

        // Iterate through the array starting from the second element
        for(int i = 1; i < nums.size(); i++) {
            // Calculate the complement needed to reach the target
            int complement = target - nums[i];

            // Check if the complement exists in the map
            // The condition handles two cases:
            // 1. If the complement is nums[0] (which has index 0).
            //    This is needed because m[complement] for index 0 would return 0,
            //    making the (m[complement] != 0) part false.
            // 2. If the complement exists in the map and its index is not 0.
            //    If complement is not in map, m[complement] will insert it with 0,
            //    making (m[complement] != 0) false.
            if ((nums[0] == complement) || (m[complement] != 0)) {
                // Complement found! Add its index and the current index to the result vector
                vec.push_back(m[complement]);
                vec.push_back(i);
                break; // Exactly one solution, so we can stop
            } else {
                // Complement not found yet, add the current number to the map
                // for future lookups
                m[nums[i]] = i;
            }
        }
        return vec;
    }
};
```

## Edge Cases
The problem statement provides helpful constraints that simplify edge case handling:
*   `2 <= nums.length <= 10^4`: The input array will always have at least two elements, so we don't need to worry about empty arrays or single-element arrays.
*   `Only one valid answer exists.`: We are guaranteed to find a pair, so we don't need to handle cases where no solution exists.
*   `You may not use the same element twice.`: This means if `nums = [3, 2, 3]` and `target = 6`, we should use `nums[0]` and `nums[2]`, not `nums[0]` twice. Our hash map approach correctly handles this by storing indices, ensuring we pick two distinct positions.
*   **Values and Target:** `nums[i]` and `target` can be negative, zero, or positive, which the map approach handles naturally. For example, `nums = [0, 1], target = 1` or `nums = [-1, -2], target = -3` work correctly.

The specific implementation detail regarding `m[key] != 0` check combined with `(nums[0] == target - nums[i])` is a way to handle the "edge case" where the index of a complement happens to be `0`. A more robust way to check for existence in `std::map` without potentially inserting an element would be `m.count(complement)` or `m.find(complement) != m.end()`. However, the provided code's logic correctly navigates this for the given problem constraints.