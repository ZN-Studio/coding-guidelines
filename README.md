# Contents
- [Background](#background)
- [Project Structure](#project-structure)
- [JavaScript](#JavaScript)
  - [JavaScript Standard Style](https://github.com/standard/standard)
  - [JS File Structure](#js-file-structure)
    - [app/index.js](#appindexjs)
    - [app/service-worker.js](#appservice-workerjs)
    - [animations/](#animations)
    - [classes/](#classes)
    - [components/](#components)
    - [pages/](#pages)
    - [shaders/](#shaders)
    - [utils/](#utils)
- [CSS](#CSS)
  - [CSS File Structure](#css-file-structure)
    - [styles.css](#stylescss)
    - [fonts.css](#fontscss)
    - [scaffolding.css](#scaffoldingcss)
    - [helpers.css](#helperscss)
    - [typography.css](#typographycss)
    - [forms.css](#formscss)
    - [buttons.css](#buttonscss)
  - [Code Guidelines](#code-guidelines)
    1. [Refactor Components](#1-refactor-components)
    2. [Keep Layout Classes Inline](#2-keep-layout-classes-inline)
    3. [Extend Tailwind Functionality](#3-extend-tailwind-functionality)
    4. [Always use `rem` instead of `px`](#4-always-use-rem-instead-of-px)
    5. [Nest CSS For Readability](#5-nest-css-for-readability)
    6. [Keep `apply`'s On New Lines](#6-keep-applys-on-new-lines)
- [Visual Studio Code Extensions](#visual-studio-code-extensions)
- [VS Code Settings](#vs-code-settings)

# Background

[ZN Studio](https://znstudio.com.au) is a digital experience design consulting studio in Brisbane with a goal to contribute our share of memorable experiences to this world. Every website, app and experience is designed, tested and built for the sake of this vision. Seriously, imagine a world where experience is always focused.

We designed these code guidelines to help us develop better front-end code.

We believe that by following these guidelines you will not only write better code, but enjoy the process much more.


# JavaScript
For all JavaScript guidelines, we follow the [JavaScript Standard Style](https://github.com/standard/standard).

## JS File Structure

```
app
│   index.js
|   service-worker.jp
│
└───animations
    │   Paragraph.js
└───classes
    │   Animation.js
└───components
    │   Menu.js
└───pages
    │   index.js
    └───Generic
        │   index.js
└───shaders
    │   fragment.glsl
    │   vertex.glsl
└───utils
    │   math.js
    |   ...
```

### app/index.js

Main entry file for the application. Creates pages, manages routes, final point for all events to bubble up to.

### app/service-worker.js

Service Worker code here.

### animations/

Used to create animation classes that handle animating in, out when in viewport.

E.g.

```
import each from 'lodash/each'

import Animation from './classes/Animation'

import { calculate, split } from './utils/text'

export default class extends Animation {
  constructor ({ element }) {
    const lines = []
    const paragraphs = element.querySelectorAll('h1, h2, p')

    if (paragraphs.length !== 0) {
      each(paragraphs, element => {
        split({ element })
        split({ element })

        lines.push(...element.querySelectorAll('span span'))
      })
    } else {
      split({ element })
      split({ element })

      lines.push(...element.querySelectorAll('span span'))
    }

    super({
      element,
      elements: {
        lines
      }
    })

    this.onResize()

    if ('IntersectionObserver' in window) {
      this.animateOut()
    }
  }

  animateIn () {
    super.animateIn()

    each(this.lines, (line, lineIndex) => {
      each(line, word => {
        word.style.transition = `transform 1.5s ${0.5 + lineIndex * 0.1}s ease`
        word.style[this.transformPrefix] = 'translateY(0)'
      })
    })
  }

  animateOut () {
    super.animateOut()

    each(this.lines, line => {
      each(line, word => {
        word.style[this.transformPrefix] = 'translateY(100%)'
      })
    })
  }

  onResize () {
    this.lines = calculate(this.elements.lines)
  }
}

```

### classes/
Main classes used for the app.

### components/
Used to create re-usable components. Most often used for all functionality. Components should manage their own state and bind/unbind events.

E.g.

```
import Component from '../classes/Component'

export default class extends Component {
  constructor ({ element, elements }) {
    super({
      classes: {},
      element: element,
      elements: elements
    })

    this.isOpen = false
    this.isHidden = false
    this.isAnimating = false

    this.create()
  }

  create () {}

  reveal () {
    if (!this.isHidden) {
      return
    }
    this.isAnimating = true
    this.isHidden = false
    this.isAnimating = false
  }

  hide () {
    if (this.isHidden) {
      return
    }
    this.isAnimating = true
    this.isHidden = true
    this.isAnimating = false
  }

  openMenu () {
    if (this.isAnimating) {
      return
    }
    this.isAnimating = true
    this.isOpen = true
    this.isAnimating = false
  }

  closeMenu () {
    if (this.isAnimating) {
      return
    }
    this.isAnimating = true
    this.isOpen = true
    this.isAnimating = false
  }

  onResize () {

  }
}

```

### pages/
Pages can be created under folders, e.g. `Generic/index.js`

It is often helpful to create code for specific pages that is not used for the whole site.

When code is shared across all sites, I simply use the generic page.

All pages need to be added to the map in `app/index.js`:
```
this.pages = new Map()
this.pages.set('generic', new Generic())
```

By default, we use the `data-template` property on the `.page__content` to load in:

```
<div class="page__content" data-template="generic">
  ...
</div>
```

Each custom page type can also have unique `show()` and `hide()` methods.

### shaders/
Useful to keep all shader code.

### utils/
Useful for any helpful utility code, e.g. `math.js` contains functions like:

```
export function random (min, max) {
  return GSAP.utils.random(min, max)
}
```


# CSS

We have created our own CSS coding guideline system as seen below. We are hoping to achieve simplicity, readability and extendability.

## CSS File Structure

Styles should be broken up into individual styles for understandability

```
styles
│   styles.css
│   scaffolding.css
│   helpers.css
│   fonts.css
│
└───components
    │   header.css
    │   footer.css
```

### styles.css

```
@import "fonts";
@import "scaffolding";
@import "helpers";
@import "typography";
@import "components/header";
@import "components/footer";
```

### fonts.css

Use this to declare any font faces, e.g.

```
@font-face {
  font-family: 'Neue Haas Grotesk Regular';
  src: url('../fonts/neue-haas-grotesk-regular.woff2') format('woff2'),
       url('../fonts/neue-haas-grotesk-regular.woff') format('woff');
  font-weight: normal;
  font-style: normal;
  font-display: swap;
}
```

### scaffolding.css

Use this to import Tailwind base and utility helpers, then basic setup;

```
@tailwind base;
@tailwind utilities;

html {
  @apply font-body;
  font-size: calc(100vw / 750 * 10);

  @screen sm {
    font-size: calc(100vw / 1920 * 10);
  }
}
```

### helpers.css

Useful in creating helper classes that are:

1. General in nature
2. Often extended by other components

For example a section component:

```
.section {
  @apply py-12;
  @screen sm {
    @apply py-24;
  }
}
```

This class might be extended by other components:

```
// component.css

.component {
  @apply section;
}

```

### typography.css

Used to create type classes that are extended by other components, at the end these are given to default h1, h2, h3 etc.

```
.h1 {
  @apply text-4xl;
  @apply font-bold;
}

h1 {
  @apply h1;
}
```

These classes can also be extended by components later,

```
// component.css

.heading-component {
  @apply h1;
}
```

### forms.css

Used to style forms and their inputs.

```
input:not([type="submit"]),
select,
textarea {
  @apply bg-dark;
  @apply shadow-none;
  @apply border-none;
  @apply rounded-none;
  @apply appearance-none;
  @apply block;
  @apply text-2xl;
  @apply w-full;
  @apply text-white;
  @apply py-6;
  @apply my-4;
  @apply font-light;
  border-bottom: 1px solid theme(colors.white);

  &::placeholder {
    @apply text-white;
  }
}
```

### buttons.css

Used to create buttons and links.

```
.link-line-visible {
  @apply inline-block;
  @apply relative;

  &::after {
    content: '';
    @apply w-full;
    @apply absolute;
    @apply bottom-0;
    @apply right-0;
    @apply transition;
    @apply transition-all;
    @apply duration-200;
    @apply ease-loco;
    @apply origin-right;
    @apply bg-current;
    height: 1px;
  }

  &:hover::after {
    @apply w-0;
  }
}
```

## Code Guidelines

1. [Refactor Components](#1-refactor-components)
2. [Keep Layout Classes Inline](#2-keep-layout-classes-inline)
3. [Extend Tailwind Functionality](#3-extend-tailwind-functionality)
4. [Always use `rem` instead of `px`](#4-always-use-rem-instead-of-px)
5. [Nest CSS For Readability](#5-nest-css-for-readability)
6. [Keep `apply`'s On New Lines](#6-keep-applys-on-new-lines)

### 1: Refactor Components

If you are creating something which can be thought of as an element, e.g. a gallery, it should be refactored into a component file under `components/{name}.css`.

This helps reduce code repetition in the HTML.

A few things to note:

1. All buttons, typography etc. should already be refactored into their files
2. Components should in most cases be able to fill any content size

**✅ DO:**

```
// component.css

.component {
    @apply pt-4;
    @apply mt-2;
    @apply w-full;
    @apply h-full;
    @apply text-lg;
    @apply text-base;
    @apply font-bold;
}
```

**❌ DON'T:**

```
// index.html

<div class="pt-4 mt-2 w-full h-full text-lg text-base font-bold">Name</div>
<div class="pt-4 mt-2 w-full h-full text-lg text-base font-bold">Name</div>
<div class="pt-4 mt-2 w-full h-full text-lg text-base font-bold">Name</div>
<div class="pt-4 mt-2 w-full h-full text-lg text-base font-bold">Name</div>
```

When creating custom components, try and stick to the [BEM Syntax](http://getbem.com/introduction/) as much as possible.

**✅ DO:**

```
// component.css

.component {
    @apply text-lg;

    &__block {
        @apply bg-black;
    }

    &__block__another {
        @apply bg-orange;
    }
}
```

**❌ DON'T:**

```
// component.css

.component {
    @apply text-lg;

    & ul {
        @apply bg-black;
    }
}
```

Sometimes, however, I prefer to keep elements like `p` and `a` free from additional classes.

### 2: Keep Layout Classes Inline

Try to keep layout HTML as inline styles or else components can be polluted.

Components should be as free as possible from particular layout restraints.

**✅ DO:**

```
index.html

<div class="grid grid-cols-2 gap-12">
  <div class="component">...</div>
</div>
```

**❌ DON'T:**

```
component.css

.component-container {
  @apply grid;
  @apply grid-cols-2;
  @apply gap-12;
}
```

### 3: Extend Tailwind Functionality

When deciding what code to use for a particular item, always try and use as many tailwind helper functions and classes as possible.

If required, extend the `tailwind.config.js`.

**✅ DO:**
Extend or alter Tailwind classes when styling components.

```
module.exports {
  ...
  theme: {
    fontSize: {
      2xl: '10 rem'
    }
  }
}
```

```
type.css

.h4 {
  @apply text-2xl;
  @apply py-4;
  @apply px-4;
}
```

**❌ DON'T:**
Create random hard coded values everywhere in the styles.

```
.new-title {
  font-size: 10rem;
  margin: 4rem;
}
```

**✅ DO:**
Always @apply classes where possible, do not create custom styles if not necessary

```
// component.css

.component {
    @apply pt-4;
}
```

**❌ DON'T:**
Create random utility classes outside of `tailwind.config.js`

```
.mobpt2 {
    padding-top: 2rem;
}
```

**✅ DO:**
Use Tailwind `screen` directive over media queries

```
// component.css

.component {
    @apply text-lg;
    @screen sm {
        @apply text-2xl;
    }
}
```

**❌ DON'T:**
Create or use custom media queries in code

```
// component.css

.component {
    @apply text-lg;
}

@media (min-width: 768px) {
    @apply text-2xl;
}
```

### 4: Always use `rem` instead of `px`

Styling should always be in `rem` values as they are relative to the body font size, which changes based on the screen width.

This means that every element will by, default, scale up and down as the window changes.

Furthermore, the calculations on the body means that any pixel value from a design can be easily converted into an equivalent `rem` in the code.

For example,
A `120px` padding at the top of an element, would be equivalent to `12rem` in this system. This is helpful in determining the `rem` value.

**✅ DO:**
Use `rem` values.

```
// component.css

.component {
  margin-top: 10rem;
}
```

**❌ DON'T:**
Use `px` values.

```
// component.css

.component {
    margin-top: 100px;
}
```

### 5: Nest CSS For Readability

**✅ DO:**
Nest all styles, including media queries.

```
// component.css

.component {
    @apply text-lg;
    @screen sm {
        @apply text-2xl;
    }

    &__block {
        @apply bg-black;
        @apply text-white;
    }
}
```

**❌ DON'T:**
Separate classes out.

```
// component.css

.component {
    @apply text-lg;
}

@screen sm {
    .component {
        @apply text-2xl
    }
}

.component__block {
    @apply bg-black;
    @apply text-white;
}
```

### 6: Keep `apply`'s On New Lines

This just helps readability of components, especially when lots of styles.

**✅ DO:**
Keep each `apply` on a new line.

```
// component.css

.component {
    @apply text-lg;
    @apply bg-black;
}
```

**❌ DON'T:**
Write them on the same line.

```
// component.css

.component {
    @apply text-lg bg-black;
}
```

# Visual Studio Code Extensions

Essential extensions:

1. [Tailwind CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss) - Amazingly helpful extension to show tailwind classes in intellisense. Follow install instructions.
2. [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
3. [EditorConfig for VS Code](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)
4. [GLSL Lint](https://marketplace.visualstudio.com/items?itemName=CADENAS.vscode-glsllint)
5. [HTML Snippets](https://marketplace.visualstudio.com/items?itemName=abusaidm.html-snippets)
6. [PostCSS syntax](https://marketplace.visualstudio.com/items?itemName=ricard.PostCSS)
7. [pug (jade) formatter](https://marketplace.visualstudio.com/items?itemName=ducfilan.pug-formatter)
8. [Twig](https://marketplace.visualstudio.com/items?itemName=whatwedo.twig)
9. [Twig Language](https://marketplace.visualstudio.com/items?itemName=mblode.twig-language)
10. [Twig Language 2](https://marketplace.visualstudio.com/items?itemName=mblode.twig-language-2)
11. [WebGL GLSL Editor](https://marketplace.visualstudio.com/items?itemName=raczzalan.webgl-glsl-editor)
12. [Color Highlight](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight)
13. [Shader languages support for VS Code](https://marketplace.visualstudio.com/items?itemName=slevesque.shader)

# VS Code Settings
VS Code Settings for `settings.json`:
```
{
    "editor.tabSize": 2,
    "files.associations": {
        "*.css": "postcss",
        "*.html": "twig"
    },
    "emmet.includeLanguages": {
        "postcss": "css",
        "javascript": "javascriptreact",
        "twig": "html"
    },
    "emmet.syntaxProfiles": {
        "postcss": "css"
    },
    "emmet.excludeLanguages": [
        "markdown"
    ],
    "liveServer.settings.root": "/dist",
    "[twig]": {
        "editor.defaultFormatter": "mblode.twig-language-2"
    },
    "css.validate": false,
    "editor.quickSuggestions": {
        "strings": true
    },
    "tailwindCSS.includeLanguages": {
        "plaintext": "html"
    },
    "tailwindCSS.emmetCompletions": true,
    "window.zoomLevel": -1,
    "eslint.format.enable": true,
    "editor.codeActionsOnSave": {
      "source.fixAll.eslint": true
    },
    "[javascript]": {
        "editor.defaultFormatter": "dbaeumer.vscode-eslint"
    }
}
```