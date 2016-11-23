<!-- MarkdownTOC -->

- Global
- Markup
- SCSS
- Javascript

<!-- /MarkdownTOC -->

## Global

There are a few global guidelines across all languages that are important to follow:

* Avoid committing commented code. Version control should be enough to debug should anything go wrong in the future.
*  For both comments and source code, try to avoid lines going over ~80 characters wide - Your peers shouldn't have to scroll horizontally in order to digest your code.
*   Use a [Docblockr](https://github.com/spadgos/sublime-jsdocs) extension to maintain consistent commenting styles. Spend 5 minutes reading through the README there if you haven't used it already.
*   Ensure whatever IDE you're using has an [EditorConfig](http://editorconfig.org/) so that settings are configured the same across all of our machines. This prevents unnecessary git diffs, and it helps maintain legibility. 

Whatever IDE you choose, ensure your settings and your EditorConfig are;

```
/* Linux File system line endings */
"default_line_ending": "LF",

/* Newline at end of file */
"insert_final_newline" = true,

/* Always use your setup for indentation.
This will prevent legacy projects from
tripping you up. */
"detect_indentation": false,

/* 4 space tabs */
"tab_size": 4,

/* Translate tabs to spaces to ensure
consistent visual spacing regardless of system
or font selected */
"translate_tabs_to_spaces": true
```

## Markup

*   Avoid using inline style attributes or tags - There is only rarely a requirement for inline style attributes (ISA) or inline style tags (IST). In the event a scenario presents itself that calls for IST, isolate and reference an additional stylesheet rather than using an IST.

*   Write all elements and attributes in lower case

*   Double quote (rather than single quote) all attributes on an element

*   Call attributes in the same order that CSS specificity points would be awarded e.g `<div id="" class="" data-attr=""></div>`

*   Use only a single space between values of an attribute e.g `<div class="foo bar"></div>`

## SCSS

All styling should follow the flavour of [BEM Methodology](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/) introduced by [Harry Roberts](http://csswizardry.com/about/), which will also ensure consistency with the Inuit framework. In addition, [Hugo Giraudal](http://hugogiraudel.com/) wrote a nice set of [Sass Guidelines](http://sass-guidelin.es/), many of which will be referenced here for the sake of maintaining a canonical reference point.

### Naming conventions [(ref)](http://sass-guidelin.es/#naming-conventions)

Mixins, function names and variables should be lower-case with hyphenations. As with all programming, please try to be as abstract as possible with your naming so that if the code output or technique changes the function name will still make sense.

### BEM Depth

Try to avoid going more than two elements deep when declaring BEM selectors in order to maintain some legibility of the markup when reading attributes. For example, a comment block which contains a body, an avatar and an avatar image might look like this:
```
.comment{
    ...

    &__body{
        ...

        &__avatar{
            ...

            &__image{
                ...
            }
        }
    }
}
```

But, `.comment__body__avatar__image` is beginning to sit a little too long. In this situation there is no problem with targeting tags in the declaration, as the components are accutely targeted enough that it won't cause any style leaking.

Thus, the following would also be acceptable here:

```
.comment{
    ...

    &__body{
        ...

        &__avatar{
            ...

            /*
            Tag styling fine here as there is unlikely to be
            many different elements within this section of the
            component.
             */
            img{
                ...
            }
        }
    }
}
```

_NB_ - Use the parent BEM child selector (`&__`) sparingly. As a general rule, it is acceptable for small components or modules where you're unlikely to need to scroll in order to read the entire rule set. For larger rule sets that may require some arguably complex arrangements, stick to declaring children outside the nest.

However, usage of the BEM modifier selector (`&--`) is a little more relaxed and in generaly it should be fine to use in most scenarios, and is often quote helpful for human digestion of the code base.

### Media Queries

The cleanest, most predictable way of writing media queries _used to be_ at the end of a file or segment which included query overrides for the preceding set of styles. Sass makes this method largely redundant and we opt for nesting media queries within a style declaration instead.

*   Nest media queries
*   Use framework provisioned variables for breakpoints
*   If a single use media query is required where a unit-based value is used, ensure to leave a descriptive comment explaining why it was used and where the number comes from. These are generally referred to as 'shit points'. ie; where the layout starts to look like shit.
```
.block{
    ...

    /*
    Navigational elements are too tightly
    arranged at this break point, right before
    our mobile styles kicked in, so just a quick
    hack adjustment to account for that.
     */
    @media screen and (max-width:756px){
        font-size:14px;
    }
}
```

Media queries should be handled with an additive 'mobile first' approach, rather than a reductive approach. In essence, build the generally more simple mobile version first and then build styles up on top of that. In this way, 'max-width' queries should be rare.

```
//Yep
.block {
    width:200px;

    @media screen and (min-width:400px) {
        float:left;
        border:1px solid #000;
    }
}

//Nope 
.block {
    width:200px;
    float:left;

    @media screen and (max-width:399px){
        float:none; /* <-- Here we're having to override the pre-existing float */
        border:1px solid #000; /* <-- This is particularly bad, as we now some additive, and some reductive style declarations */
    }
}
```

Never create differential media queries.

### Declaration Order

To help with predictability to ensure quick scans of styles, use the following order when declaring rules for a selector:

1.  **Extends/Imports** - Extend pre-existing style sets
2.  **Mixins** - Run a set of mixins over the top of the latter
3.  **Styles** - Finalise specific styles for this element

For example:

    .block{
        @extend %clearfix;
        @extend .island;
        @include fontStack(helvetica);
        color:#f00;
        text-transform:uppercase;
        ...
    }

This will help speed up debugging for team members as it clearly outlines a seperation of inheritance versus local style changes.

### Whitespace

*   Never new line between property:value pairs
*   Always new line after opening brace, and before closing brace
*   Always new line before nested selector
*   Always new line before nested media query
*   Always new line after each comma in comma delimited selector

```
// Yep
.block{
    color:#f00;
    background:#c0ff33;
    width:200px;

    .another-element{
        ...
    }

    @media screen and (min-width:400px){

    }
}

// Nope
.block{ color:#f00; background:#c0ff33;}

// Nope
.block{
    color:#f00;

    background:#c0ff33;
    .another-element{
        ...
    }
    width:200px;
}
```

### Strings [(ref)](http://sass-guidelin.es/#strings)

Use single quotes where necessary, rather than double.

### Numbers [(ref)](http://sass-guidelin.es/#numbers)

Never display trailing zeroes, but always display leading zeroes.

```
// Yep
.block{
    opacity:0.5;
}

// Nope{
    opacity:.50;
}
```

Values of `0` should be unitless, unless overriding a unit that is already set.

```
// Yep
.block{
    width:0;
}

//Nope
.block{
    width:0px;
}

//Yep
.block{
    width:20px;

    @media screen and (min-width:500px) {
        width:0px;   
    }
}
```

Always wrap calculations in parantheses.

```
// Yep
.block{
    padding:($base-spacing-unit * 2);
}

// Nope
.block{
    padding:$base-spacing-unit*2;
}
```

Best to avoid heavy computational units as it increases cognitive load of any developer working on the project. Whilst, the final value could be seen in the inspector of a browser, we should be able to read the value quickly enough that it doesn't stump digestion of the codebase.

```
// Avoid
.block{

    /*
    This messy and doesn't hold any meaning
    to the rest of the codebase. In this instance
    it would be fine to specify an exact pixel value,
    and if quirky for any reason, just leave a comment
    explaining why - Though a comment isn't compulsory.
     */
    margin: $base-spacing-unit*4-22px 0 0 0;
}

// Better
.block{
    margin: 82px 0 0 0;
}
```

### Colours

All colours should be declared as hexidecimal values and stored in variables for consistency. It is your choice whether you use a data-map for referencing the design palette or a series of name-spaced variables. My personal preference is to use a data-map, but either option is acceptable. Please make sure to at least stick with what ever the project author has dictated.

**Using variables:**

```
$primary-darker: ...;
$primary-dark: ...;
$primary: #f00;
$primary-light: ...;
$primary-lighter: ...;

$secondary-darker: ...;
$secondary-dark: ...;
$secondary: #c0ff33;
$secondary-light: ...;
$secondary-lighter: ...;

/*
Example usage:
 */
.block{
    color:$primary;
    background:$primary-darker;
}
```

**Using Data-maps:**

```
// Colour Palette
$palette: (
    'primary': (
        'base': #f00,
        'dark': ...,
        'darker': ...,
        'light': ...,
        'lighter': ...
    ),
    'secondary': (
        'base': #c0ff33,
        'dark': ...,
        'darker': ...,
        'light': ...,
        'lighter': ...
    )
);

// Mixin
@function getColor($name,  $tone: 'base') {
    @return map-get(map-get($palette, $name), $tone);
}

/*
Example usage:
 */
.block{
    // Returns base colour
    color:getColor('primary');

    // Returns darker variation
    background:getColor('secondary', 'darker');
}
```


Furthermore we'd actually encourage use of data-maps for other situations where you're likely to end up with a cluster of related/scoped variables; such as with predefined project z-indicies [as discussed here](http://sass-guidelin.es/#multiple-variables-or-maps).

### States and Psuedo Selectors

Interactive states and psuedo selectors are to be declared after the core rule set, with psuedo selectors taking precedence over interactive states in order to preserve the cascading nature of stylesheets. This allows us to declare psuedo styles, then alter them when targeting interactive states afterward.

```
// Yep
.block{
    color:#f00;

    &:before{
        content:'-';
        display:block;
        height:10px;
        width:10px;
        background:#f00;
        color:#fff;
    }

    &:hover{
        color:#c0ff33;
    }

    &:focus{
        border-bottom:1px dotted #f00;

        &:before{
            border:1px solid #c0ff33;
        }
    }
}

// Nope
.block{
    color:#f00;

    &:hover{
        color:#c0ff33;
    }

    &:focus{
        border-bottom:1px dotted #f00;

        &:before{
            border:1px solid #c0ff33;
        }
    }

    &:before{
        content:'-';
        display:block;
        height:10px;
        width:10px;
        background:#f00;
        color:#fff;
    }
}
```

### Contextual modifiers

A context modification occurs when a parent node higher up in the DOM tree has a class or ID applied to it, which is used to flag some sort of change to the document, or in the case of Modernizr and Detectizr, the existence of browser functionality. Much like media queries, these declarations should be nested within the rule set.

```
.block{
    opacity:0.25;

    .no-opacity &{
        visibility:hidden;
    }
}
```

### Cutting/Bleeding Edge Specification considerations

Use new spec attributes only when they are required, or no fallback will need to be created.
Using `flexbox` as an example;
* DO NOT create an entire site layout in `flex`, if you're supporting IE9. You'll have to create a fallback anyway, and by now you should know how to create the same layout in CSS2 (even though yes, it's more work).
* DO use `flex` to build a card pattern where equal heights are required. `Flex` makes this much easier and is quick to use with SASS mixins. The fallback is hacky and difficult, and as such can be considered graceful degradation to ignore it.
* Of course, modernizr should be included in all projects to make feature detection easier

## Javascript

The [jQuery Docs](http://contribute.jquery.org/style-guide/js) have a decent guide for reference, some of which will be referred to here, though a few hard and fast rules to follow:

*   There is to be **no** styling done in Javascript. Instead, we should be adding/removing classes and letting CSS handle the styles.
*   Classes used specifically for JS targeting should be prefixed with `.js--`
*   JS should not target classes created for style purposes. In this way function and style are decoupled and should you need to change the styling of an element, the functionality remains.

### Functions

Functions should be as abstract and versatile as possible. Choose names for functions that represent what the code is doing and not what the project is doing. If you're stuck on how this should be done, a good reference is the [Underscore.js annotated source](http://underscorejs.org/docs/underscore.html).

### Variables

Variables should always be declared at the beginning of a segment of code, and where more than one is required, a single `var` declaration should be used, followed by a new lined and comma separated list of variables.

```
// Yep
var
foo = "Hello",
bar = 42,
baz = false;

// Nope
var foo = "Hello";
var bar = 42;
var baz = false;
```

### Chaining [(ref)](http://contribute.jquery.org/style-guide/js/#chained-method-calls)

Chained methods should always new line each link in the chain for legibility.

```
// Yep
$('.block')
.addClass('block--active')
.find('button')
.fadeOut(100);

// Nope
$('.block').addClass('block--active').find('button').fadeOut(100);
```
