+++
date = '2026-06-26T00:00:00-07:00'
draft = false
title = 'Big O Notation: A Practical Guide to Measuring Code Efficiency'
description = 'A practical walkthrough of Big O notation: what each complexity class means, with runnable code examples.'
categories = ['Mathesis']
tags = ['Algorithms', 'Python', 'PHP', 'Craft CMS', 'Yii2', 'Computer Science', 'Performance']
series = ['Big O']
showTableOfContents = true
+++

## What is Big O Notation?
 Big O Notation is a way of mathematically describing how much memory or time an algorithm uses based on the size of its input. How does the memory or time grow as the input gets bigger? Basically, as your input scales, how well does your algorithm scale. Keep this graph in your head. Y axis is time, X axis is input size (n). To evaluate the performance of your code you'll want to understand Big O at a fairly consistent level. It just takes practice, and I believe in you.

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

O(1) or O(n) - we don't typically care about these. Constant time is not concerning.  Typically is doing a lot of heavy lifting here, but very generally this is linear time. O(1) is always the same, and O(n) is linear, so we don't care about that either. No matter how many inputs you inject into  O(n) the Y axis stays at the same angle of growth. Put it another way, for *both* O(1) and O(n) the total number of operations is always going to remain the same. Total number of operations doesn't always mean the algorithm is performant though. It largely depends on what you're trying to accomplish.

{{< alert "circle-info" >}}
**Patterns that land here:**
- Index access: `items[0]`, `items[-1]`, `items[i]`
- Dict operations: `d[key]`, `d[key] = val`, `del d[key]`
- Set membership: `x in my_set`
{{< /alert >}}

```python
def get_first(items):
    return items[0] if items else None
```

In Python a list is an ordered, mutable collection accessed by integer index. Direct memory lookup, no traversal. Three items or three billion, the cost is the same.

## O(log n) — Logarithmic Time

```text
 time│        _____________
     │   ____/
     │__/
     │
     └──────────────────── n
```

O(log n) time and you're sitting pretty. Any algorithm that lands here is halving the work on every step instead of grinding through it one element at a time. Binary search does it by throwing away half the array each loop. A heap does it through its shape: it's a tree where each level holds twice the nodes of the one above, so it's only log n levels deep, and a push or pop just walks a single value up or down through those levels.


Halving is why `log n` barely grows. Every doubling of the
input costs just one extra step:

```text
            n          steps (log2 n)
           16     ->        4
        1,024     ->       10
    1,000,000     ->      ~20
1,000,000,000     ->      ~30
```

A billion items, found in about thirty steps.

{{< alert "circle-info" >}}
**Patterns that land here:**
- Binary search over a sorted list
- `heapq.heappush()`, `heapq.heappop()`
- Any algorithm that cuts the remaining input in half on every step
{{< /alert >}}

```python
def binary_search(items, target):
    low, high = 0, len(items) - 1

    while low <= high:
        mid = (low + high) // 2
        if items[mid] == target:
            return mid
        if items[mid] < target:
            low = mid + 1
        else:
            high = mid - 1

    return -1
```

A heap reaches `log n` a different way. It's a binary tree filled top to bottom, left to right, and every level holds twice the nodes of the one above. That doubling is the whole trick: `n` values pack into only `log n` levels.

```text
level 0:              3                  1 node
                    /   \
level 1:           5     8               2 nodes
                  / \   / \
level 2:        12  10 15  20            4 nodes
                                         (next level holds 8, then 16 ...)

   n nodes  ->  about log2(n) levels deep
```

`pop()` always returns the smallest value, which is the root. To fill the hole, the last node moves up to the root and *sifts down*, swapping with its smaller child until heap order is restored. It drops one level per step, so it touches at most `log n` nodes.

```text
1. remove the root (3), move the last node (20) into its place

        20
       /  \
      5    8
     / \   /
    12 10 15

2. 20 is bigger than its smallest child (5) -> swap

        5
       /  \
      20   8
     / \   /
    12 10 15

3. 20 still bigger than its smallest child (10) -> swap, then stop

        5
       /  \
      10   8
     / \   /
    12 20 15
```

`push()` is the same move in reverse: drop the new value in the next open slot at the bottom, then bubble it *up*, swapping with its parent while it's smaller. One node per level, up or down. Always `log n`.

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

Linear time is time scaled by input linearly. That is to say the more inputs (n) you have, the higher Y drives up on our scale. This results in a predictable pattern. Inserting into the middle of an array, summing an array, scanning an array for a particular value, these are all O(n). Some of the previous examples may  not seem like they fit with the others. We have to assume the worst case scenario. The worst case scenario is that when inserting into an array we're inserting at the very front, and therefore have to move every other position one over therefore linear operation. In the example below, the time scales linearly with the amount of input (n). Let's say *items* parameter in the function *find_max* below is [1,2,3,4,5], the runtime will be quick, but it's still O(n) because the growth rate doesn't change. If items were to be [1...900000000] we'd have a problem on our hands. The end of the article discusses how to handle O(n) if it's unavoidable. 

{{< alert "circle-info" >}}
**Patterns that land here:**
- Any single loop: `for`, `while`
- Built-ins that scan the whole collection: `max()`, `min()`, `sum()`, `x in my_list`
- `list.insert(0, x)` — inserting at the front shifts everything
- Recursion that processes one element per call

**Watch out:** a single loop is your floor, not your ceiling. What you do inside it determines if you climb higher.
{{< /alert >}}

```python
def find_max(items):
    largest = items[0]
    for item in items:
        if item > largest:
            largest = item
    return largest
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

Merge sort is the textbook example here. It does two things: split and merge. Splitting halves the list every pass, using the same halving trick from binary search, so there are only log n levels before you hit single-element lists. Then the merge step at each level walks every element once to stitch the sorted halves back together, which is O(n). Multiply those two costs: n elements touched at each of log n levels, and you get O(n log n).

```text
[4, 2, 7, 1]            <- level 0
    /        \
[4, 2]      [7, 1]      <- level 1
 /   \       /   \
[4]  [2]   [7]  [1]    <- level 2 (log n levels deep)
```

Merge walks back up, comparing and inserting at each level, touching every element on the way. That's the n part. The log n levels × n work per level is where the name comes from.

{{< alert "circle-info" >}}
**Patterns that land here:**
- `sorted()`, `list.sort()` — Python's Timsort is O(n log n)
- Merge sort, quicksort (average case)
- Any divide-and-conquer that splits the input and does O(n) work at each level
{{< /alert >}}

```python
def merge_sort(items):
    if len(items) <= 1:
        return items
    mid = len(items) // 2
    left = merge_sort(items[:mid])
    right = merge_sort(items[mid:])
    return merge(left, right)


def merge(a, b):
    result = []
    i = j = 0
    while i < len(a) and j < len(b):
        if a[i] <= b[j]:
            result.append(a[i])
            i += 1
        else:
            result.append(b[j])
            j += 1
    result.extend(a[i:])
    result.extend(b[j:])
    return result
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

Quadratic Time polynomial. This is saying for every element, you look at every other element. As input doubles, work quadruples. This can sneak up on you as an input of 100 is still performant, but an input of 1000 bogs everything down. If the input is 10,000 then the number of operations is 100,000,000. It hides until it doesn't. O(n²) checks every pair. 

{{< alert "circle-info" >}}
**Patterns that land here:**
- Nested loops where both loops scale with n
- Checking every pair: `for i in range(n): for j in range(i+1, n):`
- `x in my_list` inside a loop — list scan inside a loop is O(n) times O(n)

**Watch out:** if the inner loop runs a fixed number of times regardless of n, you are still at O(n), not O(n²).
{{< /alert >}}

```python
def has_duplicate(items):
    for i in range(len(items)):
        for j in range(i + 1, len(items)):
            if items[i] == items[j]:
                return True
    return False
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

This algorithm grows exponentially relative to the size of the input. It's basically the inverse of O(log n). As O(log n) per every operation cuts the remaining operations in half, O(2ⁿ) doubles every operation until the algorithm finishes.

{{< alert "circle-info" >}}
**Patterns that land here:**
- A recursive function that calls itself twice per invocation without memoization
- Generating all subsets of a set

**Watch out:** wrapping the call in `@functools.lru_cache` can drop this to O(n) by caching repeated subproblems.
{{< /alert >}}

```python
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
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

**Watch out:** this grows faster than exponential. 10 items means roughly 3.6 million operations. 12 means roughly 479 million.
{{< /alert >}}

```python
def permutations(items):
    if len(items) <= 1:
        return [items]

    result = []
    for i, item in enumerate(items):
        rest = items[:i] + items[i + 1:]
        for perm in permutations(rest):
            result.append([item] + perm)
    return result
```

## N+1 Queries: O(n) Where It Hurts

Picture a product catalog with a faceted filter. You pick a category, and you get the matching products back. Simple enough so far.

The trouble starts with a second query. For every product that comes back, it loads every category attached to that product, and the point of that is to know which filters to grey out when nothing is left behind them. The cost, though, is one extra query per product. That's the N+1.

Keep in mind these are Craft elements, so each one drags its entire object graph along with it. By the time it finished, the page had fired 337 queries and pulled down 99 megabytes, and at that point it basically died.

The fix is to eager-load the categories along with the products, so you get them all in a single query instead of hundreds. While you're at it, hand back the ids rather than the full elements. That alone dropped the queries from 337 down to 8, and the payload from 99 megabytes to under a kilobyte. The page loaded in about 200ms after that.

The N+1 is one of the most common performance bugs out there, and the tricky thing is how well it hides. It looks completely fine in the code, and it looks fine when you run it against ten products. This is actually where Big O lets you down a bit. It'll happily tell you the work is O(n), linear, nothing to worry about. What it won't tell you is that every one of those n steps is a full round trip to the database and back, not a quick read from memory. So on paper the complexity looks harmless, but the real cost is buried in each step, and that's what drags the whole page to a crawl.

```twig
{# 1 query: products in the selected facet
   (e.g. "Playground Structures · Ages 2–5") #}
{% set products = craft.entries()
  .section('products')
  .relatedTo(selectedCategory)
  .all() %}

{# To grey out facets with no products left, walk
   every product and read its categories — each
   .categories access fires its OWN element query,
   hydrating full Craft elements. #}
{% set selectable = [] %}
{% for product in products %}
  {% for cat in product.categories.all() %}
    {% set selectable = selectable|merge([cat.id]) %}
  {% endfor %}
{% endfor %}
{# ~336 products => 337 queries, ~99 MB across. #}
```

```twig
{# Eager-load categories with the products up front,
   so product.categories reads memory, not the DB. #}
{% set products = craft.entries()
  .section('products')
  .relatedTo(selectedCategory)
  .with(['categories'])
  .all() %}

{# Collect JUST the category ids — never serialize the
   full Craft elements back to the client. #}
{% set selectable = [] %}
{% for product in products %}
  {# no .all() — already loaded, no extra query #}
  {% for cat in product.categories %}
    {% set selectable = selectable|merge([cat.id]) %}
  {% endfor %}
{% endfor %}
{# ~8 queries, and a payload of ids => < 1 KB. #}
```

## A Lookup in a Loop: O(n²) to O(1)

This one came out of a report I had to generate from a CSV. The job was straightforward enough: take a file full of SKUs, loop over the rows, and for each one look up its asset in the database, read a property off it, and find where its file actually lived. Then merge the PDFs together and stamp each one with the metadata from that row.

It worked, but on dev it absolutely crawled.

When I traced the lag, it came down to a database query sitting inside the loop. That's one query per row, and each query has to scan for its match. So if you run that N times against N rows, the work is N times N, which is O(n²).

That's the real tell for quadratic. It's fine on a small file, but it falls apart on a real one.

The fix here is a hash map. You pull every asset in a single query up front, then key them all by SKU. Now the lookup is O(1) and the loop itself is just O(n). After that it was back to running in well under a second.

Quadratic is sneaky like that. It'll pass just fine on ten rows and then completely die on a thousand, and there's no runtime fast enough to save you from it. Eventually you just learn to spot that query-in-a-loop pattern before it ever ships.

```php
// Ingest a CSV of SKUs. One element query
// per row — 100+ rows means 100+ queries (and if
// sku isn't indexed, each is a table scan).
foreach ($rows as $row) {
    $asset = Asset::find()
        ->sku($row->sku)
        ->one();

    // then a pdftk process per PDF to stamp it
    $this->stampPdf($asset, $row);
}
```

```php
// 1 query: pull every asset the CSV references.
$skus = array_column($rows, 'sku');
$assets = Asset::find()
    ->sku($skus)
    ->all();

// Index by SKU once — lookups are now O(1).
$bySku = [];
foreach ($assets as $asset) {
    $bySku[$asset->sku] = $asset;
}

// Loop the CSV: a hash hit, not a round-trip.
foreach ($rows as $row) {
    $asset = $bySku[$row->sku] ?? null;
    // lookup is now O(1) — but the pdftk stamp is
    // still one process per PDF: irreducibly O(n)
    $this->stampPdf($asset, $row);
}
```

## When Big O Runs Out

Once the lookups were sorted, every PDF still needed its stamp, whether that's a name, a SKU, or whatever else the run called for, and that's one pdftk process per file.

This part is O(n), and it stays O(n) no matter what you do. You can't stamp a thousand PDFs with fewer than a thousand stamps, so there's no clever algorithm waiting to save you here. Big O is simply out of road at this point.

What you can lean on, though, is that the files are completely independent. Nothing waits on anything else, and that's the opening you need.

So I split the work into batches and pushed them onto Craft's queue. From there, several runners pull batches and chew through them at the same time, separate processes on separate cores, actual parallel execution. The batch size becomes your throttle, so you set it to whatever the box can comfortably take.

The total amount of work didn't shrink at all. The wall clock did. That's really the move when Big O has nothing left to give you.

```php
use craft\queue\BaseJob;

// A queue job that stamps one batch of PDFs.
class StampBatch extends BaseJob
{
    public array $skus = [];

    public function execute($queue): void
    {
        // 1 query, O(1) round trips: one IN(...) pulls the whole batch
        $assets = Asset::find()
            ->sku($this->skus)
            ->all();

        // O(n): one stamp per asset, nothing nested -> linear
        foreach ($assets as $asset) {
            $this->stampPdf($asset); // pdftk, fixed cost per file
        }
    }
}
```

```php
// Break the CSV into batches of X and queue them.
// X is tuned to what the CPU can take; several queue
// runners then chew through the batches in parallel.
$batchSize = 25;

// O(n / batchSize) pushes -> still O(n), just fewer, fatter jobs
foreach (array_chunk($skus, $batchSize) as $batch) {
    Craft::$app->queue->push(new StampBatch([
        'skus' => $batch,
    ]));
}
```
