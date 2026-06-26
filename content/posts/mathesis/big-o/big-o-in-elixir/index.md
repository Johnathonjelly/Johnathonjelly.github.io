+++
date = '2026-06-26T00:00:00-07:00'
draft = true
title = 'Big O in Elixir: When the Data Structures Fight Back'
description = 'Big O notation in Elixir, where linked lists and immutability change what "constant time" even means.'
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

```elixir
# Matching the head is O(1); Enum.at(list, i) would not be.
def first([head | _tail]), do: head
def first([]), do: nil
```

## O(log n) — Logarithmic Time

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

```elixir
def find_max([head | tail]) do
  Enum.reduce(tail, head, &max/2)
end
```

## O(n log n) — Linearithmic Time

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

```elixir
# Enum.member?/2 is O(n), called for each element.
def has_duplicate?([]), do: false

def has_duplicate?([head | tail]) do
  Enum.member?(tail, head) or has_duplicate?(tail)
end
```

## O(2ⁿ) — Exponential Time

```elixir
def fib(n) when n <= 1, do: n
def fib(n), do: fib(n - 1) + fib(n - 2)
```

## O(n!) — Factorial Time

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
