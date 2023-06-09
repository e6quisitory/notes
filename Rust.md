---
title: Rust
created: '2023-06-09T17:33:40.303Z'
modified: '2023-06-09T20:42:25.960Z'
---

# Rust

- **Repeated Trait Bound**

  Say you're creating a custom type and doing a lot of `impl` blocks, doing operator overloading, etc. You'll likely need to repeat a set of trait bounds a lot. Here's a way to clean up your code and assign a kinda of `typedef` or alias to the collection of trait bounds.

  1. Define a custom trait and give it a name. For any type to implement this trait, it must _**also**_ implement the listed traits.

        ``` rust
        pub trait ValidVecElement: Copy + Add<Output = Self> + Sub<Output = Self> + AddAssign + SubAssign {}
        ```
  2. Any type that implements `Copy`, `Add`, `Sub`, `AddAssign`, `SubAssign` _**also**_ implements `ValidVecElement`.
       
        ``` rust
        impl<T: Copy + Add<Output = Self> + Sub<Output = Self> + AddAssign + SubAssign> ValidVecElement for T {}
        ```
  A diagram to illustrate:
  
  <img src="https://github.com/e6quisitory/wolf3d-clone/assets/25702188/2fb7c69d-e3e3-4088-8386-e7db2486dced" width = 700/>

  Then, we can define a struct in a clean way:

  ``` rust
  pub struct Vec2D<T: ValidVecElement> {
    e: [T; 2]
  }
  ```
  Valid elements of `Vec2D` are now only those that implement the `ValidVecElement` trait.


  ## Current Questions

  - What are trait objects, what is `dyn`?
  
  - Wtf does `~const Index<Idx>` mean from the following trait definition for `IndexMut` in `std::ops`?

    ``` rust
    pub trait IndexMut<Idx: ?Sized>: ~const Index<Idx> {
      fn index_mut(&mut self, index: Idx) -> &mut Self::Output;
      }
    ```
  

