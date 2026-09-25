# Natours Project Learning Notes

Learning notes for the Natours project from the Udemy course **Advanced CSS and Sass: Flexbox, Grid, Animations and More!**

These notes focus on:

- Modern CSS concepts and layout techniques
- Why specific CSS approaches work
- Useful mental models and implementation details
- Differences between older course techniques and current CSS practices
- Git/project setup details worth remembering

The goal is to preserve useful learning breadcrumbs without turning this file into a full CSS reference.

## Project Start

### Basic CSS Reset

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;

}
```

- CSS length values of `0` do not require a unit. For example, `margin: 0` is equivalent to `margin: 0px`.
- Non-zero length values still require a unit, such as `10px`, `2rem`, or `50%`.
- `box-sizing: border-box` changes how an element's width and height are calculated.
- By default, CSS uses `content-box`, where the declared `width` and `height` apply only to the content. Padding and borders are added outside those dimensions.
- With `border-box`, padding and borders are included inside the declared `width` and `height`, which makes element sizing easier to predict.
- Putting `box-sizing: border-box` in the reset applies this sizing model across the whole page from the start, so adding padding or borders later does not unexpectedly make elements larger than the dimensions you set.

For example, with:

```css
width: 300px;
padding: 20px;
border: 5px solid black;
```

`content-box` produces a total width of `350px`, while `border-box` keeps the entire element at `300px`.

### Base Typography

```html
<link href="https://fonts.googleapis.com/css?family=Lato:100,300,400,700,900" rel="stylesheet">
```

- This allows us to use the Lato font from Google Fonts.

```css
body {
    font-family: "Lato", sans-serif;
    font-weight: 400;
    font-size: 16px;
    line-height: 1.7;
    color: #777;
}
```

- The `body` rule is being used to establish the default typography and text appearance for the whole site.
- `font-family` tells the browser which typeface should be used for text.
- It is set on `body` because `body` contains almost all of the visible content on the page. Setting the common typography here avoids repeating the same declarations on every heading, paragraph, link, and other text element.
- `font-family`, `font-weight`, `font-size`, `line-height`, and `color` are inherited properties. Child elements inside `body` will normally inherit these values unless another CSS rule overrides them.
- `"Lato"` can be used because it is loaded in `index.html` through the Google Fonts `<link>` above. That link downloads the font and makes it available for the CSS to use.
- The values `100`, `300`, `400`, `700`, and `900` specify which Lato font weights are being loaded. These control how thin or bold the text appears: `100` is very thin, `300` is light, `400` is normal, `700` is bold, and `900` is very bold.
- `font-weight: 400` sets the default text weight to Lato's normal weight.
- `font-size: 16px` establishes the default text size used by elements that inherit it.
- `line-height: 1.7` is unitless and means the line height is `1.7` times the element's font size. With a `16px` font size, that works out to `27.2px`.
- Because the line height is unitless, an element that later uses a different font size can still calculate its line height proportionally.
- `color: #777` establishes the default text color for elements that inherit it.
- `sans-serif` is the fallback font family. If Lato cannot be loaded, the browser will use an available sans-serif font instead.

### Viewport Height (`vh`)

`vh` is a relative CSS length unit based on the height of the browser's visible viewport.

```css
.header {
    height: 95vh;
}
```

- `vh` stands for **viewport height**.
- `1vh` equals `1%` of the viewport's height.
- `95vh` makes the element `95%` as tall as the visible browser window.
- Unlike a fixed value such as `950px`, a `vh` value changes as the viewport height changes, making it useful for large responsive sections such as hero headers.


### Clipping an Element with `clip-path`

```css
clip-path: polygon(0 0, 100% 0, 100% 75vh, 0 100%);
```

- `clip-path` defines which part of an element remains visible. Anything outside the defined shape is clipped away.
- `polygon()` creates that visible shape by defining a series of points around the element.
- Each point is written as an `x y` coordinate, measured from the element's top-left corner.
- The browser connects the points in the order they are written and then connects the final point back to the first point to form the completed shape.

The four points used here are:

1. `0 0` — top-left corner.
2. `100% 0` — top-right corner.
3. `100% 75vh` — right side of the header, `75vh` down from the top of the viewport.
4. `0 100%` — bottom-left corner of the header.

This produces a shape roughly like:

```text
0 0 -------------------- 100% 0
|                              |
|                              |
|                              |
|                        100% 75vh
|                         /
|                      /
|                   /
0 100% ------------
```

The last two points create the diagonal bottom edge. The right side ends higher at `75vh`, while the left side extends all the way to the bottom of the header.

Because the header itself is `95vh` tall, the point at `75vh` is above the bottom of the header. The area beneath that diagonal line is still part of the element, but `clip-path` prevents it from being visible.

That last distinction is important: clip-path isn't resizing the header or cutting the actual element apart. It is defining a visible mask over it.

#### Header Text Structure

```html
<div class="text-box">
    <h1 class="heading-primary">
        <span class="heading-primary-main">Outdoors</span>
        <span class="heading-primary-sub">is where life happens</span>
    </h1>
</div>
```

- `.text-box` wraps the entire heading, so positioning `.text-box` moves the main heading and subheading together as one unit.
- `<h1>` provides the semantic main heading for the page.
- The two `<span>` elements allow the two pieces of the heading to be styled independently while still belonging to the same `<h1>`.

#### Centering the Text Box

```css
.text-box {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```

- `top: 50%` and `left: 50%` move the **top-left corner** of `.text-box` to the center of its containing element.
- At this point the box itself is not centered — only its top-left corner is.
- `transform: translate(-50%, -50%)` then moves `.text-box` left by `50%` of **its own width** and up by `50%` of **its own height**.
- Because `.text-box` contains the entire `<h1>`, this centers the whole heading group together.

A useful mental model is:

**Move the element's corner to the center, then pull the element back by half of its own size.**

#### Main and Subheading Layout

```css
.heading-primary-main,
.heading-primary-sub {
    display: block;
}
```

- `<span>` elements are inline by default, so without `display: block` the two spans would normally continue on the same line when space allows.
- Setting each span to `display: block` makes each one take its own line.
- This lets `"Outdoors"` and `"is where life happens"` remain part of the same `<h1>` while being laid out and styled separately.

### CSS Animations with `@keyframes`

CSS animations are created in two parts:

1. Define what the animation does using `@keyframes`.
2. Apply that animation to an element using the `animation` properties.

#### Defining the Animation

```css
@keyframes moveInLeft {
    
    0%
    {
        opacity: 0;
        transform: translateX(-100px);
    }
    
    80%
    {
        transform: translateX(10px);
    }

    100%
    {
        opacity: 1;
        transform: translate(0);
    }
}
```

- `@keyframes` defines the stages of an animation.
- `moveInLeft` is the name given to the animation so it can be applied to an element later.
- The percentages represent points in the animation timeline:
  - `0%` is the starting state.
  - `80%` is a state near the end.
  - `100%` is the final state.
- `translateX()` moves an element horizontally without changing the surrounding document layout.
- A negative `translateX()` value moves the element left, while a positive value moves it right.
- At `0%`, the element starts invisible with `opacity: 0` and is shifted `100px` to the left using `translateX(-100px)`.
- At `80%`, the element moves `10px` past its final position using `translateX(10px)`. This creates a small overshoot effect rather than having the element stop immediately.
- At `100%`, the element becomes fully visible and returns to its original position with `translate(0)`.

The `moveInRight` animation uses the same idea in the opposite direction:

```css
@keyframes moveInRight {
    
    0%
    {
        opacity: 0;
        transform: translateX(100px);
    }
    
    80%
    {
        transform: translateX(-10px);
    }

    100%
    {
        opacity: 1;
        transform: translate(0);
    }
}
```

It begins `100px` to the right, slightly overshoots to the left, and then settles back into its original position.

#### Applying an Animation

The main heading applies the animation using separate animation properties:

```css
animation-name: moveInLeft;
animation-duration: 1s;
animation-timing-function: ease-out;
```

- `animation-name` selects which `@keyframes` animation should run.
- `animation-duration` controls how long it takes to move from `0%` to `100%`.
- `animation-timing-function` controls how the animation's speed changes while it runs.
- `ease-out` starts the animation relatively quickly and slows it down toward the end.

Other optional animation properties include:

```css
animation-iteration-count: 3;
animation-delay: 3s;
```

- `animation-iteration-count` controls how many times the animation runs.
- `animation-delay` waits before starting the animation.

#### Animation Shorthand

The same animation settings can also be written using the `animation` shorthand property:

```css
animation: moveInRight 1s ease-out;
```

This combines:

```css
animation-name: moveInRight;
animation-duration: 1s;
animation-timing-function: ease-out;
```

The basic shorthand used here follows:

`animation: name duration timing-function;`

So the animation is defined once with `@keyframes`, then individual elements choose which animation to use and how it should run.

### Multiple Classes and Spaces in Selectors

#### Multiple Classes in HTML

The `class` attribute can contain multiple class names separated by spaces:

```html
<a href="#" class="btn btn-white">
```

This element has two separate classes:

- `btn`
- `btn-white`

The space inside `class=""` separates the class names.

For example:

```html
class="btn btn-white animated"
```

means the element has all three classes:

- `btn`
- `btn-white`
- `animated`

Each class can provide its own styles to the same element. For example, `btn` can provide the shared button styles while `btn-white` provides the white button styles.

#### Spaces in CSS Class Selectors

There is one related CSS syntax trap:

```css
.btn.btn-white
```

means **an element that has both classes**, while:

```css
.btn .btn-white
```

means **a `.btn-white` element somewhere inside a `.btn` element**.

So:

```css
.btn.btn-white   /* One element with both classes */
.btn .btn-white  /* A btn-white element inside a btn element */
```

Whitespace is doing different jobs depending on where it appears:

- Inside an HTML `class=""` attribute, spaces separate class names applied to the same element.
- Between CSS selectors, a space can represent a descendant relationship between elements.

### Centering an Inline-Block Button with `text-align`

The button is an `<a>` element:

```html
<a href="#" class="btn btn-white">Discover our tours</a>
```

By default, an anchor is an inline element. For this button, however, we want two things at the same time:

1. We want it to behave more like its own box so padding and other box-related styles work naturally.
2. We still want it to behave as inline-level content so the parent `.text-box` can center the entire button with `text-align: center`.

This is why the button uses:

```css
.btn:link,
.btn:visited {
    display: inline-block;
}
```

`inline-block` combines useful behavior from both inline and block elements.

A useful mental model is:

> **Inline for layout, block-like for sizing and box behavior.**

Compared with the other display types:

- `inline` stays within the text flow, but has more limited box-like behavior.
- `block` behaves as its own block and normally takes up the available width.
- `inline-block` stays inline in the surrounding layout, but can still behave like a distinct box with padding, dimensions, transforms, and similar styling.

The parent `.text-box` contains both the heading and the button:

```html
<div class="text-box">
    <h1 class="heading-primary">
        ...
    </h1>

    <a href="#" class="btn btn-white">Discover our tours</a>
</div>
```

The parent then uses:

```css
.text-box {
    text-align: center;
}
```

It is easy to think that `text-align: center` only centers actual text, but it also controls the horizontal alignment of inline-level content inside the element.

Because the button is `display: inline-block`, the **entire button box** still counts as inline-level content. This allows `.text-box` to center the whole button horizontally with `text-align: center`.

The text inside the button is **not what moves the button into the center**. The parent is centering the inline-block button itself.

There are therefore two related effects:

1. `.text-box` uses `text-align: center` to center the inline-block button inside the container.
2. `text-align` is inherited, so the text inside the button is also centered within the button.

If the anchor were changed to a normal block element instead, it would normally take up the available width. In that case, `text-align: center` would center the text inside that full-width block rather than centering the button box itself.

So `inline-block` is what allows the button to have **block-like box behavior while still remaining inline-level content that its parent can center**.

### Button Pseudo-Classes and Transitions

The button uses pseudo-classes to style the same `<a>` element differently depending on its current state:

```css
.btn:link,
.btn:visited {
    transition: all .2s;
}

.btn:hover {
    transform: translateY(-3px);
}

.btn:active {
    transform: translateY(-1px);
}
```

The pseudo-classes represent different states of the link:

- `:link` targets the normal, unvisited link state.
- `:visited` targets a link that has already been visited.
- `:hover` targets the element while the mouse is over it.
- `:active` targets the element while it is being clicked or pressed.

The `:link` and `:visited` rules are being used together as the button's **base states**. The hover and active rules then temporarily change properties such as `transform` and `box-shadow`.

The transition is placed on the base `:link/:visited` rule:

```css
transition: all .2s;
```

This means changes between the base, hover, and active states are animated smoothly over `.2s`.

Putting the transition on the base state is important because it allows the animation to work in both directions:

- entering `:hover` or `:active`
- returning from those states back to the base state

A useful mental model is:

> **The pseudo-classes define the different states of the same element, while `transition` controls how smoothly the element moves between those states.**

### Button `::after` Pseudo-Element and Animation

The button now uses three classes:

```html
<a href="#" class="btn btn-white btn-animated">Discover our tours</a>
```

- `btn` provides the shared button styles.
- `btn-white` provides the white button styling.
- `btn-animated` adds the entrance animation.

#### Creating the `::after` Pseudo-Element

```css
.btn::after {
    content: "";
    display: inline-block;
    height: 100%;
    width: 100%;
    border-radius: 100px;
    position: absolute;
    top: 0;
    left: 0;
    z-index: -1;

    transition: all .4s;
}
```

`::after` creates an extra pseudo-element associated with the button without adding another HTML element.

```css
content: "";
```

is required for the pseudo-element to exist. It is empty because it is being used only for a visual effect.

The pseudo-element is made the same size and shape as the button and positioned directly behind it with `position: absolute` and `z-index: -1`.

A useful mental model is:

> `::after` creates an extra visual layer behind the button that can be animated independently.

The white button gives that layer the same background color:

```css
.btn-white::after {
    background-color: #fff;
}
```

#### Hover Effect

```css
.btn:hover::after {
    transform: scaleX(1.4) scaleY(1.6);
    opacity: 0;
}
```

This means:

> When `.btn` is hovered, style its `::after` pseudo-element.

The pseudo-element grows to:

- `140%` of its original width with `scaleX(1.4)`
- `160%` of its original height with `scaleY(1.6)`

At the same time, `opacity: 0` makes it fade away.

Because the base `::after` rule has:

```css
transition: all .4s;
```

the growth and fade happen smoothly.

#### Button Entrance Animation

```css
.btn-animated {
    animation: moveInButton .5s ease-out .75s;
    animation-fill-mode: backwards;
}
```

The animation shorthand is:

```text
name duration timing-function delay
```

So the button waits `.75s`, then runs `moveInButton` for `.5s`.

`animation-fill-mode: backwards` applies the animation's starting styles during the delay, preventing the button from appearing in its normal position before the animation begins.

### `animation-fill-mode: backwards`

The button animation has a `.75s` delay before it begins:

```css
.btn-animated {
    animation: moveInButton .5s ease-out .75s;
    animation-fill-mode: backwards;
}
```

During that delay, the animation has not started yet.

Normally, the button would use its regular CSS styles while it waits. Then, when the animation starts, it would suddenly switch to the `0%` state from `moveInButton` before animating toward `100%`.

`animation-fill-mode: backwards` prevents that jump by telling the browser to use the animation's **starting `0%` styles during the delay**, even though the animation itself has not started yet.

The animation delay still exists. `backwards` does not remove or shorten the delay; it only changes what the element looks like while it is waiting.

It also does **not** grab properties from another animation. It uses the starting keyframe from the animation already assigned to the element.

A useful mental model is:

> `backwards` applies the animation's starting appearance while the animation is waiting to begin.

#### How `backwards` Uses the Starting Animation State

So `backwards` is basically saying:

> **"Fill the animation's waiting time with frame `0%`."**

The word `backwards` refers to **time before the animation starts**. It does not mean the animation runs backward or that the button moves backward.

The animation normally begins only after the `.75s` delay. `backwards` reaches **backward from that starting point into the delay** and extends the animation's starting appearance into that waiting time.

The `0%` keyframe comes from the `@keyframes` rule for the animation assigned to the element. In this case, the button uses `moveInButton`:

```css
@keyframes moveInButton {
    0% {
        opacity: 0;
        transform: translateY(30px);
    }

    100% {
        opacity: 1;
        transform: translateY(0);
    }
}
```

For `moveInButton`, the `0%` state means the button starts:

- completely invisible because `opacity` is `0`
- `30px` below its normal position because `translateY(30px)` moves it down

During the `.75s` delay, `backwards` tells the browser to hold the button in that `0%` state:

```css
opacity: 0;
transform: translateY(30px);
```

The animation is **not running yet** during the delay. The browser is simply holding the button in the same visual state that the animation will start from.

Once the delay finishes, the animation begins from that same `0%` state and moves toward:

```css
100% {
    opacity: 1;
    transform: translateY(0);
}
```

The button then fades in while moving upward into its normal position.

Without `backwards`, the button could appear normally during the delay, then suddenly become invisible and jump `30px` down when the animation starts before moving upward again.

With `backwards`, there is no sudden visual jump because the button is already displaying the animation's starting state when the animation begins.

A useful way to picture the word `backwards` is:

> **The animation starts after the delay, and `backwards` reaches backward in time from that start point and fills the delay with the animation's starting frame.**



