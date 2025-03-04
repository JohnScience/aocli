# aocli

[![Crates.io](https://img.shields.io/crates/v/aocli)](https://crates.io/crates/aocli)
[![Downloads](https://img.shields.io/crates/d/aocli.svg)](https://crates.io/crates/aocli)
[![License](https://img.shields.io/crates/l/aocli)](https://crates.io/crates/aocli)
[![Dependency Status](https://deps.rs/repo/github/sncxyz/aocli/status.svg)](https://deps.rs/repo/github/sncxyz/aocli)

A helper CLI tool for solving [Advent of Code](https://adventofcode.com) in Rust.
Uses [aoclib](https://github.com/sncxyz/aoclib) to parse inputs and run solutions.

## Installation

```console
cargo install aocli
```

The binary name for aocli is `aoc`.

## Requirements

- `cargo` is in PATH
- terminal with ANSI colour support

## Features

- download puzzle inputs and past puzzle answers
- submit puzzle answers
- cache puzzle answers locally for testing
- run solutions on many different inputs
- run solutions to all days, or specified days, of a year
- time solutions
- parse puzzle inputs with aoclib
- quickly view your progress
- project management (create crates and files)
- minimal source files and compile times for each day
- open year or day webpage in browser

## Example

![screenshot of the command line](example.png)

## Functionality

A solution workspace consists of the root directory, year directories, and day directories.
For example:

```text
aoc-solutions
├── aoc-root
├── .session
├── .gitignore
├── 2015
│   ├── 01
│   └── 02
├── 2016
│   └── 01
└── 2017
    ├── 01
    ├── 02
    └── 03
```

Here, the root directory is called `aoc-solutions` and contains year directories `2015`, `2016` and `2017`, which each contain various day directories.
The path to 2015 day 1 is `/aoc-solutions/2015/01` and the path to 2017 day 3 is `/aoc-solutions/2017/03`.
Each day directory is a regular Rust binary crate.
The `aoc-root` file denotes the root of a solution workspace, and the `.session` file contains the session cookie to log into the Advent of Code site.

aocli commands may work from within any of the three directory levels, and the required arguments and functionality may differ for each level.
For example, the following three commands will do the same thing:

```text
/aoc-solutions > aoc run 15 1
/aoc-solutions/2015 > aoc run 1
/aoc-solutions/2015/01 > aoc run
```

while the following two commands will run every solution for the year 2015 instead of just day 1:

```text
/aoc-solutions > aoc run 15
/aoc-solutions/2015 > aoc run
```

## Solutions

A solution to a day consists of a call to `aoc::parts!` from aoclib and a function for each solved part.

The input is passed in as an instance of `aoc::Input` from aoclib. See the docs for more information on how to use it.

For example:

```rust
use aoc::{Input, Parse};

aoc::parts!(1, 2);

fn part_1(input: Input) -> impl ToString {
    // solution to part 1
}

fn part_2(input: Input) -> impl ToString {
    // solution to part 2
}
```

However, `aoc::parts!()`, `aoc::parts!(1)` or `aoc::parts!(2)` may instead be used depending on which parts are implemented.
The functions can return any type that is `ToString`, and the conversion to a `String` is not included in the timing of the solution.

## Inputs

In addition to being a Rust binary crate for the solution, each day directory contains the day's puzzle input(s) and corresponding puzzle answers.
Your actual puzzle input is named `actual`, but you may create other inputs and answers, such as for the examples on the site.

The file system structure looks like this:

```text
DAY
├── Cargo.toml
├── src/main.rs
└── data
    ├── actual
    ├── example1
    └── example2
```

while each input directory contains the puzzle input and answers:

```text
example1
├── input
├── 1
│   └── answer
└── 2
    └── answer
```

Empty or non-existent answer files are taken to mean that the answer is unknown.

`add example1` would create the directories and empty files for `example1` as above, to be filled in as desired.

`get` would create and fill in the `actual` input directory from the website.

`run example1` would run the solution using the input `example1`.

`run` defaults to using the input `actual`.

## Interpreting the output

`run`, `debug`, `test` and `submit` display puzzle answers colour-coded.
Green means correct, red means incorrect, and yellow means the correct answer is not known.

## Commands

Note that parameters surrounded by `<>` are **required**, while those surrounded by `[]` are **optional**.

### `init`

Initialises a solution workspace in the current directory. For example:

```text
/aoc-solutions > aoc init
```

will set up a solution workspace with `aoc-solutions` as the root directory, and it will attempt to call `git init`.

In order to use the network features of aocli (`get` and `submit`), you must paste your session cookie into the `.session` file created by this command. You can do so by running `aoc-session > .session` in the `/aoc-solutions` directory. See [`aoc-session`](https://crates.io/crates/aoc-session) crate.

<details>
  <summary>About the format of <code>.session</code> file</summary>

  The contents of the `.session` file must match one of the following:

  - `<session-id>`, e.g.
  
  ```text
  53616c7465645f5f286128b614b18e3d5a906611b3bac6740671d5e461a7ab0d2be049fb7363eadb2e07ecfe51b600927ddd29b2311ea418ce366e8b9cf98dbb
  ```

  - `session=<session-id>`, e.g.
  
  ```text
  session=53616c7465645f5f286128b614b18e3d5a906611b3bac6740671d5e461a7ab0d2be049fb7363eadb2e07ecfe51b600927ddd29b2311ea418ce366e8b9cf98dbb
  ```

  - `<cookie-value>`, e.g.
  
  ```text
  _ga=GA1.2.1481191691.1701307337; _gid=GA1.2.2089724069.1701307337; session=53616c7465645f5f286128b614b18e3d5a906611b3bac6740671d5e461a7ab0d2be049fb7363eadb2e07ecfe51b600927ddd29b2311ea418ce366e8b9cf98dbb; _ga_MHSNPJKWC7=GS1.2.1701383119.5.1.1701383528.0.0.0
  ```

</details>

### `open`

```text
/root > aoc open <YEAR>
/root/YEAR > aoc open
```

```text
/root > aoc open <YEAR> <DAY>
/root/YEAR > aoc open <DAY>
/root/YEAR/DAY > aoc open
```

Opens the webpage for the year or day in the default browser using [webbrowser](https://crates.io/crates/webbrowser).

### `new`

```text
/root > aoc new <YEAR> <DAY>
/root/YEAR > aoc new <DAY>
```

Creates the directories, files and Rust crate for the solution to a new day of Advent of Code.

### `get`

```text
/root > aoc get <YEAR> <DAY>
/root/YEAR > aoc get <DAY>
/root/YEAR/DAY > aoc get
```

Downloads the puzzle input and any existing puzzle answers for the day from the website if they are not already in local files.

### `add`

```text
/root > aoc add <YEAR> <DAY> <INPUT>
/root/YEAR > aoc add <DAY> <INPUT>
/root/YEAR/DAY > aoc add <INPUT>
```

Creates the directories and empty files for a new puzzle input called \<INPUT\>.

The name of the input must be a valid directory name, and cannot be `1` or `2`.

### `clean`

```text
/root > aoc clean <YEAR>
/root/YEAR > aoc clean
```

```text
/root > aoc clean <YEAR> <DAY>
/root/YEAR > aoc clean <DAY>
/root/YEAR/DAY > aoc clean
```

Resets the input and answer files to empty files within the `actual` input of every day of the year, or the specified day, so that `get` can fill them in.

### `run`

```text
/root > aoc run <YEAR>
/root/YEAR > aoc run
```

Runs the solution to both parts of every day of the year with the `actual` puzzle input in release mode, providing total and average time statistics.

```text
/root > aoc run <YEAR> <DAY> [INPUT] [PART]
/root/YEAR > aoc run <DAY> [INPUT] [PART]
/root/YEAR/DAY > aoc run [INPUT] [PART]
```

Runs the solution to the day in release mode.

Defaults to running both parts using the `actual` input, but a different input, or a specific part, can be provided.

For example:

```text
/root/YEAR/DAY > aoc run example
```

to run both parts with input `example`,

```text
/root/YEAR/DAY > aoc run 1
```

to run just part 1 with input `actual`, and

```text
/root/YEAR/DAY > aoc run example 2
```

to run just part 2 with input `example`.

Note that the lack of flags here is why `1` and `2` are invalid names for inputs.

### `debug`

```text
/root > aoc debug <YEAR> <DAY> [INPUT] [PART]
/root/YEAR > aoc debug <DAY> [INPUT] [PART]
/root/YEAR/DAY > aoc debug [INPUT] [PART]
```

The same as `run`, except the solution is run in debug mode instead of release mode.

### `run days`

```text
/root > aoc run <YEAR> days <DAYS>
/root/YEAR > aoc run days <DAYS>
```

Runs the solution to both parts of the specified days of the year with the `actual` puzzle input in release mode, providing total and average time statistics.

The \<DAYS\> argument should be a sequence of space-separated terms, where each term is one of the following:

- a day, `X`
- an inclusive range of days, `X..Y`
- a negation of the above, `-X` or `-X..Y`

The start and end days in a range are optional, so `X..` is equivalent to `X..25` and `..Y` is equivalent to `1..Y`.

If the first term is a regular term, initially no days are included, and if the first term is a negated term, initially all days are included.
Terms are then applied in order, one by one. A regular term causes its day(s) to be included, and a negated term causes them to be excluded.

- `days 4 8` = `days 4..8 -5 -6 -7` = `days 4..8 -5..7` = `days ..8 -5..7 -..3`
- `days -3` = `days 1..25 -3` = `days 1 2 4..`
- `days -1 -25` = `days 2..24`

For example:

```text
/root > aoc run 2019 days -25
```

to run all but day 25 in 2019,

```text
/root/2021 > aoc run days -19
```

to run all but day 19 in 2021,

```text
/root/2022 > aoc run days ..10 20..
```

to run days 1 to 10 and 20 to 25 in 2022, and

```text
/root/2018 > aoc run days 1 4 9
```

to run days 1, 4 and 9 in 2018.

### `test`

```text
/root > aoc test <YEAR>
/root/YEAR > aoc test
```

Runs the solution to both parts of every day of the year with every puzzle input in release mode.

```text
/root > aoc test <YEAR> <DAY> [PART]
/root/YEAR > aoc test <DAY> [PART]
/root/YEAR/DAY > aoc test [PART]
```

Runs the solution to both parts, or a specific part, of the day with every puzzle input found in `/DAY/data` in release mode.

### `test days`

```text
/root > aoc test <YEAR> days <DAYS>
/root/YEAR > aoc test days <DAYS>
```

Runs the solution to both parts of the specified days of the year with every puzzle input in release mode.

The rules governing the argument \<DAYS\> are the same as in `run days` above.

### `submit`

```text
/root > aoc submit <YEAR> <DAY> [ANSWER]
/root/YEAR > aoc submit <DAY> [ANSWER]
/root/YEAR/DAY > aoc submit [ANSWER]
```

Submits a puzzle answer to the next unsolved part of the day on the Advent of Code website.

The puzzle answer submitted will be the argument \[ANSWER\] if provided, or the last answer produced by the solution.

For example:

```text
/root/YEAR/DAY > aoc run
/root/YEAR/DAY > aoc submit
```

will run the solution and then submit the answer it produced to the site.

### `progress`

```text
/root > aoc progress
```

```text
/root > aoc progress <YEAR>
/root/YEAR > aoc progress
```

Displays your account's progress in all years, or the specified year.

```text
/root > aoc progress <YEAR> <DAY>
/root/YEAR > aoc progress <DAY>
/root/YEAR/DAY > aoc progress
```

Displays your account's correctly submitted answers to the day.

### `help`

Opens this `README.md` in the default web browser.
