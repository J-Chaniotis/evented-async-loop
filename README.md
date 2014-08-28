##evented-async-loop

Loop through asynchronous code with events

## Installation

`npm install evented-async-loop`

## Usage

Check `examples` and `test` folders for more details.

**Basic**
```javascript
'use strict';

var asyncLoop = require('../index');
var dummyArray = require('./dummyArray');


//Make an array of asynchronous functions
var arr = dummyArray(10);

var loop = asyncLoop.create(arr);

loop.on('next', function (elm, i, array) {
    elm(function() {
        console.log(i);
        loop.next();
    });
});

loop.on('done', function () {
    console.log('Done!');
});

loop.start();
```

**Chaining**
```javascript
//All methods accept .next are chainable
loop.on('next', function (elm, i, array) {
    elm(function () {
        console.log(i);
        loop.next();
    });
}).on('done', function () {
    console.log('Done!');
}).start();
```

**Exit early**
```javascript
loop.on('next', function (elm, i, array) {
    elm(function () {
        if (i === 5) {
            // Stop the loop early, you can pass any number of arguments to .done
            loop.done(i, 'i am Batman');
        }
        loop.next();
    });
}).on('done', function (i, msg) {
    console.log('Loop stopped at %s and %s', i, msg);
}).start();
```

**Data propagation**
```javascript
// loop.start(...).on() is ok too!
loop.on('next', function (elm, i, array, arg1, arg2) { // extra arguments are appended
    elm(function () {
        // pass data to the next iteration
        loop.next(++arg1, arg2);
        // after the final iteration tha arguments are passed to 'done'
    });
}).on('done', function (arg1, arg2) {
    console.log('arg1: %s, arg2: %s', arg1, arg2);
}).start(0, 'blah'); // Pass any number of arguments
```

**Error handling**
```javascript
loop.on('next', function (elm, i, array) {
    elm(function () {
        if (i === 3) {
            // Emitting an error event will not break the loop
            loop.error('Oh noez!');
        }

        if (i === 8) {
            // But if you need to break the loop and emit an error
            loop.break().error('This will stop the loop');
            // or, loop.error('This will stop the loop').break();
            // if you want to emit the error first
        }

        loop.next();
    });
}).on('done', function () {
    // This wont run
    console.log('Done!');
}).on('error', function (err) {
    console.log(err);
}).start();
```
##API
// Todo
###Events
###Methods

