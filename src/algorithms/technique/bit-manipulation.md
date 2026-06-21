# Bit Manipulation

Bit manipulation involves operating directly on the binary representations of numbers. It is incredibly fast, memory-efficient, and often essential for systems programming, cryptography, and optimizing specific algorithmic bottlenecks.

## Core Bitwise Operators in Rust

* `&` (AND): `1` if both bits are `1`, otherwise `0`.
* `|` (OR): `1` if at least one bit is `1`, otherwise `0`.
* `^` (XOR): `1` if the bits are different, `0` if they are the same.
* `!` (NOT): Flips all bits (Note: Rust uses `!` instead of `~` for bitwise complement).
* `<<` (Left Shift): Shifts bits left, filling with `0`s (equivalent to multiplying by a power of 2).
* `>>` (Right Shift): Shifts bits right (equivalent to dividing by a power of 2). Sign-extends for signed integers (`i32`), zero-fills for unsigned integers (`u32`).

---

## 1. Idiomatic Rust: The Standard Library is Your Friend

Before writing clever bit-twiddling hacks, always check if Rust's standard library integer types (`u32`, `i32`, `u8`, etc.) already have a built-in method. These methods are highly optimized and often compile down to single hardware instructions.

```rust
pub fn idiomatic_bit_methods() {
    let n: u32 = 0b1011_0010;

    // Counts the number of 1 bits (Hamming weight)
    assert_eq!(n.count_ones(), 4); 
    
    // Counts the number of 0 bits
    assert_eq!(n.count_zeros(), 28); // u32 has 32 bits total

    // Checks if a number is a power of 2
    assert_eq!(16u32.is_power_of_two(), true);
    
    // Finds the number of trailing/leading zeros
    assert_eq!(8u32.trailing_zeros(), 3); // 8 is 1000 in binary
}

```

---

## 2. Common Bitmasking Patterns

When you do need to manipulate specific bits manually, you typically use a "mask" (a specific binary number) combined with a bitwise operator.

```rust
/// Demonstrates common bit manipulations on an unsigned 8-bit integer.
pub fn bit_tricks(mut n: u8, i: usize) -> u8 {
    // 1. Check if a number is even or odd
    let is_odd = (n & 1) == 1;

    // 2. Set the i-th bit to 1
    n |= 1 << i;

    // 3. Clear the i-th bit to 0
    n &= !(1 << i);

    // 4. Toggle the i-th bit (flip 1 to 0, or 0 to 1)
    n ^= 1 << i;

    // 5. Check if the i-th bit is set
    let is_set = (n & (1 << i)) != 0;

    n
}

```

---

## 3. Classic Algorithmic Tricks

### Example: Find the Single Number (XOR Trick)

**Problem:** Given an array where every element appears exactly twice except for one, find that single element.
**Concept:** `A ^ A = 0` and `A ^ 0 = A`. If you XOR all numbers together, the pairs cancel each other out, leaving only the single number.

```rust
/// Finds the single non-repeating number using XOR.
/// Uses `fold` for an idiomatic functional approach.
pub fn single_number(nums: &[i32]) -> i32 {
    nums.iter().fold(0, |acc, &x| acc ^ x)
}

```

### Example: Brian Kernighan's Algorithm

**Problem:** Count the number of set bits (`1`s) in an integer (if you couldn't use `.count_ones()`).
**Concept:** The operation `n & (n - 1)` always drops the lowest set bit of `n`.

```rust
/// Calculates the Hamming weight manually by clearing the lowest set bit 
/// in each iteration until the number becomes 0.
pub fn manual_hamming_weight(mut n: u32) -> u32 {
    let mut count = 0;
    
    while n != 0 {
        n &= n - 1; // Clears the least significant bit that is 1
        count += 1;
    }
    
    count
}

```

## When to Use Bit Manipulation

Consider using this pattern if the problem involves:

* **Flags/State:** Packing multiple boolean values into a single integer to save memory.
* **Subsets:** Representing sets or finding all combinations (e.g., iterating from `0` to `2^N` where each bit represents inclusion).
* **Mathematical optimization:** Fast multiplication/division by powers of 2, or specific mathematical properties (like powers of 2).
