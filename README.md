# RxChef

In this tutorial we will be learning the concepts of ReactiveX and Reactive Programming, whilst acting as chief mechanic for all of the Robo Chefs for a chain of vintage 1950s restaurants. Your job is to engineer the firmware for the Robo Chefs ordered for these restaurants from technology conglomerate RxChef. By the end you'll be a fully certified RxChef Mechanic, ready to start a lucrative career in Reactive Culinary.

More seriously though, this tutorial will lay down the foundation to help you enter the world of Reactive Programming. So without further ado.

## What is Reactive Programming ?

Reactive programming is an increasingly popular style of programming to compose your application
using streams of events. Sound technical? Well lets break it down.

There are two core components Observables and Subscribers.

An __Observable__ is something that can be reacted to (Hence why we call it reactive programming); this could be an event or a property. Some examples of observables in the kitchen could be:

- Getting a new order.
- How many bags of chips are there left?

A __Subscriber__ is an interested party to that Observable, for the examples above our Robo Chef we would be the interested party. You could also describe the customer as being a __Subscriber__ since they are interested in knowing when their meal is ready.

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
chips = Chips(grams: 220)
tray.place(chips)

- Place the tray in the oven.
oven.place(tray)

- After 20 minutes take the tray out of the oven.
wait(minutes: 20)
oven.remove(tray)

- Wait 5 minutes for the chips to cool down.
wait(minutes: 5)

- Serve them up.
serve(chips)
```

Whilst simple and easy to understand our Robo Chef will turn on, prepare a plate of chips before remaining idle. We want our Robo Chef to prepare it each time someone places an order and only when they place an order. So lets have a look at how we would do this.

In the past we may have had a loop who's job it was to poll if there were any new orders. Luckily in an era of ever growing numbers of frameworks we don't have to do this anymore. Most of these frameworks have a concept similar to observables, so let's use that:

```
- When we have a new order
observe(orders.count, callback: {

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

That's still pretty easy right? well let's give it a go:

```
- When we have a new order
observe(orders.count, callback: {

  - Heat up the oven to 220°
  oven.setTemperature(220)

  - Place 200 grams of chips onto a tray.
  chips = new Chips(grams: 220)
  tray.place(chips)

  - Place the tray in the oven.
  oven.place(tray)

  observe(chips.are_golden, callback: { order in

    - After 20 minutes take the tray out of the oven.
    oven.remove(tray)

    - Wait 5 minutes for the chips to cool down.
    wait(minutes: 5)

    - Serve them up.
    serve(chips.serve)
  })
})
```

Again still pretty easy, we wrapped up the last steps in a callback which will be ran whenever the chips become golden and crispy. But we are starting to see a problem emerge here.

To illustrate this problem a bit more, lets add another layer of complexity. Customers are complaining again, this time the Robo Chef is sending out plates of chips.......without the chips. This time it is discovered that it isn't monitoring the stock levels and keeps accepting orders even though there are no more ingredients to cook with.

When this happens the Robo Chef needs to inform the waiter who brought him the order that it cannot be full-filled. How hard can that be?

```
- When we have a new order
observe(orders.count, callback: {

  - Heat up the oven to 220°
  oven.setTemperature(220)

  - Check we have chips
  if store.has_chips {

    - If we do, place 200 grams of chips onto a tray.
    chips = new Chips(grams: 220)
    tray.place(chips)

    - Place the tray in the oven.
    oven.place(tray)

    observe(chips.are_golden, callback: {

      - After 20 minutes take the tray out of the oven.
      oven.remove(tray)

      - Wait 5 minutes for the chips to cool down.
      wait(minutes: 5)

      - Serve them up.
      serve(chips.serve)
    })
  }
  else
  {
    - If we don't - inform the waiter
    order.waiter.inform("I don't have any chips for that order")
  }

})
```
What happened? We used to have such beautiful code and now look at it! Whats started as a list of step-by-step imperative instructions has quickly turned into tangled-mess that makes it hard to grasp the flow of the program without having to re-read it a few times. These steps may not execute at all and not even in the same order in which we read them.

Most engineers are aware of the negative impact of "spagetti code", having neat organised code is a goal of hopefully all professional software companies. Unfortunatly a lot of the best companies are still battling with the day to day struggles of "spagetti flow" - the inability to predict the flow of their program based on their code due to tangled up state.

Lets try re-building our Robo Chef again but this time using reactive techniques. I'm also going to throw in a few requirements later on to really show some of the power of using this style of programming.
