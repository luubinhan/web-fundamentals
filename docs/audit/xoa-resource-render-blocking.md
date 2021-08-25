![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/8xAmOUQsM8sfEAgzLBis.png?auto=format&w=964)

Lighthouse đánh dấu 2 loại resource gây render-blocking: script và stylesheet

`<script/>`

- Trong thẻ `<head>`
- Không có attribute `defer`
- Không có attribute `async`

`<link rel="stylesheet">`

- Không có attribute `disabled` (nếu có nó sẽ không download stylesheet)
- Không có attribute `media` phù hợp với thiết bị, `media="all"` được xem là render-blocking

## Làm sao để xác định render-blocking resource

Trên Chrome DevTools, mở tab Coverage để xác định CSS và JS không cần thiết

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/Y2Yc8DMcBYMknz5y0Are.png?auto=format&w=964)

Click vào đường dẫn của file trên cửa sổ **Source**, sẽ được dánh dầu 2 màu

- Xanh(critical): cần thiết cho first-paint
- Đỏ(non-critical): chưa cần thiết với các nội dung đang hiển thị 

Phương pháp cải thiện: inline script, stylesheet nào critical, phần non-critical có thể load async

