# 55. Jump Game

| | |
|---|---|
| **Link** | [leetcode.com/problems/jump-game](https://leetcode.com/problems/jump-game/) |
| **Difficulty** | Medium |
| **Pattern** | Greedy · reachability boundary |

> **One-line takeaway:** I never have to choose which jumps to take. I only track how far all the reachable positions *together* can cover, and I only extend that boundary from a position I've already proven reachable.

---

## 1. My first idea: keep track of the farthest I can get

The first question was: can I just keep a running record of the farthest index I can reach? My answer was yes, so the core variable became `farthest`, with this update:

```python
farthest = max(farthest, i + nums[i])
```

## 2. Where I got stuck (three questions, three lessons)

### Question 1: how do I know `i` itself is reachable?

`i + nums[i]` only means something if I can actually stand on `i`. If `i` is unreachable, jumping from it is fantasy. So before trusting the update, I asked what guarantees that `i` is reachable.

The answer is the order of the checks:

```python
if i > farthest:                        # i is past everything I've proven reachable
    return False
farthest = max(farthest, i + nums[i])   # only runs when i <= farthest
```

If the code gets past the `if`, then `i <= farthest`, which **means** `i` is reachable. Only then is it allowed to extend the boundary: first prove `i` is reachable, then expand from it.

**Lesson:** before using a formula, ask what its prerequisite is, and check that the control flow guarantees it.

### Question 2: what does `farthest` physically mean?

I first read it as "where I am now". It isn't. `farthest` is **the farthest index I've proven reachable from the start, using every reachable position I've processed so far.**

With `nums = [2, 3, 1, 1, 4]`:

| `i` | `i + nums[i]` | `farthest` after | What it means |
|---|---|---|---|
| start | | 0 | only index 0 is proven reachable |
| 0 | 2 | 2 | indices 0, 1, 2 are all reachable. I did **not** "jump to 2" |
| 1 | 4 | 4 | the boundary grows to 4, the last index → done |

**Lesson:** `farthest` is a boundary, not a position. Everything in `[0, farthest]` is reachable.

### Question 3: can I return `True` early?

My idea:

```python
if farthest > len(nums) - 1:    # ❌ too strict
    return True
```

The idea is right, but landing **exactly** on the last index already counts as success, so it has to be `>=`.

**Lesson:** for "can I reach index X", reaching X exactly is enough. Check `>` vs `>=` against the exact-landing case.

## 3. The breakthrough: don't choose a path

I don't need to decide how far to jump at each index. All possible paths together cover some prefix `[0, farthest]` of the array, and the only question is whether that prefix grows to the end or gets stuck. That's what makes this greedy instead of a search over paths.

## 4. Final solution

```python
class Solution:
    def canJump(self, nums: list[int]) -> bool:
        farthest = 0                                # farthest index proven reachable so far
        for i in range(len(nums)):
            if i > farthest:                        # i is unreachable → so is everything after it
                return False
            farthest = max(farthest, i + nums[i])   # safe: i is reachable, so extend from it
            if farthest >= len(nums) - 1:           # landing exactly on the last index counts
                return True
        return True
```

- **Time:** O(n): one pass.
- **Space:** O(1)

## 5. Edge cases that changed my thinking

- `[2, 0, 0]`: `farthest` becomes 2, which **is** the last index. Exact landing is success, which is why the early exit uses `>=`.
- `[3, 2, 1, 0, 4]`: `farthest` gets stuck at 3, then `i = 4 > 3`. This is the case the `if i > farthest` check exists for, and it's exactly my question "how do I know `i` is reachable?".

## 6. How I'd explain it in an interview (30 seconds)

> "`farthest` isn't my current position. It's the farthest index I've proven reachable so far. I scan left to right. Before extending it with `i + nums[i]`, I first check that `i` itself is reachable, meaning `i <= farthest`. If it isn't, there's a gap I can never cross, so I return false. As soon as `farthest` reaches the last index, I return true. I never pick an actual jump path, so it's one pass: O(n) time, O(1) space."

## 7. Connections & what's next

- **Same habit as:** Coin Change, where I asked what the prerequisite of `dp[6] = dp[1] + 1` was. Here it's "is `i` reachable?". For every line of a formula: what has to be true for this to be valid?
- **Same "what does this variable mean?" question as:** [300 LIS](0300-longest-increasing-subsequence.md) (`dp[i]`) and [62 Unique Paths](0062-unique-paths.md) (`dp[i][j]`).
- **Next:** 45 Jump Game II (minimum number of jumps). Same boundary idea, but now I count how many times the boundary has to be extended.
