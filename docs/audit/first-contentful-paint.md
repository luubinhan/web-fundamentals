![](https://web-dev.imgix.net/image/MtjnObpuceYe3ijODN3a79WrxLU2/0zDaizn5WxE6QfToxVMG.png?auto=format&w=845)

FCP đo thời gian trình duyệt cần để render content DOM đầu tiên khi user truy cập. Image, `<canvas>`, SVG được xem là content DOM, những gì bên trong iframe không được tính

Kết này này sẽ được đem so sánh với một [website thực tế](https://httparchive.org/reports/loading-speed#fcp) được xem là có tốc độ load nhanh nhất

| FCP (s) | Màu          |
| ------- | ------------ |
| 0-1.8   | Xanh - nhanh |
| 1.8-3   | Cam - tạm    |
| Trên 3  | Đỏ - chậm    |

Một trong những nhân tố quan trọng ảnh hưởng FCP là thời gian load font. [Xem thêm cách cải thiện](https://web.dev/font-display)

Để quan sát FCP phía người sử dụng website, xem thêm [Tài liệu google](https://developers.google.com/web/fundamentals/performance/user-centric-performance-metrics)

Ví dụ, trên section **Opportunities** của Lighthouse, sẽ khuyến cáo chúng ta cải thiện render-blocking resource

![](https://web.dev/images/shared/opportunities.png)

https://web.dev/first-contentful-paint/