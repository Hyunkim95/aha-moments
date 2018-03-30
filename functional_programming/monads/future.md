# Future

```
const futureLast =
  compose(
    map(first),
    map(reverse),
    Future.of
  )

futureLast([3,2,1]).fork(errFn, console.log)
```

```
new Future(function(res, rej) {})
```

```
const readFile = curry(
  (rej, res, fileName) => {
    fs.readFile('some_file', 'utf-8', (err,data) {
      err 
        ? rej(err)
        : res(data)
    })
  }
)

const readFile = fileName =>
  new Future((rej, res) => {
    fs.readFile('some_file', 'utf-8', (err,data) {
      err 
        ? rej(err)
        : res(data)
  })
```

```
const getJSON = curry(
  (rej, res, url, params) =>
    $.getJSON(url, params, res).fail(rej)
)

const getJSON = (url, params) =>
  new Future(rej, res) => {
    $.getJSON(url, params, res).fail(rej)
  }
```

```
const posts =
  compose(
    map(sortBy('date')),
    getJSON('/posts')
  )

posts({}).fork(logError, renderView)
```

```
const fiveLines =
  compose(
    join('\n'),
    take(5),
    split('\n')
  )

const previewFiles = 
  compose(
    map(append('...')),
    map(fiveLines),
    readFile
  )

previewFile('infinite_jest.epub').fork(logError, printLines)
```

```
const makePage = curry(
  (posts, tags) => { someFn }
)

const app = 
  liftA2(
    makePage, 
    getJSON('/posts', {id: 1}),
    getJSON('/tags', {id: 1}),
  )

app.fork(log, $('#main').html)
```

```
const getPost = getJSON('/posts')

map(getPost, [{id: 1}, {id: 2}])
// [Future(Post), Future(Post)]

traverse(
  getPost, 
  Future.of, 
  [{id: 1}, {id: 2}]
  )
// [Future(Post, Post)]
```

```
// post :: String -> Future(JSON)

// analyze :: String -> Future(JSON)
const analyze = 
  compose(
    chain(post('/analytics')),
    readFile
  )

analyze('log').fork(errFn, successFn)
```

```
// findUser :: Number -> Future(User)
// getFriends :: User -> Future([User])
// renderTemplates :: [User] -> Future(String)

// friendPage :: Number -> Future(String)
const friendPage =
  compose(
    chain(renderTemplate),
    chain(getFriends),
    findUser
  )

friendPage(2).fork(errFn, successFn)
```

```
const fs = require('fs')
const Future = require('data.future')
const _ = require('lodash')

const readFile = fileName =>
  new Future((rej, res) => {
    fs.readFile(fileName, 'utf-8', (err,data) {
      err 
        ? rej(err)
        : res(data)
  })

const getDir = path =>
  new Future((rej, res) => {
    fs.readDir(path, (err,files) {
      err 
        ? rej(err)
        : res(files)    
  })

const result = 
  compose(
    map(_.get(0)),
    getDir
  )

const readIt = 
  compose(
    readFile,
    _.get(0)
  )

const result2 = 
  compose(
    chain(readIt),
    getDir
  )

const readThem = 
  traverse(
    readFile,
    Future.of
  )

const result3 = 
  compose(
    chain(readThem),
    map(
      filter(
        match(/\.js$/)
      )
    ),
    getDir
  )

const result4 = 
  liftA2(
    concat,
    readFile('const.js'),
    readFile('future.js')
  )

result('.').fork(console.log, console.log)
result2('.').fork(console.log, console.log)
result3('.').fork(console.log, console.log)
result4.fork(console.log, console.log)
```