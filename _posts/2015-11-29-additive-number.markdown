---
layout: post
title:  "Whiteboarding: Additive Numbers"
date:   2015-11-29 13:59:00 -0800
tags: ["#algorithms", "#whiteboarding"]
markdown: redcarpet
---
I've been working through some whiteboarding problems lately so I thought I might document some interesting ones here. Today, I came across the problem 'Additive Number'.

#### **Problem Statement**

Additive number is a string whose digits can form additive sequence.

A valid additive sequence should contain at least three numbers. Except for the first two numbers, each subsequent number in the sequence must be the sum of the preceding two.
{% highlight ruby %}
"112358" -> 1 + 1 = 2
            1 + 2 = 3
            2 + 3 = 5
            3 + 5 = 8
{% endhighlight %}

**Note**: Numbers in the additive sequence *cannot* have leading zeros, so sequence `"1203"` or `"1023"` is invalid.

#### **Approach**
**First**

For this problem, I had to spend a lot of time just walking through small examples. I started off with `"1123"` -- probably the most straightforward case possible.

In this case, I could treat each character as it's own digit and just walk through the string. My algorithm would be the following:

  1. Take the numbers at `i` and `j` of the string where `i = 0` and `j = 1`
  3. Check if `string[i] + string[j] == string[j + 1]`
  4. - If equal, set `i = j` and `j = j + 1` and repeat until `j` is out of bounds
     - Otherwise, return false

**Second**

Fairly straightfoward, however, this solution does not check for digits > 9 in the string. What happens if there's the case `"1910"`? It would return false because `1 + 9 != 1`. To remedy this, I had to modify the initial approach:
  
  1. Set `num1 = string[i]` and `num2 = string[j]` where `i = 0` and `j = 1`
  2. Set `sum = num1 + num2`
  3. Check if `sum == string[j...sum.length]`
  4. - If equal, set `num1 = num2`, `num2 = sum`, and `j = j + sum.length`
     - Otherwise, return false

This pretty much gets us there! With a few modifications, we can walk through every possible example (not ideal) and check if the string is valid. Here's my code (Note: I actually did the validity check recursively...):

{% highlight javascript %}
var isAdditiveNumber = function(num) {
    var num1, num2, len = num.length;
    for (var idx1 = 1; idx1 <= Math.floor(len / 2); idx1++) {
        num1 = parseInt(num.slice(0, idx1));
        for (var idx2 = 1; Math.max(idx1, idx2) <= len - idx1 - idx2; idx2++) {
            if (num[0] === "0" && idx2 > 1) { return false; }
            if (num.charAt(idx1) === "0" && idx2 > 1) { break; }
            num2 = parseInt(num.slice(idx1, idx1 + idx2));
            if (validNumber(num1, num2, idx1 + idx2, num)) {
                return true;
            }
        }
    }
    return false;
};

var validNumber = function(num1, num2, startIdx, num) {
    if (startIdx === num.length) { return true; }
    num2 = num1 + num2;
    num1 = num2 - num1;
    var sum = "" + num2; // turn it into a string
    return num.slice(startIdx, startIdx + sum.length ) === sum && validNumber(num1, num2, startIdx + sum.length, num);
}
{% endhighlight %}

 
- Walter
