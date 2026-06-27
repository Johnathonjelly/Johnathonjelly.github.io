+++
date = '2026-06-26T00:00:00-07:00'
draft = false
title = 'Big O in Elixir: How Immutability Rewrites the Rules'
description = 'Big O notation in Elixir, where immutable linked lists invert the cost assumptions you learned in imperative languages.'
summary = 'Big O notation in Elixir, where immutable linked lists invert the cost assumptions you learned in imperative languages.'
categories = ['Mathesis']
tags = ['Algorithms', 'Elixir', 'Functional Programming', 'Performance']
series = ['Big O']
showTableOfContents = true
+++

## What is Big O Notation?

Big O Notation is a way of mathematically describing how much memory or time an algorithm uses based on the size of its input. How does the memory or time grow as the input gets bigger? Basically, as your input scales, how well does your algorithm scale. Keep this graph in your head. Y axis is time, X axis is input size (n).

```text
 operations (time)
   ^
   |  O(n!) O(2^n)            O(n^2)
   |    |    |                 /
   |    |    |                /
   |    |    |               /
   |    |    |              /       O(n log n)
   |    |    |             /       /
   |    |    |            /      /
   |    |    |           /     /         O(n)
   |    |    |          /    /        /
   |    |    |         /   /      /
   |    |    |        /  /     /       O(log n)
   |    |    |       / /   /     ______
   |     \    \     // / __---
   |      \    \   //_--
   |       \____\_//________________________ O(1)
   +--------------------------------------------->
                  input size (n)
```

## O(1) — Constant Time

```text
 time│
     │─────────────────────
     │
     └──────────────────── n
```

O(1) or O(n) - we don't typically care about these. Typically is doing a lot of heavy lifting here, but very generally this is linear time. O(1) is always the same, and O(n) is linear, so we don't care about that either. No matter how many inputs you inject into  O(n) the Y axis stays at the same angle of growth.

{{< alert "circle-info" >}}
**Patterns that land here:**
- Pattern matching the head: `[head | _tail]`
- `hd(list)`, `tl(list)`, `tuple_size(tuple)`, `elem(tuple, i)`
- Map access: `map[key]`, `Map.get/2`, `Map.put/3`

**Watch out:** `Enum.at(list, i)` is not O(1), it is O(n). Elixir lists are linked lists, so reaching index `i` means walking `i` nodes.
{{< /alert >}}

```elixir
# Matching the head is O(1); Enum.at(list, i) would not be.
def first([head | _tail]), do: head
def first([]), do: nil
```

## O(log n) — Logarithmic Time

```text
 time│        _____________
     │   ____/
     │__/
     │
     └──────────────────── n
```

Elixir's linked list structure means you cannot jump to the middle of a list. To reach element 500 you have to walk through the first 499 nodes. That makes binary search on a list O(n), not O(log n). If you need logarithmic lookup, you need a tuple. Tuples are stored contiguously in memory, so `elem(tuple, i)` is O(1), and the implementation below can cut the search space in half on every step the same way binary search does in any other language.

{{< alert "circle-info" >}}
**Patterns that land here:**
- Binary search over a **tuple** (not a list, list access is O(n))
- Erlang's `:gb_trees` and `:gb_sets` operations
- Any algorithm that halves the search space each step

**Watch out:** there is no built-in O(log n) binary search on Elixir lists. If you need it, convert to a tuple first.
{{< /alert >}}

```elixir
# Lists give O(n) access, so binary search needs a tuple.
def binary_search(tuple, target) do
  search(tuple, target, 0, tuple_size(tuple) - 1)
end

defp search(_tuple, _target, low, high)
     when low > high,
     do: -1

defp search(tuple, target, low, high) do
  mid = div(low + high, 2)

  case elem(tuple, mid) do
    ^target -> mid
    v when v < target -> search(tuple, target, mid + 1, high)
    _ -> search(tuple, target, low, mid - 1)
  end
end
```

## O(n) — Linear Time

```text
 time│                   /
     │                /
     │             /
     │          /
     │       /
     │    /
     │ /
     └──────────────────── n
```

Single-pass operations are your bread and butter in Elixir. `Enum.map`, `Enum.filter`, `Enum.reduce` all walk the list once and hand you back a new one. That is the immutability model: you are never sorting or filtering in place, you are always building something fresh. One important difference from Python: `length(list)` in Elixir is O(n). It has to walk every node to count them. Python's `len()` is O(1) because lists store their own length. Keep that in mind any time you find yourself calling `length/1` inside a loop.

{{< alert "circle-info" >}}
**Patterns that land here:**
- `Enum.map/2`, `Enum.filter/2`, `Enum.reduce/3`, `Enum.each/2`
- `length(list)` — walks every node, unlike Python's `len()` which is O(1)
- Recursive functions that process one element per call

**Watch out:** a single pass is your floor. What you do inside it determines if you climb higher.
{{< /alert >}}

```elixir
def find_max([head | tail]) do
  Enum.reduce(tail, head, &max/2)
end
```

## O(n log n) — Linearithmic Time

```text
 time│                  /
     │               _/
     │             _/
     │           _/
     │         _/
     │      __/
     │ ____/
     └──────────────────── n
```

Same algorithm as the Python version, same O(n log n), but Elixir's linked list structure adds a hidden cost worth knowing about. In Python, `len(items)` is O(1) because the list stores its own length. In Elixir, `length(list)` has to walk every node to count them, which is O(n). So every recursive call in `merge_sort` pays O(n) just to find the midpoint. That sounds alarming, but it doesn't change the complexity class: O(n) work per level × O(log n) levels is still O(n log n). It's extra constant-factor overhead, not a class bump.

The other Elixir-specific detail is in the merge step. Each `[h1 | merge(t1, right)]` prepends to a new list rather than writing into an existing one. That's immutability at work. You're not sorting in place; you're building a fresh sorted list on the way back up. The old lists get garbage collected. It's idiomatic Elixir, just a different mental model than mutating an array in place.

{{< alert "circle-info" >}}
**Patterns that land here:**
- `Enum.sort/1`, `Enum.sort_by/2` — both O(n log n)
- Any divide-and-conquer that splits and merges

**Watch out:** keep sort comparators cheap. Calling `length/1` inside a comparator adds hidden O(n) cost per comparison.
{{< /alert >}}

```elixir
def merge_sort([]), do: []
def merge_sort([x]), do: [x]

def merge_sort(list) do
  {left, right} = Enum.split(list, div(length(list), 2))
  merge(merge_sort(left), merge_sort(right))
end

defp merge([], right), do: right
defp merge(left, []), do: left

defp merge([h1 | t1] = left, [h2 | t2] = right) do
  if h1 <= h2 do
    [h1 | merge(t1, right)]
  else
    [h2 | merge(left, t2)]
  end
end
```

## O(n²) — Quadratic Time

```text
 time│              /
     │           _/
     │         _/
     │      __/
     │   __/
     │__/
     └──────────────────── n
```

The classic trap in Elixir is calling `Enum.member?/2` inside a recursive function or `Enum.map`. `Enum.member?/2` walks the entire list looking for a match, which is O(n). Do that once for every element in an n-element list and you have O(n) work happening n times. That is O(n²). Input doubles, work quadruples. It feels fine at 100 elements and falls apart at 10,000.

{{< alert "circle-info" >}}
**Patterns that land here:**
- `Enum.member?/2` called inside `Enum.map` or a recursive function
- Nested `for` comprehensions over the same list

**Watch out:** both levels have to scale with n to be quadratic. A fixed-size inner operation keeps you at O(n).
{{< /alert >}}

```elixir
# Enum.member?/2 is O(n), called for each element.
def has_duplicate?([]), do: false

def has_duplicate?([head | tail]) do
  Enum.member?(tail, head) or has_duplicate?(tail)
end
```

## O(2ⁿ) — Exponential Time

```text
 time│             |
     │            /
     │          _/
     │        _/
     │    ___/
     │___/
     └──────────────────── n
```

Same shape as the Python version. A recursive function that calls itself twice per invocation without memoization doubles its work on every step. It is basically the inverse of O(log n): where logarithmic time cuts the remaining work in half on every step, exponential time doubles it. In Python you can throw `@functools.lru_cache` on it and call it fixed. In Elixir, functions are stateless by design, so you have to carry the cache yourself, either as an accumulator map threaded through the recursion or stored in ETS if you need it to survive across processes.

{{< alert "circle-info" >}}
**Patterns that land here:**
- A recursive function that spawns two recursive calls per invocation without memoization

**Watch out:** memoization in Elixir means passing an accumulator map or using ETS. Pattern guards alone do not cache results.
{{< /alert >}}

```elixir
def fib(n) when n <= 1, do: n
def fib(n), do: fib(n - 1) + fib(n - 2)
```

## O(n!) — Factorial Time

```text
 time│         |
     │         |
     │        /
     │      _/
     │   __/
     │__/
     └──────────────────── n
```

This is a nightmare scenario if you find anything in your code hitting factorial O(n!) time. Factorial means you multiply n by every integer below it: 5! = 5 × 4 × 3 × 2 × 1 = 120. The growth is brutal. The traveling salesman problem is the classic example: you have N cities and need to find the shortest route through all of them. You can not rule out any path until you have compared them all, so you have to check every possible ordering. With 10 cities that is 3,628,800 paths. With 12 it is 479,001,600.

{{< alert "circle-info" >}}
**Patterns that land here:**
- Generating all permutations of a list
- Brute-forcing every possible ordering

**Watch out:** `for element <- list, rest <- permutations(list -- [element])` is the canonical Elixir permutation pattern, and it is O(n!). 10 items means roughly 3.6 million operations. 12 means roughly 479 million.
{{< /alert >}}

```elixir
def permutations([]), do: [[]]

def permutations(list) do
  for element <- list,
      rest <- permutations(list -- [element]) do
    [element | rest]
  end
end
```

## A Note on N+1

Elixir gives you no free pass. Lazily loading an association inside a loop fires one query per row. Same O(n) round-trips. Ecto's fix is `Repo.preload/2`, or a `preload:` in the query, which pulls them in one batch. I break down a real-world N+1, and what it cost, in [Big O Notation](/posts/big-o-notation/#n1-queries-on-where-it-hurts).
