# Responsive image

Responsive Web Design không chỉ cập nhập layout mà còn cập nhập nội dung load lên để hiển thị cho người dùng. Ví dụ với màn hình 2x, chúng ta cần đảm bảo hình ảnh sắc nét nhưng cũng cần yêu cầu đảm bảo không load file dung lượng không cần thiết

![Một số nguyên tắc với hình ảnh responsive](https://developers.google.com/web/fundamentals/design-and-ux/responsive/img/art-direction.png)

Crop, thay đổi vị trí đặt hình, hoặc thậm chí là thay luôn một hình khác trên từng màn hình, miễn sao nó hiển thị đúng ý đồ của designer, như trong hình ví dụ, hình người chèo thuyền phải nằm giữa

## Một số nguyên tắc chung

- Luôn sử dụng kích thước hình phù hợp
- Sử dụng thẻ `<picture >` khi muốn chỉ định từng hình cũ thể cho từng màn hình.
- Sử dụng `srcset` trong thẻ `<img />` để báo với trình duyệt chọn hình tốt nhất trong từng trường hợp
- Nếu chỉ có 1 hoặc 2 hình, và nó chỉ được sử dụng ở một trang, sử dụng loại ảnh phù hợp

## Kích thước phù hợp

Chỉ định đơn vị `width` của hình phù hợp, tránh để nó lớn viewport.

```css
img, embed, object, video {
  max-width: 100%;
}
```

## Sử dụng `srcset` với thiết bị DPI cao

Với thuộc tính `srcset`, trình duyệt có thể quyết định load hình nào tùy theo thiết bị, ví dụ 2x trên màn hình retina, hoặc 1x trên màn hình retina khi mạng chậm

```html
<img src="photo.png" srcset="photo@2x.png 2x" />
```

Khi trình duyệt chưa hỗ trợ `srcset`, nó sẽ load hình trong thuộc tính `src`

## Sử dụng thẻ `<picture />`

Thẻ `<picture />` sẽ cho khai báo nhiều hình khác nhau ứng với từng điều kiện như: kích thước, độ phân giải, chiều đứng hay ngang.

```html
<picture>
  <source media="(min-width: 800px)" srcset="head.jpg, head-2x.jpg 2x">
  <source media="(min-width: 450px)" srcset="head-small.jpg, head-small-2x.jpg 2x">
  <img src="head-fb.jpg" srcset="head-fb-2x.jpg 2x" alt="a head carved out of wood">
</picture>
```

Xài thử [https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/responsive/media.html](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/responsive/media.html)

## Thuộc tính `sizes`

Đọc thêm bài [Tầm quan trọng của thuộc tính sizes trong thẻ img](http://vuilaptrinh.com/2018-07-30-huong-dan-tam-quan-trong-cua-thuoc-tinh-sizes-trong-the-img)

## Trường hợp đặc biệt: ảnh sản phẩm

Khách hàng luôn thích xem, sờ, ngắm thật kỹ sản phẩm muốn mua, luôn cho khách hàng ảnh thật chi tiết, to nhất, chất lượng nhất có thể, để khách hàng có thể zoom vào mà soi

![Một số nguyên tắc với hình ảnh responsive](https://developers.google.com/web/fundamentals/design-and-ux/responsive/img/sw-make-images-expandable-good.png)


## Một số kỹ thuật khác

### Nén ảnh

<a href="https://www.html5rocks.com/en/mobile/high-dpi/#toc-tech-overview" rel="noopener noreferrer">Kỹ thuật nén ảnh</a> này, với mức độ nén phù hợp, định dạng của ảnh gốc, chất lượng ảnh sau nén không thay đổi, nhưng kích thước giảm khá nhiều.

### Dùng javascript

Kiểm tra độ phân giải của màn hình bằng `window.devicePixelRatio`, và thông tin mạng `navigator.connection` rồi quyết định load hình nào.

Nhược điểm của cách này là phải đợi javascript chạy xong mới load hình, bị delay một chút.

### Sử dụng loại ảnh phù hợp

Nếu được thì xài SVG vì dung lượng nhỏ mà có võ, có thể nén bằng <a rel="noopener noreferrer" target="_blank" href="https://www.sarasoueidan.com/blog/svgo-tools/">công cụ này</a>

![Một số nguyên tắc với hình ảnh responsive](https://developers.google.com/web/fundamentals/design-and-ux/responsive/img/html5.svg)

Sử dụng Data URI để encode Base64 file hình thành `string`

```html
<img src="data:image/svg+xml;base64,[data]">

<!-- Ví dụ -->
<img src="data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz4NCjwhLS0gR2VuZXJhdG9yOiB
BZG9iZSBJbGx1c3RyYXRvciAxNi4wLjAsIFNWRyBFeHBvcnQgUGx1Zy1JbiAuIFNWRyBWZXJzaW ...">
```

Để convert, thì có thể dùng [jpillora.com/base64-encoder](http://jpillora.com/base64-encoder/)

string convert được cũng có thể xài với thuộc tính background trong css

```css
.element {
  background: url(data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIi...);
}
```

Việc sử dụng background trong css có thể làm giảm số lượng HTTP request, bên cạnh đó là một số nhược điểm

- Trên mobile, Data URI hiển thị chậm hơn `<img src="http://" />`
- Dảm số lượng request, nhưng tăng kích thước của request css
- Data URI không cache được
- IE8 không hổ trợ
- HTTP/2 giảm số lượng request không còn cần thiết

Trong CSS có một thuộc tính để load hình như thẻ `<picture />` là `image-set` (hiện tại hổ trợ bởi chrome và safari)

```css
.element {
  background-image: url(icon1x.png);
  background-image: -webkit-image-set(
    url(icon1x.png) 1x,
    url(icon2x.png) 2x
  );
  background-image: image-set(
    url(icon1x.jpg) 1x,
    url(icon2x.jpg) 2x
  );
}
```

Với độ phân giải 2x, 1x

```css
@media (min-resolution: 2dppx),
(-webkit-min-device-pixel-ratio: 2)  /* Dành riêng cho Safari và Android */
{
  .sample {
    background-size: contain;
    background-image: url(icon2x.png);
  }
}
```

Các phương pháp như lựa chọn hình JPG, GIF, PNG, các phương pháp giảm kích thước file (mình hay dùng [FileOptimizer](https://nikkhokkho.sourceforge.io/static.php?page=FileOptimizer)), [image sprite](https://www.w3schools.com/css/css_image_sprites.asp), [lazy load](https://css-tricks.com/native-lazy-loading/) khá phổ biến nên mình không đề cập ở đây.

[Responsive Image](https://developers.google.com/web/fundamentals/design-and-ux/responsive/images)
