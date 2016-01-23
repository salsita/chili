# inStyle

`inStyle` gives you an intuitive way to modify current selector parents, enabling fully nested CSS development pattern for the styles of your element.
A powerful replacement for `&`.

Currently available in [SASS 3.4](src/instyle/core.sass).  

```Sass
.app > main article div:first-child
  span.thing // let's style span.thing
    color: red
    +in('.app.unleashed article:hover')
      color: blue // .app.unleashed > main article:hover div:first-child span.thing { };
    +in('.isIE4') // .isIE4 .app > main ... span.thing { };
      zoom: 1
```

## Examples

```Sass
.links
  list-style: none

  li
    display: block

    +in('footer')
      display: inline-block // footer .links li { };

    a
      line-height: 1.5

      +in('li:hover')
        color: blue // .links li:hover a { };

      +in('.minimal')
        line-height: 1.2 // .minimal .links li a { };

      +in('.links.with-flowers')
        background-image: url('flowers.png') // .links.with-flowers li a { };

        +in('li:hover')
          background-position: 10px 10px // .links.with-flowers li:hover a { };

      +in('footer')
        color: white // footer .links li a { };

      +in('footer li:hover')
        text-decoration: underline // footer .links li:hover a { };
```

The `in()` query is mapped onto the current selector chain. Any compound elements found are modified upwards of the current nest, otherwise they're expected to be a parent and prepended instead while respecting the logical order of query.

Infinitely nestable, accepting multiple queries and excluding any invalidated parent group selectors.

String parameters are validated by SASS internal `selector-parse()`, so any errors in the attribute query will be reported on compilation.

```Sass
ul,
ol
  list-style: none

    li
      display: block

      &.links
        display: inline-block

      a
        line-height: 1.5

        +in('ol:hover')
          color: red  // ol:hover li a { }; (removes irrelevant ul from group)

        +in('li.links:hover, ol.pictures')
          color: blue  // ul li.links:hover a, ol li.links:hover a, ol.pictures li a { };

        +in('footer')
          font-size: 1.4rem  // footer ul li a, footer ol li a { };
          color: white
```

```Sass
.ultra-search
  display: block

  > div
    height: 40px

    input
      width: 100%
      border-color: blue

      +in('div:hover')
        outline: blue

      +in('.ultra-search.invalid')
        border-color: red

  .suggestion-list
    display: none

    +in('.ultra-search.is-open')
      display: block
```

Let's add media queries to the mix for wellness purposes. [include-media](https://github.com/eduardoboucas/include-media) is recommended, as it allows very flexible and expressive conditioning and fits the pattern and code style, but standard SASS `@media` will also work.

```Sass
article
  max-width: 960px

  +media('>phone', '<desktop')
    max-width: 480px

  +in('.article-listing')
    height: 150px // .article-listing article { };

    +media('<=phone')
      height: 10vh // @media screen and ( ... ) { .article-listing article { ... }; }
```

```Sass
item
  display: flex

  > div:first-child
    flex: 0 0 100px

    +media('<tablet')
      flex-basis: 20vw

      +in('.inverted')
        order: 2  // @media ( ... ) { .inverted item > div:first-child { ... }; }

  > div:nth-child(2)
    flex: 1
```

### Same parent preference

```Sass
div
  overflow: auto

    div.wrapper
      display: flex

      div
        flex-basis: 320px

        article
          padding: 20px

          a:hover
            text-decoration: underline

            +in('div:hover')
              color: grey // div div.wrapper div:hover article a:hover

            +in('div:hover div')
              color: blue // div:hover div.wrapper div article a:hover
    div
      flex-grow: 1

```

## Installation

`@import 'instyle'` in your SASS/SCSS stylesheet.

Ruby SASS compilation is required due to reliance on 3.4 features. Conversion to `libsass` should work out of the box once 3.4 is stable.

- Install Ruby - [Win](http://rubyinstaller.org/), [Linux](https://www.ruby-lang.org/en/documentation/installation/#package-management-systems)

- `gem install sass`

- `sass --watch testearea.sass:style.css`

## Roadmap

- Stylus port (blocked by [#1703](https://github.com/stylus/stylus/issues/1703))
