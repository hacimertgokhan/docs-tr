# Composition API: setup() {#composition-api-setup}

## Temel Kullanım {#basic-usage}

`setup()` kancası, aşağıdaki durumlarda bileşenlerde Composition API kullanımı için giriş noktası görevi görür:

1. Derleme adımı olmadan Composition API kullanma;
2. Options API bileşeninde Composition-API tabanlı kodla entegrasyon.

:::info Not
Tek Dosyalı Bileşenlerle Composition API kullanıyorsanız, daha özlü ve ergonomik bir sözdizimi için [`<script setup>`](/api/sfc-script-setup) şiddetle tavsiye edilir.
:::

[Reaktivite API'lerini](./reactivity-core) kullanarak reaktif durum bildirebilir ve `setup()`'tan bir nesne döndürerek bunları şablona gösterebiliriz. Döndürülen nesnedeki özellikler, bileşen örneğinde de kullanılabilir hale getirilir (başka seçenekler kullanılıyorsa):

```vue
<script>
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    // şablona ve diğer options API kancalarına göster
    return {
      count
    }
  },

  mounted() {
    console.log(this.count) // 0
  }
}
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>
```

`setup`'tan döndürülen [refs](/api/reactivity-core#ref), şablonda erişildiğinde [otomatik olarak sığ bir şekilde sarılır](/guide/essentials/reactivity-fundamentals#deep-reactivity), böylece onlara erişirken `.value` kullanmanız gerekmez. `this` üzerinde erişildiğinde de aynı şekilde sarılırlar.

`setup()`'ın kendisi bileşen örneğine erişemez - `setup()` içinde `this` değeri `undefined` olacaktır. Option API'den Composition API ile gösterilen değerlere erişebilirsiniz, ancak tersi geçerli değildir.

`setup()`, _eşzamanlı_ olarak bir nesne döndürmelidir. `async setup()`'ın kullanılabileceği tek durum, bileşenin bir [Suspense](../guide/built-ins/suspense) bileşeninin alt öğesi olduğunda ortaya çıkar.

## Props'a Erişme {#accessing-props}

`setup` işlevindeki ilk argüman, `props` argümanıdır. Standart bir bileşende beklendiği gibi, bir `setup` işlevi içindeki `props` reaktiftir ve yeni props'lar geçirildiğinde güncellenir.

```js
export default {
  props: {
    title: String
  },
  setup(props) {
    console.log(props.title)
  }
}
```

`props` nesnesini yapısını bozarsanız, yapısı bozulan değişkenler reaktivitesini kaybeder. Bu nedenle, her zaman `props.xxx` biçiminde prop'lara erişilmesi önerilir.

Props'u gerçekten bozmanız gerekiyorsa veya reaktiviteyi korurken bir prop'u harici bir fonksiyona geçirmeniz gerekiyorsa, [toRefs()](./reactivity-utilities#torefs) ve [toRef()](/api/reactivity-utilities#toref) yardımcı API'leri ile bunu yapabilirsiniz:

```js
import { toRefs, toRef } from 'vue'

export default {
  setup(props) {
    // `props`'u bir ref nesnesine çevir, sonra yapısını boz
    const { title } = toRefs(props)
    // `title`, `props.title`'ı izleyen bir ref'tir
    console.log(title.value)

    // VEYA, `props` üzerindeki tek bir özelliği bir ref'e çevir
    const title = toRef(props, 'title')
  }
}
```

## Kurulum Bağlamı {#setup-context}

`setup` fonksiyonuna geçirilen ikinci argüman, bir **Kurulum Bağlamı** nesnesidir. Bağlam nesnesi, `setup` içinde yararlı olabilecek diğer değerleri gösterir:

```js
export default {
  setup(props, context) {
    // Özellikler (Reaktif olmayan nesne, $attrs'a eşdeğer)
    console.log(context.attrs)

    // Slotlar (Reaktif olmayan nesne, $slots'a eşdeğer)
    console.log(context.slots)

    // Olayları yayınla (Fonksiyon, $emit'e eşdeğer)
    console.log(context.emit)

    // Genel özellikleri göster (Fonksiyon)
    console.log(context.expose)
  }
}
```

Bağlam nesnesi reaktif değildir ve güvenli bir şekilde yapısı bozulabilir:

```js
export default {
  setup(props, { attrs, slots, emit, expose }) {
    ...
  }
}
```

`attrs` ve `slots`, bileşenin kendisi güncellendiğinde her zaman güncellenen durumlu nesnelerdir. Bu, yapılarının bozulmasından kaçınmanız ve özelliklere her zaman `attrs.x` veya `slots.x` olarak başvurmanız gerektiği anlamına gelir. Ayrıca, `props`'tan farklı olarak, `attrs` ve `slots`'un özelliklerinin **reaktif olmadığını** unutmayın. `attrs` veya `slots`'taki değişikliklere dayalı olarak yan etkiler uygulamayı planlıyorsanız, bunu bir `onBeforeUpdate` yaşam döngüsü kancası içinde yapmalısınız.

### Genel Özellikleri Gösterme {#exposing-public-properties}

`expose`, bileşen örneğine bir üst bileşen tarafından [şablon referansları](/guide/essentials/template-refs#ref-on-component) aracılığıyla erişildiğinde gösterilen özellikleri açıkça sınırlamak için kullanılabilen bir fonksiyondur:

```js{5,10}
export default {
  setup(props, { expose }) {
    // örneği "kapalı" hale getir -
    // yani, üst öğeye hiçbir şey gösterme
    expose()

    const publicCount = ref(0)
    const privateCount = ref(0)
    // yerel durumu seçerek göster
    expose({ count: publicCount })
  }
}
```

## Render Fonksiyonları ile Kullanım {#usage-with-render-functions}

`setup`, aynı kapsamda bildirilen reaktif durumu doğrudan kullanabilen bir [render fonksiyonu](/guide/extras/render-function) da döndürebilir:

```js{6}
import { h, ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return () => h('div', count.value)
  }
}
```

Bir render fonksiyonu döndürmek, başka bir şey döndürmemizi engeller. Dahili olarak bunun bir sorun olmaması gerekir, ancak bu bileşenin metotlarını şablon referansları aracılığıyla üst bileşene göstermek istersek sorunlu olabilir.

Bu sorunu [`expose()`](#exposing-public-properties) çağırarak çözebiliriz:

```js{8-10}
import { h, ref } from 'vue'

export default {
  setup(props, { expose }) {
    const count = ref(0)
    const increment = () => ++count.value

    expose({
      increment
    })

    return () => h('div', count.value)
  }
}
```

`increment` metodu daha sonra bir şablon referansı aracılığıyla üst bileşende kullanılabilir hale gelir.
