# DocBlockr
[![Latest Release](https://img.shields.io/github/tag/CodeByZach/sublime_docblockr.svg?label=version)](https://github.com/CodeByZach/sublime_docblockr/releases)

DocBlockr is a package for [Sublime Text 2 & 3][sublime] which makes writing documentation a breeze. DocBlockr supports **JavaScript** (including ES6), **PHP**, **ActionScript**, **Haxe**, **CoffeeScript**, **TypeScript**, **Java**, **Apex**, **Groovy**, **Objective C**, **C**, **C++** and **Rust**.

## Installation

### [Package Control](https://packagecontrol.io)

1. Open Package Control: `Preferences -> Package Control`
2. Select `Package Control: Install Package`
3. Type `DocBlockr` into the search box and select the package to install it

## Feature requests & bug reports

You can leave either of these things [here][issues]. Pull requests are welcomed
heartily, but please read [CONTRIBUTING.md][contrib] first! Basically: in this
repo, the main development branch is `develop` and the stable 'production'
branch is `master`. Please remember to base your branch from `develop` and issue
the pull request back to that branch.

## Changelog

See [the history file][history].

## Usage

> Below are some examples of what the package does. Note that there are no keyboard shortcuts required to trigger these completions - just type as normal and it happens for you!

### Docblock completion

Pressing **enter** or **tab** after `/**` (or `###*` for Coffee-Script) yields a new line and closes the comment.

![](docs/basic.gif)

Single-asterisk comment blocks behave similarly:

![](docs/basic-block.gif)

### Function documentation

However, if the line directly afterwards contains a function definition, then its name and parameters are parsed and some documentation is automatically added.

![](docs/function-template.gif)

Press <kbd>Tab</kbd> to move forward through the fields, press <kbd>Shift</kbd>+<kbd>Tab</kbd> to move back through the fields.

If there are many arguments, or long variable names, it is sometimes useful to spread the arguments across multiple lines. DocBlockr handles this situation too:

![](docs/long-args.gif)

In languages which support [type hinting][typehinting] or default values, then those types are prefilled as the datatypes.

![](docs/type-hinting.gif)

DocBlockr will try to make an intelligent guess about the return value of the function.

- If the function name is or begins with "set" or "add", then no `@return` is inserted.
- If the function name is or begins with "is" or "has", then it is assumed to return a `Boolean`.
- In Javascript, if the function begins with an uppercase letter then it is assumed that the function is a class definition. No `@return` tag is added.
- In PHP, some of the [magic methods][magicmethods] have their values prefilled:
  - `__construct`, `__destruct`, `__set`, `__unset`, `__wakeup` have no `@return` tag.
  - `__sleep` returns an `array`.
  - `__toString` returns a `string`.
  - `__isset` returns a `bool`.
- In ES6 Javascript, generator functions get a `@yield` tag instead of `@return`

### Variable documentation

If the line following the docblock contains a variable declaration, DocBlockr will try to determine the data type of the variable and insert that into the comment.

Press <kbd>space</kbd> or <kbd>shift</kbd>+<kbd>enter</kbd> after an opening `/**` to insert an inline docblock.

![](docs/vars.gif)

DocBlockr will also try to determine the type of the variable from its name. Variables starting with `is` or `has` are assumed to be booleans, and `callback`, `cb`, `done`, `fn`, and `next` are assumed to be functions. If you use your own variable naming system, (e.g. hungarian notation: booleans all start with `b`, arrays start with `arr`), you can define these rules yourself. Use the `docblockr_notation_map` setting, example:

```js
{
    "docblockr_notation_map": [
        {
            "prefix": "b", /* a prefix, matches only if followed by an underscore or A-Z */
            "type": "bool" /* translates to "Boolean" in javascript, "bool" in PHP */
        },
        {
            "regex": "tbl_?[Rr]ow", /* any arbitrary regex to test against the variable name */
            "type": "TableRow"      /* you can add your own types */
        }
    ]
}
```

The notation map can also be used to add arbitrary tags, according to your own code conventions. For example, if your conventions state that functions beginning with an underscore are private, you could add this to the `docblockr_notation_map`:

```json
{
    "prefix": "_",
    "tags": ["@private"]
}
```

### Comment extension

Pressing enter inside a docblock will automatically insert a leading asterisk and maintain your indentation.

![](docs/auto-indent.gif)

![](docs/auto-indent-2.gif)

This applies to docblock comments `/** like this */` as well as inline double-slash comments `// like this`

![](docs/single-line.gif)

In either case, you can press `shift+enter` to stop the automatic extension.

Oftentimes, when documenting a parameter, or adding a description to a tag, your description will cover multiple lines. If the line you are on is directly following a tag line, pressing <kbd>Tab</kbd> will move the indentation to the correct position.

![](docs/deep-indent.gif)

### Comment decoration

If you write a double-slash comment and then press `Ctrl+Enter`, DocBlockr will 'decorate' that line for you.

    // Foo bar baz<<Ctrl+Enter>>

    -- becomes

    /////////////////
    // Foo bar baz //
    /////////////////

This can be disabled by changing the `docblockr_decorate` setting to `false`.

### Reparsing a DocBlock

Sometimes, you'll perform some action which clears the fields (sections of text which you can navigate through using <kbd>Tab</kbd>). This leaves you with a number of placeholders in the DocBlock with no easy way to jump to them.

With DocBlockr, you can reparse a comment and reactivate the fields by pressing the hotkey `Alt+Shift+Tab` in OS X or Linux, or `Alt+W` in Windows

### Reformatting paragraphs

Inside a comment block, hit `Alt+Q` to wrap the lines to make them fit within your rulers. If you would like subsequent lines in a paragraph to be indented, you can adjust the `docblockr_indentation_spaces_same_para` setting. For example, a value of `3` might look like this:

    /**
     * Duis sed arcu non tellus eleifend ullamcorper quis non erat. Curabitur
     *   metus elit, ultrices et tristique a, blandit at justo.
     * @param  {String} foo Lorem ipsum dolor sit amet.
     * @param  {Number} bar Nullam fringilla feugiat pretium. Quisque
     *   consectetur, risus eu pellentesque tincidunt, nulla ipsum imperdiet
     *   massa, sit amet adipiscing dolor.
     * @return {[type]}
     */

### Adding extra tags

Finally, typing `@` inside a docblock will show a completion list for all tags supported by [JSDoc][jsdoc], the [Google Closure Compiler][closure], [YUIDoc][yui] or [PHPDoc][phpdoc]. Extra help is provided for each of these tags by prefilling the arguments each expects. Pressing <kbd>Tab</kbd> will move the cursor to the next argument.

## Configuration

You can access the configuration settings by selecting `Preferences -> Package Settings -> DocBlockr`.

- `docblockr_indentation_spaces` *(Number)* The number of spaces to indent after the leading asterisk.
```
        // docblockr_indentation_spaces = 1
        /**
         * foo
         */

        // docblockr_indentation_spaces = 5
        /**
         *     foo
         */
```
- `docblockr_align_tags` *(String)* Whether the words following the tags should align. Possible values are `'no'`, `'shallow'` and `'deep'`

    > For backwards compatibility, `false` is equivalent to `'no'`, `true` is equivalent to `'shallow'`

    `'shallow'` will align only the first words after the tag. eg:

        @param    {MyCustomClass} myVariable desc1
        @return   {String} foo desc2
        @property {Number} blahblah desc3

    `'deep'` will align each component of the tags, eg:

        @param    {MyCustomClass} myVariable desc1
        @return   {String}        foo        desc2
        @property {Number}        blahblah   desc3


- `docblockr_extra_tags` *(Array.String)* An array of strings, each representing extra boilerplate comments to add to *functions*. These can also include arbitrary text (not just tags).
```
        // docblockr_extra_tags = ['This is a cool function', '@author nickf', '@version ${1:[version]}']
        /**<<enter>>
        function foo (x) {}

        /**
         * [foo description]
         * This is a cool function
         * @author nickf
         * @version [version]
         * @param  {[type]} x [description]
         * @return {[type]}
         */
        function foo (x) {}
```
    Basic variable substitution is supported here for the variables `date` and `datetime`, wrapped in double curly brackets.
```
        // docblockr_extra_tags = ['@date {{date}}', '@anotherdate {{datetime}}']
        /**<<enter>>
        function foo() {}

        /**
         * [foo description]
         * @date     2013-03-25
         * @datetime 2013-03-25T21:16:25+0100
         * @return   {[type]}
         */
```
- `docblockr_extra_tags_go_after` *(Boolean)* If true, the extra tags are placed at the end of the block (after param/return). Default: `false`

- `docblockr_extend_double_slash` *(Boolean)* Whether double-slash comments should be extended. An example of this feature is described above. Default: `true`

- `docblockr_deep_indent` *(Boolean)* Whether pressing tab at the start of a line in docblock should indent to match the previous line's description field. An example of this feature is described above. Default: `true`

- `docblockr_notation_map` *(Array)* An array of notation objects. Each notation object must define either a `prefix` OR a `regex` property, and a `type` property.

- `docblockr_type_info` *(Array)* Customize @param description based on type, should be type: description, e.g. "array": "${1:[descriptiona]}".

- `docblockr_return_tag` *(String)* The text which should be used for a `@return` tag. By default, `@return` is used, however this can be changed to `@returns` if you use that style.

- `docblockr_spacer_between_sections` *(Boolean|String)* If true, then extra blank lines are inserted between the sections of the docblock. If set to `"after_description"` then a spacer will only be added between the description and the first tag. Default: `false`.

- `docblockr_indentation_spaces_same_para` *(Number)* Described above in the *Reformatting paragraphs* section. Default: `1`

- `docblockr_autoadd_method_tag` *(Boolean)* Add a `@method` tag to docblocks of functions. Default: `false`

- `docblockr_simple_mode` *(Boolean)* If true, DocBlockr won't add a template when creating a doc block before a function or variable. Useful if you don't want to write Javadoc-style, but still want your editor to help when writing block comments. Default: `false`

- `docblockr_lower_case_primitives` *(Boolean)* If true, primitive data types are added in lower case, eg "number" instead of "Number". Default: `false`

- `docblockr_short_primitives` *(Boolean)* If true, the primitives `Boolean` and `Integer` are shortened to `Bool` and `Int`. Default: `false`

- `docblockr_newline_after_block` *(Boolean)* If true, an extra line break is added after the end of a docblock to separate it from the code. Default `false`

- `docblockr_param_name` *(Boolean)* If true, the name of a function parameter is added to the template. If false, it is omitted. Default: `true`

- `docblockr_decorate` *(Boolean)* If false, disable decoration of single line comments with <kbd>Ctrl+Enter</kbd>. Default: `true`

- `docblockr_quick_open_inline` *(Boolean)* If true, an inline docblock will be opened when pressing <kbd>Space</kbd> after an opener (`/**`). When set to `false`, these can be opened by pressing <kbd>Shift+Enter</kbd>. Default: `true`

- `docblockr_function_description` *(Boolean)* If true, a 'description' line will be added for functions. Default: `true`

## Contributors

This package was created by [Nick Fisher][spadgos], but has many contributions from others. Please take a look at the [contributors list][contributors] to see who else should get some thanks.

[closure]: https://code.google.com/closure/compiler/docs/js-for-compiler.html
[contrib]: https://github.com/CodeByZach/sublime_docblockr/blob/master/CONTRIBUTING.md
[contributors]: https://github.com/CodeByZach/sublime_docblockr/blob/master/CONTRIBUTORS.md
[history]: https://github.com/CodeByZach/sublime_docblockr/blob/master/HISTORY.md
[issues]: https://github.com/CodeByZach/sublime_docblockr/issues
[jsdoc]: https://code.google.com/p/jsdoc-toolkit/wiki/TagReference
[magicmethods]: https://www.php.net/manual/en/language.oop5.magic.php
[package_control]: https://packagecontrol.io
[phpdoc]: https://phpdoc.org/
[spadgos]: https://github.com/spadgos
[sublime]: https://www.sublimetext.com/
[tags]: https://github.com/CodeByZach/sublime_docblockr/tags
[typehinting]: https://php.net/manual/en/language.oop5.typehinting.php
[yui]: https://yui.github.com/yuidoc/syntax/index.html
