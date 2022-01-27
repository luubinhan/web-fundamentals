Tổng quan về Worker

Web Worker và Service worker có thể cải thiện tốc độ trang bằng cách nào và khi nào thì sử dụng web worker, khi nào dùng service worker

Trình duyệt sử dụng single thread để chạy tất cả JS của một trang web, cũng như các tác vụ render trang và thực hiện các xử lý garbage collection. Chạy các JS xử lý cồng kền có thể block main thread và làm trì trễ các xử lý khác, dẫn đến trải nghiệm người dùng không tốt.

Trên các ứng dụng iOS/Android, một cách xử lý phổ biến là đưa các tác vụ xử lý này ra một thread khác để main thread rảnh tay xử lý các yêu cầu từ user.

Một cách xử lý tương tự như vậy trên web là dùng *worker* để chạy các xử lý ngầm. Worker là một JS chạy hẳn ở một thread khác, có scope riêng và không có chia sẽ bất kỳ dữ liệu nào với main thread

Chúng ta cùng tìm hiểu 2 worker khá tương đồng là Web Worker và Service Worker

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/eN5kePr9U0aZMgCyekhJ.png?auto=format&w=964)

Một vài điểm chung giữa service worker và web worker

- Đều chạy ngoài main thread, không block main thread và giao diện người dùng
- Không thể truy cập vào `Window` và `Document`, do đó không thể tương tác trực tiếp với DOM, bị giới hạn một số API của trình duyệt

Điểm khác nhau

- Không giống web worker, service worker cho phép tương tác với network request (thông qua `fetch`) và lắng nghe Push event
- Một trang có thể có nhiều web worker, nhưng chỉ có một service worker để điều khiển tất cả các tab đã đăng ký
- Web worker sẽ gắn liền với một tab đã khởi tạo nó, trong khi service worker độc lập hơn, khi đóng một tab service worker vẫn tiếp tục chạy nền (thậm chí nếu không có tab nào đang mở nó vẫn chạy), trong khi web worker sẽ terminate

# Tính huống sử dụng

Web worker được dùng để đưa các tác vụ tính toán cồng kềnh ra khỏi main thread, để tránh việc block UI

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/ZCC24V8uqi6HfFjRzuPq.png?auto=format&w=650)

Ví dụ team làm game [Proxx](https://proxx.app/)  đưa những xử lý về logic, quản lý state ra luồng xử lý riêng biệt

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/xXNCbahCtPrS8rw6uf1z.png?auto=format&w=296)

Service worker thường đi liền với cách proxy network, caching và giúp ứng dụng hoạt động offline

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/GmGcVnb2y1yNc4ZIFFQ8.png?auto=format&w=741)

Ví dụ [podcast PWS](https://bgfetch-http203.glitch.me/) cho phép user download toàn bộ về để nghe offline, nếu user đóng một tab đang download, task này sẽ không bị gián đoạn.

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/oUH6K2JvcmfdAynTMjxQ.png?auto=format&w=650)

# Công cụ và thư viện

Tất nhiên vẫn có thể tự xử lý mọi thứ ở mức low level. Các thư viện hỗ trợ sẽ giúp xử lý các tình huống sử dụng hay thấy. Có thể tham khảo qua 2 thư viện [Comlink](https://github.com/GoogleChromeLabs/comlink) và [Workbox](https://developers.google.com/web/tools/workbox)

![https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/MNMwX5KuJt5iOLZJbdO0.png?auto=format&w=650]