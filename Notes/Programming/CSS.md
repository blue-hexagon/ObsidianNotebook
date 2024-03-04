# CSS Fundamentals

## Precedence
Internal stylesheets (ones that are defined within a `<style>` element) does not take precedence over linked stylesheets (`<link rel="stylesheet" href=".."></link>`).
Whichover is active depends on whichever has the highest specificity and if two selectors have the same specificity, the last one defined takes precedence.

Inline styles (`<h1 style="color: blue">`Hello World`</h1>`) does take precende over both the linked and the internal stylesheet.
## Specificity & Hierarchy of Precedence
Each CSS selector has a precise place in the hiearchy of precedence and if two selectors have the same precende level, the last one defined overwrites the latter (aka. taking precedence).
```
p               = 1
p .class        = 11
p .class #id    = 111
.class          = 10
#id             = 100
body p span#id  = 103
body .class #id = 111
```

  
![billede](https://user-images.githubusercontent.com/26361520/187608751-0860f785-0bef-4e14-9ead-6fae6146344b.png)

## Inheritance
Anything related to font, typography and color is inherited and everything else is not.
An exception to this however is form elements which do not typically inherit font-settings.
```css
body {
  font-size: 22px;
}

p {
  /* inherits body's fontsize */
} 

button, input, textarea, select {
  /* Does not inherit fontsize */ 
  /* To manually enable inheritance of font settings */
  font: inherit;
}
```

## Selectors
Look for `<span>` elements nested inside a `<p>` elements.
```css
p span {
  color: blue;
  text-transform: uppercase;
}
```
  
## Colors
```css
color: rgb(0,255,0) /* Completely green */
color: rgba(0,255,0,0) /* Completely transparent */
color: rgba(0,255,0,1) /* Completely green */
color: rgba(0,255,0,0.5) /* 50% translucent green */
color: #00FF00 /* Equal to: #0F0 */
```
  
## Units
  `em` is a multiplier based off of the parent element.
  `rem` is a multiplier based off of the root element.
  `ch` is useful for setting a max character width of a paragraph.
  
## Typography
- font-family `/* Anytime the fontname has a space in it, you have to enclose it in quotes */`
- text-decoration
- line-height
- letter-spacing
- word-spacing
- font-weight
- font-style

### Lists
- list-style
- list-style-type
- list-style-position: [outside, inside];
- list-style-image: url('...);

### Remove Decoration
```css
ol {
  list-style-type: none;
  padding: 0;
}
```
### Modify Decoration
```css
::marker {
  color: red;
  font-family: fantasy;
  font-size: .2rem;
  content: ">>"; /* This replaces the bullet point or whatever other decoration you have chosen */
}
```
## Pseudo Classes
Pseudo-classes represent the current state of an element (i.e. visited as in below).
```css
a:visited {
  ...
}
```
The state of an anchor element can change, because it has either been visited or it hasn't.
## Pseudo Element
Instead of staring with `:` the pseudo-element starts with `::`
## Position
* Use as: `position: [static|relative|absolute|fixed|sticky];
* Use the `z-index` property to change the order of overlapping elements.
### Static
The default. It's already there and wont change anything.
### Relative
* Doesn't change anything on it's own.
* Need to set either: `top`, `bottom`, `left` or `right` along with the position property.
* Positions itself, relatively against it's parent container.
### Absolute
* Abosoulute positioning needs a relative-positioned parent to position itself against.
  * If it doesnt have an ancestor element that is positioned relatively, then it positions itself against the dimensions of the browser window.
  * Picks it closest (nearest) relatively positioned parent.
* Doesn't change anything on it's own.
* Need to set either: `top`, `bottom`, `left` or `right` along with the position property.
* Can be used as an hack for keeping elements in the DOM tree which are needed for accessibility but not for display (e.g.: 'position...; left: -10000px;')
### Fixed
* Positions itself against the browser's dimensions.
* Stays in place when scrolling the window (hence the 'fixed')
### Sticky
* Just a litte diffrent than fixed.
* Need to set either: `top`, `bottom`, `left` or `right` along with the position property.
  * Most often used is 'top' (makes sense).
* Will stay in its normal flow, until it reaches a spot (e.g. 'top: 0;') you have defined.
## Display
### none
Used to hide the element(s).
### Float
Can be used to wrap text around an element.
### Inline
* Inline elements do not stack on top of each other when nested and they do not create a new line.
* They also only take up the width of their content.
* We can't apply top-margin, bottom-margin or height to an inline element
* We can apply padding though! Padding does however not respect the parent element and overflows it's parents renderbox.
* We can apply padding (in all directions) and set a width
### Inline-Block
* On inline-blocks we can apply margin and padding as usual.
* Doesn't overflow (but respects) its parent when applying margin, padding or size.
  * It's parent will resize when necessary.
* Inline block is great for Navs
### Block
* Block elements stack on top of eachother when nested and always create a new line.
* Block level elements automatically have 100% width of their parent.
### Flex
Defaults to left-to-right with wrapping
TODO
* gap
* justify-content (horizontal alignment/spacing)
* align-items (vertical alignment)
* aling-content (vertical alignment/spacing) 

### Inline-Flex
TODO

## UI Miscellaneous
### Columns
```html
<section class="columns">
  <p>...</p>
  <p>...</p>
  <p class="quote">... - Author</p>
  <h2>...</h2>
  <p>...</p>
  <p>...</p>
</section>
```
```css
.columns {
  column-count: 4;
  column-width: 250px; /* Minnimum width -  it'll never go below that. It'll scale the column count down or up */
  column-rule: 3px solid #333;
  column-gap: 2rem;
}

.columns p {
  margin-top: 0; /* Remove top-margin on first column*/
}

.columns h2 {
  background-color: #ccc;
  break-inside: avoid; /* Prevents the background of the block element from breaking unto two columns */
  break-before: column; /* Makes sure the header is not shown at the end of a column */
}

.columns .quote {
  margin-top: 2rem;
  column-span: all;
}
```
