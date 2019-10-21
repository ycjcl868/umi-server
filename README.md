# umi-server

[![Coverage Status](https://coveralls.io/repos/github/umijs/umi-server/badge.svg?branch=master)](https://coveralls.io/github/umijs/umi-server?branch=master) [![NPM version](https://img.shields.io/npm/v/umi-server.svg?style=flat)](https://npmjs.org/package/umi-server) [![NPM downloads](http://img.shields.io/npm/dm/umi-server.svg?style=flat)](https://npmjs.org/package/umi-server) [![CircleCI](https://circleci.com/gh/umijs/umi-server/tree/master.svg?style=svg)](https://circleci.com/gh/umijs/umi-server/tree/master) [![GitHub Actions status](https://github.com/umijs/umi-server/workflows/Node%20CI/badge.svg)](https://github.com/umijs/umi-server)

🚀 A runtime render tool for Umijs Server-Side Rendering.

## Quick Start

(config + ctx) => htmlString

```sh
npm install umi-server -S
```

```js
// if using ES6 / TypeScript
// import server from 'umi-server';
const server = require('umi-server');
const http = require('http');
const { readFileSync } = require('fs');
const { join, extname } = require('path');

const render = server({
  filename: join(__dirname, 'dist', 'umi.server.js'),
  manifest: join(__dirname, 'dist', 'ssr-client-mainifest.json'),
  // you can use root rather than filename and manifest
  // if both in the same directory
  // root: join(__dirname, 'dist');
  publicPath: '/',
})
const headerMap = {
  '.js': 'text/javascript',
  '.css': 'text/css',
}

// your server
http.createServer(async (req, res) => {
  const ext = extname(req.url);
  const header = {
    'Content-Type': headerMap[ext] || 'text/html'
  }
  res.writeHead(200, header);

  if (req.url === '/') {
    const ctx = {
      req,
      res,
    }
    const { ssrHtml } = await render(ctx);
    res.write(ssrHtml);
    res.end()
  } else {
    const content = await readFileSync(join(root, req.url) , 'utf-8');
    res.end(content, 'utf-8');
  }

}).listen(8000)

console.log('http://localhost:8000')
```

Visit [http://localhost:8000](http://localhost:8000).

## Usage

The type definition:

```js
interface IConfig {
  /** prefix path for `filename` and `manifest`, if both in the same directory */
  root: string;
  /** ssr manifest, default: `${root}/ssr-client-mainifest.json` */
  manifest?: string;
  /** umi ssr server file, default: `${root}/umi.server.js` */
  filename?: string;
  /** default false */
  polyfill?: boolean | IPolyfill;
  /** use renderToStaticMarkup  */
  staticMarkup?: boolean;
  /** handler function for user to modify render html */
  postProcessHtml?: IHandler | IHandler[];
  /** TODO: serverless */
  serverless?: boolean;
}

type IHandler = (html: string, args: IArgs) => string;
```

more example usages in [test cases](https://github.com/umijs/umi-server/blob/master/test/index.test.ts).
