---
layout: post
title: "Top Chef and Inversion of Control"
date: 2020-12-15 12:00:00 -0500
--- 

I binge watched a lot of [Top Chef](https://en.wikipedia.org/wiki/Top_Chef) throughout my quarnatine in 2020. Part of the reason was that I was getting much more into cooking, so it was interesting to watch how chefs thought about and handled food. Another reason was that it as just plain entertaining -- where else can you watch a chefs compete in a mise en place relay race?


![Sheldon is the best](https://static.hollywoodreporter.com/sites/default/files/2013/02/sheldon_simeon_top_chef_seattle.jpg)

_[Sheldon](https://www.instagram.com/chefwonder/) is clearly the best_

However, an issue I took with the show was how some of the challenges were set up. In most scenarios, the chefs were plauged with huge disadvantages. For example, kitchens would be missing burners or ovens, pantries were devoid of fresh produce, chefs were given ingredients which they are allergic to and cannot taste, time would be severely limited, space lacking, etc.

To me, these restrictions felt gimmicky.

If you want to find the best chef, you don't run them through a gamut of odd challenges, you give them adequate time and resources to prepare the best meal. By severely restraining the chefs, you don't get the best chefs, you get the chef that's best at surviving gimmicks. Or at least, that's what I thought.

### Programming to the rescue??
Here's were the inversion of control comes into play. Inversion of control is a programming principle in which all dependencies of a function/class are passed into the function/class.

Let's use chefs for our example:

Here is a case where we do NOT illustrate inversion of control.

```java
class Chef
{
	private List<Knife> knives;

	private Kitchen kitchen;

	private Pantry pantry;

	public Chef()
	{
		knives = new ArrayList<Knife>();
		knives.add(new ButterKnife());
		knives.add(new PlasticKnife());

		kitchen = new Kitchen(new InductionBurner(), new StockPot(), new Bowl());
		pantry = new Pantry(new Uni(), new ShortGrainRice(), new Vinegar())
	}

	public Meal cook()
	{
		// special cooking algorithm
	}
}
```

In most cases, chefs have their own kitchen and pantry to work with. So we represent initializing the chef with their own knives, kitchen, and pantry in the constructor.

Then, we want the chef to cook for us, we call `Chef::cook` without any arguments and they return us a meal!

In this case, `Chef` makes a mean uni nigiri. But how do we compare it to another chef's way of creating an uni nigiri? Maybe the other chef has access to nori and wraps their nigiri in nori. Maybe another chef has access to Hokkaido uni. Maybe another only has access to long grain rice. 

In any case, we can't actually test the thing that's most important about the chef -- the `special cooking algorithm`!

### Inversion of Control
Here's what Top Chef tries to do, which also demonstrates inversion of control.

```java
class Chef
{
	// Chefs still get their own knives ☺️🔪
	private List<Knife> knives;

	public Chef()
	{
		knives = new ArrayList<Knife>();
		knives.add(new ButterKnife());
		knives.add(new PlasticKnife());
	}

	public Meal cook(Kitchen kitchen, Pantry pantry)
	{
		// special cooking algorithm
	}
}
```

Instead of each chef having their own kitchen or pantry, they are given a kitchen or pantry at the time of cooking. This allows Top Chef to test each chef's `special cooking algorithm` as opposed to their kitchen or pantry. So, with respect to inversion of control, Top Chef really is testing how "good" a chef is.
