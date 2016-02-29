# RxChef

In this tutorial we will be learning the concepts of ReactiveX and Reactive Programming whilst running the kitchen for a hugely successful vintage 1950s diner. By the end you'll be a fully fledged RxChef.

## What is Reactive Programming ?

Reactive programming is an increasingly popular style of programming to compose your application
using streams of events. Sound technical? Well lets break it down.

There are two core components Observables and Subscribers.

An __Observable__ is something that can be reacted to (Hence why we call it reactive programming); this could be an event or a property. Some examples of observables in the kitchen could be:

- Getting a new order.
- How many buns are there left for the burgers?

A __Subscriber__ is an interested party to that Observable, for the examples above as a RxChef we would be the interested party; So you could describe us as being the subscriber. You could also describe a customer as being a __Subscriber__ since they are interested in knowing when their meal is ready.

So that really is the basics of Reactive Programming and hopefully you can see that it isn't all that complicated. In fact if you've been programming for a while already, you may have been using traditional frameworks who already allow you to observe things. So what's different?

## Pipelines and Streams

Reactive programming has an ace up its sleeve, it allows you to easily compose these observables into pipelines.

In comparison Traditional frameworks only allowed you to respond to a change to a property and trigger an action. Reactive programming not only allows you to do this but also allows you to chain these actions together which in turn can then be observed themselves.

This is where things can get a bit confusing so lets start with a concrete example. We're going to write the first steps for fulfilling an order for a plate of chips. To make things easier lets write an outline of the steps needed to make one:

```
- Heat up the oven to 220°
- Place 200 grams of chips onto a tray.
- Place the tray in the oven.
- After 20 minutes take the tray out of the oven.
- Wait 5 minutes for the chips to cool down and serve them up.
```
