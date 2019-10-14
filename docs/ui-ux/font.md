# Font chữ trên web

## Giới thiệu

Trong cộng đồng developer, chữ font và typeface thường được sử dụng để thay thế cho nhau. Nhưng trong design, nó là 2 phạm trù riêng biệt. Typeface là tập các font chữ thuộc cùng một **gia tộc**, như Roboto typeface. Trong khi đó font là một file duy nhất, của **gia tộc**, như Roboto Bold, Roboto Italic.

![Font chữ trên web](https://developers.google.com/web/fundamentals/design-and-ux/typography/variable-fonts/images/robot-specimen.png)

![Font chữ trên web](https://developers.google.com/web/fundamentals/design-and-ux/typography/variable-fonts/images/roboto-family.png)

## Thách thức

Khi designer sử dụng một typeface, họ không nghĩ đến việc typeface đó có hoạt động tốt trên mọi môi trường không, hoặc có bản quyền để nhúng trên web.

Với web developer, chúng ta phải xem xét đến cả 2 trường hợp, đồng thời là tài nguyên tiêu tốn cho việc load font đó. Nếu sử dụng nhiều tất cả những font trong một bộ typeface, nghĩa là user phải download toàn bộ tất cả những font này xuống mãi để có thể hiển thị đúng như thiết kế. Ví dụ như để có cả Regular, Bold, Italic cho typeface Roboto, tốn khoản 500k dữ liệu. Đây là điểm gây tranh cãi nhiều năm qua của developer và designer, đẹp hơn nhưng cũng tiêu tốn dung lượng để tải về của user hơn, đó là chưa kể đến việc render sẽ bị hoãn lại khi chưa load font xong ([FOIT và FOUT](https://www.zachleat.com/web/fout-vs-foit/)).

```css
@font-face {
  font-family: 'AmstelvarAlpha';
  src: url('../fonts/AmstelvarAlpha-VF.ttf');
}

#font-amstelvar {
  font-family: 'AmstelvarAlpha';
}
```

![Font chữ trên web](https://developers.google.com/web/fundamentals/design-and-ux/typography/variable-fonts/images/gingham-weight-e.gif)

## Cải thiện hiệu năng với font

Kiểu font OpenType cho phép lưu nhiều dạng khác nhau của typeface vào một file duy nhất. [Monotype](https://goo.gl/9gonHT) chạy một thử nghiệm bằng cách dồn 12 font của Roman vào trong 1 file duy nhất, kết quả thu được dung lượng giảm **88%** so với ban đầu.

Khi thực hiện animation trên các dạng font chữ, trình duyệt cũng tiêu tốn hiệu năng để paint và render lại, [xem thêm video này](https://www.youtube.com/watch?v=B42rUMdcB7c)

Với những typeface khác nhau, designer có thể đem đến những trải nghiệm nhất định cho thương hiệu hay website, nếu không kể đến việc tiêu tốn dung lượng của trang phải tải về. Việc lựa chọn tốc độ hay đẹp để mang đến trải nghiệm nào cho người dùng, bạn phải tự lựa chọn.
