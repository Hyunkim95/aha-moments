# Callbacks

## Different types of callbacks

***A synchronous*** callbacks is invoked before a function returns, that is, while the API receiving the callback remains on the stack.  

```
list.foreach(callback) 
```

* the expectation is that when foreach() returns, you expect that the callback has been invoked on each element 

***An asynchronous or deferred*** callbacks is invoked after a function returns, the deferral can be done through threads and main loops.  These kind of callbacks tend to be popular for  IO-related APIs.

```
socket.connect(callback)
```

* You would expect that when the connect() returns, the callback may not have been called, since it's waiting for the connection to complete. 

Note neither is better - nor are they interchangeable - they have different use purposes

## Choose Sync or Async

Because sync and async callbacks have different rules, they create different bugs.  The general rule is the following:

***if the callback must be deferred in any situtation, always defer it***

## Callbacks in Node 

An asynchronous operation are generally handled using a callback, when the returned data or error is ready it points to a function that will be executed once that result is ready. 

```
const request = require('request');
request('http://www.somepage.com', (error, response, body) => {
  if (error) // handle error
  else // do seomthing with the result 
})
```

The callback is saved to be executed later once the underling I/O operation of fetching the data over HTTP is done.  It is put on a queue called the event loop and is executed with a result from the request.

## Calback Hell

But more often or not, we want to use the data from an asynchronous operation to make another request - this will involve the following: 

```
const request = require('request');
let result;

request('http://www.somepage.com', (error, response, body) => {
  if (error) // handle body
  else result = body;
})

console.log(result);
```

The consoled output will be undefined as the callback has not been called yet, thus to do a second call we need to nest the second request inside the first one.

```
request('http://www.somepage.com', (firstError, firstResponse, firstBody) => {
  if (firstError) {
    // handle first error
  } else {
    request(`http://www.somepage.com/${firstBody.someVlaue}`, (secondError, secondResponse, secondBody) => {
      if (secondError) // handle second error
      else // use secondBody for something
    })
  }
})
```

The key things to note are the following: 
* using the data for more asynchronous operations will undoubtedly become messy 
* an error needs to be check for each consecutive asynchronous operation adding to the messiness

## Solution to callback hell

> The cause of callback hell is when people try to write JavaScript in a way where execution happens visually from top to bottom.

```
var form = document.querySelector('form')
form.onsubmit = function (submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, function (err, response, body) {
    var statusMessage = document.querySelector('.status')
    if (err) return statusMessage.value = err
    statusMessage.value = body
  })
}
```

The first solution is to remove anonymity of the functions giving us the following impact:
* code is easier to read 
* stack traces 
* modularity 

```
document.querySelector('form').onsubmit = formSubmit

function formSubmit (submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, postResponse)
}

function postResponse (err, response, body) {
  var statusMessage = document.querySelector('.status')
  if (err) return statusMessage.value = err
  statusMessage.value = body
}
```

* note the use of ***function hoisting***

> When JS is parsed, a first pass is done over each scope, and function definitions are immediately discovered. An example of a function definition is function foo() {}. When a function is declared like this, with a name, that name becomes available to the entire scope when the code in that scope is executed.

A crude timeline of how JS gets executed:

1. Parse the scope and detect all function definitions
2. Execute the code top-to-bottom with all functions found in step 1 available as variables

This behavior is called 'hoisting' because it is almost like the function definitions have been 'hoisted' up to the top of the function.

### Modularize 

> "Write small modules that each do one thing, and assemble them into other modules that do a bigger thing. You can't get into callback hell if you don't go there."

```
module.exports.submit = formSubmit

function formSubmit (submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, postResponse)
}

function postResponse (err, response, body) {
  var statusMessage = document.querySelector('.status')
  if (err) return statusMessage.value = err
  statusMessage.value = body
}
```

can be turned into 

```
var formUploader = require('formuploader')
document.querySelector('form').onsubmit = formUploader.submit
```

Resulting benefits: 
* easier for new developers to understand -- they won't get bogged down by having to read through all of the formuploader functions
* formuploader can get used in other places without duplicating code and can easily be shared on github or npm

## Resources

* http://callbackhell.com/
* http://blog.izs.me/post/59142742143/designing-apis-for-asynchrony
* https://blog.ometer.com/2011/07/24/callbacks-synchronous-and-asynchronous/
* https://medium.com/codebuddies/getting-to-know-asynchronous-javascript-callbacks-promises-and-async-await-17e0673281ee