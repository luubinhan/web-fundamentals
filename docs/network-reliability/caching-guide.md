Nhiều website cần giao tiếp với service worker mà không cần biết kết quả trả về. Ví dụ
- Gửi một danh sách các URL cần prefetch, khi user click vào một link hoặc trang con nó sẽ có sẵn trong cahce
- Yêu cầu service worker lấy và cache lại các bài viết mới nhất, để có thể xem offline

Đưa các công việc không quá quan trọng này cho service worker để main thread có thể xử lý những công việc khác

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/gCpdKiIbSDZBMJEJE2tZ.png?auto=format&w=741)

Bài hướng dẫn này sẽ chỉ kỹ thuật giao tiếp *một chiều* đến service worker bằng các API của trình duyệt và bằng thư viện Worker.

## Ví dụ thực tế

Trang web 1-800-Flowers.com sử dụng phương pháp này để *prefetch* bằng `postMessage()` để load các item trên trang category để tăng tốc quá trình hiển thị khi vào trang này.

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/eNMKYuaKnlYu0N3IIhI5.png?auto=format&w=500)

Họ sử dụng kết hợp nhiều phương pháp để quyết định item nào sẽ prefetch

- Ở thời điểm page load, họ yêu cầu service worker lấy JSON của 9 item đầu tiên, và đưa kết quả này xuống cache
- Với các item còn lại, họ lắng nghe trên event `mouseover` để khi user rê chuột lên một item nào, sẽ trigger việc fetch

Sau đó sử dụng [API Cache](https://developer.mozilla.org/docs/Web/API/Cache) để lưu kết quả JSON

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/FH4clAbGShyIdhj4jWdL.png?auto=format&w=964)

## Sử dụng Workbox

[Workbox](https://developers.google.com/web/tools/workbox) hỗ trỡ gửi các message xuống service worker dễ dàng hơn thông qua package [workbox-window](https://developers.google.com/web/tools/workbox/modules/workbox-window)

Để giao tiếp với service worker, trước hết tạo một object Workbox để đăng ký với service worker

```js
const wb = new Werkbox('/sw.js')
wb.register()
```

Sau đó có thể gửi message trực tiếp, không cần phải nghĩ về việc đăng ký thế nào, activate ra sao

```js
wb.messageSW({"type": "PREFETCH", "payload": {"urls": ["/data1.json", "data2.json"]}}); });
```

Service worker sẽ hiện thực phần listen trên message này, nó có thể trả về response hoặc không, trong trường hợp này là không

```js
self.addEventListener('message', (event) => {
	if (event.data && event.data.type === 'PREFETCH') {
    	// do something
	}
})
```

## Sử dụng API của trình duyệt

Nếu Workerbox không đủ những tính năng bạn cần, có thể dụng thẳng các API của trình duyệt không thông qua thư viện

API `postMessage` có thể sử đụng để thực hiện một giao tiếp một chiều giữa trang và service worker

```js
navigator.serviceWorker.controller.postMessage({
  type: 'MSG_ID',
  payload: 'some data to perform the task',
});
```

Phần handler `message` ở phía service worker

```js
self.addEventListener('message', (event) => {
  if (event.data && event.data.type === MSG_ID) {
    // do something
  }
});
```

Tham số `{type: 'MSG_ID'}` không bắt buộc, đây là cách để trang có nhiều hơn các loại message nếu cần, ví dụ để prefetch, để clear storage.

## Ví dụ prefetch đơn giản

Các phương pháp để thực hiện prefetch

- Sử dụng [prefetch tag](https://web.dev/link-prefetch/), resource được giữ trên trình duyệt trong khoảng 5 phút, sau đó rule `Cache-Control` được áp dụng cho resource
- Sử dụng [service worker](https://web.dev/instant-navigation-experiences/) để thực hiện prefetch vượt ngoài các giới hạn thông thường

Prefetch trang chi tiết sản phẩm

```js
navigator.serviceWorker.controller.postMessage({
  type: 'PREFETCH',
  payload: {
    urls: [
      'www.exmaple.com/apis/data_1.json',
      'www.exmaple.com/apis/data_2.json',
    ],
  },
});
```

Thực hiện xử lý bên trong service worker

```js
addEventListener('message', (event) => {
  let data = event.data;
  if (data && data.type === 'PREFETCH') {
    let urls = data.payload.urls;
    for (let i in urls) {
      fetchAsync(urls[i]);
    }
  }
});
```

Hàm `featchAsync` để lặp qua mảng URL và fetch cho từng item

```js
async function fetchAsync(url) {
  let prefetched = await fetch(url);

  // có thể cache lại resource bên trong service worker storage
}
```

Khi đã fetch được JSON data, thường bên trong cũng sẽ có các URL như là đường dẫn hình, cũng là một resource cần phải cache

Ví dụ chúng ta fetch được file JSON

```json
{
  "productName": "banana",
  "productPic": "https://cdn.example.com/product_images/banana.jpeg",
  "unitPrice": "1.99"
}
```

Sửa lại hàm `fetchAsync()`, bổ sung phần cache hình ảnh

```js
async function fetchAsync(url, postProcess) {
  let prefetched = await fetch(url);

  if (postProcess) {
    await postProcess(prefetched);
  }
}

async function postProcess(prefetched) {
  let productJson = await prefetched.json();
  if (productJson && productJson.product_pic) {
    fetchAsync(productJson.product_pic);
  }
}
```

Chúng ta có thể thêm phần xử lý exception, như 404, tuy nhiên việc sử dụng service work chỉ như một phần có thì tốt, không có cũng không ảnh hưởng gì đến user. Nên sẽ không cần thiết thêm phần exception handler ở đây