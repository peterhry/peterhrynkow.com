---
layout: post
title:  "Invalidate Pattern"
date:   2017-08-24 00:00:00
categories: [performance]
---

_@todo write something about this_

```js
class MyClass {
  constructor(domElement) {
    this._domElement = domElement;
  }

  setWidth(width) {
    this._width = width;
    this._layout();
  }

  setHeight(height) {
    this._height = height;
    this._layout();
  }

  _layout() {
    this._domElement.style.width = `{this._width}px`;
    this._domElement.style.height = `{this._height}px`;
  }
}

const instance = new MyClass(myElement);

instance.setWidth(200);
instance.setHeight(200);

// _layout called twice.
```

_@todo write something about this_

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
      this._layout();
    });
  }

  _layout() {
    this._domElement.style.width = `{this._width}px`;
    this._domElement.style.height = `{this._height}px`;
  }
}

const instance = new MyClass(myElement);

instance.setWidth(200);
instance.setHeight(200);

// _layout called once.
```
