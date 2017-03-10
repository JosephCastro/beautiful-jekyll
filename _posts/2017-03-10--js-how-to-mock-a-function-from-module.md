---
layout: post
published: false
title: '[JS] How to mock a function from module'
---
If you have something like:
```javascript
var myFunction = function(...){
    ...
};

var myFunction2 = function(...){
    myFunction();
};

exports.myFunction = myFunction;
exports.myFunction2 = myFunction2;
```

Then you try testing `myFunction2` with a mock of `myFunction`, but you can't, because
mocking a function after requiring a module is impossible in javascript.

So I read this possible [solution](https://github.com/facebook/jest/issues/936#issuecomment-214939935)
```javascript
var myFunction = function(...){
    ...
};

var myFunction2 = function(...){
    exports.myFunction();
};

exports.myFunction = myFunction;
exports.myFunction2 = myFunction2;
```

And then in your test:
```javascript
module.myFunction = function(...){ ... };
module.myFunction2(...);
```

But for me still I get the initial function `myFunction`.

So I try to use [`rewire`](https://github.com/jhnns/rewire)
from the repo:
```
rewire acts exactly like require. With just one difference: Your module will now export a special setter and getter for private variables.
```
```javascript
myModule.__set__("path", "/dev/null");
myModule.__get__("path"); // = "/dev/null"
```
```
This allows you to mock everything in the top-level scope of the module, like the fs module for example. Just pass the variable name as first parameter and your mock as second.
```

So I use this to mock the module function like this:

```javascript
var rewire = require('rewire');
var module = rewire('../module');

module.__set__('myFunction', function(...){ console.log('do anything!'); });
```

And now I can mock and set the behaviour for the test! :)

