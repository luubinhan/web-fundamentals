[First Input Delay](/audit/thoi-gian-input-cham-nhat/) là thời gian user có thể bắt đầu tương tác được với trang

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/eXyvkqRHQZ5iG38Axh1Z.svg)

Nguyên nhân chủ yếu làm FID chậm là do JS đang thực thi trên main thread, chúng ta cần cải thiện quá trình **parse**, **compile**, **execute**

## Chia nhỏ tác vụ

Code nào chiếm main thread quá 50ms được xem là **tác vụ lớn**

![](https://web-dev.imgix.net/image/admin/THLKu0sOPhSghNr0XkP1.png?auto=format&w=845)

Xem thêm [Are long JavaScript tasks delaying your Time to Interactive?](https://web.dev/long-tasks-devtools/)

[Web worker](https://developer.mozilla.org/en-US/docs/Web/API/Worker) là một giải pháp cho phép chạy JS ở background, tham khảo các thư viện để làm việc với web worker

- [Comlink](https://github.com/GoogleChromeLabs/comlink)
- [Workway](https://github.com/WebReflection/workway)
- [Workerize](https://github.com/developit/workerize)

Để giảm số lượng JS thực thi 

- Defer những JS nào chưa sử dụng
- Hạn chế polyfill ko cần thiết

![Kiểm tra JS không sử dụng bằng DevTools](https://web-dev.imgix.net/image/admin/UNEigFiwsGu48rtXMZM4.png?auto=format&w=845)

```html
<script defer src="…"></script>
<script async src="…"></script>
```

https://web.dev/optimize-fid/