Có nhiều tình huống ứng dụng web cần thực hiện giao tiếp 2 chiều trên channel với service worker

Ví dụ podcast PWA cho phép [user download nội dung về offline](https://web.dev/app-like-pwas/#proactive-background-downloading) đồng thời thông báo đến người dùng quá trình đã đi đến đâu

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/HIbZyXbQNijm1S4eQlEv.png?auto=format&w=964)

## Sử dụng workbox

Class [Workbox](https://developers.google.com/web/tools/workbox/reference-docs/latest/module-workbox-window.Workbox) cung cấp phương thức `messageSW()` gửi đi một thông điệp đến các service work đã đăng ký và đợi phản hồi.

Phần code bên dưới sẽ tạo mới một `Workbox` instance và gửi đi 1 thông điệp đến service worker về version hiện tại

```js
const wb = new Workbox('/sw.js');
wb.register();

const swVersion = await wb.messageSW({type: 'GET_VERSION'});
console.log('Service Worker version:', swVersion);
```

Thực hiện phần listener trên service worker

```js
const SW_VERSION = '1.0.0';

addEventListener('message', (event) => {
  if (event.data.type === 'GET_VERSION') {
    event.ports[0].postMessage(SW_VERSION);
  }
});
```

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/DxyWzq96JQCU3hADZiN8.png?auto=format&w=845)

## Sử dụng API của trình duyệt

Cách hoạt động

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/nPYcdQbxAezigMiuoLJg.png?auto=format&w=741)

### Broadcast Channel API

Trước tiên, chúng ta khởi tạo object `BroadcastChannel` với cùng ID để gửi và nhận message

```js
const broadcast = new BroadcastChannel('channel-123');
```

Object `BroadcastChannel` cung cấp phương thức `postMessage()` để gửi message đến bất kỳ một listener nào

```js
broadcast.postMessage({ type: 'MSG_ID' })
```

Ở phía trình duyệt, chúng ta cho listen trên các message được gửi đi bằng phương thức `onmesssage` của object `BroadcastChannel`

```js
//listen trên messages
broadcast.onmessage = (event) => {
  if (event.data && event.data.type === 'MSG_ID') {
    //xử lý message...
  }
};
```

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/uWl3NVD3rxbSoA0JxvuN.png?auto=format&w=845)

### Client API

Vì trang chỉ dùng một service worker, việc gửi nhận message có thể thực hiện trực tiếp trên `serviceWorker`

```js
// gửi message
navigator.serviceWorker.controller.postMessage({
  type: 'MSG_ID',
});

// listen trên messages
navigator.serviceWorker.onmessage = (event) => {
  if (event.data && event.data.type === 'MSG_ID') {
    //xử lý response
  }
};
```

Tương tự, thực hiện phần listener trên `onmessage`

```js
//listen trên messages
self.addEventListener('message', (event) => {
  if (event.data && event.data.type === 'MSG_ID') {
    //xử lý message
  }
});
```

Với các tương tác ngược trở lại với bất ký client nào, service worker nhận một mảng các object `WindowClient` thông qua việc gọi các phương thức như `Clients.matchAll()`, hay `Clients.get()`. Sau đó có thể gọi `postMessage()`


```js
//lấy về màng Window client objects
self.clients.matchAll(options).then(function (clients) {
  if (clients && clients.length) {
    // gửi tín hiệu đến tab đang focus
    clients[0].postMessage({type: 'MSG_ID'});
  }
});
```

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/oApyHsmljr1KkBW85Wv9.png?auto=format&w=650)

### Message Channel

Khởi tạo một object `MessageChannel` và sử dụng nó để gửi đến port đã đăng ký với service worker.

```js
const messageChannel = new MessageChannel();

// khởi tạo port
navigator.serviceWorker.controller.postMessage({type: 'PORT_INITIALIZATION'}, [
  messageChannel.port2,
]);

// lắng nghe trên messages
messageChannel.port1.onmessage = (event) => {
  // xử lý message
};
```

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/THMp68FKAah5qYIsiFOk.png?auto=format&w=741)

Service worker khi nhận được port này, lưu vào để sử dụng khi cần gửi đi message

```js
let communicationPort;

//lưu port
self.addEventListener('message', (event) => {
  if (event.data && event.data.type === 'PORT_INITIALIZATION') {
    communicationPort = event.ports[0];
  }
});

//gửi messages
communicationPort.postMessage({type: 'MSG_ID'});
```

### Sync ngầm

Một ứng dụng chat sẽ phải đảm bảo tin nhắn không bị mất đi nếu kết nối không tốt. [Background Sync API](https://developers.google.com/web/updates/2015/12/background-sync) cho phép chúng ta chạy lại tác vụ khi user có đường kết nối tốt hơn.

Thay vì dùng `postMessage`, chúng ta đăng ký `sync`

```js
navigator.serviceWorker.ready.then(function (swRegistration) {
  return swRegistration.sync.register('myFirstSync');
});
```

Service worker sau đó lắng nghe trên event `sync` để xử lý message

```js
self.addEventListener('sync', function (event) {
  if (event.tag == 'myFirstSync') {
    event.waitUntil(doSomeStuff());
  }
});
```

`doSomeStuff` phải trả về một `Promise`

Một khi tác vụ đã được thực thi, service worker sau đó cập nhập thông tin này đến ứng dụng bằng các API chúng ta đã đề cập.

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/eMbvk9IRoaAggs8t5CbF.png?auto=format&w=845)

### Fetch ngầm

[Background fetch api](https://developers.google.com/web/updates/2018/12/background-fetch) cho phép thực hiện các xử lý như download video, podcast ngầm mà không ảnh hưởng đến main thread

Để giao tiếp với từ ứng dụng đến service worker, dùng `backgroundFetch.fetch` thay vì `postMessage`

```js
navigator.serviceWorker.ready.then(async (swReg) => {
  const bgFetch = await swReg.backgroundFetch.fetch(
    'my-fetch',
    ['/ep-5.mp3', 'ep-5-artwork.jpg'],
    {
      title: 'Episode 5: Interesting things.',
      icons: [
        {
          sizes: '300x300',
          src: '/ep-5-icon.png',
          type: 'image/png',
        },
      ],
      downloadTotal: 60 * 1024 * 1024,
    },
  );
});
```

`BackgroundFetchRegistration` cho phép ứng dụng listen trên các event `progress`

```js
bgFetch.addEventListener('progress', () => {
  // If we didn't provide a total, we can't provide a %.
  if (!bgFetch.downloadTotal) return;

  const percent = Math.round(
    (bgFetch.downloaded / bgFetch.downloadTotal) * 100,
  );
  console.log(`Download progress: ${percent}%`);
});
```

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/Ii1koJyCl0drJyewTIxV.png?auto=format&w=845)

[Code demo](https://bgfetch-http203.glitch.me/)
[Hướng dẫn Background Fetch](https://developers.google.com/web/updates/2018/12/background-fetch)
