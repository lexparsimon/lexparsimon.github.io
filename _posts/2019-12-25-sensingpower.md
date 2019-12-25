---
title: 'Urban sensing: quantifying the sensing power of vehicle fleets'
date: {}
tags:
  - urban mobility
  - urban monitoring
  - probability theory
  - data science
header:
  image: /images/urban sensing/yerevan_sensing.jpg
excerpt: 'Urban Sensing, Mobility,Data Science'
mathjax: 'true'
published: true
---

### Introduction

Smart cities are increasingly being equipped with sensors measuring a variety of quantities indicative of the quality of the urban enviroment: air pollution, traffic congestion, air temperature, humidity, road quality, pedestrian density, parking spot occupancy, Wi-Fi accessibility, etc. All these measurements will fuel advanced urban analytics, and become a routine component in urban planning, policy making, and management.

However, the city-wide deployment of sensors has limited spatial coverage and comes at a significant cost, and the question of their optimal placement arises naturally. As a solution, the ["drive-by"](http://nrlweb.cs.ucla.edu/nrlweb/publication/download/498/vsnsurvey10.pdf) paradigm has been recently proposed, whereby sensors are installed on third-party vehicles "scanning" the city. While most of the research on drive-by sensing has focused on engineering aspects, the key question of **how many vehicles are required to adequately scan a city** remained unanswered until recently.

In this post, we will follow the arguments described in a recent [paper](https://www.pnas.org/content/116/26/12752) from the [MIT Senseable Lab](http://senseable.mit.edu/) to demonstrate the suprisingly huge potential of this method: just 30 randomly chosen taxi vehicles (less than 1% of the entire fleet) during a typical day cover on average **more than a third** of Yerevan city!

### How to measure urban sensing?

In order for a vehicle fleet to scan as large a portion of the urban street network as possible, a dense exploration of what the [authors](https://www.pnas.org/content/116/26/12752) call the city's spatio-temporal "volume" is required. The extent to which a vehicle fleet does this is called its sensing power.
Among many candidates for the vehicle fleets, such as private cars, buses, and taxis, taxis are the natural choice for deploying the sensors as they are pervasive in the city and do not follow fixed routes.

Imagine a fleet of sensor-equipped vehicles $$\mathcal{V}$$ moving in a city, scanning its street network $$S$$ during a reference period $$\mathcal{T}$$. The nodes


some *italics*

and some **bold** 

and a [link](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.gamma.html)

Here's a bulleted list:

* first 
+ second
- third

a numbered list:
1. first
2. second
3. third

Python code block:
```python
    import numpy as np

```

here's an image:
<img src="{{ site.url }}{{ site.baseurl }}/images/urban sensing/Yerevan_trip_length_distribution.jpg" alt="Yerevan trip length distribution">

Here's some math:

$$a + b = c$$

we can also put it inline $$x+y=z$$
