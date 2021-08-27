Dùng font đúng cách để cải thiện tốc độ của web, vì font cũng là một trong những nguyên nhân tác động đến First-Contentful Paint, Largest Contentful Pain, Layout Shift

Câu lệnh `@font-face` cần tối thiểu 2 khai báo: tên và vị trí của font

```css
@font-face {
    font-family: "Open Sans";
    src: url("/fonts/OpenSans-Regular-webfont.woff2") format("woff2");
}
```

Có một điều nhiều người nhầm tưởng là câu lệnh này cũng đồng thời trigger việc download font, nhưng không, font chỉ được download khi có một phần tử nào đó khai báo sử dụng font

```css
h1 {
    font-family: "Open Sans";
}
```

Nói cách khác, khi trang bắt đầu render element `<h1/>` font mới bắt đầu được download

Best practice để khi load font là cho nó load sớm nhất có thể. Đặc biệt để ý đến những font phải lên từ một bên thứ 3

Để kiểm tra thời gian font bắt đầu được load khi nào, xem trên tab **Timing** trong panel **Network** của DevTools

![Screenshot of the Timing tab in DevTools](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/hFVGVzDQHymbC5aIAtD9.png?auto=format)

Khai báo font inline trong thẻ `<head />` sẽ buộc trình duyệt download font sớm hơn khi đặt trong file stylesheet riêng

```html
<head>
    <style>
    @font-face {
        font-family: "Open Sans";
        src: url("/fonts/OpenSans-Regular-webfont.woff2") format("woff2");
    }

    body {
        font-family: "Open Sans";

    }
  </style>
</head>
```

Tuy nhiên khi inline font như vậy không được khuyến khích, vì khi đó trình duyệt buộc phải load font (có thế tốn nhiều thời gian) trước, các resource khác sẽ phải **ngồi đợi**. Tương tự việc khai báo **preload** cũng nên tránh cho resource là font

Nếu phải load font từ những trang thứ 3, **luôn nhớ** sử dụng `preconnect` để trình duyệt khởi tạo kết nối đến bên thứ 3 trước

```html
<head>
    <link rel="preconnect" href="https://fonts.com" />
</head>
```

Trên *lý thuyết* tự host font sẽ cho tốc độ load nhanh hơn, nhưng trong thực tế chứng minh, sử dụng font từ bên thứ 3 lại cho tốc độ tốt hơn, với các CDN chất lượng như google font

Nếu bạn tự host file thì nhớ dùng định dạng compress tốt nhất **WOFF2**

Tận dụng font hệ thống, font load nhanh nhất chính là font ở local của user

```css
font-family: system-ui;
```

Không những nên hạn chế dùng nhiều font, nên hạn chế luôn dùng nhiều variant của font (200,300,400,700)

khi trình trang web sử dụng một font phải load về, tùy theo trình duyệt sẽ ứng xử khác nhau, Chrome và Firefox sẽ block việc render text tối đa 3 giây trước khi dùng font thay thế.

Để can thiệp việc này, khai báo `font-display` để báo trình duyệt render bằng font khác trước khi font đã load xong

```css
@font-face {
    font-family: Roboto, Sans-Serif
  src: url(/fonts/roboto.woff) format('woff'),
  font-display: swap;
}
```

`font-display` có thể set những giá trị sau

| Giá trị  | Thời gian Block      | Thời gian Swap       |
| -------- | -------------------- | -------------------- |
| Auto     | Tùy theo trình duyệt | Tùy theo trình duyệt |
| Block    | 3 s                  | Infinite             |
| Swap     | 0ms                  | Infinite             |
| Fallback | 100ms                | 3 s                  |
| Optional | 100ms                | không                |

Trong đó

- Thời gian Block: là khoản thởi gian từ lúc trình duyệt bắt đầu request font. Trong thời gian này text sẽ không được hiển thị, sau khi có kết quả load fail, font khác sẽ được dùng thay thế
- Thời gian Swap: là khoản thời gian sau thời gian block, nếu có load được font trong khoản thời gian này, nó sẽ được thay

Ghi chú:

- Nếu tốc độ là yêu cầu hàng đầu: `font-display: optional`
- Nếu hiển thị đúng là yêu cầu hàng đầu: `font-display: swap`