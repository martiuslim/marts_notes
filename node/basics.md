# Node Basics
Node uses an `event-driven`, `non-blocking` I/O model that makes it lightweight and efficient. So what is `non-blocking`?.

## Blocking vs Non-Blocking
To illustrate the difference between `blocking` and `non-blocking` code, let's say that we want to read a file and print its contents. Here is what it will look like in pseudo-code.

### Blocking 
> Read file from Filesystem, set equal to "contents"  
> Print contents  
> Do something else

The code here executes the file reading first, followed by the printing of its contents. We cannot print the contents until the program finishes reading all the contents in the file. Hence, this is `blocking` code because the program has to wait for Line 1 to complete before being able to continue to print the contents and do something else.

### Non-Blocking
> Read file from Filesystem  
> Whenever you're complete, print the contents  
> Do something else

Here, Line 2 is known as a `callback`. Or in plain English, we're going to `callback` that function when the file is done reading. When you execute this program, it will run Line 1, then Line 3. Line 2 is run when Line 1 is done running anytime in the future, indicating `non-blocking` code because the program does not have to wait for the file to be read before doing something else.

Below we'll see the Node code snippets of the pseudo-code above.

### Blocking code
```
var contents = fs.readFileSync('/etc/hosts');
console.log(contents);
console.log('Doing something else');
```

### Non-Blocking code
```
fs.readFile('/etc/hosts', (err, contents) => {
    console.log(contents);
});
console.log('Doing something else');
```

## Callback Alternate Syntax
```
fs.readFile('/etc/hosts', (err, contents) => {
  console.log(contents);
});
```
Is also  the same as 
```
var callback = function(err, contents) {
  console.log(contents);
}
fs.readFile('/etc/hosts', callback);
```

Writing `non-blocking` code allows us to run tasks asynchronously or in parallel.

---

## Events
To understand `events` in Node, let's look at an example of a `Hello Dog` server in `hello.js`.

```
var http = require('http');

http.createServer(function(request, response) {
  response.writeHead(200);
  response.write("Hello, this is dog.");
  response.end();
}).listen(8080);

console.log('Listening on port 8080...');
```

To run the server, simply run `node hello.js`. To get a response from the server, run `curl http://localhost:8080` or visit that url in your browser. There are a few things happening here when we run the server.

1. The first time the code is executed, Node will `register events`. In this case, it is the `request` event.
2. Once the script has been executed, Node then goes into an `event loop`. This means that Node is checking for events continuously until one that was registered earlier comes in.
3. As soon as a `request` comes in, it will trigger the corresponding event and run the `callback` that was written and print `"Hello, this is dog."`.

This allows us to program in an `evented` way using the `event loop`, letting us write code that is potentially `non-blocking`. Events can also trigger more events, emitting events into an `event queue`. These events in the queue are processed one at a time on the event loop.