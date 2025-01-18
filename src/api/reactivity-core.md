# Reaktivite API'si: Temel {#reactivity-api-core}

:::info Ayrıca bakınız
Reaktivite API'lerini daha iyi anlamak için kılavuzdaki aşağıdaki bölümleri okumanız önerilir:

- [Reaktivite Temelleri](/guide/essentials/reactivity-fundamentals) (API tercihi Composition API olarak ayarlanmış)
- [Reaktivite Derinlemesine](/guide/extras/reactivity-in-depth)
  :::

## ref() {#ref}

Bir iç değer alır ve iç değere işaret eden tek bir `.value` özelliğine sahip reaktif ve değiştirilebilir bir ref nesnesi döndürür.

- **Tür**

  ```ts
  function ref<T>(value: T): Ref<UnwrapRef<T>>

  interface Ref<T> {
    value: T
  }
  ```

- **Detaylar**

  Ref nesnesi değiştirilebilir; yani `.value`'ya yeni değerler atayabilirsiniz. Aynı zamanda reaktiftir; yani `.value`'ya yapılan tüm okuma işlemleri izlenir ve yazma işlemleri ilişkili efektleri tetikler.

  Bir nesne, bir ref'in değeri olarak atanırsa, nesne [reactive()](#reactive) ile derinlemesine reaktif hale getirilir. Bu aynı zamanda nesne iç içe ref'ler içeriyorsa, bunların derinlemesine sarılacağı anlamına gelir.

  Derin dönüştürmeden kaçınmak için bunun yerine [`shallowRef()`](./reactivity-advanced#shallowref) kullanın.

- **Örnek**

  ```js
  const count = ref(0)
  console.log(count.value) // 0

  count.value = 1
  console.log(count.value) // 1
  ```

- **Ayrıca bakınız**
  - [Rehber - `ref()` ile Reaktivite Temelleri](/guide/essentials/reactivity-fundamentals#ref)
  - [Rehber - `ref()` Türlendirme](/guide/typescript/composition-api#typing-ref) <sup class="vt-badge ts" />

## computed() {#computed}

Bir [getter fonksiyonu](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description) alır ve getter'dan döndürülen değer için salt okunur bir reaktif [ref](#ref) nesnesi döndürür. Yazılabilir bir ref nesnesi oluşturmak için `get` ve `set` fonksiyonlarına sahip bir nesne de alabilir.

- **Tür**

  ```ts
  // salt okunur
  function computed<T>(
    getter: (oldValue: T | undefined) => T,
    // aşağıdaki "Hesaplanmış Hata Ayıklama" bağlantısına bakın
    debuggerOptions?: DebuggerOptions
  ): Readonly<Ref<Readonly<T>>>

  // yazılabilir
  function computed<T>(
    options: {
      get: (oldValue: T | undefined) => T
      set: (value: T) => void
    },
    debuggerOptions?: DebuggerOptions
  ): Ref<T>
  ```

- **Örnek**

  Salt okunur hesaplanmış bir ref oluşturma:

  ```js
  const count = ref(1)
  const plusOne = computed(() => count.value + 1)

  console.log(plusOne.value) // 2

  plusOne.value++ // hata
  ```

  Yazılabilir hesaplanmış bir ref oluşturma:

  ```js
  const count = ref(1)
  const plusOne = computed({
    get: () => count.value + 1,
    set: (val) => {
      count.value = val - 1
    }
  })

  plusOne.value = 1
  console.log(count.value) // 0
  ```

  Hata ayıklama:

  ```js
  const plusOne = computed(() => count.value + 1, {
    onTrack(e) {
      debugger
    },
    onTrigger(e) {
      debugger
    }
  })
  ```

- **Ayrıca bakınız**
  - [Rehber - Hesaplanan Özellikler](/guide/essentials/computed)
  - [Rehber - Hesaplanan Hata Ayıklama](/guide/extras/reactivity-in-depth#computed-debugging)
  - [Rehber - `computed()` Türlendirme](/guide/typescript/composition-api#typing-computed) <sup class="vt-badge ts" />
  - [Rehber - Performans - Hesaplanan Kararlılık](/guide/best-practices/performance#computed-stability)

## reactive() {#reactive}

Nesnenin reaktif bir proxy'sini döndürür.

- **Tür**

  ```ts
  function reactive<T extends object>(target: T): UnwrapNestedRefs<T>
  ```

- **Detaylar**

  Reaktif dönüştürme "derindir": tüm iç içe özellikler etkilenir. Reaktif bir nesne aynı zamanda reaktifliği korurken herhangi bir [ref](#ref) olan özellikleri derinlemesine sarar.

  Ref'in, reaktif bir dizinin veya `Map` gibi yerel bir koleksiyon türünün bir öğesi olarak erişildiğinde ref sarma işleminin yapılmadığı da belirtilmelidir.

  Derin dönüştürmeden kaçınmak ve reaktiviteyi yalnızca kök düzeyde tutmak için bunun yerine [shallowReactive()](./reactivity-advanced#shallowreactive) kullanın.

  Döndürülen nesne ve iç içe geçmiş nesneleri, orijinal nesnelerle eşit **olmayan** [ES Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) ile sarılır. Yalnızca reaktif proxy ile çalışmanız ve orijinal nesneye güvenmekten kaçınmanız önerilir.

- **Örnek**

  Reaktif bir nesne oluşturma:

  ```js
  const obj = reactive({ count: 0 })
  obj.count++
  ```

  Ref sarma:

  ```ts
  const count = ref(1)
  const obj = reactive({ count })

  // ref sarılacaktır
  console.log(obj.count === count.value) // true

  // `obj.count` değerini güncelleyecektir
  count.value++
  console.log(count.value) // 2
  console.log(obj.count) // 2

  // ayrıca `count` ref'ini de güncelleyecektir
  obj.count++
  console.log(obj.count) // 3
  console.log(count.value) // 3
  ```

  Ref'lerin dizi veya koleksiyon öğeleri olarak erişildiğinde **sarılmadığını** unutmayın:

  ```js
  const books = reactive([ref('Vue 3 Kılavuzu')])
  // burada .value gerekli
  console.log(books[0].value)

  const map = reactive(new Map([['count', ref(0)]]))
  // burada .value gerekli
  console.log(map.get('count').value)
  ```

  `reactive` özelliğine bir [ref](#ref) atarken, bu ref de otomatik olarak sarılacaktır:

  ```ts
  const count = ref(1)
  const obj = reactive({})

  obj.count = count

  console.log(obj.count) // 1
  console.log(obj.count === count.value) // true
  ```

- **Ayrıca bakınız**
  - [Rehber - Reaktivite Temelleri](/guide/essentials/reactivity-fundamentals)
  - [Rehber - `reactive()` Türlendirme](/guide/typescript/composition-api#typing-reactive) <sup class="vt-badge ts" />

## readonly() {#readonly}

Bir nesne (reaktif veya düz) veya bir [ref](#ref) alır ve orijinaline salt okunur bir proxy döndürür.

- **Tür**

  ```ts
  function readonly<T extends object>(
    target: T
  ): DeepReadonly<UnwrapNestedRefs<T>>
  ```

- **Detaylar**

  Salt okunur bir proxy derindir: erişilen herhangi bir iç içe geçmiş özellik de salt okunur olacaktır. Ayrıca, sarılan değerler de salt okunur hale getirilecek şekilde `reactive()` ile aynı ref sarma davranışına sahiptir.

  Derin dönüştürmeden kaçınmak için bunun yerine [shallowReadonly()](./reactivity-advanced#shallowreadonly) kullanın.

- **Örnek**

  ```js
  const original = reactive({ count: 0 })

  const copy = readonly(original)

  watchEffect(() => {
    // reaktivite takibi için çalışır
    console.log(copy.count)
  })

  // orijinali değiştirmek, kopyaya dayanan izleyicileri tetikleyecektir
  original.count++

  // kopyayı değiştirmek başarısız olur ve bir uyarıya neden olur
  copy.count++ // uyarı!
  ```

## watchEffect() {#watcheffect}

Bağımlılıklarını reaktif olarak izlerken bir fonksiyonu hemen çalıştırır ve bağımlılıklar değiştiğinde tekrar çalıştırır.

- **Tür**

  ```ts
  function watchEffect(
    effect: (onCleanup: OnCleanup) => void,
    options?: WatchEffectOptions
  ): WatchHandle

  type OnCleanup = (cleanupFn: () => void) => void

  interface WatchEffectOptions {
    flush?: 'pre' | 'post' | 'sync' // varsayılan: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }

  interface WatchHandle {
    (): void // çağrılabilir, `stop` ile aynı
    pause: () => void
    resume: () => void
    stop: () => void
  }
  ```

- **Detaylar**

  İlk argüman, çalıştırılacak efekt fonksiyonudur. Efekt fonksiyonu, bir temizleme geri çağrısı kaydetmek için kullanılabilecek bir fonksiyon alır. Temizleme geri çağrısı, efektin bir sonraki çalıştırılmasından hemen önce çağrılacak ve geçersiz kılınan yan etkileri, örneğin bekleyen bir asenkron isteği temizlemek için kullanılabilir (aşağıdaki örneğe bakın).

  İkinci argüman, efektin akış zamanlamasını ayarlamak veya efektin bağımlılıklarında hata ayıklamak için kullanılabilecek isteğe bağlı bir seçenekler nesnesidir.

  Varsayılan olarak, izleyiciler bileşen render edilmeden hemen önce çalışır. `flush: 'post'`'u ayarlamak, izleyiciyi bileşen render edilene kadar erteler. Daha fazla bilgi için [Geri Çağrı Akış Zamanlaması](/guide/essentials/watchers#callback-flush-timing) bölümüne bakın. Nadir durumlarda, bir önbelleği geçersiz kılmak gibi, reaktif bir bağımlılık değiştiğinde bir izleyiciyi hemen tetiklemek gerekebilir. Bu, `flush: 'sync'` kullanılarak elde edilebilir. Ancak, aynı anda birden fazla özellik güncelleniyorsa performans ve veri tutarlılığı sorunlarına yol açabileceğinden, bu ayar dikkatli kullanılmalıdır.

  Dönüş değeri, efektin tekrar çalışmasını durdurmak için çağrılabilen bir işleyici fonksiyonudur.

- **Örnek**

  ```js
  const count = ref(0)

  watchEffect(() => console.log(count.value))
  // -> 0 yazar

  count.value++
  // -> 1 yazar
  ```

  İzleyiciyi durdurma:

  ```js
  const stop = watchEffect(() => {})

  // izleyiciye artık ihtiyaç duyulmadığında:
  stop()
  ```

  İzleyiciyi duraklatma/devam ettirme: <sup class="vt-badge" data-text="3.5+" />

  ```js
  const { stop, pause, resume } = watchEffect(() => {})

  // izleyiciyi geçici olarak duraklat
  pause()

  // daha sonra devam ettir
  resume()

  // durdur
  stop()
  ```

  Yan etki temizleme:

  ```js
  watchEffect(async (onCleanup) => {
    const { response, cancel } = doAsyncWork(newId)
    // `id` değişirse `cancel` çağrılır ve henüz tamamlanmamışsa önceki istek iptal edilir
    onCleanup(cancel)
    data.value = await response
  })
  ```

  3.5+'da yan etki temizleme:

  ```js
  import { onWatcherCleanup } from 'vue'

  watchEffect(async () => {
    const { response, cancel } = doAsyncWork(newId)
    // `id` değişirse `cancel` çağrılır ve henüz tamamlanmamışsa önceki istek iptal edilir
    onWatcherCleanup(cancel)
    data.value = await response
  })
  ```

  Seçenekler:

  ```js
  watchEffect(() => {}, {
    flush: 'post',
    onTrack(e) {
      debugger
    },
    onTrigger(e) {
      debugger
    }
  })
  ```

- **Ayrıca bakınız**
  - [Rehber - İzleyiciler](/guide/essentials/watchers#watcheffect)
  - [Rehber - İzleyici Hata Ayıklama](/guide/extras/reactivity-in-depth#watcher-debugging)

## watchPostEffect() {#watchposteffect}

`flush: 'post'` seçeneğiyle [`watchEffect()`](#watcheffect) takma adı.

## watchSyncEffect() {#watchsynceffect}

`flush: 'sync'` seçeneğiyle [`watchEffect()`](#watcheffect) takma adı.

## watch() {#watch}

Bir veya daha fazla reaktif veri kaynağını izler ve kaynaklar değiştiğinde bir geri çağrı fonksiyonunu çağırır.

- **Tür**

  ```ts
  // tek kaynağı izleme
  function watch<T>(
    source: WatchSource<T>,
    callback: WatchCallback<T>,
    options?: WatchOptions
  ): WatchHandle

  // birden çok kaynağı izleme
  function watch<T>(
    sources: WatchSource<T>[],
    callback: WatchCallback<T[]>,
    options?: WatchOptions
  ): WatchHandle

  type WatchCallback<T> = (
    value: T,
    oldValue: T,
    onCleanup: (cleanupFn: () => void) => void
  ) => void

  type WatchSource<T> =
    | Ref<T> // ref
    | (() => T) // getter
    | (T extends object ? T : never) // reaktif nesne

  interface WatchOptions extends WatchEffectOptions {
    immediate?: boolean // varsayılan: false
    deep?: boolean | number // varsayılan: false
    flush?: 'pre' | 'post' | 'sync' // varsayılan: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
    once?: boolean // varsayılan: false (3.4+)
  }

  interface WatchHandle {
    (): void // çağrılabilir, `stop` ile aynı
    pause: () => void
    resume: () => void
    stop: () => void
  }
  ```

  > Türler, okunabilirlik için basitleştirilmiştir.

- **Detaylar**

  `watch()`, varsayılan olarak tembeldir; yani geri çağrı yalnızca izlenen kaynak değiştiğinde çağrılır.

  İlk argüman, izleyicinin **kaynağıdır**. Kaynak aşağıdakilerden biri olabilir:

  - Bir değer döndüren bir getter fonksiyonu
  - Bir ref
  - Reaktif bir nesne
  - ...veya yukarıdakilerin bir dizisi.

  İkinci argüman, kaynak değiştiğinde çağrılacak geri çağrıdır. Geri çağrı, üç argüman alır: yeni değer, eski değer ve bir yan etki temizleme geri çağrısı kaydetmek için bir fonksiyon. Temizleme geri çağrısı, efektin bir sonraki çalıştırılmasından hemen önce çağrılacak ve geçersiz kılınan yan etkileri, örneğin bekleyen bir asenkron isteği temizlemek için kullanılabilir.

  Birden çok kaynak izlenirken, geri çağrı, kaynak dizisine karşılık gelen yeni / eski değerleri içeren iki dizi alır.

  İsteğe bağlı üçüncü argüman, aşağıdaki seçenekleri destekleyen bir seçenekler nesnesidir:

  - **`immediate`**: izleyici oluşturulduğunda geri çağrıyı hemen tetikler. İlk çağrıda eski değer `undefined` olacaktır.
  - **`deep`**: bir nesne ise, geri çağrının derin mutasyonlarda tetiklenmesi için kaynağın derin geçişini zorlar. 3.5+'da bu, maksimum geçiş derinliğini gösteren bir sayı da olabilir. [Derin İzleyiciler](/guide/essentials/watchers#deep-watchers) bölümüne bakın.
  - **`flush`**: geri çağrının akış zamanlamasını ayarlayın. [Geri Çağrı Akış Zamanlaması](/guide/essentials/watchers#callback-flush-timing) ve [`watchEffect()`](/api/reactivity-core#watcheffect) bölümüne bakın.
  - **`onTrack / onTrigger`**: izleyicinin bağımlılıklarında hata ayıklayın. [İzleyici Hata Ayıklama](/guide/extras/reactivity-in-depth#watcher-debugging) bölümüne bakın.
  - **`once`**: (3.4+) geri çağrıyı yalnızca bir kez çalıştırın. İzleyici, ilk geri çağrı çalıştırıldıktan sonra otomatik olarak durdurulur.

  [`watchEffect()`](#watcheffect) ile karşılaştırıldığında `watch()`, şunları yapmamızı sağlar:

  - Yan etkiyi tembelce gerçekleştirin;
  - Hangi durumun izleyicinin tekrar çalışmasını tetiklemesi gerektiği konusunda daha spesifik olun;
  - İzlenen durumun hem önceki hem de geçerli değerine erişin.

- **Örnek**

  Bir getter'ı izleme:

  ```js
  const state = reactive({ count: 0 })
  watch(
    () => state.count,
    (count, prevCount) => {
      /* ... */
    }
  )
  ```

  Bir ref'i izleme:

  ```js
  const count = ref(0)
  watch(count, (count, prevCount) => {
    /* ... */
  })
  ```

  Birden çok kaynak izlenirken, geri çağrı, kaynak dizisine karşılık gelen yeni / eski değerleri içeren diziler alır:

  ```js
  watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
    /* ... */
  })
  ```

  Bir getter kaynağı kullanıldığında, izleyici yalnızca getter'ın dönüş değeri değiştiyse tetiklenir. Geri çağrının derin mutasyonlarda bile tetiklenmesini istiyorsanız, izleyiciyi `{ deep: true }` ile açıkça derin moda zorlamanız gerekir. Derin modda, geri çağrı derin bir mutasyon tarafından tetiklendiyse yeni değer ile eski değerin aynı nesne olacağına dikkat edin:

  ```js
  const state = reactive({ count: 0 })
  watch(
    () => state,
    (newValue, oldValue) => {
      // newValue === oldValue
    },
    { deep: true }
  )
  ```

  Doğrudan reaktif bir nesne izlenirken, izleyici otomatik olarak derin moddadır:

  ```js
  const state = reactive({ count: 0 })
  watch(state, () => {
    /* state'te derin mutasyonda tetikler */
  })
  ```

  `watch()`, [`watchEffect()`](#watcheffect) ile aynı akış zamanlamasını ve hata ayıklama seçeneklerini paylaşır:

  ```js
  watch(source, callback, {
    flush: 'post',
    onTrack(e) {
      debugger
    },
    onTrigger(e) {
      debugger
    }
  })
  ```

  İzleyiciyi durdurma:

  ```js
  const stop = watch(source, callback)

  // izleyiciye artık ihtiyaç duyulmadığında:
  stop()
  ```

  İzleyiciyi duraklatma/devam ettirme: <sup class="vt-badge" data-text="3.5+" />

  ```js
  const { stop, pause, resume } = watch(() => {})

  // izleyiciyi geçici olarak duraklat
  pause()

  // daha sonra devam ettir
  resume()

  // durdur
  stop()
  ```

  Yan etki temizleme:

  ```js
  watch(id, async (newId, oldId, onCleanup) => {
    const { response, cancel } = doAsyncWork(newId)
    // `id` değişirse `cancel` çağrılır ve henüz tamamlanmamışsa önceki istek iptal edilir
    onCleanup(cancel)
    data.value = await response
  })
  ```

  3.5+'da yan etki temizleme:

  ```js
  import { onWatcherCleanup } from 'vue'

  watch(id, async (newId) => {
    const { response, cancel } = doAsyncWork(newId)
    onWatcherCleanup(cancel)
    data.value = await response
  })
  ```

- **Ayrıca bakınız**

  - [Rehber - İzleyiciler](/guide/essentials/watchers)
  - [Rehber - İzleyici Hata Ayıklama](/guide/extras/reactivity-in-depth#watcher-debugging)

## onWatcherCleanup() <sup class="vt-badge" data-text="3.5+" /> {#onwatchercleanup}

Geçerli izleyici yeniden çalıştırılmak üzereyken yürütülecek bir temizleme fonksiyonu kaydedin. Yalnızca `watchEffect` efekt fonksiyonunun veya `watch` geri çağrı fonksiyonunun eşzamanlı yürütülmesi sırasında çağrılabilir (yani, asenkron bir fonksiyonda bir `await` ifadesinden sonra çağrılamaz).

- **Tür**

  ```ts
  function onWatcherCleanup(
    cleanupFn: () => void,
    failSilently?: boolean
  ): void
  ```

- **Örnek**

  ```ts
  import { watch, onWatcherCleanup } from 'vue'

  watch(id, (newId) => {
    const { response, cancel } = doAsyncWork(newId)
    // `id` değişirse `cancel` çağrılır ve henüz tamamlanmamışsa önceki istek iptal edilir
    onWatcherCleanup(cancel)
  })
  ```
