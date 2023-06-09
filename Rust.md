---
title: Rust
created: '2023-06-09T17:33:40.303Z'
modified: '2023-06-09T18:09:36.849Z'
---

# Rust

- **Repeated Trait Bound**

  Say you're creating a custom type and doing a lot of `impl` blocks, doing operator overloading, etc. You'll likely need to repeat a set of trait bounds a lot. Here's a way to clean up your code and assign a kinda of `typedef` or alias to the collection of trait bounds.

  ``` rust
  pub trait ValidVecElement: Copy + Add<Output = Self> + Sub<Output = Self> + AddAssign + SubAssign {}
  ```
  Define a custom trait and give it a name. For any type to implement this trait, it must _**also**_ implement the listed traits.

  ``` rust
  impl<T: Copy + Add<Output = Self> + Sub<Output = Self> + AddAssign + SubAssign> ValidVecElement for T {}
  ```
  Any type that implements `Copy`, `Add`, `Sub`, `AddAssign`, `SubAssign` _**also**_ implements `ValidVecElement`.

  Then, we can define a struct in a clean way:

  ``` rust
  pub struct Vec2D<T: ValidVecElement> {
    e: [T; 2]
  }
  ```
  Valid elements of Vec2D are now only those that implement the `ValidVecElement` trait.
  We defined earlier that any types that implement `Copy`, `Add`, `Sub`, `AddAssign`, `SubAssign` also automatically implement `ValidVecElement`.
