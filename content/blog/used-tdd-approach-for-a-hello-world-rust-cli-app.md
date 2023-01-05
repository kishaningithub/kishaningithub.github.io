+++
title = "Used TDD Approach for a Hello World Rust CLI App"
description = ""
date = 2022-06-26T15:12:24+05:30
tags = ["rust", "tdd"]
categories = []
+++

For the first time ever in my life i wrote hello world application using [TDD (Test driven development)](https://en.wikipedia.org/wiki/Test-driven_development). Don't get me wrong here, i have practiced TDD before for larger applications but never have i done it for an hello world app.

## Problem

Write an CLI application that prints out "Hello TDD world!" in STDOUT.

## How did i do it?

- I created a project using `cargo new hello-tdd-world`
- I added a dependency `assert_cmd` which lets us test out outputs of any binary program
- Created a file `tests/cli.rs`  and I wrote the following test
```rust
use assert_cmd::Command;

#[test]
fn test_cli_app_should_print_hello_tdd_world() {
    let mut cmd = Command::cargo_bin("hello-world-tdd").unwrap();
    cmd.assert().success().stdout("Hello TDD world!\n");
}
```
- Run the test using command `cargo test`
- The above test will fail saying the following, this happens because the cargo new had generated a `src/main.rs` file which printed out "Hello world!"
```log
running 1 test
test test_cli_app_should_print_hello_tdd_world ... FAILED

failures:

---- test_cli_app_should_print_hello_tdd_world stdout ----
thread 'test_cli_app_should_print_hello_tdd_world' panicked at 'Unexpected stdout, failed diff original var
├── original: Hello TDD world!
├── diff:
│   --- 	orig
│   +++ 	var
│   @@ -1 +1 @@
│   -Hello TDD world!
│   +Hello world!
└── var as str: Hello world!
```
- Now that we have a failing test(RED), the next job is to make it green by modifying the contents of the println statement.
- Run `cargo test` again, The test now passes (Green)

The code for this is available at https://github.com/kishaningithub/hello-world-tdd

## Links

- [Command line rust OReilly book](https://learning.oreilly.com/library/view/command-line-rust/9781098109424/preface01.html#idm45828825384320)
