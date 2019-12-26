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
* To reflect heterogeneities in real taxi trajectories, destinations  are not selected uniformly at random. Instead, already visited nodes are chosen preferentially: The probability $$q_{n}(t)$$ of selecting a node $$n $$ is proportional to $$1+v_{n}^{\beta}(t)$$, where $$v_{n}(t)$$ is the total number of times node $$n$$ has been visited at during the time interval $$\[t_{start}, t)$$ and $$\beta$$ is a city-specific parameter
to be tweaked. This ["preferential attachment"](https://en.wikipedia.org/wiki/Preferential_attachment) mechanism, colloquially known as rich-get-richer effect and discussed mainly within the context of wealth distribution and tie formation in social networks, has been [shown](http://barabasi.com/publications/10/human-dynamics) to capture the statistical properties of human mobility and, as it turns out, also captures those of taxis.

We first calculate the street segment popularities $$p_{i}$$, the relative frequency each street segment in the city is traversed by the vehicles in the fleet $$\mathcal{V}$$ during $$\mathcal{T}$$ (the $$p_{i}$$ sum to 1) from the GG taxi data (**spoiler**: we are going to use the $$p_{i}$$ to compute our target $$\langle C\rangle$$). Then, we run the simulation of the taxi-drive process with the same fleet size and the same number of trips for each vehicle. We discover that, despite the unrealistic assumptions in the model, the taxi-drive process captures quite well the statistical properties of real taxis' trajectories.

In particular, the simulation produces surprisingly realistic distributions of segment popularities $$p_{i}$$, in agreement with that obtained from the GG data:

<img src="{{ site.url }}{{ site.baseurl }}/images/urban sensing/Yerevan_segment_pops.jpg" alt="Yerevan segment popularity distribution">

As one might expect from a preferential attachment mechanism, the distributions are heavy-tailed and closely follow [Zipf's law](https://en.wikipedia.org/wiki/Zipf%27s_law). This good agreement between the model and the data is rather surprising. One might expect that the simplistic assumptions in the taxi-drive model miss many important factors in the real world, such as variations in street-segment lengths and driving speeds, the spatial arranagement of attractive destinations specific to a city, human-routing decisions, as well as heterogeneities in passenger-pickup and -dropoff times and locations. However, the results show that, at the macro level of segment popularity distributions, these complexities are irrelevant. Moreover, the original paper finds this agreement to be true across many cities varying in size and continent.

### Analytic derivation of $$\langle C_{N_V}\rangle$$

Having computed the segment popularities $$p_{i}$$, we now proceed to estimating the the sensing power $$\langle C_{N_V}\rangle$$ analytically by recognizing the connection with the [urn](https://en.wikipedia.org/wiki/Urn_problem) problem in probability theory (this is why knowledge of basic probability theory is so useful!). The street segments are considered as "bins" into which "balls" are placed every time a taxi vehicle traverses that street segment. Using the segment popularities $$p_{i}$$ as the bin probabilities, we can derive the analytic expression for $$\langle C_{N_V}\rangle$$.

As stated in the paper, given the nontrivial topology of $$S$$ and the non-Markovian nature of the
taxi-drive process (this essentially means that the future does not only depend on the present - loosely speaking the Markov property - but on the past as well), it is difficult to solve for $$\langle C_{N_V}\rangle$$ exactly. However, tt is possible to derive a very good approximation. 

As we will see in a bit, it is actually easier to first solve for the trip-level $$\langle C_{N_T}\rangle$$ covered fraction — i.e., when $$N_T$$, the total number of trips, is the dependent variable, so we begin the derivation with this simpler case; the vehicle-level expression for $$\langle C_{N_V}\rangle$$ will then be trivial to obtain.

Imagine we have a set $$\mathcal{P}$$ of taxi trajectories. We define a taxi trajectory $$T_r$$ as a sequence of street segments $$T_{r} = (S_{i_1} , S_{i_2} , . . . )$$. The trajectories can be those obtained from the empirical data or from the simulation - this is unimportant for now. Given $$\mathcal{P}$$, our strategy for finding $$\langle C_{N_T}\rangle$$ will be to imagine street segments as bins into which balls are added every time they are traversed by a trajectory from $$\mathcal{P}$$. Note that, in contrast to the traditional urn problem, a random number of balls is placed at each time step, since taxis' trajectories have random length.

#### Trajectories with unit length

Let $$L$$ be the random length of a trajectory. The special case of $$L=1$$ is trivial to solve, since it corresponds exactly to the classical case: drawing $$N_T$$ trips at random from $$\mathcal{P}$$ amounts to adding $$N_T$$ balls into $$N_S$$ bins, where $$N_S$$ is the total number of street segments, and each bin is selected with probability $$p_i$$. Let $$\vec{M}=(M1, M2, . . ., M_{N_S})$$, where $$$M_i$ is the number of balls in the $$i$$-th bin. It is well known that the $$M_i$$ are multinomially distributed:

$$\vec{M} \sim \operatorname{Multi}\left(N_{T}, \vec{p}\right)$$,

where $\vec{p}=\left(p_{1}, p_{2}, \dots p_{N_{s}}\right)$. Now, since a street segment is considered covered during time period $$\mathcal{T}$$ if it is traversed by at least one vehicle from $$\mathcal{V}$$, the (random) fraction of street segments covered is

$$C=\frac{1}{N_{S}} \sum_{i=1}^{N_{S}} 1_{\left(M_{i} \geq 1\right)}$$,

where $$1_A$$ is the indicator function of event $$A$$. The expectation of this fraction $$C$$ is

$$\langle C\rangle_{\left(N_{T}, L=1\right)}=\frac{1}{N_{S}} \sum_{i=1}^{N_{S}} \mathbb{P}_{N_{T}}\left(M_{i} \geq 1\right)$$.

Now the trick is to note that the number of balls in each bin is binomial random variable $$M_{i} \sim B i\left(N_{T}, p_{i}\right)$$. The survival function of the binomial distribution is $\mathbb{P}\left(M_{i} \geq 1\right)=1-\left(1-p_{i}\right)^{N_{T}}$. Finally, we substitute this into the previous equation and obtain:

$\langle C\rangle_{\left(N_{T}, L=1\right)}=1-\frac{1}{N_{S}} \sum_{i=1}^{N_{S}}\left(1-p_{i}\right)^{N_{T}}$.

#### Trajectories with fixed length



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
