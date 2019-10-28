# Sử dụng công cụ đánh giá hiệu năng

Mục tiêu chính cho việc đánh giá hiệu năng có nhiều loại, ứng với từng loại chúng ta có cách audit tương ứng

| Mục tiêu  | Test gì  |
|---|---|
| Đảm bảo an toàn, bảo mật dữ liệu và các API mới nhất của trình duyệt  | HTTPS được hỗ trợ trên tất cả các trang/route và tài nguyên  |
| Tăng tốc độ load  | Javascript và css có thể load bất đồng bộ hoặc deffered. Đặt mục tiêu thời gian cụ thể cho trang (53% user bỏ qua một trang nếu tốn hơn 3s để load)  |
| Giảm dung lượng trang  | Lần đầu tiên load 400kb. Kiểm tra các file javascript lớn. Image, media, HTML, CSS nào không cần thiết. Bật lazy load cho hình. Xem thêm công cụ [coverage tools](https://developers.google.com/web/updates/2017/04/devtools-release-notes#coverage)  |
| Giảm số lượng request  | Kiểm tra các request nào không cần thiết, thí dụ file được load lại nhiều lần giống nhau, load các đoạn javascript giống nhau nhưng khác version, css không bao giờ sử dụng, hình chưa hiển thị trên viewport  |
| Tối ưu bộ nhớ  | Sử dụng Chrome Task Manager để so sanh site của bạn đã sử dụng bao nhiêu bộ nhớ, so với các site khác  |
| Giảm CPU | Kiểm tra javascript nặng, đoạn nào chạy không cần thiết ở first load, các version javascript có trùng lặp.  |

## Kiểm tra bộ nhớ và CPU

Trước khi thay đổi, chúng ta lưu lại thông tin hiện tại của bộ nhớ và CPU đã sử dụng

Trong Chrome, mở **Task Manager** trong **menu chính > Other tools** (Shift + Esc)

![](https://developers.google.com/web/fundamentals/performance/audit/images/task-manager.png)

## Kiểm tra lần load đầu tiên và các lần load tiếp theo

[Lighthouse](https://developers.google.com/web/tools/lighthouse/), [WebPagetest](https://www.webpagetest.org/easy) và [Pagespeed Insights](https://developers.google.com/speed/pagespeed/insights/) là những công cụ hữu ích để đánh giá tốc độ, dữ liệu và resource sử dụng. WebPageTest cũng đồng thời check những nội dung static đang được cache, site có tốt hơn khi sử dụng CDN

![](https://developers.google.com/web/fundamentals/performance/audit/images/lighthouse-download-1000.png)
