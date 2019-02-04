# ts-transform-graphql-tag

<!--
[![npm](https://img.shields.io/npm/v/ts-transform-graphql-tag.svg)](https://www.npmjs.com/package/ts-transform-graphql-tag)
[![Travis](https://img.shields.io/travis/firede/ts-transform-graphql-tag.svg)](https://travis-ci.org/firede/ts-transform-graphql-tag)
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Ffirede%2Fts-transform-graphql-tag.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2Ffirede%2Fts-transform-graphql-tag?ref=badge_shield) -->
[![license](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)

This repo is forked to attach sha256 hash of the transforming GraphQL Query to the output AST.

---

Compiles GraphQL tagged template strings using [graphql-tag](https://github.com/apollographql/graphql-tag) in TypeScript files.

The plugin was mostly inspired by great Babel's plugin [babel-plugin-graphql-tag](https://github.com/gajus/babel-plugin-graphql-tag).

## Motivation

Compiling GraphQL queries at the build time:

* reduces the script initialization time; and
* removes the `graphql-tag` dependency

Removing the `graphql-tag` dependecy from the bundle saves approx. 50 KB.

## Implementation

* Searches for imports of `graphql-tag` and removes them.
* Searches for [tagged template literals](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals) with `gql` identifier and compiles them using `graphql-tag`.

## Installation

The following command adds the packages to the project as a development-time dependency:

```sh
npm i --save-dev @quramy/ts-transform-graphql-tag
```

This also depends on `graphql` and `graphql-tag` so you'll need those in your project as well (if you don't already have them):

```sh
# usually, this is a production dependency
npm i graphql

# add this as a development-time dependency
npm i --save-dev graphql-tag
```

## Usage

### Integration with `Webpack`

If you using [Webpack](https://webpack.js.org/), there are two popular TypeScript loaders that support specifying custom transformers:

* [**awesome-typescript-loader**](https://github.com/s-panferov/awesome-typescript-loader), supports custom transformers since v3.1.3
* [**ts-loader**](https://github.com/TypeStrong/ts-loader), supports custom transformers since v2.2.0

Both loaders use the same setting `getCustomTransformers` which is an optional function that returns `{ before?: Transformer[], after?: Transformer[] }`.
In order to inject the transformer into compilation, add it to `before` transformers array, like: `{ before: [getTransformer()] }`.

#### `awesome-typescript-loader`

In the `webpack.config.js` file in the section where **awesome-typescript-loader** is configured as a loader:

```js
// 1. import `getTransformer` from the module
var getTransformer = require('@quramy/ts-transform-graphql-tag').getTransformer

// 2. create a transformer and add getCustomTransformer method to the loader config
var config = {
  /// ...
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        loader: 'awesome-typescript-loader',
        options: {
          // ... other loader's options
          getCustomTransformers: () => ({ before: [getTransformer()] })
        }
      }
    ]
  }
  /// ...
}
```

#### `ts-loader`

In the `webpack.config.js` file in the section where **ts-loader** is configured as a loader:

```js
// 1. import `getTransformer` from the module
var getTransformer = require('@quramy/ts-transform-graphql-tag').getTransformer

// 2. create a transformer and add getCustomTransformer method to the loader config
var config = {
  // ...
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        loader: 'ts-loader',
        options: {
          // ... other loader's options
          getCustomTransformers: () => ({ before: [getTransformer()] })
        }
      }
    ]
  }
  // ...
};
```

### Integration with `FuseBox`

[FuseBox](https://fuse-box.org) is a blazing fast (TypeScipt first) bundler/module loader.

In the `fuse.ts` file, you can configured like this:

```ts
// 1. import `getTransformer` from the module
import { getTransformer } from "@quramy/ts-transform-graphql-tag"

// 2. create a transformer and add it to the `transformers.before` config
const fuse = FuseBox.init({
  // ... other init options
  transformers: {
    before: [
      getTransformer()
    ]
  }
})
```

## Example

**before**

```ts
// with transformer
import gql from "graphql-tag"
export default gql`query Hello {hello}`
```

**after**

```js
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
exports.default = { "__signature__": "04559d249676e347b115eda728635d5856f6560300659ae81adbcd2242b41d09", "kind": "Document", "definitions": [{ "kind": "OperationDefinition", "operation": "query", "name": { "kind": "Name", "value": "Hello" }, "variableDefinitions": [], "directives": [], "selectionSet": { "kind": "SelectionSet", "selections": [{ "kind": "Field", "alias": undefined, "name": { "kind": "Name", "value": "hello" }, "arguments": [], "directives": [], "selectionSet": undefined }] } }], "loc": { "start": 0, "end": 19, "source": { "body": "query Hello {hello}", "name": "GraphQL request", "locationOffset": { "line": 1, "column": 1 } } } };
```

Need more example? run `npm test` and checkout `test/fixture/actual/*.js`.

## Related

* [graphql-tag](https://github.com/apollographql/graphql-tag)

## Thanks

* [babel-plugin-graphql-tag](https://github.com/gajus/babel-plugin-graphql-tag)
* [ts-transform-img](https://github.com/longlho/ts-transform-img/)
* [typescript-plugin-styled-components](https://github.com/Igorbek/typescript-plugin-styled-components)
* [firede's original repo](https://github.com/firede/ts-transform-graphql-tag)
