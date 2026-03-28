Hey LeetCoders! 👋

Let's dive into the classic "Search in Rotated Sorted Array" problem. This problem is a fundamental application of binary search, requiring a clever twist to handle the rotation. We'll aim for an optimal `O(log n)` solution using a single-pass binary search.

---

# Search in Rotated Sorted Array: Optimal O(log N) Single-Pass Binary Search

## Intuition
The core idea behind solving this problem efficiently is to adapt **binary search**. Even though the entire array isn't sorted, one crucial property holds true: **when you split the array into two halves at `mid`, at least one of these halves will always be sorted.**

We can leverage this fact:
1.  Identify which half (`[start, mid]` or `[mid, end]`) is sorted.
2.  Check if our `target` lies within the *sorted* half.
3.  If it does, narrow our search to that sorted half.
4.  If it doesn't, the `target` must be in the *other* (unsorted) half, so we narrow our search there.

By repeatedly halving the search space, we maintain the `O(log n)` complexity.

## ⚡ Approach
We'll use a modified binary search algorithm.

1.  **Initialize Pointers**: Set `start = 0` and `end = nums.size() - 1`.
2.  **Loop Condition**: Continue the loop as long as `start <= end`.
3.  **Calculate Mid**: Find the middle index `mid = start + (end - start) / 2`. This calculation prevents integer overflow compared to `(start + end) / 2`.
4.  **Target Found**: If `nums[mid] == target`, we've found our element, so return `mid`.
5.  **Identify Sorted Half**:
    *   **Case 1: Left half `[start, mid]` is sorted (`nums[start] <= nums[mid]`)**
        *   Check if `target` lies within this sorted left half: `nums[start] <= target && target < nums[mid]`.
        *   If yes, the target must be on the left side, so update `end = mid - 1`.
        *   If no, the target must be on the right side (even if unsorted relative to `start`), so update `start = mid + 1`.
    *   **Case 2: Right half `[mid, end]` is sorted (`nums[mid] > nums[end]` implies `nums[start] > nums[mid]`)**
        *   Check if `target` lies within this sorted right half: `nums[mid] < target && target <= nums[end]`.
        *   If yes, the target must be on the right side, so update `start = mid + 1`.
        *   If no, the target must be on the left side (even if unsorted relative to `end`), so update `end = mid - 1`.
6.  **Target Not Found**: If the loop finishes (i.e., `start > end`), it means the target was not found in the array. Return `-1`.

## Dry Run
Let's trace `nums = [4,5,6,7,0,1,2]`, `target = 0`

-   **Initial**: `start = 0`, `end = 6`
-   **Iteration 1**:
    -   `mid = 0 + (6-0)/2 = 3`. `nums[mid] = nums[3] = 7`.
    -   `nums[mid]` (7) != `target` (0).
    -   Check sorted half: `nums[start]` (4) <= `nums[mid]` (7) -> **Left half `[4,5,6,7]` is sorted.**
    -   Is `target` (0) in `[nums[start], nums[mid]]` (`[4, 7]`)? No, `0` is not in this range.
    -   So, search in the right half: `start = mid + 1 = 4`.
    -   Current: `start = 4`, `end = 6`
-   **Iteration 2**:
    -   `mid = 4 + (6-4)/2 = 5`. `nums[mid] = nums[5] = 1`.
    -   `nums[mid]` (1) != `target` (0).
    -   Check sorted half: `nums[start]` (0) <= `nums[mid]` (1) -> **Left half `[0,1]` is sorted.** (Wait, `nums[start]` is `0`, `nums[mid]` is `1`. The elements are `nums[4]=0, nums[5]=1`. This segment is sorted.)
    -   Is `target` (0) in `[nums[start], nums[mid]]` (`[0, 1]`)? Yes, `0 <= 0 && 0 < 1` is true.
    -   So, search in the left half: `end = mid - 1 = 4`.
    -   Current: `start = 4`, `end = 4`
-   **Iteration 3**:
    -   `mid = 4 + (4-4)/2 = 4`. `nums[mid] = nums[4] = 0`.
    -   `nums[mid]` (0) == `target` (0). **Target found!**
    -   Return `mid = 4`.

## ⏱ Complexity
*   **Time Complexity**: `O(log n)`. In each step of the binary search, we reduce the search space by half.
*   **Space Complexity**: `O(1)`. We only use a few extra variables for pointers, regardless of the input array size.

## Code
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int n = nums.size();
        if (n == 0) {
            return -1;
        }

        int start = 0;
        int end = n - 1;

        while (start <= end) {
            int mid = start + (end - start) / 2;

            if (nums[mid] == target) {
                return mid;
            }

            // Check if the left half is sorted
            if (nums[start] <= nums[mid]) {
                // If target is within the sorted left half
                if (nums[start] <= target && target < nums[mid]) {
                    end = mid - 1; // Search in the left half
                } else {
                    start = mid + 1; // Target is in the right (unsorted or sorted) half
                }
            } 
            // Else, the right half must be sorted
            else { 
                // If target is within the sorted right half
                if (nums[mid] < target && target <= nums[end]) {
                    start = mid + 1; // Search in the right half
                } else {
                    end = mid - 1; // Target is in the left (unsorted or sorted) half
                }
            }
        }

        return -1; // Target not found
    }
};
```

## Edge Cases
*   **Empty Array**: `nums = []`, `target = 5`. The code handles this with `if (n == 0) return -1;`.
*   **Single Element Array**: `nums = [1]`, `target = 1` (returns 0). `nums = [1]`, `target = 0` (returns -1). Handled correctly by the loop.
*   **Array Not Rotated**: `nums = [1,2,3,4,5]`, `target = 3`. The `nums[start] <= nums[mid]` condition will always be true, and the algorithm will behave like a standard binary search.
*   **Target at the ends**: `nums = [4,5,6,7,0,1,2]`, `target = 4` (returns 0). `nums = [4,5,6,7,0,1,2]`, `target = 2` (returns 6).
*   **Target is the pivot/minimum**: `nums = [4,5,6,7,0,1,2]`, `target = 0` (returns 4).

This approach is robust and covers all valid inputs for the problem! Happy coding!