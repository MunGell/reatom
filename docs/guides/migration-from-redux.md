# Migration from Redux

This is a simple instruction to replace approaches in writing code with Reatom if you have used Redux before.

> NOTE. This guide is still in the process of development and will be periodically supplemented with details.

## Actions

Reatom uses [action creators](https://redux.js.org/basics/actions#action-creators) by default.

**Redux**

```js
const ADD_TODO = 'ADD_TODO'

function addTodo(text) {
  return {
    type: ADD_TODO,
    payload: text,
  }
}
```

**Reatom**

```js
import { declareAction } from '@reatom/core'

const addTodo = declareAction()
```

> NOTE. See more info about [actions](/glossary?id=action)

## Reducers

In Reatom world [atoms](/glossary?id=atom) act as reducers.

**Redux**

```js
const SET_VISIBILITY_FILTER = 'SET_VISIBILITY_FILTER'

export const setVisibilityFilter = (payload) => ({
  type: SET_VISIBILITY_FILTER
  payload
})

export function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return {
        ...state,
        visibilityFilter: action.payload
      }
    default:
      return state
  }
}
```

**Reatom**

```js
import { declareAction, declareAtom } from '@reatom/core'

export const setVisibilityFilter = declareAction()

export const todoAppAtom = declareAtom(initialState, on => [
  on(setVisibilityFilter, (state, payload) => ({
    ...state,
    visibilityFilter: payload,
  })),
])
```

> NOTE. See more info about [atoms](/glossary?id=atom)

## Selectors

If your used [reselect](https://github.com/reduxjs/reselect) for creating memoized selectors, then you don't have to do it anymore. [Atoms](/glossary?id=atom) solve this problem.

**Redux**

```js
import { createSelector } from 'reselect'

const getVisibilityFilter = state => state.visibilityFilter
const getTodos = state => state.todos

export const getVisibleTodos = createSelector(
  [getVisibilityFilter, getTodos],
  (visibilityFilter, todos) => {
    switch (visibilityFilter) {
      case 'SHOW_ALL':
        return todos
      case 'SHOW_COMPLETED':
        return todos.filter(t => t.completed)
      case 'SHOW_ACTIVE':
        return todos.filter(t => !t.completed)
    }
  },
)
```

**Reatom**

```js
import { map, combine } from '@reatom/core'

export const visibleTodosAtom = map(
  combine([visibilityFilterAtom, todosAtom]),
  ([visibilityFilter, todos]) => {
    switch (visibilityFilter) {
      case 'SHOW_ALL':
        return todos
      case 'SHOW_COMPLETED':
        return todos.filter(t => t.completed)
      case 'SHOW_ACTIVE':
        return todos.filter(t => !t.completed)
    }
  },
)
```

## Creating store

Creating a store is similar to how you did it in Redux.

**Redux**

```js
import { createStore } from 'redux'

const store = createStore(rootReducer, preloadedData)
```

**Reatom**

```js
import { createStore } from '@reatom/core'

const store = createStore(rootAtom, preloadedData)
```

> NOTE. All arguments are optional. You can create store like `createStore()`

## Getting state

Getting state is similar to how you did it in Redux, but with small differences.

**Redux**

```js
store.getState()
```

**Reatom**

```js
// Get full store state
const storeState = store.getState()

// Get atom state
const atomState = store.getState(myAtom)
```

> NOTE. Use full store state only if needed, because this affects performance. In most cases you need to use atom state.

## Subscribing

Subscribing to the store is similar to how you did it in Redux, but with small differences.

**Redux**

```js
import { createSelector } from 'reselect'

const onVisibleTodosUpdate = createSelector(
  getVisibleTodos,
  todosState => {
    // ...do something
  },
)

const unsubscribe = store.subscribe(() =>
  onVisibleTodosUpdate(store.getState()),
)

unsubscribe()
```

**Reatom**

Subscribing to the atoms

```js
const unsubscribe = store.subscribe(visibleTodosAtom, todosState => {
  // Will be called after update atom state
  // ...do something
})

unsubscribe()
```

Subscribing to the actions

```js
// If you has some logic in middlewares now you can using it here

const unsubscribe = store.subscribe(action => {
  // Always called after dispatch (but after updating atoms)
  // ...do something
})

unsubscribe()
```

## Dispatching

Dispatching to the store is similar to how you did it in Redux.

**Redux**

```js
// via action creator
store.dispatch(addTodo('Hello Redux'))

// via object notation
store.dispatch({
  type: 'ADD_TODO',
  payload: 'Hello Redux',
})
```

**Reatom**

```js
// via declarated action
store.dispatch(addTodo('Hello Reatom'))

// via object notation (if needs)
store.dispatch({
  type: 'ADD_TODO',
  payload: 'Hello Reatom',
})
```

## Async actions

In Redux world your you need to use special libraries ([redux-thunk](github.com/reduxjs/redux-thunk) or [redux-saga](https://github.com/redux-saga/redux-saga)) for creating async actions. In Reatom world you don't need to use it. This role is played by [reactions](/glossary?id=action-reactions).

**Redux**

```js
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'

const store = createStore(reducer, applyMiddleware(thunk))

// It still recognizes plain object actions
store.dispatch({ type: 'INCREMENT' })

// But with thunk middleware, it also recognizes functions
store.dispatch(function(dispatch) {
  // ... which themselves may dispatch many times
  dispatch({ type: 'INCREMENT' })
  dispatch({ type: 'INCREMENT' })
  dispatch({ type: 'INCREMENT' })

  setTimeout(() => {
    // ... even asynchronously!
    dispatch({ type: 'DECREMENT' })
  }, 1000)
})
```

**Reatom**

```js
import { createStore, declareAction } from '@reatom/core'

const store = createStore()

const increment = declareAction()
const decrement = declareAction()

const incrementAsync = declareAction(({ dispatch }) => {
  // ... which themselves may dispatch many times
  dispatch(increment())
  dispatch(increment())
  dispatch(increment())

  setTimeout(() => {
    // ... even asynchronously!
    dispatch(decrement())
  }, 1000)
})

store.dispatch(incrementAsync())
```

Changing logic from sagas to effects could be painful. Redux-saga allows us to work with any external state management library through `runSaga`. Redux-saga uses [pattern](redux-saga.js.org/docs/api/#takepattern) to determine which action is needed

**Redux**

```js
import { createStore, applyMiddleware } from 'redux'
import createSagaMiddleware from 'redux-saga'
import { put, takeEvery, delay } from 'redux-saga/effects'

const sagaMiddleware = createSagaMiddleware()

const store = createStore(reducer, applyMiddleware(sagaMiddleware))

export function* incrementAsyncSaga() {
  yield delay(1000)
  yield put({ type: 'INCREMENT' })
}

export function* watchIncrementAsyncSaga() {
  yield takeEvery('INCREMENT_ASYNC', incrementAsyncSaga)
}

sagaMiddleware.run(watchIncrementAsyncSaga)

store.dispatch({ type: 'INCREMENT_ASYNC' })
```

**Reatom**

Usage with explicit action type

```js
import { createStore, declareAction } from '@reatom/core'
import { runSaga } from 'redux-saga'
import { put, takeEvery, delay } from 'redux-saga/effects'

const store = createStore()

const increment = declareAction(['INCREMENT'])
const decrement = declareAction(['INCREMENT_ASYNC'])

export function* incrementAsync() {
  yield delay(1000)
  yield put({ type: 'INCREMENT' })
}

export function* watchIncrementAsyncSaga() {
  yield takeEvery('INCREMENT_ASYNC', incrementAsync)
}

const options = {
  dispatch: store.dispatch,
  getState: store.getState,
}

runSaga(options, watchIncrementAsyncSaga)

store.dispatch({ type: 'INCREMENT_ASYNC' })
```

Or use the action creator. To do this, just use `getType()` method

```js
import { createStore, declareAction } from '@reatom/core'
import { runSaga, eventChannel } from 'redux-saga'
import { put, takeEvery, delay } from 'redux-saga/effects'

const store = createStore()

const increment = declareAction()
const incrementAsync = declareAction()

export function* incrementAsyncSaga() {
  yield delay(1000)
  yield put(increment())
}

export function* watchIncrementAsyncSaga() {
  yield takeEvery(incrementAsync.getType(), incrementAsyncSaga)
}

const options = {
  dispatch: store.dispatch,
  getState: store.getState,
  channel: eventChannel(emit => store.subscribe(emit)),
}

runSaga(options, watchIncrementAsyncSaga)

store.dispatch(incrementAsync())
```

## Async reducers

Reatom has a built in solution for lazy connections of atoms.

**Redux**

See [instruction](https://redux.js.org/recipes/code-splitting)

**Reatom**

```js
import { createStore } from '@reatom/core'

const store = createStore()

import('my/feature').then(module => {
  // Async connection of the atom to the store and subscribe to it
  // All deps of atom will be connected too
  store.subscribe(module.myAtom, atomState => {})
})
```

## Middleware

Reatom don't use middleware, because he considers it an ambiguous pattern. You can use [subscribe to actions](#subscribing) for these purposes.
