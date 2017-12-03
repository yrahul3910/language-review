This was constructed with the help of Node School's How to NPM and Learn You Node. You can get these by running
```
npm install how-to-node
npm install learnyounode
```
# Some NPM Commands
## `npm adduser`
This registers you on [the NPM website](npmjs.com).

## `npm init`
Creates a `package.json` file for developing an app with Node. The `-y` option is equivalent to pressing Enter for all the questions.

## `npm install`
Installs a package from the NPM repository. Use `--save` to add the package as a dependency to the project, `--save-dev` to indicate it's a developer dependency (like the Babel preloader), and `-g` to install globally (this requires root privileges).

## `npm ls`
Lists the installed packages.

## `npm publish`
Publishes your package to the NPM JS website. It's a nicety to include a `README.md` file in the current directory for others to read.

## `npm version`
Used to update the version of the package. Takes the new version number as an argument. This reflects the change in the `package.json` file.

## `npm dist-tag`
Adds, removes or modifies a tag for a given version. Example:  
`npm dist-tag add @yrahul3910/pkg@0.0.1 latest`  
Marks the version 0.0.1 as the latest.  
`npm dist-tag rm @yrahul3910/pkg@1.0.0 beta`  
Removes the beta tag for version 1.0.0.

## `npm outdated`
Lists the outdated packages in use. Used in conjunction with `npm update`, which takes a package name as the argument.

# I/O in Node.js
`let fs = require('fs');`  
* `fs` is a Node core module that includes functions to help with I/O.  
* In Node.js, the first two elements of the `process.argv` array, which lists the arguments to a program, are reserved for Node settings. This means the first argument is at `process.argv[2]`. You can run a program using `node program.js`  
Examples:
```
// Synchronously read a file, which returns a buffer object, and get its contents
var input = fs.readFileSync(process.argv[2]).toString();
```
The Node way is to use async methods, as shown below:
```
// Async way (the Node way):
fs.readFile(process.argv[2], (err, data) => {
  if (err) console.log(err);
  else {
    let lines = data.toString().split('\n');
    console.log(lines.length - 1);
  }
});
```
`readdir()` returns the files in a directory. It takes a directory and a callback as arguments. Example:
```
// Get filenames in a directory, filter by the extension in 2nd argument
fs.readdir(process.argv[2], (err, list) => {
  if (err) console.log(err);
  else {
    list.forEach((filename) => {
      if (filename.endsWith('.' + process.argv[3])) console.log(filename);
    });
  }
});
```
# Modularizing Code
For a single function export, overwrite the `module.exports` object. Example:
```
module.exports = function (dir, filterStr, callback) {
  fs.readdir(dir, function (err, list) {
    if (err) {
      return callback(err);
    }
    
    list = list.filter(function (file) {
      return path.extname(file) === '.' + filterStr
    });
    
    callback(null, list)
  });
}
```
This is put in a file, say `list.js`, and then used as:
```
let list = require('./list');
list(process.argv[2], process.argv[3], (err, data) => {
  if (err) console.log(err);
  data.forEach((item) => {
    console.log(item);
  });
});
```

# HTTP Requests
## HTTP GET in NodeJS
```
const http = require('http');
http.get(process.argv[2], (response) => {
  response.setEncoding('utf8');
  response.on('data', (data) => {
    console.log(data);
  });
});
```

## Handling Async Data
When getting data from multiple servers using `http.get()`, the data may not come in the order expected. To solve this issue in Node, we use counting callbacks that count how many servers have sent data, and store the data into a queue. An alternative is to use the `async` package. The example below uses the `bl` module to get all the chunks of data from a server.
```
const http = require('http');
const bl = require('bl');

let queue = [];
let count = 0;

const handleData = (data, index) => {
  ++count;
  queue[index] = data.toString();
    
  if (count === 3) {
    for (var x of queue)
      console.log(x);
  }
};

for (let i = 2; i < 5; ++i) {
  http.get(process.argv[i], (response) => {
    response.pipe(bl((err, data) => {
      if (err) console.log(err);
      handleData(data, i - 2);
    }));
  });
}
```

# Creating Servers in NodeJS
## Creating a TCP Server
The `net` module has a `createServer()` function that takes a connection listener as an argument, and returns the server. Every connection to the TCP server triggers a call to the listener. The listener takes the socket as a parameter. You must call `server.listen()` to listen on a particular port.  

The example below creates a TCP time server that writes the current time to the socket in the format `YYYY-MM-DD HH:MM`.
```
const net = require('net');

const formatZeros = (i) => {
  return ("00" + i).slice(-2);
}
let server = net.createServer((socket) => {
  // Get current date
  let date = new Date(Date.now());
  let dateString = date.getFullYear() + "-" +
    formatZeros(date.getMonth() + 1) + "-" + 
    formatZeros(date.getDate()) + " " +
    formatZeros(date.getHours()) + ":" +
    formatZeros(date.getMinutes()) + "\n";
  socket.end(dateString);
});
server.listen(process.argv[2]);
```

## Creating an HTTP File Server
The `http` module also provides a `createServer()` function that takes a listener as an argument and returns the HTTP server. The listener takes an HTTP request and a response as parameters. `request` is used to get information about the request such as the query string, while `response` is what is sent back, with headers. These parameters are also Node streams. The following code creates an HTTP server that returns the same text file on every connection.
```
const http = require('http');
const fs = require('fs');

let server = http.createServer((request, response) => {
  let stream = fs.createReadStream(process.argv[3]);
  stream.pipe(response);
});
server.listen(process.argv[2]);
```

### HTTP Uppercaser
The example below receives POST requests, converts the POST request data to uppercase and returns it to the client.
```
const http = require('http');

let server = http.createServer((request, response) => {
  if (request.method == 'POST') {
    let body = '';
    request.on('data', (data) => {
      response.write(data.toString().toUpperCase());
    });
  }
});
server.listen(process.argv[2]);
```

## Creating an API Server
The `url` module is helpful in parsing the URL of the API request. The example below serves JSON data in a string format when it receives a GET request to the path `/api/parsetime`, where the query string contains the key 'iso' and an ISO-format time as value. Another endpoint for this server, with the path `/api/unixtime` accepts the same query string but returns the UNIX epoch time in milliseconds.
```
const http = require('http');
const url = require('url');

let server = http.createServer((request, response) => {
  if (request.method == 'GET') {
    let reqDetails = url.parse(request.url, true);
    
    const isoTime = reqDetails.query['iso'];
    const isoDate = new Date(isoTime);
    response.writeHead(200, {'Content-Type': 'application/json'});
    
    if (reqDetails.pathname == '/api/parsetime') {
      response.end(JSON.stringify({
        'hour': isoDate.getHours(),
        'minute': isoDate.getMinutes(),
        'second': isoDate.getSeconds()
      }));
    } else if (reqDetails.pathname == '/api/unixtime') {
      response.end(JSON.stringify({
        'unixtime': isoDate.valueOf()
      }));
    }
  }
});
server.listen(process.argv[2]);
```