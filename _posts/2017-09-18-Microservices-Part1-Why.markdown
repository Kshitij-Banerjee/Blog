---
layout: post
title:  "Microservices with Spring Cloud"
description: "The sneaky details around Spring Cloud Netflix, Zuul, Eureka and setting things up the right way"
name: "The sneaky details around Spring Cloud Netflix, Zuul, Eureka and setting things up the right way"
author: Kshitij Banerjee
avatar: "img/authors/K_icon.png"
img: "spring_cloud.png"
date: 2017-09-18
color: 5e35b1
tags: Tutorial, Spring Cloud Netflix
tags: Tutorial, Spring Cloud Netflix
---

## Micro-Services - Part 1
------------------

<img src="https://martinfowler.com/articles/microservices/images/sketch.png">
<i><center> Photo credits: martinfowler.com </center></i>

### WHY?
-------

Every application is made of many many features.
You start with a simple and brilliant idea, and you soon realize you need so much more for just that initial _"MVP"_ :D, and you convert that coffee into code and bloat your application with those features just like how that coffee bloats your stomach.

Where to add these new features though?
The general practice of development has been to create everything in the same monolithic application and __hope that developers write code and treat code like a piece of art__. You hope and believe that things will turn out to be magically beautiful.


Ever so often though, production bugs come in and you start appreciating the person who named it _bugs_.

The art that was intended, the hero that was promised .. crumbles .. on its failure and no one wants to even look at the (un)intended beauty.


##### The Pain
--------------
- It is complicated to understand what is going on.
- It is painful to prevent idiots from breaking your thoughtfully written code.
- Every change that goes to production gives you sleepless nights.
- Every morning after a deployment you have a late lunch (if you are lucky enough to have lunch)
- Everyone is screaming. People are unhappy and blame each other all the time.
- The management comes and demands change. Someone big needs answers.
- Architects will come and claim the person who built this had lack of foresight (nicest I could put it)
- New phrophecies will float around a promising new grand re-architecture of this application.
- While this amazing plan is forming, new features are being built with flash level speed and the dream of changing things is just an idea.. a hope .. a light at the end of the dark.
- New teams are formed. New architecture is written. And the story repeats itself. (Faster the second time)




----------------
Lets face it. __Change is inevitable__. What looks the best right now might be the worst part of the future. You will never be able to think about all the possibilities in the future (unless you are into astrology).
We don't need perfect. We need an application that can change. Change fast. One we can reason about. One that is hard to break and that can let us be agile again.

-----------------


### So how do you define microservices?
Who am I to define? Hear it from the wiser ones.

_Microservices is a **variant of the service-oriented architecture** (SOA) architectural style that structures an application as a **collection of loosely coupled** services. In a microservices architecture, services should be fine-grained and the protocols should be lightweight. The benefit of decomposing an application into different smaller services is that it improves modularity and makes the application easier to understand, develop and test._

_Source: Wikipedia_

_The term "Microservice Architecture" has sprung up over the last few years to describe a particular way of designing software applications as suites of independently deployable services. While there is no precise definition of this architectural style, there are certain common characteristics around organization around business capability, automated deployment, intelligence in the endpoints, and decentralized control of languages and data._

_Source: Martin Fowler_


Its the _same good old foundational principles of software development applied to a larger system and application architecture_ IMO.
So lets quickly revisit them.


### Going back to the basics.
---------

There are some fundamental software concepts that are just so useful that we all learn them, follow them in our development, and appreciate the beauty as we age as developers.
Micro-services tend to share the same concepts and implement them at a larger scale.

#### 1. Modularize. Make modules loosely coupled and highly cohesive.
#### 2. Work over abstractions. Use contracts aka interfaces.
#### 3. Use the same language.
#### 4. Prefer Composition.

### What they promise to provide ?
-------

#### Scalability.

My Platform just got featured. Getting a lot of user creations. Create some more user service instances quick!


#### Modularity

If you have a problem happening, its fairly easy to reason about where the faulty codebase is, based on which services handles that functional responsibility.

When you think modular, you think contracts, you think abstraction and that helps in the long run.

When the whole is made of smaller parts, its easier to move the parts around, reuse these parts and `compose` new features with ease.


#### Promotes better practices. Aka: harder to be an idiot

Since your system is made out of isolated pieces of services, its harder to cross boundaries, create code that is harder to understand and in the process create bugs.

#### Scopes your data

The responsibility of the underlying data is given to the service. That service is now free to understand the data patterns, change the data source if it wants to or scale without changing its contract with other services.

#### Easier to test

The boundaries are smaller, more focussed on the specific functionality that this service provides. So your tests are shorter, cleaner and neater.


#### Polyglot development Aka: No long term marriage with technology choices.

Change the underlying language, change the data storage, change the logic if you want, the calling service doesn't care, as long as you don't break the protocol.

### What is the other side of the coin? What problems do they create?
---------

#### Distributed Transactions
#### Configuration Management
#### Service explosion
