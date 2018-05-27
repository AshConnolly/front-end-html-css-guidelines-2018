# Front end guidelines 2018

### Why have guidelines?

- Disorganised CSS structures make future updates very time ineffective and difficult
- Updating poorly structured CSS can lead to unexpected style changes across the website, which may even break layout or prevent functionality.
- Clear CSS structures speed up development
- Guidelines help get multiple developers work in the same way to keep code bases consistent, making it easier for developers to move projects.

**Main objectives of CSS:**

- Component driven
- Object Orientated
- Maintainable

The CSS should aim to be object orientated to create reusable, flexible classes which make future updates easy and logical. Utilise component based css inline with an atomic design methodology.


### Principles for scalable front end development

- Dry / single source of truth - mixins and variables for easier maintenance.
- The single responsibility principle - flexible atomic-like classes.
- Separation of concerns - do not couple markup and HTML, this creates inherent fragility.
- Immutability - utility classes should not be mutated, split into two classes if needed.
- Cyclomatic Complexity - specificity is like if statements, don't nest deeply, keep it simple.
- The open/closed principle - 'open for extension, closed for modification', don't change legacy code directly, instead use modifiers.
- Orthogonality - using component based CSS means components can be moved around with consistent results, creating a flexible modular system.

These are taken from the Harry Roberts' talk '[CSS for Software Engineers for CSS Developers](https://speakerdeck.com/csswizardry/css-for-software-engineers-for-css-developers)'.

### Why used component based CSS?

When building interfaces we are creating a system of elements and components that will be used in various combinations. If we keep this in mind we can create a truly fluid and flexible interface system - much like the [Brad Frost - Atomic Design](https://vimeo.com/67476280) approach. I've written about how atomic / component driven design can transition easily into component based css for better, easier development, see the article [here](https://medium.freecodecamp.com/a-more-seamless-workflow-style-guides-for-better-design-and-development-639fc55be28c#.2sd1cbhc6).

### scss-lint config adjustments 

```
linters:  
    DuplicateProperty:

     enabled: false # We have fall backs with duplicate tags

Indentation:  
     enabled: true # 4 spaces of indentation  
     width: 4

SelectorDepth:  
     enabled: true # 4 deep selector  
     max_depth: 4

NestingDepth:  
     enabled: true # 4 deep selector  
     max_depth: 4

PropertySortOrder:  
     enabled: false # don't order properties alphabetically

NameFormat:  
     enabled: true # make vars etc use small-hyphen-case  
     convention: hyphenated_lowercase

# new changes below

QualifyingElement:  
    enabled: false # allow qualifying of attributes and classes  
   # we should avoid naming tags as it adds unnecessary specificity. Eg - div.thing {colour:red}  
   # Quasi qualified sectors are ok for readability sake e.g. - /*ul*/.list

SingleLinePerProperty:  
    true # better readability

StringQuotes:  
    enabled: true  
    style: single_quote #faster to type than double quote

SelectorFormat:  
    enabled: true  
    convention: hyphenated_BEM # to parse CSS and alert and non BEM syntax
```

### Nesting and specificity

Specificity can cause issues when working with the cascade, but if component nesting is kept as low as possible, no more than 4, and clear, it will cause minimal problems.

BEM approach encourages having a unique class for every element, keeping all elements at the root of the cascade, eliminating any cascade entirely.

### Context vs component / element styles

Implementation styles are based on context, visual styles are based on component. Root component styles should be based on what they are - not where they are. Aesthetic styles should be based on the component. Implementation code should be based on their context.

For example, if a button need to be floated right in a component, like the header, it is due to being inside the header. The root button styling doesn’t need modifying. Simply reference the button inside of the component class and add the implementation styles.

```
.c-site-header {
	...
 	.c-button {float:right;}
 	...
}
```


### The interface is a flexible design system 

Rules applied to nested components via child or descendant selectors should never be aesthetic changes, only implementation / positioning changes.

For example, if a button component needs to have a rounded corner when found in another component, say a card component, you may style it like this - 

```
/* Bad */
.c-card {
	... 
 	.c-button {border-radius: 4px;}
 	...
}
```

But this makes updates and maintenance difficult. What if you want to use a rounded corner button elsewhere?

Instead a modifier would need to be added to the button component. This modifier can be used in any context and offers more flexibility in the UI - a flexible design system.

```
<button “c-button c-button--rounded”>Submit</button>
```

This gives complete control over all buttons in the UI.

### Content driven media queries - not device driven

In recent years it has been a firmly established practice to set your media queries at popular device sizes, iphone, ipad, etc. Whilst this means you are ensuring your components look great on the most popular devices, it inherently make these components somewhat inflexible, meaning their content is best represented outside of these popular devices.

The best way to determine when to add a media query is simple:

- design / build your component mobile first
- expand your component width / window width 
- when the component looks bad and the content could be presented better - add a media query.

**A media query should simply be added when the content could be better presented, not to suit a device.**   
**They should be component specific, not device specific.**

This will ensure that your interface system of components will work at any screen size, future proofing your interface.  We wont have the issue of updating our entire interface when Apple release a new device with a new device width. This could mean that a codebase has vastly more media queries, but these have a very minimal affect on performance. 

### Content agnostic class names

Component class names should not be tied to the content inside the component. For example using the the class name welcome_message would not be ideal. As that layout / component could be used elsewhere for other purposes a more appropriate class name would be message.

- Use meaningful names; use structural or purposeful names over presentational.

### Avoid use of IDs

IDs have the highest specificity of any CSS selector and as a result they cause issue with overwriting styles in the cascade. Avoid using any IDs. Sometimes developers will add an ID to an element for the sole purpose of referencing the ID in javascript. However this isn’t necessary, instead use a class and reference the element using `getElementsByClassName("class_name")[0];` (note `ClassName` has wider browser support than `ClassList`).

### Quasi-qualified selectors

```
ul.nav {}
```

Here we can see that the .nav class is meant to be used on a ul element, and not on a nav. By using quasi-qualified selectors we can still provide that information without actually qualifying the selector

```
/*ul*/.nav {}
```

### CSS structure

The proposed CSS structure is based on the ‘Inverted Triangle’ CSS structure created by Harry Roberts. 

The biggest issue faced with writing scalable CSS is keeping the styles organised and maintainable. ITCSS organises the css into layers of specificity to avoid these issues.

[http://www.creativebloq.com/web-design/manage-large-css-projects-itcss-101517528](http://www.creativebloq.com/web-design/manage-large-css-projects-itcss-101517528)

[http://csswizardry.net/talks/2014/11/itcss-dafed.pdf](http://csswizardry.net/talks/2014/11/itcss-dafed.pdf)

[https://github.com/itcss/itcss-netmag](https://github.com/itcss/itcss-netmag)

All partials are split into the groups / folders, and listed in a specific, reverse specificity order when the partials are brought into the main.css file. Please note I have modified the original structure, as I found what is considered an 'object' is slightly unclear and overlaps with components. [Brad Frost agrees in this respect](https://twitter.com/AshConnnolly/status/806815418950225920).


- **Settings**: Global variables, config switches, [base sizing units](https://builttoadapt.io/intro-to-the-8-point-grid-system-d2573cde8632)
- **Tools**: Default mixins and functions. 
- **Generic**: Ground-zero styles (Normalize.css, resets, box-sizing, device specific resets + fixes). 
- **Elements:** body, html. Font styling, global margins (vertical rhythm). if any use of classes is used it is only 1 level deep, eg 'h1, .h1'.
- **Layout**: Cosmetic-free design patterns, grids, wrappers, content width etc.
- **Components**: Designed components, chunks of UI. 
- **Fallbacks:** Browser specific styles to fix inconsistencies (this may not be needed)
- **Utilities**: Helpers and overrides

**Front End Folder structure**
```
|- src/  
    |- img/   
    |- js/  
    |- scss/  
        |- settings/  
        |- Tools/  
        |- Generic/  
        |- Elements/  
        |- Layout/  
        |- Components/    
        |- Fallbacks/    
        |- Utilities/  
|- dist/  
    |- css/  
        |- main.css  
    |- js/  
    |- img/   
    |- fonts/  
    |- index.html  
|- gulpfile.js  
|- node_modules/  
|- package.json  
 
```
The src and dist folders represent source and distribution. The source folder contains all assets used for the development environment and the distribution folder contains all the files served to the user. This is a common folder structure among app development and helps keep feels organised. The images folder is also held in the same structure. This allows us to have control over image size as we can process all site images with one gulp task and output them to the dist folder. This is hugely beneficial in meeting performance budgets and increasing site speed. 

**SCSS folder in more detail:**
```
- settings/  
    |-  variables.scss  
- Tools/  
    |- mixins.scss  
    |- functions.scss - sass functions  
- Generic/  
    |- normalize.scss  
    |- generic.css - resets, box-sizing  
- Elements  
    |- elements.scss - body html etc  
    |- fonts.scss - p, h1-h6, ul, ol,   
- Layout/    
    |- layout - grids partials   
- Components/    
    |- header.scss  
    |- footer.scss  
    |- button.scss  
    |- nav.scss  
    |- hero-banner.scss  
- Fallbacks /    
    |- fallbacks.scss  
    |- shame.scss  
- Utilities/  
    |- utilities.scss - u-float-left, u-float-right, u-absolute-vertical-center.
```
**Note: Shame.scss**

[http://csswizardry.com/2013/04/shame-css/](http://csswizardry.com/2013/04/shame-css/)

> The idea of shame.css is that you have a totally new stylesheet reserved just for your hacky code. The code you have to write to get the release out on time, but the code that makes you ashamed. By putting your bodges, hacks and quick-fixes in their own file you do a few things:
> 
> - You make them stick out like a sore thumb.
> - You keep your ‘main’ codebase clean.
> - You make developers aware that their hacks are made very visible.
> - You make them easier to isolate and fix.
> - $ git blame shame.css.

Perhaps a 'FIXME' in the code is a better option. Dev's can then search for 'FIXME' when they have time to improve the site.

### Naming system - Block Element Modifier

BEM is a CSS naming system to help create scalable and maintainable CSS.

It offers explicit naming to help developers quickly understand the purpose of class names and the roles they play. 

It is designed for scalable CSS, not to be pretty - the only criticism is slightly bloated html. If we tried to avoid bloated html we would move more styling into CSS - but difficult CSS is much harder to manage than having lots of class names - markup is easy, css can be very complex.

**BEM element names do not reflect the block’s structure**. Regardless of nested levels within, it’s always just the block name and the element name (so, never `.c-card__header__title`), instead do `.c-card__title` - this offers more reusability.
```
.c-card /* Block */
    .c-card__header /* Element */
    	.c-card__title /* Element */
	.c-card--fullwidth /* Modifier */

<div class="c-card c-card--fullwidth">
    <div class="c-card__header">
        <h2 class="c-card__title”>Title text here</h2>
    </div>
</div>
```

Note that elements should only ever have 1 component class. incorrect - 
```
<div class="c-card c-icon">', correct - '<div class="c-card"><div class="c-icon">
```

[Sass supports shorthand BEM](https://gist.github.com/AshConnolly/96e7e1ad044517d82d0d771437fadaaa)   
Linting also supports BEM.   
Gulp scss-lint setting would be like so:
```
NameFormat:
    enabled: true  # make vars etc use small-hyphen-case  
    convention: hyphenated_BEM
```

**Modifiers**


If a modifier only affects one element, it can be applied directly to a child element, see the `c-card__title--large` - 

```
<div class="c-card c-card--fullwidth">
    <div class="c-card__header">
        <h2 class="c-card__title c-card__title--large”>Title text here</h2>
    </div>
</div>
```

If a modifier affects more than one element, it can be applied alongside the root component class. 

In the example below, assume the default style would put the image on the right and the header text on the left. 

The modifier would reverse this layout. Both the header and image element are affect by this modifier.

```
<div class="c-card c-card--fullwidth c-card--image-left">
    <div class="c-card__header">
        <h2 class="c-card__title c-card__title--large”>Title text here</h2>
    </div>
    <img class="c-card__image” src=“…”>
</div>
```

### Class name prefixes 

Prefixing class names allows us to put our CSS classes into explicit categories. 

This is extremely helpful for clarifying the role of each class and makes it easy to decipher an application’s codebase at a glance. 

It also helps breaks down ui elements into an established design system.

- `c-` for components
- `l-` for layout based classes, like grids and wrappers
- `u-` for utility classes, that are immutable* 
- `js-` for js hooks - `<button class="c-btn js-open-modal">Open</button>`
- `is-` for state changes e.g. `.c-menu .is-open’ classes should be used for state, not data attributes
- `tt-` is used for automation testing. These classes may or may not be unique `<button class="c-btn js-submit-form tt-sign-in-page-submit">Submit</button>`

*utility classes being immutable mean that their properties will not change or be overwritten by standard classes. Only another utility class can overwrite an existing utility class, usually only when using responsive utility classes. If a change is required its likely the styling should be based inside a component.

*Utility classes and component classes should never be used together. If this is the case then styling for a component would be relying on a utility class and go against the component based nature of the UI.

These prefixes can be particularly helpful in allowing us to easily find where a particular or element type (component, layout etc) is used across the entire site and it allows us to see all iterations in any one page, or template by searching for the prefix (eg `c-` can show us all components). We can even use these prefixes to style all instances of a specific element type to help break up the ui.

```
[class^='c-'] { border:1px solid red;} //style all components with red outline  
[class^='l-'] { border:1px solid green;} //style all layout elements with green outline  
[class^='js-'] { border:1px solid blue;} //style all js driven elements with blue outline
```

### Encapsulated, structured, component based CSS 

CSS is to be structured in modular, component based blocks. This means that all styles for individual components are kept in one place for easier edits and maintainability. Once definition of a component across the whole project. It also mains styles are sandboxed so that unexpected behaviour, and styles impacting other components is kept to a minimum. 

example element - 
```
<div class="c-card c-card--fullwidth>
    <div class="c-card__header">
        <h2 class="c-card__title c-card__title--large”>Title text here</h2>
    </div>
</div>
```

- [1] Class name variable comes first. This is to allow easy future updates if the class name were to change. Naturally this class would need to be update elsewhere in the project, but in the component it should only need updating on the first 2 lines, then all changes will cascade down. [See here for example](https://gist.github.com/AshConnolly/375b408b60ddd4c4d1840dadb54d69a2)
- [2] Local variables and mixins specific only to the component. If these are used in any other component they are a global variable / mixin and should be moved to the respective file.
- [3] Root component properties come first
- [4] Then child element selectors using &__ to select child but using BEM selection
- [5] Then modifier classes. Element modifiers can be nested within their element selector - see `&__title` with its child `&--large` for an example below.
- [6] Then any `@at-root` selectors (see below for an explanation) 
- [7] Then state changes
- [8] Then media queries last. The media queries should comprise nearly entirely of layout changes: padding, margin, width, height, position etc. Cosmetic styles should not change.

```
/*------------------------------------*\ 
    card
\*------------------------------------*/

.c-card {  
  $class: c-card; // [1]  
  
  // local mixins + variables [2]  
  $box-height: 300px;  
  @mixin example {  
    box-shadow:$box-shadow;   
    // more styles here  
  }  
  
  // root styles [3]  
  background:green;  
  width:33%;   
  float:left;  
  height: $box-height;  
   
  // child styles [4]  
  &__header {  
    color:green;  
  }

    
  &__title {  
    font-size:22px;  
    &--large {  
      font-size:28px;  
    }  
  }  
  
  // modifiers [5]  
  &--fullwidth {  
     width:100%;  
     .#{$class}__header {color:blue} // [1]  
  }  
  
  // root changes [6]  
  @at-root.hide-cards .#{$class} {display:none;}  
 

  // state changes [7]  
  &.is-active {  
    background:lightgreen;  
  } 

  
  &.is-hidden {  
    display:none;  
  } 

  // media queries [8]  
  @media (min-width:500px) {  
    width:25%;  
  }  
    
  @media (min-width:1000px) {  
    &__header {  
      margin-top:30px  
      &--right {  
        padding-right:25px;  
      }  
    }  
    &--fullwidth {  
     width:90%;  
    }  
  }  
}
```

See a compiled version here - [https://gist.github.com/Ashffrees/a71bbeae0bc58db2e43a6304b7842400](https://gist.github.com/Ashffrees/a71bbeae0bc58db2e43a6304b7842400)

### Using `@at-root` for component encapsulation

If a component changes it's properties based on another element up the DOM tree receiving a change, like a new class, you need to manage those styles carefully. A good example is a menu opening when a class is added to the html. A good way to manage this is using the `@at-root` selector. This brings the current selection back to the root of the document, the html tag.

```
.c-menu {  
    display:none;  
    /*html*/@at-root /*body*/.menu_open .c-menu {  
         display:block;  
    }  
}
```
### CSS Titles

All component titles are listed as:
```
/*------------------------------------*\ 
	Component Title
\*------------------------------------*/
```
All file titles are listed as:

```
/*------------------------------------*\

	File Title

\*------------------------------------*/
```

The double hash is an identifier to help use find components quickly when scanning through files. 

### Declaration order

No specific declaration order is needed, see below for reasoning. CSS properties are sandboxed to their respective scope so aside form readability their order is irrelevant. Also any duplicate properties (and thus overwritten) will be alerted during linting. 

Resources:
[https://css-tricks.com/poll-results-how-do-you-order-your-css-properties/](https://css-tricks.com/poll-results-how-do-you-order-your-css-properties/)
[http://codeguide.co/#css-declaration-order](http://codeguide.co/#css-declaration-order)

### Z-Index variable table

To help keep z-index values manageable, its a good idea to store z-index values as variables kept neatly in your variables partial.

```
/*------------------------------------*\   
 ## z-index table  
\*------------------------------------*/  
/* 10 */   
/* 20 */   
/* 30 */   
/* 40 */ $z-site-header:40;  
/* 50 */ $z-menu:50;  
/* 60 */   
/* 70 */   
/* 80 */ $z-alert:50;  
/* 90 */   
/* 100 */$z-modal:100;
```

They could also be stored as in a mixin or in a function, see here - [https://www.sitepoint.com/better-solution-managing-z-index-sass/](https://www.sitepoint.com/better-solution-managing-z-index-sass/).

This also means you can use z-index values within an element like so.

```
z-index: $z-header + 1;
```

to maintain an organised z-index system when adjusting nested z-index values.

### Interface interactions - hierarchy of difficulty

When creating complex user interactions, such as behavioural changes we should aim to make the code as readable, maintainable and reusable as possible. Javascript can be very complicated, CSS is simpler, HTML / markup is simpler still. We should first try to achieve our object in pure HTML and CSS. If needed we will need to use JS / jQuery to meet the interaction requirements. First we should try only adding and removing classes via JS / jQuery to reach the desired outcome. If the desired result is not met we will sometimes need to change styles via JS / jQuery. 

Order of difficulty (from least complex, to most):

- HTML (mark up)
- CSS
- JS / jQuery

We should work in this order to keep our codebase as clean and easy to work with as possible.

### Semantic HTML

Use appropriate semantic elements where possible. If unfamiliar with semantic HTML please see here - [HTML5 Semantics – Smashing Magazine](https://www.smashingmagazine.com/2011/11/html5-semantics/)

### Gulp additions

Autoprefixer - no need for mixins, quicker to write, can also set it to be 2 versions back

Browser sync - watches for file changes and will refresh the page when a change occurs. It will automatically inject CSS changes without the need to reload the page. Browser sync is also a great testing tool as multiple devices can be tested simultaneously. Scrolling and click events are mirrored across multiple devices allowing you to see debug and spot errors quickly (the devices just need to be on the same network), 

MQpacker - concatenates media queries for smaller CSS [https://github.com/hail2u/node-css-mqpacker](https://github.com/hail2u/node-css-mqpacker)

### Additional best practices

- no extends - they often cause CSS bloat - [http://csswizardry.com/2016/02/mixins-better-for-performance/](http://csswizardry.com/2016/02/mixins-better-for-performance/) - instead use mixins
- mixins are used for any repeated strings (like font management) for easier up dates and maintainability due to single source of truth principle / DRY programming
- Chaining variables for context:
```
    $blue:#00338d; 

    $highlight: $blue;     

    .title {$highlight}
```
