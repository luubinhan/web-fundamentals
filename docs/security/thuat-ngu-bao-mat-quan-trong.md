## Cặp public/private key

Cặp **public/private key** là 2 đoạn string được dùng để mã hóa và giải mã, sử dụng chung một công thức toán học đặc biệt tạo ra.

**Public key** được sử dụng để mã hóa nội dung nào đó và chỉ có thể giải mã khi có **private key**

## Certificate authority (CA)

CA là nơi sẽ đảm bảo public key và public DNS name (ví dụ www.foobar.com) là khớp với nhau. Ví dụ, làm sao client biết được nếu một public key đúng là của www.foobar.com. CA sử dụng private key và công thức để tạo ra một chữ ký trên public key này.

## Certificate signing request (CSR)

CSR là một dữ liệu đã format, dữ liệu này bao gồm public key, thông tin gì đó được nhét vào. Tuy nhiên, client không thông dịch CSR, CA sẽ làm.
