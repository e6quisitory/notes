---
title: Rust
created: '2023-06-09T17:33:40.303Z'
modified: '2023-06-09T21:53:03.479Z'
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
  - When should I implement the Copy (and Clone) traits on a custom type and when should I not?
      - If you implement them, then the ownership/burrowing model no longer applies for that type. Obviously this is good for scalar types.
      - For custom types that allocate on the heap, you obviously _do not_ want to implement Copy. And I don't think Rust even lets you.
      - However, what about custom types that do not allocate on heap at all; pure stack. Should I implement copy? Forget performance reasons--I'm keenly interested in whether there are any memory safety problems that can pop up if I do this.
    
    Currently for my Vec2D custom type, I haven't implemented the Copy/Clone traits. I wanna see if I'll actually end up needing them. If not, I think I won't implement them. Will perhaps make the code more rigid and safe, less error-prone.
  

