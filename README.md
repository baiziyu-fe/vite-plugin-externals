# vite-plugin-externals

<p>
  <a href="https://www.npmjs.com/package/vite-plugin-externals" target="_blank">
    <img alt="NPM package" src="https://img.shields.io/npm/v/vite-plugin-externals.svg?style=flat">
  </a>
  <a href="https://www.npmjs.com/package/vite-plugin-externals" target="_blank">
    <img alt="downloads" src="https://img.shields.io/npm/dt/vite-plugin-externals.svg?style=flat">
  </a>
  <a href="https://github.com/vitejs/awesome-vite#helpers" target="_blank">
    <img src="https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg" alt="Awesome">
  </a>
</p>

English | [简体中文](README.zh-CN.md)

use to external resources, like webpack externals, but only use in browser now.

Can be used in `production` mode without other `rollup` configuration.

but it will not take effect by default in `commonjs`, such as `ssr`.

## Usage

```bash
npm i vite-plugin-externals -D
```

Add it to `vite.config.js`

```js
// vite.config.js
import { viteExternalsPlugin } from 'vite-plugin-externals'

export default {
  plugins: [
    viteExternalsPlugin({
      vue: 'Vue',
      react: 'React',
      'react-dom': 'ReactDOM',
    }),
  ]
}
```

**Warning**: If you loaded `production` libray in `vite dev mode` , may make `HMR` **fail**.

Eg.
```html
<!-- may make HMR fail -->
<script src="./vue.global.prod.js"></script>

<!-- good -->
<script src="./vue.global.js"></script>
```

## How to work

transform source code of js file.

```js
// configuration
viteExternalsPlugin({
  vue: 'Vue',
}),
// source code
import Vue from 'vue'
// transformed
const Vue = window['Vue']

// source code
import { reactive, ref as r } from 'vue'
// transformed
const reactive = window['Vue'].reactive
const r = window['Vue'].ref

// source code
import * as vue from 'vue'
// transformed
const vue = window['Vue']
```

**Warning**: please use the plugin after converting to JS code, because the plugin only transform JS code. Eg.

```js
import vue from '@vitejs/plugin-vue'

export default {
  plugins: [
    vue(), // @vitejs/plugin-vue will transofrm SFC to JS code

    // It should be under @vitejs/plugin-vue
    viteExternalsPlugin({
      vue: 'Vue',
    }),
  ]
}
```

## Configuration

### filter

The files in `node_modules` are filtered by default, and only transform js/ts/vue/jsx/tsx file.

You can specify the `filter` function. Return `true` will be transform to external.

```js
viteExternalsPlugin({
  vue: 'Vue',
}, {
  filter(code, id, ssr) {
    // your code
    return false
  }
}),
```

### useWindow

set `false`, the `window` prefix will not be added.

**Warning**: If your module name has special characters, such as `/`, set useWindow option `false`, will throw error.

```js
viteExternalsPlugin({
  vue: 'Vue',
}, { useWindow: false }),

// source code
import Vue from 'vue'
// transformed, no `const Vue = window['Vue']`
const Vue = Vue
```
