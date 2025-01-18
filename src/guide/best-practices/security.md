# Güvenlik {#güvenlik}

## Güvenlik Açıklarını Bildirme {#güvenlik-açıklarını-bildirme}

Bir güvenlik açığı bildirildiğinde, tam zamanlı bir katkıda bulunanın her şeyi bırakıp üzerinde çalışmasıyla, hemen en önemli önceliğimiz haline gelir. Bir güvenlik açığı bildirmek için lütfen [security@vuejs.org](mailto:security@vuejs.org) adresine e-posta gönderin.

Yeni güvenlik açıklarının keşfi nadir olsa da, uygulamanızın olabildiğince güvenli kalmasını sağlamak için Vue'nun ve resmi yardımcı kitaplıklarının en son sürümlerini kullanmanızı da öneririz.

## 1. Kural: Güvenilmeyen Şablonları Asla Kullanmayın {#kural-no-1-güvenilmeyen-şablonları-asla-kullanmayın}

Vue kullanırken en temel güvenlik kuralı, **güvenilmeyen içeriği bileşen şablonunuz olarak asla kullanmamaktır**. Bunu yapmak, uygulamanızda rastgele JavaScript yürütülmesine izin vermekle eşdeğerdir ve daha da kötüsü, kod sunucu tarafı oluşturma sırasında yürütülürse sunucu ihlallerine yol açabilir. Böyle bir kullanım örneği:

```js
Vue.createApp({
  template: `<div>` + userProvidedString + `</div>` // ASLA BUNU YAPMAYIN
}).mount('#app')
```

Vue şablonları JavaScript'e derlenir ve şablonlardaki ifadeler, işleme sürecinin bir parçası olarak yürütülür. İfadeler belirli bir işleme bağlamına karşı değerlendirilse de, olası genel yürütme ortamlarının karmaşıklığı nedeniyle, Vue gibi bir çerçevenin sizi gerçekçi olmayan performans yüküne neden olmadan olası kötü amaçlı kod yürütmesinden tamamen koruması pratik değildir. Bu kategori sorunlarından tamamen kaçınmanın en basit yolu, Vue şablonlarınızın içeriğinin her zaman güvenilir ve tamamen sizin tarafınızdan kontrol edildiğinden emin olmaktır.

## Vue Sizi Korumak İçin Ne Yapar? {#vue-sizi-korumak-için-ne-yapar}

### HTML içeriği {#html-içeriği}

Şablonlar veya işleme işlevleri kullanılıp kullanılmadığına bakılmaksızın, içerik otomatik olarak kaçırılır. Bu, bu şablonda şu anlama gelir:

```vue-html
<h1>{{ userProvidedString }}</h1>
```

`userProvidedString` şunları içeriyorsa:

```js
'<script>alert("merhaba")</script>'
```

o zaman aşağıdaki HTML'ye kaçırılır:

```vue-html
&lt;script&gt;alert(&quot;merhaba&quot;)&lt;/script&gt;
```

böylece komut dosyası enjeksiyonu önlenir. Bu kaçırma işlemi, `textContent` gibi yerel tarayıcı API'leri kullanılarak yapılır, bu nedenle bir güvenlik açığı yalnızca tarayıcının kendisi savunmasızsa ortaya çıkabilir.

### Öznitelik bağlamaları {#öznitelik-bağlamaları}

Benzer şekilde, dinamik öznitelik bağlamaları da otomatik olarak kaçırılır. Bu, bu şablonda şu anlama gelir:

```vue-html
<h1 :title="userProvidedString">
  merhaba
</h1>
```

`userProvidedString` şunları içeriyorsa:

```js
'" onclick="alert(\'merhaba\')'
```

o zaman aşağıdaki HTML'ye kaçırılır:

```vue-html
&quot; onclick=&quot;alert('merhaba')
```

böylece yeni, rastgele HTML enjekte etmek için `title` özniteliğinin kapatılması önlenir. Bu kaçırma işlemi, `setAttribute` gibi yerel tarayıcı API'leri kullanılarak yapılır, bu nedenle bir güvenlik açığı yalnızca tarayıcının kendisi savunmasızsa ortaya çıkabilir.

## Olası Tehlikeler {#olası-tehlikeler}

Herhangi bir web uygulamasında, temizlenmemiş, kullanıcı tarafından sağlanan içeriğin HTML, CSS veya JavaScript olarak yürütülmesine izin vermek potansiyel olarak tehlikelidir, bu nedenle mümkün olduğunca kaçınılmalıdır. Ancak, bazı risklerin kabul edilebilir olabileceği zamanlar da vardır.

Örneğin, CodePen ve JSFiddle gibi hizmetler, kullanıcı tarafından sağlanan içeriğin yürütülmesine izin verir, ancak bu, beklendiği ve ölçüde iframe'ler içinde korunduğu bir bağlamdadır. Önemli bir özelliğin doğası gereği bir miktar güvenlik açığı gerektirdiği durumlarda, bu güvenlik açığının etkinleştirdiği en kötü senaryolara karşı özelliğin önemini tartmak ekibinize kalmıştır.

### HTML Enjeksiyonu {#html-enjeksiyonu}

Daha önce öğrendiğiniz gibi, Vue, HTML içeriğini otomatik olarak kaçırarak, uygulamanıza yanlışlıkla yürütülebilir HTML enjekte etmenizi önler. Ancak, **HTML'nin güvenli olduğunu bildiğiniz durumlarda**, HTML içeriğini açıkça işleyebilirsiniz:

-   Bir şablon kullanarak:

    ```vue-html
    <div v-html="userProvidedHtml"></div>
    ```

-   Bir işleme işlevi kullanarak:

    ```js
    h('div', {
      innerHTML: this.userProvidedHtml
    })
    ```

-   JSX ile bir işleme işlevi kullanarak:

    ```jsx
    <div innerHTML={this.userProvidedHtml}></div>
    ```

:::warning
Kullanıcı tarafından sağlanan HTML, korumalı bir iframe'de veya uygulamanın yalnızca bu HTML'yi yazan kullanıcının maruz kalabileceği bir bölümünde olmadığı sürece asla %100 güvenli kabul edilemez. Ek olarak, kullanıcıların kendi Vue şablonlarını yazmasına izin vermek de benzer tehlikeler getirir.
:::

### URL Enjeksiyonu {#url-enjeksiyonu}

Şuna benzer bir URL'de:

```vue-html
<a :href="userProvidedUrl">
  Bana tıkla
</a>
```

`javascript:` kullanarak JavaScript yürütülmesini önlemek için URL "temizlenmemişse" potansiyel bir güvenlik sorunu vardır. Bu konuda yardımcı olacak [sanitize-url](https://www.npmjs.com/package/@braintree/sanitize-url) gibi kitaplıklar vardır, ancak unutmayın: ön uçta URL temizleme yapıyorsanız, zaten bir güvenlik sorununuz var demektir. **Kullanıcı tarafından sağlanan URL'ler, bir veritabanına kaydedilmeden önce her zaman arka uç tarafından temizlenmelidir.** Ardından, bu sorun, yerel mobil uygulamalar da dahil olmak üzere API'nize bağlanan *her* istemci için önlenir. Ayrıca, temizlenmiş URL'lerle bile, Vue'nun bunların güvenli hedeflere yönlendirdiğini garanti edemeyeceğini unutmayın.

### Stil Enjeksiyonu {#stil-enjeksiyonu}

Bu örneğe bakarak:

```vue-html
<a
  :href="sanitizedUrl"
  :style="userProvidedStyles"
>
  Bana tıkla
</a>
```

`sanitizedUrl`'nin temizlendiğini, dolayısıyla JavaScript yerine kesinlikle gerçek bir URL olduğunu varsayalım. `userProvidedStyles` ile kötü amaçlı kullanıcılar hala "tıklama hırsızlığı" için CSS sağlayabilir, örneğin bağlantıyı "Oturum aç" düğmesinin üzerinde şeffaf bir kutuya stillendirebilirler. Ardından, `https://user-controlled-website.com/` uygulamanızın giriş sayfasına benzeyecek şekilde oluşturulmuşsa, bir kullanıcının gerçek oturum açma bilgilerini yakalamış olabilirler.

Bir `<style>` öğesi için kullanıcı tarafından sağlanan içeriğe izin vermenin, kullanıcının tüm sayfayı nasıl stillendireceğine tam kontrol vermesi nedeniyle nasıl daha da büyük bir güvenlik açığı yaratacağını hayal edebilirsiniz. Bu nedenle Vue, şablonlar içinde stil etiketlerinin işlenmesini engeller, örneğin:

```vue-html
<style>{{ userProvidedStyles }}</style>
```

Kullanıcılarınızı tıklama hırsızlığından tamamen korumak için, CSS üzerinde tam kontrolü yalnızca korumalı bir iframe içinde vermenizi öneririz. Alternatif olarak, bir stil bağlaması aracılığıyla kullanıcı kontrolü sağlarken, [nesne sözdizimini](/guide/essentials/class-and-style#binding-to-objects-1) kullanmanızı ve yalnızca kullanıcıların kontrol etmesinin güvenli olduğu belirli özellikler için değerler sağlamalarına izin vermenizi öneririz, örneğin şu şekilde:

```vue-html
<a
  :href="sanitizedUrl"
  :style="{
    color: userProvidedColor,
    background: userProvidedBackground
  }"
>
  Bana tıkla
</a>
```

### JavaScript Enjeksiyonu {#javascript-enjeksiyonu}

Şablonların ve işleme işlevlerinin asla yan etkileri olmaması gerektiğinden, Vue ile bir `<script>` öğesi işlemesini asla önermiyoruz. Ancak bu, çalışma zamanında JavaScript olarak değerlendirilecek dizeleri eklemenin tek yolu değildir.

Her HTML öğesi, `onclick`, `onfocus` ve `onmouseenter` gibi JavaScript dizelerini kabul eden değerlere sahip özniteliklere sahiptir. Kullanıcı tarafından sağlanan JavaScript'i bu olay özniteliklerinden herhangi birine bağlamak potansiyel bir güvenlik riskidir, bu nedenle kaçınılmalıdır.

:::warning
Kullanıcı tarafından sağlanan JavaScript, korumalı bir iframe'de veya uygulamanın yalnızca bu JavaScript'i yazan kullanıcının maruz kalabileceği bir bölümünde olmadığı sürece asla %100 güvenli kabul edilemez.
:::

Bazen Vue şablonlarında siteler arası komut dosyası oluşturmanın (XSS) nasıl mümkün olduğuna dair güvenlik açığı raporları alıyoruz. Genel olarak, bu tür durumları gerçek güvenlik açıkları olarak kabul etmiyoruz çünkü geliştiricileri XSS'ye izin verecek iki senaryodan korumanın pratik bir yolu yok:

1.  Geliştirici, Vue'dan kullanıcı tarafından sağlanan, temizlenmemiş içeriği Vue şablonları olarak işlemesini açıkça istiyor. Bu, doğası gereği güvensizdir ve Vue'nun kaynağını bilmesinin bir yolu yoktur.

2.  Geliştirici, Vue'yu sunucu tarafından işlenmiş ve kullanıcı tarafından sağlanan içeriği içeren tüm bir HTML sayfasına monte ediyor. Bu, temelde 1 numaradakiyle aynı sorundur, ancak bazen geliştiriciler bunu fark etmeden yapabilir. Bu, saldırganın düz HTML olarak güvenli ancak bir Vue şablonu olarak güvenli olmayan HTML sağladığı olası güvenlik açıklarına yol açabilir. En iyi uygulama, **Vue'yu asla sunucu tarafından işlenmiş ve kullanıcı tarafından sağlanan içerik içerebilecek düğümlere monte etmemektir**.

## En İyi Uygulamalar {#en-iyi-uygulamalar}

Genel kural, temizlenmemiş, kullanıcı tarafından sağlanan içeriğin (HTML, JavaScript ve hatta CSS olarak) yürütülmesine izin verirseniz, kendinizi saldırılara açık hale getirebileceğinizdir. Bu tavsiye, ister Vue, ister başka bir çerçeve, hatta çerçeve kullanmıyor olsanız bile geçerlidir.

[Olası Tehlikeler](#olası-tehlikeler) için yukarıda verilen önerilerin ötesinde, kendinizi bu kaynaklara aşina olmanızı da öneririz:

-   [HTML5 Güvenlik Hile Sayfası](https://html5sec.org/)
-   [OWASP'nin Siteler Arası Komut Dosyası Oluşturma (XSS) Önleme Hile Sayfası](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)

Ardından, öğrendiklerinizi, 3. taraf bileşenleri içeren veya DOM'a neyin işlendiğini etkileyen bağımlılıklarınızın kaynak kodunu olası tehlikeli kalıplar için incelemek için de kullanın.

## Arka Uç Koordinasyonu {#arka-uç-koordinasyonu}

Siteler arası istek sahteciliği (CSRF/XSRF) ve siteler arası komut dosyası ekleme (XSSI) gibi HTTP güvenlik açıkları öncelikle arka uçta ele alınır, bu nedenle Vue'nun bir sorunu değildir. Ancak, form gönderimleriyle CSRF belirteçleri göndermek gibi API'leriyle en iyi nasıl etkileşim kuracağınızı öğrenmek için arka uç ekibinizle iletişim kurmanız yine de iyi bir fikirdir.

## Sunucu Tarafı İşleme (SSR) {#sunucu-tarafı-işleme-ssr}

SSR kullanırken bazı ek güvenlik endişeleri vardır, bu nedenle güvenlik açıklarından kaçınmak için [SSR belgelerimizde](/guide/scaling-up/ssr) özetlenen en iyi uygulamaları izlediğinizden emin olun.
