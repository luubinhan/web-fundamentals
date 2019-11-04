# Lấy thông tin vị trí của user

API Geolocation cho phép bạn lấy thông tin vị trí của user. Chúng ta có thể sử dụng để bổ sung cho một tính năng nào đó như bổ sung vị trí hình ảnh đã chụp.

> Từ chrome 50, API Geolocation chỉ hổ trợ khi site chạy HTTPS.


## Khi nào sử dụng Geolocation

- Cần dùng thông tin vị trí để cung cấp những thông tin thực sự liên quan, hữu ích cho user
- Hiển thị vị trí user đang đứng trên bản đồ
- Tự động điền thông tin vị trí cho user


## Xin phép user


Theo khảo sát gần đây, người dùng rất khó chịu khi vừa vào site mà phải trả lời những câu hỏi dạng popup như: cho phép notify, hỏi vị trí, thông báo một chiến lược quảng cáo ngay đầu trang.

> Luôn đặt tình huống xấu nhất: user không cho lấy địa chỉ

Nếu yêu cầu user cho phép lấy thông tin vị trí hiện tại, ngay cả khi chưa sử dụng tới, luôn làm cho user không cảm thấy thoải mái và khả năng sẽ từ chối việc cho phép lấy thông tin này

![](https://developers.google.com/web/fundamentals/native-hardware/user-location/images/sw-navigation-good.png)

**NÊN**: gợi ý cho phép lấy địa chỉ để tăng cường trải nghiệm

![](https://developers.google.com/web/fundamentals/native-hardware/user-location/images/sw-navigation-bad.png)

**KHÔNG**: yêu cầu cho phép lấy thông tin trên trang chủ

Khi cần lấy thông tin địa chỉ, những hành động sẽ yêu cầu việc đó luôn thể hiện một cách rõ ràng nhất

![](https://developers.google.com/web/fundamentals/native-hardware/user-location/images/indication.png)

Sử dụng range finder

![](https://developers.google.com/web/fundamentals/native-hardware/user-location/images/nearme.png)

Dùng nút "Find near me"

## Không ép buộc user

Khi yêu cầu lấy địa chỉ, tránh trường hợp ép buộc user hoặc nhắc đi nhắc lại yêu cầu. Cách làm đề nghị

Để kiểm tra trình duyệt có hỗ trợ API không

```js
// check for Geolocation support
if (navigator.geolocation) {
  console.log('Geolocation is supported!');
}
else {
  console.log('Geolocation is not supported for this Browser/OS.');
}
```

Xác định vị trí hiện tại

```js
window.onload = function() {
  var startPos;
  var geoSuccess = function(position) {
    startPos = position;
    document.getElementById('startLat').innerHTML = startPos.coords.latitude;
    document.getElementById('startLon').innerHTML = startPos.coords.longitude;
  };
  navigator.geolocation.getCurrentPosition(geoSuccess);
};
```

## Theo dõi vị trí user

Khi dùng `getCurrentPosition()` chúng ta chỉ lấy được vị trí lúc user cho phép, nếu muốn theo dõi vị trí user khi user di chuyển, sử dụng `watchPosition()`

```js
var watchId = navigator.geolocation.watchPosition(function(position) { document.getElementById('currentLat').innerHTML = position.coords.latitude; document.getElementById('currentLon').innerHTML = position.coords.longitude; });
```

## Một vài kinh nghiệm đúc kết

Việc sử dụng vị trí vật lý của user không phải miễn phí, để có thể theo dõi vị trí hiện tại, thiết bị của user phải tiêu tốn tài nguyên để xử lý, cũng như pin, do đó sau khi ko còn cần thông tin này nữa, luôn nhớ `clearWatch` để hủy đăng ký.

Có rất nhiều tình huống dẫn đến việc không lấy được thông tin vị trí, việc xuất hiện lỗi là rất thường xảy ra, bên dưới là cách để xử lý khi gặp lỗi.

```js
window.onload = function() {
  var startPos;
  var geoSuccess = function(position) {
    startPos = position;
    document.getElementById('startLat').innerHTML = startPos.coords.latitude;
    document.getElementById('startLon').innerHTML = startPos.coords.longitude;
  };
  var geoError = function(error) {
    console.log('Error occurred. Error code: ' + error.code);
    // error.code can be:
    //   0: unknown error
    //   1: permission denied
    //   2: position unavailable (error response from location provider)
    //   3: timed out
  };
  navigator.geolocation.getCurrentPosition(geoSuccess, geoError);
};
```

Có nhiều trường hợp, như sử dụng trên máy tình bàn, vị trí user không thay đổi nhiều, chúng ta có thể giảm số lần yêu cầu thông tin này bằng việc dùng lại thông tin cũ trước đó, thêm `maximumAge` để cache lại giá trị này

```js
window.onload = function() {
  var startPos;
  var geoOptions = {
    maximumAge: 5 * 60 * 1000,
  }

  var geoSuccess = function(position) {
    startPos = position;
    document.getElementById('startLat').innerHTML = startPos.coords.latitude;
    document.getElementById('startLon').innerHTML = startPos.coords.longitude;
  };
  var geoError = function(error) {
    console.log('Error occurred. Error code: ' + error.code);
    // error.code can be:
    //   0: unknown error
    //   1: permission denied
    //   2: position unavailable (error response from location provider)
    //   3: timed out
  };

  navigator.geolocation.getCurrentPosition(geoSuccess, geoError, geoOptions);
};
```

Nếu không có giá trị `timeout`, yêu cầu lấy thông tin cũng sẽ không bao giờ dừng lại nếu chưa nhận được giá trị return

```js
window.onload = function() {
  var startPos;
  var geoOptions = {
     timeout: 10 * 1000
  }

  var geoSuccess = function(position) {
    startPos = position;
    document.getElementById('startLat').innerHTML = startPos.coords.latitude;
    document.getElementById('startLon').innerHTML = startPos.coords.longitude;
  };
  var geoError = function(error) {
    console.log('Error occurred. Error code: ' + error.code);
    // error.code can be:
    //   0: unknown error
    //   1: permission denied
    //   2: position unavailable (error response from location provider)
    //   3: timed out
  };

  navigator.geolocation.getCurrentPosition(geoSuccess, geoError, geoOptions);
};
```

Theo mặc định, API chỉ trả về vị trí tương đối của user, ví dụ trong trường hợp bạn cần lấy thông tin vị trí chính xác hơn để tìm các cửa hàng gần nhất cho user, chúng ta có thể dùng tùy chọn `enableHighAccuracy`

```js
window.onload = function() {
  var startPos;
  var geoOptions = {
    enableHighAccuracy: true
  }

  var geoSuccess = function(position) {
    startPos = position;
    document.getElementById('startLat').innerHTML = startPos.coords.latitude;
    document.getElementById('startLon').innerHTML = startPos.coords.longitude;
  };
  var geoError = function(error) {
    console.log('Error occurred. Error code: ' + error.code);
    // error.code can be:
    //   0: unknown error
    //   1: permission denied
    //   2: position unavailable (error response from location provider)
    //   3: timed out
  };

  navigator.geolocation.getCurrentPosition(geoSuccess, geoError, geoOptions);
};
```

Giả lập location bằng Chrome DevTools

Mở **Console Drawer**, chọn vào menu **Console Drawer**, click **Sensors**

![](https://developers.google.com/web/tools/chrome-devtools/customize/images/more-drawer-tabs.svg)

![](https://developers.google.com/web/fundamentals/native-hardware/user-location/images/sensors-drawer.png)


[https://developers.google.com/web/fundamentals/native-hardware/user-location](https://developers.google.com/web/fundamentals/native-hardware/user-location)
