# Những cách làm phổ biến để gợi ý cài đặt PWA trên mobile

Khi user cài đặt ứng dụng web lên màn hình ứng dụng của điện thoại, mức độ *thân mật* với user của chúng ta đã tăng lên đáng kể, họ truy cập ứng dụng thường xuyên hơn, dùng ứng dụng lâu hơn, tương tác nhiều hơn trên ứng dụng.

**Nếu user truy cập ứng dụng web của bạn hàng tuần, có khi 2 ba lần một tuần, điều đó cho thấy user đã *sẵn sàng* cài đặt ứng dụng web lên màn hình chính**

## Vài cái best practice

1. Luồng "Gợi ý cài đặt" nên tách ra với các luồng khác. Ví dụ, đừng đặt nút cài đặt PWA trong màn hình login, ngay cạnh nút login. Nó sẽ ảnh hưởng tới luồng chính mà user đang làm "Đăng Nhập"
2. Cung cấp tính năng "Bỏ qua" nếu user không thực sự muốn cài đặt PWA
3. Kết hợp nhiều cách làm bên dưới cũng không sao, miễn đừng gây khó chịu cho user vì yêu cầu quá nhiều lần và không đúng chỗ
4. Chỉ bật "Gợi ý cài đặt" khi kiểm tra đã có `beforeinstallprompt`

## Dùng tính năng tự động của trình duyệt

![Example of the Add to Home screen mini-infobar for AirHorner](https://developers.google.com/web/updates/images/2018/06/a2hs-infobar-cropped.png)

Trình duyệt sẽ tự động thông báo cho user có muốn cài đặt PWA không, nếu ứng dụng web của bạn đã [đáp ứng đủ yêu cầu](https://developers.google.com/web/fundamentals/app-install-banners/) trên Android

## Những cách làm phổ biến

### Trong fixed header

![](https://developers.google.com/web/fundamentals/app-install-banners/images/install-promo/header.png)

Nếu được đây là chỗ tốt nhất để hiển thị nút "Gợi ý cài đặt". Từng pixel trên header điều giá trị khi hiển thị trong mobile, do đó hiển thị nút này sao cho vừa vặn đừng tham lam chiếm hết chỗ cho những thành phần khác.

### Trong thanh navigation

![](https://developers.google.com/web/fundamentals/app-install-banners/images/install-promo/nav.png)

Navigation chính cũng là lựa chọn không tồi khi bất kỳ user nào cũng sẽ click vào menu.

Ưu tiên những thành phần khác của navigation trước, đặt nút "Gợi ý cài đặt" ở dưới cùng, viết thêm vài câu ngắn gọn để user biết được lợi ích của việc cài đặt PWA

### Trang landing

![](https://developers.google.com/web/fundamentals/app-install-banners/images/install-promo/landing.png)

Mục tiêu của trang landing là giới thiệu những dịch vụ, sản phẩm mà bạn đang cung cấp, một vị trí tốt để lôi kéo user cài PWA

Trước tiên, giải thích cho user hiểu lợi ích user có được khi cài PWA, tập trung nhấn mạnh vào những điểm mà khách hàng của bạn được lợi nhiều nhất.

### Banner

![](https://developers.google.com/web/fundamentals/app-install-banners/images/install-promo/banner.png)

Khi chọn hiển thị dạng banner ở trên cùng của trang, nhớ thêm tính năng để user **đóng và bỏ qua** gợi ý cài đặt này.

Đợi đến khi user cảm thấy có chút hứng thú với ứng dụng của bạn mới hiển thị banner ra, đa phần khi vừa vào đã hiển thị banner user sẽ xem đó là quảng cáo và bỏ qua ngay không cần đọc đó là gì.

### Bên trong danh sách bài viết
![](https://developers.google.com/web/fundamentals/app-install-banners/images/install-promo/in-feed.png)

Những trang mà user hay *lướt* để đọc nội dung như danh sách bài viết. Không nên lạm dụng và hiển thị với tần suất quá nhiều lần sẽ khiến user bực bội. Cho phép user bỏ qua vĩnh viễn những gợi ý nằm ở đây

### Trang checkout

![](https://developers.google.com/web/fundamentals/app-install-banners/images/install-promo/journey.png)

Bên dưới phần checkout, là một cơ hội để hiển thị nút Gợi ý cài đặt. Cung cấp thông tin tại sao user phải cài đặt, nó liên quan gì tới việc user đang làm. Không bắt buộc user phải đồng ý cài đặt để đi tiếp. Luồng cài đặt nên tách riêng không ảnh hướng luồng checkout

[https://developers.google.com/web/fundamentals/app-install-banners/promoting-install-mobile](https://developers.google.com/web/fundamentals/app-install-banners/promoting-install-mobile)
