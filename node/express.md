# Express
`Express` is a web development framework for Node.js. Some of the pros of `express` are:
- Easy route URLs to callbacks
- Midddleware (from Connect)
- Environment based configuration
- Redirection helpers
- File uploads

To install `express`, run `npm install --save express`. The `--save` switch will also add the installed module to the `package.json` dependencies file.

Here's how to get started with `express`.
```
var express = require('express');
var app = express();
```

Then, we are define an endpoint at the `root route` indicated by `'/'`. The callback here then reads in a file and sends it as a response.
```
app.get('/', function(request, response) {
  response.sendFile(__dirname + "/index.html");
});

app.listen(8080);
```

## Example
