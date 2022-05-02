---
id: cdn-links
title: CDN Bağlantıları
permalink: docs/cdn-links.html
prev: create-a-new-react-app.html
next: release-channels.html
---

Hem React'e hem de ReactDOM'a CDN üzerinden ulaşılabilir.

```html
<script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
```

Yukarıdaki sürümler yalnızca geliştirme amaçlıdır ve yayınlamaya uygun değildir. React'in küçültülmüş ve optimize edilmiş yayınlamaya uygun sürümleri şu adreste mevcuttur:

```html
<script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
```

<<<<<<< HEAD
`react`'in ve `react-dom`'un belirli bir sürümünü yüklemek için, `17`'yı istediğiniz sürüm numarası ile değiştirin.
=======
To load a specific version of `react` and `react-dom`, replace `18` with the version number.
>>>>>>> 5f3a9756e00e256735a5f52df19b403d8fdd3a9d

### Neden `crossorigin` Özelliği? {#why-the-crossorigin-attribute}

Bir CDN'den React kullanıyorsanız, [`crossorigin`](https://developer.mozilla.org/en-US/docs/Web/HTML/CORS_settings_attributes) özelliğini ayarlı tutmanızı öneririz:

```html
<script crossorigin src="..."></script>
```

Ayrıca, kullandığınız CDN’nin `Access-Control-Allow-Origin: *` HTTP başlığını ayarladığını doğrulamanızı öneririz:

![Access-Control-Allow-Origin: *](../images/docs/cdn-cors-header.png)

Bu, React 16 ve sonraki sürümlerinde daha iyi bir [hata yönetme deneyimi](/blog/2017/07/26/error-handling-in-react-16.html) sağlar.
