# LeetCode Journal

Not a solutions dump. Each note records how I actually got to the answer: my first idea, where I got stuck and why, the breakthrough, and how I'd explain it now.

| # | Problem | Pattern | Where I got stuck | Breakthrough |
|---|---|---|---|---|
| 55 | [Jump Game](0055-jump-game.md) | Greedy | Didn't trust `i + nums[i]` until I knew `i` itself was reachable; read `farthest` as "where I am" | `farthest` = farthest index *proven* reachable. Check `i > farthest` first, then extend. No need to choose an actual path |
| 62 | [Unique Paths](0062-unique-paths.md) | 2D DP | Forgot to set row 0 / column 0 to 1 (all zeros); then ran the recurrence from 0, and Python's `-1` index silently gave 56 instead of 28 | Base cases first, and the recurrence starts where they end (`range(1, m)`) so it never overwrites them |
| 70 | [Climbing Stairs](0070-climbing-stairs.md) | 1D DP | Not really stuck | "Where did the last step come from?" gives `dp[n] = dp[n-1] + dp[n-2]` |
| 91 | [Decode Ways](0091-decode-ways.md) | 1D DP | `dp[i-1] + 2` instead of inheriting ways; read `+=` as "adding itself"; zeros (`"10"`, `"01"`, `"2101"`) | ⏸️ *Paused.* Got: `dp[i]` accumulates "single digit" + "two digits". Not yet: one clean rule for zeros |
| 139 | [Word Break](0139-word-break.md) | Recursion → memoization | `None` vs `""` base case; threw away the recursive return value; TLE on `"aaaa…ab"` | ⏸️ *Paused.* Got: store the recursive result in a variable. Not yet: memoization |
| 153 | [Find Minimum in Rotated Sorted Array](0153-find-minimum-in-rotated-sorted-array.md) | Binary search | `left = mid` looped forever; a two-element special case broke on an unrotated array | Invariant "the min is always in `[left, right]`" explains `left = mid + 1` vs `right = mid` |
| 300 | [Longest Increasing Subsequence](0300-longest-increasing-subsequence.md) | 1D DP | What `dp[i]` physically means; why `dp[i]` is on both sides; assumed `j` and `i` had to be adjacent | `dp[i]` = longest subsequence that must *end* at `nums[i]`. Attach `nums[i]` to the best legal earlier `j` |

⏸️ = paused: I understood part of it and stopped before copying a solution I didn't get yet. The note records where I stopped and how I plan to come back.

## Videos

Animated, narrated walkthroughs for the problems I got stuck on. Each one picks up where the journal note paused. Every video comes in Chinese (`zh.html`) and English (`en.html`).

| # | Problem | What the video covers | Watch |
|---|---|---|---|
| 91 | Decode Ways | Group the decodings by their last piece; each group *inherits* `dp[k-1]` / `dp[k-2]`; one rule for every zero: the single gate (`digit != "0"`) and the pair gate (`10 <= pair <= 26`); `dp[0] = 1`; table traces of `226`, `2101`, `10`, `30`, `00` | [中文](videos/0091-decode-ways/zh.html) · [English](videos/0091-decode-ways/en.html) |
| 139 | Word Break | Prefix + rest recursion and where the return value goes; the `"aaaab"` recursion tree (`"ab"` solved 4 times); memoization (three extra lines, including saving failures); the same idea as a bottom-up `dp[i]` | [中文](videos/0139-word-break/zh.html) · [English](videos/0139-word-break/en.html) |

GitHub shows HTML files as source, so to watch: clone or download the repo and open the `.html` file in a browser. The narration clips sit next to it in `a/`.
