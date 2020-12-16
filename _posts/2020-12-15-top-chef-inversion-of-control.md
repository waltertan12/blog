---
layout: post
title: "Top Chef as a Testing Framework"
date: 2020-12-16 12:00:00 -0500
keywords: "topchef,testing"
categories: ["#topchef", "#testing"]
--- 

I binge watched a lot of [Top Chef](https://en.wikipedia.org/wiki/Top_Chef) throughout my quarantine in 2020. Part of the reason was that I was getting much more into cooking, so it was interesting to watch how chefs thought about and handled food. Another reason was that it as just plain entertaining -- where else can you watch a chefs compete in a mise en place relay race?


![Sheldon is the best](https://static.hollywoodreporter.com/sites/default/files/2013/02/sheldon_simeon_top_chef_seattle.jpg)

*[Sheldon](https://www.instagram.com/chefwonder/) is clearly the best*

However, an issue I took with the show was how some of the challenges were set up. In most scenarios, the chefs were plauged with huge disadvantages. For example, kitchens would be missing burners or ovens, pantries were devoid of fresh produce, chefs were given ingredients which they are allergic to and cannot taste, time would be severely limited, space lacking, etc.

To me, these conditions felt gimmicky, and it led to ask the question: what is Top Chef really testing for?

### Test Goals
Traditionally, when scouting for the best chefs, chefs are tightly coupled to their restaurants. Part of this is because one can't taste the food from a chef without going to a place where they their serve food i.e. their restaurant. But also, the role of the chef is multi-faceted. A chef's job is to produce food, yes, but a chef must also source ingredients, manage their kitchen staff, design and test menu items, etc -- it's much more than just cooking. And being so, a chef's success is tied to the success of their restaurant and vice versa.

So, is the best chef a someone who runs the best restaurants?

Looking at the Michelin guide or the James Beard foundation sizes up chefs, it does seem that way.

So, why does Top Chef test its chef the way it does?

### Programming to the rescue?
Let's take a brief attempt to model chefs in a programmatic way. In this case, we model chefs to be in complete control things like their kitchen, staff, and pantry. This inherently couples their `cook`ing to things they provision themselves:

```java
// 🐀
class TinyChef
{
	private List<Knife> knives;

	private Kitchen kitchen;

	private Pantry pantry;

	private List<Chef> staff;

	public Chef()
	{
		knives = new ArrayList<Knife>();
		knives.add(new ButterKnife());
		knives.add(new PlasticKnife());

		kitchen = new Kitchen(new InductionBurner(), new StockPot(), new Bowl());
		pantry = new Pantry(new SantaBarbaraUni(), new ShortGrainRice(), new Vinegar())
		staff = new ArrayList<Chef>(new ChefNoz());
	}

	public Meal cook()
	{
		// special cooking algorithm
		// ...
		return uniNigiri;
	}
}
```

Let's say `TinyChef` makes an objectively great uni nigiri, given his pantry and [chef Noz](https://www.sushinoz.com/#chef2). But how do we compare it to another chef's way of creating an uni nigiri? Maybe the other chef has access to nori and wraps their nigiri with nori. Maybe another chef has access to Bafun uni. Maybe another only has access to long grain rice.

Again, it begs the question, what are we really testing? Who can find better ingredients? Who has a better kitchen? A better sous chef? In any case, we can't be certain that we're testing the thing that's most important about the chef -- the `special cooking algorithm`! 

### Inversion of Control
Here's where Top Chef model, or inversion of control, comes to play.

Inversion of control is a programming principle in which all dependencies of a function/class are passed into the function/class. To illustrate what this means, let's refactor our `TinyChef` to implement inversion of control.

```java
class TinyChef
{
	// 🐀 still gets his own set of knives ☺️
	private List<Knife> knives;

	public Chef()
	{
		knives = new ArrayList<Knife>();
		knives.add(new ButterKnife());
		knives.add(new PlasticKnife());
	}

	// However, the kitchen, pantry, staff are all given to the chef!
	public Meal cook(Kitchen kitchen, Pantry pantry, List<Chef> staff, int timeLimit)
	{
		// special cooking algorithm...
	}
}
```

Instead of each chef having their own kitchen or pantry, they are given a kitchen or pantry at the time of cooking. What this allows allows Top Chef to do is standardize variables that affect the output of each chef and zero in on what matters most -- their cooking!

Have access to A5 wagyu from Miyazaki prefacture? Who cares. Have amazing produce because you're based in California? 🤷. Have the latest in kitchen technology? Good for you. But on Top chef, it doesn't matter at all. In essence, what Top Chef selects for is not the chef's ability to source or to manage, but rather, the `special cooking algorithm` that resides within each chef. 

### Unit Testing
To close out this ridiculous metaphor, we can view Top Chef as a suite of unit tests with insane edge cases (think cooking a meal in 15 minutes, cooking a meal with ingredients found in a vending machine, etc). And, if a chef pass all the unit tests, they win!

Obviously, this was a silly exercise but it was still fun to think through. And, it does still make me wonder how someone like Thomas Keller or René Redzepi would perform on the show.

- Walter
