# Reaktivite API'si: Gelişmiş {#reactivity-api-advanced}

## shallowRef() {#shallowref}

[`ref()`](./reactivity-core#ref)'in sığ versiyonu.

- **Tür**

  ```ts
  function shallowRef<T>(value: T): ShallowRef<T>

  interface ShallowRef<T> {
    value: T
  }
  ```

- **Detaylar**

  `ref()`'in aksine, sığ bir ref'in iç değeri olduğu gibi depolanır ve gösterilir ve derinlemesine reaktif hale getirilmez. Yalnızca `.value` erişimi reaktiftir.

  `shallowRef()`, genellikle büyük veri yapılarının performans optimizasyonları veya harici durum yönetimi sistemleriyle entegrasyon için kullanılır.

- **Örnek**

  ```js
  const state = shallowRef({ count: 1 })

  // değişikliği tetiklemez
  state.value.count = 2

  // değişikliği tetikler
  state.value = { count: 2 }
  ```

- **Ayrıca bakınız**
  - [Rehber - Büyük Değişmez Yapılar İçin Reaktivite Yükünü Azaltma](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
  - [Rehber - Harici Durum Sistemleriyle Entegrasyon](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

## triggerRef() {#triggerref}

Bir [sığ ref'e](#shallowref) bağlı efektleri zorla tetikler. Bu genellikle sığ bir ref'in iç değerinde derin mutasyonlar yaptıktan sonra kullanılır.

- **Tür**

  ```ts
  function triggerRef(ref: ShallowRef): void
  ```

- **Örnek**

  ```js
  const shallow = shallowRef({
    greet: 'Merhaba, dünya'
  })

  // İlk çalıştırmada bir kez "Merhaba, dünya" yazar
  watchEffect(() => {
    console.log(shallow.value.greet)
  })

  // Bu, ref sığ olduğu için efekti tetiklemeyecektir
  shallow.value.greet = 'Merhaba, evren'

  // "Merhaba, evren" yazar
  triggerRef(shallow)
  ```

## customRef() {#customref}

Bağımlılık takibi ve güncelleme tetiklenmesi üzerinde açık kontrol ile özelleştirilmiş bir ref oluşturur.

- **Tür**

  ```ts
  function customRef<T>(factory: CustomRefFactory<T>): Ref<T>

  type CustomRefFactory<T> = (
    track: () => void,
    trigger: () => void
  ) => {
    get: () => T
    set: (value: T) => void
  }
  ```

- **Detaylar**

  `customRef()`, argüman olarak `track` ve `trigger` fonksiyonlarını alan ve `get` ve `set` metotlarına sahip bir nesne döndürmesi gereken bir fabrika fonksiyonu bekler.

  Genel olarak, `track()` `get()` içinde ve `trigger()` `set()` içinde çağrılmalıdır. Ancak, ne zaman çağrılması gerektiği veya hiç çağrılıp çağrılmaması gerektiği konusunda tam kontrole sahipsiniz.

- **Örnek**

  En son set çağrısından sonra belirli bir zaman aşımından sonra değeri yalnızca güncelleyen debounced bir ref oluşturma:

  ```js
  import { customRef } from 'vue'

  export function useDebouncedRef(value, delay = 200) {
    let timeout
    return customRef((track, trigger) => {
      return {
        get() {
          track()
          return value
        },
        set(newValue) {
          clearTimeout(timeout)
          timeout = setTimeout(() => {
            value = newValue
            trigger()
          }, delay)
        }
      }
    })
  }
  ```

  Bileşende kullanım:

  ```vue
  <script setup>
  import { useDebouncedRef } from './debouncedRef'
  const text = useDebouncedRef('merhaba')
  </script>

  <template>
    <input v-model="text" />
  </template>
  ```

  [Playground'da deneyin](https://play.vuejs.org/#eNplUkFugzAQ/MqKC1SiIekxIpEq9QVV1BMXCguhBdsyaxqE/PcuGAhNfYGd3Z0ZDwzeq1K7zqB39OI205UiaJGMOieiapTUBAOYFt/wUxqRYf6OBVgotGzA30X5Bt59tX4iMilaAsIbwelxMfCvWNfSD+Gw3++fEhFHTpLFuCBsVJ0ScgUQjw6Az+VatY5PiroHo3IeaeHANlkrh7Qg1NBL43cILUmlMAfqVSXK40QUOSYmHAZHZO0KVkIZgu65kTnWp8Qb+4kHEXfjaDXkhd7DTTmuNZ7MsGyzDYbz5CgSgbdppOBFqqT4l0eX1gZDYOm057heOBQYRl81coZVg9LQWGr+IlrchYKAdJp9h0C6KkvUT3A6u8V1dq4ASqRgZnVnWg04/QWYNyYzC2rD5Y3/hkDgz8fY/cOT1ZjqizMZzGY3rDPC12KGZYyd3J26M8ny1KKx7c3X25q1c1wrZN3L9LCMWs/+AmeG6xI=)

  :::warning Dikkatli kullanın
  `customRef` kullanırken, özellikle getter her çalıştırıldığında yeni nesne veri türleri oluştururken, getter'ının dönüş değeri konusunda dikkatli olmalıyız. Bu, böyle bir `customRef`'in bir prop olarak geçirildiği üst ve alt bileşenler arasındaki ilişkiyi etkiler.

  Üst bileşenin render fonksiyonu, farklı bir reaktif durumdaki değişiklikler tarafından tetiklenebilir. Yeniden render etme sırasında, `customRef`'imizin değeri yeniden değerlendirilir ve alt bileşene bir prop olarak yeni bir nesne veri türü döndürülür. Bu prop, alt bileşendeki son değeriyle karşılaştırılır ve farklı oldukları için `customRef`'in reaktif bağımlılıkları alt bileşende tetiklenir. Bu arada, üst bileşendeki reaktif bağımlılıklar çalışmaz çünkü `customRef`'in ayarlayıcısı çağrılmamıştır ve bağımlılıkları sonuç olarak tetiklenmemiştir.

  [Bunu Playground'da görün](https://play.vuejs.org/#eNqFVEtP3DAQ/itTS9Vm1ZCt1J6WBZUiDvTQIsoNcwiOkzU4tmU7+9Aq/71jO1mCWuhlN/PyfPP45kAujCk2HSdLsnLMCuPBcd+Zc6pEa7T1cADWOa/bW17nYMPPtvRsDT3UVrcww+DZ0flStybpKSkWQQqPU0IVVUwr58FYvdvDWXgpu6ek1pqSHL0fS0vJw/z0xbN1jUPHY/Ys87Zkzzl4K5qG2zmcnUN2oAqg4T6bQ/wENKNXNk+CxWKsSlmLTSk7XlhedYxnWclYDiK+MkQCoK4wnVtnIiBJuuEJNA2qPof7hzkEoc8DXgg9yzYTBBFgNr4xyY4FbaK2p6qfI0iqFgtgulOe27HyQRy69Dk1JXY9C03JIeQ6wg4xWvJCqFpnlNytOcyC2wzYulQNr0Ao+Mhw0KnTTEttl/CIaIJiMz8NGBHFtYetVrPwa58/IL48Zag4N0ssquNYLYBoW16J0vOkC3VQtVqk7cG9QcHz1kj0QAlgVYkNMFk6d0bJ1pbGYKUkmtD42HmvFfi94WhOEiXwjUnBnlEz9OLTJwy5qCo44D4O7en71SIFjI/F9VuG4jEy/GHQKq5hQrJAKOc4uNVighBF5/cygS0GgOMoK+HQb7+EWvLdMM7weVIJy5kXWi0Rj+xaNRhLKRp1IvB9hxYegA6WJ1xkUe9PcF4e9a+suA3YwYiC5MQ79KlFUzw5rZCZEUtoRWuE5PaXCXmxtuWIkpJSSr39EXXHQcWYNWfP/9A/uV3QUXJjueN2E1ZhtPnSIqGS+er3T77D76Ox1VUn0fsd4y3HfewCxuT2vVMVwp74RbTX8WQI1dy5qx12xI1Fpa1K5AreeEHCCN8q/QXul+LrSC3s4nh93jltkVPDIYt5KJkcIKStCReo4rVQ/CZI6dyEzToCCJu7hAtry/1QH/qXncQB400KJwqPxZHxEyona0xS/E3rt1m9Ld1rZl+uhaxecRtP3EjtgddCyimtXyj9H/Ii3eId7uOGTkyk/wOEbQ9h)

  :::

## shallowReactive() {#shallowreactive}

[`reactive()`](./reactivity-core#reactive)'in sığ versiyonu.

- **Tür**

  ```ts
  function shallowReactive<T extends object>(target: T): T
  ```

- **Detaylar**

  `reactive()`'den farklı olarak, derin dönüştürme yoktur: yalnızca sığ bir reaktif nesne için kök düzeyindeki özellikler reaktiftir. Özellik değerleri olduğu gibi depolanır ve gösterilir; bu aynı zamanda ref değerlerine sahip özelliklerin otomatik olarak sarılmayacağı anlamına gelir.

  :::warning Dikkatli Kullanın
  Sığ veri yapıları yalnızca bir bileşendeki kök düzeyindeki durum için kullanılmalıdır. Anlaşılması ve hata ayıklaması zor olabilen tutarsız reaktivite davranışına sahip bir ağaç oluşturduğu için onu derin bir reaktif nesnenin içine yerleştirmekten kaçının.
  :::

- **Örnek**

  ```js
  const state = shallowReactive({
    foo: 1,
    nested: {
      bar: 2
    }
  })

  // durumun kendi özelliklerini değiştirmek reaktiftir
  state.foo++

  // ...ancak iç içe geçmiş nesneleri dönüştürmez
  isReactive(state.nested) // false

  // reaktif DEĞİL
  state.nested.bar++
  ```

## shallowReadonly() {#shallowreadonly}

[`readonly()`](./reactivity-core#readonly)'in sığ versiyonu.

- **Tür**

  ```ts
  function shallowReadonly<T extends object>(target: T): Readonly<T>
  ```

- **Detaylar**

  `readonly()`'den farklı olarak, derin dönüştürme yoktur: yalnızca kök düzeyindeki özellikler salt okunur hale getirilir. Özellik değerleri olduğu gibi depolanır ve gösterilir; bu aynı zamanda ref değerlerine sahip özelliklerin otomatik olarak sarılmayacağı anlamına gelir.

  :::warning Dikkatli Kullanın
  Sığ veri yapıları yalnızca bir bileşendeki kök düzeyindeki durum için kullanılmalıdır. Anlaşılması ve hata ayıklaması zor olabilen tutarsız reaktivite davranışına sahip bir ağaç oluşturduğu için onu derin bir reaktif nesnenin içine yerleştirmekten kaçının.
  :::

- **Örnek**

  ```js
  const state = shallowReadonly({
    foo: 1,
    nested: {
      bar: 2
    }
  })

  // durumun kendi özelliklerini değiştirmek başarısız olur
  state.foo++

  // ...ancak iç içe geçmiş nesnelerde çalışır
  isReadonly(state.nested) // false

  // çalışır
  state.nested.bar++
  ```

## toRaw() {#toraw}

Vue tarafından oluşturulan bir proxy'nin ham, orijinal nesnesini döndürür.

- **Tür**

  ```ts
  function toRaw<T>(proxy: T): T
  ```

- **Detaylar**

  `toRaw()`, [`reactive()`](./reactivity-core#reactive), [`readonly()`](./reactivity-core#readonly), [`shallowReactive()`](#shallowreactive) veya [`shallowReadonly()`](#shallowreadonly) tarafından oluşturulan proxy'lerden orijinal nesneyi döndürebilir.

  Bu, proxy erişim/takip yükü olmadan geçici olarak okumak veya değişiklikleri tetiklemeden yazmak için kullanılabilen bir çıkış yoludur. Orijinal nesneye kalıcı bir referans tutulması **tavsiye edilmez**. Dikkatli kullanın.

- **Örnek**

  ```js
  const foo = {}
  const reactiveFoo = reactive(foo)

  console.log(toRaw(reactiveFoo) === foo) // true
  ```

## markRaw() {#markraw}

Bir nesneyi hiçbir zaman proxy'ye dönüştürülmeyecek şekilde işaretler. Nesnenin kendisini döndürür.

- **Tür**

  ```ts
  function markRaw<T extends object>(value: T): T
  ```

- **Örnek**

  ```js
  const foo = markRaw({})
  console.log(isReactive(reactive(foo))) // false

  // diğer reaktif nesnelerin içine iç içe yerleştirildiğinde de çalışır
  const bar = reactive({ foo })
  console.log(isReactive(bar.foo)) // false
  ```

  :::warning Dikkatli Kullanın
  `markRaw()` ve `shallowReactive()` gibi sığ API'ler, varsayılan derin reaktif/salt okunur dönüştürmeden seçmeli olarak çıkmanıza ve durum grafiğinize ham, proxy olmayan nesneleri yerleştirmenize olanak tanır. Çeşitli nedenlerle kullanılabilirler:

  - Bazı değerler basitçe reaktif hale getirilmemelidir, örneğin karmaşık bir 3. taraf sınıf örneği veya bir Vue bileşen nesnesi.

  - Proxy dönüştürmesini atlamak, değişmez veri kaynaklarıyla büyük listeler render ederken performans iyileştirmeleri sağlayabilir.

  Ham vazgeçme yalnızca kök düzeyde olduğundan, bu bir karmaşık API olarak kabul edilir. Bu nedenle, iç içe yerleştirilmiş, işaretlenmemiş ham bir nesneyi reaktif bir nesneye ayarlarsanız ve ardından ona tekrar erişirseniz, proxy'lenmiş sürümü geri alırsınız. Bu, **kimlik tehlikelerine** yol açabilir; yani, nesne kimliğine dayanan bir işlem gerçekleştirirken, aynı nesnenin hem ham hem de proxy'lenmiş sürümünü kullanabilirsiniz:

  ```js
  const foo = markRaw({
    nested: {}
  })

  const bar = reactive({
    // `foo` ham olarak işaretlenmiş olsa da, foo.nested işaretlenmemiştir.
    nested: foo.nested
  })

  console.log(foo.nested === bar.nested) // false
  ```

  Kimlik tehlikeleri genel olarak nadirdir. Ancak, kimlik tehlikelerinden güvenli bir şekilde kaçınırken bu API'leri düzgün bir şekilde kullanmak, reaktivite sisteminin nasıl çalıştığına dair sağlam bir anlayış gerektirir.

  :::

## effectScope() {#effectscope}

İçinde oluşturulan reaktif etkileri (yani, hesaplananlar ve izleyiciler) yakalayabilen bir efekt kapsamı nesnesi oluşturur, böylece bu efektler birlikte ortadan kaldırılabilir. Bu API'nin ayrıntılı kullanım durumları için lütfen ilgili [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0041-reactivity-effect-scope.md) bölümüne başvurun.

- **Tür**

  ```ts
  function effectScope(detached?: boolean): EffectScope

  interface EffectScope {
    run<T>(fn: () => T): T | undefined // kapsam etkin değilse tanımsız
    stop(): void
  }
  ```

- **Örnek**

  ```js
  const scope = effectScope()

  scope.run(() => {
    const doubled = computed(() => counter.value * 2)

    watch(doubled, () => console.log(doubled.value))

    watchEffect(() => console.log('Sayı:', doubled.value))
  })

  // kapsamdaki tüm efektleri ortadan kaldırmak için
  scope.stop()
  ```

## getCurrentScope() {#getcurrentscope}

Varsa geçerli aktif [efekt kapsamını](#effectscope) döndürür.

- **Tür**

  ```ts
  function getCurrentScope(): EffectScope | undefined
  ```

## onScopeDispose() {#onscopedispose}

Geçerli aktif [efekt kapsamına](#effectscope) bir ortadan kaldırma geri çağrısı kaydeder. İlişkili efekt kapsamı durdurulduğunda geri çağrı çağrılacaktır.

Bu metot, her Vue bileşeninin `setup()` fonksiyonu da bir efekt kapsamında çağrıldığından, yeniden kullanılabilir bileşim fonksiyonlarında `onUnmounted`'ın bileşenle bağlantılı olmayan bir alternatifi olarak kullanılabilir.

Aktif bir efekt kapsamı olmadan bu fonksiyon çağrılırsa bir uyarı verilir. 3.5+ sürümlerinde, ikinci argüman olarak `true` geçirilerek bu uyarı gizlenebilir.

- **Tür**

  ```ts
  function onScopeDispose(fn: () => void, failSilently?: boolean): void
  ```
