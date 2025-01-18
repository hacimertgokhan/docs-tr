# Render Fonksiyonu API'leri {#render-function-apis}

## h() {#h}

Sanal DOM düğümleri (vnode'lar) oluşturur.

- **Tür**

  ```ts
  // tam imza
  function h(
    type: string | Component,
    props?: object | null,
    children?: Children | Slot | Slots
  ): VNode

  // prop'ları atlama
  function h(type: string | Component, children?: Children | Slot): VNode

  type Children = string | number | boolean | VNode | null | Children[]

  type Slot = () => Children

  type Slots = { [name: string]: Slot }
  ```

  > Türler, okunabilirlik için basitleştirilmiştir.

- **Detaylar**

  İlk argüman, bir string (yerel öğeler için) veya bir Vue bileşen tanımı olabilir. İkinci argüman, geçirilecek prop'lardır ve üçüncü argüman çocuklardır.

  Bir bileşen vnode oluşturulurken, çocuklar slot fonksiyonları olarak geçirilmelidir. Bileşen yalnızca varsayılan slotu bekliyorsa tek bir slot fonksiyonu geçirilebilir. Aksi takdirde, slotlar slot fonksiyonlarından oluşan bir nesne olarak geçirilmelidir.

  Kolaylık olması için, çocuklar bir slot nesnesi olmadığında prop argümanı atlanabilir.

- **Örnek**

  Yerel öğeler oluşturma:

  ```js
  import { h } from 'vue'

  // tür dışındaki tüm argümanlar isteğe bağlıdır
  h('div')
  h('div', { id: 'foo' })

  // hem özellikler hem de öznitelikler prop'larda kullanılabilir
  // Vue, onu atamak için doğru yolu otomatik olarak seçer
  h('div', { class: 'bar', innerHTML: 'merhaba' })

  // class ve style, şablonlardaki gibi aynı nesne / dizi
  // değer desteğine sahiptir
  h('div', { class: [foo, { bar }], style: { color: 'red' } })

  // olay dinleyicileri onXxx olarak geçirilmelidir
  h('div', { onClick: () => {} })

  // çocuklar bir string olabilir
  h('div', { id: 'foo' }, 'merhaba')

  // prop'lar olmadığında prop'lar atlanabilir
  h('div', 'merhaba')
  h('div', [h('span', 'merhaba')])

  // çocuklar dizisi, karışık vnode'lar ve stringler içerebilir
  h('div', ['merhaba', h('span', 'merhaba')])
  ```

  Bileşenler oluşturma:

  ```js
  import Foo from './Foo.vue'

  // prop'ları geçirme
  h(Foo, {
    // some-prop="merhaba" ile eşdeğer
    someProp: 'merhaba',
    // @update="() => {}" ile eşdeğer
    onUpdate: () => {}
  })

  // tek varsayılan slot geçirme
  h(Foo, () => 'varsayılan slot')

  // adlandırılmış slotlar geçirme
  // slot nesnesinin prop'lar olarak değerlendirilmesini önlemek için `null` gereklidir
  h(MyComponent, null, {
    default: () => 'varsayılan slot',
    foo: () => h('div', 'foo'),
    bar: () => [h('span', 'bir'), h('span', 'iki')]
  })
  ```

- **Ayrıca bakınız** [Rehber - Render Fonksiyonları - VNode Oluşturma](/guide/extras/render-function#creating-vnodes)

## mergeProps() {#mergeprops}

Belirli prop'lar için özel işlemeyle birden çok prop nesnesini birleştirin.

- **Tür**

  ```ts
  function mergeProps(...args: object[]): object
  ```

- **Detaylar**

  `mergeProps()`, aşağıdaki prop'lar için özel işlemeyle birden çok prop nesnesini birleştirmeyi destekler:

  - `class`
  - `style`
  - `onXxx` olay dinleyicileri - aynı ada sahip birden çok dinleyici bir dizi halinde birleştirilecektir.

  Birleştirme davranışına ihtiyacınız yoksa ve basit üzerine yazmalar istiyorsanız, bunun yerine yerel nesne yayılımı kullanılabilir.

- **Örnek**

  ```js
  import { mergeProps } from 'vue'

  const one = {
    class: 'foo',
    onClick: handlerA
  }

  const two = {
    class: { bar: true },
    onClick: handlerB
  }

  const merged = mergeProps(one, two)
  /**
   {
     class: 'foo bar',
     onClick: [handlerA, handlerB]
   }
   */
  ```

## cloneVNode() {#clonevnode}

Bir vnode'u klonlar.

- **Tür**

  ```ts
  function cloneVNode(vnode: VNode, extraProps?: object): VNode
  ```

- **Detaylar**

  İsteğe bağlı olarak orijinaliyle birleştirilecek ekstra prop'larla birlikte klonlanmış bir vnode döndürür.

  Vnode'lar oluşturulduktan sonra değişmez olarak kabul edilmelidir ve mevcut bir vnode'un prop'larını değiştirmemelisiniz. Bunun yerine, farklı / ekstra prop'larla klonlayın.

  Vnode'lar özel iç özelliklere sahiptir, bu nedenle onları klonlamak bir nesne yayılımı kadar basit değildir. `cloneVNode()`, iç mantığın çoğunu ele alır.

- **Örnek**

  ```js
  import { h, cloneVNode } from 'vue'

  const original = h('div')
  const cloned = cloneVNode(original, { id: 'foo' })
  ```

## isVNode() {#isvnode}

Bir değerin vnode olup olmadığını kontrol eder.

- **Tür**

  ```ts
  function isVNode(value: unknown): boolean
  ```

## resolveComponent() {#resolvecomponent}

Kayıtlı bir bileşeni ada göre manuel olarak çözümlemek için.

- **Tür**

  ```ts
  function resolveComponent(name: string): Component | string
  ```

- **Detaylar**

  **Not: Bileşeni doğrudan içe aktarabiliyorsanız buna ihtiyacınız yoktur.**

  `resolveComponent()`, doğru bileşen bağlamından çözümlemek için <span class="composition-api">ya `setup()` içinde ya da</span> render fonksiyonu içinde çağrılmalıdır.

  Bileşen bulunamazsa, bir çalışma zamanı uyarısı verilir ve ad stringi döndürülür.

- **Örnek**

  <div class="composition-api">

  ```js
  import { h, resolveComponent } from 'vue'

  export default {
    setup() {
      const ButtonCounter = resolveComponent('ButtonCounter')

      return () => {
        return h(ButtonCounter)
      }
    }
  }
  ```

  </div>
  <div class="options-api">

  ```js
  import { h, resolveComponent } from 'vue'

  export default {
    render() {
      const ButtonCounter = resolveComponent('ButtonCounter')
      return h(ButtonCounter)
    }
  }
  ```

  </div>

- **Ayrıca bakınız** [Rehber - Render Fonksiyonları - Bileşenler](/guide/extras/render-function#components)

## resolveDirective() {#resolvedirective}

Kayıtlı bir direktifi ada göre manuel olarak çözümlemek için.

- **Tür**

  ```ts
  function resolveDirective(name: string): Directive | undefined
  ```

- **Detaylar**

  **Not: Direktifi doğrudan içe aktarabiliyorsanız buna ihtiyacınız yoktur.**

  `resolveDirective()`, doğru bileşen bağlamından çözümlemek için <span class="composition-api">ya `setup()` içinde ya da</span> render fonksiyonu içinde çağrılmalıdır.

  Direktif bulunamazsa, bir çalışma zamanı uyarısı verilir ve fonksiyon `undefined` döndürür.

- **Ayrıca bakınız** [Rehber - Render Fonksiyonları - Özel Direktifler](/guide/extras/render-function#custom-directives)

## withDirectives() {#withdirectives}

Vnode'lara özel direktifler eklemek için.

- **Tür**

  ```ts
  function withDirectives(
    vnode: VNode,
    directives: DirectiveArguments
  ): VNode

  // [Direktif, değer, argüman, değiştiriciler]
  type DirectiveArguments = Array<
    | [Directive]
    | [Directive, any]
    | [Directive, any, string]
    | [Directive, any, string, DirectiveModifiers]
  >
  ```

- **Detaylar**

  Mevcut bir vnode'u özel direktiflerle sarar. İkinci argüman, özel direktiflerden oluşan bir dizidir. Her özel direktif, `[Direktif, değer, argüman, değiştiriciler]` biçiminde bir dizi olarak da temsil edilir. Gerekli değilse dizinin sonundaki öğeler atlanabilir.

- **Örnek**

  ```js
  import { h, withDirectives } from 'vue'

  // özel bir direktif
  const pin = {
    mounted() {
      /* ... */
    },
    updated() {
      /* ... */
    }
  }

  // <div v-pin:top.animate="200"></div>
  const vnode = withDirectives(h('div'), [
    [pin, 200, 'top', { animate: true }]
  ])
  ```

- **Ayrıca bakınız** [Rehber - Render Fonksiyonları - Özel Direktifler](/guide/extras/render-function#custom-directives)

## withModifiers() {#withmodifiers}

Bir olay işleyici fonksiyonuna yerleşik [`v-on` değiştiricileri](/guide/essentials/event-handling#event-modifiers) eklemek için.

- **Tür**

  ```ts
  function withModifiers(fn: Function, modifiers: ModifierGuardsKeys[]): Function
  ```

- **Örnek**

  ```js
  import { h, withModifiers } from 'vue'

  const vnode = h('button', {
    // v-on:click.stop.prevent ile eşdeğer
    onClick: withModifiers(() => {
      // ...
    }, ['stop', 'prevent'])
  })
  ```

- **Ayrıca bakınız** [Rehber - Render Fonksiyonları - Olay Değiştiricileri](/guide/extras/render-function#event-modifiers)
