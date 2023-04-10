# ⚡️ Vite front-end boilerplate

This repository contains a frontend boilerplate made with Vite, SASS, Twig and the ES6 modules. It is shipped with some pre-made mixins, a configured SVG-Sprite setup and some image optimization functionalities. It also includes some great performance enhancement tools : Purge & Critical CSS.

## Requirements

- `node` : `>=16`
- `yarn` (or equivalent)

## Installation

```sh
$ yarn install
```

## Configuration

Edit the [`config.js`](config.js) according to your needs.

### Environment

- **`server`**: to configure the development server specify the `host` and `port` values. You can refer to the full development server configuration options for [`Vite server options`](https://vitejs.dev/config/#server-options).
- **`rootDir`**: specify the project's root directory (where your index.html file is located). The specified path can be an absolute path or be relative to the location of the config.js file.
- **`buildDir`**: specify the output directory (relative to the project's root).

### Purge CSS

- **`purgecss`**:
  - `enable`: boolean, toggle to activate or deactivate Purge CSS.
  - `safeList`: optional, an array of classes to add to the [`safelist`](https://purgecss.com/safelisting.html). The safelist specifies the selectors which are safe to leave in the final CSS.

### Critical CSS

- **`critical`**:
  - `enable`: boolean, toggle to activate or deactivate Critical CSS.

### Images

- **`imagemin`**: an object containing all image optimization configurations. For more information you can refer to [`vite-plugin-imagemin`](https://github.com/vbenjs/vite-plugin-imagemin)

### HTML Minify

- **`htmlMinify`**:
  - `enable`: boolean, toggle to activate or deactivate HTML Minify (uses Terser).
  - `options`: For a detailed look at the overall configuration options, please refer to [html-minifier-terser](https://github.com/terser/html-minifier-terser#options-quick-reference),

## Development

### Assets

- #### Styles

  **SASS/PostCSS** files are located under `src/assets/scss/`

- #### TypeScript

**TypeScript** files with support of the latest ECMAScript are located under `src/assets/js/` Scripts are loaded as modules with conditionnal import using `conditioner`, feel free to consult the documentation of this dependency [`here`](https://github.com/rikschennink/conditioner).

Use a `data-module` attribute with a value corresponding to the name of your JS file for it to be loaded in your HTML file. You can condition the loading of your module to a context, for instance a breakpoint, by specifying a `data-context` attribute. For instance, in the following example, the Hello module will be imported if the current viewport has a width of at least 48rem (768px) and if the element is visible in the viewport.

```twig
<h1
  data-module="Hello"
  data-context="@media (min-width: 48rem) and was @visible"
>
  {{ siteTitle }}
</h1>
```

```js
// src/assets/js/modules/Hello.ts
export default (element: HTMLElement): Function => {
  // Module mounted
  console.log('Hello', element);

  return () => {
    // Module unmounted
    console.log('Bye');
  };
};
```

- #### Images

**Image** files are located under `src/assets/images/` You can convert all your `.png` and `.jpg` images to WebP format by using this command :

```sh
$ yarn webp
```

There is a twig template located under `src/assets/templates/utils/_image.twig` which you can use as a reference.

Example with a simple image

```twig
# image.src: path to your image (required)
# image.ext: image source extension (required)
# pictureClass: Class attribute for the picture element
# image.src_2x: path to the retina version of your image
# image.webp: set webp to true if you want to use your webp image converted

{% include 'utils/_image.twig' with {
  pictureClass: 'picture',
  image: {
    src: '/assets/images/cover',
    src_2x: '/assets/images/cover@2x',
    ext: 'png',
    webp: true
    alt: 'Hero cover'
  }
} %}
```

Example with multiple breakpoints

```twig
{% include 'utils/_image.twig' with {
  pictureClass: 'picture',
  image: {
    src: '/assets/images/cover',
    src_2x: '/assets/images/cover@2x',
    ext: 'png',
    webp: true
    alt: 'Hero cover'
  },
  breakpoints: {
    768: {
      src: '/assets/images/cover-md',
      src_2x: '/assets/images/cover-md@2x',
      ext: 'png',
      webp: true
    },
    1280: {
      src: '/assets/images/cover-lg',
      src_2x: '/assets/images/cover-lg@2x',
      ext: 'png',
      webp: true
    }
  }
} %}
```

- #### Fonts

**Font** files are located under `src/assets/fonts/`

- #### Icons

**Icons** files are located under `src/assets/icons`, they are used to create a svg sprite. Feel free to use the icon twig template as a reference, it is located under : `src/assets/templates/utils/_icon.twig`

```twig
# iconID is the name of your svg file (required)

{% include 'utils/_icon.twig' with {
  iconID: 'menu'
} %}
```

- #### Lazyloading

[`Lazysizes`](https://github.com/aFarkas/lazysizes) is used to lazyload images and background images. Simply add `lazyload` class on your image elements and `data-bg="path/to/your/background"` for background images.

- #### Twig

**Twig** files are located under `src/assets/templates`. For twig modules or components, prefix the file name with `_` to avoid html conversion.

The html files located by default in the src folder are not intented to be replaced directly by the twig ones as the normal page files resolution/linking on the Vite's dev server is wanted to be preserved along with the build logic. However, those files are supposed to contain a json definition instead of the traditional markup, which should be moved on the twig side.

An html file should look something like this:

```html
<!-- index.html -->
<script type="application/json">
  {
    "template": "path/to/template.twig",
    "data": {
      "title": "Homepage"
    }
  }
</script>
```

Template is the path to the twig template to be rendered (relative to the cwd) and data is the local context for that page.

If you want to go deeper with data, like for internationalization. You can create a JSON files associated to the langugage you want to support. JSON files are located under `src/data/{lang}.json`.

For example :

Create a JSON file to support the french language : `src/data/fr.json`

```json
{
  "title": "Accueil"
}
```

Then edit `vite.config.js` to import and inject JSON to twig global data

```js
// JSON data
...
const fr = require(`./${rootDir}/data/fr.json`)

export default defineConfig ({
  ...
  plugins: [
    twig({
      globals: {
        data: {
          en,
+         fr,
        }
      }
    }),
    ...
  ],
})
```

In your html file, pass your language key as data

```html
<script type="application/json">
  {
    "template": "src/assets/templates/index.twig",
    "data": {
      "lang": "fr"
    }
  }
</script>
```

you can now use your data according to the language you wish to use

```twig
{{ data[lang].title }}
```

- #### Theming

All default theming configuration are located in `src/scss/vars/_default.scss`. A bunch of useful mixins ready for use in the `src/scss/base/_mixins.scss` file.

- ##### Fluid sizing

A SASS mixin using linear interpolation to resize values with the help calc() across multiple breakpoints. It uses some basic math behind the scenes. You don't need to know the math or understand it to use this mixin. [`Source`](https://github.com/Jakobud/poly-fluid-sizing)

Minimum and maximum size are set in `src/scss/vars/_default.scss`, they are related to the breakpoints map.

```scss
$pfs-min: map.get($breakpoints, xs);
$pfs-max: map.get($breakpoints, 2xl);
```

Usage :

```scss
@include poly-fluid-sizing(
  'font-size',
  (
    $pfs-min: rem(18px),
    $pfs-max: rem(24px),
  )
);
```

- ##### Colors

Like font styles, colors are also declared as a map

```scss
$colors: (
  'black': (
    base: #000,
  ),
  'white': (
    base: #fff,
  ),
  'main': (
    light: #63ccff,
    base: #039be5,
    dark: #006db3,
  ),
);

// Then use set-color function, default type is "base"

.white {
  color: set-color(white);
}

.main--dark {
  background-color: set-color(main, dark);
}
```

- ##### Breakpoints

Breakpoints are also referred to in a map

```scss
$breakpoints: (
  xs: rem(360px),
  sm: rem(480px),
  md: rem(768px),
  lg: rem(1024px),
  xl: rem(1280px),
  2xl: rem(1440px),
);

// Then use mq mixin
.container {
  max-width: 100%;

  @include mq(lg) {
    max-width: rem(1024px);
  }
}
```

You can set `$bp-helper` to `true` in order to enable a little UI helper displayed in the bottom right of your screen which will give you a real time visual update of your current breakpoint.

- ##### Grid

Grid settings are located under `src/scss/vars/_default.scss` Gutter sizes are related to breakpoints, fluid sizing is used to calculate them.

```scss
// Define grid inner gutter sizes
$grid-inner-gutters: (
  xs: rem(8px),
  2xl: rem(20px),
);
$grid-outer-gutters: (
  xs: rem(20px),
  2xl: rem(50px),
);

// Define number of grid columns
$grid-columns: 12;
// Define grid container max-width
$grid-container-width: rem(1024px);
```

helper classes are automatically generated, these include:

- `wrapper`: Grid wrapper with `$grid-container-width` value as max-width
- `container`: Handle grid outer gutters
  - `container--relative`: Modifier to set a relative position to the container
  - `container--ng`: Modifier to remove outer gutters
- `row`: Flex container for columns
  - `row--reverse`: Modifier to reverse columns order
  - `row--full`: Modifier to break the wrapper max-width to fit the whole screen size
- `col-{number}`: A column whose width is equal no $grid-columns / {number}
- `col-{breakpoint}-{number}`: Same as above, but with a breakpoint condition
- `col-offset-{number}`: Push the columns
- `col-{breakpoint}-offset-{number}`: Same as above, but with a breakpoint condition

Example :

```twig
<div class="wrapper">
  <div class="container">
    <div class="row">
      <div class="col-12 col-md-6 col-lg-3">
        Full with on small viewport, half width on medium viewport and one quarter on large viewport
      </div>

      <div class="col-12 col-md-6 col-lg-3">
        Full with on small viewport, half width on medium viewport and one quarter on large viewport
      </div>

      <div class="col-12 col-md-6 col-lg-3">
        Full with on small viewport, half width on medium viewport and one quarter on large viewport
      </div>

      <div class="col-12 col-md-6 col-lg-3">
        Full with on small viewport, half width on medium viewport and one quarter on large viewport
      </div>
    </div>
  </div>
</div>
```

- #### SEO Friendly

Use 2 data attributes to spread a link around an entire element. It uses the `pseudo-classes` trick with absolute positioning. Be careful however with relative positions inside your element. Flag the element you want to spread your link to with `data-seo-container` then add `data-seo-target` to your `<a>` element.

Demo : [`https://codepen.io/Nkzq/pen/RwgJwzd`](https://codepen.io/Nkzq/pen/RwgJwzd)

```html
<div class="card" data-seo-container>
  <div class="card__media">
    <img src="https://picsum.photos/165/165" alt="photo placeholder" />
  </div>
  <div class="card__content">
    <p class="card__category">Technology</p>
    <h2 class="card__title">
      <a
        href="https://romainlefort.com"
        class="card__link"
        target="_blank"
        data-seo-target
        >The companies offering delivery to the Moon</a
      >
    </h2>
    <p class="card__excerpt">
      Not many people can say they have a doctorate in interplanetary
      navigation, but Tim Crain can.
    </p>
  </div>
</div>
```

## Build Assets

### Development

Start a local development server with previous defined settings, default is `https://localhost:8000/`

```sh
$ yarn dev
```

### Production

Build all assets for production :

```sh
$ yarn build
```

To include WebP images convert :

```sh
$ yarn prod
```

Preview your production build :

```sh
$ yarn preview
```

## Run Code Style Linters

### SASS

```sh
$ yarn lint:sass
```

### TypeScript

```sh
$ yarn lint:js
```

---

Hope you like it ! Feel free to open issues if you need any clarifications or misunderstand some points mde in this documentation. Cheers!
