# 91. Decode Ways

| | |
|---|---|
| **Link** | [leetcode.com/problems/decode-ways](https://leetcode.com/problems/decode-ways/) |
| **Difficulty** | Medium |
| **Pattern** | 1D DP · counting from two sources |
| **Status** | ⏸️ Paused: I understand the two sources and what `+=` does, but zeros still trip me up |

> **One-line takeaway (so far):** A valid way to decode the current digit doesn't add "+1" or "+2". It **inherits** however many ways the earlier prefix already had.

---

## 1. My first idea: two ways the current digit can be used

`dp[i]` = the number of ways to decode the prefix `s[:i + 1]`. The current digit can be decoded in two ways:

- **alone** (a single digit): every decoding of the prefix before it can be extended → inherit `dp[i - 1]`
- **together with the previous digit** (a two-digit number): every decoding of the prefix before those two digits can be extended → inherit `dp[i - 2]`

## 2. Where I got stuck (three bugs, three lessons)

### Bug 1: turning two characters into a number

```python
s[i - 1] * 10 + s[i]      # ❌ these are strings
int(s[i - 1:i + 1])       # ✅ "26" → 26
```

For strings, `"2" * 10` is `"2222222222"` (repetition, not arithmetic).

**Lesson:** characters of a string are strings. Slice first, then `int()`.

### Bug 2: `int("")` raised `ValueError`

My loop started at `i = 0`, so the two-digit slice was `s[-1:1]`, which is `""`, and `int("")` raises.

**Lesson:** a two-digit number only exists from `i = 1` onward. The pair check can't run at `i = 0`.

### Bug 3: `"12"` returned 3 instead of 2

```python
if pair is valid:
    dp[i] = dp[i - 1] + 2      # ❌
```

I was adding a fixed number of ways. But a valid way to decode the current digit(s) doesn't add a constant: it **extends every decoding that already reached the earlier prefix**. So the contributions are `dp[i - 1]` and `dp[i - 2]`, not `+ 1` or `+ 2`.

**Lesson:** in counting DP, a valid choice extends every existing path, so it adds the number of paths, not a constant. Ask "how many paths can reach here?", not "how many new options do I have?".

## 3. The breakthrough: `dp[i]` is an accumulator

The usual code has `dp[i] += dp[i - 1]`, and that confused me. It wasn't the idea. It was: *why is `dp[i]` adding itself?*

Written out step by step, it stopped being magic:

```python
dp[i] = 0                          # start with no ways
if <current digit can stand alone>:
    dp[i] = dp[i - 1]              # decodings that end with a single digit
if <last two digits form a valid pair>:
    dp[i] = dp[i] + dp[i - 2]      # plus decodings that end with a two-digit number
```

`+=` just means "add another group of valid decodings to the running total". The two placeholder conditions are exactly what I haven't nailed down yet (next section).

## 4. Zeros: the part I haven't solved yet

Every bug from here on was about `0`.

### `if "0" in s: return 0`

`"10"` returned 0; expected 1. A `0` can't be decoded **alone**, but it **can** be the second digit of `10` or `20`.

### `"226"` returned 2 (expected 3)

When initializing `dp[1]`, I checked `s[:3]` (`"226"`) instead of `s[:2]` (`"22"`). Index 2 is the **third** element.

### `"2101"` returned 3 (expected 1)

I only checked `int(pair) < 27`. But `int("01")` is `1`, which passes. A valid two-digit code must satisfy **`10 <= pair <= 26`**, so `"01"` is invalid.

### `"2101"` returned 2 (expected 1)

I added `if s[i - 1] == "0": dp[i] = dp[i - 2]`. But the last `"1"` can be decoded alone, so it should inherit from `"210"` (`dp[i - 1]` = 1, namely `2 | 10`), not from `"21"` (`dp[i - 2]`).

## 5. Where I paused

- **What I understand:** the two sources (single digit, two digits), and why `dp[i]` accumulates them.
- **What I don't yet:** one clean rule that covers every zero case (`0`, `10`, `20`, `01`, `30`, `00`) instead of patching them one at a time.

## 6. Plan for when I come back

Don't start from the full formula. Re-trace these by hand, filling in `dp` one index at a time and writing down which of the two sources is allowed at each step:

| Input | Expected | What it tests |
|---|---|---|
| `"10"` | 1 | `0` can't stand alone, but `10` is a valid pair |
| `"226"` | 3 | normal case, plus index/slice correctness in the base case |
| `"2101"` | 1 | `"01"` is not a valid pair; the last `1` inherits from `dp[i - 1]` |
| `"30"` | 0 | `0` can't stand alone, and `30 > 26` |
| `"00"` | 0 | neither source is ever allowed |

Then fill in the two placeholder conditions in the accumulator.

## 7. Connections

- **Same "where did the last piece come from?" as:** [70 Climbing Stairs](0070-climbing-stairs.md): there the last step is 1 or 2 stairs; here the last piece is 1 or 2 digits. The difference is that each source here has a validity condition.
- **Same "prefix + rest" structure as:** [139 Word Break](0139-word-break.md).
