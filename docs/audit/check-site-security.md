Bạn không thể tạo trang PWA mà không dùng https

HTTPS là nền tảng bảo mật của website, rất nhiều API khác sẽ không thể chạy nếu không dùng HTTPS. Bạn có thể đọc lại [Tại sao cần HTTPS](security/tai-sao-can-https)

![Từ Chrome 68](https://developers.google.com/web/fundamentals/performance/audit/images/not-secure.png)
Từ phiên bản Chrome 68, thanh address sẽ thông báo nếu site bạn không chạy trên nền HTTPS

HTTPS cần được chạy ở mọi nơi, không phải chỉ ở trang đăng nhập, đăng ký, mua hàng. Tất cả những tài nguyên nào không dùng HTTPS cũng là cơ hội cho hacker tấn công.

Để kiểm tra, dùng DevTools của Chrome, mở cửa sổ **Security**

Site bên dưới không bảo mật tốt, một số file được truyền qua HTTP

![](https://developers.google.com/web/fundamentals/performance/audit/images/devtools-security-1000.png)
