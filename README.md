# MongoDb / Mongoose pagination done right

An utility function to work with ranged pagination, way faster than using `skip()`.

## Install

    $ npm install mongoose-paginate

## Example

```js
var paginate = require('mongoose-paginate')

...

function getQuery() {
  return Posts.find()
    .where({ tags: { $in ['foo', 'bar'] } })
}

paginate(getQuery(), {
  sort: '-timestamp',
  limit: 50
}, function (err, page1) {
  // page1 ready

  paginate(getQuery(), {
    sort: '-timestamp',
    startId: page1._id,
    startKey: page1.timestamp,
    limit: 50
  }, function (err, page2) {
    // page2 ready
  })
})
```

## KoaJS Example

```js

var paginate = require('mongoose-paginate')

...

router.get('/list', function *() {
  var query = Posts.find()
    .where({ tags: { $in ['koajs', 'yield'] } })
    .where({ published: true })
    .where({ author: 'Kilian' })

  // force sorting, newest first
  this.query.sort = '-timestamp'

  // pass limit, startId and startKey on query string
  paginate(query, this.query)

  this.type = 'json'
  this.body = query.lean().stream().pipe(stringify())
})
```

## Signature

```js
paginate(query, opts)
```

### opts

* **sort** (`String`) mongoose sort criteria, supports ASC and DESC with the `"-"` prefix.
* **limit** (`Number`: defaults to 10) limits the number of documents.
* **startId** (`String`) id of the last document of your last result set, next result set will start with the document next to this.
* **startKey** (`String`) the value of the `sort` field of the last document of your result set, next result set will start with the document next to this.

## Test suite

    $ npm test

## License

_This software is released under the MIT license cited below_.

    Copyright (c) 2014 Kilian Ciuffolo, me@nailik.org. All Rights Reserved.

    Permission is hereby granted, free of charge, to any person
    obtaining a copy of this software and associated documentation
    files (the 'Software'), to deal in the Software without
    restriction, including without limitation the rights to use,
    copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the
    Software is furnished to do so, subject to the following
    conditions:

    The above copyright notice and this permission notice shall be
    included in all copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
    EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
    OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
    NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
    HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
    WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
    FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
    OTHER DEALINGS IN THE SOFTWARE.