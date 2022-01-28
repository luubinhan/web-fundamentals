# Thông báo cập nhập bằng service worker

Trong nhiều tính huống, service worker cần chủ động giao tiếp với tab đang active để thông báo một sự kiện nào đó, ví dụ:
- Khi có một service worker mới được cài đặt, để trang có thể hiện "Update to refresh" phía dưới để user cập nhập các nội dung mới
- Cho user biết các dữ liệu đang được cache bên dưới service worker, kiểu như "Trang web đã có thể hoạt động lúc offline", "Nội dung mới đã được cập nhập"

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/RpZYhHYGpPY9e3AjxuaQ.png?auto=format&w=650)

## Sử dụng Workbox

### Lăng nghe các sự kiện lifecycle

Thư viện sử dụng các API `updatefound` và `statechange` bên dưới. [Danh sách các event lifecycle](https://developers.google.com/web/tools/workbox/modules/workbox-window#important_service_worker_lifecycle_moments)

Ví dụ khi có service worker mới được cài đặt

```js
const wb = new Workbox('/sw.js');

wb.addEventListener('installed', (event) => {
  if (event.isUpdate) {
    // Hiển thị "Update App"
  }
});

wb.register();
```

### Thông báo các thay đổi trên cache

[workbox-broadcast-update](https://developers.google.com/web/tools/workbox/modules/workbox-broadcast-update) cung cấp phương thức thông báo đến window khi cache có thay đổi.

```js
import {registerRoute} from 'workbox-routing';
import {StaleWhileRevalidate} from 'workbox-strategies';
import {BroadcastUpdatePlugin} from 'workbox-broadcast-update';

registerRoute(
  ({url}) => url.pathname.startsWith('/api/'),
  new StaleWhileRevalidate({
    plugins: [
      new BroadcastUpdatePlugin(),
    ],
  })
);
```

Bên trong App lắng nghe các sự kiện này như sau

```js
navigator.serviceWorker.addEventListener('message', async (event) => {

  if (event.data.meta === 'workbox-broadcast-update') {
    const {cacheName, updatedUrl} = event.data.payload;
    
    const cache = await caches.open(cacheName);
    const updatedResponse = await cache.match(updatedUrl);
    const updatedText = await updatedResponse.text();
  }
});
```

## Sử dụng API trình duyệt

Nếu Workbox không đủ dùng, dùng API của trình duyệt

Thông báo khi có một service worker được cài đặt

```js
// tạo một channel để gửi message
const broadcast = new BroadcastChannel('sw-update-channel');

self.addEventListener('install', function (event) {
	// thông báo khi có một service worker được install
  broadcast.postMessage({type: 'CRITICAL_SW_UPDATE'});
});
```

Trang đăng ký trên channel `sw-update-channel`

```js
const broadcast = new BroadcastChannel('sw-update-channel');

broadcast.onmessage = (event) => {
  if (event.data && event.data.type === 'CRITICAL_SW_UPDATE') {
  }
};
```

Gửi message đến tab đang focus

```js
self.clients.matchAll(options).then(function (clients) {
  if (clients && clients.length) {
    // Respond to last focused tab
    clients[0].postMessage({type: 'MSG_ID'});
  }
});
```

Thực hiện phần sử lý khi nhận được message

```js
navigator.serviceWorker.onmessage = (event) => {
     if (event.data && event.data.type === 'MSG_ID') {
         // Process response
   }
};
```

Message Channel yêu cầu các bước cài đặt đầu tiên, bao gồm gửi đi port của trang đến service worker để có thể giao tiếp qua lại

```js
const messageChannel = new MessageChannel();

navigator.serviceWorker.controller.postMessage({type: 'PORT_INITIALIZATION'}, [
  messageChannel.port2,
]);
```

Hiện thực phần handler cho `onmessage`

```js
messageChannel.port1.onmessage = (event) => {
  // Process message
};
```

Service worker nhận lấy port này và lưu lại

```js
let communicationPort;

self.addEventListener('message', (event) => {
  if (event.data && event.data.type === 'PORT_INITIALIZATION') {
    communicationPort = event.ports[0];
  }
});
```

Từ đây, nó có thể gửi thông báo đến trang, bằng cách gửi `postMessage()`

```js
communicationPort.postMessage({type: 'MSG_ID' });
```