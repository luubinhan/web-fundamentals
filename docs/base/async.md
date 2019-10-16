# Hàm Async

Hàm `async` được hỗ trợ từ phiên bản Chrome 55, cho phép chúng ta viết code như promise, một cách tuần tự, không ảnh hưởng đến main thread.

```js
async function myFirstAsyncFunction() {
  try {
    const fulfilledValue = await promise;
  }
  catch (rejectedValue) {
    // …
  }
}
```

_Lưu ý: nếu chưa biết promise, bạn cần đọc và nắm rõ promise trước_

Ví dụ, với đoạn code viết bằng Promise, fetch một dữ liệu từ API

```js
function logFetch(url) {
  return fetch(url)
    .then(response => response.text())
    .then(text => {
      console.log(text);
    }).catch(err => {
      console.error('fetch failed', err);
    });
}
```

Nếu viết bằng hàm `async`

```js
async function logFetch(url) {
  try {
    const response = await fetch(url);
    console.log(await response.text());
  }
  catch (err) {
    console.log('fetch failed', err);
  }
}
```

Số lượng dòng code không đổi, nhưng không còn `callback`, code dễ đọc hơn, đặc biết với những ai chưa nắm cách hoạt động của promise.


## Giá trị trả về của async

Hàm `async` luôn trả về về Promise, dù bên trong có await hay không. Promise này sẽ được `resolve` khi hàm có `return` hoặc `reject` khi hàm `throw` một error

```js
// wait ms milliseconds
function wait(ms) {
  return new Promise(r => setTimeout(r, ms));
}

async function hello() {
  await wait(500);
  return 'world';
}
```

Gọi `hello()` trả về một promise, với giá trị `world`.

```js
async function foo() {
  await wait(500);
  throw Error('bar');
}
```

Gọi `foo()` trả về một promise với `reject` bằng `Error('bar')`

## Streaming một response

Lợi ích của hàm `async` thấy rõ hơn trong các ứng dụng phức tạp hơn. Ví dụ như chúng ta truyền đi một response trong khi log lại từng kết quả nhỏ, và trả về kích thước cuối cùng

```js
function getResponseSize(url) {
  return fetch(url).then(response => {
    const reader = response.body.getReader();
    let total = 0;

    return reader.read().then(function processResult(result) {
      if (result.done) return total;

      const value = result.value;
      total += value.length;
      console.log('Received chunk', value);

      return reader.read().then(processResult);
    })
  });
}
```

Nếu bạn không biết closure như những lập trình viên thông minh khác, bạn sẽ khó để *nuốt* được đoạn code trên.

Viết lại với hàm `async`

```js
async function getResponseSize(url) {
  const response = await fetch(url);
  const reader = response.body.getReader();
  let result = await reader.read();
  let total = 0;

  while (!result.done) {
    const value = result.value;
    total += value.length;
    console.log('Received chunk', value);
    // get the next result
    result = await reader.read();
  }

  return total;
}
```

Cảm giác là người thông minh nhất quả đất không còn khi viết bằng `async`, code quá trực quan ngay cả người nào mới học code cũng hiểu.

## Một số cú pháp viết hàm async khác

Bạn đã biết cách khai báo `async function() {}`, từ khóa `async` còn được dùng bằng nhiều cách viết khác

**Arrow function**

```js
const jsonPromises = urls.map(async url => {
  const response = await fetch(url);
  return response.json();
});
```

**Phương thức của object**

```js
const storage = {
  async getAvatar(name) {
    const cache = await caches.open('avatars');
    return cache.match(`/avatars/${name}.jpg`);
  }
};

storage.getAvatar('jaffathecake').then(…);
```

**Phương thức của class**

```js
class Storage {
  constructor() {
    this.cachePromise = caches.open('avatars');
  }

  async getAvatar(name) {
    const cache = await this.cachePromise;
    return cache.match(`/avatars/${name}.jpg`);
  }
}

const storage = new Storage();
storage.getAvatar('jaffathecake').then(…);
```

## Cẩn thận

Nhìn code bây giờ sẽ rất **step-by-step**, nhưng bạn có thể bỏ qua cơ hội chạy song song nhiều code cùng lúc

```js
async function series() {
  await wait(500); // đợi 500ms…
  await wait(500); // …đợi thêm 500ms.
  return "done!";
}
```

Đoạn code bên dưới sẽ đợi mất 500ms để chạy xong.

```js
async function parallel() {
  const wait1 = wait(500); // Start a 500ms timer asynchronously…
  const wait2 = wait(500); // …meaning this timer happens in parallel.
  await wait1; // Wait 500ms for the first timer…
  await wait2; // …by which time this timer has already finished.
  return "done!";
}
```


### Ví dụ, kết quả fetch theo thứ tự

Theo kiểu Promise

```js
function logInOrder(urls) {
  // fetch all the URLs
  const textPromises = urls.map(url => {
    return fetch(url).then(response => response.text());
  });

  // log them in order
  textPromises.reduce((chain, textPromise) => {
    return chain.then(() => textPromise)
      .then(text => console.log(text));
  }, Promise.resolve());
}
```

Chúng ta đang dùng `reduce` để nối một chuỗi các promise với nhau. Chúng ta quá thông mình rồi. Tuy code thông minh quá như vậy rất khó bảo trì.

Nếu bạn code lại bằng async, bạn sẽ nghĩ liền tới giải pháp

❌ Không khuyến khích
```js
async function logInOrder(urls) {
  for (const url of urls) {
    const response = await fetch(url);
    console.log(await response.text());
  }
}
```

✅ Chuẩn

```js
async function logInOrder(urls) {
  // fetch all the URLs in parallel
  const textPromises = urls.map(async url => {
    const response = await fetch(url);
    return response.text();
  });

  // log them in sequence
  for (const textPromise of textPromises) {
    console.log(await textPromise);
  }
}
```

Tại sao chuẩn? Với cách một, mỗi câu fetch phải **đợi cho xong** thì cầu thứ tiếp theo mới chạy, với cách 2, chúng ta cho phép nó chạy đồng thời nhiều câu fetch.

## Trình duyệt nào hỗ trợ

Thời điểm năm 2019, bạn có thể sử dụng trên Chrome, Edge, Firefox và Safari

Bạn cần dùng Babel polyfill cho các trình duyệt cũ hơn.

Một khi tất cả trình duyệt đã hỗ trợ async, viết hết bằng async cho những hàm trả về promise, code sẽ bớt thông minh hơn, đồng thời còn đảm bảo hàm luôn trả về Promise
