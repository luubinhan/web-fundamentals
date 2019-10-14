## Thứ tự của DOM

Làm việc với các element cơ bản là cách tốt nhất để biết cách hoạt động của focus vì chúng tự động được chèn tab order theo thứ tự của DOM

Ví dụ, bạn muốn có 3 button, ấn `Tab` các button sẽ được tuần tự focus theo thứ tự của DOM

```html
<button>I Should</button>
<button>Be Focused</button>
<button>Last!</button>
```

Điều quan trọng nên nhớ là, khi sử dụng CSS, chúng ta có thể thay đổi thứ tự được nhìn thấy của các button này, nghĩa là thứ tự DOM sẽ khác với thứ tự mà user nhìn thấy. Tab sẽ vẫn chạy theo thứ tự DOM. Nếu user ấn `Tab` lúc này trạng thái focus sẽ đi theo không đúng như user mong muốn

```html
<button style="float: right">I Should</button>
<button>Be Focused</button>
<button>Last!</button>
```

Hết sức lưu ý khi bạn dùng CSS để thay đổi thứ tự hiển thị này, một khi nó không khớp với DOM, luôn kiểm tra Tab xem trạng thái focus có đi đúng như user mong muốn

## Nội dung ẩn

Khi bạn có một element không hiển thị trên giao diện, nhưng vẫn có mặt trong DOM, ví dụ như thanh navigation responsive, lúc user `Tab`, trạng thái focus sẽ có lúc không được nhìn thấy, vì nó đang rơi vào các element bị ẩn khỏi màn hình.

Nguyên tắc, khi các element đã không xuất hiện trên màn hình, chúng ta sẽ không cho phép trạng thái focus rơi vào các element này.

![Thứ tự của DOM](https://developers.google.com/web/fundamentals/accessibility/focus/imgs/slide-in-panel.png)

> Dùng `document.activeElement` để biết element nào đang Focus

Các element muốn ẩn đi, luôn dùng thuộc tính CSS `display: none` hoặc `visibility: hidden` để trình duyệt từ động bỏ qua focus trên element này, nếu chỉ dùng thuộc tính `translate` với giá trị âm, nó vẫn được focus khi user ấn `Tab`

![](https://developers.google.com/web/fundamentals/accessibility/focus/imgs/slide-in-panel2.png)
