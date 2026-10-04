# LeetCode Journal

Not a solutions dump. Each note records how I actually got to the answer: my first idea, where I got stuck and why, the breakthrough, and how I'd explain it now.

| # | Problem | Pattern | Where I got stuck | Breakthrough |
|---|---|---|---|---|
| 153 | [Find Minimum in Rotated Sorted Array](0153-find-minimum-in-rotated-sorted-array.md) | Binary search | `left = mid` looped forever; a two-element special case broke on an unrotated array | Invariant "the min is always in `[left, right]`" explains `left = mid + 1` vs `right = mid` |
