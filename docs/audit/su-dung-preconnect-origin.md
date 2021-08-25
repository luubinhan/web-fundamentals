![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/K5TLz5LOyRjffxJ6J9zl.png?auto=format&w=964)

Sử dụng `<link rel="preconnect">` hoặc `<link rel="dns-prefetch">` để thiết lập đến *origin* chứa third-party đang sử dụng

```html
<link rel="preconnect" href="https://example.com">
```

Nó sẽ thông báo với trình duyệt tiến hành các bước cần thiết để connect đến `https://example.com` sớm nhất có thể, vì sau đó chúng ta sẽ truy xuất đến một resource trên origin trên

> `<link rel="dns-prefetch" href="https://example.com">` được hổ trợ tốt trên mọi trình duyệt, chỉ thiết lập DNS lookup trước

