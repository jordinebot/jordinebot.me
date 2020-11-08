---
title: 'How to create a React + Typescript Web Project with Rollup from scratch'
date: 2020-11-08T18:59:00+01:00
tags: [react, typescript, modules, rollup]
---

This is how I started a React application with Typescript and Rollup from scratch. I try to do this kind of things from
time to time because in my daily job I don't usually start new projects, and if I do, it's through some toolchain that
does all the tooling for me. So often the outcome is that I don't really know how to start a frontend project from the
ground up.

## Init project and install Rollup

Create a folder and a default `package.json` with `npm init`. Install [Rollup](https://rollupjs.org/guide/en/),
create a basic config file, and some _Hello World_ typescript code.

```zsh
npm install rollup --save-dev
```

```javascript
// rollup.config.js
export default {
	input: 'src/main.ts',
	output: {
		file: 'public/index.js',
		format: 'es'
	}
};
```

```typescript
// src/main.ts
const message: string = 'Hello World';
document.querySelectorAll('body')[0].innerText = message;
```

This config basically tells Rollup to bundle everything in `src/main.ts` (imports included) into a single file in
`public/index.js` as a [ES6 Module](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules). If you're
not familiar with JavaScript modules, I recommend [A 10 minute primer to JavaScript modules, module formats, module
loaders and module
bundlers](https://www.jvandemo.com/a-10-minute-primer-to-javascript-modules-module-formats-module-loaders-and-module-bundlers/)
by Jurgen Van de Moere.

At this point, we can already run Rollup doing

```zsh
./node_modules/rollup/dist/bin/rollup --config
```

Local dependecies cannot be directly executed in CLI unless we explicitly run them from their relative path to
`node_modules` folder, or much easier, setting up a script in our `package.json`

```json
{
  "scripts": {
	...
	"build": "rollup --config"
  },
}
```

```zsh
npm run build
```

When we do, we'll get an error:

```error
src/main.ts → public/index.js...
[!] Error: Unexpected token (Note that you need plugins to import files that are not JavaScript)
```

## Add TypeScript support

That's because Rollup cannot transpile TypeScript. So, let's install a plugin to enable that feature. Even though I've
seen some criticism about its speed, I've found the [_not official_](https://github.com/rollup/plugins/issues/541)
[ezolenko/rollup-plugin-typescript2](https://github.com/ezolenko/rollup-plugin-typescript2) to be easier to setup.

As easy as:

```javascript
// rollup.config.js
import typescript from 'rollup-plugin-typescript2';

export default {
	input: 'src/main.ts',
	output: {
		file: 'public/index.js',
		format: 'es'
	},
	plugins: [typescript()]
};
```

Building the project now should succed and produce a readable bundle on `public/index.js`

```javascript
'use strict';

var message = 'Hello World';
document.querySelectorAll('body')[0].innerText = message;
```

## Add React

Install React and create a HelloWorld React component to be used in our main file.

```zsh
npm install react react-dom --save
```

```typescript
// src/components/hello-world.tsx
import React from 'react';

interface HelloWorldProps {
	name: string;
}

export const HelloWorld: React.FC<HelloWorldProps> = ({ name }) => (
	<h1>Hello {name ? name : 'World'}</h1>
);
```

```typescript
// src/main.tsx
import React from 'react';
import ReactDOM from 'react-dom';
import { HelloWorld } from './components/hello-world';

document.body.appendChild(
	Object.assign(document.createElement(`div`), { id: 'root' })
);
ReactDOM.render(<HelloWorld name="Jordi" />, document.getElementById('root'));
```

Remember to rename `main.ts` extension to `.tsx` since we're using [JSX](https://reactjs.org/docs/introducing-jsx.html)
syntax in a TypeScript file, as well as its reference in `rollup.config.js`.

Running the build command `npm run build` will now produce a different error message:

```error
src/main.tsx → public/index.js...
[!] (plugin rpt2) Error: /src/main.tsx(2,28): semantic error TS6142: Module './components/hello-world' was resolved to '/src/components/hello-world.tsx', but '--jsx' is not set.
```

Fix this by adding a simple `tsconfig.json` file in project's root, so that we can pass some configuration to the
TypeScript compiler, such as enabling support for JSX

```json
{
	"include": ["src/**/*"],
	"exclude": ["node_modules"],
	"compilerOptions": {
		"jsx": "react"
	}
}
```

Build for a new error message:

```error
src/main.tsx → public/index.js...
[!] (plugin rpt2) Error: /src/main.tsx(4,18): semantic error TS2304: Cannot find name 'React'.
```

What's going on here? Rollup doesn't include a built-in module resolution method and it [delegates this
responsability](https://rollupjs.org/guide/en/#why-isnt-node-resolve-a-built-in-feature) to a plugin. That means it's
not able to find React in our `node_modules` folder. Let's _resolve_ this

```zsh 
npm install @rollup/plugin-node-resolve --save-dev
```

```javascript
// rollup.config.js
import typescript from 'rollup-plugin-typescript2';
import resolve from '@rollup/plugin-node-resolve';

export default {
	input: 'src/main.tsx',
	output: {
		file: 'public/index.js',
		format: 'es'
	},
	plugins: [
		resolve(),
		typescript(),
	]
};
```

Does this work? Not yet.

```error
src/main.tsx → public/index.js...
(!) Error when using sourcemap for reporting an error: Can't resolve original location of error.
src/components/hello-world.tsx: (1:7)
[!] Error: 'default' is not exported by node_modules/react/index.js, imported by src/components/hello-world.tsx
https://rollupjs.org/guide/en/#error-name-is-not-exported-by-module
```

From Rollup's [FAQ](https://rollupjs.org/guide/en/#rollupplugin-commonjs): 

> Some libraries expose ES modules that you can import as-is [...] But at the moment, the majority of packages on NPM
> are exposed as CommonJS modules instead. Until that changes, we need to convert CommonJS to ES2015 before Rollup can
> process them.
>
> The @rollup/plugin-commonjs plugin does exactly that.

```zsh
npm install @rollup/plugin-commonjs --save-dev
```


```javascript
// rollup.config.js
import typescript from 'rollup-plugin-typescript2';
import resolve from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';

export default {
	input: 'src/main.tsx',
	output: {
		file: 'public/index.js',
		format: 'es'
	},
	plugins: [
		resolve(),
		commonjs(),
		typescript(),
	]
};
```

```zsh
npm run build
```

```success
src/main.tsx → public/index.js...
created public/index.js in 2.4s
```

Hooray! It works but, are we done? Well, not yet. Since the goal is to build a web application, we might want to have a
local server for developing. Let's do this. We need basically two things: an HTML file that loads the React app and a
server that makes it accessible in our browser.

## Setting up the local development environment

As you might guess, there's also a Rollup plugin to generate automatically an HTML file to serve our generated bundle.
It's called `@rollup/plugin-html`. And its default configuration is, as usual, install, import, include in the plugins
array. As a server, we're going to use `rollup-plugin-serve`:

```zsh
npm install @rollup/plugin-html rollup-plugin-serve --save-dev
```

```javascript
// rollup.config.js
import typescript from 'rollup-plugin-typescript2';
import resolve from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import html from '@rollup/plugin-html';
import serve from 'rollup-plugin-serve';

export default {
	input: 'src/main.tsx',
	output: {
		file: 'public/index.js',
		format: 'es'
	},
	plugins: [
		resolve(),
		commonjs(),
		typescript(),
		html(),
		serve('public'),
	]
};
```

Build should succed...

```success
src/main.tsx → public/index.js...
http://localhost:10001 -> /public
created public/index.js in 2.5s
```

But we'll find an error in our browser's devtools console when navigating to `http://localhost:10001`

```error
Uncaught ReferenceError: process is not defined
    react_development http://localhost:10001/index.js:124
    createCommonjsModule http://localhost:10001/index.js:8
    <anonymous> http://localhost:10001/index.js:122
```

This happens because React checks `process.env.NODE_ENV` for a `production` or `development` value, and Rollup isn't
providing any. Apparently, providing this information is the most common usage for `@rollup/plugin-replace`

```zsh
npm install @rollup/plugin-replace --save-dev
```

```javascript
// rollup.config.js
import typescript from 'rollup-plugin-typescript2';
import resolve from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import html from '@rollup/plugin-html';
import serve from 'rollup-plugin-serve';
import replace from '@rollup/plugin-replace';

export default {
	input: 'src/main.tsx',
	output: {
		file: 'public/index.js',
		format: 'es'
	},
	plugins: [
		replace({
			'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV)
		}),
		resolve(),
		commonjs(),
		typescript(),
		html(),
		serve('public')
	]
};
```

This way we can _pass_ the `NODE_ENV` value from `rollup.config.js` to the bundle. To set `NODE_ENV` we can use the
argument `--environment` in Rollup's execution. We're at a good point to have separate scripts for development and
production environments

```json
// package.json
{
	...
	"scripts": {
		"test": "echo \"Error: no test specified\" && exit 1",
		"build": "rollup --config --environment NODE_ENV:production",
		"start": "rollup --config --environment NODE_ENV:development"
	},
	...
}
```

To avoid serving our bundle locally on `production` builds we can modify `rollup.config.js`'s `serve()` to be run only
in development mode:

```javascript
// rollup.config.js
...
export default {
	...	
	plugins: [
		replace({
			'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV)
		}),
		resolve(),
		commonjs(),
		typescript(),
		html(),
		process.env.NODE_ENV === 'development' && serve('public')
	]
};
```

And that's it! A basic TS + React Web application setup with Rollup, step by step. We should probably add support for CSS, as well
as some automated testing, linting, live reload in the development server... But I'll leave this for a future post.

***

### Recommended links:

- [Rollup Awesome](https://github.com/rollup/awesome): A curated collection of Rollup Plugins, Packages, and Resources.
