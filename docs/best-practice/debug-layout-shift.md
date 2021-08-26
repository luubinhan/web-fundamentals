## Layout Instability API

Đây là API của trình duyệt nhân Chromium, hỗ trợ xác định layout shift, DevTools cũng sử dụng API này

```js
let cls = 0;
new PerformanceObserver((entryList) => {
  for (const entry of entryList.getEntries()) {
    if (!entry.hadRecentInput) {
      cls += entry.value;
      console.log('Current CLS value:', cls, entry);
    }
  }
}).observe({type: 'layout-shift', buffered: true});
```

Đoạn code trên sẽ log thông tin layout shift trong console. Lưu ý

- `buffered: true` sẽ cho kết quả layout shift cả trước và sau khi khởi tạo
- `PerformanceObserver` chị chạy khi main thread đang trong trạng thái `idle`, kết quả phụ thuộc vào yếu tố này nên có thể chậm
- Nó sẽ bỏ qua layout shift xuất hiện trong khoảng 500ms

Kết quả sẽ log ra sẽ như thế này

```bash
duration: 0
entryType: "layout-shift"
hadRecentInput: false
lastInputTime: 0
name: ""
sources: (3) [LayoutShiftAttribution, LayoutShiftAttribution, LayoutShiftAttribution]
startTime: 11317.934999999125
value: 0.17508567530168798
```

Ví dụ

Do element thay đổi kích thước, layout shift sẽ xuất hiện trên element, trong kết quả report có thể chỉ nhận B trong phần `sources`

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/oaM41OYL7mFtGcpIN8KF.png?auto=format&w=964)

Layout shift sẽ bao gồm cả A và B

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/AhaslIEWb5fFMMgiZcI2.png?auto=format&w=964)

Layout shift source chỉ gồm element B, do position thay đổi

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/6zKjd4Ua6YJ94LMlqiMR.png?auto=format&w=964)

Không có layout shift trong tình huống này, mặc dù kích thước element B đã thay đổi

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/ZujHWxsXI3C7tupe42oD.png?auto=format&w=964)

## DevTool

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/Uug2fnJT8mOc2YQmxo2l.png?auto=format&w=964)

Click vào ô đánh dấu layout shift, bên dưới sẽ xuất hiện cửa sổ **Summary**

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/AfVjsH9Nl9w0lJwQZEjR.png?auto=format&w=741)

Thông tin được liệt kê ở đây theo format `[width, height]`, `[x,y]`

Trên cửa sổ Event Log có thể có các thông tin về thời gian xuất hiện Layout Shift

![](https://web-dev.imgix.net/image/j2RDdG43oidUy6AL6LovThjeX9c2/124Dm7vV3EGM7M9fiugs.png?auto=format&w=741)

Có thể bật tính năng highlight khu vực có layout shift, **Settings > More Tools > Rendering > Layout Shift Region**, F5 lại để thấy kết quả

Layout shift có thể do một trong các nguyên nhân

- Thay đổi vị trí của DOM element
- Thay đổi kích thước của DOM element
- Thêm hoặc xóa DOM element
- Animation

> Tip không nên làm animation bằng cách thay đổi top, left, dùng transform của CSS để đạt hiệu năng cao nhất

Nếu chưa thể xác định element gây layout shift, có thể thêm debugger để đi từng bước

```js
new PerformanceObserver((entryList) => {
  for (const entry of entryList.getEntries()) {
    if (!entry.hadRecentInput) {
      cls += entry.value;
      debugger;
      console.log('Current CLS value:', cls, entry);
    }
  }
}).observe({type: 'layout-shift', buffered: true});
```

Xem thêm [code sample](https://github.com/GoogleChromeLabs/web-vitals-report/blob/71b0879334798c732f460945ded5267cab5a36bf/src/js/analytics.js#L104-L118)

