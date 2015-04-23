#webpack-compile-templates

Webpack version of [this](https://github.com/robrichard/browserify-compile-templates).

Compiles [underscore](http://underscorejs.org/#template) templates from HTML script tags into CommonJS in a webpack loader. You can add multiple script tags to  each file. Requiring the file will return an object with a property for each script tag. The ID attribute is the key and the compiled template function as the value.

See the [underscore documentation](http://underscorejs.org/#template) for more details.


# Usage

## Install

```
npm install --save-dev webpack-compile-templates
```

## Create a template file
myTemplates.html

Use the ID attribute to identify the template from your JS source.

```html
<script type="text/template" id="template1">
	<h2><%- title %></h2>
</script>

<script type="text/template" id="template2">
	<li><%- name %> <<%- email %>></li>
</script>
```

## Require the template file
A JS file

```javascript
var $ = require('jquery');
var templates = require('/path/to/myTemplates');

$('.container').html(templates.template1({ title: 'My Page Title' }));
$('.container').append(templates.template2({
	name: 'Rob',
	email 'rob@example.com'
}));
```

## Add to webpack
Register the template and tell webpack to look for html extensions
```
 loaders: [
            { test: /\.html$/, loader: 'webpack-compile-templates' },
```

# Why?
The advantage of this transform over other transforms or plugins is that the templates are backwards compatible with non-CommonJS code. Template files formatted this way can also be included directly in HTML. A UMD module that is shared in both a CommonJS and non-commonJS codebase can be used like this:

```javascript
(function (root, factory) {
    if (typeof module !== 'undefined') {
        // CommonJS - templates are precompiled and bundled in with JS
        var templates = require('/path/to/myTemplates');
        factory(
            module,
            templates.template1,
            templates.tempalte2
        );
    } else {
        // Browser globals. Templates are included in html and need to be compiled client-side
        var $template1 = $('#template1');
        var $tempalte2 = $('#template2');
        factory(
            _module,
            _.template($template1.html(), null),
            _.template($template2.html(), null)
        );
    }
}(window || global, function (module, template1, template2) {
// Use compliled templates in here
}));
```

The commonJS environment gets the benefit of the precompiled template. Other environments can still include the file on the page and access it by ID.
