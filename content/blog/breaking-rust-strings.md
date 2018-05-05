+++
title = "Breaking rust strings"
description = "Explore the edge cases of rust lang string implementation"
tags = [
    "rust",
]
date = "2018-05-05"
categories = [
    "programming"
]
+++

```rust
fn main() {
    let chinese_text = "可通過每頁左上角的連結隨時調整";
    let chinese_text_slice = &chinese_text[0..1]; // Focus here
    println!("{}", chinese_text_slice);
}
```

In the above code i want to extract the first charecter out which is 可.
Since the end indices are exclusive in rust i put forth a slice `0..1` on `chinese_text`.

If you expected the output to be 可. You are in for a surprise!

```bash
break_strings$ RUST_BACKTRACE=1 cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/break_strings`
thread 'main' panicked at 'byte index 1 is not a char boundary; it is inside '可' (bytes 0..3) of `可通過每頁左上角的連結隨時調整`', src/libcore/str/mod.rs:2234:5
stack backtrace:
   0: std::sys::unix::backtrace::tracing::imp::unwind_backtrace
             at src/libstd/sys/unix/backtrace/tracing/gcc_s.rs:49
   1: std::panicking::default_hook::{{closure}}
             at src/libstd/sys_common/backtrace.rs:68
             at src/libstd/sys_common/backtrace.rs:57
             at src/libstd/panicking.rs:381
  << Blah blah blah>>
  17: std::sys_common::bytestring::debug_fmt_bytestring
             at src/libstd/panicking.rs:459
             at src/libstd/panic.rs:365
             at src/libstd/rt.rs:58
  18: std::rt::lang_start
             at /Users/travis/build/rust-lang/rust/src/libstd/rt.rs:74
  19: break_strings::main
```

The worse part is this is a run time error and the back trace does
not even give the exact line number. 😓

The above was a simple 3 liner consider the same happening in a bigger project and say that the string slice
is extracted from a JSON REST response which gave you chinese where you were expecting ASCII and you cant know which line this error occured. What will you do? Will you ditch slices altogether in rust? I leave it to you...

# Why did this happen

The error message says 'byte index 1 is not a char boundary; it is inside '可' (bytes 0..3) of '可通過每頁左上角的連結隨時調整'. In rust the strings indices are actually byte indices and a that a single charecter can occupy multiple bytes. In this case the charecter 可 requires 3 bytes to for storage. So, when you are creating a string slice its up you to make sure the start and end byte index are actually char indexes 😄. Good luck with that! 😄

# The fix

Since the 可 requires 3 bytes for storage we should take 3 bytes out.

```rust
fn main() {
    let chinese_text = "可通過每頁左上角的連結隨時調整";
    let chinese_text_slice = &chinese_text[0..3]; // Focus here
    println!("{}", chinese_text_slice);
}
```

The slice `0..3` refers byte `0,1,2`

```bash
break_strings$ RUST_BACKTRACE=1 cargo run
   Compiling break_strings v0.1.0 (file:///Users/kishanb/Programming/Personal/learn-rust/break_strings)
    Finished dev [unoptimized + debuginfo] target(s) in 0.47 secs
     Running `target/debug/break_strings`
可
```

Now your program works!