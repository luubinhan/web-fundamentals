![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/9mWVASbWDLzdBUpVcjE1.svg)

Những nguyên nhân chủ yếu dẫn đến layout shift

- Hình không có kích thước
- Ads, embed, iframe không có kích thước
- Thêm nội dung động
- Web font

## Hình

Ngày xưa chúng ta lúc nào cũng có thói quen thêm `width` và `height` cho thẻ img

```html
<img src="puppy.jpg" width="640" height="360" alt="Puppy with balloons" />
```

Nhưng khi làm responsive, chúng ta dàn bỏ luôn `width` và `height` để tiện xử lý bằng CSS

```css
img {
  width: 100%; /* or max-width: 100%; */
  height: auto;
}
```

Để xử lý, chúng ta khai báo thêm aspect ratio để giúp trình duyệt biết *chừa chổ*

Nếu thẻ image có kích thước `width`, `height`

```html
<img src="puppy.jpg" width="640" height="360" alt="Puppy with balloons" />
```



```css
img {
  height: auto;
  width: 100%;
  aspect-ratio: attr(width) / attr(height);
  /* trường hợp này là 16/9 */
}
```

Nếu toán học là điểm yếu, dùng [công cụ hỗ trợ](https://aspectratiocalculator.com/16-9.html) để tính aspect ratio

Để giúp trình duyệt load đúng hình theo từng kích thước, sử dụng `srcset`

```html
<img
  width="1000"
  height="1000"
  src="puppy-1000.jpg"
  srcset="puppy-1000.jpg 1000w, puppy-2000.jpg 2000w, puppy-3000.jpg 3000w"
  alt="Puppy with balloons"
/>
```

Trên từng viewport cụ thể, chúng ta cũng có thể xác định từng hình cho phù hợp, nếu không có thể nó crop lại không như ý, crop thủ công từng hình sẽ chuẩn hơn

```html
<picture>
  <source media="(max-width: 799px)" srcset="puppy-480w-cropped.jpg" />
  <source media="(min-width: 800px)" srcset="puppy-800w.jpg" />
  <img src="puppy-800w.jpg" alt="Puppy with balloons" />
</picture>
```

## Ads, embed, iframe

- **Chừa chổ** với kích thước phù hợp với ads, embed, iframe sau khi load thành công
- Tránh đặt ads ở những nơi có thể gây layout shift như đầu trang
- Với nội dung động, skeleton là lựa chọn tốt

