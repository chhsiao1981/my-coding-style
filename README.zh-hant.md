# My Coding Style

[English](README.en.md) \
[Traditional Chinese](README.zh-hant.md)

## 簡介

在與一些 projects 合作以後<sup>[1](https://github.com/FNNDSC/ChRIS_ultron_backEnd),[2](https://github.com/chhsiao1981/use-thunk),[3](https://github.com/Ptt-official-app/pttbbs-backend)</sup>, 我覺得有必要釐清我的 coding style，以便:

* 幫助其他人理解我對 coding style 的哲學。
* 人生中透過與其他人合作的方式, 來持續改進我的 coding style。

我覺得 `python` 和 `golang` 都是非常容易溝通的語言。在這個 repository 裡，（除非特別註明，）技術術語的說明是以 `python` 的術語為基礎。如果某個功能在 `python` 中不存在，我會使用 `golang`。如果該功能在 `golang` 中也不存在，我則會使用指定的語言來描述。

## 哲學

除非必要並且特別標註的例外們, 我們 **應該要** 遵守以下的 coding style:

1. **絕對不要**（或盡量地少）使用 global variable（不論是 global 層級還是 module 層級）。
    * Global variables 是萬惡之源！
    * 例外：constants, configurations, functions.
    * constants：應集中在 1 個檔案中（至多是每個目錄 1 個檔案）。
    * 設定值：應存放於 1 個 dict 型別的 global variable `cfg.config`。
    * 其他衍生的 global variable 應盡量少，並且同樣放入 `cfg.config`。
    * 可能的例外：類似 bash scripts 的小程式，整個程式通常 < 30 行（剛好一個螢幕）。

2. **一定要** **經過審慎思考過地** 替 variables 取名字.
    * (顯而易見)
    * 寫程式的過程跟寫學術論文的過程是完全一樣的！

3. **絕對不要**（或盡量地少）[縮排波動拳](https://www.reddit.com/r/ProgrammerHumor/comments/27yykv/indent_hadouken/) 的 coding style。
    * (顯而易見)
    * 減輕人類的認知負荷並增進易讀性。

4. **一定要** **經過審慎思考過地** 處理錯誤處理！
    * 除非你有把握，並且明確註明 `XXX SILENT ERROR` 註解，否則 **絕對不要** "silent error"。
    * 然而，"return if error" 的策略並不總是有效（大概只有 50% 的情況有效）！
    * 如果不讓程式碼處理錯誤，最後就會變成你 **人工** 處理錯誤！

5. **應該要** 使用 `error-code` 而非 `try-catch`.
    * `try-catch` 很容易造成額外縮排（第 3 點）。
    * 因為這個問題，很容易最後只剩下 "return if error" 的模式。
    * 對於只能使用 `try-catch` 的語言：**可以** 替 libs 有 `try-catch` functions 寫相對應的 functions ，並轉換成 error-code 風格。

6. **應該要** 根據以下 defensive programming style 來符合第 3 至 5 點。
    * 在某些情況（依我的經驗非常少）下，我們無法使用這種風格。例如需要做一些中途檢查時。請 **經過審慎思考過地** 使用這種 coding style。

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
        [return err, with optionally primitively deal-with (ex. error-log)]

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
        [return err, with optionally primitively deal-with (ex. error-log)]

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

7. **應該要** modules + functions，而非 class inheritance / function overloading (composition over inheritance)。
    * 就像 `c++`，class inheritance 是許多災難的來源～
    * Type template (generic programming) 是可以用的，因為仍然可以容易追蹤細節。
    * 例外：`java`/`c++`，它們強制需要 class。
    * `golang` style：composition instead of class inheritance。

8. function 內的程式行數 **應該要** 限制 < 30 行。
    * 一個 function 被視為一個思考區塊。
    * 我們希望在閱讀這個 function 時能是一個完整的思考區塊。
    * 透過 divide-and-conquer，這很容易達成。
    * 30 行的限制是根據目前螢幕標準 / 人類視覺能力下的螢幕可見行數。
    * 由於人類視覺能力限制，未來這個數字也不太可能大幅增加。
    * 若必須寫 > 30 行的函式，**一定要** 註明「step-by-step 的簡易 summary」。
    * 我們永遠可以這樣做：

```python
def [f()]:
    [f_preprocess()]
    [f_process()]
    [f_postprocess()]
```

9. 每個 file/module **應該要** 只有 1 個 public function 作為主要目的，potentially 搭配多個 private assistant functions。
    * 每個 file/module 應該只能有 1 個主要目的.
    * Exception: utility files/modules.

10. 每個 file **應該要** < 100 行（標頭註解不計算）。
    * 與第 8 與第 9 點一致。
    * 若真的很複雜，每個 file < 200 行。

11. 每行 **應該要** 最多 100 個 chars。
    * 為了螢幕設定與可讀性。
    * 比之前的 80 char 限制更長。我們現在是 16:9 的時代，而不是 4:3。
    * 如果真的還想更長：由專案負責人定義。

12. **應該不要** 使用 "conditional import"。
    * 這會導致在 runtime 時, 還需要擔心 import 了哪些。
    * 對於 `ts/js`，如果 app 過於複雜，**可以** 使用 lazy-import。
    * **可以** 使用 conditional import 在 mocked modules.

13. **應該要** 使用 type hinted 方式。
    * (顯而易見)
    * 這個年代對於程式編輯器是非常好的。

14. `rust`: **應該不要** type-matching style.
    * (第 3 點).
    * 使用 `.or_else` + `.inspect_err?` 來處理 `Result`.
    * **可以** 使用 `.unwrap_or_else` 來處理 err 和 return default value.

99. 除非前面條款特別指定，否則 **應該要** 遵循典型的程式風格（例: snake_case / camelCase / CapitalCamelCase）：

    * [PEP8](https://peps.python.org/pep-0008/), [python packaging](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/)
    * [biome](https://biomejs.dev/)
    * [golang](https://google.github.io/styleguide/go/guide)
    * [Google C/C++ coding style](https://google.github.io/styleguide/cppguide.html)

### 定義

在 "除非必要並且特別標註的例外們, 我們 **應該要** 遵守以下的 coding style" 這個前提下, **一定要**, **絕對不要**, 和 **應該要**, **應該不要**, 和 **可以** 是根據 [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119).
