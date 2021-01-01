---
layout: post
title:  "Writing Command-Line Applications with AssemblyScript"
date:   2020-12-31 22:00
categories: "programming"
---

I'm probably one of the few people who likes Java.  I'm productive in Java, despite its verbosity, because it's a simple language and offers memory safety.  These attributes are shared by Python, my other favorite language.  Although I find the Python ecosystem (e.g., numerical, machine learning, and data science libraries) better suited for my needs, I find Java more convenient building apps I intend to distribute.  Java builds portable applications (runnable by nearly any JVM) and can bundle all of the dependencies into a single jar file.

I've been looking for my next language.  Rust is nice, but I find that the complexity of the language gets in my way.  I end up frustrated rather than productive.  I like, however, that Rust compiles to [WebAssembly](https://webassembly.org/).  WebAssembly is a portable, memory-safe, and sandboxed compilation target that can be run by Javascript virtual machines.  It's analogous to bytecode and the Java virtual machine.

I recently came across [AssemblyScript](https://www.assemblyscript.org/), a modification of [TypeScript](https://www.typescriptlang.org/), that targets WebAssembly.  It seems like a lightweight, typed, and compiled language that I might like.

## Writing Command-Line Applications with AssemblyScript
Unfortunately, I struggled to find documentation for how to compile AssemblyScript into command-line applications runnable with wasmtime (rather than libraries).  Eventually, I found a few pieces I was able to link together.  I've compiled these into a single "Getting Started Guide":

First, install these two:

1. [Node Version Manager](https://github.com/nvm-sh/nvm)
2. [Wasmtime](https://wasmtime.dev/)

Then install NodeJS and NPM:

```bash
$ nvm install node
```

Follow the [quick-start guide](https://www.assemblyscript.org/quick-start.html) for AssemblyScript to create a project:

```bash
$ mkdir my-project
$ cd my-project
$ npm init
$ npm install --save @assemblyscript/loader
$ npm install --save-dev assemblyscript
$ npx asinit .
```

Note that `npm init` will prompt you for various details to populate the `package.json` file.

The next set of steps come from the [Wasmtime documentation for AssemblyScript](https://docs.wasmtime.dev/wasm-assemblyscript.html). In your project, install the [as-wasi](https://github.com/jedisct1/as-wasi) dependency:

```bash
$ npm install --save as-wasi
```

Change the code in `assembly/index.ts` to:

```typescript
import "wasi"

import {Console} from "as-wasi"
Console.log('Hello World!\n');
```

Lastly, compile and run your code:

```bash
$ npm run asbuild
$ wasmtime build/optimized.wasm
```

You should now see:

```
Hello World!
```

Congratulations, you can now write command-line Webassembly applications in AssemblyScript.
