# Joiless [![Build Status](https://travis-ci.org/zackehh/joiless.svg?branch=master)](https://travis-ci.org/zackehh/joiless)

Utility functions for using Joi without using Joi.

### Installation

Joiless lives on npm, so just install it via the command line and you're good to go.

```
$ npm install --save joiless
```

### Example

I love [Joi](https://github.com/hapijs/joi)'s validation, and I love [Hapi](https://github.com/hapijs/hapi) so I use Joi all the time. Unfortunately I hate Joi's chaining, so I wrote Joiless to assist. Basically, it's Joi using JSON.

Here's an example (both of these are identical, and are in fact a test case in this repo):

```javascript
var Joi = require('joi');
var Joiless = require('joiless');

// Joi version
var joiSpec = Joi
  .date()
  .description('The date (in milliseconds) that this crash last appeared')
  .timestamp('javascript')
  .default(Date.now, 'Current timestamp generation');

// Joiless version
var joilessSpec = Joiless.spec('date', {
  description: 'The date (in milliseconds) that this crash last appeared',
  timestamp: 'javascript',
  default: [ Date.now, 'Current timestamp generation' ]
});
```

The translation is simple; the key is the function called on the Joi chain, and the value is the value passed into the Joi chain call.

- If you pass an Array as a value, it's applied to the Joi function - if you wish to pass an Array as an argument, use `[ [ <your_array> ] ]`.
- If you want to call a function with no arguments, either pass `undefined` as the value, or you can use the `after` hook (whichever you prefer):

```javascript
var Joiless = require('joiless');

// via undefined
Joiless.spec('string', {
  after: function (spec) {
    spec.strip();
    spec.insensitive();
  }
});

// via after
Joiless.spec('string', {
  strip: undefined,
  insensitive: undefined
})
```

### Other Usage

If you make a complex schema, you can use `attach` to hook the child keys onto the parent.

For example, in the snippet below you can reference `Record.Username` to pull back the username spec. This is done using `get`, it doesn't store the value twice.

```javascript
var Joiless = require('joiless');

// define the spec
var Record = Joiless.spec({

  type: 'object',

  description: 'A database model',

  keys: {

    username: Joiless.spec({
      type: 'string',
      description: 'The username for this record',
      required: true
    })

  }

});

// attach the children
Joiless.attach(Record);
```
