## CSS

Nếu user click hoặc chạm vào một đối tượng trên website, nguyên tắc chung là chúng ta phải với trả lời user " u cơ, em đã nhận được yêu cầu"

Việc đó được thực hiện bằng cách css cho đối tượng được chạm vào

> Các trạng thái bắt buộc cho một đối tượng mà user có thể chạm vào để thực hiện một thao tác nào đó gồm:  `:hover`, `:focus`, `:active`

```css
.btn {
	background-color: #4285f4;
}

.btn:hover {
	background-color: #296CDB;
}

.btn:focus {
	background-color: #0F52C1;
	outline: 0;
}

.btn:active {
	background-color: #0039A8;
}
```

![Thêm touch cho website](https://developers.google.com/web/fundamentals/design-and-ux/input/touch/images/button-states.png)

Khi mobile chưa thịnh hành, các website đều bỏ qua trạng thái `:active`, nên trình duyệt tự bổ sung, chúng ta có thể bỏ đi phần định dạng mặc định của trình duyệt

```css
/* bỏ phần định dạng của Chrome */
-webkit-tap-highlight-color: transparent;
```

IE thì dùng thẻ meta

```html
<meta name="msapplication-tap-highlight" content="no">
```

Firefox cũng có cách riêng

```css
.btn {
  background-image: none;
}

.btn::-moz-focus-inner {
  border: 0;
}
```

> Nếu bỏ định dạng của trình duyệt, nhớ tự định dạng bằng `:active`

Có nhiều trường hợp chúng ta không muốn user chọn vào một phần nào đó trên UI, để làm việc đó dùng `user-select`, cũng hết sức cẩn thận khi sử dụng thuộc tính này, vì user dễ nổi cáu vì không chọn được một phần trên UI

```css
user-select: none;
```

## Tương tác tùy biến

Với một tương tác tùy biến, như *swipe* (phổ biến trên ứng dụng mobile), thì có 2 điểm cần ghi nhớ:

1. Hỗ trợ tất cả trình duyệt
2. Đáp ứng nhanh, đồng nghĩa với việc số lượng frame/second phải cao

Chúng ta sẽ tìm hiểu các API cần để hỗ trợ cho tất cả trình duyệt và sử dụng chúng như thế nào cho hiệu quả.

Tùy theo từng tình huống, có khi chúng ta muốn user chỉ được tương tác với một element, hoặc nhiều element cùng lúc.

> Cẩn trọng: Đừng quên có nhiều user muốn sử dụng thuần bàn phím, hoặc các trợ lý ảo trên touchscreen

Chỉ cho phép user tương tác với một element

![Thêm touch cho website](https://developers.google.com/web/fundamentals/design-and-ux/input/touch/images/touch-document-level.gif)

User có thể tương tác với nhiều element cùng lúc

![Thêm touch cho website](https://developers.google.com/web/fundamentals/design-and-ux/input/touch/images/touch-element-level.gif)

### Thêm các listener cho event

Trong Chrome, IE, Edge thì dùng [window.PointerEvent](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events), các trình duyệt khác dùng [window.TouchEvent](https://developer.mozilla.org/vi/docs/Web/API/Touch_events) và [window.MouseEvent](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent)

Với `window.PointerEvent` nó sẽ gom tất cả các kiểu input của chuột, touch, pen vào một callback. Chúng ta sẽ đặt xử lý khi `pointerdown`, `pointermove`, `pointerup` và `pointercancel`

`window.TouchEvent` thì tương ứng là `touchstart`, `touchmove`, `touchend`, `touchcancel`, và `mousedown`, `mousemove`, `mouseup` cho chuột.

| Sự kiện | Giải thích           |
| ------------- |:-------------:|
| touchstart, mousedown, pointerdown | được gọi khi ngón tay lần đầu chạm vào một element or click và giữ chuột |
| touchmove, mousemove, pointermove | Khi user trượt ngón tay trên màn hình, kéo rê chuột |
| touchend, mouseup, pointerup | khi user bắt đầu bỏ ngón tay ra khỏi màn hình, hoặc bắt đầu bỏ rê chuột |
| touchcancel pointercancel| khi trình duyệt cancel xử lý |

```js
if (window.PointerEvent) {
	swipeFrontElement.addEventListener('pointerdown', this.handleGestureStart, true);
	swipeFrontElement.addEventListener('pointermove', this.handleGestureMove, true);
	swipeFrontElement.addEventListener('pointerup', this.handleGestureEnd, true);
	swipeFrontElement.addEventListener('pointercancel', this.handleGestureEnd, true);
} else {
	swipeFrontElement.addEventListener('touchstart', this.handleGestureStart, true);
	swipeFrontElement.addEventListener('touchmove', this.handleGestureMove, true);
	swipeFrontElement.addEventListener('touchend', this.handleGestureEnd, true);
	swipeFrontElement.addEventListener('touchcancel', this.handleGestureEnd, true);

	// chỉ dùng mousedown chỗ này
	swipeFrontElement.addEventListener('mousedown', this.handleGestureStart, true);
}
```

> `window.PointerEvent` chỉ cần event **pointerdown** để xử lý cả 2 trường hợp mouse và touch event

### Xử lý khi bắt đầu và kết thúc chạm

![Thêm touch cho website](https://developers.google.com/web/fundamentals/design-and-ux/input/touch/images/touch-document-level.gif)

Chúng ta sẽ hiện thiện các callback `handleGestureStart`, `handleGestureMove`, `handleGestureEnd`, `handleGestureEnd`

Tóm tắt là thế này: khi user bắt đầu tương tác với một element, chúng ta thay vì listen trên đúng element đó, thì chúng ta **đưa** nó lên `document` để lắng nghe bất kể sự kiện đó diễn ra ở đâu. Như vậy thì chúng ta có thể hỗ trợ việc `touch`

Nếu hình dung đang dùng chuột mà làm cách này có thể hơi vô lý, nhưng thử tưởng tượng sử dụng trên điện thoại, user dùng tay để lướt, bạn sẽ thấy không có gì phi lý.


![Hỗ trợ touch cho website](https://developers.google.com/web/fundamentals/design-and-ux/input/touch/images/scroll-bottleneck.gif)

Điều đó sẽ được thực hiện bên trong hàm `handleGestureStart `

```js{9-15}
this.handleGestureStart = function(evt) {
   evt.preventDefault();

   if(evt.touches && evt.touches.length > 1) {
     return;
   }

   // Add the move and end listeners
   if (window.PointerEvent) {
     evt.target.setPointerCapture(evt.pointerId);
   } else {
     // Add Mouse Listeners
     document.addEventListener('mousemove', this.handleGestureMove, true);
     document.addEventListener('mouseup', this.handleGestureEnd, true);
   }

   initialTouchPos = getGesturePointFromEvent(evt);

   swipeFrontElement.style.transition = 'initial';
 }.bind(this);
```

Nếu có `window.PointerEvent` chúng ta dùng hàm `evt.target.setPointerCapture(evt.pointerId);` để **đưa** event đó lên trên `document`

> Mấu chốt là phải đưa event đó lên document

Trong phần callback của `handleGestureEnd()`, chúng ta xóa listener trên event

```js
this.handleGestureEnd = function(evt) {
  evt.preventDefault();

  if(evt.touches && evt.touches.length > 0) {
    return;
  }

  rafPending = false;

  // Remove Event Listeners
  if (window.PointerEvent) {
    evt.target.releasePointerCapture(evt.pointerId);
  } else {
    // Remove Mouse Listeners
    document.removeEventListener('mousemove', this.handleGestureMove, true);
    document.removeEventListener('mouseup', this.handleGestureEnd, true);
  }

  updateSwipeRestPosition();

  initialTouchPos = null;
}.bind(this);
```

Những phần xử lý khác để di chuyển element chúng ta không đề cập ở bài viết này, vì đó không phải trọng tâm của bài viết. Các bạn có thể xem source của demo để tìm hiểu thêm.

[Xem Demo](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/input/touch/touch-demo-1.html)

### Sử dụng requestAnimationFrame để tối ưu hiệu năng

Bên trong đối tượng `event` chúng ta có thể lấy được tọa độ `x`, `y` nơi xảy ra sự kiện.  Nếu `event` có giá trị `targetTouches`, lấy `clientX` và `clientY` từ sự kiện touch đầu tiên, ngược lại sẽ lấy trực tiếp từ `event`

```js
function getGesturePointFromEvent(evt) {
    var point = {};

    if(evt.targetTouches) {
      // Đối với sự kiện Touch
      point.x = evt.targetTouches[0].clientX;
      point.y = evt.targetTouches[0].clientY;
    } else {
      // Sự kiện của Mouse hoặc Pointer
      point.x = evt.clientX;
      point.y = evt.clientY;
    }

    return point;
  }
```


Vì các event callback được chạy trong **main thread**, nên chúng ta chạy càng ít code càng tốt trong callback để frame rate cao

Sử dụng `requestAnimationFrame()` chúng ta có cơ hội cập nhập UI trước khi browser vẽ một frame và tách một số công việc ra khỏi callback

Nếu bạn chưa quen với `requestAnimationFrame()`, có thể [đọc thêm ở đây](https://developers.google.com/web/fundamentals/performance/rendering/optimize-javascript-execution#use-requestanimationframe-for-visual-changes)

Cách hiện thực thông thường là lưu tọa độ `x` và `y` khi bắt đầu và gọi `requestAnimationFrame()` trong callback của move event

```js
// Handle the start of gestures
this.handleGestureStart = function(evt) {
  evt.preventDefault();

  if(evt.touches && evt.touches.length > 1) {
    return;
  }

  // Add the move and end listeners
  if (window.PointerEvent) {
    evt.target.setPointerCapture(evt.pointerId);
  } else {
    // Add Mouse Listeners
    document.addEventListener('mousemove', this.handleGestureMove, true);
    document.addEventListener('mouseup', this.handleGestureEnd, true);
  }

  initialTouchPos = getGesturePointFromEvent(evt);

  swipeFrontElement.style.transition = 'initial';
}.bind(this);
```

Phương thức `handleGestureMove()` lưu vị trí trước hoặc các giá trị mà `requestAnimateFrame` cần, truyền nó vào `onAnimFrame()`

```js
this.handleGestureMove = function (evt) {
  evt.preventDefault();

  if(!initialTouchPos) {
    return;
  }

  lastTouchPos = getGesturePointFromEvent(evt);

  if(rafPending) {
    return;
  }

  rafPending = true;

  window.requestAnimFrame(onAnimFrame);
}.bind(this);
```

Hàm `onAnimFrame` khi được gọi sẽ thay đổi UI (ví dụ di chuyển UI). Truyền hàm này vào `requestAnimationFrame()`, chúng ta nói với trình duyệt gọi nó trước khi cập nhập trang


```js
function onAnimFrame() {
  if(!rafPending) {
    return;
  }

  var differenceInX = initialTouchPos.x - lastTouchPos.x;

  var newXTransform = (currentXPosition - differenceInX)+'px';
  var transformStyle = 'translateX('+newXTransform+')';
  swipeFrontElement.style.webkitTransform = transformStyle;
  swipeFrontElement.style.MozTransform = transformStyle;
  swipeFrontElement.style.msTransform = transformStyle;
  swipeFrontElement.style.transform = transformStyle;

  rafPending = false;
}
```

[Xem Demo](https://googlesamples.github.io/web-fundamentals/fundamentals/design-and-ux/input/touch/touch-demo-2.html)

## `touch-action`

Thuộc tính CSS `touch-action` cho phép bạn kiểm soát touch behavior mặc định. Trong ví dụ chúng ta sử dụng `touch-action: none` để chặn trình duyệt làm bất cứ gì khi user touch, cho phép chúng ta làm gì đó với touch

```css
touch-action: none;
```

Ví dụ IE10+ khi bạn double-tab sẽ bật zoom. Chúng ta dùng `touch-action: none` sẽ bỏ qua behavior mặc định


## Hỗ trợ trạng thái active trên iOS

Safari trên iOS mặc định không hỗ trợ trạng thái active, chúng ta phải thêm event listener `touchstart` trên `document`, `body` hoặc từng element một

Nên kiểm tra user có đang dùng iOS trước khi áp dụng tùy chỉnh

```js
window.onload = function() {
  if(/iP(hone|ad)/.test(window.navigator.userAgent)) {
    document.body.addEventListener('touchstart', function() {}, false);
  }
};
```

Một cách khác để thêm touch listener trên tất cả các element, sử dụng cẩn thận vì có thể ảnh hưởng tốc độ

```js
window.onload = function() {
  if(/iP(hone|ad)/.test(window.navigator.userAgent)) {
    var elements = document.querySelectorAll('button');
    var emptyFunction = function() {};
    for(var i = 0; i < elements.length; i++) {
      elements[i].addEventListener('touchstart', emptyFunction, false);
    }
  }
};
```

[Add Touch to Your Site](https://developers.google.com/web/fundamentals/design-and-ux/input/touch/)
