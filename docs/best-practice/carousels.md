![](https://web-dev.imgix.net/image/admin/u2FlXalClwBeDOBBiwxu.png?auto=format&w=741)

## Load ảnh bằng HTML

```html
<div class="slides">
  <img src="https://example.com/cat1.jpg">
  <img src="https://example.com/cat2.jpg">
  <img src="https://example.com/cat3.jpg">
</div>
```

Không dùng JS thuần để tạo element

```js
const slides = document.querySelector(".slides");
const newSlide = document.createElement("img");
newSlide.src = "htttp://example.com/cat1.jpg";
slides.appendChild(newSlide);
```

## Chuyển slide

Đuyển chuyển slide, không nên dùng các thuộc tính `left` `top` `width` và `marginTop`, hãy luôn dùng css `transform` để nâng cao hiệu năng

Ngoài ra nếu các slide không có kích thước bằng nhau, dễ dẫn đến tình trạng layout shift

Đừng cho chạy tự động cũng là điều nên cân nhắc

## Thư viện

Đa phần chúng ta sẽ sử dụng third-party đề làm, thư viện cũ không cập nhập các API mới, là đôi khi còn phụ thuộc vào jQuery

Tùy vào loại carousel đang làm, có thể không cần đến JS, chúng ta có thể làm chỉ cần HTML, CSS với API scroll snap, tham khảo thêm các bài hướng dẫn sử dụng

- [Building a Stories component (web.dev)](https://web.dev/building-a-stories-component/)
- [Next-generation web styling: scroll snap (web.dev)](https://web.dev/next-gen-css-2019/#scroll-snap)
- [CSS-Only Carousel (CSS Tricks)](https://css-tricks.com/css-only-carousel/)
- [How to Make a CSS-Only Carousel (CSS Tricks)](https://css-tricks.com/how-to-make-a-css-only-carousel/)



