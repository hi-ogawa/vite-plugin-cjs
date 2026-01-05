# @hiogawa/vite-plugin-cjs

Vite plugin to transform CommonJS modules to ESM for Vite's module runner environment.

Used by [@vitejs/plugin-rsc](https://github.com/vitejs/vite-plugin-react/tree/main/packages/plugin-rsc).

## Installation

```sh
npm install @hiogawa/vite-plugin-cjs
```

## Usage

```ts
import { cjsModuleRunnerPlugin } from "@hiogawa/vite-plugin-cjs";
import { defineConfig } from "vite";

export default defineConfig({
  plugins: [cjsModuleRunnerPlugin()],
});
```

## What it does

This plugin transforms `require()` calls in CommonJS modules to ESM dynamic imports when running in Vite's module runner environment (SSR dev mode with `moduleRunnerTransform` enabled).

**Before:**
```js
const dep = require("./dep");
module.exports = { value: dep.value };
```

**After:**
```js
let __filename = "/path/to/file.js";
let __dirname = "/path/to";
let exports = {};
const module = { exports };
function __cjs_interop__(m) {
  return m.__cjs_module_runner_transform ? m.default : m;
}
const dep = __cjs_interop__(await import("./dep"));
module.exports = { value: dep.value };

__vite_ssr_exportAll__(module.exports);
export default module.exports;
export const __cjs_module_runner_transform = true;
```

## Features

- Transforms `require()` to `await import()` with CJS/ESM interop
- Hoists non-top-level `require()` calls to module scope
- Provides CJS globals: `__filename`, `__dirname`, `exports`, `module`
- Skips genuine ESM modules (`.mjs` or `"type": "module"`)
- Skips modules with existing ES module syntax

## License

MIT
