# Write Browserify transforms

#### What are transforms

Things you give to Browserify and browserify will give you files and you transform those file and you give them back to browserify, which ends up in  your final bundle

#### How to find browserify transforms 

Search for npm modules [npmjs.com](npmjs.com) with the term "browserify" or for packages that end in "ify".

#### How do you install and use existing transforms

##### Install
```
$ npm install --save-dev browserify 
$ npm install --save-dev brfs
```
NOTE: This lets us inline files in our bundles

##### Use
When running the browerify command specify the transform you wan to use with the `-t` option.

```
$ browserify index.js -o bundle.js -t brfs
```
OR you could make a build script in the package.json

```
{
  ...
  "scripts": {
    "build": "browserify index.js -o bundle.js -t brfs"
  },
  ...
}
```
Then you can run like this
```
$ npm run build
```

If you have many tranforms, you can list them in the package.json and they 
will be added every time we run the browserify command (either directly in the command line or as a script in package.json like above)

```
{
  ...
  "browserify": {
    "transform": ["brfs"]
  },
  ...
}
```

#### An example of using brfs browserify tranform module

```
var fs = require('fs');
fs.readFile('./bears.txt', function(err, bears){
  console.log(bears)
})
```

Now when we browserify that file, and view our index.html with bundle.js script tage, the console will log all the bears

#### How to create you own tranform

We need to create a file that will contain our tranform. Here we'll just
name it `tranform.js` (although, you can name it anything). Then will list
it in our list of browserify tranforms in package.json, using the path

```
{
  ...
  "browserify": {
    "transform": ["./tranform.js"]
  },
  ...
}
```

Now in our `tranform.js` we will write our tranform. First, Browserify will call whatever we export and supply the filename of whatever it's exportsing
so we need to write our file like this:

```
module.exports = function(file){
  ...
}
```

It expects you to return a stream that modifies the contents of that file

```
module.exports = function(file){
  ...
  return stream;
}
```

To create the stream we can use the through2 module

```
npm install --save-dev through2
```

```
var through = require('throught2')
module.exports = function(file){
  ...
  return through();
}
```

Through2 takes a couple arguments;
- `function` that takes `part`, `enc`, and `next` parameters

```
var through = require('throught2')
module.exports = function(file){
  ...
  return through(function(part, enc, next){
  });
}
```

The contents of the file wont give the entire contents of the file, instead
it will stream parts (chunks). So you can do 2 thinks

1. Tranform individual parts, if you don't need the entire file.
```
var through = require('throught2')
module.exports = function(file){
  ...
  return through(function(part, enc, next){
    part = part.replace(/hi/g, 'hello');
    this.push()
    next()
  });
}
```
NOTE: This will write out the parts as they are recieved

2) But if you want the entire file before writing out
```
var through = require('throught2')
module.exports = function(file){
  var entireFile = [];
  return through(function(part, enc, next){
    entireFile.push(part)
    next()
  }, function(done){
    // this is called when the stream is finished, when it's flush
    entireFile = entireFile.join(''))
    entireFile = entireFile.replace(/hi/g, 'hello')
    this.push(entireFile);
    done();
  });
}
```
#### Example transform

This tranform will convert all "hi" to "hello" in our files

```
var through = require('throught2')
module.exports = function(file){
  var entireFile = [];
  return through(function(part, enc, next){
    entireFile.push(part)
    next()
  }, function(done){
    // this is called when the stream is finished, when it's flush
    entireFile = entireFile.join(''))
    entireFile = entireFile.replace(/hi/g, 'hello')
    this.push(entireFile);
    done();
  });
}
```

#### You can also use options for your tranform

To call browserify on the command line with options:
```
$ browserify file.js -o bundle.js -t [ ./index.js --key=value]
```
NOTE: key value represent your options

Then to access your options inside your custom tranform, just access the
second argument to the function you are exporting

```
var through = require('throught2')
module.exports = function(file, options){
  ...
}
