# Uygulama API'si {#uygulama-api}

## createApp() {#createapp}

Bir uygulama örneği oluşturur.

- **Tip**

  ```ts
  function createApp(rootComponent: Component, rootProps?: object): App
  ```

- **Detaylar**

  İlk argüman, kök bileşendir. İkinci isteğe bağlı argüman, kök bileşene iletilecek özelliklerdir.

- **Örnek**

  Satır içi kök bileşen ile:

  ```js
  import { createApp } from 'vue'

  const app = createApp({
    /* kök bileşen seçenekleri */
  })
  ```

  İçe aktarılmış bileşen ile:

  ```js
  import { createApp } from 'vue'
  import App from './App.vue'

  const app = createApp(App)
  ```

- **Ayrıca bakınız** [Rehber - Vue Uygulaması Oluşturma](/guide/essentials/application)

## createSSRApp() {#createssrapp}

[SSR Hidrasyonu](/guide/scaling-up/ssr#client-hydration) modunda bir uygulama örneği oluşturur. Kullanımı `createApp()` ile tamamen aynıdır.

## app.mount() {#app-mount}

Uygulama örneğini bir kapsayıcı öğeye monte eder.

- **Tip**

  ```ts
  interface App {
    mount(rootContainer: Element | string): ComponentPublicInstance
  }
  ```

- **Detaylar**

  Argüman, gerçek bir DOM öğesi veya bir CSS seçici olabilir (ilk eşleşen öğe kullanılacaktır). Kök bileşen örneğini döndürür.

  Bileşenin tanımlanmış bir şablonu veya render fonksiyonu varsa, kapsayıcının içindeki mevcut DOM düğümlerinin yerini alır. Aksi takdirde, çalışma zamanı derleyicisi mevcutsa, kapsayıcının `innerHTML` özelliği şablon olarak kullanılacaktır.

  SSR hidrasyon modunda, kapsayıcının içindeki mevcut DOM düğümlerini hidrate eder. [Uyuşmazlıklar](/guide/scaling-up/ssr#hydration-mismatch) varsa, mevcut DOM düğümleri beklenen çıktıya uyacak şekilde dönüştürülür.

  Her uygulama örneği için `mount()`, yalnızca bir kez çağrılabilir.

- **Örnek**

  ```js
  import { createApp } from 'vue'
  const app = createApp(/* ... */)

  app.mount('#app')
  ```

  Gerçek bir DOM öğesine de monte edilebilir:

  ```js
  app.mount(document.body.firstChild)
  ```

## app.unmount() {#app-unmount}

Monte edilmiş bir uygulama örneğini söker, uygulama bileşen ağacındaki tüm bileşenler için sökme yaşam döngüsü kancalarını tetikler.

- **Tip**

  ```ts
  interface App {
    unmount(): void
  }
  ```

## app.onUnmount() <sup class="vt-badge" data-text="3.5+" /> {#app-onunmount}

Uygulama söküldüğünde çağrılacak bir geri arama kaydeder.

- **Tip**

  ```ts
  interface App {
    onUnmount(callback: () => any): void
  }
  ```

## app.component() {#app-component}

Bir ad dizesi ve bir bileşen tanımı geçiriyorsa global bir bileşen kaydeder veya yalnızca ad geçirilmişse önceden kaydedilmiş bir bileşeni alır.

- **Tip**

  ```ts
  interface App {
    component(name: string): Component | undefined
    component(name: string, component: Component): this
  }
  ```

- **Örnek**

  ```js
  import { createApp } from 'vue'

  const app = createApp({})

  // bir seçenek nesnesi kaydet
  app.component('MyComponent', {
    /* ... */
  })

  // kaydedilmiş bir bileşeni al
  const MyComponent = app.component('MyComponent')
  ```

- **Ayrıca bakınız** [Bileşen Kaydı](/guide/components/registration)

## app.directive() {#app-directive}

Bir ad dizesi ve bir direktif tanımı geçiriyorsa global özel bir direktif kaydeder veya yalnızca ad geçirilmişse önceden kaydedilmiş bir direktifi alır.

- **Tip**

  ```ts
  interface App {
    directive(name: string): Directive | undefined
    directive(name: string, directive: Directive): this
  }
  ```

- **Örnek**

  ```js
  import { createApp } from 'vue'

  const app = createApp({
    /* ... */
  })

  // kaydet (nesne direktifi)
  app.directive('myDirective', {
    /* özel direktif kancaları */
  })

  // kaydet (fonksiyon direktifi kısaltması)
  app.directive('myDirective', () => {
    /* ... */
  })

  // kaydedilmiş bir direktifi al
  const myDirective = app.directive('myDirective')
  ```

- **Ayrıca bakınız** [Özel Direktifler](/guide/reusability/custom-directives)

## app.use() {#app-use}

Bir [eklenti](/guide/reusability/plugins) yükler.

- **Tip**

  ```ts
  interface App {
    use(plugin: Plugin, ...options: any[]): this
  }
  ```

- **Detaylar**

  İlk argüman olarak eklentiyi ve ikinci argüman olarak isteğe bağlı eklenti seçeneklerini bekler.

  Eklenti, bir `install()` metoduna sahip bir nesne veya sadece `install()` metodu olarak kullanılacak bir fonksiyon olabilir. Seçenekler (`app.use()`'nin ikinci argümanı), eklentinin `install()` metoduna iletilecektir.

  `app.use()` aynı eklenti üzerinde birden çok kez çağrıldığında, eklenti yalnızca bir kez yüklenecektir.

- **Örnek**

  ```js
  import { createApp } from 'vue'
  import MyPlugin from './plugins/MyPlugin'

  const app = createApp({
    /* ... */
  })

  app.use(MyPlugin)
  ```

- **Ayrıca bakınız** [Eklentiler](/guide/reusability/plugins)

## app.mixin() {#app-mixin}

Global bir mixin'i (uygulamaya kapsamlı) uygular. Global bir mixin, içerdiği seçenekleri uygulamadaki her bileşen örneğine uygular.

:::warning Önerilmez
Mixin'ler, Vue 3'te esas olarak geriye dönük uyumluluk için desteklenir, çünkü ekosistem kütüphanelerinde yaygın olarak kullanılmaktadır. Uygulama kodunda mixin'lerin, özellikle global mixin'lerin kullanılmasından kaçınılmalıdır.

Mantık yeniden kullanımı için, bunun yerine [Birleştirilebilirler](/guide/reusability/composables) tercih edin.
:::

- **Tip**

  ```ts
  interface App {
    mixin(mixin: ComponentOptions): this
  }
  ```

## app.provide() {#app-provide}

Uygulama içindeki tüm alt bileşenlerde enjekte edilebilen bir değer sağlar.

- **Tip**

  ```ts
  interface App {
    provide<T>(key: InjectionKey<T> | symbol | string, value: T): this
  }
  ```

- **Detaylar**

  İlk argüman olarak enjeksiyon anahtarını ve ikinci olarak sağlanan değeri bekler. Uygulama örneğini kendisi döndürür.

- **Örnek**

  ```js
  import { createApp } from 'vue'

  const app = createApp(/* ... */)

  app.provide('message', 'merhaba')
  ```

  Uygulamadaki bir bileşenin içinde:

  <div class="composition-api">

  ```js
  import { inject } from 'vue'

  export default {
    setup() {
      console.log(inject('message')) // 'merhaba'
    }
  }
  ```

  </div>
  <div class="options-api">

  ```js
  export default {
    inject: ['message'],
    created() {
      console.log(this.message) // 'merhaba'
    }
  }
  ```

  </div>

- **Ayrıca bakınız**
  - [Sağla / Enjekte Et](/guide/components/provide-inject)
  - [Uygulama Düzeyinde Sağlama](/guide/components/provide-inject#app-level-provide)
  - [app.runWithContext()](#app-runwithcontext)

## app.runWithContext() {#app-runwithcontext}

- Sadece 3.3+ sürümlerinde desteklenir

Mevcut uygulamayı enjeksiyon bağlamı olarak kullanarak bir geri çağrı yürütür.

- **Tip**

  ```ts
  interface App {
    runWithContext<T>(fn: () => T): T
  }
  ```

- **Detaylar**

  Bir geri çağrı fonksiyonu bekler ve geri çağrıyı hemen çalıştırır. Geri çağrının eşzamanlı çağrılması sırasında, `inject()` çağrıları, mevcut etkin bileşen örneği olmasa bile, geçerli uygulama tarafından sağlanan değerlerden enjeksiyonları arayabilir. Geri çağrının dönüş değeri de döndürülecektir.

- **Örnek**

  ```js
  import { inject } from 'vue'

  app.provide('id', 1)

  const injected = app.runWithContext(() => {
    return inject('id')
  })

  console.log(injected) // 1
  ```

## app.version {#app-version}

Uygulamanın oluşturulduğu Vue sürümünü sağlar. Bu, farklı Vue sürümlerine dayalı koşullu mantığa ihtiyaç duyabileceğiniz [eklentilerin](/guide/reusability/plugins) içinde kullanışlıdır.

- **Tip**

  ```ts
  interface App {
    version: string
  }
  ```

- **Örnek**

  Bir eklenti içinde sürüm kontrolü yapma:

  ```js
  export default {
    install(app) {
      const version = Number(app.version.split('.')[0])
      if (version < 3) {
        console.warn('Bu eklenti Vue 3 gerektirir')
      }
    }
  }
  ```

- **Ayrıca bakınız** [Global API - version](/api/general#version)

## app.config {#app-config}

Her uygulama örneği, o uygulama için yapılandırma ayarlarını içeren bir `config` nesnesi sunar. Uygulamanızı monte etmeden önce özelliklerini (aşağıda belgelenmiştir) değiştirebilirsiniz.

```js
import { createApp } from 'vue'

const app = createApp(/* ... */)

console.log(app.config)
```

## app.config.errorHandler {#app-config-errorhandler}

Uygulama içinden yayılan yakalanmamış hatalar için global bir işleyici atayın.

- **Tip**

  ```ts
  interface AppConfig {
    errorHandler?: (
      err: unknown,
      instance: ComponentPublicInstance | null,
      // `info` Vue'ye özgü bir hata bilgisidir,
      // örneğin, hatanın hangi yaşam döngüsü kancasında atıldığı
      info: string
    ) => void
  }
  ```

- **Detaylar**

  Hata işleyicisi üç argüman alır: hata, hatayı tetikleyen bileşen örneği ve hata kaynağı türünü belirten bir bilgi dizesi.

  Aşağıdaki kaynaklardan gelen hataları yakalayabilir:

  - Bileşen render'ları
  - Olay işleyicileri
  - Yaşam döngüsü kancaları
  - `setup()` fonksiyonu
  - Gözlemciler
  - Özel direktif kancaları
  - Geçiş kancaları

  :::tip
  Üretimde, 3. argüman (`info`), tam bilgi dizesi yerine kısaltılmış bir kod olacaktır. Koddan dizeye eşlemeyi [Üretim Hata Kodu Referansı](/error-reference/#runtime-errors) içinde bulabilirsiniz.
  :::

- **Örnek**

  ```js
  app.config.errorHandler = (err, instance, info) => {
    // hatayı işle, örneğin bir servise rapor et
  }
  ```

## app.config.warnHandler {#app-config-warnhandler}

Vue'den çalışma zamanı uyarıları için özel bir işleyici atayın.

- **Tip**

  ```ts
  interface AppConfig {
    warnHandler?: (
      msg: string,
      instance: ComponentPublicInstance | null,
      trace: string
    ) => void
  }
  ```

- **Detaylar**

  Uyarı işleyicisi, uyarı mesajını ilk argüman olarak, kaynak bileşen örneğini ikinci argüman olarak ve bir bileşen izleme dizesini üçüncü argüman olarak alır.

  Konsol karmaşıklığını azaltmak için belirli uyarıları filtrelemek için kullanılabilir. Tüm Vue uyarıları geliştirme sırasında ele alınmalıdır, bu nedenle bu, yalnızca birçok uyarı arasında belirli uyarılara odaklanmak için hata ayıklama oturumları sırasında önerilir ve hata ayıklama bittikten sonra kaldırılmalıdır.

  :::tip
  Uyarılar yalnızca geliştirme sırasında çalışır, bu nedenle bu yapılandırma üretim modunda yok sayılır.
  :::

- **Örnek**

  ```js
  app.config.warnHandler = (msg, instance, trace) => {
    // `trace` bileşen hiyerarşisi izidir
  }
  ```

## app.config.performance {#app-config-performance}

Tarayıcı geliştirici araçları performans/zaman çizelgesi panelinde bileşen başlatma, derleme, render ve yama performans takibini etkinleştirmek için bunu `true` olarak ayarlayın. Yalnızca geliştirme modunda ve [performance.mark](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark) API'sini destekleyen tarayıcılarda çalışır.

- **Tip:** `boolean`

- **Ayrıca bakınız** [Rehber - Performans](/guide/best-practices/performance)

## app.config.compilerOptions {#app-config-compileroptions}

Çalışma zamanı derleyici seçeneklerini yapılandırın. Bu nesneye ayarlanan değerler, tarayıcı içi şablon derleyicisine iletilecek ve yapılandırılan uygulamadaki her bileşeni etkileyecektir. Ayrıca, bu seçenekleri bileşen başına [`compilerOptions` seçeneğini](/api/options-rendering#compileroptions) kullanarak geçersiz kılabileceğinizi unutmayın.

::: warning Önemli
Bu yapılandırma seçeneği, yalnızca tam yapı (yani, şablonları tarayıcıda derleyebilen bağımsız `vue.js`) kullanıldığında dikkate alınır. Yalnızca çalışma zamanı yapısını bir oluşturma kurulumuyla kullanıyorsanız, derleyici seçenekleri bunun yerine derleme aracı yapılandırmaları aracılığıyla `@vue/compiler-dom`'a iletilmelidir.

- `vue-loader` için: [`compilerOptions` yükleyici seçeneği aracılığıyla iletin](https://vue-loader.vuejs.org/options.html#compileroptions). Ayrıca [`vue-cli` içinde nasıl yapılandırılacağına](https://cli.vuejs.org/guide/webpack.html#modifying-options-of-a-loader) bakın.

- `vite` için: [`@vitejs/plugin-vue` seçenekleri aracılığıyla iletin](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#options).
  :::

### app.config.compilerOptions.isCustomElement {#app-config-compileroptions-iscustomelement}

Yerel özel öğeleri tanımak için bir kontrol yöntemi belirtir.

- **Tip:** `(tag: string) => boolean`

- **Detaylar**

  Etiket yerel bir özel öğe olarak ele alınması gerekiyorsa `true` döndürmelidir. Eşleşen bir etiket için, Vue bunu bir Vue bileşeni olarak çözümlemeye çalışmak yerine yerel bir öğe olarak oluşturacaktır.

  Yerel HTML ve SVG etiketlerinin bu işlevde eşleştirilmesi gerekmez - Vue'nin ayrıştırıcısı bunları otomatik olarak tanır.

- **Örnek**

  ```js
  // 'ion-' ile başlayan tüm etiketleri özel öğeler olarak ele al
  app.config.compilerOptions.isCustomElement = (tag) => {
    return tag.startsWith('ion-')
  }
  ```

- **Ayrıca bakınız** [Vue ve Web Bileşenleri](/guide/extras/web-components)

### app.config.compilerOptions.whitespace {#app-config-compileroptions-whitespace}

Şablon boşluk işleme davranışını ayarlar.

- **Tip:** `'condense' | 'preserve'`

- **Varsayılan:** `'condense'`

- **Detaylar**

  Vue, daha verimli derlenmiş çıktı üretmek için şablonlardaki boşluk karakterlerini kaldırır / yoğunlaştırır. Varsayılan strateji, aşağıdaki davranışla "yoğunlaştırmak"tır:

  1. Bir öğenin içindeki baştaki / sondaki boşluk karakterleri tek bir boşluğa yoğunlaştırılır.
  2. Yeni satırlar içeren öğeler arasındaki boşluk karakterleri kaldırılır.
  3. Metin düğümlerindeki ardışık boşluk karakterleri tek bir boşluğa yoğunlaştırılır.

  Bu seçeneğin `'preserve'` olarak ayarlanması (2) ve (3)'ü devre dışı bırakır.

- **Örnek**

  ```js
  app.config.compilerOptions.whitespace = 'preserve'
  ```

### app.config.compilerOptions.delimiters {#app-config-compileroptions-delimiters}

Şablon içindeki metin enterpolasyonu için kullanılan sınırlayıcıları ayarlar.

- **Tip:** `[string, string]`

- **Varsayılan:** `{{ "['\u007b\u007b', '\u007d\u007d']" }}`

- **Detaylar**

  Bu, genellikle aynı zamanda bıyık sözdizimini kullanan sunucu tarafı çerçeveleriyle çakışmaktan kaçınmak için kullanılır.

- **Örnek**

  ```js
  // Sınırlayıcılar ES6 şablon dizesi stiline değiştirildi
  app.config.compilerOptions.delimiters = ['${', '}']
  ```

### app.config.compilerOptions.comments {#app-config-compileroptions-comments}

Şablonlardaki HTML yorumlarının işlenmesini ayarlar.

- **Tip:** `boolean`

- **Varsayılan:** `false`

- **Detaylar**

  Varsayılan olarak, Vue yorumları üretimde kaldıracaktır. Bu seçeneğin `true` olarak ayarlanması, Vue'yi üretimde bile yorumları korumaya zorlar. Yorumlar geliştirme sırasında her zaman korunur. Bu seçenek, genellikle Vue'nin HTML yorumlarına dayanan diğer kütüphanelerle birlikte kullanıldığında kullanılır.

- **Örnek**

  ```js
  app.config.compilerOptions.comments = true
  ```

## app.config.globalProperties {#app-config-globalproperties}

Uygulama içindeki herhangi bir bileşen örneğinde erişilebilen global özellikleri kaydetmek için kullanılabilecek bir nesne.

- **Tip**

  ```ts
  interface AppConfig {
    globalProperties: Record<string, any>
  }
  ```

- **Detaylar**

  Bu, Vue 2'nin Vue 3'te artık bulunmayan `Vue.prototype`'ının yerine geçer. Global olan her şeyde olduğu gibi, bu da çok az kullanılmalıdır.

  Global bir özellik bir bileşenin kendi özelliğiyle çakışırsa, bileşenin kendi özelliğinin önceliği daha yüksek olur.

- **Kullanım**

  ```js
  app.config.globalProperties.msg = 'merhaba'
  ```

  Bu, `msg`'yi uygulamadaki herhangi bir bileşen şablonunda ve ayrıca herhangi bir bileşen örneğinin `this` üzerinde kullanılabilir hale getirir:

  ```js
  export default {
    mounted() {
      console.log(this.msg) // 'merhaba'
    }
  }
  ```

- **Ayrıca bakınız** [Rehber - Global Özellikleri Artırma](/guide/typescript/options-api#augmenting-global-properties) <sup class="vt-badge ts" />

## app.config.optionMergeStrategies {#app-config-optionmergestrategies}

Özel bileşen seçenekleri için birleştirme stratejileri tanımlamak için bir nesne.

- **Tip**

  ```ts
  interface AppConfig {
    optionMergeStrategies: Record<string, OptionMergeFunction>
  }

  type OptionMergeFunction = (to: unknown, from: unknown) => any
  ```

- **Detaylar**

  Bazı eklentiler/kütüphaneler, özel bileşen seçenekleri için destek ekler (global mixin'ler enjekte ederek). Aynı seçeneğin birden çok kaynaktan (örneğin, mixin'ler veya bileşen devralımı) "birleştirilmesi" gerektiğinde, bu seçenekler özel birleştirme mantığı gerektirebilir.

  Birleştirme stratejisi işlevi, seçeneğin adını anahtar olarak kullanarak `app.config.optionMergeStrategies` nesnesine atanarak özel bir seçenek için kaydedilebilir.

  Birleştirme stratejisi işlevi, söz konusu seçeneğin üst ve alt örneklerinde tanımlanan değerini sırasıyla birinci ve ikinci argüman olarak alır.

- **Örnek**

  ```js
  const app = createApp({
    // kendinden gelen seçenek
    msg: 'Vue',
    // bir mixin'den gelen seçenek
    mixins: [
      {
        msg: 'Merhaba '
      }
    ],
    mounted() {
      // bu.$options'da gösterilen birleştirilmiş seçenekler
      console.log(this.$options.msg)
    }
  })

  // `msg` için özel bir birleştirme stratejisi tanımla
  app.config.optionMergeStrategies.msg = (parent, child) => {
    return (parent || '') + (child || '')
  }

  app.mount('#app')
  // 'Merhaba Vue' günlüğe kaydedilir
  ```

- **Ayrıca bakınız** [Bileşen Örneği - `$options`](/api/component-instance#options)

## app.config.idPrefix <sup class="vt-badge" data-text="3.5+" /> {#app-config-idprefix}

Bu uygulama içinde [useId()](/api/composition-api-helpers.html#useid) aracılığıyla oluşturulan tüm ID'ler için bir önek yapılandırın.

- **Tip:** `string`

- **Varsayılan:** `undefined`

- **Örnek**

  ```js
  app.config.idPrefix = 'myApp'
  ```

  ```js
  // bir bileşende:
  const id1 = useId() // 'myApp:0'
  const id2 = useId() // 'myApp:1'
  ```

## app.config.throwUnhandledErrorInProduction <sup class="vt-badge" data-text="3.5+" /> {#app-config-throwunhandlederrorinproduction}

Üretim modunda yakalanmamış hataların zorla atılmasını sağlar.

- **Tip:** `boolean`

- **Varsayılan:** `false`

- **Detaylar**

  Varsayılan olarak, bir Vue uygulamasında atılan ancak açıkça işlenmeyen hatalar, geliştirme ve üretim modları arasında farklı davranışlara sahiptir:

  - Geliştirmede, hata atılır ve muhtemelen uygulamanın çökmesine neden olabilir. Bu, hatayı daha belirgin hale getirmek ve geliştirme sırasında fark edilip düzeltilebilmesini sağlamak içindir.

  - Üretimde, hata yalnızca son kullanıcılar üzerindeki etkiyi en aza indirmek için konsola kaydedilecektir. Ancak, bu, yalnızca üretimde gerçekleşen hataların hata izleme hizmetleri tarafından yakalanmasını engelleyebilir.

  `app.config.throwUnhandledErrorInProduction`'ı `true` olarak ayarlayarak, yakalanmamış hatalar üretim modunda bile atılacaktır.
