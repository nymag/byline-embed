# multiplex-templates
Embed components in other components!

[![Build Status](https://travis-ci.org/nymag/multiplex-templates.svg)](https://travis-ci.org/nymag/multiplex-templates)
[![Code Climate](https://codeclimate.com/github/nymag/multiplex-templates/badges/gpa.svg)](https://codeclimate.com/github/nymag/multiplex-templates)

## Install

```
npm install --save multiplex-templates
```

## Usage

### render a component

```js
var multiplex = require('multiplex-templates')();

multiplex.render('component-name', data);
```

This will render a component. It will look in `components/[name]/template.[extension]`. 

e.g.

```
components/entry/template.jade
components/coolparagraph/template.nunjucks
```

You can also pass `'component'` explicitly as the third arg.

### render a layout

```js
multiplex.render('component-name', data, 'layout');
```

This will look in your root-level layouts folder, e.g.

```
layouts/index/template.nunjucks
layouts/archive/template.jade
```

### Engines

Currently we support these engines:

* nunjucks
* jade
* mustache (coming soon!)
* es6 template literals (coming soon!)

This module exposes the instances of the templating engines, so you can add mixins/filters/globals/etc onto them:

```js
var env = multiplex.engines.nunjucks;

env.addGlobal('key', 'value');
```

You can also instantiate your own engines (and configure them however you like) and pass them into multiplex-templates.

```js
var env = require('nunjucks').configure('.', { watch: false }),
  jadeEnv = require('jade'), // so cool, doesn't need config (⌐■_■)
  multiplex = require('multiplex-templates')({
    nunjucks: env,
    jade: jadeEnv
  });

// multiplex.engines.nunjucks === env
```

## Cross-engine Embedding

### Nunjucks

To embed a template, use an `embed` filter in the parent template. If you configured your nunjucks environment with `autoescape: true` _[(highly recommended!)](http://wonko.com/post/html-escaping)_, remember to pass the resulting html through `|safe` so it's not escaped.

```
{{ data | embed('name') | safe }}
```

The `data` you pass in is then used to render the child template. You can optionally pass in additional data:

```
{{ data | embed('name', defaults) | safe }}
```

Properties in the `data` object will overwrite properties of the same name in the `defaults` object, as this uses lodash's fast `_.defaults()` method.

### Jade

Jade embedding is very similar. Multiplex-templates adds an `embed` function to the options passed into any jade template, which takes three arguments, `data`, `name`, and `defaults`. The logic is the same as the nunjucks filter.

```jade
section#foo
  p.embedded
    != embed(data, 'name')
```

## Tests

```
npm test
```