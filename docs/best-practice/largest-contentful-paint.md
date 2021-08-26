![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/elqsdYqQEefWJbUM2qMO.svg)

Kết quả [LCP](/audit/largest-contentful-paint/) sẽ phụ thuộc nhiều vào các element sau

- `<img />`
- `<video />`
- element có thuộc tính css background-image
- element có css `display: block`

Ví dụ element được xem là LCP 

![](https://web-dev.imgix.net/image/admin/bsBm8poY1uQbq7mNvVJm.png?auto=format&w=845)

Cách tính LCP trong JS

```js
new PerformanceObserver((entryList) => {
  for (const entry of entryList.getEntries()) {
    console.log('LCP candidate:', entry.startTime, entry);
  }
}).observe({type: 'largest-contentful-paint', buffered: true});
```

Có một lựa chọn tốt hơn là sử dụng library [web-vitals](https://github.com/GoogleChrome/web-vitals)

```js
import {getLCP} from 'web-vitals';

// Measure and log LCP as soon as it's available.
getLCP(console.log);
```

Để cải thiện, có các phương án sau

- Cải thiện thời gian response của server
- Cải thiện render-blocking
- Cải thiện thời gian load
- Thời gian client render

Tham khảo thêm các bài viết

- [Apply instant loading with the PRPL pattern](https://web.dev/apply-instant-loading-with-prpl)
- [Optimizing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/)
- [Optimize your CSS](https://web.dev/fast#optimize-your-css)
- [Optimize your Images](https://web.dev/fast#optimize-your-images)
- [Optimize web Fonts](https://web.dev/fast#optimize-web-fonts)
- [Optimize your JavaScript](https://web.dev/fast#optimize-your-javascript)
