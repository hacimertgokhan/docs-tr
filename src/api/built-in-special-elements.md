# Dahili Özel Elemanlar {#dahili-ozel-elemanlar}

:::info Not Bileşenler
`<component>`, `<slot>` ve `<template>` bileşen benzeri özelliklerdir ve şablon sözdiziminin bir parçasıdır. Gerçek bileşenler değillerdir ve şablon derlemesi sırasında derlenirler. Bu nedenle, şablonlarda geleneksel olarak küçük harflerle yazılırlar.
:::

## `<component>` {#component}

Dinamik bileşenleri veya öğeleri render etmek için bir "meta bileşen".

- **Props**

  ```ts
  interface DynamicComponentProps {
    is: string | Component
  }
  ```

- **Detaylar**

  Render edilecek gerçek bileşen, `is` prop'u tarafından belirlenir.

  - `is` bir dize olduğunda, bir HTML etiket adı veya bir bileşenin kayıtlı adı olabilir.

  - Alternatif olarak, `is` doğrudan bir bileşenin tanımına da bağlanabilir.

- **Örnek**

  Kayıtlı ada göre bileşenleri render etme (Options API):

  ```vue
  <script>
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'

  export default {
    components: { Foo, Bar },
    data() {
      return {
        view: 'Foo'
      }
    }
  }
  </script>

  <template>
    <component :is="view" />
  </template>
  ```

  Tanıma göre bileşenleri render etme ( `<script setup>` ile Composition API):

  ```vue
  <script setup>
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'
  </script>

  <template>
    <component :is="Math.random() > 0.5 ? Foo : Bar" />
  </template>
  ```

  HTML öğelerini render etme:

  ```vue-html
  <component :is="href ? 'a' : 'span'"></component>
  ```

  [Dahili bileşenlerin](./built-in-components) tümü `is`'e geçirilebilir, ancak adıyla geçirmek istiyorsanız bunları kaydetmeniz gerekir. Örneğin:

  ```vue
  <script>
  import { Transition, TransitionGroup } from 'vue'

  export default {
    components: {
      Transition,
      TransitionGroup
    }
  }
  </script>

  <template>
    <component :is="isGroup ? 'TransitionGroup' : 'Transition'">
      ...
    </component>
  </template>
  ```

  Bileşeni adından ziyade kendisini `is`'e geçirirseniz, örneğin `<script setup>`'ta kayıt gerekli değildir.

  `v-model` bir `<component>` etiketi üzerinde kullanılırsa, şablon derleyicisi onu diğer bileşenlerde olduğu gibi bir `modelValue` prop'una ve `update:modelValue` olay dinleyicisine genişletecektir. Ancak, bu `<input>` veya `<select>` gibi yerel HTML öğeleriyle uyumlu olmayacaktır. Sonuç olarak, dinamik olarak oluşturulmuş yerel bir öğeyle `v-model` kullanmak işe yaramaz:

  ```vue
  <script setup>
  import { ref } from 'vue'

  const tag = ref('input')
  const username = ref('')
  </script>

  <template>
    <!-- Bu, 'input' yerel bir HTML öğesi olduğu için çalışmayacak -->
    <component :is="tag" v-model="username" />
  </template>
  ```

  Uygulamada, bu uç durum yaygın değildir, çünkü yerel form alanları genellikle gerçek uygulamalarda bileşenlere sarılır. Yerel bir öğeyi doğrudan kullanmanız gerekiyorsa, `v-model`'i manuel olarak bir öznitelik ve olaya ayırabilirsiniz.

- **Ayrıca bakınız** [Dinamik Bileşenler](/guide/essentials/component-basics#dynamic-components)

## `<slot>` {#slot}

Şablonlarda yuva içeriği çıkışlarını belirtir.

- **Props**

  ```ts
  interface SlotProps {
    /**
     * Kapsamlı yuvalar için argümanlar olarak geçirilecek
     * <slot> öğesine geçirilen herhangi bir prop
     */
    [key: string]: any
    /**
     * Yuva adını belirtmek için ayrılmıştır.
     */
    name?: string
  }
  ```

- **Detaylar**

  `<slot>` öğesi, bir yuva adı belirtmek için `name` özniteliğini kullanabilir. `name` belirtilmediğinde, varsayılan yuva render edilecektir. Yuva öğesine iletilen ek öznitelikler, üst öğede tanımlanan kapsamlı yuvaya yuva prop'ları olarak geçirilecektir.

  Öğenin kendisi, eşleşen yuva içeriğiyle değiştirilecektir.

  Vue şablonlarındaki `<slot>` öğeleri JavaScript'e derlenir, bu nedenle [yerel `<slot>` öğeleriyle](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot) karıştırılmamalıdır.

- **Ayrıca bakınız** [Bileşen - Yuvalar](/guide/components/slots)

## `<template>` {#template}

`<template>` etiketi, DOM'da bir öğeyi render etmeden dahili bir direktif kullanmak istediğimizde bir yer tutucu olarak kullanılır.

- **Detaylar**

  `<template>` için özel işleme, yalnızca şu direktiflerden biriyle birlikte kullanıldığında tetiklenir:

  - `v-if`, `v-else-if` veya `v-else`
  - `v-for`
  - `v-slot`

  Bu direktiflerden hiçbiri yoksa, bunun yerine [yerel bir `<template>` öğesi](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) olarak render edilecektir.

  `v-for`'a sahip bir `<template>`'te ayrıca bir [`key` özniteliği](/api/built-in-special-attributes#key) olabilir. Diğer tüm öznitelikler ve direktifler, karşılık gelen bir öğe olmadan anlamlı olmadıkları için atılacaktır.

  Tek dosyalı bileşenler, tüm şablonu sarmalamak için [üst düzey bir `<template>` etiketi](/api/sfc-spec#language-blocks) kullanır. Bu kullanım, yukarıda açıklanan `<template>` kullanımından ayrıdır. Bu üst düzey etiket, şablonun kendisinin bir parçası değildir ve direktifler gibi şablon sözdizimini desteklemez.

- **Ayrıca bakınız**
  - [Rehber - `<template>`'te `v-if`](/guide/essentials/conditional#v-if-on-template)
  - [Rehber - `<template>`'te `v-for`](/guide/essentials/list#v-for-on-template)
  - [Rehber - Adlandırılmış yuvalar](/guide/components/slots#named-slots)
