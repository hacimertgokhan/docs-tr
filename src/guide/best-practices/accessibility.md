# Erişilebilirlik {#erişilebilirlik}

Web erişilebilirliği (a11y olarak da bilinir), engelli bir kişi, yavaş bir bağlantı, eski veya bozuk donanım veya sadece olumsuz bir ortamda bulunan biri olsun, herkes tarafından kullanılabilen web siteleri oluşturma uygulamasına atıfta bulunur. Örneğin, bir videoya altyazı eklemek hem sağır ve işitme güçlüğü olan kullanıcılarınıza hem de gürültülü bir ortamda bulunan ve telefonlarını duyamayan kullanıcılarınıza yardımcı olacaktır. Benzer şekilde, metninizin çok düşük kontrastlı olmadığından emin olmak, hem az gören kullanıcılarınıza hem de parlak güneş ışığında telefonlarını kullanmaya çalışan kullanıcılarınıza yardımcı olacaktır.

Başlamaya hazır ama nereden başlayacağınızı bilmiyor musunuz?

[World Wide Web Consortium (W3C)](https://www.w3.org/) tarafından sağlanan [Web erişilebilirliğini planlama ve yönetme kılavuzuna](https://www.w3.org/WAI/planning-and-managing/) göz atın.

## Atla bağlantısı {#atla-bağlantısı}

Kullanıcıların birden fazla web sayfasında tekrarlanan içeriği atlaması için her sayfanın en üstüne doğrudan ana içerik alanına giden bir bağlantı eklemelisiniz.

Tipik olarak bu, tüm sayfalarınızda ilk odaklanılabilir öğe olacağından `App.vue` üstünde yapılır:

```vue-html
<ul class="skip-links">
  <li>
    <a href="#main" ref="skipLink" class="skip-link">Ana içeriğe atla</a>
  </li>
</ul>
```

Odaklanılmadığı sürece bağlantıyı gizlemek için aşağıdaki stili ekleyebilirsiniz:

```css
.skip-link {
  white-space: nowrap;
  margin: 1em auto;
  top: 0;
  position: fixed;
  left: 50%;
  margin-left: -72px;
  opacity: 0;
}
.skip-link:focus {
  opacity: 1;
  background-color: white;
  padding: 0.5em;
  border: 1px solid black;
}
```

Bir kullanıcı rota değiştirdiğinde, odağı tekrar atla bağlantısına getirin. Bu, atla bağlantısının şablon referansında `focus` çağrılarak elde edilebilir ( `vue-router` kullanımı varsayılarak):

<div class="options-api">

```vue
<script>
export default {
  watch: {
    $route() {
      this.$refs.skipLink.focus()
    }
  }
}
</script>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const skipLink = ref()

watch(
  () => route.path,
  () => {
    skipLink.value.focus()
  }
)
</script>
```

</div>

[Ana içeriğe atlama bağlantısı hakkındaki belgelere göz atın](https://www.w3.org/WAI/WCAG21/Techniques/general/G1.html)

## İçerik Yapısı {#içerik-yapısı}

Erişilebilirliğin en önemli parçalarından biri, tasarımın erişilebilir uygulamayı destekleyebildiğinden emin olmaktır. Tasarım, yalnızca renk kontrastını, yazı tipi seçimini, metin boyutlandırmasını ve dili değil, aynı zamanda içeriğin uygulamada nasıl yapılandırıldığını da dikkate almalıdır.

### Başlıklar {#başlıklar}

Kullanıcılar, bir uygulamada başlıklar aracılığıyla gezinebilir. Uygulamanızın her bölümü için açıklayıcı başlıklar kullanmak, kullanıcıların her bölümün içeriğini tahmin etmesini kolaylaştırır. Başlıklar söz konusu olduğunda, önerilen birkaç erişilebilirlik uygulaması vardır:

- Başlıkları sıralama düzenine göre iç içe yerleştirin: `<h1>` - `<h6>`
- Bir bölüm içinde başlıkları atlamayın
- Başlıkların görsel görünümünü vermek için metni stillendirmek yerine gerçek başlık etiketlerini kullanın

[Başlıklar hakkında daha fazla bilgi edinin](https://www.w3.org/TR/UNDERSTANDING-WCAG20/navigation-mechanisms-descriptive.html)

```vue-html
<main role="main" aria-labelledby="main-title">
  <h1 id="main-title">Ana başlık</h1>
  <section aria-labelledby="section-title-1">
    <h2 id="section-title-1"> Bölüm Başlığı </h2>
    <h3>Bölüm Alt Başlığı</h3>
    <!-- İçerik -->
  </section>
  <section aria-labelledby="section-title-2">
    <h2 id="section-title-2"> Bölüm Başlığı </h2>
    <h3>Bölüm Alt Başlığı</h3>
    <!-- İçerik -->
    <h3>Bölüm Alt Başlığı</h3>
    <!-- İçerik -->
  </section>
</main>
```

### İşaretler {#işaretler}

[İşaretler](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/landmark_role), bir uygulama içindeki bölümlere programlı erişim sağlar. Yardımcı teknolojiye güvenen kullanıcılar, uygulamanın her bölümüne gidebilir ve içeriği atlayabilir. Bunu başarmak için [ARIA rollerini](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles) kullanabilirsiniz.

| HTML            | ARIA Rolü            | İşaret Amacı                                                                                                   |
| --------------- | -------------------- | --------------------------------------------------------------------------------------------------------------- |
| header          | role="banner"        | Birincil başlık: sayfanın başlığı                                                                                 |
| nav             | role="navigation"    | Belgede veya ilgili belgelerde gezinirken kullanıma uygun bağlantı koleksiyonu                                  |
| main            | role="main"          | Belgenin ana veya merkezi içeriği.                                                                              |
| footer          | role="contentinfo"   | Üst belge hakkında bilgiler: dipnotlar/telif hakları/gizlilik beyanına bağlantılar                                |
| aside           | role="complementary" | Ana içeriği destekler ancak kendi başına ayrılmış ve anlamlıdır                                                   |
| search          | role="search"        | Bu bölüm, uygulama için arama işlevselliğini içerir                                                            |
| form            | role="form"          | Formla ilişkili öğelerin koleksiyonu                                                                            |
| section         | role="region"        | İlgili olan ve kullanıcıların gitmek isteyeceği içerik. Bu öğe için etiket sağlanmalıdır                        |

[İşaretler hakkında daha fazla bilgi edinin](https://www.w3.org/TR/wai-aria-1.2/#landmark_roles)

## Semantik Formlar {#semantik-formlar}

Bir form oluştururken, aşağıdaki öğeleri kullanabilirsiniz: `<form>`, `<label>`, `<input>`, `<textarea>` ve `<button>`

Etiketler tipik olarak form alanlarının üstüne veya soluna yerleştirilir:

```vue-html
<form action="/dataCollectionLocation" method="post" autocomplete="on">
  <div v-for="item in formItems" :key="item.id" class="form-item">
    <label :for="item.id">{{ item.label }}: </label>
    <input
      :type="item.type"
      :id="item.id"
      :name="item.id"
      v-model="item.value"
    />
  </div>
  <button type="submit">Gönder</button>
</form>
```

Form öğesine `autocomplete='on'` ifadesini nasıl ekleyebileceğinize ve bunun formunuzdaki tüm girişlere nasıl uygulanacağına dikkat edin. Her giriş için [autocomplete özniteliği için farklı değerler](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete) de ayarlayabilirsiniz.

### Etiketler {#etiketler}

Tüm form kontrollerinin amacını açıklamak için etiketler sağlayın; `for` ve `id` bağlantısını yapın:

```vue-html
<label for="name">Ad: </label>
<input type="text" name="name" id="name" v-model="name" />
```

Bu öğeyi Chrome Geliştirici Araçlarınızda incelerseniz ve Öğeler sekmesinin içindeki Erişilebilirlik sekmesini açarsanız, girişin adını etiketten nasıl aldığını göreceksiniz:

![Etiketten giriş erişilebilir adını gösteren Chrome Geliştirici Araçları](./images/AccessibleLabelChromeDevTools.png)

:::warning Uyarı:
Şöyle etiketlerin giriş alanlarını sardığını görmüş olabilirsiniz:

```vue-html
<label>
  Ad:
  <input type="text" name="name" id="name" v-model="name" />
</label>
```

Eşleşen bir kimliğe sahip etiketleri açıkça ayarlamak, yardımcı teknoloji tarafından daha iyi desteklenir.
:::

#### `aria-label` {#aria-label}

Girişe [`aria-label`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-label) ile erişilebilir bir ad da verebilirsiniz.

```vue-html
<label for="name">Ad: </label>
<input
  type="text"
  name="name"
  id="name"
  v-model="name"
  :aria-label="nameLabel"
/>
```

Erişilebilir adın nasıl değiştiğini görmek için bu öğeyi Chrome Geliştirici Araçları'nda incelemekten çekinmeyin:

![aria-label'dan giriş erişilebilir adını gösteren Chrome Geliştirici Araçları](./images/AccessibleARIAlabelDevTools.png)

#### `aria-labelledby` {#aria-labelledby}

[`aria-labelledby`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-labelledby) kullanmak, etiket metni ekranda görünüyorsa kullanılması dışında `aria-label` ile benzerdir. Diğer öğelerle `id`'leri aracılığıyla eşleştirilir ve birden fazla `id` bağlayabilirsiniz:

```vue-html
<form
  class="demo"
  action="/dataCollectionLocation"
  method="post"
  autocomplete="on"
>
  <h1 id="billing">Faturalandırma</h1>
  <div class="form-item">
    <label for="name">Ad: </label>
    <input
      type="text"
      name="name"
      id="name"
      v-model="name"
      aria-labelledby="billing name"
    />
  </div>
  <button type="submit">Gönder</button>
</form>
```

![aria-labelledby'den giriş erişilebilir adını gösteren Chrome Geliştirici Araçları](./images/AccessibleARIAlabelledbyDevTools.png)

#### `aria-describedby` {#aria-describedby}

[aria-describedby](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-describedby), kullanıcının ihtiyaç duyabileceği ek bilgilerle bir açıklama sağlaması dışında `aria-labelledby` ile aynı şekilde kullanılır. Bu, herhangi bir girdi için ölçütleri açıklamak için kullanılabilir:

```vue-html
<form
  class="demo"
  action="/dataCollectionLocation"
  method="post"
  autocomplete="on"
>
  <h1 id="billing">Faturalandırma</h1>
  <div class="form-item">
    <label for="name">Tam Ad: </label>
    <input
      type="text"
      name="name"
      id="name"
      v-model="name"
      aria-labelledby="billing name"
      aria-describedby="nameDescription"
    />
    <p id="nameDescription">Lütfen ad ve soyad girin.</p>
  </div>
  <button type="submit">Gönder</button>
</form>
```

Chrome Geliştirici Araçları'nı inceleyerek açıklamayı görebilirsiniz:

![aria-labelledby'den giriş erişilebilir adını ve aria-describedby ile açıklamayı gösteren Chrome Geliştirici Araçları](./images/AccessibleARIAdescribedby.png)

### Yer Tutucu {#yer-tutucu}

Çok sayıda kullanıcının kafasını karıştırabileceği için yer tutucuları kullanmaktan kaçının.

Yer tutucularla ilgili sorunlardan biri, varsayılan olarak [renk kontrastı kriterlerini](https://www.w3.org/WAI/WCAG21/Understanding/contrast-minimum.html) karşılamamalarıdır; renk kontrastını düzeltmek, yer tutucunun giriş alanlarında önceden doldurulmuş veriler gibi görünmesini sağlar. Aşağıdaki örneğe bakıldığında, renk kontrastı kriterlerini karşılayan Soyadı yer tutucusunun önceden doldurulmuş veriler gibi göründüğünü görebilirsiniz:

![Erişilebilir yer tutucu](./images/AccessiblePlaceholder.png)

```vue-html
<form
  class="demo"
  action="/dataCollectionLocation"
  method="post"
  autocomplete="on"
>
  <div v-for="item in formItems" :key="item.id" class="form-item">
    <label :for="item.id">{{ item.label }}: </label>
    <input
      type="text"
      :id="item.id"
      :name="item.id"
      v-model="item.value"
      :placeholder="item.placeholder"
    />
  </div>
  <button type="submit">Gönder</button>
</form>
```

```css
/* https://www.w3schools.com/howto/howto_css_placeholder.asp */

#lastName::placeholder {
  /* Chrome, Firefox, Opera, Safari 10.1+ */
  color: black;
  opacity: 1; /* Firefox */
}

#lastName:-ms-input-placeholder {
  /* Internet Explorer 10-11 */
  color: black;
}

#lastName::-ms-input-placeholder {
  /* Microsoft Edge */
  color: black;
}
```

Kullanıcının formları doldurmak için ihtiyaç duyduğu tüm bilgileri herhangi bir girişin dışında sağlamak en iyisidir.

### Talimatlar {#talimatlar}

Giriş alanlarınız için talimatlar eklerken, girişe doğru şekilde bağladığınızdan emin olun. Ek talimatlar sağlayabilir ve birden fazla kimliği bir [`aria-labelledby`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-labelledby) içinde bağlayabilirsiniz. Bu, daha esnek bir tasarıma olanak tanır.

```vue-html
<fieldset>
  <legend>aria-labelledby kullanma</legend>
  <label id="date-label" for="date">Geçerli Tarih: </label>
  <input
    type="date"
    name="date"
    id="date"
    aria-labelledby="date-label date-instructions"
  />
  <p id="date-instructions">GG/AA/YYYY</p>
</fieldset>
```

Alternatif olarak, talimatları [`aria-describedby`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-describedby) ile girişe ekleyebilirsiniz:

```vue-html
<fieldset>
  <legend>aria-describedby kullanma</legend>
  <label id="dob" for="dob">Doğum Tarihi: </label>
  <input type="date" name="dob" id="dob" aria-describedby="dob-instructions" />
  <p id="dob-instructions">GG/AA/YYYY</p>
</fieldset>
```

### İçeriği Gizleme {#içeriği-gizleme}

Girişin erişilebilir bir adı olsa bile, etiketleri görsel olarak gizlemek genellikle önerilmez. Ancak, girişin işlevi çevreleyen içerikle anlaşılabiliyorsa, görsel etiketi gizleyebiliriz.

Bu arama alanına bir bakalım:

```vue-html
<form role="search">
  <label for="search" class="hidden-visually">Ara: </label>
  <input type="text" name="search" id="search" v-model="search" />
  <button type="submit">Ara</button>
</form>
```

Bunu yapabiliriz çünkü arama düğmesi, görsel kullanıcıların giriş alanının amacını belirlemesine yardımcı olacaktır.

Öğeleri görsel olarak gizlemek, ancak yardımcı teknoloji için kullanılabilir tutmak için CSS kullanabiliriz:

```css
.hidden-visually {
  position: absolute;
  overflow: hidden;
  white-space: nowrap;
  margin: 0;
  padding: 0;
  height: 1px;
  width: 1px;
  clip: rect(0 0 0 0);
  clip-path: inset(100%);
}
```

#### `aria-hidden="true"` {#aria-hidden-true}

`aria-hidden="true"` eklemek, öğeyi yardımcı teknolojiden gizler, ancak diğer kullanıcılar için görsel olarak kullanılabilir durumda bırakır. Odaklanabilir öğelerde, yalnızca dekoratif, çoğaltılmış veya ekran dışı içerikte kullanmayın.

```vue-html
<p>Bu, ekran okuyuculardan gizlenmemiştir.</p>
<p aria-hidden="true">Bu, ekran okuyuculardan gizlenmiştir.</p>
```

### Düğmeler {#düğmeler}

Bir form içinde düğmeler kullanırken, formu göndermeyi önlemek için türü ayarlamanız gerekir. Düğmeler oluşturmak için bir giriş de kullanabilirsiniz:

```vue-html
<form action="/dataCollectionLocation" method="post" autocomplete="on">
  <!-- Düğmeler -->
  <button type="button">İptal</button>
  <button type="submit">Gönder</button>

  <!-- Giriş düğmeleri -->
  <input type="button" value="İptal" />
  <input type="submit" value="Gönder" />
</form>
```

### İşlevsel Görüntüler {#işlevsel-görüntüler}

İşlevsel görüntüler oluşturmak için bu tekniği kullanabilirsiniz.

- Giriş alanları

  - Bu görüntüler, formlarda bir gönderme türü düğmesi olarak işlev görecektir

  ```vue-html
  <form role="search">
    <label for="search" class="hidden-visually">Ara: </label>
    <input type="text" name="search" id="search" v-model="search" />
    <input
      type="image"
      class="btnImg"
      src="https://img.icons8.com/search"
      alt="Ara"
    />
  </form>
  ```

- Simgeler

```vue-html
<form role="search">
  <label for="searchIcon" class="hidden-visually">Ara: </label>
  <input type="text" name="searchIcon" id="searchIcon" v-model="searchIcon" />
  <button type="submit">
    <i class="fas fa-search" aria-hidden="true"></i>
    <span class="hidden-visually">Ara</span>
  </button>
</form>
```

## Standartlar {#standartlar}

World Wide Web Consortium (W3C) Web Erişilebilirlik Girişimi (WAI), farklı bileşenler için web erişilebilirlik standartları geliştirir:

- [Kullanıcı Aracısı Erişilebilirlik Yönergeleri (UAAG)](https://www.w3.org/WAI/standards-guidelines/uaag/)
  - yardımcı teknolojilerin bazı yönleri de dahil olmak üzere web tarayıcıları ve medya oynatıcıları
- [Yazma Aracı Erişilebilirlik Yönergeleri (ATAG)](https://www.w3.org/WAI/standards-guidelines/atag/)
  - yazma araçları
- [Web İçeriği Erişilebilirlik Yönergeleri (WCAG)](https://www.w3.org/WAI/standards-guidelines/wcag/)
  - web içeriği - geliştiriciler, yazma araçları ve erişilebilirlik değerlendirme araçları tarafından kullanılır

### Web İçeriği Erişilebilirlik Yönergeleri (WCAG) {#web-içeriği-erişilebilirlik-yönergeleri-wcag}

[WCAG 2.1](https://www.w3.org/TR/WCAG21/), [WCAG 2.0](https://www.w3.org/TR/WCAG20/) üzerine genişler ve webdeki değişiklikleri ele alarak yeni teknolojilerin uygulanmasına olanak tanır. W3C, Web erişilebilirlik politikalarını geliştirirken veya güncellerken WCAG'nin en güncel sürümünün kullanılmasını teşvik eder.

#### WCAG 2.1 Dört Ana Yol Gösterici İlke (POUR olarak kısaltılmıştır): {#wcag-2-1-dört-ana-yol-gösterici-ilke-pour-olarak-kısaltılmıştır}

- [Algılanabilir](https://www.w3.org/TR/WCAG21/#perceivable)
  - Kullanıcılar, sunulan bilgileri algılayabilmelidir
- [İşletilebilir](https://www.w3.org/TR/WCAG21/#operable)
  - Arayüz formları, kontrolleri ve navigasyon çalıştırılabilir
- [Anlaşılabilir](https://www.w3.org/TR/WCAG21/#understandable)
  - Bilgiler ve kullanıcı arayüzünün işleyişi tüm kullanıcılar tarafından anlaşılabilir olmalıdır
- [Sağlam](https://www.w3.org/TR/WCAG21/#robust)
  - Kullanıcılar, teknolojiler ilerledikçe içeriğe erişebilmelidir

#### Web Erişilebilirlik Girişimi – Erişilebilir Zengin İnternet Uygulamaları (WAI-ARIA) {#web-erişilebilirlik-girişimi-erişilebilir-zengin-internet-uygulamaları-wai-aria}

W3C'nin WAI-ARIA'sı, dinamik içeriğin ve gelişmiş kullanıcı arayüzü kontrollerinin nasıl oluşturulacağına dair rehberlik sağlar.

- [Erişilebilir Zengin İnternet Uygulamaları (WAI-ARIA) 1.2](https://www.w3.org/TR/wai-aria-1.2/)
- [WAI-ARIA Yazma Uygulamaları 1.2](https://www.w3.org/TR/wai-aria-practices-1.2/)

## Kaynaklar {#kaynaklar}

### Belgeler {#belgeler}

- [WCAG 2.0](https://www.w3.org/TR/WCAG20/)
- [WCAG 2.1](https://www.w3.org/TR/WCAG21/)
- [Erişilebilir Zengin İnternet Uygulamaları (WAI-ARIA) 1.2](https://www.w3.org/TR/wai-aria-1.2/)
- [WAI-ARIA Yazma Uygulamaları 1.2](https://www.w3.org/TR/wai-aria-practices-1.2/)

### Yardımcı Teknolojiler {#yardımcı-teknolojiler}

- Ekran Okuyucular
  - [NVDA](https://www.nvaccess.org/download/)
  - [VoiceOver](https://www.apple.com/accessibility/mac/vision/)
  - [JAWS](https://www.freedomscientific.com/products/software/jaws/?utm_term=jaws%20screen%20reader&utm_source=adwords&utm_campaign=All+Products&utm_medium=ppc&hsa_tgt=kwd-394361346638&hsa_cam=200218713&hsa_ad=296201131673&hsa_kw=jaws%20screen%20reader&hsa_grp=52663682111&hsa_net=adwords&hsa_mt=e&hsa_src=g&hsa_acc=1684996396&hsa_ver=3&gclid=Cj0KCQjwnv71BRCOARIsAIkxW9HXKQ6kKNQD0q8a_1TXSJXnIuUyb65KJeTWmtS6BH96-5he9dsNq6oaAh6UEALw_wcB)
  - [ChromeVox](https://chrome.google.com/webstore/detail/chromevox-classic-extensi/kgejglhpjiefppelpmljglcjbhoiplfn?hl=en)
- Zoom Araçları
  - [MAGic](https://www.freedomscientific.com/products/software/magic/)
  - [ZoomText](https://www.freedomscientific.com/products/software/zoomtext/)
  - [Magnifier](https://support.microsoft.com/en-us/help/11542/windows-use-magnifier-to-make-things-easier-to-see)

### Test {#test}

- Otomatik Araçlar
  - [Lighthouse](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk)
  - [WAVE](https://chrome.google.com/webstore/detail/wave-evaluation-tool/jbbplnpkjmmeebjpijfedlgcdilocofh)
  - [ARC Toolkit](https://chrome.google.com/webstore/detail/arc-toolkit/chdkkkccnlfncngelccgbgfmjebmkmce?hl=en-US)
- Renk Araçları
  - [WebAim Renk Kontrastı](https://webaim.org/resources/contrastchecker/)
  - [WebAim Bağlantı Renk Kontrastı](https://webaim.org/resources/linkcontrastchecker)
- Diğer Yardımcı Araçlar
  - [HeadingMap](https://chrome.google.com/webstore/detail/headingsmap/flbjommegcjonpdmenkdiocclhjacmbi?hl=en…)
  - [Color Oracle](https://colororacle.org)
  - [NerdeFocus](https://chrome.google.com/webstore/detail/nerdefocus/lpfiljldhgjecfepfljnbjnbjfhennpd?hl=en-US…)
  - [Visual Aria](https://chrome.google.com/webstore/detail/visual-aria/lhbmajchkkmakajkjenkchhnhbadmhmk?hl=en-US)
  - [Silktide Web Sitesi Erişilebilirlik Simülatörü](https://chrome.google.com/webstore/detail/silktide-website-accessib/okcpiimdfkpkjcbihbmhppldhiebhhaf?hl=en-US)

### Kullanıcılar {#kullanıcılar}

Dünya Sağlık Örgütü, dünya nüfusunun %15'inin bir tür engelliliğe sahip olduğunu ve bunların %2-4'ünün ciddi şekilde engelli olduğunu tahmin ediyor. Bu, dünya çapında tahmini 1 milyar kişidir; bu da engelli insanları dünyadaki en büyük azınlık grubu yapmaktadır.

Kabaca dört kategoriye ayrılabilen çok çeşitli engellilikler vardır:

- _[Görsel](https://webaim.org/articles/visual/)_ - Bu kullanıcılar, ekran okuyucuları, ekran büyütme, ekran kontrastını kontrol etme veya braille ekranı kullanımından yararlanabilir.
- _[İşitsel](https://webaim.org/articles/auditory/)_ - Bu kullanıcılar, altyazılardan, transkriptlerden veya işaret dili videolarından yararlanabilir.
- _[Motor](https://webaim.org/articles/motor/)_ - Bu kullanıcılar, [motor bozuklukları için çeşitli yardımcı teknolojilerden](https://webaim.org/articles/motor/assistive) yararlanabilir: ses tanıma yazılımı, göz takibi, tek anahtar erişimi, kafa çubuğu, yudum ve üfleme anahtarı, büyük boy trackball fare, uyarlanabilir klavye veya diğer yardımcı teknolojiler.
- _[Bilişsel](https://webaim.org/articles/cognitive/)_ - Bu kullanıcılar, ek medyadan, içeriğin yapısal organizasyonundan, açık ve basit yazılardan yararlanabilir.

Kullanıcılardan anlamak için WebAim'den aşağıdaki bağlantılara göz atın:

- [Web Erişilebilirlik Bakış Açıları: Herkes İçin Etkiyi ve Faydaları Keşfedin](https://www.w3.org/WAI/perspective-videos/)
- [Web Kullanıcılarının Hikayeleri](https://www.w3.org/WAI/people-use-web/user-stories/)
