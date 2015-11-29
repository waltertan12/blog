---
layout: post
title:  "Fun With Data"
date:   2015-05-12 17:28:09 -0800
categories: data crossfit
---
Long time no post, yeah?

Well, this should be a pretty good one.

I’ve taken a brief break from Ruby on Rails to do a little bit of work with Python. To be honest, I was a getting slightly bored / frustrated with web development so I thought I’d try my hand at “data-mining” and “data science.” So, with the CrossFit Regionals coming, I thought it’d be interesting to analyze what it takes to be a top CrossFit competitor. Specifically, I wanted to know what factors would affect placement in the CrossFit Open.
![image](https://40.media.tumblr.com/e4dca25cf6e0c673ff9355d6afe2b677/tumblr_inline_no9etv4dHK1qepten_1280.jpg)

The first thing I needed to do was gather data.

I used a couple Python libraries to help me do this: Requests and Beautiful Soup. Requests downloads the html of a page while Beautiful Soup allows for simple searching / manipulation of the text. With some regular expressions and some looping, I managed to scrape all the relevant data off of an athlete page and store the competitor info in an SQLite database.

The next step was analysis.

I immediately made a couple graphs with PR vs overall rank. They’re pretty cool actually!


[![Olympic Lifts vs Open Rank](https://plot.ly/~waltertan12/197.png)](https://plot.ly/~waltertan12/197/ "Olympic Lifts vs Open Rank")
<script data-plotly="waltertan12:197" src="https://plot.ly/embed.js" async=""></script>


[![Fran Time vs Open Rank](https://plot.ly/~waltertan12/199.png)](https://plot.ly/~waltertan12/199/ "Fran Time vs Open Rank")
<script data-plotly="waltertan12:199" src="https://plot.ly/embed.js" async=""></script>

I also wanted to see how age might affect performance in the open so I made a chart detailing the best score of each age group. Graphically, it’s easy to see the teenagers and 30+ aged athletes struggle. Even more interesting is the peak of performance at around age 25 (I HAVE ONE MORE YEAR TO BE SUPER PHYSICALLY FIT!).


[![Open Score vs Age](https://plot.ly/~waltertan12/194.png)](https://plot.ly/~waltertan12/194/ "Open Score vs Age")
<script data-plotly="waltertan12:194" src="https://plot.ly/embed.js" async=""></script>

The next step was to actually develop the model to predict performance. I got stuck here for a little bit. I had a brief conversation with Mike on how to perform multiple regression but my mind couldn’t process it all (Note: Mike explained it really well. I simply couldn’t grasp it right away...)

Fortunately, it all sort of came together today and I made a simple model based of multiple linear regression, specifically sum of least squares over twelve variables. I don’t have a web app built to showcase the model but the Excel spreadsheet I have incorporates it. You can find the Excel spreadsheet and the Python scripts on my GitHub account: http://github.com/waltertan12/crossfit-games

I think the next step for me is to use some better techniques, like a random forest, to build a better model. I found the Scikit-Learn libary which seems to have some great machine learning tools so I’ll give that a go at soon. Oh, and I’ll most likely build a simple web app to display the results.

- Walter