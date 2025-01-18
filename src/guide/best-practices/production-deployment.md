# Üretim Dağıtımı {#üretim-dağıtımı}

## Geliştirme ve Üretim {#geliştirme-ve-üretim}

Geliştirme sırasında Vue, geliştirme deneyimini iyileştirmek için bir dizi özellik sunar:

-   Yaygın hatalar ve tuzaklar için uyarılar
-   Prop / olay doğrulaması
-   [Reaktivite hata ayıklama kancaları](/guide/extras/reactivity-in-depth#reactivity-debugging)
-   Geliştirici araçları entegrasyonu

Ancak, bu özellikler üretimde işe yaramaz hale gelir. Bazı uyarı kontrolleri de az miktarda performans yüküne neden olabilir. Üretime dağıtırken, daha küçük yük boyutu ve daha iyi performans için kullanılmayan, yalnızca geliştirmeye yönelik tüm kod dallarını bırakmalıyız.

## Derleme Araçları Olmadan {#derleme-araçları-olmadan}

Vue'yu bir CDN'den veya kendi kendine barındırılan bir komut dosyasından yükleyerek bir derleme aracı olmadan kullanıyorsanız, üretime dağıtırken üretim derlemesini (`.prod.js` ile biten dist dosyaları) kullandığınızdan emin olun. Üretim derlemeleri, yalnızca geliştirmeye yönelik tüm kod dalları kaldırılmış olarak önceden küçültülür.

-   Genel derleme kullanılıyorsa ( `Vue` genelinden erişiliyorsa): `vue.global.prod.js` kullanın.
-   ESM derlemesi kullanılıyorsa (yerel ESM içe aktarmaları yoluyla erişiliyorsa): `vue.esm-browser.prod.js` kullanın.

Daha fazla ayrıntı için [dist dosya kılavuzuna](https://github.com/vuejs/core/tree/main/packages/vue#which-dist-file-to-use) bakın.

## Derleme Araçları İle {#derleme-araçları-ile}

`create-vue` (Vite tabanlı) veya Vue CLI (webpack tabanlı) aracılığıyla iskelelenmiş projeler, üretim derlemeleri için önceden yapılandırılmıştır.

Özel bir kurulum kullanılıyorsa, şunlardan emin olun:

1.  `vue`, `vue.runtime.esm-bundler.js`'ye çözümlenir.
2.  [Derleme zamanı özellik işaretleri](/api/compile-time-flags) düzgün bir şekilde yapılandırılmıştır.
3.  Derleme sırasında <code>process.env<wbr>.NODE_ENV</code> `production` ile değiştirilir.

Ek referanslar:

-   [Vite üretim derleme kılavuzu](https://vitejs.dev/guide/build.html)
-   [Vite dağıtım kılavuzu](https://vitejs.dev/guide/static-deploy.html)
-   [Vue CLI dağıtım kılavuzu](https://cli.vuejs.org/guide/deployment.html)

## Çalışma Zamanı Hatalarını İzleme {#çalışma-zamanı-hatalarını-izleme}

[Uygulama düzeyi hata işleyicisi](/api/application#app-config-errorhandler), hataları izleme hizmetlerine bildirmek için kullanılabilir:

```js
import { createApp } from 'vue'

const app = createApp(...)

app.config.errorHandler = (err, instance, info) => {
  // hatayı izleme hizmetlerine bildir
}
```

[Sentry](https://docs.sentry.io/platforms/javascript/guides/vue/) ve [Bugsnag](https://docs.bugsnag.com/platforms/javascript/vue/) gibi hizmetler, Vue için resmi entegrasyonlar da sağlar.
