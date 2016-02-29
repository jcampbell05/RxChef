# RxChef

In this tutorial we will be learning the concepts of ReactiveX and Reactive Programming whilst running the kitchen for a hugely successful vintage 1950s diner. By the end you'll be a fully fledged RxChef.

## What is Reactive Programming ?

Reactive programming is an increasingly popular style of programming to compose your application
using streams of events. Sound technical? Well lets break it down.

There are two core components Observables and Subscribers.

An __Observable__ is something that can be reacted to (Hence why we call it reactive programming); this could be an event or a property. Some examples of observables in the kitchen could be:

- Getting a new order.
- How many buns are there left for the burgers?

A __Subscriber__ is an interested party to that Observable, for the examples above we would be the people interested; So you could describe us as being the subscriber. You could also describe a customer as being a __Subscriber__ since they are interested in knowing when their meal is ready.

So that really is the basics of Reactive Programming and hopefully you can see that it isn't all that complicated. In fact if you've been programming for a while already, you may have been using traditional frameworks who already allow you to observe things. So what's different?

## The Pipeline

Reactive programming has an ace up its sleeve, it allows you to easily compose these observables into pipelines.

Again thats super-technical so lets break it down again.

I mentioned before that some traditional frameworks already have a way to observe things. However what they give you is the equivalent of a muppet calculator when what you need is a super computer. 
