# My Coding Style

[English](README.en.md) \
[Traditional Chinese](README.zh-hant.md)

## 簡介
在與[一些 projects](https://github.com/FNNDSC/ChRIS_ultron_backEnd) 合作以後，我覺得有必要釐清我的 coding style，以便:
* 幫助其他人理解我對 coding style 的哲學。
* 人生中透過與其他人合作的方式, 來持續改進我的 coding style。

我覺得 Python 和 golang 都是非常容易溝通的語言。在這個 repository 裡，（除非特別註明，）技術術語的說明是以 Python 的術語為基礎。如果某個功能在 Python 中不存在，我會使用 golang。如果該功能在 golang 中也不存在，我則會使用指定的語言來描述。

## 哲學
除非必要並且特別標註的例外們:

1. **絕對不要**（或盡量地少）使用 global variable（不論是 global 層級還是 module 層級）。
    * Global variables 是萬惡之源！
    * 例外：constants, configurations, functions.
    * constants：應集中在 1 個檔案中（至多是每個目錄 1 個檔案）。
    * 設定值：應存放於 1 個 dict 型別的 global variable `cfg.config`。
    * 其他衍生的 global variable 應盡量少，並且同樣放入 `cfg.config`。
    * 可能的例外：像 bash 腳本，整個程式通常 < 30 行（剛好一個螢幕）。
2. 盡量避免 [縮排波動拳](https://www.reddit.com/r/ProgrammerHumor/comments/27yykv/indent_hadouken/) 的 coding style。
    * (顯而易見)
3. 如果可以的話，**審慎地**處理錯誤處理！
    * 除非你有把握，並且明確註明 `XXX SILENT DEATH` 註解，否則絕對不要 "silent death"。
    * 然而，"return if error" 的策略並不總是有效（大概只有 50% 的情況有效）！
    * 如果不讓程式碼處理錯誤，最後就會變成你人工處理錯誤！
4. 若可行，使用 `error-code` 而非 `try-catch`.
    * `try-catch` 很容易造成額外縮排（第 2 點）。
    * 因為這個問題，很容易最後只剩下 "return if error" 的模式。
    * 如果可以的話，請遵循 golang 的錯誤碼風格（以便跨語言一致）。
    * 對於只能使用 `try-catch` 的語言：如果可以的話，替 libs 有 `try-catch` functions 寫相對應的 functions ，並轉換成 error-code 風格。希望回傳的 `error-code` 可以優雅地使用 list-comprehension 風格，而不是作為 return struct 的一部分（我真的不喜歡 `rust`）。
5. **永遠**使用 `golang` 風格的 `error-code`，絕對不要使用 `!` 或 `?`（如 `ts` 或 `rust`）。
    * `!` 或 `?` 是懶人的工具！
    * 因為偷懶，習慣用 `!` 或 `?` 的人通常只會用 "return if error" 風格，且不願意修改程式碼。
6. 如果可以的話，盡量使用以下 defensive programming style 來符合第 2 至 5 點。
    * 在某些情況（依我的經驗非常少）下，我們無法使用這種風格。例如需要做一些中途檢查時。請明智地使用這種 coding style。
```
def f():
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
7. modules + functions，而非 class inheritance / function overloading。
    * 就像 `C++`，class inheritance 是許多災難的來源～
    * Type template 是可以用的，因為仍然容易追蹤細節。唯一的差別是我們不想因為 type 不同就重複寫同樣的函式。
    * 例外：`JAVA`/`C++`，它們強制需要類別，但我希望再也不要使用這些 language 做開發。
    * `golang` style：composition instead of class inheritance。
8. function 內的程式行數限制 < 30 行。
    * 一個 function 被視為一個思考區塊。
    * 我們希望在閱讀這個 function 時能是一個完整的思考區塊。
    * 透過 divide-and-conquer，這很容易達成。
    * 30 行的限制是根據目前螢幕標準 / 人類視覺能力下的螢幕可見行數。
    * 由於人類視覺能力限制，未來這個數字也不太可能大幅增加。
    * 若必須寫 > 30 行的函式，必須註明原因並加上「step-by-step 的簡易 summary」。
    * 我們永遠可以這樣做：
```
def f():
    [f_preprocess()]
    [f_process()]
    [f_postprocess()]
```
9. 如果可以的話，每個 file/module 只有 1 個 public function 作為主要目的，potentially 搭配多個 private assistant functions。
    * (顯而易見)
10. 如果可以的話，每個 file < 100 行（標頭註解不計算）。
    * 與第 8 與第 9 點一致。
    * 若真的很複雜，每個 file < 200 行。
11. 每行最多 100 個 chars。
    * 為了螢幕設定與可讀性。
    * 比之前的 80 char 限制更長。我們現在是 16:9 的時代，而不是 4:3。
    * 如果真的想放寬限制：120 chars。
    * 如果真的還想更長：由專案負責人定義。
12. 如果可以的話，不要使用 "conditional import"。
    * 這會導致在 runtime 時, 還需要擔心 import 了哪些。
    * 對於 `ts/js`，如果 app 過於複雜，可以使用 lazy-import。
99. 除非前面條款特別指定，否則盡量遵循典型的程式風格（例: snake_case / camelCase / CapitalCamelCase）：
    * [PEP8](https://peps.python.org/pep-0008/), [Python packaging](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/)
    * [biome](https://biomejs.dev/)
    * [golang](https://google.github.io/styleguide/go/guide)
    * [Google C/C++ coding style](https://google.github.io/styleguide/cppguide.html)
