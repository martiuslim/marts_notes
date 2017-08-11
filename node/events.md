# Events
With `events`, you may think about the events in the `Document-Object-Model` or `DOM`. The DOM triggers `events` such as `click`, `submit` or `hover` which you can listen for and run code when the corresponding events get triggered. One such example of this is done through jQuery:

`$("p").on("click", function() { ... });`

Like the DOM, many `objects` in Node also emit events. If they emit events, then the objects also inherit from the `EventEmitter` constructor. For example:
- `net.Server` inherits EventEmitter and emits the `request` event
- `fs.readStream` inherits EventEmitter and emits the `data` event

## Custom Event Emitters
To create your own `EventEmitter`, we can start with the following:

`var EventEmitter = require('events').EventEmitter;`

Here, we are creating a `logger` that emits the `error`, `warn` and `info` events:

`var logger = new EventEmitter();`

To listen for the `error` event and trigger a callback when the `error` event is emitted:

```
logger.on('error', (message) => {
  console.log('ERR: ' + message);
});
```

To trigger or emit the event, specify the name of the event to be triggered:

`logger.emit('error', 'Spilled Milk');`

## Events in Node
To further understand the `"Hello Dog"` server example in `basics`, let's look at the following code snippet:

`http.createServer((request, response) => { ... });`

1. `http.createServer` takes in a `requestListener` as a parameter which listens on the `request` event and returns a `server` object.
2. The `server` object is an `EventEmitter` and emits several events, one of which is the `request` event.
3. The `request` event being emitted passes 2 parameters, `request` and `response`, back to the `callback` which is what we're using as the only parameter for our `createServer` function. 

The alternative syntax for adding `event listeners` is:

```
var server = http.createServer();
server.on('request', (request, response) => { ... });
``` 
This allows you to listen to multiple events on an object or have multiple functions that listen to the same event.