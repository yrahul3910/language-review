This was made with the help of [Understanding Express by Evan Hahn](http://evanhahn.com/understanding-express/) and `expressworks` (use `npm install expressworks` to install).
# Basics
Express is an abstraction layer web framework that helps you build web apps. Node's `http` module helps in creating a web server. The `createServer` function takes a callback as an argument, that in turn takes a `request` and a `response` variable as parameters.  

The `request` parameter has a `url` field that contains info about the URL requested by the client, and can thus be used for routing, as shown:
```
const http = require('http');

let server = http.createServer((request, response) => {
	// Home page
	if (request.url === '/') {
		response.writeHead(200, {'Content-Type': 'text/html'});
		res.end('Hello World!');
	} else if (request.url === '/about') {
		// Similar code for About page
	} else {
		response.writeHead(404, {'Content-Type': 'text/html'});
		res.end('Page not found!');
	}
});
server.listen(process.argv[2]);
```

# Middleware
Express is a middleware layer that aims to simplify this. Using Express, a basic Hello World app can be written as below:
```
const http = require('http');
const express = require('express');

let app = express();
app.use((req, res) => {
	res.writeHead(200, {'Content-Type': 'text/plain'});
	res.end('Hello World!\n');
});
http.createServer(app).listen(process.argv[2]);
```
Here, `app` is just a function that goes through all the middleware till the end, but is basically just a request handler.  

Each piece of middleware is another request handler, which also take a `next` parameter, that is called at the end. The Hello World app with logging can be written as:
```
const http = require('http');
const express = require('express');

let app = express();

// Logging middleware
app.use((req, res, next) => {
	console.log('Method: ' + req.method + '\nURL: ' + req.url);
	next();
});
app.use((req, res) => {
	res.writeHead(200, {'Content-Type': 'text/plain'});
	res.end('Hello World!\n');
});
app.listen(process.argv[2]);
```
Static middleware (i.e., pages, scripts, images, CSS) can be served using `express.static` as below:
```
const path = require('path');
app.use(express.static(path.join(__dirname, 'public')));
app.listen(process.argv[2]);
```

# Routing
You could use `app.use` to put middleware together like this:
```
app.use((req, res, next) => {
	if (req.url === '/') {
		// Send response
	} else {
		next();
	}
});
app.use((req, res, next) => {
	if (req.url === '/about') {
		// Send response
	} else {
		next();
	}
});
app.use((req, res) => {
	// No next here, send 404
});
app.listen(process.argv[2]);
```
This is an ugly solution, so Express introduces routing:
```
let app = express();

app.all('*', (req, res, next) => {
	response.writeHead(200, {'Content-Type': 'text/plain'});
	next();
});
app.get('/', (req, res) => {
	response.end('Hello, World!');
});
app.get('/about', (req, res) => {
	response.end('About page');
});
app.get('*', (req, res) => {
	response.end('404!');
});
app.listen(process.argv[2]);
```
The `.get` is invoked whenever the server receives a GET request. `.post` can be used to listen for POST requests.

# Request Handling
Express adds new methods to the `request` and `response` objects. Some examples taken from [Express API reference](http://expressjs.com/en/4x/api.html#res).
```
response.redirect('/about/v4');
response.redirect('http://www.google.com');
response.json({ 'user': 'tobi' });
response.download('/report-12345.pdf', 'report.pdf');
```

# Templating with Pug
Express also works with Pug. First, install Pug using `npm install --save pug`. Then, create the `index.pug` file under the `templates` folder.
```
html
	body
		h1 Hello World!
		p= message
```
To set up Express to use Pug, first set the path to the `templates` folder:
```
app.set('views', path.join(__dirname, 'templates'));
```
Then tell Express what template engine to use:
```
app.set('view engine', 'pug');
```
Then, we use the `render()` method of the `response` object in the request handler, as below:
```
app.get('/home', (req, res) => {
	res.render('index', {message: 'This is the home page'});
});
```
