Cơ chế đánh giá của Lighthouse dựa trên cách tính điểm số, và trọng số ứng với từng mục. Trọng số sẽ thay đổi theo thời gian, vì team lighthouse nghiên cứu và tổng hợp feedback từ phía người dùng để có nhận định đâu là yếu tố ảnh hưởng đến trải nghiệm người dùng lớn nhất

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/rLftIdSA8JJYruHOHrOn.png?auto=format&w=650)

Hệ thống đánh giá của Lighthouse 8

| Tiêu chí                                                     | Trọng Số |
| ------------------------------------------------------------ | -------- |
| [First Contentful Paint](https://web.dev/first-contentful-paint/) | 10%      |
| [Speed Index](https://web.dev/speed-index/)                  | 10%      |
| [Largest Contentful Paint](https://web.dev/lcp/)             | 25%      |
| [Time to Interactive](https://web.dev/interactive/)          | 10%      |
| [Total Blocking Time](https://web.dev/lighthouse-total-blocking-time/) | 30%      |
| [Cumulative Layout Shift](https://web.dev/cls/)                                      | 15%      |

Dãy màu được quy định như sau

- 0 đến 49: đỏ - tệ
- 50 đến 89: cam - cần cải thiện
- 90 đến 100: xanh - ổn

Đạt được điểm số trong khoản 90 và 100 là tốt, không nhất thiệt phải đạt tuyệt đối 100.

Công cụ [Lighthouse scoring calculator](https://googlechrome.github.io/lighthouse/scorecalc/)

https://web.dev/performance-scoring/
