# RxChef™

In this tutorial we will be learning the concepts of ReactiveX and Reactive Programming, whilst acting as chief mechanic for the RxChef™ Robo Chefs for a chain of restaurants. Our task is to engineer the firmware for these Chefs. By the end you'll be a fully certified RxChef™ Mechanic, ready to start a lucrative career in Reactive Culinary.

More seriously though, this tutorial will lay down the foundation to help you enter the world of Reactive Programming. So without further ado.

## What is Reactive Programming ?

Reactive programming is an increasingly popular style of programming to compose your application
using streams of events. Sound technical? Well lets break it down.

There are two core components Observables and Subscribers.

An __Observable__ is an event that can be reacted to (Hence why we call it reactive programming); Some examples of observables in the kitchen could be:

- Getting a new order.
- The amount of chips in the store room has changed.

A __Subscriber__ is an interested party to that Observable, for the examples above our Robo Chef we would be the interested party. You could also describe the customer as being a __Subscriber__ since they are interested in knowing when their meal is ready.

So that really is the basics of Reactive Programming and hopefully you can see that it isn't all that complicated. In fact you may already be using traditional frameworks who allow you to observe things. So let's explore why you would want to start programming reactively.

## From Steps

First we're going to write the first steps for preparing a plate of chips. To make things easier lets write an outline of the steps needed to make one:

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

    - Take the tray out of the oven.
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

  - Check we have chips
  if store.has_chips {

    - If we do, Heat up the oven to 220°
    oven.setTemperature(220)

    - Place 200 grams of chips onto a tray.
    chips = new Chips(grams: 220)
    tray.place(chips)

    - Place the tray in the oven.
    oven.place(tray)

    - When the chips turn golden
    observe(chips.are_golden, callback: {

      - Take the tray out of the oven.
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
What happened? We used to have such beautiful code and now look at it! Whats started as a list of step-by-step imperative instructions has quickly turned into tangled-mess that makes it hard to grasp the flow of the program without having to re-read it a few times. These steps may not execute at all, right away or even in the same order in which we read them.

Most engineers are aware of the negative impact of "spagetti code", having neat organised code is a goal of hopefully all professional software companies. Unfortunately a lot of the best companies are still battling with the day to day struggles of "spagetti flow" - the inability to predict the flow of their program based on their code due to tangled up state.

Lets try re-building our Robo Chef again but this time using reactive techniques. I'm also going to throw in a few requirements later on to really show some of the power of using this style of programming.


## To Streams

Lets start converting over to ReactiveX since our Robo Chef is interested in new-orders we are going to subscribe to an __Observable__ for those new orders instead of creating one ourselves.

```
- When we have a new order
new_orders.subscribeNext({

    - Check we have chips
    if store.has_chips {

      - If we do, Heat up the oven to 220°
      oven.setTemperature(220)

      - Place 200 grams of chips onto a tray.
      chips = new Chips(grams: 220)
      tray.place(chips)

      - Place the tray in the oven.
      oven.place(tray)

      - When the chips turn golden
      observe(chips.are_golden, callback: {

        - Take the tray out of the oven.
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

Right now this looks very similar to what we had before, the callback for `subscribeNext` will be called whenever a new order is made.

Lets do the same for monitoring the color of the chips.

```
- When we have a new order
new_orders.subscribeNext({

    - Check we have chips
    if store.has_chips {

      - If we do, Heat up the oven to 220°
      oven.setTemperature(220)

      - Place 200 grams of chips onto a tray.
      chips = new Chips(grams: 220)
      tray.place(chips)

      - Place the tray in the oven.
      oven.place(tray)

      - When the chips turn golden
      chips.are_golden.subscribeNext({

        - Take the tray out of the oven.
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

Like before the steps in the callback for `subscribeNext` is called when the chips turn golden. So far it doesn't look like there is much difference with ReactiveX and that's true but now things start to get interesting.

So now come those extra requirements, management are un-happy with the Robo Chef's lackluster performance. As the restaurant's popularity increased the Robo Chef has struggled to keep up. Right now the Robo Chef is only capable of fulfilling one order at a time, this is due to the fact that whilst the Robo Chef is waiting for the Chips to cool down our application causes the Robo Chef to be blocked for a whole 5 minutes.

This won't do, how can we fix this? You're probably thinking something along the lines of this:

```
- When we have a new order
new_orders.subscribeNext({

    - Check we have chips
    if store.has_chips {

      - If we do, Heat up the oven to 220°
      oven.setTemperature(220)

      - Place 200 grams of chips onto a tray.
      chips = new Chips(grams: 220)
      tray.place(chips)

      - Place the tray in the oven.
      oven.place(tray)

      - When the chips turn golden
      chips.are_golden.subscribeNext({

        - Take the tray out of the oven.
        oven.remove(tray)

        - Wait until they are cool
        chips.are_cool.subscribeNext({

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

But along with the "spagetti flow" we're now staring to get a "pyramid of doom". We need to be smarter than this, we need to start thinking of ways of pushing things back to the left again.

Luckily ReactiveX gives an arsenal of such ways but first I need to introduce you to the concept of __Streams__.

__Streams__ in ReactiveX are what connect __Observables__ and __Subscribers__ together. They represent events over time and ReactiveX allows us to operate on these __Streams__ to do some interesting things.

To make this easier to grasp lets think of a situation, lets imagine two people sit down and each order a plate of chips. How would we represent that collection of orders? Well via a collection of course! Namely an Array.

```
orders = [
  ChipsOrder,
  ChipsOrder
]
```

So here we have an array which contains two orders for each of those people. If we wanted to make each order we would loop through them like so:

```
orders = [
  ChipsOrder,
  ChipsOrder
]

orders.forEach {
  order.make()
}
```

So we can now take a number of orders and process them, however we have a problem, this Array is just a Data Type. Data from a Data Type is processed instantly and there is no way to know if the array changes without an __Observable__.

We want to be able to process new orders when they come in, What we need is some sort of Time Type; Enter __Streams__.

Arrays and __Streams__ are very similar in nature, they are both Collections and both have operations you can perform upon them to produce a new collection. So why should we use a __Stream__ for the above problem ?

In real-life we have two types of dimensions, Time and Space. Well in the digital world we have the same thing, Flow and Data.

- The flow dimension is how our application flows (i.e what should it do when and in what order.). This is similar to our time dimension.
- The data dimension is what is in our application (i.e Orders, Plates Of Chips). This is similar to our space dimension.

In the past we have used Arrays and Functions to model both of these dimensions but that complicates things. When using Arrays in the Data Dimension its pretty simple, for example arrays are great for a fixed set of data like a list of items on a menu especially as we are interested in the entire array.

```
menu_items = [
Burger,
Chips
]
```

But when using it for the Flow Dimension it gets complicated:

```
orders = [
  ChipsOrder,
  ChipsOrder
]

orders.forEach {
  order.make()
}
```

Like mentioned above we need some sort of __Observable__ to let us know when the array changes since we are only interested in new items. You could of course just create some kind of function for the Robo Chef which is called when a new order is placed:

```
chef.make_order(ChipsOrder)
```

But here is the real crux of the situation with this we are micro-managing our Robo Chef. We are telling it what to do in the moment rather than it's purpose in life.

Traditional ways of writing code is the equivalent of writing "Take this order and make it", we are bossing our chef around like a soldier. When we are writing reactivley we are laying down the purpose and the rules for our Chef, "Whenever you get an order I want you to make it".

This expresses the vision of what needs to be achieved and allows our Chef to think on it's own, we can see instantly at any point in time what our chef will be doing, it becomes a character rather than a object. Character based programming.

So how do we use reactive programming and __Streams__ to describe what our Robo Chef should be doing, rather than telling it what to do.

Well first we need to explore what a __Stream__ actually is.

In the Data Dimension like the Space Dimension we live in, an Array is a collection of things. Like our menu of items:

```
menu_items = [
Burger,
Chips
]
```

In the Flow Dimension like the Time Dimension, a __Stream__ is a collection of events. For example when two people order, it will look something like this:

```
---Order1---Order2--->
```

This RxMarble Timeline represents an event for one order being placed followed by another event for the second order being placed. This timeline is known as a __Stream__.

An __Observable__ emits this __Stream__ which a __Subscriber__ listens to. So how do we handle these events?

Well if you've used Arrays, you already have:

```
orders = [
  ChipsOrder,
  ChipsOrder
]

orders.forEach {
  order.make()
}
```

Above we iterate each item of the array, we can do the same thing for Streams:

```
- new_order: ---Order1---Order2--->

new_order.subscribeNext {
  order.make()
}
```

You may have noticed that in this example its called `subscribeNext` rather than `forEach`, why this is will be explained later. __Streams__ allow us to process a sequence of events *if* and *when* they happen, which make async programming especially easy to deal with.

## Operators

Like an Array, __Streams__ also have operators that can be used upon them. Almost any you use for an array is also avaliable for a __Stream__. On top of those we also gain access to special time based operators for our __Streams__.

 
