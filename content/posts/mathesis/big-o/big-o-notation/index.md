+++
date = '2026-06-26T00:00:00-07:00'
draft = true
title = 'Big O Notation: A Practical Guide to Measuring Code Efficiency'
description = 'A practical walkthrough of Big O notation: what each complexity class means, with runnable code examples.'
categories = ['Mathesis']
tags = ['Algorithms', 'Python', 'PHP', 'Craft CMS', 'Yii2', 'Computer Science', 'Performance']
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

O(1) and O(n) are the ones we rarely sweat. O(1) is flat. Y never moves. One item or a million, same cost. O(n) does rise, but in a straight line. y = x. Double the input, double the work. Predictable. Nothing here jumps off the chart.

```python
def get_first(items):
    return items[0] if items else None
```

## O(log n) — Logarithmic Time

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

## O(n) — Linear Time

```python
def find_max(items):
    largest = items[0]
    for n in items:
        if n > largest:
            largest = n
    return largest
```

## O(n log n) — Linearithmic Time

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

```python
def has_duplicate(items):
    for i in range(len(items)):
        for j in range(i + 1, len(items)):
            if items[i] == items[j]:
                return True
    return False
```

## O(2ⁿ) — Exponential Time

```python
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

## O(n!) — Factorial Time

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

A product catalog with a faceted filter. Pick a category. Get the products back. Simple.

Then a second query fires. For every product returned it loads every category on that product. The point is to know which filters to grey out. The cost is one query per product. N+1.

These are Craft elements. Each one drags its whole object graph. The cascade fired 337 queries and pulled 99 megabytes. The page died.

Eager-load the categories with the products. One query, not hundreds. Return the ids, not the elements. The queries dropped from 337 to 8. The payload went from 99 megabytes to under a kilobyte. Sub-second.

The N+1 is the most common performance bug there is. It looks fine in the code. It looks fine on ten products. It is the first thing I check when a page hangs.

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

An import crawled on dev. A CSV of part numbers fed it. A hundred rows. Sometimes more.

I traced the lag to a database query inside the loop. One per row. The query scans for its match. Run it N times against N rows and the work is N times N. O(n²).

That is the tell for quadratic. Slow on a small file. Dead on a real one.

The fix is a hash map. Pull every asset in one query. Key them by part number. The lookup drops to O(1). The loop drops to O(n). Sub-second after that.

A quadratic hides well. It passes on ten rows. It dies on a thousand. No runtime saves it. You learn to catch the loop before it ships.

```php
// Ingest a CSV of part numbers. One element query
// per row — 100+ rows means 100+ queries (and if
// partNumber isn't indexed, each is a table scan).
foreach ($rows as $row) {
    $asset = Asset::find()
        ->partNumber($row->partNumber)
        ->one();

    // then a pdftk process per PDF to stamp it
    $this->stampPdf($asset, $row);
}
```

```php
// 1 query: pull every asset the CSV references.
$partNumbers = array_column($rows, 'partNumber');
$assets = Asset::find()
    ->partNumber($partNumbers)
    ->all();

// Index by part number once — lookups are now O(1).
$byPart = [];
foreach ($assets as $asset) {
    $byPart[$asset->partNumber] = $asset;
}

// Loop the CSV: a hash hit, not a round-trip.
foreach ($rows as $row) {
    $asset = $byPart[$row->partNumber] ?? null;
    // lookup is now O(1) — but the pdftk stamp is
    // still one process per PDF: irreducibly O(n)
    $this->stampPdf($asset, $row);
}
```

## When Big O Runs Out

Then every PDF needs a stamp. Name, part number, whatever the run calls for. One pdftk process per file.

This part is O(n) and stays O(n). You cannot stamp a thousand PDFs with fewer than a thousand stamps. No algorithm saves you here. Big O is out of road.

But the files are independent. Nothing waits on anything else. That is the opening.

I split the work into batches and pushed them onto Craft's queue. Several runners pull batches and work them at once. Separate processes, separate cores, real parallel execution. The batch size is the throttle. Set it to what the box can take.

The total work did not shrink. The wall clock did. That is the move when Big O has nothing left to give.

```php
use craft\queue\BaseJob;

// A queue job that stamps one batch of PDFs.
class StampBatch extends BaseJob
{
    public array $partNumbers = [];

    public function execute($queue): void
    {
        $assets = Asset::find()
            ->partNumber($this->partNumbers)
            ->all();

        foreach ($assets as $asset) {
            $this->stampPdf($asset); // pdftk
        }
    }
}
```

```php
// Break the CSV into batches of X and queue them.
// X is tuned to what the CPU can take; several queue
// runners then chew through the batches in parallel.
$batchSize = 25;

foreach (array_chunk($partNumbers, $batchSize) as $batch) {
    Craft::$app->queue->push(new StampBatch([
        'partNumbers' => $batch,
    ]));
}
```
