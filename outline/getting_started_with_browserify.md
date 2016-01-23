# Getting started with Browserify 

#### What did we do before browserify

When working with javascript you would normally add `<script>` tags

``` javascript
// use a vendor file
<script src="jquery.js" charset="utf-8"></scritpt>
// or use our own file
<script src="app.js" charset="utf-8"></scritpt>
```

But you would need to be careful of the ordering. You need to organize your scripts tags. If you were going to use
jquery in you app.js file you couldn't have this ordering

``` javascript
<script src="app.js" charset="utf-8"></scritpt>
<script src="jquery.js" charset="utf-8"></scritpt>
```
This can get to be cumbersome when you have many files

#### Requirements
- Node.js

#### Install
```
$ npm init
$ npm install --save-dev browserify
```
QUESTION: Why --save-dev
NOTE: This will create a binary along with the source (`node_modules/.bin/browserify`) which we can call from the command line:
```
$ browserify
```
TODO: Add picture

#### Create an entry in our package.json scripts entry:

```
{
  ...
  "scripts":{
    "build": "browserify app.js -o bundle.js"
  },
  ...
}
```
NOTE: Npm know to search in the browserify bin when it sees this keyword browserify in package.json. This way we DON'T need to have browserify globally 

- `app.js` is the file we want to be our entry point basically the root of all our modules
- `-o bundle.js` the `-o` option is used to specifieces where we are going to write out to, in this case `bundle.js`

#### Call our build script
```
$ npm run build
```
NOTE: This is now available as a short cut to running `$ browserify app.js -o bundles.js`

This will add file to our root directory called `bundle.js`

#### bundle.js

Now instead of including all our our script tags we just include this  single script `bundle.js`

```
<script src="bundle.js" charset="utf-8"></scritpt>
```

#### What is Browserify doing?

Going through and anayzing files and determining what order to put the files in and just give you one file to include on you page

#### So how do I get jquery in here?

Browserify works really great with npm and jquery is on npm. So you don't need to download the script or get the cdn, you simply install jquery in your project

```
$ npm install --save jquery
```

You will now see jquery in you dependencies in you package.json
TODO: Add image  

Then Require it in app.js:

```
var $ = require('jquery');
```

What this is doing is looking in the ``node_modules/jquery/`` directory and include the appropriate files

#### Write some jquery in app.js
```javascript
var $ = require('jquery');

$(document).ready(function(){
  $(body).write("Hell world");
})
```

#### Build our bundle.js

Now we can run our build script we defined above
```
$ npm run build
```

Now if we load our index.html file we should see "Hello world"

#### Setup automatic builds

If you don't want to run `$ npm run build` every time you make a change, you can use a module that watchify 

```
npm install watchify --save-dev
```

Then create a new script in you package.json

```
{
  ...
  "scripts":{
    "build": "browserify app.js -o bundle.js"
    "watch": "watchify app.js -o bundle.js"
  },
  ...
}
```

Now instead of running `$ build` you would run this;

```
$ watch
```

Now when every you make a change and save the file you app.js will automatically be built into bundle.js

#### We can also use Browserify to better organize our own code

In addition to requiring npm modules, we can also use browserify to include our local files so we can split our code into modules

Make sure that a file is exporting a module using the common.js syntax like this:

```
// filename: say-hello.js

function sayHello(name){
  return "Hello " + name;
}

module.exports = sayHello
```

Now we can import this module using the require syntax:
```
var sayHello = require("./say-hello.js")

sayHello("joe")
```
