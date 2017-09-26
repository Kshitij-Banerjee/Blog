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
If you want an introduction for micro-services and learn the basic concepts first, read the part 1 of this post first =>
[PART-1](../Microservices-Part1-Why/)

## Migration from Monoliths
-----------------
So you have a monolithic application you are tired with it (of course) and want to move away from it.
You are convinced by the promises of the micro services architecture and want to embrace it. How do you migrate from the monolith ?

- The more time you take to migrate, more features are getting added to the monolith and its going to make make the migration harder.

\#TakeBabySteps
----------------

Yes it sounds good to migrate everything in one shot (since you are so awesome).
It might even feel that its easier to do a big-bang.

----------------
## _Big Bangs are going to set you up for disaster._
## _Been there. Done that._
## _Take baby steps. One tiny feature at a time._
## Target migration of some read paths initially to build confidence.
----------------


### Typical migration path

A typical migration path is as follows
1. Start breaking your code into logical pieces and create vertical boundaries between your features.
2. Start breaking the deployment architecture physically. Gradually. One feature at a time. Target the read paths first (no distributed tx) to build confidence.
3. Once you start with the write paths, you might have to remodel some pieces of your application to workaround the distributed transactions now created. (Since earlier you happily used the database transactions to join unrelated entities)
4. Once your services start coming out, you will realize the need for some foundation support like gateways, security, service-discovery, configuration management etc.
<img src="../images/Monolith_Migration_Typical.png">

### The overall architecture

### Zuul API Gateway

Should the UI talk to every micro-service individually ?


A micro-service may provide a granular API which is sometimes a part of the overall request, and not the complete information that is required to render the page.
It would then become quite easy to push in logic around aggregation,filtering etc inside the UI to aggregate multiple requests.


Since this is a common problem in the micro-services environment, there is a common design to fix this by having an API-gateway in between the client and the back-end services

* This gateway takes care of the filtering, aggregation, that the UI would have to do otherwise. See API composition as a pattern here :[http://microservices.io/patterns/data/api-composition.html](http://microservices.io/patterns/data/api-composition.html)
* It is also the perfect place to add any pre-processing logic that would have to be required to be done by all of the services.
* We also might have multiple types of client that might require custom response types. The gateway can abstract such adaptions from the individual micro-services.
* Gateways don't care about the actual api's. They only route the requests and act as a facade.

### - Make sure that business logic does not creep into the api-gateway. Keep the API gateway simple and keep its responsibility minimum.


We have multiple instances of the micro-services. How to load balance them ?
While we can offload that to our usual load balancer based approach, Netflix Zuul relies on _client side load balancing_ to achieve the same.

Traditional load balancers work by knowing the list of the servers which they regularly pull health information from. They then load balance between this healthy hard-coded list.
In a client side load balancing, the clients themselves have a cache of the list of services and they load balance directly between the services,and hence reduce a network hop.

<img src="../images/client_load_balancing_microservices.png">


But that just moves the configuration hard-coding from the load balancer to the individual services.
To solve this we have another foundational component called the discovery service.

### Eureka - Discovery Service

The discovery service works by reversing the hard-coding and letting each service explicitly _push_ its state, instead of the usual pull approach.

Instead of the load balancer _pulling_ health information from each of the instances, in this model the service instances themselves _push_ the information to a discovery service.
Any of the other services can pull the information from this repository of services that the discovery server keeps.


<img src="../images/discovery_service.png">

- The instance names are _no more hard-coded_ between the services.
- Once an instance stops sending a heart beat, the discovery service will mark that service as invalid and the other services will know this information in their next registry list refresh.
- The local cache then gets refreshed and that instance is no-more sent to.

### This does make the services chatty with the discovery service.
To keep the volume of the chatting at a minimum, the approach is that the registry is only sent as a delta, which means the complete list is only fetched if there has been a change in the registry list.

In the above example, Zuul acts as just another client and does load balancing between its routes based on the service registry fetches.

### Ribbon

Ribbon is the library that Netfix uses internally for the load balancing and inter process communication activities.

Checkout -> [https://github.com/Netflix/ribbon](https://github.com/Netflix/ribbon) for details.

### Eureka - Peer Aware mode for HA

How do we make eureka highly available since it becomes a foundational infrastructure piece?
Sure the clients have a cache of things in case eureka fails, but eventually the cache will get invalid and we need some sort of failover.

<img src="https://github.com/Netflix/eureka/raw/master/images/eureka_architecture.png">

So eureka helps introduce redundancy into the picture by replicating between multiple eureka instances.

- Simply provide the list of eureka servers is the peer configuration and multiple eureka instances will buddy-up with each other and provide the necessary replication needed for fail-overs.
  - The configuration is `eureka.instance.serviceUrl.defaultZone`. Have the eureka instance names comma separated here.
- If a client is unable to send a heartbeat to the nearest eureka server, it will start talking to the next eureka server and so on.
- When the original (down) eureka instance comes up, it will just replicate all the information from the other peer eureka servers.

### SSL communications between services

You can configure eureka to send even the heartbeats in SSL.
The configuration is here:
~~~~~~
eureka:
  client:
    nonSecurePortEnabled: false
    securePortEnabled: true
    securePort: 443
~~~~~



### Running them in tomcat.

If you're running things in tomcat, the service instances are under a context path (by default the name of the war) and not separate ports by default.
To be able to send the context paths correctly to the registry service, you will have to override this behaviour.

Please follow the suggestions on this issue to be able to do the same

[https://github.com/spring-cloud/spring-cloud-netflix/issues/737](https://github.com/spring-cloud/spring-cloud-netflix/issues/737)

### Zuul Filter internals

The most important piece of code to understand for the zuul filter implementation is the following.

[https://github.com/Netflix/zuul/blob/1.x/zuul-core/src/main/java/com/netflix/zuul/http/ZuulServlet.java#L64](https://github.com/Netflix/zuul/blob/1.x/zuul-core/src/main/java/com/netflix/zuul/http/ZuulServlet.java#L64)
This is how the filter is going to run.

````` java
@Override
    public void service(javax.servlet.ServletRequest servletRequest, javax.servlet.ServletResponse servletResponse) throws ServletException, IOException {
        try {
            init((HttpServletRequest) servletRequest, (HttpServletResponse) servletResponse);

            // Marks this request as having passed through the "Zuul engine", as opposed to servlets
            // explicitly bound in web.xml, for which requests will not have the same data attached
            RequestContext context = RequestContext.getCurrentContext();
            context.setZuulEngineRan();

            try {
                preRoute();
            } catch (ZuulException e) {
                error(e);
                postRoute();
                return;
            }
            try {
                route();
            } catch (ZuulException e) {
                error(e);
                postRoute();
                return;
            }
            try {
                postRoute();
            } catch (ZuulException e) {
                error(e);
                return;
            }

        } catch (Throwable e) {
            error(new ZuulException(e, 500, "UNHANDLED_EXCEPTION_" + e.getClass().getName()));
        } finally {
            RequestContext.getCurrentContext().unset();
        }
    }
`````

### Under the hood - Inside the source code.
