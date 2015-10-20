# Express-bunyan-logger

A express logger middleware powered by [bunyan](https://github.com/trentm/node-bunyan).

This module is based on https://github.com/villadora/express-bunyan-logger with my own additions.


## Installation

    npm install <github-repo-url>
   
## Usage

To use the logger: 

    app.use(require('express-bunyan-logger')());

To use the errorLogger:

    app.use(require('express-bunyan-logger').errorLogger());

And you can also pass bunyan logger options to the logger middleware:

    app.use(require('express-bunyan-logger')({
        name: 'logger', 
        streams: [{
            level: 'info',
            stream: process.stdout
            }]
        }));

Change default format:

    app.use(require('express-bunyan-logger')({
        format: ":remote-address - :user-agent[major] custom logger"
    });

And a child logger will be attached to each request object:

```javascript
app.use(require('express-bunyan-logger')();
app.use(function(req, res, next) {
    req.log.debug('this is debug in middleware');
    next();
});
```

## Configuration

### options.format

Format string, please go the source code to the metadata. ":name" will print out meta.name; ":name[key]" will print out the property 'key' of meta.name.

Or you can pass a function to _options.format_. This function accept a object as argument and return string.

### options.parseUA

Whether to parse _user-agent_ in logger, default is =true=.

### options.levelFn

Function that translate statusCode into log level.

```
function(status, err /* only will work in error logger */) {
     // return string of level
     return "info";
}
```

### options.includesFn

Function that is passed `req` and `res`, and returns an object whose properties will be added to the meta object passed to bunyan

```javascript
function(req, res) {
    if (req.user) {
        return {
            _id: req.user._id,
            name: req.user.name
        }
    }
}
```

### options.excludes

Array of string, Those fields will be excluded from meta object which passed to bunyan

### options.serializers

An object of [bunyan serializers](https://github.com/trentm/node-bunyan#serializers). They are passed on to bunyan.
The default serializers are defined as follows:
```
{
    req: bunyan.stdSerializers.req,
    res: bunyan.stdSerializers.res,
    err: bunyan.stdSerializers.err
}
```

### options.immediate

Write log line on request instead of response (for response times)

### options.genReqId

By default, `express-bunyan-logger` will generate an unique id for each request, and a field 'req_id' will be added to child logger in `request` object.

If you have already use other middleware/framework to generate request id, you can pass a function to retrieve it:

```javascript
// suppose connect-requestid middleware is already added.
app.use(require('express-bunyan-logger')({
    genReqId: function(req) {
       return req.id;
    }
});
```


## License

(The BSD License)

    Copyright (c) 2013, Villa.Gao <jky239@gmail.com>;
