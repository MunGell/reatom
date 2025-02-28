<div align="center">

[![reatom logo](https://artalar.github.io/reatom/logos/logo.svg)](https://artalar.github.io/reatom)

[![npm](https://img.shields.io/npm/v/@reatom/core?style=flat-square)](https://www.npmjs.com/package/@reatom/core)
![npm type definitions](https://img.shields.io/npm/types/@reatom/core?style=flat-square)
[![npm bundle size](https://img.shields.io/bundlephobia/minzip/@reatom/core?style=flat-square)](https://bundlephobia.com/result?p=@reatom/core)
![GitHub](https://img.shields.io/github/license/artalar/reatom?style=flat-square)

<br/>
<br/>
</div>

Reatom is **declarative** and **reactive** state manager, designed for both simple and complex applications. 

> **IMPORTANT!** Current state is **Work In Progress**.
> At the moment we do not recommend to use `reatom` in production, but... We look forward to your feedback and suggestions to improve the API

> **v1.0.0 schedule**: October 2019

## Goals and features

- 🐣 **simple abstraction** and friendly DX: minimum boilerplate and tiny API
- ❗️ **static typed**: best type inferences
- ⚡ **performance**: performant updates for partial state chagnes
- 🗜 **small size**: [2 KB](https://bundlephobia.com/result?p=@reatom/core) gzipped
- 📦 **modular**: reusable instances (SSR)
- 🍴 **lazy**: solution for code splitting out of the box
- 🧪 **testing**: simple mocking
- 🛠 **debugging**: immutable data, devtools (redux ecosystem support by adapter)
- 🔮 **deterministic**: declarative and predictable specification of state shape and its mutations
- 👴 **ES5 support**: by polyfills
- 🧯 **reliable**: predictable flow exceptions
- synchronous [glitch](https://stackoverflow.com/questions/25139257/terminology-what-is-a-glitch-in-functional-reactive-programming-rx) free: resolve [diamond problem](https://github.com/artalar/reatom/blob/master/packages/core/src/__tests__/diamond.ts)
- simple integration with other libraries (Observable, redux ecosystem, etc)
- awkward to write bad code
- easy to write good code

## Description

Reatom is a blend of the one-way data flow (by [flux](https://github.com/facebook/flux) and global store) and decentralized [atoms](https://github.com/calmm-js/kefir.atom/blob/master/README.md#related-work) for [deterministic](https://en.wikipedia.org/wiki/Deterministic_algorithm) and flexible description of state and its changes.

> Inspired by [redux](https://github.com/reduxjs/redux), [kefir](https://github.com/kefirjs/kefir), [effector](https://github.com/zerobias/effector)


Data flow diagram:

<div align="center">

![reatom data flow](https://artalar.github.io/reatom/flow.svg)

</div>

## Installation

```sh
npm i @reatom/core
```
or
```sh
yarn add @reatom/core
```

## Usage
[Open in CodeSandbox](https://codesandbox.io/s/reatom-intro-jlepp)

```js
import {
  declareAction,
  declareAtom,
  map,
  combine,
  createStore,
} from '@reatom/core'

/** Actions */
const increment = declareAction()
const add = declareAction()

/** Atoms */
const countAtom = declareAtom(1, on => [
  on(increment, state => state + 1),
  on(add, (state, payload) => state + payload),
])
const isOddAtom = map(countAtom, count => Boolean(count % 2))
const rootAtom = combine({ count: countAtom, isOdd: isOddAtom })

/** Store */
const store = createStore(rootAtom)

store.subscribe(countAtom, count => console.log('count: ', count))
store.subscribe(isOddAtom, isOdd => console.log('isOdd: ', isOdd))

store.dispatch(increment())
// count: 2
// isOdd: false

store.dispatch(add(2))
// count: 4
// here `isOdd` subscriber will not be called because its value is not changed
```

## Packages
| Package | Version | Size
--------|---------|----
| [`@reatom/core`](https://artalar.github.io/reatom/#/packages/reatom-core) | [![npm](https://img.shields.io/npm/v/@reatom/core?style=flat-square)](https://www.npmjs.com/package/@reatom/core) | [![npm bundle size](https://img.shields.io/bundlephobia/minzip/@reatom/core?style=flat-square)](https://bundlephobia.com/result?p=@reatom/core)
| [`@reatom/react`](https://artalar.github.io/reatom/#/packages/reatom-react) | [![npm](https://img.shields.io/npm/v/@reatom/react?style=flat-square)](https://www.npmjs.com/package/@reatom/react) | [![npm bundle size](https://img.shields.io/bundlephobia/minzip/@reatom/react?style=flat-square)](https://bundlephobia.com/result?p=@reatom/react)
| [`@reatom/observable`](https://artalar.github.io/reatom/#/packages/reatom-observable) | [![npm](https://img.shields.io/npm/v/@reatom/observable?style=flat-square)](https://www.npmjs.com/package/@reatom/observable) | [![npm bundle size](https://img.shields.io/bundlephobia/minzip/@reatom/observable?style=flat-square)](https://bundlephobia.com/result?p=@reatom/observable)
| [`@reatom/babel-plugin`](https://artalar.github.io/reatom/#/packages/reatom-babel-plugin) | [![npm](https://img.shields.io/npm/v/@reatom/babel-plugin?style=flat-square)](https://www.npmjs.com/package/@reatom/babel-plugin) | -
| [`@reatom/debug`](https://artalar.github.io/reatom/#/packages/reatom-debug) | [![npm](https://img.shields.io/npm/v/@reatom/debug?style=flat-square)](https://www.npmjs.com/package/@reatom/debug) | [![npm bundle size](https://img.shields.io/bundlephobia/minzip/@reatom/debug?style=flat-square)](https://bundlephobia.com/result?p=@reatom/debug)

## Motivation

> **NOTE.** Please do not consider these arguments as a way to dissuade you from using these libraries. These are very interesting projects and they deserve your attention. This list only shows the motivation for creating Reatom. 

<!-- 

### State management Zen

// https://en.wikipedia.org/wiki/Zen_of_Python

Guiding principles of state manager:

- State model must be determined
- State changes must be determined

-->

### Why not Redux
[link to repository](https://github.com/reduxjs/redux)

- Selectors are not inspectable (lacking in devtools).
- Difficult static type inference (every selector must know the full path to parent state).
- Hard for modular architecture (every selector must know about parent state).
- Separation of interfaces (reducers and selectors) complicates the prototyping of separated domains.
- Selectors - **manual** API for state. They must be **manually** described and memoized.
- Selectors are executed after state change at subscriptions - error in selector will throw an error. Also it is not possible (possible, but really hard) to restore the previous valid state.
- Classic reducer API and [static] type descriptions have a lot of boilerplate.
- Selectors are "runtime" oriented; if a "feature" uses any part of the state (by selector) and later you remove this part, you will get an error only when mounting your "feature" at runtime (if you do not have static typing). The single solution is to connect all features statically by imports.
- Middleware is a confusing pattern that can unexpectedly modify the behavior of the store. For example, actions for redux-thunk do not log.
  <!-- - Memorized selectors do extra computations by default, but it is definitely unnecessary in SSR -->
  > Some problems can be solved by various fabric functions and third party libriaries. This makes it diffcuilt to reuse solutions across multiple projects.

### Why not Effector
[link to repository](https://github.com/zerobias/effector)

- Effector is about atomic **stores** — it uses stateful approach that has certain problems:
  - probable memory leaks
    > Like any other observable libraries
  - difficult [store] instance reusability (concurrency problems with SSR)
    > It can be solved, but it is better to solve it by design of a library architecture and API
- Asynchronous and probably cyclic dependencies specification
  <details>
  <summary>show example</summary>

  ```js
  const store = createStore(0)
  store.watch(console.log)

  const event = createEvent()
  store.on(event, (state, payload) => payload)

  event(1000)
  // console.log: 1000

  // In any time and in any project part
  const otherEvent = createEvent()
  store.on(otherEvent, (state, payload) => payload)

  otherEvent(2000)
  // console.log: 2000
  ```
  </details>

- The [size](https://bundlephobia.com/result?p=effector)
- [Throw in reducer does not cancel the computations in other reducers](https://github.com/zerobias/effector/issues/90)

### Why not MobX
[link to repository](https://github.com/mobxjs/mobx)

- Large bundle size, unstandardized syntax (decorators), ES5 limitations.
- Doesn't move to separate _model_ and _view_.
- Runtime semantic and mutable state (difficult to debug).
- [Proxy pattern](https://en.wikipedia.org/wiki/Proxy_pattern) lacks a visual part of code semantic.
- It is complicated under the hood and [it can be complicated when one has to work with complex data-structures](https://twitter.com/art_al_ar/status/1162769896025075719)
- [And others...](https://mobx.js.org/best/pitfalls.html)

## Community
Follow us on Twitter [@reatomjs](https://twitter.com/reatomjs)

Telegram
- [@reatom_en](https://t.me/reatom_en) — english
- [@reatom_ru](https://t.me/reatom_ru) — russian

## Mass media
- [Reatom vs Redux. Part 1](https://soundcloud.com/5minreact/061-reatom-vs-redux) (5minreact, russian podcast)
- [Reatom vs MobX. Part 2](https://soundcloud.com/5minreact/062-reatom-vs-mobx) (5minreact, russian podcast)

---

Next:

> - <a href="https://artalar.github.io/reatom/#/glossary">Glossary</a>
> - <a href="https://artalar.github.io/reatom/#/examples">Examples</a>
> - <a href="https://artalar.github.io/reatom/#/faq">FAQ</a>
> - <a href="https://artalar.github.io/reatom/#/contributing">Contributing</a>

## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
<table>
  <tr>
    <td align="center"><a href="https://github.com/artalar"><img src="https://avatars0.githubusercontent.com/u/27290320?v=4" width="100px;" alt="Arutyunyan Artyom"/><br /><sub><b>Arutyunyan Artyom</b></sub></a><br /><a href="https://github.com/artalar/reatom/commits?author=artalar" title="Code">💻</a> <a href="#ideas-artalar" title="Ideas, Planning, & Feedback">🤔</a> <a href="https://github.com/artalar/reatom/commits?author=artalar" title="Tests">⚠️</a> <a href="#infra-artalar" title="Infrastructure (Hosting, Build-Tools, etc)">🚇</a> <a href="#tutorial-artalar" title="Tutorials">✅</a> <a href="#review-artalar" title="Reviewed Pull Requests">👀</a> <a href="#example-artalar" title="Examples">💡</a> <a href="https://github.com/artalar/reatom/commits?author=artalar" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/belozer"><img src="https://avatars1.githubusercontent.com/u/1655916?v=4" width="100px;" alt="Sergey Belozyorcev"/><br /><sub><b>Sergey Belozyorcev</b></sub></a><br /><a href="https://github.com/artalar/reatom/commits?author=belozer" title="Code">💻</a> <a href="#ideas-belozer" title="Ideas, Planning, & Feedback">🤔</a> <a href="https://github.com/artalar/reatom/commits?author=belozer" title="Tests">⚠️</a> <a href="#infra-belozer" title="Infrastructure (Hosting, Build-Tools, etc)">🚇</a> <a href="#tutorial-belozer" title="Tutorials">✅</a> <a href="#review-belozer" title="Reviewed Pull Requests">👀</a> <a href="#design-belozer" title="Design">🎨</a> <a href="#example-belozer" title="Examples">💡</a> <a href="https://github.com/artalar/reatom/commits?author=belozer" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/Wroud"><img src="https://avatars2.githubusercontent.com/u/811729?v=4" width="100px;" alt="Alexey"/><br /><sub><b>Alexey</b></sub></a><br /><a href="https://github.com/artalar/reatom/commits?author=Wroud" title="Code">💻</a> <a href="#ideas-Wroud" title="Ideas, Planning, & Feedback">🤔</a> <a href="#infra-Wroud" title="Infrastructure (Hosting, Build-Tools, etc)">🚇</a> <a href="https://github.com/artalar/reatom/commits?author=Wroud" title="Tests">⚠️</a> <a href="#platform-Wroud" title="Packaging/porting to new platform">📦</a> <a href="#plugin-Wroud" title="Plugin/utility libraries">🔌</a> <a href="#tool-Wroud" title="Tools">🔧</a></td>
    <td align="center"><a href="https://github.com/antonk52"><img src="https://avatars1.githubusercontent.com/u/5817809?v=4" width="100px;" alt="Anton"/><br /><sub><b>Anton</b></sub></a><br /><a href="https://github.com/artalar/reatom/commits?author=antonk52" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/atassis"><img src="https://avatars2.githubusercontent.com/u/5769345?v=4" width="100px;" alt="Taymuraz Kaytmazov"/><br /><sub><b>Taymuraz Kaytmazov</b></sub></a><br /><a href="https://github.com/artalar/reatom/commits?author=atassis" title="Documentation">📖</a></td>
    <td align="center"><a href="https://www.linkedin.com/in/илья-рябчинский-678a78188"><img src="https://avatars1.githubusercontent.com/u/20639767?v=4" width="100px;" alt="Ilya"/><br /><sub><b>Ilya</b></sub></a><br /><a href="https://github.com/artalar/reatom/commits?author=ilyaryabchinski" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/MunGell"><img src="https://avatars2.githubusercontent.com/u/812976?v=4" width="100px;" alt="Shmavon Gazanchyan"/><br /><sub><b>Shmavon Gazanchyan</b></sub></a><br /><a href="https://github.com/artalar/reatom/commits?author=MunGell" title="Documentation">📖</a></td>
  </tr>
  <tr>
    <td align="center"><a href="https://github.com/godested"><img src="https://avatars2.githubusercontent.com/u/22772547?v=4" width="100px;" alt="Ilya Zaitsev"/><br /><sub><b>Ilya Zaitsev</b></sub></a><br /><a href="https://github.com/artalar/reatom/commits?author=godested" title="Documentation">📖</a></td>
  </tr>
</table>

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!
