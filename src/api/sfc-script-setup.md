# `<script setup>` {#script-setup}

`<script setup>`, Tek Dosyalı Bileşenler (SFC'ler) içinde Composition API kullanmak için derleme zamanı sözdizimsel bir şekerdir. SFC'leri ve Composition API'yi birlikte kullanıyorsanız önerilen sözdizimidir. Normal `<script>` sözdizimine göre bir dizi avantaj sağlar:

- Daha az boilerplate ile daha özlü kod
- Pure TypeScript kullanarak prop'ları ve yayınlanan olayları bildirme olanağı
- Daha iyi çalışma zamanı performansı (şablon, ara proxy olmadan aynı kapsamda bir render fonksiyonuna derlenir)
- Daha iyi IDE tür çıkarımı performansı (dil sunucusunun koddan türleri çıkarması için daha az iş)

## Temel Sözdizimi {#basic-syntax}

Sözdizimine katılmak için `<script>` bloğuna `setup` özelliğini ekleyin:

```vue
<script setup>
console.log('merhaba script setup')
</script>
```

İçindeki kod, bileşenin `setup()` fonksiyonunun içeriği olarak derlenir. Bu, bileşen ilk içe aktarıldığında yalnızca bir kez yürütülen normal `<script>`'ten farklı olarak, `<script setup>` içindeki kodun **bileşenin bir örneği her oluşturulduğunda** yürütüleceği anlamına gelir.

### Üst düzey bağlamalar şablona gösterilir {#top-level-bindings-are-exposed-to-template}

`<script setup>` kullanırken, `<script setup>` içinde bildirilen herhangi bir üst düzey bağlama (değişkenler, fonksiyon bildirimleri ve içe aktarmalar dahil) doğrudan şablonda kullanılabilir:

```vue
<script setup>
// değişken
const msg = 'Merhaba!'

// fonksiyonlar
function log() {
  console.log(msg)
}
</script>

<template>
  <button @click="log">{{ msg }}</button>
</template>
```

İçe aktarmalar da aynı şekilde gösterilir. Bu, şablon ifadelerinde içe aktarılan bir yardımcı fonksiyonu, `methods` seçeneği aracılığıyla göstermek zorunda kalmadan doğrudan kullanabileceğiniz anlamına gelir:

```vue
<script setup>
import { capitalize } from './helpers'
</script>

<template>
  <div>{{ capitalize('hello') }}</div>
</template>
```

## Reaktivite {#reactivity}

Reaktif durumun, [Reaktivite API'leri](./reactivity-core) kullanılarak açıkça oluşturulması gerekir. `setup()` fonksiyonundan döndürülen değerlere benzer şekilde, ref'ler şablonlarda referans alındığında otomatik olarak sarılır:

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>
```

## Bileşenleri Kullanma {#using-components}

`<script setup>` kapsamındaki değerler, doğrudan özel bileşen etiket adları olarak da kullanılabilir:

```vue
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <MyComponent />
</template>
```

`MyComponent`'i bir değişken olarak referans aldığınızı düşünün. JSX kullandıysanız, zihinsel model burada benzerdir. Kebab-case eşdeğeri `<my-component>` da şablonda çalışır; ancak tutarlılık için PascalCase bileşen etiketleri şiddetle tavsiye edilir. Ayrıca, yerel özel öğelerden ayrılmasına da yardımcı olur.

### Dinamik Bileşenler {#dynamic-components}

Bileşenler, string anahtarları altında kaydedilmek yerine değişkenler olarak referans alındığından, `<script setup>` içinde dinamik bileşenler kullanırken dinamik `:is` bağlamasını kullanmalıyız:

```vue
<script setup>
import Foo from './Foo.vue'
import Bar from './Bar.vue'
</script>

<template>
  <component :is="Foo" />
  <component :is="someCondition ? Foo : Bar" />
</template>
```

Bileşenlerin bir üçlü ifadede nasıl değişken olarak kullanılabileceğine dikkat edin.

### Özyinelemeli Bileşenler {#recursive-components}

Bir SFC, dosya adı aracılığıyla dolaylı olarak kendisine başvurabilir. Örneğin, `FooBar.vue` adlı bir dosya, şablonunda kendisine `<FooBar/>` olarak başvurabilir.

Bunun, içe aktarılan bileşenlerden daha düşük önceliğe sahip olduğuna dikkat edin. Bileşenin çıkarılan adıyla çakışan adlandırılmış bir içe aktarmanız varsa, içe aktarmaya takma ad verebilirsiniz:

```js
import { FooBar as FooBarChild } from './components'
```

### Ad Alanlı Bileşenler {#namespaced-components}

Nesne özelliklerinin altına yerleştirilmiş bileşenlere başvurmak için `<Foo.Bar>` gibi nokta içeren bileşen etiketlerini kullanabilirsiniz. Bu, tek bir dosyadan birden çok bileşen içe aktardığınızda yararlıdır:

```vue
<script setup>
import * as Form from './form-components'
</script>

<template>
  <Form.Input>
    <Form.Label>etiket</Form.Label>
  </Form.Input>
</template>
```

## Özel Direktifleri Kullanma {#using-custom-directives}

Global olarak kaydedilen özel direktifler normal şekilde çalışır. Yerel özel direktiflerin `<script setup>` ile açıkça kaydedilmesi gerekmez, ancak `vNameOfDirective` adlandırma şemasını izlemeleri gerekir:

```vue
<script setup>
const vMyDirective = {
  beforeMount: (el) => {
    // element ile bir şeyler yap
  }
}
</script>
<template>
  <h1 v-my-directive>Bu bir Başlıktır</h1>
</template>
```

Başka bir yerden bir direktif içe aktarıyorsanız, gerekli adlandırma şemasına uyacak şekilde yeniden adlandırılabilir:

```vue
<script setup>
import { myDirective as vMyDirective } from './MyDirective.js'
</script>
```

## defineProps() & defineEmits() {#defineprops-defineemits}

Tam tür çıkarımı desteğiyle `props` ve `emits` gibi seçenekleri bildirmek için, `<script setup>` içinde otomatik olarak kullanılabilen `defineProps` ve `defineEmits` API'lerini kullanabiliriz:

```vue
<script setup>
const props = defineProps({
  foo: String
})

const emit = defineEmits(['change', 'delete'])
// kurulum kodu
</script>
```

- `defineProps` ve `defineEmits`, yalnızca `<script setup>` içinde kullanılabilen **derleyici makrolarıdır**. İçeri aktarılmaları gerekmez ve `<script setup>` işlenirken derlenirler.

- `defineProps`, `props` seçeneğiyle aynı değeri kabul ederken, `defineEmits` `emits` seçeneğiyle aynı değeri kabul eder.

- `defineProps` ve `defineEmits`, geçirilen seçeneklere göre uygun tür çıkarımı sağlar.

- `defineProps` ve `defineEmits'e geçirilen seçenekler, kurulumdan modül kapsamına yükseltilir. Bu nedenle, seçenekler kurulum kapsamında bildirilen yerel değişkenlere referans veremez. Bunu yapmak bir derleme hatasıyla sonuçlanır. Ancak, modül kapsamında da oldukları için içe aktarılan bağlamalara referans verebilir.

### Yalnızca tür prop/emit bildirimleri<sup class="vt-badge ts" /> {#type-only-props-emit-declarations}

Prop'lar ve emit'ler, `defineProps` veya `defineEmits`'e literal bir tür argümanı geçirilerek saf tür sözdizimi kullanılarak da bildirilebilir:

```ts
const props = defineProps<{
  foo: string
  bar?: number
}>()

const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()

// 3.3+: alternatif, daha özlü sözdizimi
const emit = defineEmits<{
  change: [id: number] // adlandırılmış tuple sözdizimi
  update: [value: string]
}>()
```

- `defineProps` veya `defineEmits` yalnızca çalışma zamanı bildirimi VEYA tür bildirimini kullanabilir. Aynı anda her ikisini de kullanmak bir derleme hatasıyla sonuçlanır.

- Tür bildirimi kullanılırken, çift bildirime gerek kalmadan ve yine de doğru çalışma zamanı davranışını sağlamak için statik analizden eşdeğer çalışma zamanı bildirimi otomatik olarak oluşturulur.

  - Geliştirme modunda, derleyici türlerden karşılık gelen çalışma zamanı doğrulamasını çıkarmaya çalışacaktır. Örneğin, burada `foo: string` türünden `foo: String` çıkarılır. Tür, içe aktarılan bir türe referans ise, derleyicinin harici dosyalar hakkında bilgisi olmadığından çıkarılan sonuç `foo: null` ( `any` türüne eşit) olacaktır.

  - Üretim modunda, derleyici paket boyutunu küçültmek için dizi biçimi bildirimi oluşturacaktır (buradaki prop'lar `['foo', 'bar']` olarak derlenecektir)

- 3.2 ve altı sürümlerde, `defineProps()` için genel tür parametresi, bir tür değişmezi veya yerel bir arayüze referansla sınırlıydı.

  Bu sınırlama 3.3'te çözülmüştür. Vue'nun en son sürümü, tür parametresi konumunda içe aktarılan ve sınırlı sayıda karmaşık tür referansını desteklemektedir. Ancak, türden çalışma zamanına dönüştürme hala AST tabanlı olduğundan, koşullu türler gibi gerçek tür analizi gerektiren bazı karmaşık türler desteklenmez. Tek bir prop'un türü için koşullu türler kullanabilirsiniz, ancak tüm prop'lar nesnesi için kullanamazsınız.

### Reaktif Prop Yapısını Bozma <sup class="vt-badge" data-text="3.5+" /> {#reactive-props-destructure}

Vue 3.5 ve üstü sürümlerde, `defineProps`'un dönüş değerinden yapısı bozulan değişkenler reaktiftir. Vue derleyicisi, aynı `<script setup>` bloğundaki kod `defineProps`'tan yapısı bozulan değişkenlere eriştiğinde otomatik olarak `props.`'u ön plana alır:

```ts
const { foo } = defineProps(['foo'])

watchEffect(() => {
  // 3.5'ten önce yalnızca bir kez çalışır
  // 3.5+'da "foo" prop'u değiştiğinde yeniden çalışır
  console.log(foo)
})
```

Yukarıdaki, aşağıdaki eşdeğere derlenir:

```js {5}
const props = defineProps(['foo'])

watchEffect(() => {
  // Derleyici tarafından `foo` `props.foo` olarak dönüştürüldü
  console.log(props.foo)
})
```

Ek olarak, prop'lar için varsayılan değerleri bildirmek için JavaScript'in yerel varsayılan değer sözdizimini kullanabilirsiniz. Bu, özellikle tür tabanlı prop bildirimini kullanırken kullanışlıdır:

```ts
interface Props {
  msg?: string
  labels?: string[]
}

const { msg = 'merhaba', labels = ['bir', 'iki'] } = defineProps<Props>()
```

### Tür bildirimi kullanırken varsayılan prop değerleri <sup class="vt-badge ts" /> {#default-props-values-when-using-type-declaration}

3.5 ve üstü sürümlerde, Reaktif Prop Yapısını Bozma kullanılırken varsayılan değerler doğal olarak bildirilebilir. Ancak 3.4 ve altı sürümlerde, Reaktif Prop Yapısını Bozma varsayılan olarak etkin değildir. Tür tabanlı bildirimle prop varsayılan değerlerini bildirmek için `withDefaults` derleyici makrosuna ihtiyaç vardır:

```ts
interface Props {
  msg?: string
  labels?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  msg: 'merhaba',
  labels: () => ['bir', 'iki']
})
```

Bu, eşdeğer çalışma zamanı prop `default` seçeneklerine derlenecektir. Ek olarak, `withDefaults` yardımcı programı varsayılan değerler için tür kontrolleri sağlar ve döndürülen `props` türünün, varsayılan değerleri bildirilen özellikler için isteğe bağlı işaretlerin kaldırılmasını sağlar.

:::info
Değiştirilebilir referans türleri (diziler veya nesneler gibi) için varsayılan değerlerin, kazara değişiklik ve harici yan etkilerden kaçınmak için `withDefaults` kullanılırken fonksiyonlara sarılması gerektiğine dikkat edin. Bu, her bileşen örneğinin varsayılan değerin kendi kopyasını almasını sağlar. Yapı bozma ile varsayılan değerler kullanılırken bu gerekli **değildir**.
:::

## defineModel() {#definemodel}

- Yalnızca 3.4+ sürümünde kullanılabilir

Bu makro, üst bileşenden `v-model` aracılığıyla tüketilebilen iki yönlü bir bağlama prop'u bildirmek için kullanılabilir. Örnek kullanım [Bileşen `v-model`](/guide/components/v-model) kılavuzunda da tartışılmıştır.

Kamera arkasında, bu makro bir model prop ve karşılık gelen bir değer güncelleme olayı bildirir. İlk argüman bir literal string ise, prop adı olarak kullanılacaktır; Aksi takdirde, prop adı varsayılan olarak `"modelValue"` olur. Her iki durumda da, prop'un seçeneklerini ve model ref'inin değer dönüştürme seçeneklerini içerebilen ek bir nesne de geçirebilirsiniz.

```js
// üst öğe tarafından v-model aracılığıyla tüketilen "modelValue" prop'unu bildirir
const model = defineModel()
// VEYA: seçeneklerle "modelValue" prop'unu bildirir
const model = defineModel({ type: String })

// değiştirildiğinde "update:modelValue" yayınlar
model.value = 'merhaba'

// üst bileşen tarafından v-model:count aracılığıyla tüketilen "count" prop'unu bildirir
const count = defineModel('count')
// VEYA: seçeneklerle "count" prop'unu bildirir
const count = defineModel('count', { type: Number, default: 0 })

function inc() {
  // değiştirildiğinde "update:count" yayınlar
  count.value++
}
```

:::warning
`defineModel` prop'u için bir `default` değeriniz varsa ve üst bileşenden bu prop için herhangi bir değer sağlamıyorsanız, üst ve alt bileşenler arasında bir senkronizasyon bozulmasına neden olabilir. Aşağıdaki örnekte, üst öğenin `myRef`'i tanımsızdır, ancak alt öğenin `model`'i 1'dir:

```js
// alt bileşen:
const model = defineModel({ default: 1 })

// üst bileşen:
const myRef = ref()
```

```html
<Child v-model="myRef"></Child>
```

:::

### Değiştiriciler ve Dönüştürücüler {#modifiers-and-transformers}

`v-model` direktifiyle kullanılan değiştiricilere erişmek için, `defineModel()`'in dönüş değerini aşağıdaki gibi bozabiliriz:

```js
const [modelValue, modelModifiers] = defineModel()

// v-model.trim'e karşılık gelir
if (modelModifiers.trim) {
  // ...
}
```

Bir değiştirici mevcut olduğunda, okurken veya üst öğeye geri senkronize ederken değeri dönüştürmemiz gerekir. `get` ve `set` dönüştürücü seçeneklerini kullanarak bunu başarabiliriz:

```js
const [modelValue, modelModifiers] = defineModel({
  // burada gerekmediğinden get() atlandı
  set(value) {
    // .trim değiştiricisi kullanılıyorsa, kırpılmış değer döndür
    if (modelModifiers.trim) {
      return value.trim()
    }
    // aksi takdirde, değeri olduğu gibi döndür
    return value
  }
})
```

### TypeScript ile Kullanım <sup class="vt-badge ts" /> {#usage-with-typescript}

`defineProps` ve `defineEmits` gibi `defineModel` de model değerinin ve değiştiricilerin türlerini belirtmek için tür argümanları alabilir:

```ts
const modelValue = defineModel<string>()
//    ^? Ref<string | undefined>

// seçeneklerle varsayılan model, gerekli olası tanımsız değerleri kaldırır
const modelValue = defineModel<string>({ required: true })
//    ^? Ref<string>

const [modelValue, modifiers] = defineModel<string, 'trim' | 'uppercase'>()
//                 ^? Record<'trim' | 'uppercase', true | undefined>
```

## defineExpose() {#defineexpose}

`<script setup>` kullanan bileşenler **varsayılan olarak kapalıdır**; yani şablon ref'leri veya `$parent` zincirleri aracılığıyla alınan bileşenin genel örneği, `<script setup>` içinde bildirilen bağlamalardan hiçbirini **göstermez**.

`<script setup>` bileşeninde özellikleri açıkça göstermek için `defineExpose` derleyici makrosunu kullanın:

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({
  a,
  b
})
</script>
```

Bir üst öğe, bu bileşenin bir örneğini şablon ref'leri aracılığıyla aldığında, alınan örnek `{ a: number, b: number }` şeklinde olacaktır (ref'ler, normal örneklerde olduğu gibi otomatik olarak sarılır).

## defineOptions() {#defineoptions}

- Yalnızca 3.3+ sürümünde desteklenir

Bu makro, ayrı bir `<script>` bloğu kullanmak zorunda kalmadan bileşen seçeneklerini doğrudan `<script setup>` içinde bildirmek için kullanılabilir:

```vue
<script setup>
defineOptions({
  inheritAttrs: false,
  customOptions: {
    /* ... */
  }
})
</script>
```

- Bu bir makrodur. Seçenekler modül kapsamına yükseltilecek ve literal sabitler olmayan `<script setup>` içindeki yerel değişkenlere erişemez.

## defineSlots()<sup class="vt-badge ts"/> {#defineslots}

- Yalnızca 3.3+ sürümünde desteklenir

Bu makro, slot adı ve prop türü denetimi için IDE'lere tür ipuçları sağlamak için kullanılabilir.

`defineSlots()`, yalnızca bir tür parametresi kabul eder ve çalışma zamanı argümanı kabul etmez. Tür parametresi, özellik anahtarının slot adı olduğu ve değer türünün slot fonksiyonu olduğu bir tür değişmezi olmalıdır. Fonksiyonun ilk argümanı, slotun almayı beklediği prop'lardır ve türü şablondaki slot prop'ları için kullanılacaktır. Dönüş türü şu anda yok sayılır ve `any` olabilir, ancak gelecekte slot içeriği kontrolü için bundan yararlanabiliriz.

Ayrıca, kurulum bağlamında gösterilen veya `useSlots()` tarafından döndürülen `slots` nesnesine eşdeğer olan `slots` nesnesini de döndürür.

```vue
<script setup lang="ts">
const slots = defineSlots<{
  default(props: { msg: string }): any
}>()
</script>
```

## `useSlots()` & `useAttrs()` {#useslots-useattrs}

Şablonda doğrudan `$slots` ve `$attrs` olarak erişebileceğiniz için `<script setup>` içinde `slots` ve `attrs` kullanımı nispeten nadir olmalıdır. Onlara gerçekten ihtiyacınız olan nadir durumlarda, sırasıyla `useSlots` ve `useAttrs` yardımcılarını kullanın:

```vue
<script setup>
import { useSlots, useAttrs } from 'vue'

const slots = useSlots()
const attrs = useAttrs()
</script>
```

`useSlots` ve `useAttrs`, `setupContext.slots` ve `setupContext.attrs`'a eşdeğer olanı döndüren gerçek çalışma zamanı fonksiyonlarıdır. Normal composable API fonksiyonlarında da kullanılabilirler.

## Normal `<script>` ile birlikte kullanım {#usage-alongside-normal-script}

`<script setup>`, normal `<script>` ile birlikte kullanılabilir. Aşağıdaki durumlarda normal bir `<script>` gerekebilir:

- `<script setup>` içinde ifade edilemeyen seçenekleri bildirme; örneğin `inheritAttrs` veya eklentiler aracılığıyla etkinleştirilen özel seçenekler (3.3+'da [`defineOptions`](/api/sfc-script-setup#defineoptions) ile değiştirilebilir).
- Adlandırılmış dışa aktarmaları bildirme.
- Yalnızca bir kez yürütülmesi gereken yan etkileri çalıştırma veya nesneler oluşturma.

```vue
<script>
// normal <script>, modül kapsamında yürütülür (yalnızca bir kez)
runSideEffectOnce()

// ek seçenekleri bildir
export default {
  inheritAttrs: false,
  customOptions: {}
}
</script>

<script setup>
// setup() kapsamında yürütülür (her örnek için)
</script>
```

Aynı bileşende `<script setup>` ve `<script>`'i birleştirme desteği, yukarıda açıklanan senaryolarla sınırlıdır. Özellikle:

- `props` ve `emits` gibi `<script setup>` kullanılarak zaten tanımlanabilen seçenekler için ayrı bir `<script>` bölümü **KULLANMAYIN**.
- `<script setup>` içinde oluşturulan değişkenler, Options API'den erişilemez hale getirerek bileşen örneğine özellik olarak eklenmez. API'leri bu şekilde karıştırmak kesinlikle önerilmez.

Kendinizi desteklenmeyen senaryolardan birinde bulursanız, `<script setup>` kullanmak yerine açık bir [`setup()`](/api/composition-api-setup) fonksiyonuna geçmeyi düşünmelisiniz.

## Üst düzey `await` {#top-level-await}

Üst düzey `await` `<script setup>` içinde kullanılabilir. Elde edilen kod, `async setup()` olarak derlenecektir:

```vue
<script setup>
const post = await fetch(`/api/post/1`).then((r) => r.json())
</script>
```

Ek olarak, beklenen ifade, `await`'ten sonra geçerli bileşen örneği bağlamını koruyan bir biçimde otomatik olarak derlenecektir.

:::warning Not
`async setup()`, şu anda hala deneysel bir özellik olan [`Suspense`](/guide/built-ins/suspense.html) ile birlikte kullanılmalıdır. Bunu gelecekteki bir sürümde tamamlayıp belgelemeyi planlıyoruz; ancak şu anda merak ediyorsanız, nasıl çalıştığını görmek için [testlerine](https://github.com/vuejs/core/blob/main/packages/runtime-core/__tests__/components/Suspense.spec.ts) başvurabilirsiniz.
:::

## İçe Aktarma Bildirimleri {#imports-statements}

Vue'daki içe aktarma bildirimleri, [ECMAScript modül belirtimini](https://nodejs.org/api/esm.html) izler.
Ek olarak, derleme aracı yapılandırmanızda tanımlanan takma adları kullanabilirsiniz:

```vue
<script setup>
import { ref } from 'vue'
import { componentA } from './Components'
import { componentB } from '@/Components'
import { componentC } from '~/Components'
</script>
```

## Generics <sup class="vt-badge ts" /> {#generics}

Genel tür parametreleri, `<script>` etiketindeki `generic` özelliği kullanılarak bildirilebilir:

```vue
<script setup lang="ts" generic="T">
defineProps<{
  items: T[]
  selected: T
}>()
</script>
```

`generic`'in değeri, TypeScript'teki `<...>` arasındaki parametre listesiyle tamamen aynı şekilde çalışır. Örneğin, birden çok parametre, `extends` kısıtlamaları, varsayılan türler kullanabilir ve içe aktarılan türlere referans verebilirsiniz:

```vue
<script
  setup
  lang="ts"
  generic="T extends string | number, U extends Item"
>
import type { Item } from './types'
defineProps<{
  id: T
  list: U[]
}>()
</script>
```

`ref`'te genel bir bileşene referans kullanmak için, [`vue-component-type-helpers`](https://www.npmjs.com/package/vue-component-type-helpers) kitaplığını kullanmanız gerekir, çünkü `InstanceType` çalışmaz.

```vue
<script
  setup
  lang="ts"
>
import componentWithoutGenerics from '../component-without-generics.vue';
import genericComponent from '../generic-component.vue';

import type { ComponentExposed } from 'vue-component-type-helpers';

// Jenerik olmayan bir bileşen için çalışır
ref<InstanceType<typeof componentWithoutGenerics>>();

ref<ComponentExposed<typeof genericComponent>>();
```

## Kısıtlamalar {#restrictions}

- Modül yürütme semantiğindeki farklılık nedeniyle, `<script setup>` içindeki kod bir SFC'nin bağlamına dayanır. Harici `.js` veya `.ts` dosyalarına taşındığında, hem geliştiriciler hem de araçlar için kafa karışıklığına yol açabilir. Bu nedenle, **`<script setup>`**, `src` özelliğiyle kullanılamaz.
- `<script setup>` In-DOM Kök Bileşen Şablonunu desteklemez.([İlgili Tartışma](https://github.com/vuejs/core/issues/8391))
