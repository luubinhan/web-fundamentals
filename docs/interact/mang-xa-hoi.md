Để cung cấp các thông tin sẽ hiển thị khi trang web được chia sẻ trên các trang xã hội, người sử dụng sẽ có xu hướng click vào những đường link với thông tin đầy đủ và họ cảm thấy liên quan

![](https://developers.google.com/web/fundamentals/discovery/social-discovery/imgs/gplus-snippet-2.png)

Thông tin được cung cấp đầy đủ luôn lôi cuốn người sử dụng

![](https://developers.google.com/web/fundamentals/discovery/social-discovery/imgs/gplus-snippet-1.png)

Nhiều thông tin bị bỏ sót

## Sử dụng schema.org và microdata

Sử dụng [microdata](http://www.w3.org/TR/microdata/) và [schema.org](https://schema.org/) như ví dụ để công cấp thông tin cho các công cụ tìm kiếm

```html
<div itemscope itemtype="http://schema.org/Article">
  <h1 itemprop="name">Enjoy fireworks</h1>
  <p itemprop="description">Fireworks are beautiful.
   This article explains how beautiful fireworks are.</p>
  <img itemprop="image" src="//developers.google.com/web/imgs/fireworks.jpg" />
</div>
```

- `itemscope` để gộp những thông tin liên quan
- `itemtype` đi kèm với `itemscope`, khai báo thông tin bên dưới thuộc *loại* nào đã cộng đồng thống nhất
- `itemprop` là giá trị đã định nghĩa từ trang schema.org

Dùng công cụ [Structured Data Testing Tool](https://www.google.com/webmasters/tools/richsnippets) để kiểm tra kết quả sẽ hiện thị của trang trên khi search bằng công cụ tìm kiếm

![](https://developers.google.com/web/fundamentals/discovery/social-discovery/imgs/webmaster-tools.png)

## Sử dụng Open Graph Protocol (OGP) cho Facebook

[Open Graph Protocol (OGP)](http://ogp.me/) sẽ cung cấp các thông tin để hiển thị trên Facebook

```html
<meta property="og:title" content="Enjoy Fireworks">
<meta property="og:description"
      content="Fireworks are beautiful. This article explains how beautiful fireworks are.">
<meta property="og:image"
      content="https://developers.google.com/web/imgs/fireworks.jpg">
<meta property="og:url"
      content="https://example.com/discovery-and-distribution/optimizations-for-crawlers/social-sites.html">
<meta property="og:type" content="website">
```

- `og:title` là title của trang web
- `og:description` phần diễn giải
- `og:url` đường dẫn chính
- `og:image` hình thumbnail
- `og:type` loại trang web, xem đầy đủ thông tin có thể để [ở đây](https://developers.facebook.com/docs/reference/opengraph/).

Dùng công cụ [Debugger](https://developers.facebook.com/tools/debug/) của Facebook để kiểm tra

## Twitter card

[Twitter Cards](https://dev.twitter.com/cards/overview)  là phần mở rộng [Open Graph Protocol](https://twitter.com/), ngoài thông tin meta như cho Facebook, cần bổ sung thêm

```html
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:site" content="agektmr">
```

![](https://developers.google.com/web/fundamentals/discovery/social-discovery/imgs/twitter-card.png)

Dùng [công cụ kiểm tra Twitter](https://cards-dev.twitter.com/validator)


## Best Practice


```html
<!-- namespace declaration -->
<html prefix="og: http://ogp.me/ns#">
  <!-- define microdata scope and type -->
  <head itemscope itemtype="http://schema.org/Article">
    <title>Social Site Example</title>
    <!-- define ogp and itemprop of microdata in one line -->
    <meta property="og:title" itemprop="name" content="Enjoy Fireworks">
    <!-- define ogp image -->
    <meta property="og:image"
          content="https://developers.google.com/web/imgs/fireworks.jpg">
    <!-- use link[href] to define image url for microdata -->
    <link itemprop="image" href="//developers.google.com/web/imgs/fireworks.jpg">
    <!-- define ogp and itemprop of microdata in one line -->
    <meta property="og:url"
          content="https://example.com/discovery-and-distribution/optimizations-for-crawlers/social-sites2.html">
    <!-- define ogp type -->
    <meta property="og:type" content="website">
    <!-- define twitter cards type -->
    <meta name="twitter:card" content="summary_large_image">
    <!-- define site's owner twitter id -->
    <meta name="twitter:site" content="agektmr">
    <!-- define description for ogp and itemprop of microdata in one line -->
    <meta property="og:description" itemprop="description"
          content="Fireworks are beautiful. This article explains how beautiful fireworks are.">
    <!-- general description (separate with ogp and microdata) -->
    <meta name="description"
          content="Fireworks are beautiful. This article explains how beautiful fireworks are.">
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
    <link rel="stylesheet" href="https://code.getmdl.io/1.2.1/material.indigo-pink.min.css">
    <script defer src="https://code.getmdl.io/1.2.1/material.min.js"></script>
    <style>
      body {
        margin: 2em;
      }
    </style>
  </head>
```

[https://developers.google.com/web/fundamentals/discovery/social-discovery](https://developers.google.com/web/fundamentals/discovery/social-discovery)
