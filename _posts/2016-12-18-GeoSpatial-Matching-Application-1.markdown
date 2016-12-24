---
layout: post
title:  "Creating a geospatial matching application - Part 1"
description: "A curiosity around geospatial applications leads me to wander such lands. This blog details the steps and things I did while creating my geo spatial matching application. WIP"
author: Kshitij Banerjee
avatar: "img/authors/K_icon.png"
img: "Maps_geoSpatial_Imag.png"
date:   2016-12-18
color: 4c7aa9
---

* I've been super curious on the internals and workings of geo spatial applications and the impact that they create. Scribbles on the same.

* I personally love it when apps can give me a realtime update of the status of my order / ride on a map.  It's just honest and true about the state of affairs and you have a clear picture of what's happening.
  I hence personally like such apps and tend to use them.

* But, giving such realtime feedback and assurances could be tough and maybe costly? How Much ? How do they do it ?


- Topics
------------
1. The broad needs
2. Google S2
3. Actor model
4. Mobile UI's and needs


1 - The broad needs
------------------------
Going bottom up.

* We'll need a *datastore* that can store and retrieve the location of object of interests at a blazing fast speed. Since this is a highly parallel scenario, such a datastore should support sharding and horizontally scale to our needs.
* We'll need an efficient *algorithm to figure out which shard* a (lat,long) combination belongs to.
  * Algorithm should have a sense of proximity. We should be able to figure out the nearby objects given a (lat,lomg).
* We'll need a *pub-sub* kind of model for communication between objects and viewers.
  * Given a phone view - subscribe to updates for a objects in the view.
  * Given an order - show the target object to the source object.
* We'll need a *mobile UI* that can display these lat-long information in a human readable format


2 - Google S2
---------------

* Google S2 is a library that helps do efficient geo spatial computations and honestly, its a really clever way of storing data in a fetchable way.
* It uses a parameterisation over _Hilbert Curves_ as its basis, which helps it ensure proximity queries are solved real easy.
* [hilbert curves]( http://blog.christianperone.com/2015/08/googles-s2-geometry-on-the-sphere-cells-and-hilbert-curve/ ) Read this article for more details.
* The other great part is that this forms the basis of geo spatial indexing, and a lot of other data stores software use this internally and provide useful API's on top.
* MongoDB uses S2 Internally and is a great way to be a geo spatial backing database with good horizontal scalability. [mongo]( https://docs.mongodb.com/v3.2/tutorial/geospatial-tutorial/ )


3 - Actor Model
------------------

* `The actor model in computer science is a mathematical model of concurrent computation that treats "actors" as the universal primitives of concurrent computation. In response to a message that it receives, an actor can: make local decisions, create more actors, send more messages, and determine how to respond to the next message received.` - Wikipedia
* To me the most important part is that once you start modelling your applications as actors and couple it with local transparency ( meaning that you do not know whether the other actor is in the same machine space / JVM while coding ) this is a great model for such high touch point communications. It's a great success on chat application but I feel this use case models well in the actor model.
* The primary library / toolkit here that I'm interested in is the Akka toolkit. The concepts here are clearly defined. Best practices stated and its integration with modern frameworks is great.
* [Read](http://akka.io/docs/) the akka docs here to get to understand this in more detail. Some summary points to note :-
  * Actor models are generally modeled as heirarchical structures. A parent actor will delegate tasks to its child actors and supervise them for failures. Tasks are broken down into fine grained activities.
  * Actor is a container for state, behaviour, Mailbox, Child Actors and Supervision Strategy. Actors lifecycle is also something you must control.
  * You may create actors using `ActorSystem.actorOf, ActorContext.actorOf` when they are in direct heirarchy or look them up using `ActorSystem.actorSelection`. The `ActorContext.actorSelection` can be used to start the lookup from the current actor instead of the root.
* An Akka Actor occupies around 400-500 bytes, so theoretically you should be able to fit around 8.5 million on 4GB of heap!
* Many akka actors may use the same thread.
* Akka actors are generally injected in playFramework options using plugins.

4 - Mobile UI's and Interactions
----------------------

* The communication in terms of geoLocation objects is standardized in terms of [geoJson Objects](http://geojson.org/geojson-spec.html).
* Google map apis
* Openstreetmaps.org

More detailed post on this when I get to it. :)
