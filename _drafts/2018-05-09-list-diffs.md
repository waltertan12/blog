---
layout: post
title: "List Diffs"
date: 2018-05-08 12:00:00 -0500
categories: algorithms
--- 

Diffs are an extremely important facet of programming. Tools like Git and even Google's "Did you mean X" feature, are built using diffs and [edit distance](http://blog.mikemccandless.com/2011/03/lucenes-fuzzyquery-is-100-times-faster.html).

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

<br />
However, if update the replace operation to cost 2, since it is kind of like deleting and then inserting, we get these edit distances:

|-------|--------|---------------|
| A     | B      | Edit distance |
|-------|--------|---------------|
| dog   | doge   | 1             |
| dodge | doge   | 1             |
| dope  | doge   | 2             |
|-------|--------|---------------|

## Calculating Edit Distance
