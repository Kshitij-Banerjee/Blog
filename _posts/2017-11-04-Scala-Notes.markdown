---
layout: post
title:  "A cheat sheet to using akka with scala basics in 20 minutes"
description: "Code explains best. No Long descriptions. The basics, the important stuff laid out."
name: "Code explains best. No Long descriptions. The basics. The important stuff laid out."
author: Kshitij Banerjee
avatar: "img/authors/K_icon.png"
img: "scala_akka.png"
date: 2017-04-10
color: 5e35b1
tags: Tutorial, Scala
typej: Tutorial, Scala
---

## Scala
- - - - - - - - -
### var vs val
The object assigned to a val cannot be replaced, and the object assigned to a var can
val is immutable. var is mutable.
val is like final in java.

### Partial Functions

#### Summary :

A function that is not defined for all values.
~~~~~  scala
val fraction = new PartialFunction[Int, Int] {
  def apply(d: Int) = 42 / d
  def isDefinedAt(d: Int) = d != 0
}
~~~~~~

A better way to write with case functions
~~~~~~ scala
val fraction: PartialFunction[Int, Int] =
  { case d: Int if d != 0 ⇒ 42 / d }
~~~~~~


If a function takes a partial function, and if you define it inline. No need to write the PartialFunction declaration.
~~~~~ scala
scala> List(41, "cat") collect incAny
res9: List[Int] = List(42)
~~~~~

Recomended Read: [http://blog.bruchez.name/2011/10/scala-partial-functions-without-phd.html](blog article)
### Case classes

Case classes are normal classes but provide the following (which gets beneficial in pattern matching).
- apply method, so you don't need the `new` keyword to create them. MyCaseClass(23) creates it.
- getter functions are automatically defined for the constructor parameters
- toString, equals, hashCode implementations by default.
- The most important, instances of these classes can be decomposed through _pattern matching_  `Sum(Var(x), Const(3))` is a valid expression


Recommended read : [http://docs.scala-lang.org/tutorials/scala-for-java-programmers.html#case_classes_and_pattern_matching](http://docs.scala-lang.org/tutorials/scala-for-java-programmers.html#case_classes_and_pattern_matching)

### Stateful akka actors using the _become_ feature.


* Each actor has a `behaviourStack` that keeps a stack of the `behaviour` or, how does the actor respond to the messages.
* `behaviors` are just `PartialFunctions` that define what to do on a match
* The receive method only `initializes` the the behaviour stack
* Calls to receive message via the `recieveMessage` just use the behaviorStacks head behaviour to respond
~~~~~ scala
final def receiveMessage(msg: Any): Unit = actor.aroundReceive(behaviorStack.head, msg)
~~~~~
* You can ask the actor to `become` some other `behavior`. These calls internally either add a new behaviour to the stack or replace the existing.
  * You say this by saying `context.become(someOtherBehavior)`


This can help write code like below

~~~~~ scala
def state(myObj: MyObj): Receive = {
  case doMutation() =>
    val newObj = getNewThing(myObj)
    context.become(state(newObj))
    sender() ! newObj

  case getObj() => sender() ! myObj
}

override def receive: Receive = state(someInitialObject)
~~~~~

The behaviour defined by `state` here takes as messages two case class instances for matching.
_Note_: doMutation() refers to the class instance of type doMutation wheras a simple doMutation means the companion object.

Bind.
~~~~~~ scala
class GuiceModule(environment: Environment, configuration: Configuration)
  extends AbstractModule {

     override def configure() = {
         bind(classOf[BooksService]).to(classOf[ConcreteBooksService])
     }
}
~~~~~~

Enable guiceModule
~~~~~~ scala
play.modules.enabled += "modules.GuiceModule"
~~~~~~

Inject
~~~~~~ scala
class BooksController @Inject()(booksService: BooksService)
  extends Controller {
    // ...
 }
~~~~~~


### Collections

#### Java Convertors

To be compatible with java types, scala allows to convert java collections to scala collections to reap the benefits
* Import the convertors
    ~~~~~ scala
    import collection.JavaConverters._
    ~~~~
* Use `.asScala`
These convertors are just wrappers to the original collections so there are no overheads.

#### Maps

Maps are just a collection of tuples.
* `List(Tuple(1,2))`  can be converted to its map version using `toMap` method.
* if you have two lists, convert them by `(list1 zip list2).toMap` . Zip clubs corresponding values from two lists into tuples.
* To make a SortedMap you can do `SortedMap(list1 zip list2: _*`. This vararg syntax is explained below

#### Varargs and expansions

Some code can  user vararg constructors. Also, you can ascribe(using `:` operator) a list to be read as varargs using varargs expansion (using {% raw %} _\* {%endraw%})

~~~~~~~ scala
def myMethod(params: Any*) = ... //varargs parameter, use as an Array[Any]

val list = Seq("a", 42, 3.14) //a Seq[Any]
myMethod(list : _*)
~~~~~~~
