---
layout: post
title:  "IoT: Temperature Logging"
date:   2015-02-23 16:26:30 -0800
categories: heat IoT raspberry pi
---
Here’s the latest with my Raspberry Pi:

I bought a few digital temperature sensors a couple weeks ago and finally got around to installing them this weekend. I had grand plans for a temperature logger hosted on a web app. However, not everything goes as planned. Initially, I wired the temperature sensors backwards and immediately destroyed the device. Fortunately, I bought two in case something like this happened, though I almost did the same with my backup sensor.

After some online tutorial-ing I got the temperature sensor to register with my Pi. Then, I wrote a couple scripts in Ruby and Python to read the temperature and log the info into an sqlite3 database.

But again, not everything goes as planned — I got really lazy and didn’t want to make a web app for the data. So, instead of actually using my database as intended, I looked up the Plot.ly API streamed my temperature data. Take a look!

[![Raspberry Pi Temperature Log](https://plot.ly/~waltertan12/113.png)](https://plot.ly/~waltertan12/113/ "Raspberry Pi Temperature Log")
<script data-plotly="waltertan12:113" src="https://plot.ly/embed.js" async=""></script>

Check out the source code on GitHub: [github.com/waltertan12/temp-stream](https://github.com/waltertan12/temp-stream)

*   Walter