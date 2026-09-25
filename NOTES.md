# Advanced CSS Course Notes

Course-wide learning notes from **Advanced CSS and Sass: Flexbox, Grid, Animations and More!**

These notes focus on CSS concepts, design principles, and development practices that apply across the course projects rather than implementation details specific to one project.

The course organizes good HTML/CSS development around three main pillars:

## Three Pillars of Good HTML and CSS

### 1. Responsive Design

Building layouts that adapt properly to different screen sizes and devices.

Topics include:

- Fluid layouts
- Media queries
- Responsive images
- Correct units
- Desktop-first vs. mobile-first design

### 2. Maintainable and Scalable Code

Writing HTML and CSS that remains understandable, reusable, and manageable as a project grows.

Topics include:

- Clean code
- Easy-to-understand code
- Designing for growth
- Reusable code
- How to organize files
- How to name classes
- How to structure HTML

### 3. Web Performance

Building pages efficiently so they load and run well.

Topics include:

- Fewer HTTP requests
- Less code
- Compressing code
- Using a CSS preprocessor
- Using fewer images where appropriate
- Compressing images

## How the Browser Processes HTML and CSS

### High-Level Model

```text
HTML
 ↓
Parse HTML
 ↓
DOM
  \
   \
    +----> Render Tree ----> Layout / Visual Formatting ----> Rendered Page
   /
  /
CSS
 ↓
Parse CSS
 ↓
Cascade
 ↓
Process Final CSS Values
 ↓
CSSOM
```

- **DOM** = browser representation of the HTML structure.
- **CSSOM** = browser representation of the processed CSS.
- DOM + CSSOM are used to create the **render tree**.
- The render tree is then used for layout and visual rendering.

### Loading CSS

When the HTML parser encounters:

```html
<link rel="stylesheet" href="css/style.css">
```

the browser can begin loading and parsing the CSS while HTML parsing continues.

```text
HTML parsing
     ↓
Find stylesheet
     ↓
Load / parse CSS
```

HTML and CSS processing can therefore overlap rather than happening as two completely separate steps.

### The Cascade

Multiple CSS declarations can apply to the same property:

```css
p {
    color: blue;
}

.text {
    color: red;
}
```

The **cascade** determines which declaration wins.

Factors include:

- source of the declaration
- specificity
- source order
- other cascade rules

Mental model:

> **Cascade = decide which CSS value wins.**

### Processing Final CSS Values

After the cascade determines which declaration wins, the browser still has to turn that winning CSS value into something it can actually use for layout and rendering.

For many layout-related properties, this means resolving percentages and CSS units into concrete usable measurements, often CSS pixel values.

Different values need different information from the current page before they can be resolved:

- `%` depends on another relevant dimension, such as the size of a containing block.
- `em` depends on a font size.
- `rem` depends on the root element's font size.
- `vh` and `vw` depend on the viewport dimensions.
- `px` is already expressed as a CSS pixel, so it does not need the same kind of relative calculation.
- inherited values may depend on values coming from parent elements.
- some values depend on the surrounding layout and available space.

The browser resolves these values using the context of the current page so they can be used for layout and rendering.

For example:

```css
margin-left: 50%;
```

`50%` is not one fixed number of pixels. The browser has to calculate what `50%` means based on the relevant containing block and available space.

```text
Cascade
   ↓
margin-left: 50% wins
   ↓
Browser evaluates what 50% means in the current layout
   ↓
Concrete usable value
   ↓
For example: 320px
```

That final pixel amount can change depending on the available space, so the same `50%` declaration may produce a different result on a phone, tablet, laptop, or desktop.

Not every CSS value literally becomes pixels, but for many layout calculations the browser eventually needs concrete dimensions and positions it can use to draw the page.

Mental model:

> **Cascade = which value wins.**  
> **Final value processing = turn that winning value into something the browser can actually use for layout and rendering.**

### CSSOM

**CSSOM** stands for **CSS Object Model**.

```text
HTML → DOM
CSS  → CSSOM
```

- **DOM** = page structure.
- **CSSOM** = processed styling information.

CSSOM was a new term even though the DOM was already familiar.

### Render Tree

```text
DOM + CSSOM
     ↓
 Render Tree
     ↓
 Layout
     ↓
 Rendered Page
```

The render tree combines the relevant page structure and styling information needed to visually lay out and render the page.

### CSS Processing Pipeline

```text
Load CSS
   ↓
Parse CSS
   ↓
Resolve conflicts with the cascade
   ↓
Process final CSS values
   ↓
CSSOM
   ↓
Combine with DOM
   ↓
Render Tree
   ↓
Layout / Rendering
```

### How the Cascade Chooses a Winning CSS Declaration

The **cascade** is the process the browser uses to resolve conflicts when more than one CSS declaration applies to the same property on the same element.

A useful mental model is:

```text
Multiple declarations apply
        ↓
Browser needs a winner
        ↓
Cascade resolves the conflict
        ↓
1. Importance
        ↓
2. Specificity
        ↓
3. Source Order
```

Each step is only needed if the previous step does not already determine a winner.

---

#### 1. Importance

The browser first compares the **importance** of the competing declarations.

The course presents the basic importance order as:

```text
Highest priority
      ↓
User !important declarations
Author !important declarations
Author declarations
User declarations
Default browser declarations
      ↓
Lowest priority
```

**Author declarations** are the CSS written for the website.

`!important` can be added to a declaration to give it a higher level of importance:

```css
.button {
    background-color: blue !important;
}
```

This can allow that declaration to beat a normal declaration with a more specific selector:

```css
#nav .pull-right .button {
    background-color: green;
}
```

Even though the second selector is more specific, the first declaration is `!important`, so importance is resolved before specificity is considered.

Mental model:

> **Importance is checked before asking which selector is more specific.**

---

#### 2. Specificity

If competing declarations have the same importance, the browser compares their **specificity**.

Specificity measures how specifically a selector identifies an element.

The course represents specificity using four values:

```text
(Inline, IDs, Classes, Elements)
```

The categories are compared from strongest to weakest:

```text
Highest weight
     ↓
Inline styles
IDs
Classes, pseudo-classes, attributes
Elements, pseudo-elements
     ↓
Lowest weight
```

A useful way to visualize the specificity of several selectors is:

```text
+--------------------------------+--------+-----+---------+----------+----------------+
| Selector                       | Inline | IDs | Classes | Elements | Specificity    |
+--------------------------------+--------+-----+---------+----------+----------------+
| .button                        |   0    |  0  |    1    |    0     | (0, 0, 1, 0)   |
+--------------------------------+--------+-----+---------+----------+----------------+
| nav#nav div.pull-right .button |   0    |  1  |    2    |    2     | (0, 1, 2, 2)   |
+--------------------------------+--------+-----+---------+----------+----------------+
| a                              |   0    |  0  |    0    |    1     | (0, 0, 0, 1)   |
+--------------------------------+--------+-----+---------+----------+----------------+
| #nav a.button:hover            |   0    |  1  |    2    |    1     | (0, 1, 2, 1)   |
+--------------------------------+--------+-----+---------+----------+----------------+
```

The simplest examples show how the score is built:

- `.button` contains one class, giving `(0, 0, 1, 0)`.
- `a` contains one element selector, giving `(0, 0, 0, 1)`.

More complex selectors are counted the same way.

For example:

```css
nav#nav div.pull-right .button
```

contains:

- 0 inline styles
- 1 ID: `#nav`
- 2 classes: `.pull-right` and `.button`
- 2 elements: `nav` and `div`

giving:

```text
(0, 1, 2, 2)
```

Another selector:

```css
#nav a.button:hover
```

contains:

- 0 inline styles
- 1 ID: `#nav`
- 2 class-level selectors: `.button` and `:hover`
- 1 element: `a`

giving:

```text
(0, 1, 2, 1)
```

The specificity values should be treated as a **comparison structure**, not as numbers that are simply added together.

The browser compares the values from left to right:

```text
Inline
  ↓ tie?
IDs
  ↓ tie?
Classes
  ↓ tie?
Elements
```

For example:

```text
              Inline   IDs   Classes   Elements
                 ↓      ↓       ↓         ↓
First:          (0,     1,      2,        2)
Second:         (0,     1,      2,        1)
                 =      =       =         ↑
                                      2 wins
```

So:

```text
(0, 1, 2, 2)  ← wins
(0, 1, 2, 1)
```

The first three categories are tied, so the browser reaches the element count. Because `2` is greater than `1`, the first selector has the higher specificity.

Mental model:

> **Specificity is compared category by category from strongest to weakest, not by adding everything into one total score.**

---

#### 3. Source Order

If two declarations have the **same importance and the same specificity**, the browser finally uses **source order** as the tiebreaker.

```text
Same importance?
       ↓
Same specificity?
       ↓
Compare source order
       ↓
Later declaration wins
```

For example:

```css
.button {
    color: blue;
}

.button {
    color: red;
}
```

Both declarations have the same importance and specificity, so:

```css
color: red;
```

wins because it appears later in the source code.

---

#### Cascade Decision Mental Model

```text
Multiple declarations affect the same property
                    ↓
             Compare importance
                    ↓
               Same level?
                    ↓
            Compare specificity
                    ↓
               Same specificity?
                    ↓
            Compare source order
                    ↓
          Later declaration wins
```

Or more simply:

> **Importance → Specificity → Source Order**

This explains why CSS rules sometimes appear to "override" one another: the browser is following a defined conflict-resolution process rather than choosing randomly.

> **Modern CSS note:** This is the core cascade model being taught in the course. Modern CSS also includes additional cascade features such as cascade layers, plus special precedence rules for animations and transitions. Those can be added to these notes when they become relevant.

#### Cascade and Specificity Summary

Key practical rules from the cascade and specificity model:

- `!important` gives a declaration very high priority, but it should generally be used as a **last resort** rather than as the normal way to solve specificity problems.
- Prefer writing selectors with the appropriate specificity instead of fighting the cascade with `!important`.
- Inline styles have very high specificity compared with normal stylesheet rules. Since we rarely write inline styles, this usually matters less in practice than it first sounds.
- Specificity categories do **not** combine into one total number:
  - 1 ID beats any number of classes.
  - 1 class beats any number of element selectors.
- The universal selector `*` adds no specificity:

```text
* → (0, 0, 0, 0)
```

- Prefer using clear specificity rather than relying on source order to make rules work.
- Source order should mainly act as the final tiebreaker when importance and specificity are equal.

A useful overall mental model is:

```text
Write selectors deliberately
        ↓
Let specificity express which rule should win
        ↓
Use source order only when specificity is tied
        ↓
Avoid !important unless there is a strong reason
```

> **Modern CSS note:** The course recommends loading your own stylesheet after third-party stylesheets so your rules can override equally specific rules through source order. That still works, but modern CSS also provides **cascade layers (`@layer`)**, which can give much more explicit control over stylesheet priority without depending entirely on load order.

##### Practical Specificity Example: When `:hover` Still Loses

A pseudo-class such as `:hover` does not automatically override the element's normal styling.

For example:

```css
#nav div.pull-right a.button {
    background-color: orangered;
}

#nav a.button:hover {
    background-color: yellow;
}
```

When the button is hovered, **both selectors match**.

Their specificity values are:

```text
#nav div.pull-right a.button
(0, 1, 2, 2)

#nav a.button:hover
(0, 1, 2, 1)
```

The hover selector includes `:hover`, which counts at the class/pseudo-class level, but the normal selector contains one additional element selector (`div`).

The first three specificity categories are tied:

```text
              Inline   IDs   Classes   Elements
Normal:         0       1       2         2
Hover:          0       1       2         1
                =       =       =         ↑
                                      Normal wins
```

So even while the button is hovered, the `orangered` declaration still wins.

The hover rule can be made more specific:

```css
#nav div.pull-right a.button:hover {
    background-color: green;
}
```

Its specificity becomes:

```text
(0, 1, 3, 2)
```

which is more specific than the normal rule:

```text
Normal: (0, 1, 2, 2)
Hover:  (0, 1, 3, 2)  ← wins
```

Mental model:

> **A state such as `:hover` does not automatically win. It still participates in the cascade like any other selector and must have enough specificity to override the competing declaration.**

## CSS Value Processing

Before a CSS value is actually rendered on the page, it can pass through several processing stages.

```text
Declared value
      ↓
Cascaded value
      ↓
Specified value
      ↓
Computed value
      ↓
Used value
      ↓
Actual value
```

## CSS Value Processing — Key Rules

- Every CSS property has an **initial value**, used when nothing is declared and no value is inherited.
- Browsers provide a root `font-size` for the page, commonly `16px`.
- Percentages and relative units are resolved using the value they depend on.
- `%` used for `font-size` is relative to the **parent's computed `font-size`**.
- In the course's length example, `%` is measured relative to the **parent's computed width**.
- `em` used for `font-size` is relative to the **parent's computed `font-size`**.
- `em` used for lengths is relative to the **current element's computed `font-size`**.
- `rem` is relative to the **document root's computed `font-size`**.
- `vh` and `vw` are based on percentages of the viewport's height and width.

A compact reference:

```text
% font-size  → parent's computed font-size
% length     → parent's computed width in the course example

em font-size → parent's computed font-size
em length    → current element's computed font-size

rem          → root computed font-size

vh           → viewport height
vw           → viewport width
```

> **Mental model:** A relative unit does not tell the browser a complete size by itself. It tells the browser **which other measurement to use as the reference for calculating that size**.

### 3. Specified Value

The **specified value** is the value the property will use after accounting for things such as defaulting when there is no cascaded value.

Every CSS property has an **initial value** that can be used if nothing is declared and there is no inherited value.

```text
+------------------+-----------------+-------------------+----------------+-------------------+---------------------+
| Processing Stage | Paragraph Width | Paragraph Padding | Root Font Size | Section Font Size | Paragraph Font Size |
+------------------+-----------------+-------------------+----------------+-------------------+---------------------+
| Declared         | 140px / 66%     | --                | --             | 1.5rem            | --                  |
| Cascaded         | 66%             | --                | 16px default   | 1.5rem            | --                  |
| Specified        | 66%             | 0px initial       | 16px           | 1.5rem            | 24px inherited      |
| Computed         | 66%             | 0px               | 16px           | 24px              | 24px                |
| Used             | 184.8px         | 0px               | 16px           | 24px              | 24px                |
| Actual           | 185px           | 0px               | 16px           | 24px              | 24px                |
+------------------+-----------------+-------------------+----------------+-------------------+---------------------+
```

This demonstrates several different parts of value processing at once:

- the cascade chooses `66%` instead of `140px`
- undeclared padding falls back to its initial value of `0px`
- the browser supplies a default root font size of `16px`
- `1.5rem` becomes `24px`
- the paragraph inherits the `24px` font size
- the percentage width remains `66%` until layout provides enough information
- the used width becomes `184.8px`
- the browser ultimately renders approximately `185px`

> **Mental model:** CSS values move from **what was written → what wins → what value applies → what that value means → what fits the layout → what can actually be rendered**.

## CSS Inheritance

Inheritance allows certain CSS property values to pass from **parent elements to their children**, which helps reduce repetition and makes CSS easier to maintain.

Properties related to text commonly inherit, including:

- `font-family`
- `font-size`
- `color`

The value that gets inherited is the property's **computed value**, not necessarily the value originally written in the CSS.

For example:

```css
body {
    font-size: 1.5rem;
}

If `1.5rem` computes to `24px`, child elements that inherit the font size receive the computed `24px` value.

Inheritance normally applies when the child does not declare its own value for that property.

Two useful keywords can control this behavior:

```css
color: inherit;