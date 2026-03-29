This problem asks us to find all unique subsets (the power set) of an integer array that may contain duplicates. The key challenge is to avoid generating duplicate subsets.

# Subsets II: Backtracking with Duplicate Handling

## Intuition
The core idea for generating subsets is to use recursion (backtracking). For each number in the input array, we have two choices: either **include** it in our current subset or **exclude** it. By exploring all these choices, we can build all possible subsets.

To handle duplicates, we need a way to ensure that if we have multiple identical numbers (e.g., `[2, 2]`), we don't generate the same subset multiple times (e.g., `[1, 2]` using the first `2`, and `[1, 2]` again using the second `2`). The strategy for this is:
1.  **Sort the array**: This brings duplicate elements together, making them easy to identify.
2.  **Skip duplicates in the "exclude" branch**: When we decide to *exclude* an element `nums[i]`, we should also skip all subsequent elements that are identical to `nums[i]`. This prevents redundant branches in our recursion tree that would lead to identical subsets.

## ⚡ Approach
1.  **Sort the input array `nums`**: This is the first and most crucial step. Sorting allows us to easily group and skip duplicate elements.
    ```java
    Arrays.sort(nums);
    ```
2.  **Define a recursive helper function `combination(current_subset, nums, current_index)`**:
    *   `current_subset`: A `List<Integer>` representing the subset being built in the current recursive path.
    *   `nums`: The original sorted input array.
    *   `current_index`: The index of the element in `nums` we are currently considering.
3.  **Base Case**:
    *   If `current_index` reaches the end of the `nums` array (`current_index == nums.length`), it means we have considered all elements. The `current_subset` is now complete. We add a *copy* of `current_subset` to our final list of results. This is important because `current_subset` will be modified in further recursive calls.
4.  **Recursive Steps**:
    *   **Choice 1: Include `nums[current_index]`**
        *   Create a new list (`lis1`) by copying `current_subset` and adding `nums[current_index]` to it.
        *   Make a recursive call: `combination(lis1, nums, current_index + 1)`. This explores subsets that contain `nums[current_index]`.
    *   **Choice 2: Exclude `nums[current_index]` (and its duplicates)**
        *   To avoid duplicate subsets, if we decide to exclude `nums[current_index]`, we must also skip all subsequent elements that are identical to `nums[current_index]`.
        *   Use a `while` loop to find the `next_index` that points to the *first element different from `nums[current_index]`* (or beyond the array bounds if all remaining elements are duplicates).
        *   Create a new list (`lis2`) by copying `current_subset` (without adding `nums[current_index]`).
        *   Make a recursive call: `combination(lis2, nums, next_index + 1)`. This explores subsets that *do not* contain `nums[current_index]` or its duplicates.
5.  **Initial Call**:
    *   From the main `subsetsWithDup` function, after sorting, initiate the recursion by calling `combination` with an empty `ArrayList`, the sorted `nums` array, and a starting `current_index` of `0`.

## Dry Run
Let's trace with `nums = [1,2,2]`.
1.  `Arrays.sort(nums)` -> `nums` is `[1,2,2]`.
2.  Call `subsetsWithDup` -> `combination(new ArrayList<>(), [1,2,2], 0)`

**`combination([], [1,2,2], 0)`** (Considering `nums[0] = 1`)
  *   `curr = 0`, `nums[curr] = 1`.
  *   **Include 1**:
      *   `lis1 = [1]`
      *   Call `combination([1], [1,2,2], 1)`
  *   **Exclude 1**:
      *   `index` for `nums[0]=1` will be `0` (no duplicates after it). `next_index = 0`.
      *   `lis2 = []`
      *   Call `combination([], [1,2,2], 1)`

Now, let's trace the first branch: **`combination([1], [1,2,2], 1)`** (Considering `nums[1] = 2`)
  *   `curr = 1`, `nums[curr] = 2`.
  *   **Include 2**:
      *   `lis1 = [1,2]`
      *   Call `combination([1,2], [1,2,2], 2)`
  *   **Exclude 2**:
      *   `index` for `nums[1]=2` will advance to `2` because `nums[1] == nums[2]`. `next_index = 2`.
      *   `lis2 = [1]`
      *   Call `combination([1], [1,2,2], 3)`

Tracing **`combination([1,2], [1,2,2], 2)`** (Considering `nums[2] = 2`)
  *   `curr = 2`, `nums[curr] = 2`.
  *   **Include 2**:
      *   `lis1 = [1,2,2]`
      *   Call `combination([1,2,2], [1,2,2], 3)`
          *   `curr = 3 == nums.length`. Base case. Add `[1,2,2]` to `ans`. Returns `[[1,2,2]]`.
  *   **Exclude 2**:
      *   `index` for `nums[2]=2` will be `2` (no more duplicates after it). `next_index = 2`.
      *   `lis2 = [1,2]`
      *   Call `combination([1,2], [1,2,2], 3)`
          *   `curr = 3 == nums.length`. Base case. Add `[1,2]` to `ans`. Returns `[[1,2]]`.
  *   Returns `[[1,2,2], [1,2]]`.

Tracing **`combination([1], [1,2,2], 3)`**
  *   `curr = 3 == nums.length`. Base case. Add `[1]` to `ans`. Returns `[[1]]`.

Combining results from the first branch of `combination([1], [1,2,2], 1)`: `[[1,2,2], [1,2], [1]]`.

Now, let's trace the second main branch: **`combination([], [1,2,2], 1)`** (Considering `nums[1] = 2`)
  *   `curr = 1`, `nums[curr] = 2`.
  *   **Include 2**:
      *   `lis1 = [2]`
      *   Call `combination([2], [1,2,2], 2)`
  *   **Exclude 2**:
      *   `index` for `nums[1]=2` will advance to `2` because `nums[1] == nums[2]`. `next_index = 2`.
      *   `lis2 = []`
      *   Call `combination([], [1,2,2], 3)`

Tracing **`combination([2], [1,2,2], 2)`** (Considering `nums[2] = 2`)
  *   `curr = 2`, `nums[curr] = 2`.
  *   **Include 2**:
      *   `lis1 = [2,2]`
      *   Call `combination([2,2], [1,2,2], 3)`
          *   `curr = 3 == nums.length`. Base case. Add `[2,2]` to `ans`. Returns `[[2,2]]`.
  *   **Exclude 2**:
      *   `index` for `nums[2]=2` will be `2`. `next_index = 2`.
      *   `lis2 = [2]`
      *   Call `combination([2], [1,2,2], 3)`
          *   `curr = 3 == nums.length`. Base case. Add `[2]` to `ans`. Returns `[[2]]`.
  *   Returns `[[2,2], [2]]`.

Tracing **`combination([], [1,2,2], 3)`**
  *   `curr = 3 == nums.length`. Base case. Add `[]` to `ans`. Returns `[[]]`.

Combining results from the second main branch of `combination([], [1,2,2], 0)`: `[[2,2], [2], []]`.

Finally, all results combined (order may vary): `[[], [1], [2], [1,2], [2,2], [1,2,2]]`. This matches the example output!

## ⏱ Complexity
*   **Time Complexity**: O(N * 2^N)
    *   There are `2^N` possible subsets for an array of `N` elements (in the worst case where all elements are unique).
    *   For each subset found, we perform operations like creating a new `ArrayList` and adding elements, which takes O(N) time (where N is the maximum length of a subset).
    *   The initial sorting takes O(N log N), but for N up to 10, the exponential part dominates.
*   **Space Complexity**: O(N * 2^N)
    *   The primary space consumption is for storing the `2^N` subsets in the result list. Each subset can have up to `N` elements.
    *   The recursion stack depth can go up to `N`.
    *   Intermediate `ArrayList` copies (`lis1`, `lis2`) are created at each step, contributing to space.

## Code
```java
class Solution {

    public static List<List<Integer>> combination(List<Integer> p, int []nums, int curr) {
        List<List<Integer>> ans = new ArrayList<>();
        // Base case: If we have considered all elements
        if(curr == nums.length) {
            // Add a copy of the current subset 'p' to the result list
            ans.add(new ArrayList<>(p)); // IMPORTANT: Add a new ArrayList to avoid modification issues
        } else {
            // Choice 1: Include the current element nums[curr]
            List<Integer> lis1 = new ArrayList<>(p);
            lis1.add(nums[curr]);
            ans.addAll(combination(lis1, nums, curr + 1));

            // Choice 2: Exclude the current element nums[curr]
            // Skip all duplicate elements to avoid redundant subsets
            int index = curr;
            while(index < nums.length - 1 && nums[index] == nums[index + 1]) {
                index++;
            }
            // Recursively call, skipping to the next distinct element
            List<Integer> lis2 = new ArrayList<>(p); // lis2 is 'p' without nums[curr]
            ans.addAll(combination(lis2, nums, index + 1));
        }
        return ans;
    }

    public List<List<Integer>> subsetsWithDup(int[] nums) {
        // Sort the array to group duplicate elements together
        Arrays.sort(nums);
        // Start the recursion with an empty list, the sorted array, and starting index 0
        return combination(new ArrayList<Integer>(), nums, 0);
    }
}
```

## Edge Cases
*   **Array with a single element**: e.g., `nums = [0]`. The algorithm correctly generates `[[], [0]]`.
*   **Array with all duplicate elements**: e.g., `nums = [2,2,2]`. The sorting and duplicate-skipping logic ensures unique subsets like `[[], [2], [2,2], [2,2,2]]` are generated.
*   **Constraints**: The problem states `1 <= nums.length`, so an empty input array is not an edge case to consider based on constraints. The constraints on `nums[i]` (`-10 <= nums[i] <= 10`) are also small, not affecting the algorithm logic but indicating small integer values.