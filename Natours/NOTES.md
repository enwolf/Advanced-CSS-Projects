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

