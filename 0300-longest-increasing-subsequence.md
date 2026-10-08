# 300. Longest Increasing Subsequence

| | |
|---|---|
| **Link** | [leetcode.com/problems/longest-increasing-subsequence](https://leetcode.com/problems/longest-increasing-subsequence/) |
| **Difficulty** | Medium |
| **Pattern** | 1D DP · "must end here" state |

> **One-line takeaway:** `dp[i]` isn't "the best answer so far". It's "the best answer **if the subsequence must end at `nums[i]`**". Once that definition was solid, the formula stopped being abstract.

---

## 1. Starting point: what does `dp[i]` even mean?

I was given the definition: `dp[i]` = the length of the longest increasing subsequence **ending at** `nums[i]`. At first this felt completely abstract, so my first question was: what does `dp[i]` *physically* mean?

What it does **not** mean: "the longest increasing subsequence among the first `i` numbers".

What it **does** mean: **if I force the subsequence to end with `nums[i]`, how long can it be?**

With `nums = [2, 5, 3, 7]` and `i = 3` (`nums[i] = 7`), the increasing subsequences that end at 7 are:

```text
[7]
[2, 7]
[2, 5, 7]
[2, 3, 7]
```

So `dp[3] = 3`.

A consequence of this definition: the answer is `max(dp)`, not `dp[-1]`, because the longest subsequence doesn't have to end at the last element.

## 2. Where I got stuck (four questions, four lessons)

### Question 1: why is `dp[i]` on both sides?

```python
dp[i] = max(dp[i], dp[j] + 1)
```

- The `dp[i]` on the **right** is the best I've found **before** this comparison.
- The `dp[i] =` on the **left** is the new best **after** comparing.

E.g. `dp[i]` is 2 so far and this `j` gives a candidate of 4 → `dp[i] = max(2, 4) = 4`.

**Lesson:** `x = max(x, candidate)` means "keep the best so far". It's not a circular definition.

### Question 2: does `dp[j]` get recalculated over and over?

No. It gets **reused**, not recalculated. Since `j < i`, by the time the outer loop reaches `i`, every value from `dp[0]` to `dp[i - 1]` is already final. The inner loop only *reads* them.

**Lesson:** filling `dp` left to right is what makes the earlier values safe to reuse.

### Question 3: aren't `j` and `i` supposed to be next to each other?

This was the key one. The problem asks for a **subsequence**, not a **subarray**:

- **subsequence:** keep the index order, but the indices don't have to be adjacent
- **subarray:** the indices must be contiguous

In `[2, 5, 3, 7]`, `2 → 3 → 7` is valid even though `5` sits between `2` and `3`. That's why `j` ranges over **every** earlier index, not just `i - 1`.

**Lesson:** read "subsequence" vs "subarray" carefully. It decides whether the inner loop looks at one earlier index or all of them.

### Question 4: it feels like `dp[i]` only updates once, so what's the point of `max`?

Tracing `nums = [1, 2, 4, 3, 5]` at `i = 4` (`nums[i] = 5`) showed it updating several times:

| `j` | `nums[j]` | `dp[j]` | candidate `dp[j] + 1` | `dp[4]` after |
|---|---|---|---|---|
| start | | | | 1 |
| 0 | 1 | 1 | 2 | 2 |
| 1 | 2 | 2 | 3 | 3 |
| 2 | 4 | 3 | 4 | 4 |
| 3 | 3 | 3 | 4 | 4 |

That's when I put the idea into my own words: **for different `j`, I pick the longest one to join, if allowed.**

## 3. The breakthrough

For a fixed `i`, I look at every earlier `j`. If `nums[j] < nums[i]`, then `nums[i]` can legally be attached to the end of the best subsequence ending at `j`. So:

```text
dp[i] = 1 + max(dp[j])   over all j < i with nums[j] < nums[i]
        (or just 1 if there is no such j)
```

The `dp[i] = max(dp[i], dp[j] + 1)` line is exactly this, computed one `j` at a time.

## 4. Final solution

```python
class Solution:
    def lengthOfLIS(self, nums: list[int]) -> int:
        dp = [1] * len(nums)                  # dp[i]: longest increasing subsequence that ENDS at nums[i]
        for i in range(len(nums)):
            for j in range(i):                # every earlier index, not just i - 1 (subsequence, not subarray)
                if nums[j] < nums[i]:         # nums[i] can legally extend the subsequence ending at j
                    dp[i] = max(dp[i], dp[j] + 1)
        return max(dp)                        # the best subsequence can end anywhere
```

- **Time:** O(n²)
- **Space:** O(n)

## 5. Examples that changed my thinking

- `[2, 5, 3, 7]`: `2 → 3 → 7` skips over `5`. This is what made "subsequence, not subarray" click.
- `[1, 2, 4, 3, 5]`: watching `dp[4]` go `1 → 2 → 3 → 4` across different `j` is what made the `max` line make sense.

## 6. How I'd explain it in an interview (30 seconds)

> "`dp[i]` is the length of the longest increasing subsequence that must end at `nums[i]`. For each `i`, I check every earlier index `j`, not just the neighbor, because it's a subsequence. If `nums[j] < nums[i]`, I can attach `nums[i]` to the subsequence ending at `j`, so I keep the best `dp[j] + 1`. Every `dp[j]` with `j < i` is already final, so I just reuse it. The answer is the max over all `dp[i]`. O(n²) time, O(n) space."

## 7. Connections & what's next

- **The "must end here" state** is a pattern of its own: when the answer could end anywhere, define the state as "ending exactly here", then take the max at the end.
- **Same "what does this variable physically mean?" question as:** [55 Jump Game](0055-jump-game.md) (`farthest`).
- **Next:** the O(n log n) version (keep the smallest possible tail for each length, then binary search). It reuses the binary search skills from [153](0153-find-minimum-in-rotated-sorted-array.md).
