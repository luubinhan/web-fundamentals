Third-party resource hay sử dụng như để hiển thị quảng cáo, video, tích hợp với social media. Bình thường chúng ta cho load các resource này sớm nhất có thể, nhưng nó sẽ làm chậm tốc độ load trang

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/cvQ4fxFUG5MIXtUfi77Z.jpg?auto=format&w=845)

Facade là một element tĩnh hoàn toán giống với element được nhúng bằng third-party

![](https://web-dev.imgix.net/image/tcFciHGuF3MxnTr1y5ue01OGLBn2/R0osncucBqYCIZfC85Hu.jpg?auto=format&w=845)

Cách làm

1. Khi load: thêm facade vào trang
2. Khi chuột rê lên: facade preconnect vào third-party resource
3. Khi click chuột: facade được thay thể bằng third-party

Facade gợi ý để sử dụng

YouTube

- [paulirish/lite-youtube-embed](https://github.com/paulirish/lite-youtube-embed)
- [justinribeiro/lite-youtube](https://github.com/justinribeiro/lite-youtube)
- [Daugilas/lazyYT](https://github.com/Daugilas/lazyYT)

Vimeo

- [luwes/lite-vimeo-embed](https://github.com/luwes/lite-vimeo-embed)
- [slightlyoff/lite-vimeo](https://github.com/slightlyoff/lite-vimeo)

Live chat

- [calibreapp/react-live-chat-loader](https://github.com/calibreapp/react-live-chat-loader) ([blog post](https://calibreapp.com/blog/fast-live-chat))