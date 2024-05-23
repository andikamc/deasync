# DeAsync

DeAsync turns async code into sync, implemented with a blocking mechanism by calling Node.js event loop at JavaScript layer. The core of deasync is written in C++.

This project is forked from [abbr/deasync](https://github.com/abbr/deasync) and rewritten in modern code, adding some new features: types, Promise support, and prebuild binaries.

> [!WARNING]
> 
> Due to [`uv_run()` is not reentrant](https://docs.libuv.org/en/v1.x/loop.html#c.uv_run), `awaitSync` and deasynced functions only work on top level, calling them from a callback will cause a deadlock.

## Installation

```shell
git clone https://github.com/andikamc/deasync.git
npm install
npm run compile:ts
npm run install
```

DeAsync downloads prebuild binary from GitHub releases during installation, if download fails, try to build locally. You can skip the install phase by setting the environment variable `NO_PREBUILD=1`.

DeAsync uses node-gyp to compile C++ source code, so to build Deasync you may need the compilers listed in [node-gyp](https://github.com/nodejs/node-gyp).

## Usage

DeAsync exports only two APIs: `deasync` for callback-style functions, and `awaitSync` for Promises.

### `deasync(function)`

Generic wrapper of async function with conventional API signature `function(...args, (error, result) => {})`. Returns `result` and throws `error` as exception if not null.

Sleep (a wrapper of setTimeout):

```javascript
const { deasync } = require("andikamc/deasync");

const sleep = deasync((timeout, callback) => {
	setTimeout(() => callback(null, "wake up!"), timeout);
});

console.log("Timestamp before: " + performance.now());
console.log(sleep(1000));
console.log("Timestamp after: " + performance.now());
```

### `awaitSync(promise)`

Similar with the keyword `await` but synchronously.

```javascript
const { awaitSync } = require("andikamc/deasync");

const promise = new Promise(resolve => setTimeout(resolve, 1000)).then(() => "wake up!")

console.log("Timestamp before: " + performance.now());
console.log(awaitSync(promise));
console.log("Timestamp after: " + performance.now());
```

## Recommendation

Unlike other (a)sync js packages that mostly have only syntactic impact, DeAsync also changes code execution sequence. As such, it is intended to solve niche cases like the above one. If all you are facing is syntactic problem such as callback hell, using a less drastic package implemented in pure js is recommended.
