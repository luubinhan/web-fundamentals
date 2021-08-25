![](https://addyosmani.com/assets/images/infinite-scroll.png)

Với tính năng infinite scrolling user sẽ không thể xem được footer, nội dung sẽ liên tục được chèn thêm vào khi user cuộn đến cuối trang, đây cũng là nguyên nhân dẫn đến **Layout Shift**

Đây cũng là một trong nhưng thử thách khi làm infinite scrolling, khi đến cuối trang footer sẽ xuất hiện 1 hoặc 2 giây trước khi nội dung mới được chèn thêm và sau đó footer lại bị đẩy xuống dưới.

![](https://addyosmani.com/assets/images/infinite-scroll-shift.png)

<iframe width="560" height="315" src="https://www.youtube.com/embed/vo5VTg-9I4g" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Có thể sử dụng **Chrome DevTools > Performance panel > Experience** để xác định những element nào góp phần gây ra layout shift.

![](https://addyosmani.com/assets/images/devtools-cls-fb.png)

## Làm sao để tránh Layout Shift?

> Chừa sẵn khoảng trống bên dưới

Bằng cách ước lượng số element sẽ được chèn thêm và độ cao cần thiết sau khi chèn thêm, hiển thị *Skeleton* element trong lúc đang load.

> Prefetch data

Với các dữ liệu nằm chưa xuất hiện trên màn hình, có thể `prefetch` để khi user có cuộn xuống thì dữ liệu cũng đã có sẵn không cần đợi load

Nếu đang sử dụng React, thư viện [react-window](https://github.com/bvaughn/react-window) là một lựa chọn tốt, khái niệm **virtualizing** của thư viện này được thực hiện như sau

```html
<div> <!-- <= scroll trên element này -->
    <ul> <!-- <= set position relative -->
        <li></li> <!-- set position absolute -->
        ...
        <li></li>
    </ul>
</div>
```

Thay vì render toàn bộ `<li />`, thư viện sẽ tính toán chỉ render số element đủ để lắp đầy màn hình user

