## Giải thích Same-Site cookie

Cookie là một trong những phương pháp để có được persistent trên web

Mỗi cookie là một cặp `key-value`, và một số attribute khác, như thời gian expire, xác định cookie chỉ được gửi qua HTTPS. Server set cookie bằng cách trả về response với header `Set-Cookie`

Ví dụ chúng ta muốn xuất hiện một thông báo quảng cáo lên user, user có thể tắt quảng cáo này và nó sẽ không xuất hiện nữa. Chúng ta có thể lưu trữ nó trong cookie, thời gian expire là 1 tháng (2,600,000 giây), chỉ được gửi qua HTTPS, response Header sẽ như sau

```bash
Set-Cookie: promo_shown=1; Max-Age=2600000; Secure
```

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/jJ1fqcsAk9Ig3hManFBO.png?auto=format&w=964)

Browser sẽ send lại request header

```bash
Cookie: promo_shown=1
```

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/Rq21WQpOZFvfgS9bbjmc.png?auto=format&w=964)

Để đọc và thêm cookie, chúng ta có thể dùng JS `document.cookie`

```js
// tạo
> document.cookie = "promo_shown=1; Max-Age=2600000; Secure"
< "promo_shown=1; Max-Age=2600000; Secure"

// đọc
> document.cookie;
< "promo_shown=1; color_theme=peachpuff; sidebar_loc=left"
```

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/mbV00Gy5VAPTUls0i7cM.png?auto=format&w=741)

## first-party, third-party cookie

Khi truy cập vào một tên miền, không chỉ cookie của tên miền đang truy cập (gọi là first-cookie), mà chúng ta còn có thể có các cookie từ những tên miền khác (third-party cookie). Vì trên một trang chúng ta có thể truy xuất đến nhiều hơn một domain (qua iframe, hoặc hình ảnh được load từ một domain khác)

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/zjXpDz2jAdXMT83Nm3IT.png?auto=format&w=964)

Nó dẫn đến vấn đề **cross-site request forgery** (CSRF). Ví dụ chúng ta truy cập `evil.example`, nó trigger request đến `your-blog.example` trình duyệt sẽ vẫn gửi đi cookie đình kèm. Nếu không được xử lý tốt, hacker có thể lợi dụng để thực hiện những tao thác trên `your-blog.example`

Để xử lý vấn đề này, attribute `SameSite` cho phép khai báo cookie chỉ được giới hạn cho first-party và same-site

> Đọc lại bài Phân biệt [Same-site và same-origin](https://vuilaptrinh.com/2021-08-22-phan-biet-same-site-same-origin/)

```bash
Set-Cookie: promo_shown=1; SameSite=Strict
```

**Strict** chỉ cho phép cookie gửi cho first-party (cùng url), ví dụ nếu bạn mở một email, nội dung có request đến `your-blog.example`, cookie của `your-blog.example` sẽ không được gửi đi

`SameSite=Lax`, cho phép gửi cookie khi ở top-level, ví dụ nếu có một trang tin tức hiển thị hình ảnh trực tiếp từ `your-blog.example`

```html
<p>Look at this amazing cat!</p>
<img src="https://your-blog.example/blog/img/amazing-cat.png" />
<p>Read the <a href="https://your-blog.example/blog/cat.html">article</a>.</p>
```

Cookie đang set

```bash
Set-Cookie: promo_shown=1; SameSite=Lax
```

Cookie sẽ không được gửi đi khi request `<img />`, tuy nhiên nếu click vào đường link `<a/>` như trong ví dụ, cookie sẽ được gửi đi

> Không nên dùng `SameSite=None`

Trình duyệt Chrome thì `SameSite=Lax` là giá trị mặc định, với FireFox có thể thay đổi bằng cách vào `about:config` tìm `network.cookie.sameSite.laxByDefault`

Nếu sử dụng `SameSite=None`, thêm `Secure`

```bash
Set-Cookie: widget_session=abc123; SameSite=None; Secure
```

## Khác Scheme

Scheme tức là HTTP hay HTTPS, nếu chuyển từ `http://site.example` sang `https://site.example`

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/yDViqKg9eeEeAEiCNqe4.png?auto=format&w=845)

|                      | HTTP -> HTTPS | HTTPS -> HTTP |
| -------------------- | ------------- | ------------- |
| SameSite=Strict      | ⛔             | ⛔             |
| SameSite=Lax         | ✓             | ✓             |
| SameSite=None;Secure | ✓             | ⛔             |

Nếu không *navigate* mà load ở dạng subresource như image, iframe, network request dùng XHR hay Fetch

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/GgR6Yln1f9JGkt04exRC.png?auto=format&w=845)

|                      | HTTP -> HTTPS | HTTPS -> HTTP |
| -------------------- | ------------- | ------------- |
| SameSite=Strict      | ⛔             | ⛔             |
| SameSite=Lax         | ⛔             | ⛔             |
| SameSite=None;Secure | ✓             | ⛔             |

Nếu thực hiện form submit

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/ud9LkDeGJUWHObifD718.png?auto=format&w=845)

|                      | HTTP -> HTTPS | HTTPS -> HTTP |
| -------------------- | ------------- | ------------- |
| SameSite=Strict      | ⛔             | ⛔             |
| SameSite=Lax         | ⛔             | ⛔             |
| SameSite=None;Secure | ✓             | ⛔             |



Tham khảo

- https://web.dev/schemeful-samesite/
- https://web.dev/samesite-cookies-explained/
- https://web.dev/samesite-cookie-recipes/
