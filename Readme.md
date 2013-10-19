# sandboxed-module

[![Build Status](https://secure.travis-ci.org/felixge/node-sandboxed-module.png)](http://travis-ci.org/felixge/node-sandboxed-module)

A sandboxed node.js module loader that lets you inject dependencies into your
modules.

## Installation

``` bash
npm install sandboxed-module
```

## Usage

``` javascript
var SandboxedModule = require('sandboxed-module');
var user = SandboxedModule.require('./user', {
  requires: {'mysql': {fake: 'mysql module'}},
  globals: {myGlobal: 'variable'},
  locals: {myLocal: 'other variable'},
});
```

## What to do with this

This module is intended to ease dependency injection for unit testing. However,
feel free to use it for whatever crimes you can think of.

## API

### SandboxedModule.load(moduleId, [options])

Returns a new `SandboxedModule` where `moduleId` is a regular module path / id
as you would normally pass into `require()`. The new module will be loaded in
its own v8 context, but otherwise have access to the normal node.js
environment.

`options` is an optional object that can be used to inject any of the
following:

* `requires:` An object containing `moduleId`s and the values to inject for
  them when required by the sandboxed module. This does not affect children
  of the sandboxed module.
* `globals:` An object of global variables to inject into the sandboxed module.
* `locals:` An object of local variables to inject into the sandboxed module.
* `sourceTransformers:` An object of named functions to transform the source code of
the sandboxed module's file (e.g. transpiler language, code coverage).

### SandboxedModule.require(moduleId, [options])

Identical to `SandboxedModule.load()`, but returns `sandboxedModule.exports`
directly.

### sandboxedModule.filename

The full path to the module.

### sandboxedModule.module

The underlaying node.js `Module` instance.

### sandboxedModule.exports

A getter returning the `sandboxedModule.module.exports` object.

### sandboxedModule.globals

The global object of the v8 context this module was loaded in. Modifications
to this object will be reflected in the sandboxed module.

### sandboxedModule.locals

The local variables injected into the sandboxed module using a closure.
Modifying this object has no effect on the state of the sandbox.

### sandboxedModule.required

An object holding a list of all module required by the sandboxed module itself.
The keys are the `moduleId`s used for the require calls.

### sandboxedModule.sourceTransformers

An object of named functions which will transform the source code required with
`SandboxedModule.require`. For example, CoffeeScript &
[istanbul](https://github.com/gotwarlost/istanbul) support is implemented as
default sourceTransformer functions.

A source transformer receives the source (as it's been transformed thus far) and
**must** return the transformed source (whether it's changed or unchanged).

An example source transformer to change all instances of the number "3" to "5"
would look like this:

``` javascript
SandboxedModule.require('../fixture/baz', {
  sourceTransformers: {
    turn3sInto5s: function(source) {
      return source.replace(/3/g,'5');
    }
  }
})
```

## License

sandboxed-module is licensed under the MIT license.
