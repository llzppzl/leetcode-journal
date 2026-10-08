# 62. Unique Paths

| | |
|---|---|
| **Link** | [leetcode.com/problems/unique-paths](https://leetcode.com/problems/unique-paths/) |
| **Difficulty** | Medium |
| **Pattern** | 2D DP · base cases |

> **One-line takeaway:** The recurrence was the easy part. Before writing it, I ask what the base cases are, then make sure the recurrence starts where the base cases end and never overwrites them.

---

## 1. My first idea: where can I come from?

The robot can only move right or down, so cell `(i, j)` can only be reached from **above** `(i - 1, j)` or from the **left** `(i, j - 1)`. I had the recurrence right away:

```python
dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
```

`dp[i][j]` = the number of paths from the top-left corner to cell `(i, j)`.

## 2. Where I got stuck (two bugs, two lessons)

### Bug 1: everything came out 0

```python
dp = [[0] * n for _ in range(m)]
# ❌ went straight to the recurrence
```

With the whole grid at 0, every cell is `0 + 0`. The 0 just spreads, and the answer is 0.

The missing piece was the base cases:

- **First row:** the only way to get there is to keep moving right → exactly **1** path to each cell.
- **First column:** the only way is to keep moving down → exactly **1** path to each cell.

```python
for i in range(m):
    dp[i][0] = 1
for j in range(n):
    dp[0][j] = 1
```

**Lesson:** before the recurrence, ask what the base cases are, and check that I've actually filled them in.

### Bug 2: 56 instead of 28, and no error

After fixing the base cases, I still looped from 0:

```python
for i in range(m):          # ❌ starts at 0
    for j in range(n):
        dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
```

For `m = 3, n = 7` it returned **56**. Expected: **28**.

Why no `IndexError`? At `i = 0, j = 0` this reads `dp[-1][0]` and `dp[0][-1]`. In Python, `-1` is a valid index: it means **the last row / the last column**. So the program doesn't crash. It quietly reads the wrong cells, keeps going, and overwrites the base cases I had just set up.

The fix is to start the recurrence after the base cases:

```python
for i in range(1, m):
    for j in range(1, n):
```

**Lessons:**
- Base cases I've initialized must not be overwritten by the recurrence.
- A negative index in Python doesn't fail loudly. A silent wrong answer is more dangerous than a crash.

## 3. Final solution

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        dp = [[0] * n for _ in range(m)]   # dp[i][j]: number of paths to cell (i, j)
        for i in range(m):
            dp[i][0] = 1                   # first column: can only keep moving down
        for j in range(n):
            dp[0][j] = 1                   # first row: can only keep moving right
        for i in range(1, m):              # start at 1: row 0 and column 0 are base cases
            for j in range(1, n):
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1]   # from above + from the left
        return dp[m - 1][n - 1]
```

- **Time:** O(m · n)
- **Space:** O(m · n)

## 4. Edge cases that changed my thinking

- `m = 3, n = 7`: my buggy loop returned 56 instead of 28 with no error. This is the one that taught me Python's `-1` index will silently "work".
- `m = 1` or `n = 1`: a single row or column. The answer is 1 and comes entirely from the base cases. The recurrence loop doesn't run at all.

## 5. How I'd explain it in an interview (30 seconds)

> "`dp[i][j]` is the number of paths to cell `(i, j)`. Since I can only move right or down, every path into `(i, j)` comes from the cell above or the cell to the left, so I add those two. The first row and first column have exactly one path each, because I can only go straight, so I set them to 1 and start the recurrence at `(1, 1)`. That way I never read outside the grid or overwrite the base cases. It's O(m·n) time and space."

## 6. Connections & what's next

- **Same structure as:** [70 Climbing Stairs](0070-climbing-stairs.md): "where did the last step come from?" gives the recurrence. Here it's two directions instead of two step sizes.
- **Checklist I now run for every DP:** what does `dp[...]` mean → what are the base cases → where does the loop start → can a boundary state safely use the recurrence?
- **Next:** 63 Unique Paths II (obstacles, so the base-case row and column are no longer all 1s), and the space optimization down to a single row.
