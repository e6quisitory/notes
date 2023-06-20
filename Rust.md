---
title: Rust
created: '2023-06-09T17:33:40.303Z'
modified: '2023-06-20T00:38:57.450Z'
---

# Rust

## Table of Contents
- [Repeated trait bound](#repeated_trait_bound)
- [Error handling](#error_handling)
- [Match Statement](#match_statement)
- [Current questions](#current_questions)

<a name="repeated_trait_bound"/>

## Repeated Trait Bound
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

<a name="error_handling"/>

## Error Handling
1. **`Result` Enum**

    ``` rust
    enum Result<T, E> {
      Ok(T),
      Err<E>
    }
    ```

2. **`Option` Enum**

    ``` rust
    enum Option<T> {
      Some(T),
      None
    }
    ```

For both of these enums, you have the following two methods

  - **`unwrap()`**

    Extracts `Ok` or `Some` from `Result` or `Option` enum. If enum is an `Err` or `None` variant instead, the program panics / crashes. This is meant to catch errors.
    So, when you call `unwrap()`, you're essentially saying you expect `Ok` or `Some`, and anything else is unexpected behaviour.
  
  - **`expect("Error message")`**

    Extracts `Ok` or `Some` if present. If not present (i.e. `Err` or `None` is present), program will panic / crash and the error message you passed in will be displayed.

For the `Result` enum exclusively, you also have:

  - **`?` operator**

    Used inside functions that return a `Result` type. Used to propagate error (`Err`) early, i.e. return immediately when doing an error prone sub-calculation in the function.

<a name="match_statement"/>

## Match Statement

Default with a `_`
``` rust
let number = 7;

match number {
    1 => println!("One"),
    2 => println!("Two"),
    _ => println!("Other") // default
}
```

Can even have entire code blocks in there
``` rust
let number = 7;

match number {
    1 => {
            let x = 1;
            println!("{:?}", x);
            println!("One!");
          },
    2 => println!("Two"),
    _ => println!("Other")
}
```


<a name="current_questions"/>

## Current Questions

- In the following code, what is `impl` doing in function args? (taken from ggez lib sources)

  ``` rust
  pub fn draw(&mut self, drawable: &impl Drawable, param: impl Into<DrawParam>) {
      drawable.draw(self, param)
  }
  ```

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

- Iterators

- Lifetimes (i.e. this syntax `'running`)
  

