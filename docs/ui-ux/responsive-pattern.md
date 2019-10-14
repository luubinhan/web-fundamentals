Sau hơn 10 năm ra đời, cách làm css responsive tương đối đã định hình, chúng ta cùng điểm qua những cách làm được áp dụng rộng rãi hiện nay

## Fluid Layout

Là phương pháp Mobile-First, đi từ màn hình nhỏ lên màn hình lớn.

Trong cách Fluid layout, chúng ta sử dụng hệ thống grid theo kích thước %, với cách làm này chúng ta chỉ cần một vài breakpoint là có thể đáp ứng được hầu hết kích thước màn hình.

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/imgs/mostly-fluid.svg)

Ở trạng thái nhỏ nhất, tất cả `<div/>` được xếp chồng lên nhau thành 1 cột và sẽ được gom lại thành từng hàng ở kích thước lớn hơn

```css
.container {
  display: -webkit-flex;
  display: flex;
  -webkit-flex-flow: row wrap;
  flex-flow: row wrap;
}
.c1, .c2, .c3, .c4, .c5 {
  width: 100%;
}
@media (min-width: 600px) {
  .c2, .c3, .c4, .c5 {
    width: 50%;
  }
}
@media (min-width: 800px) {
  .c1 {
    width: 60%;
  }
  .c2 {
    width: 40%;
  }
  .c3, .c4 {
    width: 33%;
  }
  .c5 {
    width: 34%;
  }
}
@media (min-width: 800px) {
  .container {
    width: 800px;
    margin-left: auto;
    margin-right: auto;
  }
}
```

## Column drop

Là phương pháp tiếp cận từ màn hình lớn xuống mobile, desktop first. Kết quả cuối cùng vẫn là các `<div/>` nằm xếp chồng lên nhau. Lựa chọn breakpoint sẽ quyết định phụ thuộc vào nội dung và từng thiết kế.

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/imgs/column-drop.svg)

```css
.container {
  display: -webkit-flex;
  display: flex;
  -webkit-flex-flow: row wrap;
  flex-flow: row wrap;
}
.c1, .c2, .c3 {
  width: 100%;
}
@media (min-width: 600px) {
  .c1 {
    width: 60%;
    -webkit-order: 2;
    order: 2;
  }.c2 {
    width: 40%;
    -webkit-order: 1;
    order: 1;
  }.c3 {
    width: 100%;
    -webkit-order: 3;
    order: 3;
  }
}
@media (min-width: 800px) {
  .c2 {
    width: 20%;
  }.c3 {
    width: 20%;
  }
}
```

## Layout shifter

Đây là cách làm **chịu khó** nhất, responsive nhất, trên các breakpoint khác nhau, layout sẽ thay đổi theo từng yêu cầu cụ thể, không chỉ đơn thuần là thay đổi dòng hay cột .

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/imgs/layout-shifter.svg)

```css
.container {
  display: -webkit-flex;
  display: flex;
  -webkit-flex-flow: row wrap;
  flex-flow: row wrap;
}
.c1, .c2, .c3, .c4 {
  width: 100%;
}
@media (min-width: 600px) {
  .c1 {
    width: 25%;
  }.c4 {
    width: 75%;
  }
}
@media (min-width: 800px) {
  .container {
    width: 800px;
    margin-left: auto;
    margin-right: auto;
  }
}
```

## Tinh chỉnh

Trong đa số các trường hợp của responsive, chúng ta cần thay đổi font size, kích thước hình, `padding`, ...

```css
.c1 {
  padding: 10px;
  width: 100%;
}
@media (min-width: 500px) {
  .c1 {
    padding: 20px;
    font-size: 1.5em;
  }
}
@media (min-width: 800px) {
  .c1 {
    padding: 40px;
    font-size: 2em;
  }
}
```

## Ẩn element

Thay vì rớt dòng, chúng ta ẩn luôn khi màn hình không đủ kích thước

![](https://developers.google.com/web/fundamentals/design-and-ux/responsive/imgs/off-canvas.svg)

Chúng ta có thể ẩn element bằng sự kết hợp của `overflow: hidden` và `translate(giá trị âm)`

[Responsive Web Design Patterns](https://developers.google.com/web/fundamentals/design-and-ux/responsive/patterns)
