# My Coding Style

[English](README.en.md) \
[Traditional Chinese](README.zh-hant.md)

## Introduction
After collaborating with some projects<sup>[1](https://github.com/FNNDSC/ChRIS_ultron_backEnd),[2](https://github.com/chhsiao1981/use-thunk),[3](https://github.com/Ptt-official-app/pttbbs-backend)</sup>, I feel the need to clarify my coding style, to:
* help the others understand my philosophy of coding style.
* improve my coding style through the lifelong journey of collaborating with the others.

I find that `python` and `golang` are very easy languages to communicate with. In this repository, (unless specified,) the description of the technical terms is based on `python` terms. I'll use `golang` if the feature does not exist in `python`. I'll use the specified language if the feature does not exist in `golang` either.

## Philosophy
Unless necessary with specified, we **SHOULD** follow the following coding style:

1. **MUST NOT** (or as less as possible) use global variables (globally and in module level).
    * Global variables are considered evil of everything！
    * Exceptions: constants, configurations, functions.
    * Constants: should be organized in 1 file (at most 1 file per dir.)
    * Configurations: should be in 1 dict-typed global `cfg.config` variable.
    * Other derived global variables: should be at few as possible, and should also be put into `cfg.config`.
    * Possible exception: like `bash`-scripts, typically < 30 lines (fitting a screen) for the whole program.
2. **MUST** **Thoughtfully** name the variables.
    * (trivial)
    * The process of writing a program is the same as writing an academic paper！
3. **MUST NOT** (or as less as possible) [Indent Hadouken](https://www.reddit.com/r/ProgrammerHumor/comments/27yykv/indent_hadouken/) coding style.
    * (trivial)
    * reduce human cognitive loading and improve readability.
4. **MUST** **Thoughtfully** dealing with error handling！
    * **MUST NOT** "silent error" unless feeling confident and specified that it's ok to do so with `XXX SILENT ERROR` comments！
    * However, "return if error" strategy does not always work (works only around 50% of the time)！
    * If you don't let your code handle the error, it will be you **Manually** handling the error!
5. **SHOULD** use `error-code` instead of `try-catch`.
    * Very easy to create an additional indent for the `try-catch` style (Item 3).
    * Because of the previous issue, it's very easy ending up with only the "return if error" pattern.
    * For the languages with only `try-catch`: **MAY** have the corresponding functions for the libs doing `try-catch` and converting to `error-code` style.
6. **SHOULD** based on the following defensive programming style to meet Item 3-5.
    * In some cases (few cases in my experience), we cannot do such kind of programming style. Use this coding style **Thoughtfully**.
```python
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
        [return err, with optionally primitively deal-with (ex. error-log)]

    ret2, err = func2()
    .
    .
    .

    return ret
```

```rust
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

7. **SHOULD** modules + functions instead of class inheritance/function overloading (composition over inheritance).
    * Like `c++`, class inheritance is the cause of many disasters～
    * Type template (generic programming) is ok to use, because it's still easy to trace the details.
    * exception: `java`/`c++`, which requires class, and I hope I won't need to dev in these lang ever again.
    * `golang` style: composition instead of class inheritance.
8. The lines in a function **SHOULD** be restricted to < 30 lines.
    * A function is considered as a thought block.
    * We want to have a complete thought block when viewing this function.
    * With divide-and-conquer, this is easily achieved.
    * 30 lines is based on most lines-in-an-screen by current screen standard/human vision ability.
    * Due to human vision ability, it is unlikely that 30 will be much larger in the future.
    * **MUST** specify "an easy summary by steps" if we do need a function > 30 lines.
    * We can always do:
```
def [f()]:
    [f_preprocess()]
    [f_process()]
    [f_postprocess()]
```
9. **SHOULD** 1 public function as the main purpose of the file/module, potentially with several private assistant functions.
    * (trivial)
    * Exception: utility modules
10. **SHOULD** < 100 lines per file (heading comments do not count).
    * consistent with Item 8 and Item 9.
    * if really complicated, < 200 lines per file.
11. **SHOULD** At most 100 chars per line.
    * for screen settings and readability.
    * longer than the previous 80-char limit. We are living in the era of 16:9, no more 4:3.
    * if really want to have a longer limit: defined by the project owner.
12. **SHOULD NOT** "conditional import"
    * need to worry which are imported during run-time.
    * For `ts/js`, **MAY** lazy-import if the app is too complicated.
    * **MAY** conditional import for mocked modules.
13. **SHOULD** have type hinted.
    * (trivial)
    * excellent for editors nowadays.
14. For `rust`: **SHOULD NOT** type-matching style.
    * (Item 3).
    * use `.or_else` + `.inspect_err?` to deal with `Result`.
    * **MAY** use `.unwrap_or_else` to deal with err and return default value.
99. Unless specified in the previous items, **SHOULD** follow the typical coding style (ex: snake_case / camelCase / CapitalCamelCase):
    * [PEP8](https://peps.python.org/pep-0008/), [python packaging](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/)
    * [biome](https://biomejs.dev/)
    * [golang](https://google.github.io/styleguide/go/guide)
    * [Google C/C++ coding style](https://google.github.io/styleguide/cppguide.html)

### Terminology
With "Unless necessary with specified, we **SHOULD** follow the following coding style" as the precondition, **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** can be referred to [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119).
