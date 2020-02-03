---
title: '<s>Love</s> Urban policy in the time of <s>Cholera</s> Coronavirus.'
date: 2020-02-03
tags:
  - coronavirus
  - mathematical modelling
  - urban policy
  - data science
header:
  image: /images/coronavirus/corona_virus.jpg
excerpt: 'Coronavirus, Mathematical Modelling, Data Science'
mathjax: 'true'
---

## Are cities prepared for epidemics? 
The recent [2019-nCoV Wuhan coronavirus](https://en.wikipedia.org/wiki/Novel_coronavirus_(2019-nCoV)) outbreak in China has sent shocks through financial markets and entire economies, and has duly triggered panic among the general population around the world. On 30 January 2020, 2019-nCoV was even [designated](https://www.bbc.com/news/world-51318246) a global health emergency by the World Health Organization (WHO). At the time of this writing, no specific treatment verified by medical research standards has yet been discovered. Moreover, some key epidemiological metrics such as the [basic reproduction number](https://en.wikipedia.org/wiki/Basic_reproduction_number)(the average number of people infected by an ill individual) are still unknown.
In our times of unprecedented global connectedness and mobility, such epidemics are a major threat on a global scale due to [small world network](https://en.wikipedia.org/wiki/Small-world_network) effects. One could conjecture that conditional on a global catastrophic event (loosely defined as > 100mln casualties) happening in 2020, the most likely cause would be precisely some pandemic - not a nuclear disaster, not climate catastrophe, etc. This is further aggravated by worldwide rapid urbanisation, with our densely populated dynamic cities becoming propagation nodes in the disease diffusion network, thus making them extremely vulnerable.

In this post, we will discuss what can happen when an epidemic strikes a city, what measures should immediately be taken, and what implications this has for urban planning, policy making, and management. We will take the city of Yerevan as our case study and will mathematically model and simulate the spread of the coronavirus in the city, looking at how urban mobility patterns affect the spread of the disease. 

## Urban mobility
Effective, efficient, and sustainable urban mobility is of crucial importance for the functioning of modern cities. It has been [shown](https://www.nature.com/articles/s41467-019-12809-y) to directly affect livability and economic output (GDP) of cities. However, **in the event of an epidemic, it will add fuel to the fire**, amplifyig and propagating the disease spread.

So let's begin by looking at the network of aggregated origin-destination ($$OD$$) flows on a uniform Cartesian grid in Yerevan to get an idea about the spatial structure of mobility patterns in the city:

<img src="{{ site.url }}{{ site.baseurl }}/images/coronavirus/OD.jpg" alt="Yerevan OD network">

Further, if we look at the total inflow to the grid cells, we see a more or less monocentric spatial organisation with some cells with high inflow during a day off the center:

<img src="{{ site.url }}{{ site.baseurl }}/images/coronavirus/Yerevan_inflow.jpg" alt="Yerevan inflow">

Now, imagine that an epidemic breaks out at a random location in the city. **How will it spread? What can be done to contain it?**

## Modelling the epidemic
To answer these questions, we will build a simple [compartmental model](https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology#The_SIR_model) to simulate the spread of the infectuous disease in the city. As an epidemic breaks out, its [transmission dynamics varies significantly](https://www.ncbi.nlm.nih.gov/pubmed/23864593), depending on the geographical locations of the initial infection and its connectivity with the rest of the city. This is one of the most important insights gained from recent, data-driven studies on epidemics in urban populations. However, as we will see further below, the various outcomes call for similar measures to contain the epidemic and to account for such a possibility in planning and managing cities.

Since runnning individual-based epidemic models is [challening](https://www.sciencedirect.com/science/article/pii/S0198971514001367), and since our goal is to show general principles of epidemic spread in cities, and not to build a minutely calibrated and accurate epidemic model, we will follow the approach described in this [Nature article](https://www.nature.com/articles/s41467-017-02064-4), using a modified version of the classical [SIR model](https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology#The_SIR_model).

The model divides the population in three compartments. For each location $$i$$ and time $$t$$, here are the compartments:

* $$S_{i, t}$$: the number of individuals not yet infected or susceptible to the disease.
* $$I_{i, t}$$: the number of individuals infected with the disease and capable of spreading the disease to those in the susceptible group.
* $$R_{i, t}$$: the number of individuals who have been infected and then removed from the infected group, either due to recovery or due to death. Individuals in this group are not capable of contracting the disease again or transmitting the infection to others.

In our simulations, time will be a discrete variable as the state of the system is modelled at a daily basis. In a fully susceptible population at location $$j$$ at time $$t$$, an outbreak happens with probability:

$$
h(t, j)=\frac{\beta_{t} S_{j, t}\left(1-\exp \left(-\sum_{k} m_{j, k}^{t} x_{k, t} y_{j, t}\right)\right)}{1+\beta_{t} y_{j, t}},
$$

where $$\beta_{t}$$ is the transmission rate on day $$t$$; $$x_{k, t}$$ and $$y_{k, t}$$ denote the fraction of the infected and susceptible populations on day $$t$$ at location $$k$$ and location $$j$$, respectively, given by $$x_{k, t}=\frac{I_{k, t}}{N_{k}}$$ and $$y_{j, t}=\frac{S_{j, t}}{N_{j}}$$, where $$N_k$$ and $$N_j$$ are the population sizes at the locations $$k$$ and $$j$$. Then we go ahead and simulate a stochastic process introducing the disease into locations with eintirely susceptible populations, with $$I_{j, t+1}$$ is a Bernoulli random variable with probability $$h(t, j)$$.

Once the infections are introduced at random locations, the disease spreads both within those locations and is carried and transmitted in other locations by travelling individuals. This is where the urban mobility patterns characterised by the $$OD$$ flow matrix play a crucial role. 
Further, to formalise how the disease is transmitted by an infected person, we need the *basic reproduction number*, $$R_0$$. It is defined as $$R_0 = \beta_{t}/\gamma$$ where $$\gamma$$ is the recovery rate, and can be thought of as the expected number of secondary infections after an infected individual comes into contact with a susceptible population. At the time of this writing, the basic reproduction number for the Wuhan coronavirus [has been estimated](https://www.nejm.org/doi/full/10.1056/NEJMoa2001316) to be between 1.4 and 4. Let's take the worst case and assume it's 4. However, we should note that it's actually a random variable and the reported number is but the *expected* number. To make things a bit more interesting, we will run our simulations with different $$R_0$$s at each locations drawn from a good candidate distribution, $$Gamma$$, with mean 4:

<img src="{{ site.url }}{{ site.baseurl }}/images/coronavirus/R_0.jpg" alt="coronavirus R_0">

We can now proceed to the model dynamics:

$$
\begin{aligned}
S_{j, t+1} &=S_{j, t}-\frac{\beta_{t} S_{j, t} I_{j, t}}{N_{j}} - \frac{\beta_{t} S_{j, t} \sum_{k} m_{j, k}^{t} x_{k, t} y_{j, t}}{N_{j} + \sum_{k} m_{j, k}^{t}} \\
I_{j, t+1} &=I_{j, t}+\frac{\beta_{t} S_{j, t} I_{j, t}}{N_{j}}-\gamma I_{j, t} +  \frac{\beta_{t} S_{j, t} \sum_{k} m_{j, k}^{t} x_{k, t} y_{j, t}}{N_{j} + \sum_{k} m_{j, k}^{t}} \\
R_{j, t+1} &=R_{j, t}+\gamma I_{j, t}
\end{aligned}
$$
