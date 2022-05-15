# Redux Saga

## Redux middleware

It provides a third-party extension point between dispatching an action, and the moment it reaches the reducer. People use Redux middleware for logging, crash reporting, talking to an asynchronous API, routing, and more.

![redux middleware image](/images/1_LXvfJLM7DzJ8uxxC5xRDYg.png)

```
const customMiddleware = function(store) {
  // Called when calling applyMiddleware so our middleware can have access to the store

  return function(next) {
    // next is the following action to be run after this middleware

    return function(action) {
      // finally, this is where our logic lives for our middleware.

    }
  }
}
```

<br>

## About Redux-Saga

`redux-saga` is a library that aims to make application side effects (i.e. asynchronous things like data fetching and impure things like accessing the browser cache) easier to manage, more efficient to execute, easy to test, and better at handling failures.

`redux-saga` is a redux middleware, which means this thread can be started, paused and cancelled from the main application with normal redux actions, it has access to the full redux application state and it can dispatch redux actions as well.

It uses an ES6 feature called Generators to make those asynchronous flows easy to read, write and test.

<br>

## Glossary

This is a glossary of the core terms in Redux Saga.

### Effect

An effect is a plain JavaScript Object containing some instructions to be executed by the saga middleware.

### Task

A task is like a process running in background. In a `redux-saga` based application there can be multiple tasks running in parallel. You create tasks by using the `fork` function

```
import { fork } from "redux-saga/effects"

function* saga() {
  ...
  const task = yield fork(otherSaga, ...args)
  ...
}
```

### Blocking/Non-blocking call

A Blocking call means that the Saga yielded an Effect and will wait for the outcome of its execution before resuming to the next instruction inside the yielding Generator.
A Non-blocking call means that the Saga will resume immediately after yielding the Effect.

```
import { call, cancel, join, take, put } from "redux-saga/effects"

function* saga() {
  yield take(ACTION)                            // Blocking: will wait for the action
  yield call(ApiFn, ...args)                    // Blocking: will wait for ApiFn (If ApiFn returns a Promise)
  yield call(otherSaga, ...args)                // Blocking: will wait for otherSaga to terminate

  yield put(...)                                // Non-Blocking: will dispatch within internal scheduler

  const task = yield fork(otherSaga, ...args)   // Non-blocking: will not wait for otherSaga
  yield cancel(task)                            // Non-blocking: will resume immediately
  // or
  yield join(task)                              // Blocking: will wait for the task to terminate
}
```

### Watcher/Worker

Refers to a way of organizing the control flow using two separate Sagas

- The watcher: will watch for dispatched actions and fork a worker on every action
- The worker: will handle the action and terminate

```
function* watcher() {
  while (true) {
    const action = yield take(ACTION)
    yield fork(worker, action.payload)
  }
}

function* worker(payload) {
  // ... do some stuff
}
```
