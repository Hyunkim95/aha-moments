# Async Await

* Async functions return a Promise.   If the function throws an error, the Promise will be rejected.   If the function returns a value, the Promise will be resolved.

Async and Await is built off generator functions, a generator function is a function which pauses its execution until it reaches a yield and a final return keyword. 

Generator functions are declared by adding a astericks in front of the function keyword.  They provide multiple values on demand, one at a time: they only continue their execution up to the next yield or return when someone else requests it.

```
function* generator() {
  const users = yield getUsersFromDatabase()
  return (users + " Correctly received")
}

const iterator = generator()
const iteration = iterator.next()

```
* runs the generator function up to its first yield: it calls getUsersFromDatabase(), and this function returns a promise, which is then yielded.  The generator function halts execution at this point, right before const users gets assigned anything 

```
iteration { value: Promise{}, done: false }
```

* Iteration holds the unresolved promise

```
iteration.value.then( (1)
  resolvedValue => {
    (2)
    resolvedValue Test Users

    (3)
    const nextIteration = iterator.next(resolvedValue)

    (4)
    nextIteration { value: 'Test Users Correctly received', done: true }
  }
)
```
1. Defines a function to call when the promise resolves 
2. At this point we got the resolved value from the promise, which is "Test Users"
3. We call next() to continue the execution of the generator function, passing it the resolvedValue, which gets assigned to the const users on line2.  The generator function then reaches its return statement, concluding the statement
4. We got the final value, with done: true

```
async function test() {
  const users = await getUsersFromDatabase()
  return (users + " Correctly received") // Test Users correctly received
}
```

## Basic Syntax

```
// normal function
function add (x,y) {
  return x + y
} 

// async function
async function add (x,y) {
  return x + y
}
```

## Await
Async functions make use of the await expression, this will pause the async function and wait for the Promise to resolve prior to moving on.

## Usage

```
function doubleAfter2Seconds(x) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x * 2);
    }, 2000);
  });
}

doubleAfter2Seconds(10).then((r) => {
  console.log(r);
});
```

To add the returned values, we need to chain the .thens,

```
function addPromise(x){
  return new Promise(resolve => {
    doubleAfter2Seconds(10).then((a) => {
      doubleAfter2Seconds(20).then((b) => {
        doubleAfter2Seconds(30).then((c) => {
          resolve(x + a + b + c);
        })
      })
    })
  });
}
```

This is the async await equivalent:

```
async function addAsync(x) {
  const a = await doubleAfter2Seconds(10);
  const b = await doubleAfter2Seconds(20);
  const c = await doubleAfter2Seconds(30);
  return x + a + b + c;
}
```

## Error handling 

```
async function asyncTask () {
  try {
    const valueA = await functionA()
    const valueB = await functionB(valueA)
    const valueC = await functionC(valueB)
    return await functionD(valueC)
  } catch (err) {
    logger.error(err)
  }
}
```

## Note

* ***Async functions always returns a promise*** 
* ***Await can only wait for promises sequentially, not in parallel.*** - the await keyword can only wait for promises one at a time, not several at once. - a way to tackle multiple parallel requests is to use Promise.all

```
async function executeParallelAsyncTasks () {
  const [ valueA, valueB, valueC ] = await Promise.all([ functionA(), functionB(), functionC() ])
  doSomethingWith(valueA)
  doSomethingElseWith(valueB)
  doAnotherThingWith(valueC)
}
```
* Async Await's intention seems to be for imperative code styles rather than functional patterns. 

## Resources
* https://medium.com/front-end-hacking/modern-javascript-and-asynchronous-programming-generators-yield-vs-async-await-550275cbe433
* https://codeburst.io/javascript-es-2017-learn-async-await-by-example-48acc58bad65
* https://blog.risingstack.com/mastering-async-await-in-nodejs/

