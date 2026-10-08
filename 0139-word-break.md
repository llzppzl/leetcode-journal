# 139. Word Break

| | |
|---|---|
| **Link** | [leetcode.com/problems/word-break](https://leetcode.com/problems/word-break/) |
| **Difficulty** | Medium |
| **Pattern** | Recursion → memoization (top-down DP) |
| **Status** | ⏸️ Paused: my recursion is correct but too slow, and memoization still feels abstract |

> **One-line takeaway (so far):** "Match a prefix, then solve the rest" is the right idea. But the recursion is only correct if I actually use what the recursive call returns.

---

## 1. My first idea: cut off a valid prefix, recurse on the rest

I didn't start from DP. My natural first thought was: scan the prefixes of `s`; when one is in `wordDict`, cut it off and recursively solve what's left.

```text
current prefix is a valid word
→ solve the remaining string
```

My first structure:

```python
if s == "":
    return True
for i in range(len(s)):
    if s[:i + 1] in wordDict:
        self.wordBreak(s[i + 1:], wordDict)
return False
```

The core idea was right. The details weren't.

## 2. Where I got stuck (three bugs, three lessons)

### Bug 1: the base case was `None`

```python
if s is None:     # ❌
```

Slicing a string all the way down doesn't produce `None`. It produces the empty string `""`.

```python
if s == "":       # ✅ nothing left to match → success
    return True
```

**Lesson:** a fully sliced string is `""`, not `None`.

### Bug 2: `()` vs `[]`

```python
s(i + 1:len(s))   # ❌ treats s like a function
s[i + 1:]         # ✅ slicing
```

Same mistake as `height(left)` vs `height[left]` in Container With Most Water.

**Lesson:** `()` calls a function; `[]` indexes or slices.

### Bug 3: `"leetcode"` returned `False`

I expected `"leet"` → `"code"` → `""` → `True`, so the outer call should be `True` too. But:

```python
self.wordBreak(s[i + 1:], wordDict)   # ❌ the result is computed, then thrown away
```

The inner call did return `True`, but nothing caught it. The outer call kept looping and eventually hit `return False`.

The usual fix is `if self.wordBreak(remaining, wordDict): return True`, but that compressed version felt too abstract to me. What made it click was storing the result in a variable first:

```python
remaining = s[i + 1:]
remaining_result = self.wordBreak(remaining, wordDict)
if remaining_result == True:
    return True
```

**Lesson:** my real difficulty with recursion is *where the return value goes*. Putting it in a named variable makes that visible. Write the explicit version first, compress later.

## 3. The version I have now (correct, but too slow)

```python
class Solution:
    def wordBreak(self, s: str, wordDict: list[str]) -> bool:
        if s == "":                                   # sliced all the way down → every piece matched
            return True
        for i in range(len(s)):
            prefix = s[:i + 1]
            if prefix in wordDict:                    # valid first word → try to solve the rest
                remaining = s[i + 1:]
                remaining_result = self.wordBreak(remaining, wordDict)
                if remaining_result == True:          # the rest can be split too → whole string works
                    return True
        return False                                  # no prefix led to a full split
```

- **Time:** exponential in the worst case (see below).

## 4. Why it gets Time Limit Exceeded

It failed on input like `s = "aaaa…ab"` with `wordDict = ["a", "aa", "aaa", …]`.

There are many different ways to cut the leading `a`s, and **many of them land on exactly the same remaining suffix**. A small version: `s = "aaaab"`, `wordDict = ["a", "aa", "aaa"]`. The suffix `"ab"` is reached by four different cuts:

```text
"a"  + "a"  + "a"  → remaining "ab"
"a"  + "aa"        → remaining "ab"
"aa" + "a"         → remaining "ab"
"aaa"              → remaining "ab"
```

My recursion solves `"ab"` from scratch every one of those times, and fails every time. With longer inputs, the number of paths into the same suffix explodes, so the total work is exponential.

## 5. Where I paused

The fix is memoization: remember the answer for each suffix (a `memo = {}` dict, or a `failed = set()` of suffixes already known to fail) so each one is solved only once. When I saw it, it still felt too abstract, so I paused here instead of copying it.

- **What I understand:** prefix match → recursively solve the remaining suffix.
- **What I don't yet:** why repeated subproblems appear → how memoization avoids them → how this recursion becomes top-down DP.

## 6. Plan for when I come back

- Don't start with the memo code.
- First, draw the recursion tree for a small input like `"aaaab"` by hand and **circle every time the same suffix shows up**.
- Only then ask: *if I had written down the answer the first time I solved `"ab"`, which of these calls could I skip?* That question is memoization.

## 7. Connections

- **Same "prefix + rest" structure as:** [91 Decode Ways](0091-decode-ways.md), where the first piece is one or two digits instead of a dictionary word.
- **Same explicit-first habit as:** the `+=` in [91 Decode Ways](0091-decode-ways.md). I understand compressed code once I've written it out step by step.
