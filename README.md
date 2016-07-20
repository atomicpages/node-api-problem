# api-problem [![version][npm-version]][npm-url] [![License][npm-license]][license-url]

> [HTTP Problem](https://tools.ietf.org/html/draft-ietf-appsawg-http-problem) Utility

[![Build Status][travis-image]][travis-url]
[![Downloads][npm-downloads]][npm-url]
[![Code Climate][codeclimate-quality]][codeclimate-url]
[![Coverage Status][codeclimate-coverage]][codeclimate-url]
[![Dependencies][david-image]][david-url]

## Install

```sh
npm install --production --save api-problem
```

## Usage

I reccomend using an optimized build matching your Node.js environment version, otherwise, the standard `require` would work just fine.

```js
/*
 * Node 6
 * Built using `babel-preset-es2015-node6`
 */
const Problem = require('api-problem/lib/node6')

/*
 * Node 5
 * Built using `babel-preset-es2015-node5`
 */
const Problem = require('api-problem/lib/node5')

/*
 * Node 4
 * Built using `babel-preset-es2015-node4`
 */
const Problem = require('api-problem/lib/node4')

/*
 * Node >=0.10 <=0.12
 * Built using `babel-preset-es2015`
 */
var Problem = require('api-problem')
```

## API

### Constructor: `Problem(status[, title][, type][, members])`

| name          | type     | required | default             | description                                                                            | referece                |
| ------------- | -------- | -------- | ------------------- | -------------------------------------------------------------------------------------- | ----------------------- |
| **`status`**  | `String` | `✔`     | `N/A`               | The HTTP status code generated by the origin server for this occurrence of the problem | [Section 3.1][spec-3.1] |
| **`title`**   | `String` | `✖`     | HTTP status phrase  | A short, human-readable summary of the problem type                                    | [Section 3.1][spec-3.1] |
| **`type`**    | `String` | `✖`     | `about:blank`       | A URI reference that identifies the problem type                                       | [Section 3.1][spec-3.1] |
| **`details`** | `Object` | `✖`     | `N/A`               | additional details to attach to object                                                 | [Section 3.1][spec-3.2] |

```js
import Problem from 'api-problem'

// HTTP defaults
new Problem(404) 
//=> { status: '404', title: 'Not Found', type: 'http://www.iana.org/assignments/http-status-codes#404' }


// override defaults
new Problem(404, 'Oops! Page Not Found') 
//=> { status: '404', title: 'Oops! Page Not Found', type: 'http://www.iana.org/assignments/http-status-codes#404' }


// custom values
new Problem(403, 'You do not have enough credit', 'https://example.com/probs/out-of-credit') 
//=> { status: '403', title: 'You do not have enough credit', type: 'https://example.com/probs/out-of-credit' }


// additional details
new Problem(403, 'You do not have enough credit', 'https://example.com/probs/out-of-credit', {
  detail: 'Your current balance is 30, but that costs 50.',
  instance: '/account/12345/msgs/abc',
  balance: 30,
  accounts: ['/account/12345', '/account/67890']
})

//=> { status: '403', title: 'You do not have enough credit', type: 'https://example.com/probs/out-of-credit', detail: 'Your current balance is 30, but that costs 50.', instance: '/account/12345/msgs/abc', balance: 30, accounts: ['/account/12345', '/account/67890'] }


// HTTP defaults + Details
new Problem(403, {
  detail: 'Account suspended',
  instance: '/account/12345',
  date: '2016-01-15T06:47:01.175Z',
  account_id: '12345'
}) 
//=> { status: '403', title: 'Forbidden', type: 'http://www.iana.org/assignments/http-status-codes#404', detail: 'Account suspended', instance: '/account/12345', account_id: 12345, 'date: 2016-01-15T06:47:01.175Z' }

```

### Method : <string> `toString()`

returns a simplified, human-readable string representation

```js
let prob = new Problem(403, 'You do not have enough credit', 'https://example.com/probs/out-of-credit') 

prob.toString() //=> [403] You do not have enough credit ('https://example.com/probs/out-of-credit')
```

### Method : <void> `send(response)`

uses [`response.writeHead`](https://nodejs.org/docs/latest/api/http.html#http_response_writehead_statuscode_statusmessage_headers) and [`response.end`](https://nodejs.org/docs/latest/api/http.html#http_response_end_data_encoding_callback) to send an appropriate error respnse message with the `Content-Type` response header to [`application/problem+json`][spec-3]

```js
import http from 'http'
import Problem from 'api-problem'

let response = new http.ServerResponse()
Problem.send(response)
```

### Express Middleware

A standard connect middleware is provided. The middleware intercepts Problem objects thrown as exceptions and serializes them appropriately in the HTTP response while setting the `Content-Type` response header to [`application/problem+json`][spec-3]

```js
import express from 'express'
import Problem from 'api-problem'
import Middleware from 'api-problem/lib/middleware'

let app = express()

app.get('/', (req, res) => {
  throw new Problem(403)
})

app.use(Problem.Middleware)
```

----
> :copyright: [www.ahmadnassri.com](https://www.ahmadnassri.com/) &nbsp;&middot;&nbsp;
> License: [ISC](LICENSE) &nbsp;&middot;&nbsp;
> Github: [@ahmadnassri](https://github.com/ahmadnassri) &nbsp;&middot;&nbsp;
> Twitter: [@ahmadnassri](https://twitter.com/ahmadnassri)

[license-url]: http://choosealicense.com/licenses/isc/

[travis-url]: https://travis-ci.org/ahmadnassri/api-problem
[travis-image]: https://img.shields.io/travis/ahmadnassri/api-problem.svg?style=flat-square

[npm-url]: https://www.npmjs.com/package/api-problem
[npm-license]: https://img.shields.io/npm/l/api-problem.svg?style=flat-square
[npm-version]: https://img.shields.io/npm/v/api-problem.svg?style=flat-square
[npm-downloads]: https://img.shields.io/npm/dm/api-problem.svg?style=flat-square

[codeclimate-url]: https://codeclimate.com/github/ahmadnassri/api-problem
[codeclimate-quality]: https://img.shields.io/codeclimate/github/ahmadnassri/api-problem.svg?style=flat-square
[codeclimate-coverage]: https://img.shields.io/codeclimate/coverage/github/ahmadnassri/api-problem.svg?style=flat-square

[david-url]: https://david-dm.org/ahmadnassri/api-problem
[david-image]: https://img.shields.io/david/ahmadnassri/api-problem.svg?style=flat-square

[spec-3]: https://tools.ietf.org/html/draft-ietf-appsawg-http-problem-02#section-3
[spec-3.1]: https://tools.ietf.org/html/draft-ietf-appsawg-http-problem-02#section-3.1
[spec-3.2]: https://tools.ietf.org/html/draft-ietf-appsawg-http-problem-02#section-3.2
