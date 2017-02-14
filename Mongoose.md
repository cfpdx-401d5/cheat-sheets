Mongoose
===

Mongoose sits ontop of the `mongodb` node.js driver and 
introduces model semantics (enforces data expectations).

## Initialize and Connect

Currently, it's a good practice to use the native Promise library via `mongoose.Promise = Promise;`

Connect using the `mongoose.connect(db_uri)` method. 

Format for `db_uri` is `mongodb://localhost:27017/mythical-animals` where:

* protocol: `mongodb`
* server: `localhost` (for dev)
* port: `27107` (default)
* database name: `mythical-animals`

After connecting, the connection is available via `mongoose.connection` which supports
events including `connected` and `error`. You can explicitly close the connection using
`mongoose.connection.close()`

## Schemas and Models

### Schema

Define data using `mongoose.Schema`:

```js
const schema = new Schema({
    name: {
        type: String,
        required: true
    },
    type: {
        type: String,
        enum: ['dog', 'cat', 'bird', 'lizard']
    }
});
```

The most common validations are `required`, `enum` (limit to set of values), 
`min` and `max` type settings. You can also define custom validation functions:

```js
legs: {
        type: Number,
        // ultimate custom validation, write your own function!
        validate: {
            validator(value) {
                return value > 0 && value < 7;
            },
            message: '{VALUE} is the ___wrong__ number of legs for a pet!'
        }
    }
```

Include general schema options as the second parameter:

```js
const schema = new Schema({
    // fields go here
}, {
    // options go here:
    timestamps: true
});
```

### Models

While the schema _defines_ the data, creating a model from that schema enables us to have an
exportable object that can be used to work with that type of data:

```js
const Pet = mongoose.model('Pet', schema);
module.exports = Pet;
```

The string name passed as the first parameter is critical as it is used:

1. To define the collection name used in mongodb
1. To refer to this model from other schemas when defining model definitions
