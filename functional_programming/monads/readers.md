# Reader 

```
Reader       e            a
        environment    resultant
```

* product type 
* enables the composition of computations that depend on a shared environment 
* (e -> a)
* Reader provides a method on it's instance that will take an environment called runWith that will run the instance with a given environment
* e must be the same type 
* a can be different type

```
const Reader = require('crocks/Reader')
const { ask } = Reader 

const concat = require('crocks/pointfree/concat')

// greet :: String -> Reader String String
const greet = greeting =>
  Reader(name => `${greeting}, ${name}`)

const flow = 
  greet('Hola')

flow.runWith('Thomas')
// => Hola, Thomas
```

## ask

* returns a Reader with the environment on the right portion of the Reader. 

```
Reader.ask :: () -> Reader e e 
Reader.ask :: (e -> b) -> Reader e b
```

```
const Reader = require('crocks/Reader')
const { ask } = Reader 

const add = 
  x => y => x + y

// Typical constructor
Reader(add(10))
  .runWith(56)
// 66

// Using 'ask' with no function 
// (identity on environment)
ask()
  .runWith(56)
// 56

// Using 'ask' with a function
// map environment before deposit
ask(add(10))
  .runWith(56)
// 66
```

## of 

* constructor for the right portion populated with it's argument

```
Reader.of(34)
  .map(add(6))
  .runWith()
```

# Example Use
***link***: https://gist.github.com/dypsilon/883e878ca1c05a7c355e41fb28a2f3e3

```
const Reader = require('fantasy-readers')
const { map, chain, evolve, pipe } = require('ramda')
const crypto = require('crypto')

// our mock database
const database = [
  { email: 'user@example.org', password: 'e0538fd8f022bb3b139d72cf12766cb0e31690ff' },
  { email: 'admin@example.org', password: '42c4fbf6fec201c66b82c97833b08d936d2cd526' }
]

// creates a statefull database connection
const connectTo = (db) => {
  return {
    insert: (doc) => db.push(doc),
    get: (i) => db[i],
    delete: (i) => db.splice(i, 1),
    list: () => db
  }
}

// some utility functions
const encrypt = (i) => crypto.createHash('sha1').update(i).digest('hex');
const encPassword = evolve({password: encrypt})
const getInput = () => ({ email: 'new@example.org', password: 'secret' });

// this is how you access the db connection inside the reader
const save = (user) => {
  return Reader.ask.map((db) => {
    db.insert(user);
    return db.list();
  });
}

// the body of the program
const prog = pipe(
  Reader.of,
  map(encPassword),
  chain(save)
);

// this is our db connection now
const dbCon = connectTo(database);

// this is how you pass the db connection in
const result = prog(getInput()).run(dbCon);

/* 
  dbCon = {
    insert: (doc) => db.push(doc),
    get: (i) => db[i],
    delete: (i) => db.splice(i, 1),
    list: () => db
  }

environment containing all the functionalities of the db 
*/

/* 
const prog = 

1. Reader({ email: 'new@example.org', password: 'secret' })
2. Reader({ email: 'new@example.org', password: encrypt('secret') })
3. save(({ email: 'new@example.org', password: encrypt('secret') }))
*/

/*
  save(({ email: 'new@example.org', password: encrypt('secret') })) 
  Reader.ask.map() return the environment and map over it, note that we are moving the environment to the right side as we cannot directly change the environment
  ....  call the methods inside the environment given our database
*/
```

Brian Lonsdorf Example
https://vimeo.com/105300347

```
              'ELLO'
const append = str => (3)
  ask.map(env =>   <- govna is now the RHS (4)
    concat(str, env) <- ELLO govna (5)
  )

const greeting =
  compose(
    chain(append),
    map(toUpperCase), <- 'ELLO' (2)
    Reader.of <- RHS is 'ello' (1)
  )

greeting('ello').run('govna')
'ELLO govna'
```

```
  {body: 'nothingness is somethingness bro'}
const setAuthor = attrs => 
  ask.map(user => <- {email: 'alanwatts@gmail.com', name: 'Alan'}
    _.assign(attrs, {written_by: user.name})
  )

const writePost =
  compose(
    map(publish),
    chain(setAuthor), 
    Reader.of <- {body: 'nothingness is somethingness bro'}
  )

const result = writePost({body: 'nothingness is somethingness bro'})
result.run({email: 'alanwatts@gmail.com', name: 'Alan'})
```

```
const Reader = require('fantasy-readers')
const ask = Reader.ask
const _ = require('lodash')
const database = { 
  insert: attrs => _.assign(attrs, {id: 1})
  }

const save = curry((db, attrs) =>
  db.insert(attrs)

const fixAttributes = attrs =>
  _.omit(attrs, 'admin')

const create = 
  compose(
    JSON.stringify,
    save(database),
    fixAttributes
  )

const result = create({ name: 'Kim', email: 'kim@test.com.', admin: true })
```
* the following needs to be refactored as it passes around a global database

```
const Reader = require('fantasy-readers')
const ask = Reader.ask
const _ = require('lodash')
const database = { 
  insert: attrs => _.assign(attrs, {id: 1})
  }

const save = attrs =>
  ask.map(
    db => 
      db.insert(attrs)
  )

const fixAttributes = attrs =>
  _.omit(attrs, 'admin')

const create = 
  compose(
    map(JSON.stringify),
    chain(save),
    map(fixAttributes),
    Reader.of
  )

const result = create({ name: 'Kim', email: 'kim@test.com.', admin: true })
result.run(database)
```