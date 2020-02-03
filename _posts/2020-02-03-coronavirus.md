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

