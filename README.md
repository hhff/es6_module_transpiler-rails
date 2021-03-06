# ES6ModuleTranspiler-Rails #

[![Build Status](https://secure.travis-ci.org/dockyard/es6_module_transpiler-rails.png?branch=master)](http://travis-ci.org/dockyard/es6_module_transpiler-rails)
[![Dependency Status](https://gemnasium.com/dockyard/es6_module_transpiler-rails.png?travis)](https://gemnasium.com/dockyard/es6_module_transpiler-rails)
[![Code Climate](https://codeclimate.com/github/dockyard/es6_module_transpiler-rails.png)](https://codeclimate.com/github/dockyard/es6_module_transpiler-rails)

Transpile ES6 Modules in the Rails Asset Pipeline

Uses [Square's ES6 Module Transpiler](https://github.com/square/es6-module-transpiler)

## Installation ##

**Node.js must be installed for the transpiling to happen**

```ruby
gem 'es6_module_transpiler-rails'
```

## Usage ##

Your modules will transpile and are named based upon their directory
nesting + filename, as long as the file has the `.es6` extension.
For example, `app/assets/javascripts/controllers/fooController.js.es6`

```js
var fooController = function() {
  console.log('fooController is in the house!');
};

export default fooController;
```

will compile to `/assets/controllers/fooController.js`

```js
define("controllers/fooController",
  ["exports"],
  function(__exports__) {
    "use strict";
    var fooController = function() {
      console.log('fooController is in the house!');
    };

    __exports__["default"] = fooController;
  });
```

### Compiling ###

By default your module will compile to an AMD. You can also compile it to globals or CommonJS by making the following switch:

```ruby
ES6ModuleTranspiler.compile_to = :globals
# or
ES6ModuleTranspiler.compile_to = :cjs
```

You may modify the `options` that are passed to the module compiler (i.e.
[compatFix](https://github.com/square/es6-module-transpiler/blob/3e708b70dffeaf753307f9d5ecdf780fd6c7b74e/lib/amd_compiler.js#L68)) by 
modifying the `compiler_options` hash:

```ruby
ES6ModuleTranspiler.compiler_options[:compatFix] = true;
```

The `compiler_options` hash is empty by default.

### Loading Modules ###

When compiling to AMD or CommonJS, you'll need to be able to load the compiled result.  In the below example, we're using a minimal AMD loader called [loader.js](https://github.com/stefanpenner/loader.js).  Without a loader, you'll get an error like:

```
Uncaught ReferenceError: define is not defined
```

For example, in **application.js**:
```
//= require loader
//= require_tree ./modules
//= require main

require('main');
```
Now `main.js.es6` is our entry point - from this file we can use the es6 module syntax to load any es6 modules in the `./modules` directory.

### Custom Module Prefix ###

You can match module names based upon a pattern to apply a prefix to the
name. You can add multiple patterns (which can each have separate prefixes):

```ruby
ES6ModuleTranspiler.add_prefix_pattern Regexp.new(File.join(Rails.root, 'app')), 'app'
ES6ModuleTranspiler.add_prefix_pattern Regexp.new(File.join(Rails.root, 'config')), 'config'
```

This would match names that start with the pattern and prepend with
`app/` or `config/`. For example, `/home/user/app/controllers/fooController` would now be named
`app/controllers/fooController`, and `/home/user/config/router` would now be
named `config/router`.

Note the path is made up of the *root path* and the *logical path* for the asset. For example, if the
path to your asset is
`/home/user/app/assets/javascripts/controllers/fooController.js.es6` the root path is `/home/user/app/assets/javascripts/` and the logical
path is `controllers/fooController`. It is entirely dependent upon what
Sprockets considers to be the mount point for the asset.

## Authors ##

[Brian Cardarella](http://twitter.com/bcardarella)

[We are very thankful for the many contributors](https://github.com/dockyard/es6_module_transpiler-rails/graphs/contributors)

## Versioning ##

This gem follows [Semantic Versioning](http://semver.org)

## Want to help? ##

Please do! We are always looking to improve this gem.

## Legal ##

[DockYard](http://dockyard.com), LLC &copy; 2013

[@dockyard](http://twitter.com/dockyard)

[Licensed under the MIT license](http://www.opensource.org/licenses/mit-license.php)
