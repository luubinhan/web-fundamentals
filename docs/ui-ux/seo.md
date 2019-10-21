# Tối ưu cho công cụ tìm kiếm

Website không chỉ được *ghé thăm* bởi người thật (user), lâu lâu còn được ghé thăm bởi mấy con crawler của công cụ tìm kiếm. Làm sao để các công cụ tìm kiếm đánh giá chính xác nội dung của trang web là điều ai làm web cũng cần biết

## Khai báo cấu trúc Website

### URL

Có nhiều cách để đưa dữ liệu đến người dùng trên các thiết bị khác nhau. Các phương pháp phổ biến nhất là:

1. Responsive web design: sử dụng cùng một HTML, một URL và thẻ media của CSS để tối ưu hiển thị trên nhiều thiết bị khác nhau. Trên Desktop và Mobile đều là `http://example.com`
2. Tách mobile site: đưa user về trang dành riêng cho mobile, tùy thuộc vào user đang sử dụng thiết bị gì. Desktop là `http://example.com`, mobile là `http://m.example.com`
3. Tùy vào thiết bị, server sẽ kiểm tra và trả về một HTML riêng cho từng loại thiết bị. Desktop và mobile dùng chung `http://example.com`

Cách làm được ưa chuộng nhất hiện nay là responsive web design


### Sử dụng `link[rel=canonical]` và `link[rel=alternate]` khi chọn kiểu tách riêng URL

Cung cấp cùng một nội dung trên desktop và mobile nhưng trên hai URL khác nhau dễ gây bối rối cho user và trình duyệt.

Khi 2 nội dung là hoàn toàn là giống nhau, chỉ khác nhau URL, chúng ta cần khai báo công cụ tìm kiếm việc đó

Trên desktop, thêm thẻ `<link rel="alternate" />` để báo có một phiên bản giống hệt như vậy trên mobile

```html
<link rel="alternate" media="only screen and (max-width: 640px)" href="http://m.example.com/">
```

Trên mobile, chúng ta khai báo có một bản desktop như vậy nữa bằng `<link rel="canonical" />`

```html
<link rel="canonical" href="http://www.example.com/" media="only screen and (max-width: 640px)">
```

![](https://developers.google.com/web/fundamentals/discovery/search-optimization/imgs/different_url.png)


## Crawl và index của search engine

Không phải lúc nào những gì đưa lên trên web cũng để cho công cụ tìm kiếm đọc được, như trang admin, trang cần quyền truy cập mới xem được, cần khai báo với công cụ tìm kiếm những trang không được vào xem như vậy

**Sự khác nhau của crawl và index**

Trước tiên, chúng ta cần làm rõ 2 khái niệm này trước khi vào phần thiết đặt sao cho công cụ tìm kiếm không được phép vào xem những trang private.

- Crawl là khi search engine dùng các con bot fetch dữ liệu và thực hiện đánh giá site của bạn. Nội dung này được search engine lưu lại trong database của nó đánh giá thứ hạng của site
- Index là quá trình search engine lưu 1 URL của website và các thông tin liên quan đến URL đó xuống database, thông tin sẽ hiển thị lên kết quả tìm kiếm

### Thiết đặt crawl bằng robots.txt

Robots.txt sẽ đặt ở thư mục gốc của website. Ví dụ, `http://example.com/robots.txt`

```bash
User-agent: *
Disallow: /
```

Câu trên là để khai báo, không cho search engine crawl bất cứ thông tin gì từ trang

```bash
User-agent: Googlebot
Disallow: /nogooglebot/
```

Câu trên, không cho phép con bot của google crawl thông tin trong `nogooglebot`

Bạn có thể dùng [Công cụ web master tool](https://www.google.com/webmasters/tools/robots-testing-tool) để kiểm tra file robots.txt của một site bất kỳ


![](https://developers.google.com/web/fundamentals/discovery/search-optimization/imgs/robots-txt-validator.png)


### Thiết đặt index bằng thẻ meta

Nếu bạn không muốn trang xuất hiện trên kết quả tìm kiếm, `robots.txt` không phải là giải pháp. Chúng ta dùng `noindex`

```html
<meta name="robots" content="noindex" />
```

Chỉ định riêng cho từng công cụ tìm kiếm

```html
<meta name="googlebot" content="noindex" />
```

Nếu bạn không muốn những resource như hình, stylesheet, script không được phép index, chúng ta cấu hình `X-Robots-Tag: noindex`

```bash
HTTP/1.1 200 OK
X-Robots-Tag: noindex
Content-Type: text/html; charset=UTF-8
```

## Một vài ví dụ sử dụng

**Cho phép mọi quyền index và crawl**

- Không có file robots.txt
- Không có thẻ `<meta name="robots" />`


**Chỉ những người biết URL mới được phép truy cập (tạm dấu url)**

Bạn không muốn search engine *mò* ra các trang này

- Không cần file robots.txt
- Dùng `<meta name="robots" content="noindex" />`
- Dùng X-Robots-Tag cho các file không phải là HTML

**Các trang chặn tất cả user không quyền vào truy cập**

Với các trang này, search engine không được phép crawl và index gì hết. Trả 401 khi có yêu cầu các trang này.

Cơ chế *authentication/authorization* tùy thuộc bạn đang dùng cái gì, nên không đề cập cụ thể ở đây.

Nếu muốn xóa một URL đã được search engine index, chúng ta lên công cụ của từng engine, yêu cầu nó xóa URL đó đi

- https://www.google.com/webmasters/tools/url-removal
- https://www.bing.com/toolbox/webmaster/
- https://webmaster.yandex.com/site/tools/del-url/



https://developers.google.com/web/fundamentals/discovery/search-optimization
