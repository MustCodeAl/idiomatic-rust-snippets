

# Two-Pointer Technique

The **Two-Pointer** technique is an algorithmic pattern that uses two indices (or pointers) to traverse a data structure—typically an array, string, or linked list. By moving these pointers based on specific conditions, you can avoid nested loops, often reducing $O(N^2)$ time complexity to $O(N)$.

While Rust highly encourages the use of Iterators for traversal, the two-pointer approach generally requires direct index manipulation (`usize`) when dealing with slices or arrays.

## Common Patterns

### 1. Opposite Ends (Inward Traversal)
One pointer starts at the beginning (`left = 0`) and the other at the end (`right = len - 1`). They move towards each other until they meet or cross. This is extremely common for searching in sorted arrays or reversing structures.

#### Example: Two Sum in a Sorted Array
**Problem:** Given a *sorted* array of integers, find two numbers that add up to a specific `target`.

```rust
/// Finds two numbers in a sorted array that sum up to `target`.
/// Returns their indices, or None if no such pair exists.
pub fn two_sum_sorted(arr: &[i32], target: i32) -> Option<(usize, usize)> {
    if arr.is_empty() {
        return None;
    }

    let mut left = 0;
    let mut right = arr.len() - 1;

    while left < right {
        let sum = arr[left] + arr[right];

        if sum == target {
            return Some((left, right));
        } else if sum < target {
            left += 1; // Need a larger sum, move left pointer right
        } else {
            right -= 1; // Need a smaller sum, move right pointer left
        }
    }

    None
}

```

### 2. Same Direction (Iterate and Replace/Read)

Both pointers start at the beginning. One pointer iterates quickly through the entire collection (the "reader"), while the other pointer moves slowly, keeping track of where to write the next valid element (the "writer").

#### Example: Remove Duplicates from Sorted Array

**Problem:** Remove duplicates from a sorted array in-place such that each element appears only once, returning the new length.

```rust
/// Removes duplicates from a sorted array in-place and returns the new length.
pub fn remove_duplicates(arr: &mut [i32]) -> usize {
    if arr.is_empty() {
        return 0;
    }

    // `unique_idx` keeps track of the last known unique element
    let mut unique_idx = 0;

    for i in 1..arr.len() {
        if arr[i] != arr[unique_idx] {
            unique_idx += 1;
            arr[unique_idx] = arr[i];
        }
    }

    unique_idx + 1
}

```

### 3. Fast and Slow Pointers (Cycle Detection)

Also known as the "hare and tortoise" algorithm. Two pointers move in the same direction but at different speeds (e.g., one moves 1 step, the other moves 2 steps). This is heavily used in linked lists to detect cycles or find the midpoint.

*(Note: In Rust, implementing standard linked list algorithms with multiple mutable pointers runs into the borrow checker by design. You'll often use `Option<Box<Node>>` or `Rc<RefCell<Node>>`, but the conceptual logic of advancing pointers remains the same).*

---

## Idiomatic Rust Tips for Two-Pointers

* **Prefer `slice::swap`:** If you are using two pointers to swap elements (like reversing an array or partitioning), always use Rust's built-in `slice::swap(i, j)`. It correctly handles the underlying memory and avoids borrowing conflicts.
```rust
pub fn reverse_in_place<T>(arr: &mut [T]) {
    let mut left = 0;
    let mut right = arr.len().saturating_sub(1);

    while left < right {
        arr.swap(left, right);
        left += 1;
        right -= 1;
    }
}

```


*(Note: `arr.reverse()` is built into the standard library and is always preferred for this specific task, but this illustrates the pattern safely).*
* **Iterator Alternatives:** Before writing a manual while loop with indices, check if an iterator can solve it cleanly. For example, checking a palindrome is a classic two-pointer problem, but in Rust, it's beautifully expressed as:
```rust
pub fn is_palindrome(s: &str) -> bool {
    let chars = s.chars();
    chars.clone().eq(chars.rev())
}

```





