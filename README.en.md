# My Coding Style

[English](README.en.md) \
[Traditional Chinese](README.zh-hant.md)

## Introduction
After collaborating with [some projects](https://github.com/FNNDSC/ChRIS_ultron_backEnd), I feel the need to clarify my coding style, to:
* help the others understand my philosophy of coding style.
* improve my coding style through the lifelong journey of collaborating with the others.

I find that `Python` and `golang` are very easy languages to communicate with. In this repository, (unless specified,) the description of the technical terms is based on `Python` terms. I'll use `golang` if the feature does not exist in `Python`. I'll use the specified language if the feature does not exist in `golang` either.

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
3. As least [Indent Hadouken](https://www.reddit.com/r/ProgrammerHumor/comments/27yykv/indent_hadouken/) coding style as possible.
    * (trivial)
    * reduce human cognitive loading and improve readability.
4. If possible, **Thoughtfully** dealing with error handling！
    * **MUST NOT** "silent death" unless feeling confident and specified that it's ok to do so with `XXX SLIENT DEATH` comments！
    * However, "return if error" strategy does not always work (works only around 50% of the time)！
    * If you don't let your code handle the error, it will be you **Manually** handling the error!
5. If possible, use `error-code` instead of `try-catch`.
    * Very easy to create an additional indent for the `try-catch` style (meet 3).
    * Because of the previous issue, it's very easy ending up with only the "return if error" pattern.
    * If possible, follow `golang` error code style (to be consistent across lang).
    * For the languages with only `try-catch`: if possible, have the corresponding functions for the libs doing `try-catch` and converting to `error-code` style. Hope that the returned `error-code` can be elegantly in the list-comprehension style, but not part of the returned struct (I don't really like `rust`).
6. **SHOULD** use `golang`-style `error-code`, **MUST NOT** `!` or `?` (by `ts` or `rust`).
    * `!` or `?` are for lazy people!
    * Due to laziness, it's very easy that the people who used to `!` or `?` do only "return if error" style and not want to change the code.
7. If possible, use the following defensive programming style as much as possible to meet Point 3-6.
    * In some cases (few cases in my experience), we cannot do such kind of programming style. For example, we may need to do some mid-process-check. Use this coding style **Thoughtfully**.
```
def [function-name]():
    if [error]:
        [return error, with optionally primitively deal-with (ex. error-log)]
    if [error]:
        [return error, with optionally primitively deal-with (ex. error-log)]
    .
    .
    .

    if [manageable error]:
        [deal with the error]
    if [manageable error]:
        [deal with the error]
    .
    .
    .

    [real business]
    .
    .
    .

    return ret
```
8. modules + functions instead of class inheritance/function overloading.
    * Like C++, class inheritance is the cause of many disasters～
    * Type template is ok to use, because it's still easy to trace the details. The only difference with template-forbidden coding-style is that we don't want to write the same function twice just because of the different types.
    * exception: `JAVA`/`C++`, which requires class, and I hope I won't need to dev in these lang ever again.
    * `golang` style: composition instead of class inheritance (though we still can practically do `class inheritance` in `golang`).
9. The lines in a function is restricted to < 30 lines.
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
10. If possible, 1 public function as the main purpose of the file/module, potentially with several private assistant functions.
    * (trivial)
    * Exception: utility modules
11. If possible, < 100 lines per file (heading comments do not count).
    * consistent with 9 and 10.
    * if really complicated, < 200 lines per file.
12. At most 100 chars per line.
    * for screen settings and readability.
    * longer than the previous 80-char limit. We are living at 16:9, no more 4:3.
    * if really want to have a longer limit: 120 chars.
    * if really want to have a longer limit: defined by the project owner.
13. If possible, no "conditional import"
    * need to worry which are imported during run-time.
    * For `ts/js`, ok to do lazy-import if the app is too complicated.
14. If possible, have type hinted.
    * (trivial)
    * excellent for editors nowadays.
99. Unless specified in the previous items, try to follow the typical coding style (ex: snake_case / camelCase / CapitalCamelCase):
    * [PEP8](https://peps.python.org/pep-0008/), [Python packaging](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/)
    * [biome](https://biomejs.dev/)
    * [golang](https://google.github.io/styleguide/go/guide)
    * [Google C/C++ coding style](https://google.github.io/styleguide/cppguide.html)

### Terminology
With "Unless necessary with specified, we **SHOULD** follow the following coding style" as the precondition, **MUST**, **MUST NOT**, and **SHOULD** can be referred to [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119).
