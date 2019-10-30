# Mô hình vỏ ứng dụng

**Mô hình vỏ ứng dụng** (app shell model) là một phương pháp tiếp cận để xây dựng Progressive Web App, giống như những gì bạn thấy trên một ứng dụng mobile native, **bộ khung sườn** (vò) đã có sẵn khi bạn cài app, phần **thịt** sẽ được đắp theo thời điểm user mở app.

**Khung sườn** ở đây là HTML, CSS, Javascript căn bản nhất của ứng dụng, những thành phần không đổi theo thời gian sẽ được cache lại bên dưới client để có thể mở ứng dụng nhanh hơn cho các lần truy cập tiếp theo.

Với các ứng dụng SPA (Single Page App), đây là phương pháp tiếp cận phải làm. Cách làm này sử dụng service worker để cache cái **sườn** lại.

![Application Shell architecture](https://developers.google.com/web/fundamentals/architecture/images/appshell.png)

Nói một cách khác, nó giống như toàn bộ bundle của của native app. Toàn bộ phần khung sẽ nằm đó, còn dữ liệu sẽ `fetch` khi chạy app.

## Lợi ích

- **Nhanh**: với những lần truy cập tiếp theo của user, tốc độ sẽ được cải thiện đáng kể.
- **Gần giống với Native App**: tương tác sẽ gần giống như những ứng dụng chạy offline
- **Dữ liệu được sử dụng hợp lý**: với tốc độ mạng ì ạch, việc tiết kiệm dữ liệu truyền tải là điều đáng quan tâm.

## Xây dựng

Dựng cấu trúc cho app một cách rõ ràng giữa đâu là **sườn** đâu là **thịt**

Ví dụ như bên dưới là ứng dụng Wikipedia, bên trái là phần **sườn**, hình giữa là sao khi đã đắp thêm *thịt*

![Offline Wikipedia app using an application shell with content caching](https://developers.google.com/web/fundamentals/architecture/images/wikipedia.jpg)

Ví dụ đây là một sườn HTML chưa đắp *thịt*

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>App Shell</title>
  <link rel="manifest" href="/manifest.json">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" type="text/css" href="styles/inline.css">
</head>

<body>
  <header class="header">
    <h1 class="header__title">App Shell</h1>
  </header>

  <nav class="nav">
  ...
  </nav>

  <main class="main">
  ...
  </main>

  <div class="dialog-container">
  ...
  </div>

  <div class="loader">
    <!-- Show a spinner or placeholders for content -->
  </div>

  <script src="app.js" async></script>
  <script>
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('/sw.js').then(function(registration) {
      // Registration was successful
      console.log('ServiceWorker registration successful with scope: ', registration.scope);
    }).catch(function(err) {
      // registration failed :(
      console.log('ServiceWorker registration failed: ', err);
    });
  }
  </script>
</body>
</html>
```

Một đoạn code ví dụ sử dụng service worker để cache

```js
var cacheName = 'shell-content';
var filesToCache = [
  '/css/styles.css',
  '/js/scripts.js',
  '/images/logo.svg',

  '/offline.html',

  '/',
];

self.addEventListener('install', function(e) {
  console.log('[ServiceWorker] Install');
  e.waitUntil(
    caches.open(cacheName).then(function(cache) {
      console.log('[ServiceWorker] Caching app shell');
      return cache.addAll(filesToCache);
    })
  );
});
```

Xem thêm phần hướng dẫn chi tiết ở [blog Làm Progressive Web App cho người mới](http://vuilaptrinh.com/2018-10-16-huong-dan-progressive-web-app-cho-nguoi-moi-bat-dau/)


[https://developers.google.com/web/fundamentals/architecture/app-shell](https://developers.google.com/web/fundamentals/architecture/app-shell)
