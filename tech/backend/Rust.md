#Basic data types in Rust
* Booleans
* Characters
* Integers
* Floats

## Memory representation

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
