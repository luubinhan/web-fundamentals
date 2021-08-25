![](https://web-dev.imgix.net/image/MtjnObpuceYe3ijODN3a79WrxLU2/JtyY7nYUTCt2Q9oFYvEL.png?auto=format&w=964)

> TTI thời gian để một trang có thể tương tác hoàn toàn

Trang được gọi là **tương tác hoàn toàn** khi

- Nội dung được hiển thị
- Tất cả các handler được đăng ký cho gần như tất cả các element hiện thị trên trang
- Trang có thể response đến user trong khoản 50ms

| TTI      | Màu              |
| -------- | ---------------- |
| 0-3.8    | Xanh - nhanh     |
| 3.9-7.3  | Cam - trung bình |
| Trên 7.3 | Đỏ - chậm        |

Để cải thiện TTI, tham khảo bài viết

- [optimize your JavaScript](https://web.dev/fast#optimize-your-javascript)
- [reducing JavaScript payloads with code splitting](https://web.dev/reduce-javascript-payloads-with-code-splitting)
- [applying the PRPL pattern](https://web.dev/apply-instant-loading-with-prpl)
- [Optimizing third-party JavaScript](https://web.dev/fast/#optimize-your-third-party-resources)
- [Minimize main thread work](https://web.dev/mainthread-work-breakdown)
- [Reduce JavaScript execution time](https://web.dev/bootup-time)

