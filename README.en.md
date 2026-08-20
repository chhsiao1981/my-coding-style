# My Coding Style

[English](README.en.md) \
[Traditional Chinese](README.zh-hant.md)

## Introduction

After collaborating on several projects<sup>[1](https://github.com/FNNDSC/ChRIS_ultron_backEnd), [2](https://github.com/chhsiao1981/use-thunk), [3](https://github.com/Ptt-official-app/pttbbs-backend)</sup>, I feel the need to clarify my coding style in order to:

* help others understand my coding philosophy.
* continuously improve my coding style through the lifelong journey of collaborating with others.

I find `python` and `golang` to be very easy languages for expressing and communicating ideas. Unless otherwise specified, the technical terminology used in this repository is based on `python` terminology. I use `golang` when a feature is not available in `python`, and I use the language specified by the project when the feature is not available in `golang` either.

## Philosophy

Unless otherwise specified or necessary, we **SHOULD** follow the coding style described below:

1. **MUST NOT** use global variables, or use them as little as possible, both globally and at the module level.
    * Global variables are considered the root of all evil!
    * Exceptions: constants, configuration, and functions.
    * Constants **SHOULD** be organized in a single file, with at most one such file per directory.
    * Configuration **SHOULD** be stored in a single dictionary-typed global variable, `cfg.config`.
    * Other derived global variables **SHOULD** be kept to a minimum and, whenever possible, stored in `cfg.config`.
    * Possible exception: short programs such as `bash` scripts, typically fewer than 30 lines (i.e., fitting on one screen).

2. Variables **MUST** be named thoughtfully.
    * (Trivial.)
    * Writing a program is similar to writing an academic paper: the names we choose should clearly communicate our ideas.

3. **MUST NOT** use [Indent Hadouken](https://www.reddit.com/r/ProgrammerHumor/comments/27yykv/indent_hadouken/) coding style, or use it as little as possible.
    * (Trivial.)
    * Avoiding excessive indentation reduces cognitive load and improves readability.

4. Error handling **MUST** be handled thoughtfully.
    * **MUST NOT** silently ignore errors unless we are confident that doing so is safe and explicitly document it with an `XXX SILENT ERROR` comment.
    * However, the "return if error" strategy does not always work. In my experience, it works only about 50% of the time.
    * If you do not let your code handle an error, you will eventually have to handle it **manually**.

5. **SHOULD** use error codes instead of `try-catch`.
    * The `try-catch` style makes it very easy to introduce additional indentation (Item 3).
    * Because of this, it is also easy to end up with nothing more than a sequence of "return if error" statements.
    * For languages that provide only `try-catch`, **MAY** provide wrapper functions around library calls that use `try-catch` and convert their behavior into an error-code style.

6. **SHOULD** follow the following defensive programming style to satisfy Items 3–5.
    * In some cases (although rarely, in my experience), this style of programming is not practical. Use this approach **thoughtfully** in those cases.

```python
# python

def [function-name]():
    ret1, err = func1()
    if [manageable err]:
        [deal with the error]
        err = None
    if [manageable err]:
        [deal with the error]
        err = None
    .
    .
    .
    if err:
        [return err, with optional primitive handling (e.g., error logging)]

    ret2, err = func2()
    if [manageable err]:
        [deal with the error]
        err = None
    if [manageable err]:
        [deal with the error]
        err = None
    .
    .
    .
    if err:
        [return err, with optional primitive handling (e.g., error logging)]

    .
    .
    .

    return ret
```

```rust
//! rust

fn [function-name]() -> Result<> {
    let ret1 = func1()
        .or_else(|err| {
            if [manageable err] {
                [deal with the error]
                Ok(recover1)
            } else if [manageable err] {
                [deal with the error]
                Ok(recover1)
            } ... {
                ...
            } else {
                Err(err)
            }
        })?

    let ret2 = func2()
        .or_else(|err| {
            if [manageable err] {
                [deal with the error]
                Ok(recover2)
            } else if [manageable err] {
                [deal with the error]
                Ok(recover2)
            } ... {
                ...
            } else {
                Err(err)
            }
        })?

    .
    .
    .

    return ret
}
```

7. **SHOULD** use modules and functions instead of class inheritance and function overloading (composition over inheritance).
    * Like `c++`, class inheritance can be the source of many disasters.
    * Type templates (generic programming) are fine because they are still relatively easy to trace and understand.
    * Exception: `java`/`c++`, where the language or framework may require the use of classes.
    * `golang` style: prefer composition over class inheritance.

8. The number of lines in a function **SHOULD** be fewer than 30.
    * Comments and imports are counted as 0 lines (no cognitive load for the code).
    * Multi-line-parameter functions are counted as 1 line (same cognitive load as 1-line-parameter functions).
    * A function should represent a single thought block.
    * We want to be able to understand the complete thought represented by a function when viewing it.
    * This can be easily achieved through divide-and-conquer.
    * The 30-line limit is based on the number of lines that can typically fit on one screen with current screen standards and human visual capabilities.
    * Given the limitations of human vision, it is unlikely that this number will increase significantly in the future.
    * If a function genuinely needs to exceed 30 lines, an "easy summary by steps" **MUST** be provided.
    * We can always break a function down into smaller steps:

```python
def [f()]:
    [f_preprocess()]
    [f_process()]
    [f_postprocess()]
```

9. A file/module **SHOULD** have one public function as its primary purpose, potentially supported by several private helper functions.
    * There should be only 1 primary purpose in each file.
    * Exception: utility files/modules.

10. A file **SHOULD** contain fewer than 100 lines.
    * Comments and imports are counted as 0 lines.
    * Multi-line-parameter functions are counted as 1 line (same cognitive load as 1-line-parameter functions).
    * This is consistent with Items 8 and 9.
    * For genuinely complicated files, fewer than 200 lines is acceptable.

11. Each line **SHOULD** contain at most 100 characters.
    * This is intended to accommodate screen dimensions and improve readability.
    * This is a longer limit than the previous 80-character limit. We are living in the era of 16:9 screens, not 4:3 screens.
    * If a longer limit is genuinely needed, the project owner may define a different limit.

12. **SHOULD NOT** use conditional imports.
    * Conditional imports make it necessary to reason about which modules are imported at runtime.
    * For `ts/js`, **MAY** use lazy imports if the application is sufficiently complicated to justify them.
    * **MAY** use conditional imports for mocked modules.

13. Code **SHOULD** use type hints.
    * (Trivial.)
    * Modern editors can make excellent use of type information.

14. For `rust`, **SHOULD NOT** use type-matching style.
    * (See Item 3.)
    * Use `.or_else` and `.inspect_err?` to handle `Result`.
    * **MAY** use `.unwrap_or_else` to handle errors and return a default value.

99. Unless otherwise specified in the previous items, code **SHOULD** follow the typical coding conventions for the language (e.g., `snake_case`, `camelCase`, or `CapitalCamelCase`):

    * [PEP 8](https://peps.python.org/pep-0008/), [Python Packaging](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/)
    * [Biome](https://biomejs.dev/)
    * [Go](https://google.github.io/styleguide/go/guide)
    * [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html)

### Terminology

With "Unless otherwise specified or necessary, we **SHOULD** follow the following coding style" as the precondition, **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are used in accordance with [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).