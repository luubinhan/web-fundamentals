## Mixed content là gì

**Mixed content** xảy ra khi HTML và được truyền qua HTTPs, mà có những thằng khác như video, stylesheet, image lại truyền qua HTTP. Tức là nội dung gửi tới user là sự **kết hợp** giữa anh giao hàng bằng xe tải và anh giao hàng bằng xe máy.

Khi đó trình duyệt sẽ báo với user, quá trình chuyển *hàng* của trang này không an toàn.

Giao hàng bằng xe tải, hàng hóa đảm bảo khó hư hao hơn (ít nhất là vậy), trong khi bạn tưởng tượng, giao cái tủ mà một phần cái tủ được giao bằng xe tải, phần kính chở xe máy tới, thì giao hàng bằng xe tải cũng chẳng còn ý nghĩa gì nữa.

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/Y7b4EWAbSL6BgI07FdQq.jpg?auto=format&w=964)

![Chrome block các request ở dạng mixed content](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/KafrfEz1adCP2eUHQEWy.jpg?auto=format&w=964)

Việc *chuyển hàng* bằng phương pháp nữa nạt-nữa mở này, trình duyệt như Chrome tuyệt đối nghiêm cấm. Tuy nhiên do có quá nhiều website hiện tại dính vào tình trạng này, nên Chrome có nhân nhượng ở thời điểm hiện tại, chỉ chặn một số loại mà đã xác định được là nguy hiểm.

Điều chúng ta cần nhớ là mỗi trình duyệt lại *hành xử* với mixed content khác nhau, trong trường hợp tệ nhất, trình duyệt không block gì hết, thông tin user gửi đi ở mức *dễ tổn thương*

## Ngăn chặn Mixed content như thế nào

Để biết trang web của mình có bị mixed content không, đơn giản là mở trang web bằng trình duyệt Chrome, mở cửa sổ DevTools lên xem có thông báo nào ở console không

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/HNxoomaHi2ksvYHGuNiE.jpg?auto=format&w=964)

![Chrome block các request ở dạng mixed content](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/xRG5zpKLr0Z3OwfYpn2H.jpg?auto=format&w=964)

Một cách để tìm trong source code, search toàn bộ source nào có đoạn `http://` không.

Có thể chặn mixed content bằng thiết đặt **CSP**

```
Content-Security-Policy-Report-Only: default-src https: 'unsafe-inline' 'unsafe-eval'; report-uri https://example.com/reportingEndpoint
```

Block toàn bộ

```
Content-Security-Policy: block-all-mixed-content
```

[https://developers.google.com/web/fundamentals/security/prevent-mixed-content/what-is-mixed-content](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/what-is-mixed-content)

[https://developers.google.com/web/fundamentals/security/prevent-mixed-content/fixing-mixed-content](https://developers.google.com/web/fundamentals/security/prevent-mixed-content/fixing-mixed-content)
