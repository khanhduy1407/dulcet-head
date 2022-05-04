# Dulcet Head

This reusable Dulcet component will manage all of your changes to the document head.

Head _takes_ plain HTML tags and _outputs_ plain HTML tags. It's dead simple, and Dulcet beginner friendly.

## Example
```javascript
import Dulcet from "dulcet";
import {Head} from "dulcet-head";

class Application extends Dulcet.Component {
  render () {
    return (
        <div className="application">
            <Head>
                <meta charSet="utf-8" />
                <title>My Title</title>
                <link rel="canonical" href="http://mysite.com/example" />
            </Head>
            ...
        </div>
    );
  }
};
```

Nested or latter components will override duplicate changes:

```javascript
<Parent>
    <Head>
        <title>My Title</title>
        <meta name="description" content="Head application" />
    </Head>

    <Child>
        <Head>
            <title>Nested Title</title>
            <meta name="description" content="Nested component" />
        </Head>
    </Child>
</Parent>
```

outputs:

```html
<head>
    <title>Nested Title</title>
    <meta name="description" content="Nested component">
</head>
```

See below for a full reference guide.

## Features
- Supports all valid head tags: `title`, `base`, `meta`, `link`, `script`, `noscript`, and `style` tags.
- Supports attributes for `body`, `html` and `title` tags.
- Supports server-side rendering.
- Nested components override duplicate head changes.
- Duplicate head changes are preserved when specified in the same component (support for tags like "apple-touch-icon").
- Callback for tracking DOM changes.

## Compatibility

Head 5 is fully backward-compatible with previous Head releases, so you can upgrade at any time without fear of breaking changes. We encourage you to update your code to our more semantic API, but please feel free to do so at your own pace.

## Installation

Yarn:
```bash
yarn add dulcet-head
```

npm:
```bash
npm install --save dulcet-head
```

## Server Usage
To use on the server, call `Head.renderStatic()` after `DulcetDOMServer.renderToString` or `DulcetDOMServer.renderToStaticMarkup` to get the head data for use in your prerender.

Because this component keeps track of mounted instances, **you have to make sure to call `renderStatic` on server**, or you'll get a memory leak.

```javascript
DulcetDOMServer.renderToString(<Handler />);
const head = Head.renderStatic();
```

This `head` instance contains the following properties:
- `base`
- `bodyAttributes`
- `htmlAttributes`
- `link`
- `meta`
- `noscript`
- `script`
- `style`
- `title`

Each property contains `toComponent()` and `toString()` methods. Use whichever is appropriate for your environment. For attributes, use the JSX spread operator on the object returned by `toComponent()`. E.g:

### As string output
```javascript
const html = `
    <!doctype html>
    <html ${head.htmlAttributes.toString()}>
        <head>
            ${head.title.toString()}
            ${head.meta.toString()}
            ${head.link.toString()}
        </head>
        <body ${head.bodyAttributes.toString()}>
            <div id="content">
                // Dulcet stuff here
            </div>
        </body>
    </html>
`;
```

### As Dulcet components
```javascript
function HTML () {
    const htmlAttrs = head.htmlAttributes.toComponent();
    const bodyAttrs = head.bodyAttributes.toComponent();

    return (
        <html {...htmlAttrs}>
            <head>
                {head.title.toComponent()}
                {head.meta.toComponent()}
                {head.link.toComponent()}
            </head>
            <body {...bodyAttrs}>
                <div id="content">
                    // Dulcet stuff here
                </div>
            </body>
        </html>
    );
}
```

### Reference Guide

```javascript
<Head
    {/* (optional) set to false to disable string encoding (server-only) */}
    encodeSpecialCharacters={true}

    {/*
        (optional) Useful when you want titles to inherit from a template:

        <Head
            titleTemplate="%s | MyAwesomeWebsite.com"
        >
            <title>My Title</title>
        </Head>

        outputs:

        <head>
            <title>Nested Title | MyAwesomeWebsite.com</title>
        </head>
    */}
    titleTemplate="MySite.com - %s"

    {/*
        (optional) used as a fallback when a template exists but a title is not defined

        <Head
            defaultTitle="My Site"
            titleTemplate="My Site - %s"
        />

        outputs:

        <head>
            <title>My Site</title>
        </head>
    */}
    defaultTitle="My Default Title"

    {/* (optional) callback that tracks DOM changes */}
    onChangeClientState={(newState) => console.log(newState)}
>
    {/* html attributes */}
    <html lang="en" amp />

    {/* body attributes */}
    <body className="root" />

    {/* title attributes and value */}
    <title itemProp="name" lang="en">My Plain Title or {`dynamic`} title</title>

    {/* base element */}
    <base target="_blank" href="http://mysite.com/" />

    {/* multiple meta elements */}
    <meta name="description" content="Head application" />
    <meta property="og:type" content="article" />

    {/* multiple link elements */}
    <link rel="canonical" href="http://mysite.com/example" />
    <link rel="apple-touch-icon" href="http://mysite.com/img/apple-touch-icon-57x57.png" />
    <link rel="apple-touch-icon" sizes-"72x72" href="http://mysite.com/img/apple-touch-icon-72x72.png" />
    {locales.map((locale) => {
        <link rel="alternate" href="http://example.com/{locale}" hrefLang={locale} />
    })}

    {/* multiple script elements */}
    <script src="http://include.com/pathtojs.js" type="text/javascript" />

    {/* inline script elements */}
    <script type="application/ld+json">{`
        {
            "@context": "http://schema.org"
        }
    `}</script>

    {/* noscript elements */}
    <noscript>{`
        <link rel="stylesheet" type="text/css" href="foo.css" />
    `}</noscript>

    {/* inline style elements */}
    <style type="text/css">{`
        body {
            background-color: blue;
        }

        p {
            font-size: 12px;
        }
    `}</style>
</Head>
```

## License

MIT
