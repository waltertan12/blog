---
layout: post
title:  "Heat Rises"
date:   2015-02-09 18:08:30 -0800
tags: ["#math", "#heat-and-mass-transfer"]
---
I live in the south. It’s pretty warm, even in the winter. Check out the 5 day forecast:
![image](https://lh6.googleusercontent.com/0tipLNbxq-SXbLeetWoiY7YM8Vf6_pAodh5Wc570JALmw4FxR7K80YoQ70gwFnEkkeLFyVHBB_UFl3ULtolTP-UE6PfE_kVfH6E3If-6S5dYcTClOmFLAITH10jTx3_lMQ)

In fact, it’s so warm, one of my co-workers started talking about how he still hasn’t turned on his heat.

> **Tall co-worker**: Dude, we haven’t turned on our heat this past weekend. It’s been really nice.
>
> **Bearded co-worker**: Tell me about it. I haven’t turned on my heat at all yet.
>
> **Tall co-worker**: Really? Don’t you live on the second floor?
>
> **Bearded co-worker**: Yeah.
>
> **Tall co-worker**: I lived on the third floor of that apartment complex and still needed heat.
>
> **Bearded co-worker**: Ahhh that’s weird.
>
> **Tall co-worker**: I assumed the heat would rise from the first and second floor which would make it a better option than the second floor.

That got me thinking. Does the insulation from the third floor keep the second floor warmer? Is it that measurable of a difference? So, I decided develop a model and see. Turns out it’s better to be on the second floor in the winter.

<iframe src="https://plot.ly/~waltertan12/83.embed?width=560&amp;height=420" width="100%" height="420" frameborder="0"></iframe>

While I was making this, I realized the second floor would be the better option, at least in the model. The top floor doesn’t take heat from both the first and second floor, just the floor directly below it. Plus, it has more heat sinks. Less heat sources and more heat sinks equals slightly colder apartment.

Here's a link to the [Excel sheet](https://www.dropbox.com/s/5vzq36xkdr5q9r2/Apartment%20Heating.xlsx?dl=0).

#### Technical stuff / Assumptions

I’ve got two models going: one for the second floor with no heat on, one for the top floor with no heat on. I’ll start off with my assumptions

General Assumptions

*   Assume outside temperature moves sinusoidally from 30-60 F
*   12 AM is the coldest; 12 PM is the warmest
*   Thermal conductivities and heat capacities listed in the Excel sheet
*   Uniform heat distribution in all apartments
*   Wall and roofing are the same material; Floors are composed of different material

All apartment dimensions: 25’ x 30’ x 10’ (LxWxH)

Second Floor Model

*   Assume first and third floor have heat and are at a constant 68 F

Third Floor Model

*   Assume the second floor has heat and is constant at 68 F

Some additional notes. I really, really simplified this 3D heat transfer problem by arranging it as a sum of 1D transfers.

Energy Accumulation = Wall Loss + Wall Loss + Wall Loss + Wall Loss + Floor Loss + Ceiling Loss

I’m really not sure if this is an acceptable solution but it my solutions seemed to pass my sanity check. The things I really want to fix mainly depend on wall material. In my model, walls pass a lot of heat. Currently, I say walls are made solely of drywall and fiberglass insulation but I’m not sure if that’s true. If you've got any suggestions for the sheet, just let me know! I'd love to incorporate it.
