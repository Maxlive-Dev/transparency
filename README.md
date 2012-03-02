Transparency is a minimal template engine for browser. It maps JSON objects to DOM elements with zero configuration.

## Features

* Data binding by convention - No extra markup in the views
* Collection rendering - No loops and partials
* Nested objects and collections - No configuration, just conventions
* Directives - No custom DSL, just functions
* Template caching - No manual template lookup/compilation/rendering
* Performant - In most real-world cases, it's faster than any other template engine or hand-crafted bingings (*)
* Compatible - Tested on IE6+, Chrome and Firefox.

More details about design principles, performance measurements and specific use cases is available at
[FAQ](https://github.com/leonidas/transparency/blob/master/FAQ.md)

(*) Take this with grain of salt, as "real-world performance" isn't that easy to measure and Transparency is
smart enough to avoid any extra work (this actually boosts your "real world performance", a lot!). One easy-to-run
performance test is available at [jsperf.com](http://jsperf.com/dom-vs-innerhtml-based-templating/366). 
See others at the `browser` folder.

## Try it

See live examples and play around at http://leonidas.github.com/transparency/

## Use it

Get the [compiled and minified version](https://raw.github.com/leonidas/transparency/master/lib/transparency.min.js)
and include it to your application. jQuery is optional, but if you happen to use it, Transparency registers itself 
as a plugin.

```html
<script src="js/jquery-1.7.1.min.js"></script>
<script src="js/transparency.min.js"></script>
```

For server-side use, see `spec` folder and awesome [jsdom](https://github.com/tmpvar/jsdom) for the details.

## Examples

Here's some of examples. For further details, please see `examples` folder, unit tests and the source code.

### Assigning values

Transparency binds JavaScript objects to DOM a element by `id`, `class`,`element name`, `name` attribute and 
`data-bind`[HTML5 data attribute](http://www.w3.org/TR/html5/elements.html#embedding-custom-non-visible-data-with-the-data-attributes)
.

Values are escaped before rendering.

Template:

```html
<div id="container">
  <div id="hello"></div>
  <div class="goodbye"></div>
  <span></span>
  <input type="text" name="greeting" />
  <button class="hi-button" data-bind="hi-label"></button>
</div>
```

Javascript:

```js
var hello = {
  hello:      'Hello',
  goodbye:    'Goodbye!',
  span:       '<i>See Ya!</i>',
  greeting:   'Howdy!',
  'hi-label': 'Terve!' // Finnish i18n
};

// with jQuery
$('#container').render(hello);

// ..or without
Transparency.render(document.getElementById('container'), hello);
```

Result:

```html
<div class="container">
  <div id="hello">Hello</div>
  <div class="goodbye">Goodbye!</div>
  <span>lt;i&gt;See Ya!&lt;/i&gt;</span>
  <input type="text" name="greeting" value="Howdy!" />
  <button class="hi-button" data-bind="hi-label">Terve!</button>
</div>
```

### Iterating over a list

Template:

```html
<ul id="activities">
  <li class="activity"></li>
</ul>
```

Javascript:

```js
var activities = [
  {activity: 'Jogging'},
  {activity: 'Gym'},
  {activity: 'Sky Diving'},
];

$('#activities').render(activities);

// or
Transparency.render(document.getElementById('activities'), activities);
```

Result:

```html
<ul id="activities">
  <li class="activity">Jogging</li>
  <li class="activity">Gym</li>
  <li class="activity">Sky Diving</li>
</ul>
```

### Iterating over a list with simple values

Template:

```html
<div>
  <div class="comments">
    <span></span>
  </div>
</div>
```

Javascript:

```js
var comments = ["That rules", "Great post!"]

$('.comments').render(comments);
```

Result:

```html
<div>
  <div class="comments">
    <span>That rules</span>
    <span>Great post!</span>
  </div>
</div>
```

### Iterating over a list with simple values, using `listElement` class

Template:

```html
<div>
  <div class="comments">
    <label>comment</label><span class="listElement"></span>
  </div>
</div>
```

Javascript:

```js
var comments = ["That rules", "Great post!"]

$('.comments').render(comments);
```

Result:

```html
<div>
  <div class="comments">
    <label>comment</label><span class="listElement">That rules</span>
    <label>comment</label><span class="listElement">Great post!</span>
  </div>
</div>
```

### Nested lists

Template:

```html
<div class="container">
  <h1 class="title"></h1>
  <p class="post"></p>
  <div class="comments">
    <div class="comment">
      <span class="name"></span>
      <span class="text"></span>
    </div>
  </div>
</div>
```

Javascript:

```js
var post = {
  title:    'Hello World',
  post:     'Hi there it is me',
  comments: [ {
      name: 'John',
      text: 'That rules'
    }, {
      name: 'Arnold',
      text: 'Great post!'
    }
  ]
};

$('.container').render(post);
```

Result:

```html
<div class="container">
  <h1 class="title">Hello World</h1>
  <p class="post">Hi there it is me</p>
  <div class="comments">
    <div class="comment">
      <span class="name">John</span>
      <span class="text">That rules</span>
    </div>
    <div class="comment">
      <span class="name">Arnold</span>
      <span class="text">Great post!</span>
    </div>
  </div>
</div>
```

### Nested objects

Template:

```html
<div class="person">
  <div class="firstname"></div>
  <div class="lastname"></div>
  <div class="address">
    <div class="street"></div>
    <div class="zip"><span class="city"></span></div>
  </div>
</div>
```

Javascript:

```js
var person = {
  firstname: 'John',
  lastname:  'Wayne',
  address: {
    street: '4th Street',
    city:   'San Francisco',
    zip:    '94199'
  }
};

$('.person').render(person);
```

Result:

```html
<div class="container">
  <div class="firstname">John</div>
  <div class="lastname">Wayne</div>
  <div class="address">
    <div class="street">4th Street</div>
    <div class="zip">94199<span class="city">San Francisco</span></div>
  </div>
</div>
```

### Directives

Directives are used for calculated values and setting element attributes. In addition to having an access to the 
current data object through `this`, directives also have access to the current element as a parameter, 
which makes it easy to, e.g., selectively hide it.

Note: Directives are about to change for more consistent design and functionality. Syntax for attribute
assignment will change:
https://github.com/leonidas/transparency/issues/26

Template:

```html
<div class="person">
  <span class="name"></span>
  <a class="email"></a>
</div>
```

Javascript:

```js
person = {
  firstname: 'Jasmine',
  lastname:  'Taylor',
  email:     'jasmine.tailor@example.com'
};

directives =
  name:         function(element) { return this.firstname + " " + this.lastname; }
  'email@href': function(element) { return "mailto:" + this.email; }
};

$('.person').render(person, directives);
```

Result:

```html
<div class="person">
  <span class="name">Jasmine Taylor</span>
  <a class="email" href="mailto:jasmine.tailor@example.com">jasmine.tailor@example.com</a>
</div>
```

### Nested directives

Template:

```html
<div class="person">
  <span class="name"></span>
  <span class="email"></span>
  <div class="friends">
    <div class="friend">
      <span class="name"></span>
      <span class="email"></span>
    </div>
  </div>
</div>
```

Javascript:

```js
person = {
  firstname:  'Jasmine',
  lastname:   'Taylor',
  email:      'jasmine.taylor@example.com',
  friends:    [ {
      firstname: 'John',
      lastname:  'Mayer',
      email:     'john.mayer@example.com'
    }, {
      firstname: 'Damien',
      lastname:  'Rice',
      email:     'damien.rice@example.com'
    }
  ]
};

nameDecorator = function() { return this.firstname + " " + this.lastname };

directives = {
  name: nameDecorator,
  friends: {
    name: nameDecorator
  }
};

$('.person').render(person, directives);
```

Result:

```html
<div class="person">
  <span class="name">Jasmine Taylor</span>
  <span class="email">jasmine.taylor@example.com</span>
  <div class="friends">
    <div class="friend">
      <span class="name">John Mayer</span>
      <span class="email">john.mayer@example.com</span>
    </div>
    <div class="friend">
      <span class="name">Damien Rice</span>
      <span class="email">damien.rice@example.com</span>
    </div>
  </div>
</div>
```

## Development instructions

You'll need node.js 0.6.x and npm.

Install uglify-js and coffee-script:

    npm install -g uglify-js
    npm install -g coffee-script

Run tests

    npm install && npm test

Run tests during development for more verbose assertion output

    node_modules/jasmine-node/bin/jasmine-node --coffee --verbose spec

Generate Javascript libs

    cake build

## Contributing

All the following are appreciated, in an asceding order of preference

1. A feature request or a bug report
2. Pull request with a failing unit test
3. Pull request with unit tests and corresponding implementation

In case the contribution is going to change Transparency API, please create a ticket first in order to discuss and
agree on design.

There's [an article](https://github.com/leonidas/codeblog/blob/master/2012/2012-01-13-implementing-semantic-anti-templating-with-jquery.md)
regarding the original design and implementation. It might be worth reading as an introduction.

## Philosophy

Transparency is heavily influenced by [PURE](http://beebole.com/pure/) but is even more opinionated about how
templates and data bind together. Templating with Transparency is unobustrive, dead simple and just stays out of the way.

Transparency relies on convention over configuration and requires you to have 1:1 match between CSS classes and
JSON objects. The idea is to minimize the cognitive noise you have to deal with.
Just call `$('.container').render(data)` and move on.

