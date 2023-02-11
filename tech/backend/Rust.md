##Basic data types in Rust
* Booleans
* Characters
* Integers
* Floats

### Memory representation

`u` means it is an unsigned integer and `i` means it is a signed integer.
A signed integer can only be positive and an unsigned integer can be positive or negative.
The number indicates the size of the integer in bit.

* u8, i8 ()
* u16, i16 ()
* u32, i32 ()
* u64, i64 ()
* u128, i128 ()

There are two special numbers in Rust: `usize` and `isize`, which are architecture dependant types, so they are either 32bit or 64bit.
We also have floating point numbers in Rust: `f32` and `f64`.
There is also the `bool` type which can store two possible values and it is one byte in size.
The last type is `char` which is always 4 bytes.

## Concept of ownership in Rust
There are 3 ownership rules in Rust:
* Each value in Rust is owned by a variable
* When the owner goes out of scope, the value will be deallocated
* There can only be ONE owner at a time.

Let's look at an example:
We create a new string:
```
let mut input = String::new();
```
This string is owned by the varianle named `input`. The string lives on the heap as we don't know its size at compile time. On the stack we just store a pointer to the memory address on the heap. 
When now the `input` goes out of scope, the string (heap) gets deallocated. This is what we call a smart pointer, so the string is a smart pointer.
The Rust compiler will invoke the `drop` function on the string when the function ends.
To understand the third rule, let's create another variable:
```
let mut s = input;
```
Now our input points to our string and our variable s also points to our string.
The problem with this is that if we follow rule nr. 2 and and it gets out of scope, we have two owners of our string. 
when they both go out of scope, they will try to deallocate our string. This is called a double free and it can lead to 
memory corruption. It is a common bug in C programms. It has led to serious security vulnerabilities.
So by guaranteeing a single owner, it eliminates the possibility of a double free.
In our situatuion the ownershio of the string is moved to the variable s. So `input` is no longer valid and the compiler wont let us use it.

If we had the same situation but instead othe input string just normal numbers.
Then the size would be known at compile time and therfore it would be on the stack:
```
let a = 5;
let b = a;
```
Here we would have two 5's and not a pointer (the value itself is copied!).

The same three rules are valid for function calls:
```
let mut input = String::new();
some_fn(input);
io::stdin().read_line(&mut input); <-- error
```
When calling the function we moved the ownership of our string to the parameter of our function.
And when the parameter goes out of scope, the string will be deallocated.
That kind of means that we cant really pass parameters to functions and then keep using them after this function 
returns. That sounds like a huge issue but Rust has a feature that is called `borrowing`.

## Borrowing
References allow us to pass variables to functions without transfering ownership:
```
fn some_fn(s: &String) {}
```
Like variables, references are immutable by default. We need to explicitly say that it is a mutable reference:
```
fn some_fn(s: &mut String) {}
```

There is one important rule for references:
We can have as many immutable references in the same scope as we want OR a single mutable reference.
The benefit of all that is that Rust prevents data races at compile time.





