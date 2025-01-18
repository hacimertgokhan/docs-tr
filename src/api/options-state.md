# Seçenekler: Durum {#options-state}

## data {#data}

Bileşen örneği için ilk reaktif durumu döndüren bir fonksiyon.

- **Tür**

  ```ts
  interface ComponentOptions {
    data?(
      this: ComponentPublicInstance,
      vm: ComponentPublicInstance
    ): object
  }
  ```

- **Detaylar**

  Fonksiyonun, Vue tarafından reaktif hale getirilecek düz bir JavaScript nesnesi döndürmesi beklenir. Örnek oluşturulduktan sonra, reaktif veri nesnesine `this.$data` olarak erişilebilir. Bileşen örneği ayrıca veri nesnesinde bulunan tüm özellikleri vekil olarak kullanır, bu nedenle `this.a`, `this.$data.a` ile eşdeğer olacaktır.

  Tüm üst düzey veri özellikleri, döndürülen veri nesnesine dahil edilmelidir. `this.$data`'ya yeni özellikler eklemek mümkündür, ancak **tavsiye edilmez**. Bir özelliğin istenen değeri henüz kullanılamıyorsa, Vue'nun özelliğin var olduğunu bilmesini sağlamak için `undefined` veya `null` gibi boş bir değer bir yer tutucu olarak dahil edilmelidir.

  `_` veya `$` ile başlayan özellikler, Vue'nun dahili özellikleri ve API metotlarıyla çakışabileceği için bileşen örneğinde vekil olarak **kullanılmaz**. Onlara `this.$data._property` olarak erişmeniz gerekecektir.

  Tarayıcı API nesneleri ve prototip özellikleri gibi kendi durumlu davranışlarına sahip nesneler döndürmek **tavsiye edilmez**. Döndürülen nesne ideal olarak yalnızca bileşenin durumunu temsil eden düz bir nesne olmalıdır.

- **Örnek**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    created() {
      console.log(this.a) // 1
      console.log(this.$data) // { a: 1 }
    }
  }
  ```

  `data` özelliğiyle bir ok fonksiyonu kullanırsanız, `this`'in bileşenin örneği olmayacağını, ancak yine de örneğe fonksiyonun ilk argümanı olarak erişebileceğinizi unutmayın:

  ```js
  data: (vm) => ({ a: vm.myProp })
  ```

- **Ayrıca bakınız** [Reaktivite Derinlemesine](/guide/extras/reactivity-in-depth)

## props {#props}

Bir bileşenin prop'larını bildirin.

- **Tür**

  ```ts
  interface ComponentOptions {
    props?: ArrayPropsOptions | ObjectPropsOptions
  }

  type ArrayPropsOptions = string[]

  type ObjectPropsOptions = { [key: string]: Prop }

  type Prop<T = any> = PropOptions<T> | PropType<T> | null

  interface PropOptions<T> {
    type?: PropType<T>
    required?: boolean
    default?: T | ((rawProps: object) => T)
    validator?: (value: unknown, rawProps: object) => boolean
  }

  type PropType<T> = { new (): T } | { new (): T }[]
  ```

  > Türler, okunabilirlik için basitleştirilmiştir.

- **Detaylar**

  Vue'da, tüm bileşen prop'larının açıkça bildirilmesi gerekir. Bileşen prop'ları iki biçimde bildirilebilir:

  - Bir string dizisi kullanan basit form
  - Her özellik anahtarının prop'un adı olduğu ve değerin prop'un türü (bir oluşturucu fonksiyonu) veya gelişmiş seçenekler olduğu bir nesne kullanan tam form.

  Nesne tabanlı sözdizimiyle, her prop aşağıdaki seçenekleri daha da tanımlayabilir:

  - **`type`**: Aşağıdaki yerel oluşturuculardan biri olabilir: `String`, `Number`, `Boolean`, `Array`, `Object`, `Date`, `Function`, `Symbol`, herhangi bir özel oluşturucu fonksiyonu veya bunların bir dizisi. Geliştirme modunda, Vue bir prop'un değerinin bildirilen türle eşleşip eşleşmediğini kontrol edecek ve eşleşmiyorsa bir uyarı verecektir. Daha fazla ayrıntı için [Prop Doğrulaması](/guide/components/props#prop-validation) bölümüne bakın.

    Ayrıca `Boolean` türüne sahip bir prop'un değer dönüştürme davranışını hem geliştirme hem de üretimde etkilediğini unutmayın. Daha fazla ayrıntı için [Boolean Dönüştürme](/guide/components/props#boolean-casting) bölümüne bakın.

  - **`default`**: Prop üst öğe tarafından geçirilmediğinde veya `undefined` değerine sahip olduğunda prop için bir varsayılan değer belirtir. Nesne veya dizi varsayılanları, bir fabrika fonksiyonu kullanılarak döndürülmelidir. Fabrika fonksiyonu ayrıca ham prop'lar nesnesini argüman olarak alır.

  - **`required`**: Prop'un gerekli olup olmadığını tanımlar. Üretim dışı bir ortamda, bu değer doğruysa ve prop geçirilmemişse bir konsol uyarısı verilir.

  - **`validator`**: Tek argüman olarak prop değerini alan özel doğrulayıcı fonksiyon. Geliştirme modunda, bu fonksiyon yanlış bir değer döndürürse (yani doğrulama başarısız olursa) bir konsol uyarısı verilir.

- **Örnek**

  Basit bildirim:

  ```js
  export default {
    props: ['size', 'myMessage']
  }
  ```

  Doğrulamalarla nesne bildirimi:

  ```js
  export default {
    props: {
      // tür kontrolü
      height: Number,
      // tür kontrolü artı diğer doğrulamalar
      age: {
        type: Number,
        default: 0,
        required: true,
        validator: (value) => {
          return value >= 0
        }
      }
    }
  }
  ```

- **Ayrıca bakınız**
  - [Rehber - Prop'lar](/guide/components/props)
  - [Rehber - Bileşen Prop'larını Türlendirme](/guide/typescript/options-api#typing-component-props) <sup class="vt-badge ts" />

## computed {#computed}

Bileşen örneğinde gösterilecek hesaplanmış özellikleri bildirin.

- **Tür**

  ```ts
  interface ComponentOptions {
    computed?: {
      [key: string]: ComputedGetter<any> | WritableComputedOptions<any>
    }
  }

  type ComputedGetter<T> = (
    this: ComponentPublicInstance,
    vm: ComponentPublicInstance
  ) => T

  type ComputedSetter<T> = (
    this: ComponentPublicInstance,
    value: T
  ) => void

  type WritableComputedOptions<T> = {
    get: ComputedGetter<T>
    set: ComputedSetter<T>
  }
  ```

- **Detaylar**

  Seçenek, anahtarın hesaplanan özelliğin adı olduğu ve değerin bir hesaplanan alıcı veya yazılabilir hesaplanan özellikler için `get` ve `set` metotlarına sahip bir nesne olduğu bir nesne kabul eder.

  Tüm alıcılar ve ayarlayıcıların `this` bağlamı otomatik olarak bileşen örneğine bağlanır.

  Hesaplanmış bir özellikle bir ok fonksiyonu kullanırsanız, `this`'in bileşenin örneğini göstermeyeceğini, ancak yine de örneğe fonksiyonun ilk argümanı olarak erişebileceğinizi unutmayın:

  ```js
  export default {
    computed: {
      aDouble: (vm) => vm.a * 2
    }
  }
  ```

- **Örnek**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    computed: {
      // salt okunur
      aDouble() {
        return this.a * 2
      },
      // yazılabilir
      aPlus: {
        get() {
          return this.a + 1
        },
        set(v) {
          this.a = v - 1
        }
      }
    },
    created() {
      console.log(this.aDouble) // => 2
      console.log(this.aPlus) // => 2

      this.aPlus = 3
      console.log(this.a) // => 2
      console.log(this.aDouble) // => 4
    }
  }
  ```

- **Ayrıca bakınız**
  - [Rehber - Hesaplanan Özellikler](/guide/essentials/computed)
  - [Rehber - Hesaplanan Özellikleri Türlendirme](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

## methods {#methods}

Bileşen örneğine karıştırılacak metotları bildirin.

- **Tür**

  ```ts
  interface ComponentOptions {
    methods?: {
      [key: string]: (this: ComponentPublicInstance, ...args: any[]) => any
    }
  }
  ```

- **Detaylar**

  Bildirilen metotlara doğrudan bileşen örneğinden erişilebilir veya şablon ifadelerinde kullanılabilir. Tüm metotların `this` bağlamı, çevrede geçirilse bile otomatik olarak bileşen örneğine bağlanır.

  Metotları bildirirken ok fonksiyonları kullanmaktan kaçının, çünkü `this` aracılığıyla bileşen örneğine erişemezler.

- **Örnek**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    methods: {
      plus() {
        this.a++
      }
    },
    created() {
      this.plus()
      console.log(this.a) // => 2
    }
  }
  ```

- **Ayrıca bakınız** [Olay İşleme](/guide/essentials/event-handling)

## watch {#watch}

Veri değişikliğinde çağrılacak izleme geri çağrılarını bildirin.

- **Tür**

  ```ts
  interface ComponentOptions {
    watch?: {
      [key: string]: WatchOptionItem | WatchOptionItem[]
    }
  }

  type WatchOptionItem = string | WatchCallback | ObjectWatchOptionItem

  type WatchCallback<T> = (
    value: T,
    oldValue: T,
    onCleanup: (cleanupFn: () => void) => void
  ) => void

  type ObjectWatchOptionItem = {
    handler: WatchCallback | string
    immediate?: boolean // varsayılan: false
    deep?: boolean // varsayılan: false
    flush?: 'pre' | 'post' | 'sync' // varsayılan: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }
  ```

  > Türler, okunabilirlik için basitleştirilmiştir.

- **Detaylar**

  `watch` seçeneği, anahtarların izlenecek reaktif bileşen örneği özellikleri (örneğin, `data` veya `computed` aracılığıyla bildirilen özellikler) olduğu ve değerlerin karşılık gelen geri çağrılar olduğu bir nesne bekler. Geri çağrı, izlenen kaynağın yeni değerini ve eski değerini alır.

  Kök düzeyinde bir özelliğe ek olarak, anahtar basit bir nokta ile ayrılmış yol da olabilir, örn. `a.b.c`. Bu kullanımın karmaşık ifadeleri desteklemediğini unutmayın; yalnızca nokta ile ayrılmış yollar desteklenir. Karmaşık veri kaynaklarını izlemeniz gerekiyorsa, bunun yerine zorunlu [`$watch()`](/api/component-instance#watch) API'sini kullanın.

  Değer, bir metot adının ( `methods` aracılığıyla bildirilen) stringi veya ek seçenekler içeren bir nesne de olabilir. Nesne sözdizimini kullanırken, geri çağrı `handler` alanı altında bildirilmelidir. Ek seçenekler şunları içerir:

  - **`immediate`**: izleyici oluşturulduğunda geri çağrıyı hemen tetikler. Eski değer, ilk çağrıda `undefined` olacaktır.
  - **`deep`**: bir nesne veya dizi ise, geri çağrının derin mutasyonlarda tetiklenmesi için kaynağın derin geçişini zorlar. [Derin İzleyiciler](/guide/essentials/watchers#deep-watchers) bölümüne bakın.
  - **`flush`**: geri çağrının akış zamanlamasını ayarlayın. [Geri Çağrı Akış Zamanlaması](/guide/essentials/watchers#callback-flush-timing) ve [`watchEffect()`](/api/reactivity-core#watcheffect) bölümüne bakın.
  - **`onTrack / onTrigger`**: izleyicinin bağımlılıklarında hata ayıklayın. [İzleyici Hata Ayıklama](/guide/extras/reactivity-in-depth#watcher-debugging) bölümüne bakın.

  İzleme geri çağrılarını bildirirken ok fonksiyonlarını kullanmaktan kaçının, çünkü `this` aracılığıyla bileşen örneğine erişemezler.

- **Örnek**

  ```js
  export default {
    data() {
      return {
        a: 1,
        b: 2,
        c: {
          d: 4
        },
        e: 5,
        f: 6
      }
    },
    watch: {
      // üst düzey özelliği izleme
      a(val, oldVal) {
        console.log(`yeni: ${val}, eski: ${oldVal}`)
      },
      // string metot adı
      b: 'someMethod',
      // izlenen nesne özelliklerinden herhangi biri iç içe derinliklerinden bağımsız olarak değiştiğinde geri çağrı çağrılır
      c: {
        handler(val, oldVal) {
          console.log('c değişti')
        },
        deep: true
      },
      // tek bir iç içe geçmiş özelliği izleme:
      'c.d': function (val, oldVal) {
        // bir şeyler yap
      },
      // gözlemin başlangıcından hemen sonra geri çağrı çağrılacaktır
      e: {
        handler(val, oldVal) {
          console.log('e değişti')
        },
        immediate: true
      },
      // geri çağrılardan oluşan dizi geçirebilirsiniz, bunlar tek tek çağrılacaktır
      f: [
        'handle1',
        function handle2(val, oldVal) {
          console.log('handle2 tetiklendi')
        },
        {
          handler: function handle3(val, oldVal) {
            console.log('handle3 tetiklendi')
          }
          /* ... */
        }
      ]
    },
    methods: {
      someMethod() {
        console.log('b değişti')
      },
      handle1() {
        console.log('handle 1 tetiklendi')
      }
    },
    created() {
      this.a = 3 // => new: 3, old: 1
    }
  }
  ```

- **Ayrıca bakınız** [İzleyiciler](/guide/essentials/watchers)

## emits {#emits}

Bileşen tarafından yayınlanan özel olayları bildirin.

- **Tür**

  ```ts
  interface ComponentOptions {
    emits?: ArrayEmitsOptions | ObjectEmitsOptions
  }

  type ArrayEmitsOptions = string[]

  type ObjectEmitsOptions = { [key: string]: EmitValidator | null }

  type EmitValidator = (...args: unknown[]) => boolean
  ```

- **Detaylar**

  Yayınlanan olaylar iki biçimde bildirilebilir:

  - Bir string dizisi kullanan basit form
  - Her özellik anahtarının olayın adı olduğu ve değerin `null` veya bir doğrulayıcı fonksiyonu olduğu bir nesne kullanan tam form.

  Doğrulama fonksiyonu, bileşenin `$emit` çağrısına geçirilen ek argümanları alacaktır. Örneğin, `this.$emit('foo', 1)` çağrılırsa, `foo` için karşılık gelen doğrulayıcı `1` argümanını alacaktır. Doğrulayıcı fonksiyonu, olay argümanlarının geçerli olup olmadığını belirtmek için bir boolean döndürmelidir.

  `emits` seçeneğinin, hangi olay dinleyicilerinin yerel DOM olay dinleyicilerinden ziyade bileşen olay dinleyicileri olarak kabul edildiğini etkilediğini unutmayın. Bildirilen olayların dinleyicileri, bileşenin `$attrs` nesnesinden kaldırılacak, bu nedenle bileşenin kök öğesine aktarılmayacaklardır. Daha fazla ayrıntı için [Geçiş Özellikleri](/guide/components/attrs) bölümüne bakın.

- **Örnek**

  Dizi sözdizimi:

  ```js
  export default {
    emits: ['check'],
    created() {
      this.$emit('check')
    }
  }
  ```

  Nesne sözdizimi:

  ```js
  export default {
    emits: {
      // doğrulama yok
      click: null,

      // doğrulama ile
      submit: (payload) => {
        if (payload.email && payload.password) {
          return true
        } else {
          console.warn(`Geçersiz gönder olay yükü!`)
          return false
        }
      }
    }
  }
  ```

- **Ayrıca bakınız**
  - [Rehber - Geçiş Özellikleri](/guide/components/attrs)
  - [Rehber - Bileşen Emit'lerini Türlendirme](/guide/typescript/options-api#typing-component-emits) <sup class="vt-badge ts" />

## expose {#expose}

Bileşen örneğine bir üst öğe tarafından şablon referansları aracılığıyla erişildiğinde gösterilen genel özellikleri bildirin.

- **Tür**

  ```ts
  interface ComponentOptions {
    expose?: string[]
  }
  ```

- **Detaylar**

  Varsayılan olarak, bir bileşen örneği, `$parent`, `$root` veya şablon referansları aracılığıyla erişildiğinde tüm örnek özelliklerini üst öğeye gösterir. Bir bileşenin, sıkı bağlanmayı önlemek için özel tutulması gereken dahili durumu veya metotları olduğundan, bu istenmeyen bir durum olabilir.

  `expose` seçeneği, bir özellik adı stringi listesi bekler. `expose` kullanıldığında, bileşenin genel örneğinde yalnızca açıkça listelenen özellikler gösterilecektir.

  `expose`, yalnızca kullanıcı tanımlı özellikleri etkiler; yerleşik bileşen örneği özelliklerini filtrelemez.

- **Örnek**

  ```js
  export default {
    // yalnızca `publicMethod` genel örnekte kullanılabilir olacaktır
    expose: ['publicMethod'],
    methods: {
      publicMethod() {
        // ...
      },
      privateMethod() {
        // ...
      }
    }
  }
  ```
