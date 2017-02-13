Express
===

## Creating an app

Express app is created by calling the "required" express function:

```js
const app = require('express')();
```

Add middlewares and routers using `app.use` with optional mounting path:

```js
app.use(morgan('dev'));
app.use(cors());
app.use(express.static(publicDir));

const widgets = require('./lib/routes/widgets');
app.use('/api/widgets', widgets);
```

While there is a convience `.listen` method on `app`, we usually export `app` so we
can manage testing and running the server differently. 

`app` is an http `requestListener` function and can be passed to `http.createServer`.

## Starting the server

Call `.listen(port)` on the httpServer.

## Creating a router

An Express router is created by calling the `express.Router` function:

```
const router = require('express').Router();
```

Specific methods can then be added to the router, which will be exported and added to the `app`:

```
router
    .get('/', (req, res, next) => {

    })
    .post('/', bodyParser, (req, res, next) => {

    });

module.exports = router;
```

## Middleware

Common middleware to use from `npm`:

* `express.static` - server a folder's contents as files
* `body-parser` - add to parse JSON to `req.body`
* `morgan` - http route logging
* `cors` - CORS support for running app and server on different hosts
* `ensureAuth` - middleware you write to check that user has a token indicating they are authenticated

Middleware is typically "required" and then called (including any options) before adding to `app.use()`.
Either call immediate when requiring:

```js
const cors = require('cors')();
const bodyParser = require('body-parser').json();
const morgan = require('morgan')('dev');
const ensureAuth = require('./auth/ensureAuth')();

app.use(cors);
app.use(bodyParser);
app.use(morgan);
app.use('/api/widgets', ensureAuth, widgets);
```

or call when passing to `app.use`:

```js
const cors = require('cors');
const bodyParser = require('body-parser');
const morgan = require('morgan');
const ensureAuth = require('./auth/ensureAuth');

app.use(cors());
app.use(bodyParser.json());
app.use(morgan('dev'));
app.use('/api/widgets', ensureAuth(), widgets);
```

Try and resuse middleware instances within a module when it makes sense:

```js
const ensureAuth = require('./auth/ensureAuth')();

app.use('/api/widgets', ensureAuth, widgets);
app.use('/api/products', ensureAuth, products);
app.use('/api/stores', ensureAuth, stores);
```

Use a more selective approach if possible when applying middleware. 
For example, add `bodyParser` to `post` routes instead of all routes.

## Route and Query Parameters

Url placeholder parametes will be automatically added to the `req.params` property.
Url query parameters will be automatically added to the `req.query` property.

```js

// /api/widgets?type=sprocket&metal=aluminum
.get('/', (req, res, next) => {
    const type = req.query.type; //"sprocket"
    const metal = req.query.metal; //"aluminum"
    // ...
})

.get('/:id', (req, res, next) => {
    const id = req.params.id;
    // ...
});
```

## Handling Errors

If your routes could have failures (usually always), you need to include `next` and use 
it to route errors to the error handler:

```js
.get('/', (req, res, next) => {
    MyModel.find()
        .then(models => res.send(models))
        .catch(next);
});
```

Servers that primarily deal in data (JSON requests) should return proper status code 
(400 level codes deal with requestor issues, 500 level codes deal with server errors)
with JSON error messages as the response body (not text!).


