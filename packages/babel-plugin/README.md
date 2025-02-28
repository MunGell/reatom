<div align="center">
<br/>

[![reatom logo](https://artalar.github.io/reatom/logos/logo.svg)](https://artalar.github.io/reatom)

</div>

# @reatom/babel-plugin
[![npm](https://img.shields.io/npm/v/@reatom/babel-plugin?style=flat-square)](https://www.npmjs.com/package/@reatom/babel-plugin) 

[Open in docs](https://artalar.github.io/reatom/#/packages/reatom-babel-plugin)

## Install

```sh
npm i -D @reatom/babel-plugin
```
or
```sh
yarn add @reatom/babel-plugin --dev
```

> NOTE. **@reatom/babel-plugin** depends on and works with [@reatom/core](https://artalar.github.io/reatom/#/reatom-core).

## Usage

### Step 1.
Add the plugin via .babelrc or babel-loader.

```json
{
  "plugins": ["@reatom/babel-plugin"]
}
```

### Step 2.
```js
import { declateAction, declareAtom, map, combine } from '@reatom/core' 

const myAction = declareAction()
const myAtom = declareAtom({}, () => [])
const mySelector = map(myAtom, atomState => atomState)
const myCombine = combine([myAtom, mySelector])
```
Will be converted to:
```js
import { declateAction, declareAtom, map, combine } from '@reatom/core' 

const myAction = declareAction('myAction')
const myAtom = declareAtom('myAtom', {}, () => [])
const mySelector = map('mySelector', myAtom, atomState => atomState)
const myCombine = combine('myCombine', [myAtom, mySelector])
```
