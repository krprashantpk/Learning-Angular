# RxJs- [RxJs](#rxjs)

- [RxJs- RxJs](#rxjs--rxjs)
  - [Observable](#observable)
    - [Pull versus Push](#pull-versus-push)
      - [What is Pull?](#what-is-pull)
      - [What is Push?](#what-is-push)
    - [Anatomy of an Observable](#anatomy-of-an-observable)
      - [Creating Observables](#creating-observables)
      - [Subscribing to Observables](#subscribing-to-observables)
      - [Executing Observables](#executing-observables)
      - [Disposing Observable Executions](#disposing-observable-executions)

## Observable

### Pull versus Push

#### What is Pull?

In Pull systems, the Consumer determines when it receives data from the data Producer. The Producer itself is unaware of when the data will be delivered to the Consumer.

**Every JavaScript Function** is a Pull system. The function is a Producer of data, and the code that calls the function is consuming it by "pulling" out a _single return value_ from its call.

ES2015 introduced generator functions and iterators (function), another type of Pull system. Code that calls iterator.next() is the Consumer, "pulling" out _multiple values_ from the iterator (the Producer).

#### What is Push?

In Push systems, the Producer determines when to send data to the Consumer. The Consumer is unaware of when it will receive that data.

**Promises** are the most common type of Push system in JavaScript today. A Promise (the Producer) delivers a resolved value to registered callbacks (the Consumers), but unlike functions, it is the Promise which is in charge of determining precisely when that value is "pushed" to the callbacks.

RxJS introduces **Observables**, a new Push system for JavaScript. An Observable is a Producer of multiple values, "pushing" them to Observers (Consumers).

### Anatomy of an Observable

Observables are **created** using new Observable or a creation operator, are **subscribed** to with an Observer, **execute** to _deliver next / error / complete notifications_ to the Observer, and their execution may be **disposed**.

#### Creating Observables

The Observable constructor takes one argument: the subscribe function.

```typescript
import { Observable } from "rxjs";

const observable = new Observable(function subscribe(subscriber) {
  const id = setInterval(() => {
    subscriber.next("hi");
  }, 1000);
});
```

> _Observables can be created with new Observable. Most commonly, observables are created using **creation functions, like of, from, interval**, etc_.

#### Subscribing to Observables

The Observable observable in the example can be subscribed to, like this:

```typescript
observable.subscribe((x) => console.log(x));
```

> _This shows how subscribe calls are not shared among multiple Observers of the same Observable. When calling observable.subscribe with an Observer, the function subscribe in new Observable(function subscribe(subscriber) {...}) is run for that given subscriber. **Each call to observable.subscribe triggers its own independent setup for that given subscriber**._

#### Executing Observables

There are three types of values an Observable Execution can deliver:

- "**Next**" notification: sends a value such as a Number, a String, an Object, etc.
- "**Error**" notification: sends a JavaScript Error or exception.
- "**Complete**" notification: does not send a value.

```typescript
import { Observable } from "rxjs";

const observable = new Observable(function subscribe(subscriber) {
  try {
    subscriber.next(1);
    subscriber.next(2);
    subscriber.next(3);
    subscriber.complete();
  } catch (err) {
    subscriber.error(err); // delivers an error if it caught one
  }
});
```

#### Disposing Observable Executions

Because Observable Executions may be infinite, and it's common for an Observer to want to abort execution in finite time, we need an API for canceling an execution.

Each Observable must define how to dispose resources of that execution when we create the Observable using create(). You can do that by returning a custom unsubscribe function from within function subscribe().

```typescript
const observable = new Observable(function subscribe(subscriber) {
  // Keep track of the interval resource
  const intervalId = setInterval(() => {
    subscriber.next("hi");
  }, 1000);

  // Provide a way of canceling and disposing the interval resource
  return function unsubscribe() {
    clearInterval(intervalId);
  };
});
```
