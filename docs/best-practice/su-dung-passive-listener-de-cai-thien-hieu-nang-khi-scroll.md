Lighthouse sẽ thông báo khi có listener event làm chậm việc scrolling

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/a59Rk7aCUDvyKNqqoYRJ.png?auto=format&w=845)

Với các listener event bị Lighthouse đánh dấu, truyền thêm option `passive`

```js
document.addEventListener('touchstart', onTouchStart, {passive: true});
```

Xem thêm

- [Source code for **Does not use passive listeners to improve scrolling performance** audit](https://github.com/GoogleChrome/lighthouse/blob/master/lighthouse-core/audits/dobetterweb/uses-passive-event-listeners.js)
- [Improving Scrolling Performance with Passive Event Listeners](https://developers.google.com/web/updates/2016/06/passive-event-listeners)
- [Passive event listeners explainer](https://github.com/WICG/EventListenerOptions/blob/gh-pages/explainer.md)
- [EventTarget.addEventListener()](https://developer.mozilla.org/docs/Web/API/EventTarget/addEventListener)