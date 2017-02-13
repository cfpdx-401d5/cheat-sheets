Express
===

## Creating an app

Express app is created by calling the exported express function:

```
const app = require('express')();
```

`app` is an http `requestListener` function and can be passed to `http.createServer`.

Add middleware and router using `app.use` with optional mounting path:

```
app.use(morgan('dev'));
app.use(cors());
app.use(express.static(publicDir));

const widgets = require('./lib/routes/widgets');
app.use('/api/widgets', widgets);

## Starting the server

Call `.listen(port)` on the httpServer.

## Creating a router

Express router is created be calling the `express.Router` function:

```
const router = require('express').Router();
```
