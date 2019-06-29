# Throttling and Debouncing in JavaScript
##### Are you excessively invoking functions and hampering performance?
It’s not uncommon that you might need to tackle performance issues within your JavaScript applications every once in a while.

The notion of throttling and debouncing gives developers specific control over the rate that functions are executed and are a need to know for any user interface developer. They become especially useful when used in conjunction with event handler assignments.

Having greater control over the rate at which a function is executed can be particularly useful in situations where functions are being executed excessively and in some cases unnecessarily. You may have come across scenarios like this when binding to mouse movement and window events such as scrolling.

## What’s the difference between throttling and debouncing?
### Throttling
If you think of a car throttle. The amount you push your foot down limits the amount of gas going into the engine. In this context, we want to limit the amount that a function is executed. Whereas in a car, we limit the amount of gas relative to how far your foot is down, in JavaScript, we are limiting the amount a function is executed over a time period. I find a better analogy to be either how the lottery numbers are drawn, say one every five seconds or a greater one being ordering drinks at a bar. Consider the latter, you go to a bar and the barman has a policy of only allowing you to order a drink every 45 minutes or things get crazy. You order a drink in the first minute and he hands one over. You then try and order one every minute thereafter being denied by the barman until the 45th minute when the then tired barman hands over the next drink. You won’t get another drink for another 45 minutes. Sometimes with throttling you may want one last invocation to happen after the throttle is over, this being one of the denied invocations. Referring to the last analogy, imagine you order a drink in the 15th minute and get denied but in the 45th minute, you don’t order but the barman sends a waiter over with the drink from that 15th minute order because he feels sorry for you 😁

### Debouncing
Debouncing works a little differently and can be a little harder to explain (if anyone has a good analogy, please leave a note). With debouncing, it’s almost like we are saying “Hey, I’m not going to execute that function until I know there are no more changes to be made”. It is almost like we don’t execute our function until everyone else is happy and we’re clear to proceed. Imagine ordering food at a restaurant. You start listing off items to the waiter/waitress and at the end they ask “Is that everything?” If it is, they leave you to it and go get your food and drinks sorted out etc. If it isn’t, you add to the order and then they ask you again until they are clear to proceed (there are some great responses left at the end of this article with some further explanations/analogies).

### Examples
An example for when to throttle a function execution might be when we are binding some logic to a button click. We may have a button that increases the quantity of an item in a shopping basket and we want to stop the user from being able to click the button frantically to increase the quantity so we only allow one click per second. Most shopping basket quantities have an input field so this would be redundant any how. Alternatively we may bind some function to mouse movement and only want to execute every 250ms that the cursor is moved.
For debouncing, we could have some auto save feature in our application. With auto save on the application tries to save the state of our application every time the user makes an update or has some interaction. It waits 5 seconds to make sure no other updates or interactions are made before saving the state else it records the new state and repeats the process.
Implementing throttle and debounce
There are various implementations of throttle and debounce but the majority will achieve the same goal. The implementation of both throttle and debounce revolve around the clever use of setTimeout.

For those in camp TL;DR who just want a demo, I’ve got you covered

## Debounce
##### Debounce is the simpler of the two to implement.
```
const debounce = (func, delay) => {
  let inDebounce
  return function() {
    const context = this
    const args = arguments
    clearTimeout(inDebounce)
    inDebounce = setTimeout(() => func.apply(context, args), delay)
  }
}
```
We are passing a function(`func`) and a delay(`delay`) into the debounce function. inDebounce is a variable that we use to track the delay period.

If we are invoking for the first time, our function will execute at the end of our delay. If we invoke once and then invoke again before the end of our delay, the initial delay is cleared and a new delay process started whereby at the end, our second function invocation will be executed (It’s much easier to understand by reading the code and playing with the demo 😉).

Lastly, we integrate our debounce function into our event handling assignment.

```debounceBtn.addEventListener('click', debounce(function() {
  console.info('Hey! It is', new Date().toUTCString());
}, 3000));
```

In this example we are debouncing the call by 3 seconds at which point we print the date.

## Throttle
What makes throttle a little taxing is that its desired behaviour could be interpreted differently by different people.

Let’s start by simply limiting the rate at which we execute a function.
```
const throttle = (func, limit) => {
  let inThrottle
  return function() {
    const args = arguments
    const context = this
    if (!inThrottle) {
      func.apply(context, args)
      inThrottle = true
      setTimeout(() => inThrottle = false, limit)
    }
  }
}
```

The first invocation of our function will execute and sets the limit period inThrottle. We can invoke our function during this period but it will not be executed until the throttle period has passed. Once it has passed, the next invocation will be executed and the process repeats

```ThrottleBtn.addEventListener('click', throttle(function() {
  return console.log('Hey! It is', new Date().toUTCString());
}, 1000));
```

But what about our last invocation? If it’s in the limit period it will be ignored and maybe that’s not what we want. To put this into context, if we bound to mouse movement for say a drag or resize event and missed out the last invocation we would never get the desired result as the final invocation would always be ignored. So we need to catch this and execute it after the limit period (Thanks to worldwar for questioning the previous implementation which didn’t always work 100% as expected).

```
const throttle = (func, limit) => {
  let lastFunc
  let lastRan
  return function() {
    const context = this
    const args = arguments
    if (!lastRan) {
      func.apply(context, args)
      lastRan = Date.now()
    } else {
      clearTimeout(lastFunc)
      lastFunc = setTimeout(function() {
        if ((Date.now() - lastRan) >= limit) {
          func.apply(context, args)
          lastRan = Date.now()
        }
      }, limit - (Date.now() - lastRan))
    }
  }
}
```
This implementation ensures that we catch and execute that last invocation. We also invoke it at the correct time. We do this by creating a variable lastRan which is a timestamp of the last invocation. We can then use this to determine if the last invocation took place within the throttle limit. We can also use lastRan to determine whether the throttled function has ran at all. This makes the previous variable inThrottle redundant.

One way to think about this implementation of throttle is like a chaining debounce. Each time the debounce waiting period is reduced accordingly.

throttle has some interesting possible variations. For example, you could store all the ignored executions and run them all at the end in order.

## To conclude
Throttling and debouncing the execution of your functions is something that the majority of developers should know. It’s a technique that can vastly improve the performance of your applications.

Have a play around with the demo!
