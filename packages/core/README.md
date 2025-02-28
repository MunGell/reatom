<div align="center">
<br/>

[![reatom logo](https://artalar.github.io/reatom/logos/logo.svg)](https://artalar.github.io/reatom)

</div>

# @reatom/core

Core package of [Reatom](https://github.com/artalar/reatom) state manager. 

[![npm](https://img.shields.io/npm/v/@reatom/core?style=flat-square)](https://www.npmjs.com/package/@reatom/core)
![npm type definitions](https://img.shields.io/npm/types/@reatom/core?style=flat-square)
[![npm bundle size](https://img.shields.io/bundlephobia/minzip/@reatom/core?style=flat-square)](https://bundlephobia.com/result?p=@reatom/core)
![GitHub](https://img.shields.io/github/license/artalar/reatom?style=flat-square)

[Open in docs](https://artalar.github.io/reatom/#/packages/reatom-core)

> Reatom is **declarative** and **reactive** state manager, designed for both simple and complex applications. See [docs](https://artalar.github.io/reatom/).

> **IMPORTANT!** Current state is **Work In Progress**.
> At the moment we do not recommend to use `reatom` in production, but... We look forward to your feedback and suggestions to improve the API

> **v1.0.0 schedule**: October 2019


## Install

```sh
npm i @reatom/core
```
or
```sh
yarn add @reatom/core
```

## Usage

[Open in CodeSandbox](https://codesandbox.io/s/reatomcore-demo-28t3d)

```js
import {
  declareAction,
  declareAtom,
  map,
  combine,
  createStore
} from '@reatom/core'

/**
 * Step 1.
 * Declare actions
 */
const increment = declareAction()

/**
 * Step 2.
 * Declare atoms (like reducers or models)
 */
const counterAtom = declareAtom(0, on => [
  on(increment, state => state + 1)
])
const counterDoubledAtom = map(counterAtom, value => value * 2)
const countersShapeAtom = combine({ 
  counter: counterAtom, 
  counterDoubled: counterDoubledAtom
})

/**
 * Step 3.
 * Create store entry point
 */
const store = createStore(countersShapeAtom)

/**
 * Step 4.
 * Dispatch action
 */
store.dispatch(increment())

/**
 * Step 5.
 * Get action results
 */
console.log(store.getState(counterAtom))
// ➜ 1

console.log(store.getState(counterDoubledAtom))
// ➜ 2

console.log(store.getState(countersShapeAtom))
// ➜ { counter: 1, counterDoubled: 2 }
```
