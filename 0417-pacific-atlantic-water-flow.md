# 417. Pacific Atlantic Water Flow

| | |
|---|---|
| **Link** | [leetcode.com/problems/pacific-atlantic-water-flow](https://leetcode.com/problems/pacific-atlantic-water-flow/) |
| **Difficulty** | Medium |
| **Pattern** | Graph DFS · reverse search from the ocean · multi-source |

> **One-line takeaway:** Don't send water downhill from every cell. Start from each ocean's edge and climb **uphill** (`pre_h <= cur_h`), once per ocean, into **its own** visited set. The answer is the cells in **both** sets.

---

## 1. My first idea: start from the ocean and go inward

I had the right direction from the start: instead of checking every cell, start from the cells that touch an ocean and travel inward in reverse, to see where the water could have come from.

- Reversing "water flows to a lower-or-equal neighbor" means "from the ocean, I can climb to a **higher-or-equal** neighbor", so the check is `pre_h <= cur_h`.
- The Pacific edges are the top row and the left column; the Atlantic edges are the bottom row and the right column.

The idea was right. Almost every bug was in the details around it.

## 2. Where I got stuck

### First version: one shared `result` list

```python
result = []
def dfs(pre_h, i, j):
    ...
    if pre_h <= cur_h:
        result.append([i, j])
        dfs(cur_h, i, j - 1)   # ... four directions
```

Four problems:

1. **No visited check → infinite recursion.** If A can climb to B and B can climb back to A (two cells of equal height), the calls bounce back and forth forever. Before entering a cell, check whether it's already been visited, and mark it as soon as it's entered.
2. **One `result` for both oceans gives the union, not the intersection.** It collects cells that reach the Pacific *or* the Atlantic. The problem asks for *both*. Each ocean needs its own set.
3. **The start loops skipped the corners.** `range(1, m)`, `range(1, m - 1)`, etc. left out cells like `(0, 0)` and `(m - 1, n - 1)`. Every edge should be walked in full (`range(m)`, `range(n)`). Starting from the same cell twice is harmless because the visited check stops it.
4. **`set(result)` crashes** because `result` holds lists, and lists can't go into a set. Store tuples `(i, j)` instead.

**Lesson:** the reverse-search idea is only half the solution. "Each ocean has its own visited set, and the answer is their intersection" is the other half.

### Second version: two sets, but small Python mistakes

After switching to `pac` and `atl`, the remaining bugs were all Python details:

| Bug | Fix | Why |
|---|---|---|
| `heights: [list[list[int]]` | `heights: list[list[int]]` | an extra `[` → `SyntaxError` before anything runs |
| `height[i][j]` | `heights[i][j]` | typo in the variable name |
| `seen.add(i, j)` | `seen.add((i, j))` | `add` takes **one** item, so pack the coordinates into a tuple first |
| `dfs(..., seen)` in the outer loops | `dfs(..., pac)` / `dfs(..., atl)` | `seen` only exists *inside* `dfs` as a parameter name. At the call site, pass the real set |
| `return seen` | return the intersection (see below) | same reason, and the answer is cells in both sets |

The `[list[list[int]]` typo survived three rounds of fixes because I kept fixing the *logic* bugs I knew about, while the error message pointed at line 2 the whole time. A `SyntaxError` means nothing else in the file has run yet.

**Lesson:** read the error's line number first. A syntax error has to be fixed before any logic bug can even show up.

**Lesson:** a parameter name is just a label inside the function. The caller decides what that label points to: passing `pac` makes `seen` mean "the Pacific's set" for that whole search.

## 3. The line I couldn't write: the return

The usual one-liner is:

```python
return [list(p) for p in pac & atl]
```

Taken apart:

| Piece | What it does | Example |
|---|---|---|
| `pac & atl` | set **intersection**: the cells in both sets | `{(0, 4), (1, 3)} & {(1, 3), (2, 2)}` → `{(1, 3)}` |
| `for p in ...` | each `p` is one tuple `(i, j)` | `p = (1, 3)` |
| `list(p)` | turn the tuple into a list, the format the problem wants | `(1, 3)` → `[1, 3]` |
| `[ ... for p in ... ]` | list comprehension: collect the results into a list | `[[1, 3]]` |

My first attempt at the one-liner dropped the outer brackets:

```python
result = list(p) for p in pac & atl     # ❌ SyntaxError: the for has nowhere to go
result = [list(p) for p in pac & atl]   # ✅ the [ ] is what collects the results into a list
```

The square brackets aren't decoration. They *are* the "make a list" part.

The explicit version I can write myself, which does exactly the same thing:

```python
result = []
for p in pac & atl:          # cells reachable from both oceans
    result.append(list(p))   # (i, j) → [i, j]
return result
```

**Lesson:** same habit as the `+=` in [91 Decode Ways](0091-decode-ways.md) and the stored return value in [139 Word Break](0139-word-break.md). Write the explicit loop first; the one-liner is just its compressed form.

## 4. Final solution

```python
class Solution:
    def pacificAtlantic(self, heights: list[list[int]]) -> list[list[int]]:
        m = len(heights)
        n = len(heights[0])
        pac = set()                              # cells the Pacific can climb to
        atl = set()                              # cells the Atlantic can climb to

        def dfs(pre_h, i, j, seen):
            if i < 0 or i > m - 1 or j < 0 or j > n - 1:   # outside the grid
                return
            if (i, j) in seen:                   # already visited for this ocean
                return
            cur_h = heights[i][j]
            if pre_h <= cur_h:                   # reverse of "water flows down": climb up or stay level
                seen.add((i, j))
                dfs(cur_h, i, j - 1, seen)       # left
                dfs(cur_h, i, j + 1, seen)       # right
                dfs(cur_h, i - 1, j, seen)       # up
                dfs(cur_h, i + 1, j, seen)       # down

        for i in range(m):
            dfs(heights[i][0], i, 0, pac)            # left column → Pacific
            dfs(heights[i][n - 1], i, n - 1, atl)    # right column → Atlantic
        for j in range(n):
            dfs(heights[0][j], 0, j, pac)            # top row → Pacific
            dfs(heights[m - 1][j], m - 1, j, atl)    # bottom row → Atlantic

        result = []
        for p in pac & atl:                      # reachable from both oceans
            result.append(list(p))               # (i, j) → [i, j]
        return result
```

- **Time:** O(m · n): each cell is entered at most once per ocean.
- **Space:** O(m · n) for the two sets and the recursion stack.

## 5. Edge cases that changed my thinking

- **Two neighbors of equal height:** this is what makes a missing visited check recurse forever, since each can "climb" to the other.
- **The corners** `(0, n - 1)` and `(m - 1, 0)` touch both oceans, so they're always in the answer. Skipping them in the start loops loses answers.
- `[[1]]`: a single cell touches all four edges → `[[0, 0]]`.

## 6. How I'd explain it in an interview (30 seconds)

> "Checking every cell separately would repeat the same paths, so I reverse it: start from each ocean's edge and climb to neighbors that are higher or equal, because that's where water could have flowed down from. I run this once for the Pacific from the top row and left column, and once for the Atlantic from the bottom row and right column, each with its own visited set. The answer is the intersection of the two sets. Each cell is visited at most twice, so it's O(m·n). For a very large grid I'd switch the DFS to BFS to avoid deep recursion."

## 7. Connections & what's next

- **Same "reverse the search" idea:** instead of asking "can this cell reach X?" for every cell, start from X once and see what it reaches.
- **Same explicit-first habit as:** [91 Decode Ways](0091-decode-ways.md) (`+=`) and [139 Word Break](0139-word-break.md) (store the recursive result).
- **Next:** the BFS version (a queue instead of recursion), and 200 Number of Islands / 130 Surrounded Regions, which use the same flood-fill-from-the-edge structure.
