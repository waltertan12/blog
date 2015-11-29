---
layout: post
title:  "Hash Tables"
date:   2015-09-05 16:13:18 -0800
categories: data structures hash
---
Today was super cool! We traveled further into data structures and 
algorithmic complexity by learning about Hash Maps. Hash Maps (or 
dictionaries) are extremely useful structures that allows user to search
 for information almost instantaneously. To understand the importance, 
I’ll try to give an ELI5 overview of Hash Maps.

Imagine you’re 
trying to look up a specific section in a book you’ve never read before.
 How do you find this section? You have to read the book from the 
beginning and gradually move to the end until you found it. Really time 
consuming right? To remedy this situation, people have added a table of 
contents. A table of of contents lets you look up a specific section and
 jump right to the page. No need to read any portion of the book before 
it.

A Hash Map is basically a table of contents but for data. It 
let’s users look up data instantaneously (or on an O(1) complexity) 
rather than forcing them to iterate through everything (an O(n) 
complexity).

The inner workings of them are a bit more complicated
 but can be broken down into three parts: a hash function, a table, and a
 collision function. (Keep in mind a Hash Map or Hash Table is trying to
 store an item)

*   **Hash Function**: The hash function takes an item, does some crazy math (see hashing functions) to create a _near-unique_ (this is important) index for the item. Basically, this is creating the table contents of a book.
*   **Table**: This is where the data is actually stored. In my book analogy, the data is stored on the page the hash function decided.
*   **Collision Function**:
 Remember how I said a hash function creates a near-unique index? Hashfunctions are not perfect and sometimes gives two different pieces ofdata the same index. To remedy this, the hash map needs a method todifferentiate the data called a collision function. This would besimilar to giving Section numbers to a chapter of book (you know, likeChapter 3 Section 1 and Chapter 3 Section 2).

Well, that’s my attempt to explain Hash Maps! Hopefully it was simple enough to understand.

- Walter