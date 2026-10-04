# 153. Find Minimum in Rotated Sorted Array

| | |
|---|---|
| **Link** | [leetcode.com/problems/find-minimum-in-rotated-sorted-array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) |
| **Difficulty** | Medium |
| **Pattern** | Binary Search · loop invariant |

> **One-line takeaway:** Binary search isn't "cut the array in half". It's "keep an invariant about where the answer must be, and make sure the range shrinks every iteration."

---

## 1. My first idea: name the thing I'm looking for

Before writing any binary search, I tried to describe the answer in my own words. In a rotated sorted array like `[4, 5, 6, 7, 0, 1, 2]`, the minimum is **the first element after the largest one**. That's the **rotation point**.

That gave me a mental model: the array is split into an *ordered* part and a part that still contains the "break". **I keep moving toward the part that still contains the break.**

## 2. How do I detect the "break" in code?

My first try was `nums[i] < nums[i - 1]`. Then I questioned it myself: in `[4, 5, 6, 7, 0, 1, 2]`, `1` is also smaller than `7`. So why is `0` special?

The answer is that "smaller than the largest" isn't the point. The point is that **the order between two adjacent elements breaks**:

```python
nums[i - 1] > nums[i]   # only happens once, at the rotation point
```

A linear scan for this gives O(n). The problem asks for O(log n), so I needed to turn this idea into binary search.

## 3. Where I got stuck (three bugs, three lessons)

### Bug 1: wrong `mid`

```python
mid = (right - left) // 2          # ❌
```

This only works when `left == 0`. When the range is `[4, 7]`, it gives `mid = 1`, which isn't even inside the range.

```python
mid = left + (right - left) // 2   # ✅ offset from left
```

**Lesson:** `mid` is a position in the current range, not just a length.

### Bug 2: Time Limit Exceeded

```python
if nums[mid] > nums[right]:
    left = mid        # ❌
```

When only two elements are left, `mid == left`, so `left = mid` changes nothing. `left` and `right` never move, and the loop runs forever.

**Lesson:** every iteration **must shrink the search range**. Now I check this for every binary search I write.

### Bug 3: a special case built on a false assumption

To "handle" the two-element case, I added:

```python
if right == left + 1:
    return nums[right]   # ❌
```

It failed on `[11, 13, 15, 17]`: the range ends at `[11, 13]` and returns `13`. My hidden assumption was that the last two elements always straddle the rotation point. That's false when the array isn't rotated.

**Lesson:** when I add a special case, I write down the assumption it relies on and try to break it. Here the right fix was to **delete** the special case, not patch it.

## 4. The breakthrough: why does `left` end on the minimum?

This was the question I kept asking: *why doesn't the loop stop on the largest element instead?*

The answer is an **invariant**: **the minimum is always inside `[left, right]`.**

| Condition | What it tells me | Update | Why this exact update |
|---|---|---|---|
| `nums[mid] > nums[right]` | the break is to the right of `mid` | `left = mid + 1` | `mid` is bigger than something to its right, so it **can't** be the minimum. Safe to exclude. |
| otherwise | `mid..right` is ordered | `right = mid` | `mid` **might** be the minimum, so I keep it (`mid - 1` could throw the answer away). |

Once I saw the `+1` / no-`+1` asymmetry as "can `mid` still be the answer?", the boundaries stopped feeling like memorization.

## 5. A refinement I found on my own

While tracing examples, I noticed something: if the current range is already ordered (`nums[left] < nums[right]`), its leftmost element **is** the minimum, so there's no need to keep cutting.

```python
if nums[left] < nums[right]:
    return nums[left]
```

This connected my first mental model ("move toward the part with the break") to the binary search: when no break is left, I'm done.

## 6. Final solution

```python
class Solution:
    def findMin(self, nums: list[int]) -> int:
        left, right = 0, len(nums) - 1
        while left < right:
            if nums[left] < nums[right]:      # range already sorted → leftmost is min
                return nums[left]
            mid = left + (right - left) // 2
            if nums[mid] > nums[right]:       # break is right of mid; mid can't be min
                left = mid + 1
            else:                             # mid..right sorted; mid may be min
                right = mid
        return nums[left]
```

- **Time:** O(log n): the range strictly shrinks every iteration.
- **Space:** O(1)

## 7. Edge cases that changed my thinking

- `[11, 13, 15, 17]`: not rotated at all. This is the one that exposed the false assumption behind my special case.
- `[2, 1]`: two elements, where `mid == left`. This is the one that exposed the TLE.

## 8. How I'd explain it in an interview (30 seconds)

> "I keep the search range always containing the minimum. If `nums[mid] > nums[right]`, the rotation point is to the right of mid, and mid itself can't be the minimum, so `left = mid + 1`. Otherwise mid to right is sorted and mid might be the answer, so `right = mid`. If the whole range is already sorted, I return the leftmost element right away. Every step strictly shrinks the range, so it's O(log n)."

## 9. Connections & what's next

- **Same skill as:** any "binary search on a condition" problem. The template I took away: *state the invariant → decide whether `mid` can still be the answer → guarantee the range shrinks.*
- **Next:** 33 Search in Rotated Sorted Array (same structure plus a target), and 154 (duplicates, where `nums[mid] == nums[right]` breaks my invariant: how do I still shrink safely?).
