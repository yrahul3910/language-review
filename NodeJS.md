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