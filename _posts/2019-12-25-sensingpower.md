---
title: 'Urban sensing: quantifying the sensing power of vehicle fleets'
date: 2019-12-26
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

However, the city-wide deployment of sensors has limited spatial coverage and comes at a significant cost, and the question of their optimal placement arises naturally. As a solution, the ["drive-by"](http://nrlweb.cs.ucla.edu/nrlweb/publication/download/498/vsnsurvey10.pdf) paradigm has been recently proposed, whereby sensors are installed on third-party vehicles "scanning" the city. While most of the research on drive-by sensing has focused on engineering aspects, the key question of **how many vehicles are required to adequately scan a city** remained unanswered until recently. The answer intuitively depends on the mobility patterns of the vehicles in question. Among many candidates for the vehicle fleets, such as private cars, buses, and taxis, taxis are the natural choice for deploying the sensors as they are pervasive in the city and do not follow fixed routes. Is it possible to find out whether attaching sensors to taxi vehicles is a good idea? This is what we are going to do in this post.

### Why is it important?
If we discover that a small number of taxis covers a large portion of the city, attaching sensors to those taxis could provide a cheap way for monitoring the various quantities mentioned in the introduction. In this post, we define a measure of this "covering" and discuss its analytic description, which agrees surprisingly well with empirical data from a ride sharing company called GG based in Yerevan. We will follow the arguments described in a recent [paper](https://www.pnas.org/content/116/26/12752) from the [MIT Senseable Lab](http://senseable.mit.edu/) to demonstrate the suprisingly huge potential of this method: just 30 randomly chosen taxi vehicles (less than 1% of the entire fleet) during a typical day cover on average **more than a third** of Yerevan city and **almost 70%** of the centrally located districts (Kentron, Ajapnyak, Kanaker-Zeytun, Nork-Marash)! This has huge implications for smart city projects as it demonstrates that drive-by sensing can be readily implemented in real projects at a relatively small cost.

### How to measure urban sensing?

In order for a vehicle fleet to scan as large a portion of the urban street network as possible, a dense exploration of what the [authors](https://www.pnas.org/content/116/26/12752) call the city's spatio-temporal "volume" is required. The extent to which a vehicle fleet does this is called its *sensing power*.

Imagine a fleet of sensor-equipped vehicles $$\mathcal{V}$$ moving in a city, scanning its street network $$S$$ during a reference period $$\mathcal{T}$$. Below you can see such a fleet of randomly chosen 30 taxis traversing the streets of Yerevan:
![Alt Text](/images/urban sensing/Yerevan_30_gg.gif)

We represent the nodes of the street network $$S$$ as potential pick-up and drop-off locations for the vehicles. Since we are going to work with street segments, we convert the set of GPS coordinates of a given vehicle to a trajectory $$T_{r}$$, defined as a sequence of street segments $$T_{r} = (S_{i_1} , S_{i_2} , . . . )$$. In order to achieve this, we need to match the taxi trajectories to the OpenStreetMap driving network segments. However, this is not an easy task, since a naive projection to the closest road segment yields incorrect results. Fortunately, this problem has been solved in [this](https://www.microsoft.com/en-us/research/publication/hidden-markov-map-matching-noise-sparseness/) paper, which builds a Hidden Markov Model for identifying the most probable street network path, given a sequence of GPS points.

In order to measure the *sensing power* of a fleet, we quantify it as its covering fraction $$\langle C\rangle$$, defined in the paper as the average fraction of street segments in $$S$$ that are "covered" or sensed by a taxi during time period $$\mathcal{T}$$ , assuming that $$N_{V}$$ vehicles are selected
uniformly at random from the vehicle fleet $$\mathcal{V}$$.

What we want is an understanding of how the *sensing power* of a vehicle fleet in a city changes as a function of the size of the fleet.

### Can we simulate the taxi movements?

To model the taxis' movements, the paper introduces the taxi-drive process. The model makes very simple (and wrong) assumptions: 

* that taxis travel to randomly chosen destinations via shortest paths, with ties between multiple shortest paths broken at random. 
* Once a destination is reached, another destination is chosen, again at random, and the process repeats. 
* To reflect heterogeneities in real taxi trajectories, destinations  are not selected uniformly at random. Instead, already visited nodes are chosen preferentially: The probability $$q_{n}(t)$$ of selecting a node $$n $$ is proportional to $$1+v_{n}^{\beta}(t)$$, where $$v_{n}(t)$$ is the total number of times node $n$ has been visited at during the time interval $$`[t_{start}, t)`$$ and $$\beta$$ is a city-specific parameter
to be tweaked. This ["preferential attachment"](https://en.wikipedia.org/wiki/Preferential_attachment) mechanism, colloquially known as rich-get-richer effect and discussed mainly within the context of wealth distribution and tie formation in social networks, has been [shown](http://barabasi.com/publications/10/human-dynamics) to capture the statistical properties of human mobility and, as it turns out, also captures those of taxis.

We first calculate the street segment popularities $$p_{i}$$, the relative frequency each street segment in the city is traversed by the vehicles in the fleet $$\mathcal{V}$$ during $$\mathcal{T}$$ (the $$p_{i}$$ sum to 1) from the GG taxi data (**spoiler**: we are going to use the $$p_{i}$$ to compute our target $$\langle C\rangle$$). Then, we run the simulation of the taxi-drive process with the same fleet size and the same number of trips for each vehicle. We discover that, despite the unrealistic assumptions in the model, the taxi-drive process captures quite well the statistical properties of real taxis' trajectories.

In particular, the simulation produces surprisingly realistic distributions of segment popularities $$p_{i}$$, in agreement with that obtained from the GG data:

<img src="{{ site.url }}{{ site.baseurl }}/images/urban sensing/Yerevan_segment_pops.jpg" alt="Yerevan segment popularity distribution">

As one might expect from a preferential attachment mechanism, the distributions are heavy-tailed and closely follow [Zipf's law](https://en.wikipedia.org/wiki/Zipf%27s_law). This good agreement between the model and the data is rather surprising. One might expect that the simplistic assumptions in the taxi-drive model miss many important factors in the real world, such as variations in street-segment lengths and driving speeds, the spatial arranagement of attractive destinations specific to a city, human-routing decisions, as well as heterogeneities in passenger-pickup and -dropoff times and locations. However, the results show that, at the macro level of segment popularity distributions, these complexities are irrelevant. Moreover, the original paper finds this agreement to be true across many cities varying in size and continent.


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
