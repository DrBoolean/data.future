Data.Future
===========

[![Build Status](https://secure.travis-ci.org/folktale/data.future.png?branch=master)](https://travis-ci.org/folktale/data.future)
[![NPM version](https://badge.fury.io/js/data.future.png)](http://badge.fury.io/js/data.future)
[![Dependencies Status](https://david-dm.org/folktale/data.future.png)](https://david-dm.org/folktale/data.future)
[![stable](http://hughsk.github.io/stability-badges/dist/stable.svg)](http://github.com/hughsk/stability-badges)


The `Future(a, b)` structure represents values that depend on time. This allows one
to model time-based effects explicitly, such that one can have full knowledge
of when they're dealing with delayed computations, latency, or anything that
can not be computed immediately.

A common use for this monad is to replace the usual
[Continuation-Passing Style][CPS] form of programming, in order to be able to
compose and sequence time-dependent effects using the generic and powerful
monadic operations.


## Example

```js
var Future = require('data.future')
var fs = require('fs')

// read : String -> Future(Error, Buffer)
function read(path) {
  return new Future(function(reject, resolve) {
    fs.readFile(path, function(error, data) {
      if (error)  reject(error)
      else        resolve(data)
    })
  })
}

// decode : Future(Error, Buffer) -> Future(Error, String)
function decode(buffer) {
  return buffer.map(function(a) {
    return a.toString('utf-8')
  })
}

var intro = decode(read('intro.txt'))
var outro = decode(read('outro.txt'))

// You can use `.chain` to sequence two asynchronous actions, and
// `.map` to perform a synchronous computation with the eventual
// value of the Future.
var concatenated = intro.chain(function(a) {
                     return outro.map(function(b) {
                       return a + b
                     })
                   })

// But the implementation of Future is pure, which means that you'll
// never observe the effects by using `chain` or `map` or any other
// method. The Future just records the sequence of actions that you
// wish to observe, and defers the playing of that sequence of actions
// for your application's entry-point to call.
//
// To observe the effects, you have to call the `fork` method, which
// takes a callback for the rejection, and a callback for the success.
concatenated.fork(
  function(error) { throw error }
, function(data)  { console.log(data) }
)
```


## Installing

The easiest way is to grab it from NPM. If you're running in a Browser
environment, you can use [Browserify][]

    $ npm install data.future


### Using with CommonJS

If you're not using NPM, [Download the latest release][release], and require
the `data.future.umd.js` file:

```js
var Future = require('data.future')
```


### Using with AMD

[Download the latest release][release], and require the `data.future.umd.js`
file:

```js
require(['data.future'], function(Future) {
  ( ... )
})
```


### Using without modules

[Download the latest release][release], and load the `data.future.umd.js`
file. The properties are exposed in the global `Future` object:

```html
<script src="/path/to/data.future.umd.js"></script>
```


### Compiling from source

If you want to compile this library from the source, you'll need [Git][],
[Make][], [Node.js][], and run the following commands:

    $ git clone git://github.com/folktale/data.future.git
    $ cd data.future
    $ npm install
    $ make bundle
    
This will generate the `dist/data.future.umd.js` file, which you can load in
any JavaScript environment.

    
## Documentation

You can [read the documentation online][docs] or build it yourself:

    $ git clone git://github.com/folktale/monads.maybe.git
    $ cd monads.maybe
    $ npm install
    $ make documentation

Then open the file `docs/index.html` in your browser.


## Platform support

This library assumes an ES5 environment, but can be easily supported in ES3
platforms by the use of shims. Just include [es5-shim][] :)


## Licence

Copyright (c) 2013-2014 Quildreen Motta.

Released under the [MIT licence](https://github.com/folktale/data.future/blob/master/LICENCE).

<!-- links -->
[Fantasy Land]: https://github.com/fantasyland/fantasy-land
[Browserify]: http://browserify.org/
[Git]: http://git-scm.com/
[Make]: http://www.gnu.org/software/make/
[Node.js]: http://nodejs.org/
[es5-shim]: https://github.com/kriskowal/es5-shim
[docs]: http://folktale.github.io/data.future
[CPS]: http://matt.might.net/articles/by-example-continuation-passing-style/
<!-- [release: https://github.com/folktale/data.future/releases/download/v$VERSION/data.future-$VERSION.tar.gz] -->
[release]: https://github.com/folktale/data.future/releases/download/v2.1.0/data.future-2.1.0.tar.gz
<!-- [/release] -->

