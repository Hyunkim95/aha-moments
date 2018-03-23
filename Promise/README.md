# Promises
The Promise is a stand-in for a future value, and so it can be returned from a function, passed as a parameter and used in any other way a standard value would be used.  

**Rules for a promise:** 
* A promise or thenable is an object that supplies a standard compliant then() method
* A pending promise may transition into a fulfilled or rejected state
* A fulfilled or rejected promise is settled, and must not transition into any other state
* Once a promise is settle, it must have a value (which may be undefined) - the value must not change
	* The promise is a one way latch - once resolved with a value or rejected with a reason, its state and value/reason can never change. No matter how many times you do `.then()`  on the same promise you will always get the same result. It is immutable 


----------


* The core essence of promises are an abstractions to make working asynchronous operations more pleasant
* It allows you to treat asynchronous operations as first class objects, passing them around and aggregating them 

> A promise is defined as an object that has a function as the value for the property  `then`:

> `then(fulfilledHandler, errorHandler, progressHandler)`

> Adds a  `fulfilledHandler`,  `errorHandler`, and  `progressHandler`  to be called for completion of a promise. The`fulfilledHandler`  is called when the promise is fulfilled. The  `errorHandler`  is called when a promise fails. The`progressHandler`  is called for progress events. All arguments are optional and non-function values are ignored. The`progressHandler`  is not only an optional argument, but progress events are purely optional. Promise implementors are not required to ever call a  `progressHandler`  (the  `progressHandler`  may be ignored), this parameter exists so that implementors may call it if they have progress events to report.

* You use then to attach callbacks to a promise whether for success, errors or progress.

* Promises are about functional composition and error bubbling of async functions, that is - you can feed the return value of one function straight into another and keep doing this indefinitely. Furthermore, an error within the chain will bypass all the layers until it is handled by a `catch`

Promises should either :
* Become fulfilled by a value 
* Become rejected with an exception

* Allowing us to create a compositional chain of fulfillments, but also being interrupted at any time by a rejection that is only handled by someone who declares they are ready to handle it. 


----------


> This function should return a new promise that is fulfilled when the given  `fulfilledHandler`  or  `errorHandler`callback is finished. This allows promise operations to be chained together. The value returned from the callback handler is the fulfillment value for the returned promise. If the callback throws an error, the returned promise will be moved to failed state.

* then is really a mechanism for applying a transformation to a promise and yielding a new promise from that transaction
* In a way, the Promise is a monad  and the `then()` is the `fmap` equivalent 

> Once a promise is fulfilled or failed, the promise's value MUST not be changed, just as a values in JavaScript, primitives and object identities, can not change (although objects themselves may always be mutable even if their identity isn't).

## Resources
* https://medium.com/front-end-hacking/implementing-javascript-functors-and-monads-a87b6a4b4d9a
* https://gist.github.com/domenic/3889970
* https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-promise-27fc71e77261