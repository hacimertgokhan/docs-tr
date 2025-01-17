# Bileşen Örneği {#bilesen-ornegi}

:::info
Bu sayfa, bileşen genel örneğinde, yani `this`'de kullanıma sunulan yerleşik özellikleri ve metotları belgeler.

Bu sayfada listelenen tüm özellikler ( `$data` içindeki iç içe geçmiş özellikler hariç) salt okunurdur.
:::

## $data {#data}

Bileşen tarafından reaktif hale getirilen [`data`](./options-state#data) seçeneğinden döndürülen nesne. Bileşen örneği, veri nesnesi üzerindeki özelliklere erişimi vekil eder.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $data: object
  }
  ```

## $props {#props}

Bileşenin mevcut, çözümlenmiş prop'larını temsil eden bir nesne.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $props: object
  }
  ```

- **Detaylar**

  Yalnızca [`props`](./options-state#props) seçeneği aracılığıyla bildirilen prop'lar dahil edilecektir. Bileşen örneği, prop nesnesi üzerindeki özelliklere erişimi vekil eder.

## $el {#el}

Bileşen örneğinin yönettiği kök DOM düğümü.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $el: Node | undefined
  }
  ```

- **Detaylar**

  `$el`, bileşen [monte edilene](./options-lifecycle#mounted) kadar `undefined` olacaktır.

  - Tek bir kök öğeye sahip bileşenler için, `$el` o öğeyi işaret edecektir.
  - Metin köklü bileşenler için, `$el` metin düğümünü işaret edecektir.
  - Birden çok kök düğüme sahip bileşenler için, `$el`, Vue'nun bileşenin DOM'daki konumunu takip etmek için kullandığı yer tutucu DOM düğümü olacaktır (metin düğümü veya SSR hidrasyon modunda bir yorum düğümü).

  :::tip
  Tutarlılık için, `$el`'e güvenmek yerine öğelere doğrudan erişim için [şablon referansları](/guide/essentials/template-refs) kullanmanız önerilir.
  :::

## $options {#options}

Geçerli bileşen örneğini oluşturmak için kullanılan çözümlenmiş bileşen seçenekleri.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $options: ComponentOptions
  }
  ```

- **Detaylar**

  `$options` nesnesi, geçerli bileşen için çözümlenmiş seçenekleri sunar ve bu olası kaynakların birleştirme sonucudur:

  - Global mixin'ler
  - Bileşen `extends` tabanı
  - Bileşen mixin'leri

  Genellikle özel bileşen seçeneklerini desteklemek için kullanılır:

  ```js
  const app = createApp({
    customOption: 'foo',
    created() {
      console.log(this.$options.customOption) // => 'foo'
    }
  })
  ```

- **Ayrıca bakınız** [`app.config.optionMergeStrategies`](/api/application#app-config-optionmergestrategies)

## $parent {#parent}

Geçerli örneğin bir üst örneği varsa. Kök örneğin kendisi için `null` olacaktır.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $parent: ComponentPublicInstance | null
  }
  ```

## $root {#root}

Geçerli bileşen ağacının kök bileşen örneği. Geçerli örneğin üst öğesi yoksa, bu değer kendisi olacaktır.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $root: ComponentPublicInstance
  }
  ```

## $slots {#slots}

Üst bileşen tarafından geçirilen [yuvaları](/guide/components/slots) temsil eden bir nesne.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $slots: { [name: string]: Slot }
  }

  type Slot = (...args: any[]) => VNode[]
  ```

- **Detaylar**

  Genellikle [render fonksiyonlarını](/guide/extras/render-function) manuel olarak yazarken kullanılır, ancak bir yuvanın mevcut olup olmadığını tespit etmek için de kullanılabilir.

  Her yuva, `this.$slots` üzerinde, o yuvanın adına karşılık gelen anahtar altında vnode'lardan oluşan bir dizi döndüren bir fonksiyon olarak kullanıma sunulur. Varsayılan yuva `this.$slots.default` olarak kullanıma sunulur.

  Bir yuva [kapsamlı bir yuva](/guide/components/slots#scoped-slots) ise, yuva fonksiyonlarına geçirilen argümanlar, yuva prop'ları olarak yuvada kullanılabilir.

- **Ayrıca bakınız** [Render Fonksiyonları - Yuvaları Render Etme](/guide/extras/render-function#rendering-slots)

## $refs {#refs}

[Şablon referansları](/guide/essentials/template-refs) aracılığıyla kaydedilen DOM öğelerinin ve bileşen örneklerinin bir nesnesi.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $refs: { [name: string]: Element | ComponentPublicInstance | null }
  }
  ```

- **Ayrıca bakınız**

  - [Şablon referansları](/guide/essentials/template-refs)
  - [Özel Öznitelikler - ref](./built-in-special-attributes.md#ref)

## $attrs {#attrs}

Bileşenin geri gönderme özniteliklerini içeren bir nesne.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $attrs: object
  }
  ```

- **Detaylar**

  [Geri gönderme Öznitelikleri](/guide/components/attrs), üst bileşen tarafından geçirilen ancak alt öğe tarafından bir prop veya yayılmış bir olay olarak bildirilmemiş öznitelikler ve olay işleyicileridir.

  Varsayılan olarak, `$attrs` içindeki her şey, yalnızca tek bir kök öğe varsa, bileşenin kök öğesinde otomatik olarak devralınacaktır. Bileşenin birden çok kök düğümü varsa bu davranış devre dışı bırakılır ve [`inheritAttrs`](./options-misc#inheritattrs) seçeneğiyle açıkça devre dışı bırakılabilir.

- **Ayrıca bakınız**

  - [Geri gönderme Öznitelikleri](/guide/components/attrs)

## $watch() {#watch}

Gözlemciler oluşturmak için zorunlu API.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $watch(
      source: string | (() => any),
      callback: WatchCallback,
      options?: WatchOptions
    ): StopHandle
  }

  type WatchCallback<T> = (
    value: T,
    oldValue: T,
    onCleanup: (cleanupFn: () => void) => void
  ) => void

  interface WatchOptions {
    immediate?: boolean // varsayılan: false
    deep?: boolean // varsayılan: false
    flush?: 'pre' | 'post' | 'sync' // varsayılan: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }

  type StopHandle = () => void
  ```

- **Detaylar**

  İlk argüman, gözlem kaynağıdır. Bir bileşen özelliği adı dizesi, basit nokta ile ayrılmış bir yol dizesi veya bir [getter fonksiyonu](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description) olabilir.

  İkinci argüman, geri arama fonksiyonudur. Geri arama, gözlemlenen kaynağın yeni değerini ve eski değerini alır.

  - **`immediate`**: Gözlemci oluşturulduğunda geri aramayı hemen tetikler. İlk çağrıda eski değer `undefined` olacaktır.
  - **`deep`**: Kaynak bir nesne ise, geri aramanın derin mutasyonlarda tetiklenmesi için derin geçişi zorlar. [Derin Gözlemcilere](/guide/essentials/watchers#deep-watchers) bakın.
  - **`flush`**: Geri aramanın temizleme zamanlamasını ayarlar. [Geri Arama Temizleme Zamanlamasına](/guide/essentials/watchers#callback-flush-timing) ve [`watchEffect()`](/api/reactivity-core#watcheffect) bakın.
  - **`onTrack / onTrigger`**: Gözlemcinin bağımlılıklarında hata ayıklama. [Gözlemci Hata Ayıklamasına](/guide/extras/reactivity-in-depth#watcher-debugging) bakın.

- **Örnek**

  Bir özellik adını gözlemleyin:

  ```js
  this.$watch('a', (newVal, oldVal) => {})
  ```

  Nokta ile ayrılmış bir yolu gözlemleyin:

  ```js
  this.$watch('a.b', (newVal, oldVal) => {})
  ```

  Daha karmaşık ifadeler için getter kullanma:

  ```js
  this.$watch(
    // `this.a + this.b` ifadesi her farklı bir sonuç verdiğinde,
    // işleyici çağrılacaktır.
    // Sanki hesaplanmış özelliği tanımlamadan
    // hesaplanmış bir özelliği izliyor gibiydik.
    () => this.a + this.b,
    (newVal, oldVal) => {}
  )
  ```

  Gözlemciyi durdurma:

  ```js
  const unwatch = this.$watch('a', cb)

  // daha sonra...
  unwatch()
  ```

- **Ayrıca bakınız**
  - [Seçenekler - `watch`](/api/options-state#watch)
  - [Rehber - Gözlemciler](/guide/essentials/watchers)

## $emit() {#emit}

Geçerli örnekte özel bir olay tetikler. Ek argümanlar, dinleyicinin geri arama fonksiyonuna geçirilecektir.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $emit(event: string, ...args: any[]): void
  }
  ```

- **Örnek**

  ```js
  export default {
    created() {
      // yalnızca olay
      this.$emit('foo')
      // ek argümanlarla
      this.$emit('bar', 1, 2, 3)
    }
  }
  ```

- **Ayrıca bakınız**

  - [Bileşen - Olaylar](/guide/components/events)
  - [`emits` seçeneği](./options-state#emits)

## $forceUpdate() {#forceupdate}

Bileşen örneğini yeniden render etmeye zorlayın.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $forceUpdate(): void
  }
  ```

- **Detaylar**

  Vue'nun tam otomatik reaktivite sistemi göz önüne alındığında buna nadiren ihtiyaç duyulmalıdır. Buna ihtiyaç duyabileceğiniz tek durum, gelişmiş reaktivite API'lerini kullanarak açıkça reaktif olmayan bileşen durumu oluşturduğunuz zamandır.

## $nextTick() {#nexttick}

Global [`nextTick()`](./general#nexttick)'in örneğe bağlı sürümü.

- **Tip**

  ```ts
  interface ComponentPublicInstance {
    $nextTick(callback?: (this: ComponentPublicInstance) => void): Promise<void>
  }
  ```

- **Detaylar**

  `nextTick()`'in global sürümünden tek farkı, `this.$nextTick()`'e geçirilen geri aramanın `this` bağlamının geçerli bileşen örneğine bağlı olmasıdır.

- **Ayrıca bakınız** [`nextTick()`](./general#nexttick)
