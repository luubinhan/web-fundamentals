# Web App Manifest

Web App Manifest là một file JSON báo với trình duyệt ứng dụng web của bạn có thể *cài đặt* trên điện thoại hoặc desktop như một ứng dụng offline. Khi có file này, trình duyệt Chrome sẽ hiển thị nút "Add to Home screen" khi truy cập vào trang.

Một file Web App Manifest cơ bản nhất chứa đủ 3 thông tin `name`, `icons` và `start_url`

Đầy đủ hơn

```json
{
  "short_name": "Maps",
  "name": "Google Maps",
  "icons": [
    {
      "src": "/images/icons-192.png",
      "type": "image/png",
      "sizes": "192x192"
    },
    {
      "src": "/images/icons-512.png",
      "type": "image/png",
      "sizes": "512x512"
    }
  ],
  "start_url": "/maps/?source=pwa",
  "background_color": "#3367D6",
  "display": "standalone",
  "scope": "/maps/",
  "theme_color": "#3367D6"
}
```

File json này chúng ta thêm nó vào đầu trang html bằng thẻ `link`

```html
<link rel="manifest" href="/manifest.json">
```

Giải thích về các key được khai báo trong file JSON

- `short_name` được sử dụng để hiển thị trên màn hình điện thoại
- `name` là tên hiển thị trên popup gửi yêu cầu cài đặt
- `icons` là một mảng object chứa kích thước, loại file, và đường dẫn đến file sẽ dùng làm icon trên màn hình

```json
"icons": [
  {
    "src": "/images/icons-192.png",
    "type": "image/png",
    "sizes": "192x192"
  },
  {
    "src": "/images/icons-512.png",
    "type": "image/png",
    "sizes": "512x512"
  }
]
```
- `start_url` là đường link sẽ mở khi vừa khởi chạy ứng dụng
- `background_color` màu nền của splash screen
- `display` giá trị để tùy biến UI khi chạy, có thể ẩn đi thanh address của trình duyệt
	- `fullscreen` mở toàn màn hình, chỉ hiển thị nội dung trang web
	- `standalone` mở như ứng dụng native, không bao gồm các thành phần của trình duyệt
	- `minimal-ui` tương tự như `fullscreen`, nhưng có hiển thị cách thanh điều hướng, reload
	- `browser` giống như mở trang web bình thường với đầy đủ các control của trình duyệt
- `orientation` định hướng trang web sẽ mở theo chiều ngang hay đứng
- `scope` những đường dẫn nào sẽ thuộc vào app, nghĩa là không mở bằng trình duyệt. Theo mặc định giá trị scope sẽ là toàn bộ những trang có đường dẫn nằm cùng và bên dưới file manifest
- `theme_color` màu sắc chủ đạo của thanh toolbar

## Splash screen

Trình duyệt Chrome sẽ tự động kết hợp 3 giá trị `name`, `background_color`, `icons` để tạo ra splash screen cho ứng dụng

![background color](https://developers.google.com/web/fundamentals/web-app-manifest/images/background-color.gif)

Kích thước icon cho splash screen đề nghị là 128dp, trong đa số các trường hợp bạn cần cung cấp 2 kích thước 192px và 512px là hiển thị đẹp

## Kiểm tra file manifest

Sử dụng cửa sổ **Application** trong DevTools của Chrome để kiểm tra những nội dung đã khai báo trong file manifest

![](https://developers.google.com/web/fundamentals/web-app-manifest/images/devtools-manifest.png)

[https://developers.google.com/web/fundamentals/web-app-manifest](https://developers.google.com/web/fundamentals/web-app-manifest)
