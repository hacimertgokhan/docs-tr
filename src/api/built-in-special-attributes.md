# Dahili Özel Öznitelikler {#dahili-ozel-oznitelikler}

## key {#key}

`key` özel özniteliği, öncelikle Vue'nun sanal DOM algoritması için yeni düğüm listesini eski listeye karşı farklılaştırırken vnode'ları tanımlamak için bir ipucu olarak kullanılır.

- **Beklenen:** `number | string | symbol`

- **Detaylar**

  Anahtarlar olmadan, Vue, öğe hareketini en aza indiren ve aynı türdeki öğeleri mümkün olduğunca yerinde yamamaya/yeniden kullanmaya çalışan bir algoritma kullanır. Anahtarlarla, öğeleri anahtarların sıra değişikliğine göre yeniden sıralayacak ve artık bulunmayan anahtarlara sahip öğeler her zaman kaldırılacak/yok edilecektir.

  Aynı ortak ebeveynin alt öğelerinin **benzersiz anahtarları** olmalıdır. Yinelenen anahtarlar, render hatalarına neden olur.

  En yaygın kullanım örneği, `v-for` ile birleştirilir:

  ```vue-html
  <ul>
    <li v-for="item in items" :key="item.id">...</li>
  </ul>
  ```

  Ayrıca, yeniden kullanmak yerine bir öğenin/bileşenin değiştirilmesini zorlamak için de kullanılabilir. Bu, aşağıdakileri yapmak istediğinizde yararlı olabilir:

  - Bir bileşenin yaşam döngüsü kancalarını düzgün şekilde tetikleme
  - Geçişleri tetikleme

  Örneğin:

  ```vue-html
  <transition>
    <span :key="text">{{ text }}</span>
  </transition>
  ```

  `text` değiştiğinde, `<span>`, yamalanmak yerine her zaman değiştirilecek, bu nedenle bir geçiş tetiklenecektir.

- **Ayrıca bakınız** [Rehber - Liste Render - `key` ile Durumu Koruma](/guide/essentials/list#maintaining-state-with-key)

## ref {#ref}

Bir [şablon ref'ini](/guide/essentials/template-refs) belirtir.

- **Beklenen:** `string | Function`

- **Detaylar**

  `ref`, bir öğeye veya bir alt bileşene bir referans kaydetmek için kullanılır.

  Options API'de, referans bileşenin `this.$refs` nesnesi altında kaydedilecektir:

  ```vue-html
  <!-- this.$refs.p olarak kaydedilir -->
  <p ref="p">merhaba</p>
  ```

  Composition API'de, referans eşleşen ada sahip bir ref'te saklanacaktır:

  ```vue
  <script setup>
  import { useTemplateRef } from 'vue'

  const pRef = useTemplateRef('p')
  </script>

  <template>
    <p ref="p">merhaba</p>
  </template>
  ```

  Düz bir DOM öğesi üzerinde kullanılıyorsa, referans o öğe olacaktır; bir alt bileşende kullanılıyorsa, referans alt bileşen örneği olacaktır.

  Alternatif olarak `ref`, referansın nereye kaydedileceği üzerinde tam kontrol sağlayan bir fonksiyon değeri kabul edebilir:

  ```vue-html
  <ChildComponent :ref="(el) => child = el" />
  ```

  Ref kaydı zamanlaması hakkında önemli bir not: ref'lerin kendileri render fonksiyonunun bir sonucu olarak oluşturulduğundan, onlara erişmeden önce bileşen monte edilene kadar beklemeniz gerekir.

  `this.$refs` ayrıca reaktif değildir, bu nedenle onu şablonlarda veri bağlama için kullanmaya çalışmamalısınız.

- **Ayrıca bakınız**
  - [Rehber - Şablon Refleri](/guide/essentials/template-refs)
  - [Rehber - Şablon Ref'lerini Yazma](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />
  - [Rehber - Bileşen Şablonu Ref'lerini Yazma](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

## is {#is}

[Dinamik bileşenleri](/guide/essentials/component-basics#dynamic-components) bağlamak için kullanılır.

- **Beklenen:** `string | Component`

- **Yerel öğeler üzerinde kullanım**

  - Yalnızca 3.1+ sürümlerinde desteklenir

  `is` özniteliği yerel bir HTML öğesinde kullanıldığında, [Özelleştirilmiş yerleşik öğe](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements-customized-builtin-example) olarak yorumlanacaktır, bu yerel bir web platformu özelliğidir.

  Ancak, [DOM İçi Şablon Ayrıştırma Uyarıları](/guide/essentials/component-basics#in-dom-template-parsing-caveats) bölümünde açıklandığı gibi, yerel bir öğeyi bir Vue bileşeniyle değiştirmeniz gerekebilecek bir kullanım durumu vardır. Vue'nin öğeyi bunun yerine bir Vue bileşeni olarak render etmesi için `is` özniteliğinin değerine `vue:` ön ekini ekleyebilirsiniz:

  ```vue-html
  <table>
    <tr is="vue:my-row-component"></tr>
  </table>
  ```

- **Ayrıca bakınız**

  - [Dahili Özel Öğe - `<component>`](/api/built-in-special-elements#component)
  - [Dinamik Bileşenler](/guide/essentials/component-basics#dynamic-components)
