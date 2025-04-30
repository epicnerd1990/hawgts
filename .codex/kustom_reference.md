# Kustom Expression Language Reference

    This document collects everything you need to know to author and troubleshoot Kustom formulas.

## 1. Embedding Expressions
    - Wrap any dynamic formula between `$` delimiters.
        E.g.: `$if(gv(enabled)=1, "ON", "OFF")$`

## 2. Data Types
    - Number (floating‑point)
    - String (any text; use `\n` for newlines)
    - Boolean (0/empty = false; non‑zero/non‑empty = true)
    - Color (`#RRGGBB` or `#AARRGGBB`)
    - List (comma‑separated string; index with `tc(split,…)`)

## 3. Operators & Syntax
    - Arithmetic: `+`, `-`, `*`, `/`, `%`
    - Comparison: `=`, `!=`, `<`, `>`, `<=`, `>=`, `~=` (regex match)
    - Logical (bitwise only): `&` (AND), `|` (OR), `^` (XOR), `~` (NOT)
    - Parentheses for grouping
    - String concatenation via `+` when operands are strings

## 4. Variables & Scope
    - Local variable
        - Defines a *local* var for the current formula (or loop iteration).
        - After being set, local variables can be called by `#name`

    `lv(name, value)`

    - Global variable
        - Caches its result *once per formula*. Multiple calls in the same formula return the same value.

    `gv(name[, default])`

    - Global "function"
    You can set a local variable and have it pass through a subsequently called global variable, since its loaded in the current formula. You can also use local variables back. This can be used to create global "functions", for example to read a json file, you simple set the keyname, call the global function, which reads the keyname from your local, runs the formula, and you can then get the output back by reading a local variable set in the global function

    `gv(globalfun)` = `$lv(output, (#input + 5))`

    `$lv(input, 5)$$gv(globalfun)$$#output$` = 10


## 5. Conditionals: if()
    ```
    text
    if(cond1, result1,
        cond2, result2,
        …,
        defaultValue)
    ```

Evaluates each cond; returns the associated result.  If none match, returns defaultValue (optional).

## 6. Loops: fl()

    fl(start, end, step, exprString)

    * Repeats `exprString` for `i = start` to `end` stepping by `step`.
    * Each iteration gets its own `gv()` cache but shares `lv()` locals.
    * To force numeric args when using `mu()` or `tc()`, append `*1` (e.g. `mu(min,0,1)*1`).
    * Loop bodies are auto‑wrapped in `$…$`; embed your own `$…$` to mix in plain text.

## 7. System & Widget Info: si()

    * `si(rwidth)`: widget rendered width in px
    * `si(rheight)`: widget rendered height
    * `si(rratio)`: `rwidth/rheight` ratio
    * `si(ratio)`: same as `rratio`
    * `si(dpi)`: screen density DPI
    * `si(orient)`: 0 = horizontal, 1 = vertical

## 8. Math & Numeric: mu()

    mu(mode, x[, y])

Common modes:

    * `abs`, `sqrt`, `floor`, `ceil`, `round`
    * `min`, `max`
    * `sin`, `cos`, `tan`, `log`, `exp`
    * `pow`, `sign`, `rand`
        _(See official docs for the full list)_

## 9. Conversion & Parsing: tc()

    tc(type, input, arg1[, arg2])

Core types:

    * `json`: parse JSON      tc(json, jsonText, pathExpr, default?)
    * `split`: split on delimiter      tc(split, text, delimiter, index)
    * `reg`: regex replace      tc(reg, text, pattern, replacement)
    * `cut`: substring      tc(cut, text, start, length)
    * `utf`: Unicode codepoint → character      tc(utf, codePointHexOrDec)
    * `ord`: character → codepoint (may break on floats)
    * `count`: count occurrences      tc(count, text, sub)
    * Negative `start` counts from end.
    * Negative `length` *intended* to cut “up to N from end” but is buggy if `abs(length) ≤ start`.
    * See all tc types at https://docs.kustom.rocks/docs/reference/functions/tc/
    * Kustom processor uses "json-path" (https://github.com/json-path/JsonPath)

### Regex Caveats

    * Literal `"` in patterns/replacements is not allowed; use `\x22`.
    * Only `$1`…`$9` in replacement are supported (no multi‑digit `$10`).

## 10. Regex Match Operator: ~=

    #text ~= "^err"

Returns true if text matches the regex.

## 14. Quirks & Gotchas

    * Unquoted `!` and `~` parse as literals—wrap in quotes or use them only in `!=`/`~=`.
    * `gv()` is cached per formula; use `fl()` to reset it.
    * Floats break some `tc()` subtypes—convert via `+0` or `*1`.
    * `gv(_)` returns an empty string (short hack for `""`).
    * `tc(split)` strips *empty* tokens but preserves whitespace‑only tokens.

## 15. Official Reference

For the exhaustive, versioned list of functions and subtypes, see:
https://docs.kustom.rocks/docs/reference/functions

-------------------------------------------------------------------------------------------------------------------------
---

This file is intended to be your single‑source reference for all Kustom formula work.

