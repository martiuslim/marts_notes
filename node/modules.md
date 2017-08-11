# Modules
At its simplest, `modules` are simply snippets of reusable code. [Here is a great article](https://medium.freecodecamp.com/javascript-modules-a-beginner-s-guide-783f7d7a5fcc) that explains `why` and `how` we use modules.

Creating a `module` is easy. Here is an example of a module named `bacon.js`.

```
var hello = function() {
  console.log("I like bacon!");
}

module.exports = bacon;
```

The `module.exports` defines what the `require` keyword returns. We can now use this module in our `app.js`.

```
var bacon = require('./bacon.js');

bacon();
```

`Modules` can be defined in different ways such as in `breakfast.js` here.

```
exports.eggs = function() {
  console.log("eggs are great too!");
}
```

Now the way we use the module also needs to be changed.

```
var bf = require('./breakfast.js');
bf.eggs();
```

We can also `call` the function in one line `require('./breakfast.js').eggs();`

Writing the `module` this way allows us to expose more than one public function. Exporting multiple functions works the same way as seen here in `modules.js`.

```
var foo = function() { ... }
var bar = function() { ... }
var zed = function() { ... }

exports.foo = foo
exports.bar = bar
```

We can then use the functions in the `module` the same way as above. However, `zed` here is a private function only accessible from within `modules.js`.

```
var mods = require('./modules.js');

mods.foo();
mods.bar();
```

## Creating & Using Modules
Here is an example of a code snippet that makes a `http request`.

```
var http = require('http');

var message = "Breakfast?";
var options = {
  host: 'localhost', port: 8080, path: '/', method: 'POST'
}

var request = http.request(options, (response) => {
  response.on('data', (data) => {
    console.log(data);
  });
});

request.write(message);
request.end();
```

To rewrite this code for it to be more reusable, we can `encapsulate` the code inside a function.

```
var http = require('http');

var makeRequest = function(message) {
  var options = {
    host: 'localhost', port: 8080, path: '/', method: 'POST'
  }

  var request = http.request(options, (response) => {
    response.on('data', (data) => {
      console.log(data);
    });
  });

  request.write(message);
  request.end();
}
```

We can now create a module called `make_request.js` using this code by adding `module.exports`.

```
var http = require('http');
var makeRequest = function(message) { ... }

module.exports = makeRequest
```

We can then require the module and use it, just like that!

```
var makeRequest = require('./make_request.js');

makeRequest("Breakfast?");
```

## How `require` searches for modules

> Looks in the same directory   

`var alice = require('./alice');`

> Looks in the parent directory

`var bob = require('../bob');`

> Looks at the absolute path provided  

`var charlie = require('/Users/nodes/charlie');`

You can also `require` modules without specifying any path.

`var dianne = require('dianne');`

> This will search node_module directories in the following order
1. In current app

   /Home/mart/my_app/node_modules/dianne.js

2. From Users directory

   /Home/mart/node_modules/dianne.js
   
3. From Home directory

   /Home/node_modules/dianne.js

4. Root   

   /node_modules/dianne.js

## npm (Node Package Manager)
`npm` is the package manager for node and it comes bundled with node. It is a `module repository`, offers `dependency management` as well as allows you to `easily publish modules`. Learn more about `npm` [here](https://www.npmjs.com/).

### Installing a npm Module
Let's say that our current directory is at `/Home/my_app`. Installing a `npm module`, for example the `request` module, is as easy as running `npm install request`. This will then install `request` into the local node_modules directory found at `/Home/my_app/node_modules/request`.  

We can then write `var request = require('request');` in our `app.js` and it will load the `request` module from the local `node_modules` directory.

### Local vs Global Modules
Usually, modules with `executables` are installed `globally` with the `-g` switch.

`npm install coffee-script -g`  

Doing so will allow us to run the `coffeescript module` executable with `coffee app.coffee`.

However, `global` npm modules cannot be required. You still need to install the module locally to be able to use it inside your application.

### Finding Modules
Instead of reinventing the wheel, there are many modules or libraries of code written out there that usually serves commonly used functions. Some ways to look for these modules are through the `npm registry`, searching on `Github` or even the `npm command line` using the `npm search <module_name>` command.

## Defining Dependencies
It's a best practice to add a `package.json` file to your Node projects. 

```
{
  "name": "My App",
  "version": 1,
  "dependencies": {
      "connect": "1.8.7"
  }
}
```

This allows us to easily install missing dependencies with `npm install` which will install the missing modules into the `node_modules` directory.

## Semantic Versioning
Earlier, we have seen the versioning for the `connect` module where it looks like `1.8.7`.  
- Major version: `1`
  - Safe to assume that API will change
- Minor version: `8`
  - Probably will not change API
- Patch version: `7`
  - Does not change API

### Ranges
You can specify a range of versions to keep your application up to date.

| Range | Versions | Remarks |
| ----- | -------- | ------- |
| `"connect": "~1"` | >= 1.0.0 < 2.0.0 | Dangerous |
| `"connect": "~1.8"` | >= 1.8.0 < 1.9.0 | API could change |
| `"connect": "~1.8.7"` | >= 1.8.7 < 1.9.0 | Considered safe |

More information for Semantic Versioning can be found here at [SemVer.org](http://semver.org/).