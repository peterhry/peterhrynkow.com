---
layout: post
title:  "Invalidate Pattern"
date:   2017-08-24 00:00:00
categories: [performance]
---

Nowadays, you can’t have a discussion about component-based software design without mentioning React, but the idea of building apps with components was around long before Facebook. 

My first exposure to components was in 2003 when the engineers at Macromedia shipped a component library called MX components with Flash MX. 

Say what you will about Flash but the engineers at Macromedia came up with some clever patterns for improving a component’s rendering performance. One such pattern is called the “invalidate pattern” and you see it everywhere in today’s modern components.

Say you have an object that controls the width and height of an element. It exposes some methods `setWidth` and `setHeight`. Each time one of these methods is called, the dimensions of the element are set.

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

The problem with this code is that the `_render` method is called twice, once for `setWidth`, and once for `setHeight`. It would be more efficient if `_render` was called just once, _after_ `setWidth` and `setHeight` have both been called.

To solve this, we can debounce calls to `_render` using `requestAnimationFrame`.

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

When `setWidth` is called, the `_invalidate` function schedules a call to `_render` in the next frame. When `setHeight` is called immediately after, the scheduled call to `_render` is cancelled and replaced with a new scheduled call to `_render`.

The [debounce](https://lodash.com/docs/4.17.4#debounce) function from lodash would have worked here, too, except that it uses `setTimeout` under the hood. Since the `_render` function changes CSS properties like `width` and `height`, using `requestAnimationFrame` allows the browser to better optimize these updates.
