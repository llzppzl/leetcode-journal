# 70. Climbing Stairs

| | |
|---|---|
| **Link** | [leetcode.com/problems/climbing-stairs](https://leetcode.com/problems/climbing-stairs/) |
| **Difficulty** | Easy |
| **Pattern** | 1D DP · "where did the last step come from?" |

> **One-line takeaway:** To count the ways to reach step `n`, look at the last move: it started from `n - 1` or from `n - 2`.

---

## 1. My first idea: look at the last step

The question that unlocked it: *to stand on step `n`, where could my last move have started?*

- from step `n - 1`, taking 1 step, or
- from step `n - 2`, taking 2 steps.

Those two groups don't overlap and together cover every way up, so:

```python
dp[n] = dp[n - 1] + dp[n - 2]
```

## 2. Where I got stuck

Honestly, nowhere. The recurrence came right away. The only thing left to decide was the base cases:

- `dp[1] = 1`: one way (`1`)
- `dp[2] = 2`: two ways (`1 + 1`, `2`)

## 3. Final solution

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        if n <= 2:                          # base cases: 1 way to reach step 1, 2 ways to reach step 2
            return n
        dp = [0] * (n + 1)                  # dp[i]: number of ways to reach step i
        dp[1] = 1
        dp[2] = 2
        for i in range(3, n + 1):
            dp[i] = dp[i - 1] + dp[i - 2]   # last move was 1 step (from i-1) or 2 steps (from i-2)
        return dp[n]
```

- **Time:** O(n)
- **Space:** O(n)

## 4. Edge cases

- `n = 1`: returned directly. Without the early return, `dp[2] = 2` would be out of range for an array of size 2.

## 5. How I'd explain it in an interview (30 seconds)

> "To reach step `n`, my last move either came from `n - 1` with one step or from `n - 2` with two steps. Those cases don't overlap and cover everything, so the number of ways is `dp[n - 1] + dp[n - 2]`. The base cases are 1 way for step 1 and 2 ways for step 2, and I fill the table bottom-up. O(n) time and O(n) space, or O(1) if I only keep the last two values."

## 6. Connections & what's next

- **Why this easy one matters:** it's the base pattern "the current state comes from a few earlier states". The same question builds the recurrence for [62 Unique Paths](0062-unique-paths.md) (from above or from the left), [91 Decode Ways](0091-decode-ways.md) (the last piece is one digit or two), and 198 House Robber (rob this house or skip it).
- **Next:** the O(1)-space version, keeping only the last two values.
