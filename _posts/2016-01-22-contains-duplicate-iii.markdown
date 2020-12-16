---
layout: post
title:  "Whiteboarding: Contains Nearby Almost Duplicate"
date:   2016-01-22 12:07:00 -0700
tags: ["#algorithms", "#whiteboarding"]
markdown: redcarpet
---

Here's a problem I've been working on today -- it's [LeetCode](http://leetcode.com) problem [#220 Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/):

> Given an array of integers **nums**, find out whether there are two distinct indices _i_ and _j_ in the array such that the difference between **nums[i]** and **nums[j]** is at most _t_ and the difference between _i_ and _j_ is at most _k_.

I'm gonna walk you through my whole thought process (that admittedly took a very long time because I literally tried every possible solution). We're gonna start with the most naive solution first. Here's the rundown:

## Approach One: Iterate Through Everything
This is probably the most straightforward approach.

1. Check each element **nums[i]**
2. At each element, do a linear scan from **nums[i]** to **nums[i + k]** and check if that value is within _t_ of **nums[i]**
3. Return true if a value meets the criteria; otherwise, go to the next value
4. Return false if you've iterated through all the values and found no matches 

This approach results in a time complexity of **O(n * k)** because each element requires a **O(k)** search. In the worst case, _k_ could be set equal _n_ making this algorithm **O(n ** 2)**.

An important takeaway from this approach is that we are scanning from a window from _i_ to _i + k_ and checking to see if we have a matching value for **nums[i]**. To be more exact, we are looking for a value `Math.abs(nums[i + k] - nums[i]) <= t`

You can think of this window as an array that holds all the values in that range; and, as we iterate through the array, we unshift the value at _i_ and push in the value at _i + k + 1_.

{% gist waltertan12/b215a3af48d54e2c2c03 %}

## Approach Two: Binary Search Tree
So, what are some options to reduce the time complexity of the previous algorithm? It seems like we'll always have to iterate through each value in **nums** so we'll at most have an **O(n)** solution. But what about the **O(k)** second step. Is there a way to reduce that?

Well, what if we sort the window? Then, we could binary search for a matching value. That search will only take **O(log k)** -- a great improvment over the **O(k)** approach. However, to get the window in sorted order will require a sort which will take **O(k log k)** making our solution **O(n * k log k)**. Not quite what we want...

The key to accomplishing this is to use a different data structure for our window. That is, instead of using an Array of size _k_ for the window, we'll use a Binary Search Tree of size _k_. The BST will let us build, search, and delete in **O(log k)** reducing our old Step 2 to just **O(log k)**. This results in a time-complexity of **O(n log k)** overall!.

{% gist waltertan12/ef5eb88560931b76d4e7 %}

_Note: There isn't actually a BST class in JavaScript and I didn't want to implement one_

## Approach Three: Bucket Sort
We can actually do even better than **O(n log k)** by using a HashMap. As you know, HashMap allows for **O(1)** insertion, deletion, and retrieval (even better than the logarithmic time of a Binary Search Tree). If you carefully design our new window to use a HashMap, we can transform our Step 2 from **O(log k)** to **O(1)**.

Here is the key to make this work: create _k_ buckets where each bucket has a range from **nums[i]** to **nums[i] + t**.

{% gist waltertan12/2804ceedec4da04a9953 %}
