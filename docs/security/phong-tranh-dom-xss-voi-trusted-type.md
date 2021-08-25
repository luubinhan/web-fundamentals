Phòng tránh kiểu tấn công DOM XSS với Trusted Type

[Trusted Types](https://github.com/w3c/webappsec-trusted-types) là một công cụ ra đời để xử lý DOM XSS, được hỗ trợ từ Chrome 83, với các trình duyệt chưa hỗ trợ, có thể dùng [polyfill này](https://github.com/w3c/webappsec-trusted-types#polyfill) 

> DOM cross-site scripting xảy ra khi hacker có thể chèn script độc ẩn trong DOM và execute nó (như `eval`, `.innerHTML`)

Với DOM XSS có thể gom thành 2 loại, từ phía server và từ phía client

Để chặn Server-side XXS, không tạo HTML bằng cách nối string, sử dụng các thư viện đã được kiểm tra để tự động `escape`

> escape là thuật ngữ ám chỉ việc xóa đi các ký tự đặc biệt trong lập trình, kết quả là có một string thuần túy

Phía client, trình duyệt sử dụng Trusted Types để xử lý

Trusted Types sẽ lock toàn bộ những function nguy hiểm sau

- manipulation script: `<script src />` và set text content của thẻ `<script />`
- Generate HTML từ string: [`innerHTML`](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML), [`outerHTML`](https://developer.mozilla.org/en-US/docs/Web/API/Element/outerHTML),[`insertAdjacentHTML`](https://developer.mozilla.org/en-US/docs/Web/API/Element/insertAdjacentHTML), [` srcdoc`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe#attr-srcdoc), [`document.write`](https://developer.mozilla.org/en-US/docs/Web/API/Document/write), [`document.writeln`](https://developer.mozilla.org/en-US/docs/Web/API/Document/writeln), và [`DOMParser.parseFromString`](https://developer.mozilla.org/en-US/docs/Web/API/DOMParser#DOMParser.parseFromString)
- Execute content plugin: `<embed src>`, `<object data>` và `<object codebase>`
- Chạy code js: `eval`, `setTimeout`, `setInterval`, `new Function()`

Trước khi truyền dữ liệu vào các function trên, Trusted Types buộc chúng ta phải xử lý dữ liệu, nếu chỉ là một string sẽ không được chấp nhận vì trình duyệt không thể xác định được có nguy hiểm không

☠️Không được phép

```js
anElement.innerHTML = location.href
```

Nó sẽ throw `TypeError` khi chúng ta viết như vậy

✅ Nên

```js
anElement.innerHTML = aTrustedHTML;
```

Browser chấp nhận `TrustedHTML`, `TrustedScript`, `TrustedScriptUrl` khi sử dụng với các function kể trên.

## Làm sao sử dụng Trusted Types

>  Công cụ hỗ trở đánh giá Content Security Policy [go-csp-collector](https://github.com/jacobbednarz/go-csp-collector)

Có thể debug nhanh bằng trình duyệt

```js
window.addEventListener('securitypolicyviolation',
   console.error.bind(console))
```

Thêm vào trong header của HTTP Response muốn migrate sang Trusted Types

```bash
Content-Security-Policy-Report-Only: require-trusted-types-for 'script'; report-uri //my-csp-endpoint.example
```

Những gì không hợp lệ sẽ được report đến `//my-csp-endpoint.example`

> Trusted Types chỉ hoạt động trên HTTPS và localhost

Ví dụ khi đưa một string cho `innerHTML`, trình duyệt sẽ gửi report như sau

```js
{
"csp-report": {
    "document-uri": "https://my.url.example",
    "violated-directive": "require-trusted-types-for",
    "disposition": "report",
    "blocked-uri": "trusted-types-sink
    // highlight-next-line
    "line-number": 39,
    "column-number": 12,
    // highlight-next-line
    "source-file": "https://my.url.example/script.js",
    "status-code": 0,
    // highlight-next-line
    "script-sample": "Element innerHTML <img src=x"
}
}
```

Kết quả cho thấy, dòng 39 của file `https://my.url.example/script.js` được gọi với một string bắt đầu bằng `<img src=x`

>Sử dụng plugin của eslint để tự động nhắc chúng ta, [eslint plugin](https://github.com/mozilla/eslint-plugin-no-unsanitized)

Để fix chúng ta có thể dùng 3 cách

### DOMPurify

```js
import DOMPurify from 'dompurify';
el.innerHTML = DOMPurify.sanitize(html, { RETURN_TRUSTED_TYPE: true });
```

DOMPurify sẽ senitize HTML, sau đó wrap trong object `TrustedHTML`

☠️Không được phép

```js
el.innerHTML = '<img src=xyz.jpg>';
```

✅ Nên

```js
el.textContent = '';
const img = document.createElement('img');
img.src = 'xyz.jpg';
el.appendChild(img);
```

### Tự tạo Trusted Type policy

Trong trường hợp không thể xóa tính năng đó, và không thể dùng thư viện, thì chúng ta phải tự viết object Trusted Type

```js
if (window.trustedTypes && trustedTypes.createPolicy) { // Feature testing
  const escapeHTMLPolicy = trustedTypes.createPolicy('myEscapePolicy', {
    createHTML: string => string.replace(/\</g, '&lt;')
  });
}
```

Đoạn code này tạo ra một policy gọi là `myEscapePolicy` có thể trả về một object `TrustedHTML`  thông qua hàm `createHTML()`, hàm này bản chất sẽ remove hết tất cả ký tự `<` trong string

Sử dụng như sau

```js
const escaped = escapeHTMLPolicy.createHTML('<img src=x onerror=alert(1)>');
console.log(escaped instanceof TrustedHTML);  // true
el.innerHTML = escaped;  // '&lt;img src=x onerror=alert(1)>'
```

### Default Policy

Nếu đoạn code không hợp lệ nằm trong thư viện ngoài, load từ CDN, chúng ta không sửa được thì có thể dùng default policy

```js
if (window.trustedTypes && trustedTypes.createPolicy) { // Feature testing
  trustedTypes.createPolicy('default', {
    createHTML: (string, sink) => DOMPurify.sanitize(string, {RETURN_TRUSTED_TYPE: true})
  });
}
```

Bất kỳ khi nào một string được sử dụng cho các function đã liệt kê, nó sẽ sử dụng policy `default`

> Hạn chế sử dụng default policy, tốt hơn nên refactor lại codebase

