# Reaktivite API'si: Yardımcı Programlar {#reactivity-api-utilities}

## isRef() {#isref}

Bir değerin bir ref nesnesi olup olmadığını kontrol eder.

- **Tür**

  ```ts
  function isRef<T>(r: Ref<T> | unknown): r is Ref<T>
  ```

  Dönüş türünün bir [tür öngörüsü](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates) olduğuna dikkat edin, bu, `isRef`'in bir tür koruyucusu olarak kullanılabileceği anlamına gelir:

  ```ts
  let foo: unknown
  if (isRef(foo)) {
    // foo'nun türü Ref<unknown> olarak daraltılmıştır
    foo.value
  }
  ```

## unref() {#unref}

Argüman bir ref ise iç değeri döndürür, aksi takdirde argümanın kendisini döndürür. Bu, `val = isRef(val) ? val.value : val` için bir şeker fonksiyonudur.

- **Tür**

  ```ts
  function unref<T>(ref: T | Ref<T>): T
  ```

- **Örnek**

  ```ts
  function useFoo(x: number | Ref<number>) {
    const unwrapped = unref(x)
    // unwrapped'in artık sayı olduğu garanti edilir
  }
  ```

## toRef() {#toref}

Değerleri / ref'leri / getter'ları ref'lere normalleştirmek için kullanılabilir (3.3+).

Ayrıca, bir kaynak reaktif nesnesi üzerindeki bir özellik için bir ref oluşturmak için de kullanılabilir. Oluşturulan ref, kaynak özelliğiyle senkronize edilir: kaynak özelliğini değiştirmek ref'i günceller ve bunun tersi de geçerlidir.

- **Tür**

  ```ts
  // normalleştirme imzası (3.3+)
  function toRef<T>(
    value: T
  ): T extends () => infer R
    ? Readonly<Ref<R>>
    : T extends Ref
    ? T
    : Ref<UnwrapRef<T>>

  // nesne özelliği imzası
  function toRef<T extends object, K extends keyof T>(
    object: T,
    key: K,
    defaultValue?: T[K]
  ): ToRef<T[K]>

  type ToRef<T> = T extends Ref ? T : Ref<T>
  ```

- **Örnek**

  Normalleştirme imzası (3.3+):

  ```js
  // mevcut ref'leri olduğu gibi döndürür
  toRef(existingRef)

  // .value erişiminde getter'ı çağıran salt okunur bir ref oluşturur
  toRef(() => props.foo)

  // işlev olmayan değerlerden normal ref'ler oluşturur
  // ref(1)'e eşdeğer
  toRef(1)
  ```

  Nesne özelliği imzası:

  ```js
  const state = reactive({
    foo: 1,
    bar: 2
  })

  // orijinal özellikle senkronize olan iki yönlü bir ref
  const fooRef = toRef(state, 'foo')

  // ref'i değiştirmek orijinali günceller
  fooRef.value++
  console.log(state.foo) // 2

  // orijinali değiştirmek de ref'i günceller
  state.foo++
  console.log(fooRef.value) // 3
  ```

  Bunun şundan farklı olduğuna dikkat edin:

  ```js
  const fooRef = ref(state.foo)
  ```

  Yukarıdaki ref, `ref()` düz bir sayı değeri aldığı için `state.foo` ile senkronize **edilmez**.

  `toRef()`, bir prop'un ref'ini bir composable fonksiyona geçirmek istediğinizde kullanışlıdır:

  ```vue
  <script setup>
  import { toRef } from 'vue'

  const props = defineProps(/* ... */)

  // `props.foo`'yu bir ref'e dönüştür, ardından
  // bir composable'a geçir
  useSomeFeature(toRef(props, 'foo'))

  // getter sözdizimi - 3.3+'da önerilir
  useSomeFeature(toRef(() => props.foo))
  </script>
  ```

  `toRef`, bileşen prop'larıyla kullanıldığında, prop'ları değiştirme konusundaki olağan kısıtlamalar hala geçerlidir. Ref'e yeni bir değer atamaya çalışmak, prop'u doğrudan değiştirmeye çalışmakla eşdeğerdir ve izin verilmez. Bu senaryoda, bunun yerine `get` ve `set` ile [`computed`](./reactivity-core#computed)'ı kullanmayı düşünebilirsiniz. Daha fazla bilgi için [bileşenlerle `v-model` kullanımı](/guide/components/v-model) rehberine bakın.

  Nesne özelliği imzasını kullanırken, `toRef()`, kaynak özelliği şu anda mevcut olmasa bile kullanılabilir bir ref döndürür. Bu, [`toRefs`](#torefs) tarafından alınmayacak isteğe bağlı özelliklerle çalışmayı mümkün kılar.

## toValue() {#tovalue}

- Yalnızca 3.3+ sürümünde desteklenir

Değerleri / ref'leri / getter'ları değerlere normalleştirir. Bu, bir getter'ı da normalleştirmesi dışında [unref()](#unref)'e benzer. Argüman bir getter ise, çağrılır ve dönüş değeri döndürülür.

Bu, değer, ref veya getter olabilen bir argümanı normalleştirmek için [Composable'larda](/guide/reusability/composables.html) kullanılabilir.

- **Tür**

  ```ts
  function toValue<T>(source: T | Ref<T> | (() => T)): T
  ```

- **Örnek**

  ```js
  toValue(1) //       --> 1
  toValue(ref(1)) //  --> 1
  toValue(() => 1) // --> 1
  ```

  Composable'larda argümanları normalleştirme:

  ```ts
  import type { MaybeRefOrGetter } from 'vue'

  function useFeature(id: MaybeRefOrGetter<number>) {
    watch(() => toValue(id), id => {
      // id değişikliklerine tepki ver
    })
  }

  // bu composable aşağıdakilerden herhangi birini destekler:
  useFeature(1)
  useFeature(ref(1))
  useFeature(() => 1)
  ```

## toRefs() {#torefs}

Reaktif bir nesneyi, sonuçtaki nesnenin her özelliğinin orijinal nesnenin karşılık gelen özelliğine işaret eden bir ref olduğu düz bir nesneye dönüştürür. Her bir ref, [`toRef()`](#toref) kullanılarak oluşturulur.

- **Tür**

  ```ts
  function toRefs<T extends object>(
    object: T
  ): {
    [K in keyof T]: ToRef<T[K]>
  }

  type ToRef = T extends Ref ? T : Ref<T>
  ```

- **Örnek**

  ```js
  const state = reactive({
    foo: 1,
    bar: 2
  })

  const stateAsRefs = toRefs(state)
  /*
  stateAsRefs'in Türü: {
    foo: Ref<number>,
    bar: Ref<number>
  }
  */

  // Ref ve orijinal özellik "bağlantılı"
  state.foo++
  console.log(stateAsRefs.foo.value) // 2

  stateAsRefs.foo.value++
  console.log(state.foo) // 3
  ```

  `toRefs`, bir composable fonksiyonundan reaktif bir nesne döndürürken, tüketen bileşenin, reaktiviteyi kaybetmeden döndürülen nesnenin yapısını bozabilmesi/yayabilmesi için kullanışlıdır:

  ```js
  function useFeatureX() {
    const state = reactive({
      foo: 1,
      bar: 2
    })

    // ...durum üzerinde çalışan mantık

    // döndürürken ref'lere dönüştür
    return toRefs(state)
  }

  // reaktivite kaybetmeden yapısı bozulabilir
  const { foo, bar } = useFeatureX()
  ```

  `toRefs`, yalnızca kaynak nesnesinde çağrı zamanında numaralandırılabilir olan özellikler için ref'ler oluşturacaktır. Henüz mevcut olmayabilecek bir özellik için bir ref oluşturmak için, bunun yerine [`toRef`](#toref) kullanın.

## isProxy() {#isproxy}

Bir nesnenin [`reactive()`](./reactivity-core#reactive), [`readonly()`](./reactivity-core#readonly), [`shallowReactive()`](./reactivity-advanced#shallowreactive) veya [`shallowReadonly()`](./reactivity-advanced#shallowreadonly) tarafından oluşturulan bir proxy olup olmadığını kontrol eder.

- **Tür**

  ```ts
  function isProxy(value: any): boolean
  ```

## isReactive() {#isreactive}

Bir nesnenin [`reactive()`](./reactivity-core#reactive) veya [`shallowReactive()`](./reactivity-advanced#shallowreactive) tarafından oluşturulan bir proxy olup olmadığını kontrol eder.

- **Tür**

  ```ts
  function isReactive(value: unknown): boolean
  ```

## isReadonly() {#isreadonly}

Geçirilen değerin salt okunur bir nesne olup olmadığını kontrol eder. Salt okunur bir nesnenin özellikleri değişebilir, ancak geçirilen nesne aracılığıyla doğrudan atanamazlar.

[`readonly()`](./reactivity-core#readonly) ve [`shallowReadonly()`](./reactivity-advanced#shallowreadonly) tarafından oluşturulan proxy'lerin her ikisi de salt okunur olarak kabul edilir ve aynı zamanda bir `set` fonksiyonu olmayan [`computed()`](./reactivity-core#computed) ref'i de öyledir.

- **Tür**

  ```ts
  function isReadonly(value: unknown): boolean
  ```
