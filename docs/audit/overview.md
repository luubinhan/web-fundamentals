# Đánh giá hiệu năng

## Tại sao và cái gì?

Bạn chắc đã nghe đến những điều tốt đẹp mà kỹ thuật Progressive Web App có thể làm cho trang web. Bạn sẽ muốn thêm các tính năng của PWA vào site.

Dù cho có bao nhiêu điều hay ho của PWA được thêm vào, nó cũng không thể giải quyết những vấn đề như Javascript blocking render, ảnh quá lớn. PWA chỉ chạy tốt khi có một nền tảng đã tốt.

Làm sao để bạn kiểm tra tình hình hiện tại của website? Trước tiên bạn phải xác định mục tiêu của việc audit là gì: như những chỗ nào đang hoạt động tốt, những điểm nào cần được cải thiện.

Chạy audit để giúp bạn xác định được đâu là điều cần cải thiện cho site, đâu là điểm cần được xếp ưu tiên cao hơn những điểm khác, tổng hợp được kết quả đạt được sau khi cải tiến.


## Nếu bạn chỉ có 5 phút

Bạn có thể chạy [Lighthouse](https://developers.google.com/web/tools/lighthouse/) trên trang chủ và lưu kết quả báo cáo. Bạn sẽ có danh sách những việc cần làm để cải thiện hiệu năng, accessibility, security và SEO.

## Nếu bạn có 30 phút

[Lighthouse](https://developers.google.com/web/tools/lighthouse/) vẫn là công cụ tốt để bắt đầu audit, nhưng nếu có thời gian bạn có thể xem qua những công cụ hữu ích khách

- [Chrome DevTools Security](https://developers.google.com/web/tools/chrome-devtools/security): có sử dụng HTTPS không
- [Chrome DevTools Network](https://developers.google.com/web/tools/chrome-devtools/network-performance/): kiểm tra thời gian load, kích thước resource, số lượng request HTML, CSS, Javascript, image, font, các loại file khác
- Chrome Task Manager: nếu site sử dụng quá nhiều CPU hoặc bộ nhớ hơn các ứng dụng khác, bạn phải xem đến vấn đề memory leak, task running. Đảm bảo kiểm tra trang web trên thiết bị mà user của bạn hay sử dụng.
- [WebPagetest](https://www.webpagetest.org/easy) thực hiện nhiều kiểu kết nối, địa điểm truy cập khác nhau để đánh giá hiệu năng.
- [Pagespeed Insights](https://developers.google.com/speed/pagespeed/insights/): tốc độ load, kích thước dữ liệu, bao gồm thống kê từ những người dùng Chrome trong thực tế
- [Speed Scorecard and Impact Calculator](https://www.thinkwithgoogle.com/feature/mobile/) so sánh tốc độ của site với các trang tương tự.

Luôn nhớ kiểm tra site như lúc user lần đầu tiên truy cập (chưa bị cache), sử dụng Incognito Window.

## Nếu bạn không biết sử dụng các công cụ trên

Bạn có thể đọc thêm hướng dẫn [How To Think About Speed Tools](https://developers.google.com/web/fundamentals/performance/speed-tools/)

Có thể sử dụng Lighthouse để kiểm tra:

- [HTTPS](https://developers.google.com/web/fundamentals/security/encrypt-in-transit/why-https)
- Server: web server và CDN phải [sử dụng đúng compression, HTTP/2](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/optimize-encoding-and-transfer), và bao gồm [header phù hợp](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching) để trình duyệt có thể cach resource
- Script có thể đưa xuống cuối của trang hoặc sử dụng thuộc tính [async hoặc defer](http://peter.sh/experiments/asynchronous-and-deferred-javascript-execution-explained/)
- Các thư viện javascript có thể bỏ
- [Javascript](https://developers.google.com/web/updates/2017/04/devtools-release-notes), [CSS](https://umaar.com/dev-tips/121-css-coverage/) không được sử dụng
- Ảnh nào chưa được nén, sử dụng kiểu file chưa tối ưu

## Đối tượng khách hàng, hoàn cảnh, và những người liên quan

Ưu tiên tối ưu phần nào phụ thuộc vào khách hàng của bạn là ai, hoàn cảnh hiện tại, cũng như những người liên quan đến site. Bạn [đầu tư bao nhiêu cho tối ưu](https://www.performancebudget.io/)? Nếu chưa trả lời được những câu hỏi này, bạn có thể đọc thêm bài [Your audience, your content](https://developers.google.com/web/ilt/pwa/your-audience-your-content), [Design for all your users](https://developers.google.com/web/ilt/pwa/design-for-all-your-users)

https://developers.google.com/web/fundamentals/performance/audit
