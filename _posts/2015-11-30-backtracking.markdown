---
layout: post
title:  "Whiteboarding: Backtracking"
date:   2015-11-29 13:59:00 -0800
tags: ["#algorithms", "#whiteboarding"]
markdown: redcarpet
---
Today, I spent most of my time working on backtracking problems. For anyone that isn't sure, backtracking is a method that incrementally builds potential solutions to problems and abandons those solutions as soon as it determines the approach is invalid. In fewer words, the algorithm builds 

Here's one I worked on today: **Factor Combinations**.

Write a function that takes an integer n and return all possible combinations of its factors.

Numbers can be regarded as product of its factors. For example,

{% highlight ruby%}
8 -> 2 x 2 x 2
  -> 2 x 4
{% endhighlight %}

**Note**

  1. Each combination's factors must be sorted ascending, for example: The factors of 2 and 6 is [2, 6], not [6, 2].
  2. You may assume that n is always positive.
  3. Factors should be greater than 1 and less than n. 

### Approach
Again, I like to start with these problems by walking through examples. Here's one iteration using `8` as an example.

{% highlight ruby %}
# Walk through one potential solution
num = 8, factor = 2, potential_solution = []

check if 8 is divisible by 2
  add 2 to potentional_solution # [2]
  set num = 8 / 2
  repeat process with num = 4 and factor = 2

check if 4 is divisible by 2
  add 2 to potential_solution # [2, 2]
  set num = 4 / 2
  repeat process with num = 2 and factor = 2

check if 2 is divisible by 2
  add 2 to potential_solution # [2, 2, 2]
  set num = 2 / 2
  end process because [2, 2, 2] is a solution
  pop last value from potential_value # [2, 2]
{% endhighlight %}

So, what happens next?
{% highlight ruby %}
# Next iteration
# Backtrack to see if any other values work
# num = 2, factor = 3, potential_value = [2, 2]
check if 2 is divisible by 3
  # no-op
check if 2 is divisible by 4
  # no-op
check if 2 is divisible by 5
  # no-op
check if 2 is divisible by 6
  # no-op
check if 2 is divisible by 7
  # no-op

# Backtrack because no more solutions with candidate [2, 2]
pop last value from potential_value # [2]

# num = 4, factor = 3, potential_value = [2]
check if 4 is divisible by 3
  # no-op
check if 4 is divisible by 4
  add 4 to potential_solution # [2, 4]
  set num = 4 / 4
  end process because [2, 4] is a solution
  pop last value from potential_value # [2]

check if 4 is divisible by 5
  # no-op

check if 4 is divisible by 6
  # no-op

check if 4 is divisible by 7
  # no-op

# Backtrack because no more solutions with candidate [2]
# num = 5, factor = 2, potential_value = []

check if 5 is divisible by 2
  # no-op

...
# You get the picture
{% endhighlight %}

From the walkthrough, our algorithm becomes clear.
{% highlight javascript %}
var getFactors = function (num) {
  var result = [];
  _getFactors(num, num, 2, [], result);
  return result;
};

var _getFactors = function (num, max, start, candidate, solutions) {
  if (num < 2) {
    if (candidate.length > 1) {
      solutions.push(candidate.slice());
      candidate.pop();
    }
  } else {
    for (var i = start; i <= num; i++) {
      if (num % i === 0 && i !== max) {
        candidate.push(i);
        _getFactors(num / i, max, i, candidate, solutions)
      }
    }
    candidate.pop();
  }
};
{% endhighlight %}

While this backtracking solution works, it seems highly inefficient as there seems to be a good amount of rework. Honestly, this seems ripe for dynamic programming (think bottom up). Imagine this list 

{% highlight ruby %}
  {
    0: []
    1: []
    2: []
    3: []
    4: [2, 2]
    5: []
    6: []
    7: []
    8: [2, 4]
  }
{% endhighlight %}

Given `[2, 4]`, we should be able to deduce `[2, 2, 2]` because we know the factors for 4. Well, that's for another day!

- Walter
