# WebAssembly System Interface implementation for Deno, Node.js, Bun

This package provides an implementation of the [WebAssembly System
Interface](https://github.com/webassembly/wasi) for the
[Deno](https://github.com/denoland/deno) TypeScript runtime, [Node.js](https://nodejs.org), [Bun](https://bun.sh/) using Node.js API's (`node:fs`, `node:process`).

# Status

This is a fork and modified work of https://github.com/caspervonb/deno-wasi. Compiled to JavaScript from TypeScript with `deno bundle` (`deno` version 1.46.2). 

Tested with `wasi.js` and WASM compiled from JavaScript using Facebook's [Static Hermes](https://github.com/facebook/hermes/tree/static_h) and [WASI SDK](https://github.com/WebAssembly/wasi-sdk) (see [WASI.md](https://github.com/guest271314/hermes/blob/shermes-wasm/doc/WASI.md)) for compilation details) using `node` version v24.0.0-nightly20250111ad68d088a3, `deno` version 2.1.5+70c822b, `bun` version 1.1.44.

`.github`, `testdata` directories, `mod.ts` (source that was modified to create `wasi.js`) and `test.ts` retained in this branch for historical purposes.

## Example

```javascript
import WASI from "./wasi.js"; 
import { readFile }  from "node:fs/promises";
const bin = await readFile("./out/fopen.wasm");
const mod = await WebAssembly.compile(bin);
const wasi = new WASI();
const instance = await WebAssembly.instantiate(mod, {
  wasi_snapshot_preview1: wasi.exports,
});
wasi.memory = instance.exports.memory;
instance.exports._start();
```

```javascript
echo '4 5' | node --no-warnings run-wasi.js
5 of 23 (0-indexed, factorial 24) => [0,3,2,1]
```
```javascript
echo '5 6' | deno -A run-wasi.js
6 of 119 (0-indexed, factorial 120) => [0,2,1,3,4]
```
```javascript
echo '7 8' | bun run-wasi.js
8 of 5039 (0-indexed, factorial 5040) => [0,1,2,4,5,3,6]
```
