
# Sliding Window Technique

The **Sliding Window** algorithm is a subset of the two-pointer technique. It is primarily used to solve problems involving arrays or strings where you need to find a contiguous subarray or substring that satisfies a specific condition (e.g., longest, shortest, or specific sum).

It works by maintaining a "window" of elements—which can be of fixed or variable size—and sliding it across the data structure to avoid redundant calculations.

## 1. Fixed-Size Sliding Window

In a fixed-size sliding window, the size of the window (`k`) remains constant throughout the traversal. 

**Idiomatic Rust Note:** Rust makes fixed-size windows incredibly ergonomic through the standard library's `slice::windows()` method, which returns an iterator over all contiguous windows of length `size`.

### Example: Maximum Sum Subarray of Size K

**Problem:** Given an array of integers and a number `k`, find the maximum sum of a contiguous subarray of size `k`.

```rust
/// Finds the maximum sum of any contiguous subarray of size `k`.
/// Uses Rust's built-in `windows` iterator for an idiomatic and safe approach.
pub fn max_sum_fixed_window(arr: &[i32], k: usize) -> Option<i32> {
    if k == 0 || k > arr.len() {
        return None;
    }

    arr.windows(k)
        .map(|window| window.iter().sum())
        .max()
}

```

*Note: While `windows()` recalculates the sum from scratch for each window, it is often fast enough for small `k` and highly readable. For very large `k` where performance is critical, a manual sliding sum (subtracting the outgoing element and adding the incoming element) is preferred.*

### Example: Manual Fixed Window (O(N) Optimization)

```rust
pub fn max_sum_fixed_manual(arr: &[i32], k: usize) -> Option<i32> {
    if k == 0 || k > arr.len() {
        return None;
    }

    // Calculate the sum of the first window
    let mut current_sum: i32 = arr.iter().take(k).sum();
    let mut max_sum = current_sum;

    // Slide the window by subtracting the left-most element and adding the right-most
    for i in k..arr.len() {
        current_sum = current_sum - arr[i - k] + arr[i];
        max_sum = max_sum.max(current_sum);
    }

    Some(max_sum)
}

```

---

## 2. Variable-Size Sliding Window

In a variable-size window, the window expands and contracts dynamically based on a specific condition. You typically use two pointers (`left` and `right`) to represent the bounds of the window.

**General Pattern:**

1. Expand the window by moving the `right` pointer and updating the state.
2. If the window condition is violated, contract the window by moving the `left` pointer until the condition is satisfied again.
3. Update your target metric (e.g., max length, min length) at the appropriate time.

### Example: Minimum Size Subarray Sum

**Problem:** Find the minimal length of a contiguous subarray of which the sum is greater than or equal to `target`.

```rust
/// Finds the minimal length of a subarray whose sum is >= target.
pub fn min_subarray_len(target: i32, nums: &[i32]) -> usize {
    let mut min_len = usize::MAX;
    let mut left = 0;
    let mut current_sum = 0;

    for right in 0..nums.len() {
        current_sum += nums[right];

        // Shrink the window as long as the condition is met
        while current_sum >= target {
            min_len = min_len.min(right - left + 1);
            current_sum -= nums[left];
            left += 1; // Contract from the left
        }
    }

    if min_len == usize::MAX {
        0
    } else {
        min_len
    }
}

```

### Example: Longest Substring Without Repeating Characters

**Problem:** Find the length of the longest substring without repeating characters.

```rust
use std::collections::HashMap;

/// Finds the length of the longest substring without repeating characters.
pub fn length_of_longest_substring(s: &str) -> usize {
    let mut char_index_map = HashMap::new();
    let mut max_len = 0;
    let mut left = 0;

    for (right, ch) in s.char_indices() {
        // If the character is found and its index is within the current window,
        // move the left pointer past the duplicate.
        if let Some(&prev_index) = char_index_map.get(&ch) {
            if prev_index >= left {
                left = prev_index + 1;
            }
        }

        char_index_map.insert(ch, right);
        max_len = max_len.max(right - left + 1);
    }

    max_len
}

```

## When to use Sliding Window

Consider using this pattern if the problem asks for:

* **Contiguous sequences:** Subarrays, substrings, or consecutive elements.
* **Min/Max/Longest/Shortest:** Finding an optimal contiguous segment that meets a requirement.
* **Running statistics:** Calculating sums, averages, or character frequencies over a sequence.


