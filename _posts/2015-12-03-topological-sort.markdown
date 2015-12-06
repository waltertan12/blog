---
layout: post
title:  "Topological Sort"
date:   2015-12-01 11:59:00 -0800
categories: algorithms
markdown: redcarpet
---
Today I want to write a little bit about topological sorting. Topological sort deals with graphs and essentially orders vertices of the graph on dependencies.

Take this simple graph for example
{% highlight ruby %}
A -----> B
|        |
V        V
C -----> D
{% endhighlight %}

The topological ordering of this could be `A B C D` or `A C D B` depending on how the graph is traversed. Why is this? And what does ordering vertices by dependencies actually mean?

Well, take a look at node `A`. Notice how there are no incoming arrows? That means it is not dependent on any other node. Now look at `B` and `C`. They both have an incoming arrow from `A` which makes them dependent on `A`. Finally, look at `D`. This node has arrows from `B` *and* `C` which makes it dependent on those two nodes.

What applications can come from this? Scheduling, recipes, build instructions... A whole bunch!

Imagine you have to take a bunch of classes for your major and each class has some prerequisite. How do you determine what class to take first? Try topological sort!


{% highlight ruby %}
Chemistry -----> Thermo -> PChem
                  ^          ^
                  |          |
MultiVarCalc --> Physics  DiffEq
    |                       ^
    |                       |
    -------------------------
{% endhighlight %}