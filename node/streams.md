# Streams
`Streams` are like channels, where data can simply flow through. This allows for increased efficiency, especially when dealing with larged sized data, as we can then access the data piece by piece. Doing so lets us manipulate the data as it arrives in the server and prevent it from hogging the memory all at once.

The two most common `stream` types are `readable streams` and `writable streams`.

In the following code snippet  
`http.createServer(function(request, response) { ... })`  
The `request` object is a `Readable Stream` and the `response` object is a `Writable Stream`.

## Streaming Response
```
http.createServer(function(request, response) {
  response.writeHead(200);
  response.write("<p>Dog is running.</p>");
  setTimeout(function() {
    response.write("<p>Dog is done.</p>");
    response.end();  
  }, 5000);
}).listen(8080).
```
To understand what the code here is doing, we can break it down into simple steps:

1. When a `request` is issued from the browser, our server `responds` with the 200 status code.
2. The server then `writes` 'Dog is running' to the `stream`.
3. The browser then receives the `response` and the server then sets a timeout for 5 seconds.
4. During this time, the `stream` is still kept open - the channel between the server and client is still ready to receive data.
5. After 5 seconds, the server `writes` 'Dog is done' to the `stream` and `closes` it.

## Reading from the Request
Since the `request` object is a `Readable Stream` and also inherits from `EventEmitter`, it allows the `request` object to communicate with other objects through emitting events such as `readable` and `end`.

```
http.createServer(function(request, response) {
  response.writeHead(200);
  request.on('readable', function() {
    var chunk = null;
    while (null !== (chunk = request.read())) {
      console.log(chunk.toString());
    }
  });
  request.on('end', function() {
      response.end();
  })
}).listen(8080).
```
Here, the code lets us read the data from the `request` object and print it to the console.

1. The server is listening for the `readable` event on the request object.
2. When the `readable` event is emitted, we start a while-loop to read `chunks` (of data) from the `request`.
3. We then print this `chunk` of data to the console. 
4. When the `end` event is emitted, we complete the response with `response.end()`.

Notice that we have to use the `toString()` function when printing as the `chunks` of data are `buffers` - meaning that we may be dealing with binary data.

To echo the data read back to the client, we can simply change `console.log(chunk.toString())` to `response.write(chunk)`. 

## Piping
In the above example, we are basically `writing` to a `Writable Stream` as soon as we are `reading` from a `Readable Stream`. To make things easier, Node actually offers a helper function called `pipe` to perform these 2 functions together. 

Hence, the fairly verbose code earlier can be refactored into much shorter and simpler code. We can test the echo server with `curl -d 'hello bacon' http://localhost:8080`.

```
http.createServer(function(request, response) {
  response.writeHead(200);
  request.pipe(response);
}).listen(8080);
```

This is similar to the UNIX command line `|` where the `output` of one operation is `streamed` to the `input` of another operation. For example, `cat 'groceries.txt' | grep 'bacon'`.

## Reading & Writing Files
We can also use `streams` for files. In this case, we are streaming the contents from one file to another.
```
var fs = require('fs');

var file = fs.createReadStream("readme.md");
var newFile = fs.createWriteStream("readme_copy.md");

file.pipe(newFile)
```

In this regard, you can `pipe` any `Readable Stream` into any `Writable Stream`. Here, we will `pipe` data read from a `request` into a file.

```
var fs = require('fs');
var http = require('http');

http.createServer(function(request, response) {
  var newFile = fs.createWriteStream("readme_copy.md");
  request.pipe(newFile);

  request.on('end', function() {
    response.end('uploaded file!');
  });
}).listen(8080);
```

Here, we are `streaming` pieces of the file to the server. The server is then `streaming` those pieces to disk **as they are being read** from the request. The server does not hold the entire file in memory at once at any given time - it's all flowing continuously and `non-blocking` due to Node.

## Upload Progress
Here is an example of how to create a server that indicates your `progress` as you upload a file. We can upload a file to the server with `curl --upload-file large_file.jpg http://localhost:8080`.

```
var fs = require('fs');
var http = require('http');

http.createServer(function(request, response) {
  var newFile = fs.createWriteStream("readme_copy.md");
  var fileBytes = request.headers['content-length'];
  var uploadedBytes = 0;

  request.on('readable', function() {
    var chunk = null;
    while(null !== (chunk = request.read())) {
        uploadedBytes += chunk.length;
        var progress = (uploadedBytes / fileBytes) * 100;
        response.write("progress: " + parseInt(progress, 10) + "%\n");
    }
  });
  request.pipe(newFile);

  request.on('end', function() {
    response.end('file uploaded!');
  });
}).listen(8080);
```

1. We use `request.headers['content-length']` to get the size of the entire file and store this in the `fileBytes` variable.
2. We use the `uploadedBytes` variable to keep track of how many bytes have been uploaded so far.
3. When the `readable` event is emitted, we loop through and `read` each chunk from the request.
4. With each loop, we update the total number of `uploadedBytes` with the length of each `chunk`.
5. We can then calculate the total percentage `progress` with some simple math.
6. This value is then rounded and returned to the client with `response.write()`.
7. `Pipe` is still used to perform the actual file upload.