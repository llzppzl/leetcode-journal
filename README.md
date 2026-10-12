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
| 417 | [Pacific Atlantic Water Flow](0417-pacific-atlantic-water-flow.md) | Graph DFS · reverse search | No visited set (infinite recursion); one shared `result` gave the union, not the intersection; start loops skipped the corners; `seen.add(i, j)`; passed the parameter name `seen` at the call site; couldn't write the return | Climb uphill from each ocean into its own set; answer = `pac & atl`, written explicitly as a loop of `list(p)` |

⏸️ = paused: I understood part of it and stopped before copying a solution I didn't get yet. The note records where I stopped and how I plan to come back.

## Videos

Animated, narrated walkthroughs in Chinese and English. **Watch them on the [video page](https://llzppzl.github.io/leetcode-journal/).**

### From my notes

Each of these picks up where my journal note got stuck.

| # | Problem | What the video covers | Watch |
|---|---|---|---|
| 55 | Jump Game | farthest is a boundary, not a position; prove i is reachable before extending from it; >= for landing exactly on the end | [中文](https://llzppzl.github.io/leetcode-journal/videos/0055-jump-game/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0055-jump-game/en.html) |
| 91 | Decode Ways | each group inherits dp[k-1] / dp[k-2]; one rule for every zero: the single gate and the pair gate; dp[0] = 1 | [中文](https://llzppzl.github.io/leetcode-journal/videos/0091-decode-ways/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0091-decode-ways/en.html) |
| 139 | Word Break | where the recursive result goes; the "aaaab" tree solves "ab" 4 times; memoization (save failures too) and bottom-up dp[i] | [中文](https://llzppzl.github.io/leetcode-journal/videos/0139-word-break/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0139-word-break/en.html) |
| 153 | Find Minimum in Rotated Sorted Array | the invariant "the min stays in [left, right]"; why mid + 1 vs mid; why left = mid loops forever; the special case to delete | [中文](https://llzppzl.github.io/leetcode-journal/videos/0153-find-minimum-in-rotated-sorted-array/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0153-find-minimum-in-rotated-sorted-array/en.html) |
| 300 | Longest Increasing Subsequence | dp[i] must end at nums[i], so the answer is max(dp); subsequence means every earlier j; the max line is king of the hill | [中文](https://llzppzl.github.io/leetcode-journal/videos/0300-longest-increasing-subsequence/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0300-longest-increasing-subsequence/en.html) |
| 417 | Pacific Atlantic Water Flow | climb uphill from each ocean into its own set; the answer is the intersection | [中文](https://llzppzl.github.io/leetcode-journal/videos/0417-pacific-atlantic-water-flow/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0417-pacific-atlantic-water-flow/en.html) |

### More Blind 75 walkthroughs

The Blind 75 problems that are hard, hard to see, or need a specific trick. No journal note yet; each video starts from the natural first idea and shows exactly where it breaks.

| # | Problem | The idea | Watch |
|---|---|---|---|
| 5 | Longest Palindromic Substring | expand around all 2n − 1 centers; the even-length center is the classic miss | [中文](https://llzppzl.github.io/leetcode-journal/videos/0005-longest-palindromic-substring/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0005-longest-palindromic-substring/en.html) |
| 11 | Container With Most Water | two pointers from both ends; why moving the shorter line can never lose the answer | [中文](https://llzppzl.github.io/leetcode-journal/videos/0011-container-with-most-water/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0011-container-with-most-water/en.html) |
| 15 | 3Sum | sort, fix one number, two pointers for the rest; skip duplicates at both levels | [中文](https://llzppzl.github.io/leetcode-journal/videos/0015-3sum/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0015-3sum/en.html) |
| 19 | Remove Nth Node From End of List | two pointers n apart in one pass; a dummy head so removing the head needs no special case | [中文](https://llzppzl.github.io/leetcode-journal/videos/0019-remove-nth-node-from-end-of-list/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0019-remove-nth-node-from-end-of-list/en.html) |
| 23 | Merge k Sorted Lists | the next node is always one of the k fronts: keep them in a min-heap, push (val, i, node) | [中文](https://llzppzl.github.io/leetcode-journal/videos/0023-merge-k-sorted-lists/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0023-merge-k-sorted-lists/en.html) |
| 33 | Search in Rotated Sorted Array | one half is always sorted; check whether the target sits inside that half | [中文](https://llzppzl.github.io/leetcode-journal/videos/0033-search-in-rotated-sorted-array/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0033-search-in-rotated-sorted-array/en.html) |
| 48 | Rotate Image | in place: transpose, then reverse every row; follow one cell to see why | [中文](https://llzppzl.github.io/leetcode-journal/videos/0048-rotate-image/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0048-rotate-image/en.html) |
| 53 | Maximum Subarray | Kadane: the best sum that must end here; a negative running sum is dead weight | [中文](https://llzppzl.github.io/leetcode-journal/videos/0053-maximum-subarray/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0053-maximum-subarray/en.html) |
| 73 | Set Matrix Zeroes | zeroing while scanning spreads; record first, and use the first row/column as the record for O(1) space | [中文](https://llzppzl.github.io/leetcode-journal/videos/0073-set-matrix-zeroes/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0073-set-matrix-zeroes/en.html) |
| 76 | Minimum Window Substring | expand until the window covers t, shrink while it still does; one "missing" counter makes the check O(1) | [中文](https://llzppzl.github.io/leetcode-journal/videos/0076-minimum-window-substring/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0076-minimum-window-substring/en.html) |
| 98 | Validate Binary Search Tree | checking only children is not enough; pass down a (low, high) range; inorder must be increasing | [中文](https://llzppzl.github.io/leetcode-journal/videos/0098-validate-binary-search-tree/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0098-validate-binary-search-tree/en.html) |
| 105 | Construct Binary Tree from Preorder and Inorder | preorder gives the root, inorder splits left from right, the left size splits preorder | [中文](https://llzppzl.github.io/leetcode-journal/videos/0105-construct-binary-tree-from-preorder-and-inorder-traversal/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0105-construct-binary-tree-from-preorder-and-inorder-traversal/en.html) |
| 124 | Binary Tree Maximum Path Sum | the recursion returns a one-sided path but records the path that bends here | [中文](https://llzppzl.github.io/leetcode-journal/videos/0124-binary-tree-maximum-path-sum/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0124-binary-tree-maximum-path-sum/en.html) |
| 128 | Longest Consecutive Sequence | O(n) with a set: only start counting where x − 1 is missing | [中文](https://llzppzl.github.io/leetcode-journal/videos/0128-longest-consecutive-sequence/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0128-longest-consecutive-sequence/en.html) |
| 133 | Clone Graph | an old → new map: the old node has no pointer to its copy; cycles and duplicates are the same lookup | [中文](https://llzppzl.github.io/leetcode-journal/videos/0133-clone-graph/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0133-clone-graph/en.html) |
| 141 | Linked List Cycle | tortoise and hare: inside the loop the gap shrinks by one per step, so they must meet | [中文](https://llzppzl.github.io/leetcode-journal/videos/0141-linked-list-cycle/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0141-linked-list-cycle/en.html) |
| 143 | Reorder List | three known moves: find the middle, reverse the second half, merge alternately | [中文](https://llzppzl.github.io/leetcode-journal/videos/0143-reorder-list/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0143-reorder-list/en.html) |
| 152 | Maximum Product Subarray | track both the max and the min ending here; a negative flips them | [中文](https://llzppzl.github.io/leetcode-journal/videos/0152-maximum-product-subarray/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0152-maximum-product-subarray/en.html) |
| 207 | Course Schedule | take the courses with zero prerequisites left, then see what unlocks; leftovers mean a cycle | [中文](https://llzppzl.github.io/leetcode-journal/videos/0207-course-schedule/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0207-course-schedule/en.html) |
| 212 | Word Search II | put the words in a trie and walk the board and the trie together in one DFS | [中文](https://llzppzl.github.io/leetcode-journal/videos/0212-word-search-ii/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0212-word-search-ii/en.html) |
| 213 | House Robber II | the circle forbids first + last together: run House Robber twice, without first and without last | [中文](https://llzppzl.github.io/leetcode-journal/videos/0213-house-robber-ii/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0213-house-robber-ii/en.html) |
| 230 | Kth Smallest Element in a BST | inorder visits a BST in increasing order; stop the stack walk after k nodes | [中文](https://llzppzl.github.io/leetcode-journal/videos/0230-kth-smallest-element-in-a-bst/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0230-kth-smallest-element-in-a-bst/en.html) |
| 238 | Product of Array Except Self | no division: product of everything left of i times everything right of i | [中文](https://llzppzl.github.io/leetcode-journal/videos/0238-product-of-array-except-self/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0238-product-of-array-except-self/en.html) |
| 295 | Find Median from Data Stream | two heaps: the small half (max-heap) and the large half (min-heap); the median sits on top | [中文](https://llzppzl.github.io/leetcode-journal/videos/0295-find-median-from-data-stream/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0295-find-median-from-data-stream/en.html) |
| 297 | Serialize and Deserialize Binary Tree | preorder alone is ambiguous; add a marker for every missing child | [中文](https://llzppzl.github.io/leetcode-journal/videos/0297-serialize-and-deserialize-binary-tree/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0297-serialize-and-deserialize-binary-tree/en.html) |
| 322 | Coin Change | greedy fails on [1, 3, 4] for 6; dp over amounts: min(dp[x − c] + 1) | [中文](https://llzppzl.github.io/leetcode-journal/videos/0322-coin-change/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0322-coin-change/en.html) |
| 371 | Sum of Two Integers | XOR is the sum without carries, AND << 1 is the carry; Python needs a 32-bit mask | [中文](https://llzppzl.github.io/leetcode-journal/videos/0371-sum-of-two-integers/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0371-sum-of-two-integers/en.html) |
| 377 | Combination Sum IV | it counts ordered sequences; the loop order decides permutations vs combinations | [中文](https://llzppzl.github.io/leetcode-journal/videos/0377-combination-sum-iv/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0377-combination-sum-iv/en.html) |
| 424 | Longest Repeating Character Replacement | a window is fixable when length − max count ≤ k; why a stale max count is still correct | [中文](https://llzppzl.github.io/leetcode-journal/videos/0424-longest-repeating-character-replacement/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0424-longest-repeating-character-replacement/en.html) |
| 435 | Non-overlapping Intervals | remove the fewest = keep the most; sort by end and keep whatever ends earliest | [中文](https://llzppzl.github.io/leetcode-journal/videos/0435-non-overlapping-intervals/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/0435-non-overlapping-intervals/en.html) |
| 1143 | Longest Common Subsequence | don't pick which pointer to move, try both: diagonal + 1 on a match, else max(up, left) | [中文](https://llzppzl.github.io/leetcode-journal/videos/1143-longest-common-subsequence/zh.html) · [English](https://llzppzl.github.io/leetcode-journal/videos/1143-longest-common-subsequence/en.html) |

### High-frequency interview problems

Beyond Blind 75: the problems interviewers ask most often (monotonic stacks, backtracking, LRU, …), same format.

| # | Problem | The idea | Watch |
|---|---|---|---|

The pages are static HTML served by GitHub Pages from `videos/`; the narration clips sit next to each page in `a/`.
