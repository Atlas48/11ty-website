---
eleventyNavigation:
  parent: Template Languages
  key: Nunjucks
  order: 5
relatedKey: nunjucks
relatedTitle: Template Language—Nunjucks
tags:
  - related-filters
  - related-shortcodes
  - related-custom-tags
layout: layouts/langs.njk
---
{% tableofcontents "open" %}

| Eleventy Short Name | File Extension | npm Package                                       |
| ------------------- | -------------- | ------------------------------------------------- |
| `njk`               | `.njk`         | [`nunjucks`](https://mozilla.github.io/nunjucks/) |

You can override a `.njk` file’s template engine. Read more at [Changing a Template’s Rendering Engine](/docs/languages/).

## Nunjucks Environment Options

We use [Nunjucks defaults for all environment options](https://mozilla.github.io/nunjucks/api.html#configure) (shown in the `configure` section of the Nunjucks docs).

### Optional: Use your Nunjucks Environment Options {% addedin "1.0.0" %}

It’s recommended to use the Configuration API to override the default Nunjucks options.

```js
module.exports = function(eleventyConfig) {
  eleventyConfig.setNunjucksEnvironmentOptions({
    throwOnUndefined: true,
    autoescape: false, // warning: don’t do this!
  });
};
```

### Advanced: Use your Nunjucks Environment {% addedin "0.3.0" %}

While it is preferred and simpler to use the Options-specific API method above (new in Eleventy 1.0!)—as an escape mechanism for advanced usage you may pass in your own instance of a [Nunjucks Environment](https://mozilla.github.io/nunjucks/api.html#environment) using the Configuration API.

{% callout "warn" %}Not compatible with <code>setNunjucksEnvironmentOptions</code> above—this method will <em>override</em> any configuration set there.{% endcallout %}

```js
let Nunjucks = require("nunjucks");

module.exports = function(eleventyConfig) {
  let nunjucksEnvironment = new Nunjucks.Environment(
    new Nunjucks.FileSystemLoader("_includes")
  );

  eleventyConfig.setLibrary("njk", nunjucksEnvironment);
};
```

## Supported Features

| Feature                                                                      | Syntax                                                                    |
| ---------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| ✅ Includes                                                                  | `{% raw %}{% include 'included.njk' %}{% endraw %}` looks in `_includes/included.njk`. Filenames must be in quotes. Does not process front matter in the include file.         |
| ✅ Includes (Relative Path) {% addedin "0.9.0" %}                                                                   | Relative paths use `./` (template’s directory) or `../` (template’s parent directory).<br><br>Example: `{% raw %}{% include './included.njk' %}{% endraw %}` looks for `included.njk` in the template’s current directory. Does not process front matter in the include file.         |
| ✅ Extends                                                                   | `{% raw %}{% extends 'base.njk' %}{% endraw %}` looks in `_includes/base.njk`. Does not process front matter in the include file.                  |
| ✅ Extends (Relative Path) {% addedin "0.9.0" %}                                                                   | Relative paths use `./` (template’s directory) or `../` (template’s parent directory)<br><br>Example: `{% raw %}{% extends './base.njk' %}{% endraw %}` looks for `base.njk` in the template’s current directory. Does not process front matter in the include file.                  |
| ✅ Imports                                                                   | `{% raw %}{% import 'macros.njk' %}{% endraw %}` looks in `_includes/macros.njk`. Does not process front matter in the include file.               |
| ✅ Imports (Relative Path) {% addedin "0.9.0" %}                                                                   | Relative paths use `./` (template’s directory) or `../` (template’s parent directory):<br>`{% raw %}{% import './macros.njk' %}{% endraw %}` looks for `macros.njk` in the template’s current directory. Does not process front matter in the include file.               |
| ✅ Filters                                                                   | `{% raw %}{% name \| filterName %}{% endraw %}` Read more about [Filters](/docs/filters/).                                |
| ✅ [Eleventy Universal Filters](/docs/filters/#universal-filters) | `{% raw %}{% name \| filterName %}{% endraw %}` Read more about [Filters](/docs/filters/). |
| ✅ [Custom Tags](/docs/custom-tags/) | `{% raw %}{% uppercase name %}{% endraw %}` Read more about [Custom Tags](/docs/custom-tags/). {% addedin "0.5.0" %}|
| ✅ [Shortcodes](/docs/shortcodes/) | `{% raw %}{% uppercase name %}{% endraw %}` Read more about [Shortcodes](/docs/shortcodes/). {% addedin "0.5.0" %}|

## Filters

Filters are used to transform or modify content. You can add Nunjucks specific filters, but you probably want to add a [Universal filter](/docs/filters/) instead.

Read more about [Nunjucks Filter syntax](https://mozilla.github.io/nunjucks/templating.html#filters).

```js
module.exports = function(eleventyConfig) {
  // Nunjucks Filter
  eleventyConfig.addNunjucksFilter("myNjkFilter", function(value) { … });

  // Nunjucks Asynchronous Filter (read on below)
  eleventyConfig.addNunjucksAsyncFilter("myAsyncNjkFilter", function(value, callback) { … });

  // Universal filters (Adds to Liquid, Nunjucks, and Handlebars)
  eleventyConfig.addFilter("myFilter", function(value) { … });
};
```

### Usage

{% raw %}
```html
<h1>{{ myVariable | myFilter }}</h1>
```
{% endraw %}

### Multiple Filter Arguments

```js
module.exports = function(eleventyConfig) {
  // Nunjucks Filter
  eleventyConfig.addNunjucksFilter("concatThreeStrings", function(arg1, arg2, arg3) {
    return arg1 + arg2 + arg3;
  });
};
```

{% raw %}
```html
<h1>{{ "first" | concatThreeThings("second", "third") }}</h1>
```
{% endraw %}

### Asynchronous Nunjucks Filters {% addedin "0.2.13" %}

By default, almost all templating engines are synchronous. Nunjucks supports some asynchronous behavior, like filters. Here’s how that works:

```js
module.exports = function(eleventyConfig) {
  eleventyConfig.addNunjucksAsyncFilter("myAsyncFilter", function(value, callback) {
    setTimeout(function() {
      callback(null, "My Result");
    }, 100);
  });
};
```

The last argument here is the callback function, the first argument of which is the error object and the second is the result data. Use this filter like you would any other: `{% raw %}{{ myValue | myAsyncFilter }}{% endraw %}`.

Here’s a Nunjucks example with 2 arguments:

```js
module.exports = function(eleventyConfig) {
  eleventyConfig.addNunjucksAsyncFilter("myAsyncFilter", function(value1, value2, callback) {
    setTimeout(function() {
      callback(null, "My Result");
    }, 100);
  });
};
```

Multi-argument filters in Nunjucks are called like this: `{% raw %}{{ myValue1 | myAsyncFilter(myValue2) }}{% endraw %}`.

## Shortcodes

Shortcodes are basically reusable bits of content. You can add Nunjucks specific shortcodes, but you probably want to add a [Universal shortcode](/docs/shortcodes/) instead.

### Single Shortcode

```js
module.exports = function(eleventyConfig) {
  // Nunjucks Shortcode
  eleventyConfig.addNunjucksShortcode("user", function(name, twitterUsername) { … });

  // Universal Shortcodes (Adds to Liquid, Nunjucks, Handlebars)
  eleventyConfig.addShortcode("user", function(name, twitterUsername) {
    return `<div class="user">
<div class="user_name">${name}</div>
<div class="user_twitter">@${twitterUsername}</div>
</div>`;
  });
};
```

#### Usage

{% raw %}
```html
{% user "Zach Leatherman", "zachleat" %}
```
{% endraw %}

#### Outputs

```html
<div class="user">
  <div class="user_name">Zach Leatherman</div>
  <div class="user_twitter">@zachleat</div>>
</div>
```

### Paired Shortcode

```js
module.exports = function(eleventyConfig) {
  // Nunjucks Shortcode
  eleventyConfig.addPairedNunjucksShortcode("user", function(bioContent, name, twitterUsername) { … });

  // Universal Shortcodes (Adds to Liquid, Nunjucks, Handlebars)
  eleventyConfig.addPairedShortcode("user", function(bioContent, name, twitterUsername) {
    return `<div class="user">
<div class="user_name">${name}</div>
<div class="user_twitter">@${twitterUsername}</div>
<div class="user_bio">${bioContent}</div>
</div>`;
  });
};
```

#### Usage

Note that you can put any Nunjucks tags or content inside the `{% raw %}{% user %}{% endraw %}` shortcode! Yes, even other shortcodes!

{% raw %}
```html
{% user "Zach Leatherman", "zachleat" %}
  Zach likes to take long walks on Nebraska beaches.
{% enduser %}
```
{% endraw %}

##### Outputs

```html
<div class="user">
  <div class="user_name">Zach Leatherman</div>
  <div class="user_twitter">@zachleat</div>
  <div class="user_bio">Zach likes to take long walks on Nebraska beaches.</div>
</div>
```

### Shortcode Named Argument Syntax (Nunjucks-only)

Creates a single argument object to pass to the shortcode.

```js
module.exports = function(eleventyConfig) {
  // Nunjucks Shortcode
  eleventyConfig.addNunjucksShortcode("user", function(user) {
    return `<div class="user">
<div class="user_name">${user.name}</div>
${user.twitter ? `<div class="user_twitter">@${user.twitter}</div>` : ''}
</div>`;
  });
};
```

#### Usage

The order of the arguments doesn’t matter.

{% raw %}
```html
{% user name="Zach Leatherman", twitter="zachleat" %}
{% user twitter="zachleat", name="Zach Leatherman" %}
```
{% endraw %}

##### Outputs

```html
<div class="user">
  <div class="user_name">Zach Leatherman</div>
  <div class="user_twitter">@zachleat</div>
</div>
```

#### Usage

Importantly, this syntax means that any of the arguments can be optional (without having to pass in a bunch of `null, null, null` to maintain order).

{% raw %}
```html
{% user name="Zach Leatherman" %}
```
{% endraw %}

##### Outputs

```html
<div class="user">
  <div class="user_name">Zach Leatherman</div>
</div>
```

### Asynchronous Shortcodes {% addedin "0.10.0" %}

Note that the configuration methods here to add asynchronous shortcodes are different than their synchronous counterparts.

{% codetitle ".eleventy.js" %}

```js
module.exports = function(eleventyConfig) {
  eleventyConfig.addNunjucksAsyncShortcode("user", async function(name, twitterUsername) {
    return await fetchAThing();
  });

  eleventyConfig.addPairedNunjucksAsyncShortcode("user2", async function(content, name, twitterUsername) {
    return await fetchAThing();
  });
};
```

#### Usage

(It’s the same.)

{% raw %}
```html
{% user "Zach Leatherman", "zachleat" %}

{% user2 "Zach Leatherman", "zachleat" %}
  Zach likes to take long walks on Nebraska beaches.
{% enduser2 %}
```
{% endraw %}

### Access to `page` data values {% addedin "0.11.0" %}

If you aren’t using an arrow function, Nunjucks Shortcodes (and Handlebars, Liquid, and 11ty.js JavaScript Functions) will have access to Eleventy [`page` data values](/docs/data-eleventy-supplied/#page-variable-contents) without needing to pass them in as arguments.

```js
module.exports = function(eleventyConfig) {
  eleventyConfig.addNunjucksShortcode("myShortcode", function() {
    // Available in 0.11.0 and above
    console.log( this.page );

    // For example:
    console.log( this.page.url );
    console.log( this.page.inputPath );
    console.log( this.page.fileSlug );
  });
};
```

### Generic Global {% addedin "1.0.0" %}

Nunjucks provides a custom way to [add globals](https://mozilla.github.io/nunjucks/api.html#addglobal) to templates. These can be any arbitrary JavaScript: functions, variables, etc. Note that this is not async-friendly (Nunjucks does not support `await` inside of templates).

```js
module.exports = function(eleventyConfig) {
  eleventyConfig.addNunjucksGlobal("fortythree", 43);
};
```

{% raw %}
```
{{ fortythree }}
```
{% endraw %}

```js
module.exports = function(eleventyConfig) {
  eleventyConfig.addNunjucksGlobal("fortytwo", function() {
    return 42;
  });
};
```

{% raw %}
```
{{ fortytwo() }}
```
{% endraw %}

Read more on the [Nunjucks documentation](https://mozilla.github.io/nunjucks/api.html#addglobal) or [relevant discussion on Eleventy Issue #1060](https://github.com/11ty/eleventy/pull/1060).