---
layout: post
title:  "Invalidate Pattern"
date:   2017-08-24 00:00:00
categories: [performance]
---

You can’t talk about component-based software design without mentioning React, but the idea of building apps with components predates Facebook by a long shot. My first exposure was in 2003 when Macromedia shipped an MX component library with Flash MX.

Flash’s engineers came up with clever patterns for improving component rendering performance. One such pattern is the “invalidate pattern,” which you see everywhere in modern components.

Say you have an object that controls the width and height of an element. It exposes `setWidth` and `setHeight`. Each time one of these methods is called, the dimensions of the element are set immediately.

```js
class MyClass {
  constructor(domElement) {
    this._domElement = domElement;
  }

  setWidth(width) {
    this._width = width;
    this._render();
  }

  setHeight(height) {
    this._height = height;
    this._render();
  }

  _render() {
    this._domElement.style.width = `${this._width}px`;
    this._domElement.style.height = `${this._height}px`;
  }
}

const instance = new MyClass(myElement);

instance.setWidth(200);
instance.setHeight(200);

// _render called twice.
```

The problem is that `_render` runs twice—once for `setWidth`, and once for `setHeight`. It would be more efficient if `_render` ran once _after_ both setters were called.

To solve this, debounce calls to `_render` using `requestAnimationFrame`.

```js
class MyClass {
  constructor(domElement) {
    this._domElement = domElement;
  }

  setWidth(width) {
    this._width = width;
    this._invalidate();
  }

  setHeight(height) {
    this._height = height;
    this._invalidate();
  }

  _invalidate() {
    cancelAnimationFrame(this._raf);

    this._raf = requestAnimationFrame(() => {
      this._render();
    });
  }

  _render() {
    this._domElement.style.width = `{this._width}px`;
    this._domElement.style.height = `{this._height}px`;
  }
}

const instance = new MyClass(myElement);

instance.setWidth(200);
instance.setHeight(200);

// _render called once.
```

When `setWidth` is called, `_invalidate` schedules a call to `_render` in the next frame. When `setHeight` is called immediately after, the scheduled call is cancelled and replaced with a new one. Rendering now happens once per animation frame, no matter how many setters fire.

You could use lodash’s [debounce](https://lodash.com/docs/4.17.4#debounce) here too, but because `_render` manipulates layout properties, `requestAnimationFrame` gives the browser more opportunity to optimize these updates.
