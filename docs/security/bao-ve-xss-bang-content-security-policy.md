Content Security Policy (CSP) là một cơ chế bảo mật để bảo vệ khỏi XSS, Thiết đặt thông qua HTTP header

> nonce là một con số ngẫu nhiên, chỉ dùng một lần, sử dụng để đánh dấu thẻ `<script/>` hợp lệ

> hash function là một hàm toán học, chuyển một giá trị thành một giá trị ở dạng hash. Giá trị hash này được dùng để đánh dấu thẻ script inline hợp lệ

## Strict Content Security Policy là gì?

Là một thiết đặt trên HTTP response header, khai báo bằng một trong những cách sau

### Dùng Nonce

```bash
Content-Security-Policy:
  script-src 'nonce-{RANDOM}' 'strict-dynamic';
  object-src 'none';
  base-uri 'none';
```

![](https://web-dev.imgix.net/image/vgdbNJBYHma2o62ZqYmcnkq3j0o1/er4BaGCJzBwDaESFKfZd.jpg?auto=format&w=964)

### Dùng Hash

```bash
Content-Security-Policy:
  script-src 'sha256-{HASHED_INLINE_SCRIPT}' 'strict-dynamic';
  object-src 'none';
  base-uri 'none';
```

*Lưu ý đây chỉ là phiên bản rút gọn, cần tùy chỉnh để phù hợp với nhiều trình duyệt*

Khai báo trên có nghĩa là

- Dùng nonce `nonce-{RANDOM}` hoặc hash `sha256-{HASHED_INLINE_SCRIPT}` để xác định một tag `<script />` có an toàn và cho phép thực thi hay không
- ['strict-dynamic'](https://www.w3.org/TR/CSP3/#strict-dynamic-usage) để cho phép thực thi script từ những nguồn đã biết, ví dụ như các third-party đã biết
- Khóa inline script
- Chặn `object-src`, thường gặp trên các plugin Flash
- Chặn `base-uri`, khóa inject vào thẻ `<base />`, hacker có thể sử dụng để thay đổi đường dẫn của script bằng cách dùng `<base />`

Có thể dùng [Lighthouse](https://developers.google.com/web/tools/lighthouse) để kiểm tra xem trang có đang khai báo CSP không

![Lighthouse report warning that no CSP is found in enforcement mode.](https://web-dev.imgix.net/image/9B7J9oWjgsWbuE84mmxDaY37Wpw2/42a4iEEKsD4T3yU47vNQ.png?auto=format)

Các bước cụ thể xem thêm tại [Strict CSP](https://web.dev/strict-csp/#adopting-a-strict-csp)

## Hạn chế

- Nếu đang sử dụng `nonce`, nhưng bị inject vào trong nội dung hoặc trong `src` của thẻ `<script />` thì không còn được bảo vệ
- Nếu bị hack bằng cách tạo script động `document.createElement('script')`, cách này không bảo vệ được

Tham khảo thêm Trusted Type
