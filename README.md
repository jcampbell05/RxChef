# RxChef

In this tutorial we will be learning the concepts of ReactiveX and Reactive Programming, whilst acting as chief mechanic for all of the Robo Chefs for a chain of vintage 1950s restaurants. Your job is to engineer the firmware for the Robo Chefs ordered for these restaurants from technology conglomerate RxChef. By the end you'll be a fully certified RxChef Mechanic, ready to start a lucrative career in Reactive Culinary.

More seriously though, this tutorial will lay down the foundation to help you enter the world of Reactive Programming. So without further ado.

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

This is where things can get a bit confusing so lets start with a concrete example. We're going to write the first steps for preparing a plate of chips. To make things easier lets write an outline of the steps needed to make one:

```
- Heat up the oven to 220°
- Place 200 grams of chips onto a tray.
- Place the tray in the oven.
- After 20 minutes take the tray out of the oven.
- Wait 5 minutes for the chips to cool down.
- Serve them up.
```

On the face of it, converting these instructions to traditional step-by-step imperative code  should be pretty simple:

```
- Heat up the oven to 220°
oven.setTemperature(220)

- Place 200 grams of chips onto a tray.
chips = new Chips(grams: 220)
tray.place(chips)

- Place the tray in the oven.
oven.place(tray)

- After 20 minutes take the tray out of the oven.
wait(minutes: 20)
oven.remove(tray)

- Wait 5 minutes for the chips to cool down.
wait(minutes: 5)

- Serve them up.
serve(chips.serve)
```

Whilst simple and easy to understand our Robo Chef will turn on, prepare a plate of chips before remaining idle. We want our Robo Chef to prepare it each time someone places an order and only when they place an order. So lets have a look at how we would do this.

In the past we may have had a loop who's job it was to poll if there were any new orders. Luckily in an era of ever growing numbers of frameworks we don't have to do this anymore. Most of these frameworks have a concept similar to observables, so let's use this.

```
- When we have a new order
observe(NewOrder, {

  - Heat up the oven to 220°
  oven.setTemperature(220)

  - Place 200 grams of chips onto a tray.
  chips = new Chips(grams: 220)
  tray.place(chips)

  - Place the tray in the oven.
  oven.place(tray)

  - After 20 minutes take the tray out of the oven.
  wait(minutes: 20)
  oven.remove(tray)

  - Wait 5 minutes for the chips to cool down.
  wait(minutes: 5)

  - Serve them up.
  serve(chips.serve)

})
```

As you can see we simply wrapped up the original steps in a callback which will be ran every time a new order is placed. Still fairly familiar, still pretty simple. What could go wrong?

Well it turns out a lot, customers have been complaining that their chips are undercooked. On further inspection it seems that taking the chips out after 20 minutes is the problem. When a real person cooks, they judge if it's ready or not based on the color. So we need to upgrade our Robo Chef to do the same.
