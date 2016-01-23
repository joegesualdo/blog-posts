# Getting started with Node streams

#### What is a stream
Something that if you implement you code will automagically work with a bunch of other people code that works stream

It's basically an interface

#### Types of stream

- `Readable`
    Can be read. Readabel output
- `Writable`
    You can write to it. It takes input
- `Transform stream`
    You can write to it. And you can read it 

#### Create a file to use with our examples
Add a file with many items:

```
// filename: cars.txt
honda
nissan
jeep
chocolate
hummer
```
NOTE: Some items were intentially not cars

## Read the file with readable streams

Use the `fs` module to read the file

```
var fs = require('fs')
var cars = fs.createReadStream('cars.txt')
```

This will create a readable stream called cars that will read the file

To make sure it's working add an event listener that everytime the steam gets data, print the string

```
var fs = require('fs')
var cars = fs.createReadStream('cars.txt')
cars.on('data', function(data){
  console.log(data.toSTring())
})
```

Now if we run this our cars.txt file contents will get printed to the console

BUT since our console out put is a stream itself, so we could just pass our readable stream to the console steam like this:

```
var fs = require('fs')
var cars = fs.createReadStream('cars.txt')
cars.pip(process.stdout)
```

Now when we run this file we get the same out put because it just pipes the stream directly to our stdoutput stream which prints to our console 

## Create a writable stream:

```
var fs = require('fs')
var cars = fs.createReadStream('cars.txt')
var actualcars = fs.createWriteStream('actualcars.txt')
```

So anything that get's the the actualcars stream will write to the actualcars.txt

Now we can write the readable stream to the writable stream by piping:

```
var fs = require('fs')
var cars = fs.createReadStream('cars.txt')
var actualcars = fs.createWriteStream('actualcars.txt')
cars.pipe(actualcars)
```

So now when we run the code it creates a file `actualcars.txt` with the same contents as `cars.txt`

Stream make it really easy to move data around from one or another

#### Create your own stream (transform stream)

Let say we want to filter our any cars that are not actaully cars

We need access to the tranformable stream:
```
var Transform = require('stream').Transform
```

##### Create the class
Once you have access you need to inherit from it and we'll use the inherits helper to do this: 
```
var Transform = require('stream').Transform
var inherits = require('util').inherits

function ActualBears(){
  Transform.call(this)
}
inherits(ActualBears, Tranform)
```

##### Add a method to the class
```
ActualCars.prototype._transform = function(chunk, enc, done){
}
```
NOTE: This isn't a method you call. Rather it's called by the steam when data is written to it. It takes 3 argumetns
- chunk
    The data that's being writtent o it
- enc
    type of data it is
- done
    When were ready for more data to be written to it

So when data is written to our transfor stream we will get it within the `_transform` method we defined as chunk

```
ActualCars.prototype._transform = function(chunk, enc, done){
}
```

This will split our chuncks by new lines then filter out everthing that is not an actual car then join them back into a string

NOTE: The way streams work, is they wont return the entire content of the file. But rather parts of the file at time (chunk)

```
ActualCars.prototype._transform = function(chunk, enc, done){
  chunk = chunk.toString().split('\n').filter(function(car){
    return (car !== 'chocolate')
  }).join('\n')
}
```
But we aren't done, now that we modified our data, or tranformed the data the way we want, we need to call `this.push()` which says data will need to be written out of the stream. So now our

```
ActualCars.prototype._transform = function(chunk, enc, done){
  chunk = chunk.toString().split('\n').filter(function(car){
    return (car !== 'chocolate')
  }).join('\n')
  this.push(chunk);
}
```

So no data comes in (readable strea) then we tranform the data, then we push the data out

Lastly, we need to tell it we're done. we do this by calling the `done()` method

```
ActualCars.prototype._transform = function(chunk, enc, done){
  chunk = chunk.toString().split('\n').filter(function(car){
    return (car !== 'chocolate')
  }).join('\n')
  this.push(chunk);
  done();
}
```

NOTE: We converted our chunk to a string `chunk.toString()` but if you had more complex data that's where the `enc` parameter comes in
TODO: Explain

#### Use our transform stream

```
var fs = require('fs')
var read = fs.createReadStream('cars.txt')
var write = fs.createWriteStream('actualcars.txt')

read.pipe(new ActualCars()).pipe(write)
```

This is basically saying read our file, then pipe it and transform it using our custom transform stream then write it out to our actaul cars file 

Now when we run it, we will get a list of the care with chocolate 

#### Sharing your tranform stream with others

Now that you created a transform stream, anyone can use it
