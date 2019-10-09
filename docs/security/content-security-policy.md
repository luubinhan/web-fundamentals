Mô hình bảo mật của web luôn xoay quanh quy định **same-origin**, *đèn nhà ai nhà đó sáng*. Code từ trang `vuilaptrinh.com` không được phép lấy gì từ `google.com` nếu ko được cấp giấy xét nhà

Tấn công theo cách **Cross-site scripting** (XSS) để qua mặt câu hỏi **nhà anh ở đâu thế**, dùng một tên *nội gián* (một đoạn script) gài vào nhà đó, rồi tuồn thông tin nội bộ từ nhà này ra *thế giới*, một khi nội gián đã nằm trong nhà bạn, thì xem như game over.

Để giảm thiểu tối đa nguy cơ bị dính *nội gián* này, các trình duyệt *hiện đại* sử dụng một phương pháp khác **same-origin** gọi là Content Security Policy (**CSP**)

## Whitelist

Ví dụ nút like của Google, khi user click vào nút này code đặt ở nhà bạn, nhưng nó sẽ chạy đưa thông tin tới  `api.google.com`, câu hỏi là làm sao chúng ta có thể cho phép những đoạn code gửi thông tin tới `api.google.com` mà không cho tới trang `evil.com`

Chúng ta cấp *giấy chứng nhận an toàn vệ sinh thực phẩm* **CSP** (trong HTTP Header), để báo với browser là: "Ê, mấy trang này là tao tin tưởng, mày có thể cho code chạy thoải mái"

```
Content-Security-Policy: script-src 'self' https://apis.google.com
```

Đoạn trên có nghĩa là chúng ta tin tưởng và cho phép thực thi những đoạn script từ nhà chúng ta, và từ nhà google

`script-src` được gọi là **directive**, có thể hình dung nó như những biển báo giao thông ngoài đường, đây là đường một chiều, chỉ cho phép xe máy, cấm xe ba gác.

Nếu *kẻ xấu* muốn load và thực thi một đoạn script ở một nhà không ai quen biết, nó sẽ bị chặn lại ngay lập tức bởi trình duyệt.

![Không cho chạy code ở trang không nằm trong whitelist](https://developers.google.com/web/fundamentals/security/csp/images/csp-error.png)

Danh sách **directive** khá nhiều, đang được khai báo thêm, chi tiết có thể xem tại [https://www.w3.org/TR/CSP3/](https://www.w3.org/TR/CSP3/)


**Sử dụng thẻ meta để thiết đặt CSP**

CSP được khuyến khích cung cấp thông qua HTTP header, hoặc có thể thông qua thẻ `<meta/>` với thuộc tính `http-equiv`

```html
<meta
http-equiv="Content-Security-Policy"
content="default-src https://cdn.example.net; child-src 'none'; object-src 'none'"
>
```

Không được phép dùng thẻ meta với các thuộc tính `frame-ancestors`, `report-uri`, `sandbox`

## Inline code vô cùng nguy hiểm

CSP sử dụng một các khai báo trong whitelist để thông báo với trình duyệt *đâu là bạn, đâu là thù*, trong khi cơ chế **same-origin** ko giải quyết được con nội gián **XSS**. Ví dụ bên trong source code trả về có đoạn code `<script>sendMyDataToEvilDotCom();</script>` trình duyệt không có cơ chế để phân biệt được bạn-thù ở đây.

Với CSP nó mặc định cấm hết các script inline như vậy. Ko chỉ những đoạn inline code bên trong thẻ `script`, bạn cũng phải bỏ hết những đoạn code `onclick`

```js
<script>
  function doAmazingThings() {
    alert('YOU AM AMAZING!');
  }
</script>
<button onclick='doAmazingThings();'>Am I amazing?</button>
```

Thay thế nó bằng

```html
<!-- amazing.html -->
<script src='amazing.js'></script>
<button id='amazing'>Am I amazing?</button>
```
```js
// amazing.js
function doAmazingThings() {
  alert('YOU AM AMAZING!');
}
document.addEventListener('DOMContentLoaded', function () {
  document.getElementById('amazing')
    .addEventListener('click', doAmazingThings);
});
```

Đoạn code bên dưới sẽ được **CSP** cấp giấy chứng nhận hành nghề, có thể hoạt động kinh doanh bình thường.

Sau khi bị càm ràm là có nhiều trường hợp buộc dùng inline code, **CSP** phiên bản 2 được đưa ra với bổ sung khả năng cho chạy inline code, nhưng phải thêm 2 bước

1. Khai báo id cho đoạn code muốn chạy inline

```html
<script nonce="EDNnf03nceIOfn39fn3e9h3sdfa">
  // Some inline code I can't remove yet, but need to asap.
</script>
```

2. Bổ sung id đó vào whitelist

```
Content-Security-Policy: script-src 'nonce-EDNnf03nceIOfn39fn3e9h3sdfa'
```

## Eval

Ngay cả khi không thể inject script trực tiếp, bọn *ác ôn* còn có thể dụ dỗ ứng dụng của chúng ta convert một đoạn text (bị nhúng) thành javascript rồi chạy nó bằng `eval()`, `new Function()`, `setTimeout([string],...)` và `setInterval([string],...)`. CSP cũng block toàn bộ những dạng này.

Nó sẽ ảnh hướng cách chúng ta xây dựng ứng dụng:

- Trong ta phải parse JSON thông qua hàm `JSON.parse` và không dùng `eval()`
- Viết lại những hàm `setTimeout` hoặc `setInterval` bạn đang viết inline. Ví dụ

*viết thế này méo cho chạy đâu*

```js
setTimeout("document.querySelector('a').style.display = 'none';", 10);
```

viết bình thường thôi

```js
setTimeout(function () {
  document.querySelector('a').style.display = 'none';
}, 10);
```

## Tố cáo

Ngoài việc chặn không cho thực thi những hành vi nguy hiểm ở client, nếu phát hiện chúng ta hãy *tố cáo* nó lên server để xác định những thành viên của tổ chức áo đen này và cho luôn vào blacklist

Config CSP như thế này để nó tố cáo

```
Content-Security-Policy: default-src 'self'; ...; report-uri /my_amazing_csp_report_parser;
```

Kết quả report nó sẽ dạng như thế này

```json
{
  "csp-report": {
    "document-uri": "http://example.org/page.html",
    "referrer": "http://evil.example.com/",
    "blocked-uri": "http://evil.example.com/evil.js",
    "violated-directive": "script-src 'self' https://apis.google.com",
    "original-policy": "script-src 'self' https://apis.google.com; report-uri http://example.org/my_amazing_csp_report_parser"
  }
}
```

Chứng chỉ hành nghề **CSP** 1 được sử dụng rộng rãi trong Chrome, Safari, Firefox, chưa hỗ trợ đầy đủ trong IE10. Những ông lớn hiện nay như Twitter và Facebook có đã áp dụng vào thực tiễn, đọc thêm [Case study của Twitter](https://blog.twitter.com/engineering/en_us/a/2011/improving-browser-security-with-csp.html)


[https://developers.google.com/web/fundamentals/security/csp](https://developers.google.com/web/fundamentals/security/csp)
