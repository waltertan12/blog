---
layout: post
title: "List Diffs"
date: 2018-05-08 12:00:00 -0500
tags: ["#algorithms", "diffs"]
--- 

Diffs are an extremely important facet of programming. Tools like Git and even Google's "Did you mean X" feature, are built using diffs and [edit distance](http://blog.mikemccandless.com/2011/03/lucenes-fuzzyquery-is-100-times-faster.html).

![Did you mean](/blog/images/did-you-mean.png)

Table of Contents
1. [What's a Diff](#whats-a-diff)
2. [Edit Distance](#edit-distance)
3. [Calculating Edit Distance](#calculating-edit-distance)
4. [Dynamic Programming](#dynamic-programming)
5. [Heuristics](#heuristics)

## What's a Diff
A diff is exactly what it sounds like: it's the difference between A and B.

If we let A be the following:

> The cat is happy

and let B be the following:

> The dog is happy

We would get the following diff:

> The ~~cat~~dog is happy

Pretty straightfoward, right? The difference between the two strings is `cat` and `dog`.

## Edit Distance
Now that we know what a diff is, there's a metric we can use to determine just how 'diffed' something is. That metric is called edit distance.

### Operations
We can think of edit distance as the number of operations we need to perform to make A look like B.

For strings, we have a few operations we can run:
- Insert
  - If we want to change `dog` to `doge`, we insert an `e`
- Delete
  - If we want to change `dodge` to `doge`, we delete a `d`
- Replace
  - If we want to change `dope` to `doge`, we replace `p` with `g`

### Cost
We can also apply a cost to each operation. For example, let's say we use these costs:

|-----------|------|
| Operation | Cost |
|-----------|------|
| Insert    |   1  |
| Delete    |   1  |
| Replace   |   1  |
|-----------|------|

We get the following edit distances:

|-------|--------|---------------|
| A     | B      | Edit Distance |
|-------|--------|---------------|
| dog   | doge   | 1             |
| dodge | doge   | 1             |
| dope  | doge   | 1             |
|-------|--------|---------------|

However, if update the replace operation to cost 2, since it is kind of like deleting and then inserting, we get these edit distances:

|-------|--------|---------------|
| A     | B      | Edit distance |
|-------|--------|---------------|
| dog   | doge   | 1             |
| dodge | doge   | 1             |
| dope  | doge   | 2             |
|-------|--------|---------------|

## Calculating Edit Distance
To calculate edit distance between two strings, `strA` and `strB`, we want to process every character of both strings starting from the right side or the left side.

In the case of starting from the right side, we'd do this:

- Compare the last character of `strA` and `strB`
- If the characters are the same, compare the rest of the `strA` and `strB` except the last characters
  - tl;dr call
  ```javascript
    editDistance(strA.substr(0, lenA - 1), strB.substr(0, lenB - 1))
  ```

- If the characters are different, calculate the edit distance for inserting, deleting and replacing. Then, take the minimum cost.
    - Calculate edit distance for inserting the last character of `strB` to the end of `strA`
    ```javascript
  costOfInsert = editDistance(strA, strB.substr(0, lenB - 1))
    ```
    - Calculate edit distance for deleting the last character of `strA`
    ```javascript
  costOfDelete = editDistance(strA.substr(0, lenA - 1), strB)`
    ```
    - Calculate edit distance for replacing the last character of `strA` with the last character of `strB`
    ```javascript
  costOfReplace = editDistance(strA.substr(0, lenA - 1), strB.substr(0, lenB - 1))
    ```
    - Take the minimum of the edit distances
    ```javascript
  cost = min(costOfInsert, costOfDelete, costOfReplace)
    ```

Here's a code snippet with the full implementation:
```javascript
const editDistance = (strA, strB) => {
  const lenA = strA.length;
  const lenB = strB.length;

  /*
   * Base case 0:
   * If strA is empty, we'd have to insert all of strB, so the cost is the length of strB
   */
  if (lenA === 0) {
    return lenB;
  }

  /*
   * Base case 1:
   * If strB is empty, we'd have to delete all of strA, so the cost is the length of strA
   */
  if (lenB === 0) {
    return lenA;
  }

  // If the last character of strA and strB are the same, move on to rest of the string
  if (strA[lenA - 1] === strB[lenB - 1]) {
    return editDistanceTwo(strA.substr(0, lenA - 1), strB.substr(0, lenB - 1));
  }

  /*
   * Otherwise, we have to perform an insert, delete or replace
   *
   * Recursively calculate the edit distance in those scenarios
   */
  return 1 + Math.min(
    // Delete the last character of strA
    editDistanceTwo(strA.substr(0, lenA - 1), strB),
    // Insert the last character of strB into strA
    editDistanceTwo(strA, strB.substr(0, lenB - 1)),
    // Replace the last character of strA with strB
    editDistanceTwo(strA.substr(0, lenA - 1), strB.substr(0, lenB - 1))
  );
};
```

This algorithm has an $$O\left (3^{max(n, m))}\right )$$ time complexity since we spawn 3 new calls if a character does not match.

Take a look at the call tree we generate:

<p data-height="450" data-theme-id="dark" data-slug-hash="BxVoNo" data-default-tab="result" data-user="waltertan12" data-embed-version="2" data-pen-title="EditDistance" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/BxVoNo/">EditDistance</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Visually, it's pretty obvious there is a lot of repeated work. This means it's a great candidate for dynamic programming!

## Dynamic Programming
### Top down
Since we see a lot of repeated work, we can take the simple approach of caching the results of two unique inputs. Basically, if we've already solved a subtree, we don't need to recalculate it -- we just need to grab the stored value!

Take a look at the implementation:
```javascript
const editDistance = (strA, strB, cache = {}) => {
  const cacheKey = `A:${strA}-B:${strB}`;
  if (cache.hasOwnProperty(cacheKey)) {
    return cache[cacheKey];
  }

  const lenA = strA.length;
  const lenB = strB.length;

  /*
   * Base case 0:
   * If strA is empty, we'd have to insert all of strB, so the cost is the length of strB
   */
  if (lenA === 0) {
    cache[cacheKey] = lenB;

    return cache[cacheKey];
  }

  /*
   * Base case 1:
   * If strB is empty, we'd have to delete all of strA, so the cost is the length of strA
   */
  if (lenB === 0) {
    cache[cacheKey] = lenA;

    return cache[cacheKey];
  }

  // If the last character of strA and strB are the same, move on to rest of the string
  if (strA[lenA - 1] === strB[lenB - 1]) {
    cache[cacheKey] = editDistanceTwo(strA.substr(0, lenA - 1), strB.substr(0, lenB - 1), cache);

    return cache[cacheKey];
  }

  /*
   * Otherwise, we have to perform an insert, delete or replace
   *
   * Recursively calculate the edit distance in those scenarios
   */
  cache[cacheKey] = 1 + Math.min(
    // Delete the last character of strA
    editDistanceTwo(strA.substr(0, lenA - 1), strB),
    // Insert the last character of strB into strA
    editDistanceTwo(strA, strB.substr(0, lenB - 1)),
    // Replace the last character of strA with strB
    editDistanceTwo(strA.substr(0, lenA - 1), strB.substr(0, lenB - 1))
  );

  return cache[cacheKey];
};
```

And check out the new call tree we generate:
<p data-height="450" data-theme-id="dark" data-slug-hash="pVObvj" data-default-tab="result" data-user="waltertan12" data-embed-version="2" data-pen-title="EditDistance 2" class="codepen">See the Pen <a href="https://codepen.io/waltertan12/pen/pVObvj/">EditDistance 2</a> by Walter Tan (<a href="https://codepen.io/waltertan12">@waltertan12</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

For educational purposes, here's the cache that gets generated comparing `dodge` and `doggo`:

```json
{
    "A:-B:": 0,
    "A:-B:d": 1,
    "A:-B:do": 2,
    "A:-B:dog": 3,
    "A:-B:dogg": 4,
    "A:d-B:": 1,
    "A:d-B:d": 0,
    "A:d-B:do": 1,
    "A:d-B:dog": 2,
    "A:d-B:dogg": 3,
    "A:do-B:": 2,
    "A:do-B:d": 1,
    "A:do-B:do": 0,
    "A:do-B:dog": 1,
    "A:do-B:dogg": 2,
    "A:do-B:doggo": 3,
    "A:dod-B:": 3,
    "A:dod-B:d": 2,
    "A:dod-B:do": 1,
    "A:dod-B:dog": 1,
    "A:dod-B:dogg": 2,
    "A:dod-B:doggo": 3,
    "A:dodg-B:": 4,
    "A:dodg-B:d": 3,
    "A:dodg-B:do": 2,
    "A:dodg-B:dog": 1,
    "A:dodg-B:dogg": 1,
    "A:dodg-B:doggo": 2,
    "A:dodge-B:": 5,
    "A:dodge-B:d": 4,
    "A:dodge-B:do": 3,
    "A:dodge-B:dog": 2,
    "A:dodge-B:dogg": 2,
    "A:dodge-B:doggo": 2
}
```

The cache that we've created looks very similar to something that we'll build using the bottom up approach.

### Bottom up
Instead of calling everything from top to bottom and storing the results, we are actually able to calculate all of the distances bottom-up by using a distance matrix.

Take a look at the matrix we get comparing `dodge` and `doggo`:

|       | **''**| **d** | **o** | **d** | **g** | **e** |
| **''**| **0** | 1     | 2     | 3     | 4     | 5     |
| **d** | 1     | **0** | 1     | 2     | 3     | 4     |
| **o** | 2     | 1     | **0** | 1     | 2     | 3     |
| **g** | 3     | 2     | 1     | **1** | 2     | 3     |
| **g** | 4     | 3     | 2     | 2     | **1** | 2     |
| **o** | 5     | 4     | 3     | 3     | 2     | **2** |

*Side note: Notice how each column corresponds to a group in the top-down cache*

Each cell in this matrix reprsents the number of changes that must be made to get from one string to another. For example, matrix[3][3] has the value `0`. That's because it compares the string `do` to `do` which doesn't require any changes.

However, matrix[4][4] has the value `1` because it compares `dog` to `dod`, which requires replacing `g` with `d`.

The final edit distance is then found at the bottom right of the matrix.

Take a look at the implementation:

```javascript
const editDistance = (strA, strB) => {
  const lenA = strA.length;
  const lenB = strB.length;
  const matrix = [];

  for (let i = 0; i <= lenA; i += 1) {
    matrix.push([]);
    for (let j = 0; j <= lenB; j += 1) {
      // If strA is empty, we'd have to insert all of strB, so the cost is the length of strB
      if (i === 0) {
        matrix[i][j] = j;

      // If strB is empty, we'd have to delete all of strA, so the cost is the length of strA
      } else if (j === 0) {
        matrix[i][j] = i;

      // If the last character of strA and strB are the same, move on to rest of the string
      } else if (strA[i - 1] === strB[j - 1]) {
        matrix[i][j] = matrix[i - 1][j - 1];

      // Otherwise, we have to perform an insert, delete or replace
      } else {
        matrix[i][j] = 1 + Math.min(
          matrix[i][j - 1],    // Insert
          matrix[i - 1][j],    // Delete
          matrix[i - 1][j - 1] // Replace
        );
      }
    }
  }

  return matrix[lenA][lenB];
};
```

## Heuristics
With dynamic programming, we are able to reduce the time complexity of the edit distance algorithm from $$O\left (  3^{\max(m, n))}\right )$$ to $$O\left ( m * n \right )$$.

This is a great improvement! However, in reality, quadratic time complexities are still not ideal.

To improve this algorithm, we have to use heuristics.

In the case of lists, we can use a `key` heuristic in which every single item has a unique key associated with it. While this approach limits us to having lists of unique items, it improves the time complexity to $$ O\left(m + n \right) $$

```javascript
const dodge = [
  { key: 'd-1', value: 'd' },
  { key: 'o-1', value: 'o' },
  { key: 'd-2', value: 'd' },
  { key: 'g-1', value: 'g' },
  { key: 'e-1', value: 'e' },
];
const doggo = [
  { key: 'd-1', value: 'd' },
  { key: 'o-1', value: 'o' },
  { key: 'g-1', value: 'g' },
  { key: 'g-2', value: 'g' },
  { key: 'o-2', value: 'o' },
];

/**
 * Transforms a string into a list of objects
 * Each object has the following structure:
 * {
 *   key: string,
 *   value: string,
 * }
 *
 * @param  {string}   str
 * @return {Object[]} list
 */
const buildList = str => {
  const keyMap = {};

  return str.split('')
    .map(char => {
      if (!keyMap[char]) {
        keyMap[char] = 1;
      }

      return { key: `${char}-${keyMap[char]++}`, value: char };
    });
};

/**
 * Takes a list and returns a hash map where each key references a node in the list
 *
 * @param  {Object[]} list
 * @return {Object} 
 */
const processKeys = list => {
  return list.reduce((keyMap, node) => {
    keyMap[node.key] = node;

    return keyMap;
  }, {});
};

const editDistance = (listA, listB) => {
  const keysA = processKeys(listA);
  const keysB = processKeys(listB);

  // If a key exists in listA but not listB, it needs to be deleted
  const numDeletes = listA.reduce((deletes, node) => {
    if (!keysB.hasOwnProperty(node.key)) {
      deletes += 1;
    }

    return deletes;
  }, 0);

  // If a key exists in listB but not listA, it needs to be inserted
  const numInserts = listB.reduce((inserts, node) => {
    if (!keysA.hasOwnProperty(node.key)) {
      inserts += 1;
    }

    return inserts;
  }, 0);

  return numDeletes + numInserts;
};
```

Another noticable downside of this heuristic is the absence of a replace operation. So, when we actually run the edit distance on `dodge` and `doggo`, we get a value of `4` instead of `2`.

It's certainly a less "accurate" result, but in the world of engineering, sometimes approximations are good enough.

Walter
