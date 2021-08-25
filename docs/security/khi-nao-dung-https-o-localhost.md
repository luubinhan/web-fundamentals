Sử dụng http://localhost ở khi develop hoàn toàn không có vấn đề, trừ một số trường hợp đặc biệt sau, chúng ta sẽ cần dùng **HTTPS**

- Thiết đặt cookie `Secure`, thống nhất trên mọi trình duyệt
- Debug mixed-content
- Sử dụng HTTP/2 và các phiên bản mới
- Sử dụng thư viện third-party hoặc APIs bắt buộc HTTPS
- Sử dụng hostname tùy biến

![](https://web-dev.imgix.net/image/admin/ifswaep3VUkY7cjArbIc.png?auto=format&w=1252)

## Làm sao sử dụng HTTPS

> mkcert

Để sử dụng HTTPS như `https://localhost` hoặc `https://mysite.example` chúng ta cần **TLS certificate**, trình duyệt không chấp nhận mọi certificate, mà phải có **sign** (chữ ký kỹ thuật số) hẳn hoi, được gọi là *certificate authority (**CA**)

[mkcert](https://github.com/FiloSottile/mkcert) là công cụ để giải quyết vấn đề trên

![](https://web-dev.imgix.net/image/admin/3kdjci7NORnOw54fMia9.jpg?auto=format&w=845)

Lưu ý

- Không bao giờ export hay share file `rootCA-key.pem` được tạo bởi mkcert khi chạy lệnh `mkcert -install`. Hacker có thể lợi dụng để tấn công vào bất kỳ trang nào bạn truy cập
- Chỉ sử dụng mkcert cho mục đích develop
- Tất cả thành viên tự tạo CA riêng, không chia sẻ và dùng chung

### Cài đặt

1. Trên window dùng [[Chocolatey](https://chocolatey.org/)](https://chocolatey.org/)

   ```bash
   choco install mkcert
   ```

2. Thêm vào local root CA

   ```bash
   mkcert -install
   ```

3. Tạo certificate, sign bằng mkcert, mở thư mục gốc của site

   ```bash
   mkcert localhost
   ```

   Nếu muốn sử dụng tên miền `mysite.example`

   ```bash
   mkcert mysite.example
   ```

4. Cấu hình server để sử dụng certificate vừa tạo, ví dụ với node

   ```js
   const https = require('https');
   const fs = require('fs');
   const options = {
     key: fs.readFileSync('{PATH/TO/CERTIFICATE-KEY-FILENAME}.pem'),
     cert: fs.readFileSync('{PATH/TO/CERTIFICATE-FILENAME}.pem'),
   };
   https
     .createServer(options, function (req, res) {
       // server code
     })
     .listen({PORT});
   ```

   với http-server

   ```bash
   http-server -S -C {PATH/TO/CERTIFICATE-FILENAME}.pem -K {PATH/TO/CERTIFICATE-KEY-FILENAME}.pem
   ```

   với React, file `package.json`

   ```json
   "scripts": {
   "start": "HTTPS=true SSL_CRT_FILE={PATH/TO/CERTIFICATE-FILENAME}.pem SSL_KEY_FILE={PATH/TO/CERTIFICATE-KEY-FILENAME}.pem react-scripts start"
   ```

   Ví dụ bạn lưu file CA trong thư mục

   ```bash
   |-- my-react-app
       |-- package.json
       |-- localhost.pem
       |-- localhost-key.pem
       |--...
   ```

   Script sẽ update như sau

   ```json
   "scripts": {
       "start": "HTTPS=true SSL_CRT_FILE=localhost.pem SSL_KEY_FILE=localhost-key.pem react-scripts start"
   ```

   