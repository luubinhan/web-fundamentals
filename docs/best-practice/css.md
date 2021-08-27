Những kỹ thuật để cải thiện điểm số Lighthouse với CSS. Chủ ý là xử lý Cumulative Layout Shift và Largest Contentful Paint

Trang dùng làm ví dụ

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/pgmpMOmweK7BVBsVkQ5g.png?auto=format&w=964)

Lighthouse thông báo có 3 element dẫn đến layout shift

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/jaHtgwzDXCjx3vAFOO33.png?auto=format&w=964)

3 element này bị layout shift khi cửa sổ thông báo chấp nhận cookie xuất hiện. Để fix thì chúng ta sẽ cho cửa sổ thông báo này nằm fixed

Code cũ

```css
.banner {
  position: sticky;
  top: 0;
}
```

Code fix

```css
.banner {
  position: fixed;
  bottom: 0;
}
```

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/YBYLT9jJ9AXrbsaRNVoa.png?auto=format&w=964)

Hoặc chúng ta để sẵn min-height cho element, cũng sẽ giải quyết được.

Đối với việc element được xác định là LCP sẽ khác nhau trên từng lừng load, nó có thể là một trong 3

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/bMoAoohyLOgTqV6B7lHr.png?auto=format&w=964)

Ở đây chúng ta có thể dùng css để vẽ được ảnh nền kiểu gradient, nền không cần dùng đến image

```css
background: url("https://cdn.pixabay.com/photo/2015/07/15/06/14/gradient-845701\_960\_720.jpg")
```

Được thay bằng

```css
background: linear-gradient(135deg, #fbc6ff 20%, #bdfff9 90%);
```

Image không có xác định rõ width và height cũng sẽ bị nhắc nhở

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/wDGRVi7JaUOTjD9ODOk9.png?auto=format&w=964)

Chúng ta buộc phải set width và height cho image

```html
<img src="https://source.unsplash.com/random/2000x600" width="2000" height="600" alt="image to load in">
<img src="https://source.unsplash.com/random/800x600" width="800" height="600" alt="image to load in">
```

Với font chữ, không dùng `@import` để load font, dùng `<link />` để chúng ta có thể setup preconnect

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@100&display=swap" rel="stylesheet">
```

Với animation, chúng ta luôn dùng transform, thay vì các property định position như top, left, right, bottom, margin, padding để làm animation

```css
.header {
  animation: slideIn 1s 1 ease;
}

@keyframes slideIn {
  from {
    transform: translateX(-100%);
  }

  to {
    transform: translateX(0);
  }
}
```

CSS nào thật sự cần thiết, được xác định là critical cho first render, có thể tìm cách [inline ](https://web.dev/extract-critical-css/) nó và [defer loading các CSS chưa cần đến](https://web.dev/defer-non-critical-css/#optimize)

https://web.dev/css-web-vitals/