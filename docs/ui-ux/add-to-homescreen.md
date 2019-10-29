# Thêm vào Home screen

## Yêu cầu

Để user có thể cài đặt PWA vào điện thoại hay desktop, chúng ta phải đáp ứng đủ các yêu cầu sau

- Ứng dụng web chưa được cài đặt, và giá trị `prefer_related_applications` KHÔNG bằng `true`
- File web app manifest với đầy đủ thông tin
  - `short_name` hoặc `name`
  - `icons` ít nhất 2 kích thước 192px và 512px
  - `start_url`
  - `display` là một trong các giá trị: `fullscreen`, `standalone` hoặc `minimal-ui`
- Chạy trên HTTPS
- Đã đăng ký service worker với các sự kiện của `fetch`

Khi các điều kiện này đã đáp ứng đủ, trình duyệt sẽ bắn ra sự kiện `beforeinstallprompt`, chúng ta dùng sự kiện này để thông báo cho user có muốn cài PWA không

## Thêm nút Add to Home Screen

![](https://developers.google.com/web/fundamentals/app-install-banners/images/a2hs-dialog-g.png)

Khi có sự kiện `beforeinstallprompt` bắn ra, chúng ta sẽ lưu tạm nó vào một biến để dành xài.

```js
let deferredPrompt;

window.addEventListener('beforeinstallprompt', (e) => {
  // lưu vào đây, chút nữa sẽ xài.
  deferredPrompt = e;
  ...
});
```

Gợi ý cho user cài đặt ứng dụng PWA của chúng ta, xem thêm bài [Gợi ý cài đặt PWA](/ui-ux/install-pwa) để biết đặt đâu cho *ngầu*

```js{5}
window.addEventListener('beforeinstallprompt', (e) => {
  // lưu vào đây, chút nữa sẽ xài.
  deferredPrompt = e;
  // cập nhập UI
  showInstallPromotion();
});
```

Khi user đã thấy *hấp dẫn* bởi ứng dụng của chúng ta và muốn mang nó ra trang chủ điện thoại.

```js
btnAdd.addEventListener('click', (e) => {
  // ấn nút Dụ dỗ cài đặt đi
  btnAdd.style.display = 'none';
  // Hiện thông báo xác nhận
  deferredPrompt.prompt();
  // Đợi user trả lời
  deferredPrompt.userChoice
    .then((choiceResult) => {
      if (choiceResult.outcome === 'accepted') {
        console.log('User accepted the A2HS prompt');
      } else {
        console.log('User dismissed the A2HS prompt');
      }
      deferredPrompt = null;
    });
});
```

## Mini-info bar

Đây là kỹ thuật hiển thị một thanh thông tin nhỏ nhỏ bên dưới để gợi ý cho user cài đặt PWA, nếu user đóng nó lại, 3 tháng sau nó mới hiện lại để hỏi user của muốn cài không, thời gian lâu như vậy là để tránh làm phiền user.

![](https://developers.google.com/web/updates/images/2018/06/a2hs-infobar-cropped.png)

Đây là một tính năng chỉ có trên Chrome cho Android (Chrome 76, phát hành tháng 6 năm 2019), nếu cảm thấy không cần thiết làm phiền user như vậy, chúng ta tắt luôn nó đi

```js{3}
window.addEventListener('beforeinstallprompt', (e) => {
  // chặn thanh mini info bar
  e.preventDefault();
  deferredPrompt = e;
  showInstallPromotion();
});
```

## Xác định app đã cài thành công chưa

Để biết ứng dụng PWA đã cài thành công chưa, chúng ta có thể bắt lấy sự kiện `appinstalled`

```js
window.addEventListener('appinstalled', (evt) => {
  console.log('a2hs installed');
});
```

## Xác định user có mở ứng dụng web từ màn hình chính không (không thông qua trình duyệt)

Sử dụng câu media `display-mode` để định dạng lại nếu cần thiết

```css
@media all and (display-mode: standalone) {
  body {
    background-color: yellow;
  }
}
```

Chúng ta cũng xác định được `display-mode` bên trong javascript

```js
if (window.matchMedia('(display-mode: standalone)').matches) {
  console.log('display-mode is standalone');
}
```

với Safari chúng ta có cách xác định khác

```js
if (window.navigator.standalone === true) {
  console.log('display-mode is standalone');
}
```
