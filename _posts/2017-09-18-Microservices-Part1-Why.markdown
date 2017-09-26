---
layout: post
title:  "Microservices - Why we need them"
description: "What is the whole buzz. What are the pitfalls and how to avoid them"
name: "What is the whole buzz. What are the pitfalls and how to avoid them"
author: Kshitij Banerjee
avatar: "img/authors/K_icon.png"
img: "Microservices_Main.png"
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

<center><i> Source: Wikipedia.com </i></center>


_The term "Microservice Architecture" has sprung up over the last few years to describe a particular way of designing software applications as suites of independently deployable services. While there is no precise definition of this architectural style, there are certain common characteristics around organization around business capability, automated deployment, intelligence in the endpoints, and decentralized control of languages and data._

<i><center> Source: Martinfowler.com </center></i>


Its the _same good old foundational principles of software development applied to a larger system and application architecture_.
So lets quickly revisit them.


### Going back to the basics.
---------

There are some fundamental software concepts that are just so useful that we all learn them, follow them in our development, and come to appreciate their beauty as we age as developers.

Micro-services tend to share the same concepts and implement them at a larger scale.

#### 1. Modularize.
Make modules loosely coupled and highly cohesive.
Services are the corresponding piece in micro-services that contain highly cohesive information internally, but are loosely coupled to other services.
Each service becomes the stalwart responsible for upholding the functionality of that service and ensuring that the related data is pristine.

Lets say you have to make a shopping website.
Would you make everything a single class?

<img src="../images/single_class_microservices.png" width="25%" height="25%">

You would probably make multiple classes right ?

<img src="../images/multi_class_microservices.png" width="25%" height="25%">

You would probably need a database which has your shared state soon. So you will make the related classes into their own modules.

<img src="../images/multi_module_microservices.png" width="25%" height="25%">

Micro-services is the next step and makes these modules separately deployable units.

<img src="../images/multi_service_microservices.png" width="25%" height="25%">

#### 2. Work over abstractions.
Use contracts (aka interfaces) to define boundaries.

This helps change the internal implementations when needed.
The user wants a dog today. Tomorrow he comes to love those furry cats, and then he grows and starts treating humans as pets.

Different people want different things, so you keep their connections in terms of contracts and abstract that as an interface.

<img src="../images/abstractions_microservices.png" width="25%" height="25%">

And then you play with these abstractions to do things like dependency injection and the like. This gives you immense freedom and flexibility.

<img src="../images/abstraction_rest_microservices.png" width="60%" height="60%">

Micro-services make these contracts in terms of API's and separate services based on the promise of a response to certain request. You as the developer of that service is free to change how you honor those responses without changes needed in the caller.

And then your love for abstraction starts and you say, let the client be abstracted too. So that gives rise to another pattern called, API GATEWAYs

Simple, have a service that the client talks to, and let that route/inject which actual service it needs to call.
This pattern is so widely used that some libraries help you make them. We will talk about one like Spring Cloud Netflix-Zuul in the next post.

#### 3. Use the same language.
Unix treats everything as a file. Hence you can pipe programs around. Once the language that binds things together is the same everything fits in nicely.

Micro-services generally choose their language as REST HTTP over TCP and Json as the preferred language. So now each of your service can be in whatever language/framework/architecture so long as it can honor the RESTful contracts. :)

#### 4. Prefer Composition.
Break a problem into smaller sub-problems and use a composition of the smaller pieces to make a whole.

#### 5. Avoid global variables.
Global variables are shared mutable states.
It becomes hard to keep track of these variables and consequently it becomes easy to do some changes from one place that conflicts with the intent of the other.


__Your database is your global state!.__

Having a big god object that is the representation of your global state has all the flaws that come with having those big singleton states in your application.
Many people updating the same global state will need what ? Locks, synchronizations and versioning. All those evil things come back and you want to move out of them.

When you hit bottlenecks, what do you do ? You try to parallelize.

You start with task parallelism, where you parallelize multiple applications working on the same data.

You quickly realize, the overhead of synchronizations, locks are wearing down the performance.
A better solution, is to design the system so that you are moving towards __data parallelism__


That's what micro-services are trying to do.
<img src="../images/global_states_microservices.png" width="70%" height="70%">

Break that global state (Shared Database) into smaller states that are parallelizable independently.
And instead of public variables which anyone can modify without honoring the validation semantics.


You keep the variables/state private and only let the class modify it.

In terms of micro-services, the data for a service can only change from its corresponding service. The data is private to the service, not globally available.
This does not mean that we _have_ to separate the data physically. These are logical boundaries, services are unaware of other service data. But the data might still be on the same physical server.

### Promises of a micro-service architecture.
-------

#### Scalability.

My Platform just got featured. Getting a lot of user creations. Create some more user service instances quick since they are independently deployable remember!


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
Some transactions will need updates to multiple areas/functionalities. Naturally, multiple services will get involved.

This will lead to distributed transactions. These are very very tough to solve. So how do you solve them ?

Dont.

Model your functionality such that things are _eventually consistent_. Some instances might intermittently have a view which is different from the other service, but it is okay, as long as eventually everyone understands the same state.
Intermittent failures need to be modelled.

Some tricks here.
1. dangling pointers / references are okay. Its just some data that needs to be cleaned up at max. (maybe.. storage is cheap).
2. Make use of 1, and order your updates such that only the _last_ update in the whole process makes the whole structure useful.

Entities are hierarchical. There is a parent entity that will reference multiple child entities and so on. Update the parent last.
An Order, will have items, that are of a product. The order has users, a payment, a schedule etc.
Only when the _order_ references the child do the childs come into existence. Otherwise, they don't exist.

Update the order references last. If a payment makes the order valid, update the payment last.

<img src="../images/heirarchy_microservices.png">

But how about intermittent failures?

Ensure that things are auto-retried and that API's are idempotent.
Don't update everything in the real-time path. Keep your real time api's short and sweet.
Let asynchronous workers update the whole process later. Let them retry, let pieces  be idempotent.


#### Configuration Management
Configurations about the system are magic numbers. Configuration at a service level are maybe okay.
Especially with so many services coming up. The configuration of a cluster is dynamic, and hardcoding these instances will not go a long way.



#### Service explosion

Soon you have a lot of services. Keeping track of them and where the instances are can get complicated.
Document everything. Document services well. Document contracts well.
Let service discoveries ledger your instances. Let things be dynamic.


Since we have so many topics and concepts, naturally these problems are seen across organizations, people start solving the same problems and patterns emerge.
Design patterns are born. Some companies are benign enough to share these implementations with the world.

Netflix integrated with Spring, our favourite java platform, and gave us these goodies.

Let me share my understanding of this in the next post ...  

Here: [PART-2](../Microservices-Part2-Spring-Cloud-Netflix)
---------------


