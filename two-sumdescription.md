# Two Sum: The O(N log N) Map-Based Solution

## Intuition
The problem asks us to find two numbers in an array that sum up to a specific `target` value and return their indices. A brute-force approach would involve checking every possible pair, leading to an O(N^2) time complexity. We want something faster.

The core idea is to efficiently check if the "complement" of a number (i.e., `target - current_number`) has been seen before. If we iterate through the array, for each `nums[i]`, we can calculate its `complement = target - nums[i]`. If this `complement` already exists in a data structure that allows fast lookups, we've found our pair! A hash map (or `std::map` in C++) is perfect for this, as it allows us to store numbers along with their indices and retrieve them quickly.

## ⚡ Approach
The provided solution uses a `std::map<int, int>` to store the numbers encountered so far along with their respective indices. Here's a step-by-step breakdown:

1.  **Initialize Map**: Create an empty `std::map<int, int>` called `m`. This map will store `(number, index)` pairs.
2.  **Handle First Element**: The code starts by placing the first element `nums[0]` and its index `0` into the map: `m[nums[0]] = 0;`. This ensures `nums[0]` is available for lookup.
3.  **Iterate and Check**: Loop through the `nums` array starting from the second element (index `i = 1`) up to the end.
    *   For each `nums[i]` at the current index `i`:
        *   **Calculate Complement**: Determine the `complement` needed to reach the `target`: `complement = target - nums[i]`.
        *   **Check for Complement**: The solution's critical check is: `if ((nums[0]) == (target - nums[i]) || (m[target - nums[i]] != 0))`
            *   Let's break this down:
                *   `nums[0] == (target - nums[i])`: This part specifically checks if the current `nums[i]` forms a pair with the very first element `nums[0]`. This is important because `nums[0]`'s index is `0`.
                *   `m[target - nums[i]] != 0`: This part checks if the `complement` exists in the map and its stored index is **not** `0`. In C++'s `std::map`, accessing a key with `[]` that doesn't exist will insert it with a default-constructed value (which is `0` for `int`). So, if the complement was `nums[0]` (whose index is `0`), `m[complement]` would be `0`, and this condition `0 != 0` would be false. The first part of the `||` condition (`nums[0] == complement`) covers this specific case for `nums[0]`.
            *   If either of these conditions is true, it means we've found the `complement`!
        *   **Found Pair**: If the complement is found:
            *   Retrieve its index from the map: `m[complement]`.
            *   Add this index and the current index `i` to the result vector `vec`.
            *   Since the problem guarantees exactly one solution, we can `break` the loop and return `vec`.
        *   **Not Found**: If the complement is not found in the map (or its index was `0` and `nums[0]` wasn't the complement), add the current number `nums[i]` and its index `i` to the map: `m[nums[i]] = i;`. This makes `nums[i]` available for future lookups.
4.  **Return Result**: The function returns the `vec` containing the two indices.

## Dry Run
Let's trace with `nums = [3, 2, 4]`, `target = 6`.

1.  Initialize `vec` (empty), `m` (empty).
2.  `m[nums[0]] = 0;` becomes `m = {3: 0}`.
3.  Loop starts from `i = 1`:
    *   **`i = 1`**: `nums[1] = 2`.
        *   `complement = target - nums[1] = 6 - 2 = 4`.
        *   Check condition: `(nums[0] == complement || m[complement] != 0)`
            *   `nums[0] == complement` is `(3 == 4)`, which is `false`.
            *   `m[4]` is not in map. Accessing `m[4]` inserts `4` with value `0`. So, `m[4] != 0` is `(0 != 0)`, which is `false`.
            *   Overall condition `(false || false)` is `false`.
        *   `else` block: `m[nums[1]] = 1;` becomes `m[2] = 1`.
        *   `m` is now `{3: 0, 2: 1}`.
    *   **`i = 2`**: `nums[2] = 4`.
        *   `complement = target - nums[2] = 6 - 4 = 2`.
        *   Check condition: `(nums[0] == complement || m[complement] != 0)`
            *   `nums[0] == complement` is `(3 == 2)`, which is `false`.
            *   `m[2]` is in map, its value is `1`. So, `m[2] != 0` is `(1 != 0)`, which is `true`.
            *   Overall condition `(false || true)` is `true`.
        *   `if` block:
            *   `vec.push_back(m[complement]);` -> `vec.push_back(m[2]);` -> `vec.push_back(1);`. `vec = [1]`.
            *   `vec.push_back(i);` -> `vec.push_back(2);`. `vec = [1, 2]`.
            *   `break;`
4.  Return `vec`, which is `[1, 2]`. This is correct!

## ⏱ Complexity
*   **Time Complexity**: O(N log N)
    *   The loop iterates up to `N-1` times, where `N` is the number of elements in `nums`.
    *   Inside the loop, `std::map` operations (insertion `m[key] = value` and lookup `m[key]` or `m.count(key)`) take O(log N) time on average for a balanced binary search tree (which `std::map` is).
    *   Therefore, the total time complexity is O(N log N).
    *   *Note*: If `std::unordered_map` (a hash map) were used instead, the average time complexity would be O(N) because hash map operations take O(1) time on average.
*   **Space Complexity**: O(N)
    *   In the worst case, the `std::map` stores up to `N-1` elements (if the solution pair is found only at the very end).
    *   Therefore, the space complexity is proportional to the number of elements stored in the map.

## Code
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> vec;
        // int f=0; // This variable is unused and can be removed for cleaner code.
        map<int, int> m; // Using std::map (balanced binary search tree)

        // Store the first element and its index
        m[nums[0]] = 0;

        // Iterate from the second element
        for(int i=1; i<nums.size(); i++){
            int complement = target - nums[i];

            // Check if the complement exists in the map.
            // The condition (nums[0] == complement) handles the case where nums[0] is the complement
            // and its index (0) would make m[complement] != 0 evaluate to false.
            // The (m[complement] != 0) part checks if the complement exists and its index is not 0.
            if((nums[0]) == (complement) || (m[complement] != 0)){
                vec.push_back(m[complement]); // Add the index of the complement
                vec.push_back(i);             // Add the current index
                break; // Found the solution, exit loop
            } else {
                // If complement not found, add the current number and its index to the map
                m[nums[i]] = i;
            }
        }
        return vec;
    }
};
```

## Edge Cases
*   **Smallest Input Size**: The constraints state `2 <= nums.length`. The code correctly handles an array of two elements. For `nums = [x, y], target = x+y`, the first element `x` is put into the map with index 0. Then for `y` at index 1, `complement = target - y = x`. The condition `nums[0] == complement` becomes `x == x`, which is true. It correctly returns `[0, 1]`.
*   **Negative Numbers**: `nums` elements and `target` can be negative. The arithmetic `target - nums[i]` and map operations work correctly with negative values.
*   **Duplicate Numbers**: The problem states "you may not use the same element twice", which implies *indices* must be distinct. If `nums = [3, 3], target = 6`, the code handles this:
    *   `m[3] = 0`.
    *   For `i=1, nums[1]=3`, `complement = 3`.
    *   `nums[0] == complement` (`3 == 3`) is true.
    *   Returns `[m[3], 1]` which is `[0, 1]`. This is correct as `nums[0]` and `nums[1]` are at different indices.
*   **Target is Zero**: If `nums = [0, 0], target = 0`, the logic holds. `m[0]=0`. For `i=1, nums[1]=0`, `complement=0`. `nums[0]==complement` is true. Returns `[0, 1]`.