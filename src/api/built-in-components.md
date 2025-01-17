---
pageClass: api
---

# Dahili Bileşenler {#dahili-bileşenler}

:::info Kayıt ve Kullanım
Dahili bileşenler, kaydedilmeye gerek kalmadan doğrudan şablonlarda kullanılabilir. Ayrıca ağaç sallanabilirler: yalnızca kullanıldıklarında yapıya dahil edilirler.

[Render fonksiyonlarında](/guide/extras/render-function) kullanırken, açıkça içe aktarılmaları gerekir. Örneğin:

```js
import { h, Transition } from 'vue'

h(Transition, {
  /* props */
})
```

:::

## `<Transition>` {#transition}

**Tek** bir öğeye veya bileşene animasyonlu geçiş efektleri sağlar.

- **Props**

  ```ts
  interface TransitionProps {
    /**
     * Geçiş CSS sınıf adlarını otomatik olarak oluşturmak için kullanılır.
     * örn. `name: 'fade'`, `.fade-enter`,
     * `.fade-enter-active` vb. olarak otomatik olarak genişleyecektir.
     */
    name?: string
    /**
     * CSS geçiş sınıflarının uygulanıp uygulanmayacağı.
     * Varsayılan: true
     */
    css?: boolean
    /**
     * Geçiş sonu zamanlamasını belirlemek için beklenecek
     * geçiş olaylarının türünü belirtir.
     * Varsayılan davranış, daha uzun süreye sahip türü otomatik olarak
     * algılamaktır.
     */
    type?: 'transition' | 'animation'
    /**
     * Geçişin açık sürelerini belirtir.
     * Varsayılan davranış, kök geçiş öğesindeki ilk
     * `transitionend` veya `animationend` olayını beklemektir.
     */
    duration?: number | { enter: number; leave: number }
    /**
     * Ayrılma/giriş geçişlerinin zamanlama sırasını denetler.
     * Varsayılan davranış eşzamanlıdır.
     */
    mode?: 'in-out' | 'out-in' | 'default'
    /**
     * İlk renderda geçiş uygulanıp uygulanmayacağı.
     * Varsayılan: false
     */
    appear?: boolean

    /**
     * Geçiş sınıflarını özelleştirmek için props.
     * Şablonlarda kebab-case kullanın, örn. enter-from-class="xxx"
     */
    enterFromClass?: string
    enterActiveClass?: string
    enterToClass?: string
    appearFromClass?: string
    appearActiveClass?: string
    appearToClass?: string
    leaveFromClass?: string
    leaveActiveClass?: string
    leaveToClass?: string
  }
  ```

- **Olaylar**

  - `@before-enter`
  - `@before-leave`
  - `@enter`
  - `@leave`
  - `@appear`
  - `@after-enter`
  - `@after-leave`
  - `@after-appear`
  - `@enter-cancelled`
  - `@leave-cancelled` (`v-show` yalnızca)
  - `@appear-cancelled`

- **Örnek**

  Basit öğe:

  ```vue-html
  <Transition>
    <div v-if="ok">geçişli içerik</div>
  </Transition>
  ```

  `key` özniteliğini değiştirerek bir geçişi zorlama:

  ```vue-html
  <Transition>
    <div :key="text">{{ text }}</div>
  </Transition>
  ```

  Dinamik bileşen, geçiş modu + görünümde animasyonla:

  ```vue-html
  <Transition name="fade" mode="out-in" appear>
    <component :is="view"></component>
  </Transition>
  ```

  Geçiş olaylarını dinleme:

  ```vue-html
  <Transition @after-enter="onTransitionComplete">
    <div v-show="ok">geçişli içerik</div>
  </Transition>
  ```

- **Ayrıca bakınız** [Rehber - Geçiş](/guide/built-ins/transition)

## `<TransitionGroup>` {#transitiongroup}

Bir listedeki **birden çok** öğe veya bileşen için geçiş efektleri sağlar.

- **Props**

  `<TransitionGroup>`, `mode` dışında `<Transition>` ile aynı prop'ları ve ayrıca iki ek prop'u kabul eder:

  ```ts
  interface TransitionGroupProps extends Omit<TransitionProps, 'mode'> {
    /**
     * Tanımlanmamışsa, bir parça olarak render eder.
     */
    tag?: string
    /**
     * Taşıma geçişleri sırasında uygulanan CSS sınıfını özelleştirmek için.
     * Şablonlarda kebab-case kullanın, örn. move-class="xxx"
     */
    moveClass?: string
  }
  ```

- **Olaylar**

  `<TransitionGroup>`, `<Transition>` ile aynı olayları yayar.

- **Detaylar**

  Varsayılan olarak, `<TransitionGroup>` bir sarmalayıcı DOM öğesi render etmez, ancak `tag` prop'u aracılığıyla biri tanımlanabilir.

  Animasyonların düzgün çalışması için `<transition-group>` içindeki her çocuğun [**benzersiz anahtarlı**](/guide/essentials/list#maintaining-state-with-key) olması gerektiğini unutmayın.

  `<TransitionGroup>`, CSS dönüştürmesi yoluyla hareketli geçişleri destekler. Bir güncellemeden sonra ekranda bir çocuğun konumu değiştiğinde, hareketli bir CSS sınıfı uygulanacaktır ( `name` özniteliğinden otomatik olarak oluşturulur veya `move-class` prop'u ile yapılandırılır). CSS `transform` özelliği, hareketli sınıf uygulandığında "geçiş yapılabilir" ise, öğe [FLIP tekniği](https://aerotwist.com/blog/flip-your-animations/) kullanılarak hedefine düzgün bir şekilde animasyonla getirilecektir.

- **Örnek**

  ```vue-html
  <TransitionGroup tag="ul" name="slide">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </TransitionGroup>
  ```

- **Ayrıca bakınız** [Rehber - TransitionGroup](/guide/built-ins/transition-group)

## `<KeepAlive>` {#keepalive}

İçine sarılmış dinamik olarak değiştirilen bileşenleri önbelleğe alır.

- **Props**

  ```ts
  interface KeepAliveProps {
    /**
     * Belirtilirse, yalnızca `include` ile eşleşen adlara sahip bileşenler
     * önbelleğe alınacaktır.
     */
    include?: MatchPattern
    /**
     * Adı `exclude` ile eşleşen herhangi bir bileşen
     * önbelleğe alınmayacaktır.
     */
    exclude?: MatchPattern
    /**
     * Önbelleğe alınacak maksimum bileşen örneği sayısı.
     */
    max?: number | string
  }

  type MatchPattern = string | RegExp | (string | RegExp)[]
  ```

- **Detaylar**

  Dinamik bir bileşen etrafına sarıldığında, `<KeepAlive>` etkin olmayan bileşen örneklerini yok etmeden önbelleğe alır.

  Herhangi bir zamanda `<KeepAlive>`'ın doğrudan alt öğesi olarak yalnızca bir etkin bileşen örneği olabilir.

  Bir bileşen `<KeepAlive>` içinde değiştirildiğinde, `mounted` ve `unmounted` yerine çağrılmayan, `activated` ve `deactivated` yaşam döngüsü kancaları buna göre çağrılacaktır. Bu, `<KeepAlive>`'ın doğrudan alt öğesi ve tüm alt öğeleri için geçerlidir.

- **Örnek**

  Temel kullanım:

  ```vue-html
  <KeepAlive>
    <component :is="view"></component>
  </KeepAlive>
  ```

  `v-if` / `v-else` dallarıyla kullanıldığında, aynı anda yalnızca bir bileşen render edilmelidir:

  ```vue-html
  <KeepAlive>
    <comp-a v-if="a > 1"></comp-a>
    <comp-b v-else></comp-b>
  </KeepAlive>
  ```

  `<Transition>` ile birlikte kullanılır:

  ```vue-html
  <Transition>
    <KeepAlive>
      <component :is="view"></component>
    </KeepAlive>
  </Transition>
  ```

  `include` / `exclude` kullanımı:

  ```vue-html
  <!-- virgülle ayrılmış dize -->
  <KeepAlive include="a,b">
    <component :is="view"></component>
  </KeepAlive>

  <!-- regex ( `v-bind` kullanın) -->
  <KeepAlive :include="/a|b/">
    <component :is="view"></component>
  </KeepAlive>

  <!-- Dizi ( `v-bind` kullanın) -->
  <KeepAlive :include="['a', 'b']">
    <component :is="view"></component>
  </KeepAlive>
  ```

  `max` ile kullanım:

  ```vue-html
  <KeepAlive :max="10">
    <component :is="view"></component>
  </KeepAlive>
  ```

- **Ayrıca bakınız** [Rehber - KeepAlive](/guide/built-ins/keep-alive)

## `<Teleport>` {#teleport}

Yuva içeriğini DOM'un başka bir bölümüne render eder.

- **Props**

  ```ts
  interface TeleportProps {
    /**
     * Gerekli. Hedef kapsayıcıyı belirtin.
     * Bir seçici veya gerçek bir öğe olabilir.
     */
    to: string | HTMLElement
    /**
     * `true` olduğunda, içerik hedef kapsayıcıya taşınmak yerine orijinal
     * konumunda kalacaktır.
     * Dinamik olarak değiştirilebilir.
     */
    disabled?: boolean
      /**
     * `true` olduğunda, Teleport, hedefi çözümlemeden önce
     * uygulamanın diğer bölümleri monte edilene kadar erteler. (3.5+)
     */
    defer?: boolean
  }
  ```

- **Örnek**

  Hedef kapsayıcı belirtme:

  ```vue-html
  <Teleport to="#some-id" />
  <Teleport to=".some-class" />
  <Teleport to="[data-teleport]" />
  ```

  Koşullu olarak devre dışı bırakma:

  ```vue-html
  <Teleport to="#popup" :disabled="displayVideoInline">
    <video src="./my-movie.mp4">
  </Teleport>
  ```

  Hedef çözümlemeyi erteleme <sup class="vt-badge" data-text="3.5+" />:

  ```vue-html
  <Teleport defer to="#late-div">...</Teleport>

  <!-- şablonda daha sonra bir yerde -->
  <div id="late-div"></div>
  ```

- **Ayrıca bakınız** [Rehber - Teleport](/guide/built-ins/teleport)

## `<Suspense>` <sup class="vt-badge experimental" /> {#suspense}

Bir bileşen ağacında iç içe geçmiş asenkron bağımlılıkları düzenlemek için kullanılır.

- **Props**

  ```ts
  interface SuspenseProps {
    timeout?: string | number
    suspensible?: boolean
  }
  ```

- **Olaylar**

  - `@resolve`
  - `@pending`
  - `@fallback`

- **Detaylar**

  `<Suspense>`, iki yuva kabul eder: `#default` yuvası ve `#fallback` yuvası. Varsayılan yuva bellekte render edilirken geri dönüş yuvasının içeriğini gösterecektir.

  Varsayılan yuva render edilirken asenkron bağımlılıklarla ([Asenkron Bileşenler](/guide/components/async) ve [`async setup()`](/guide/built-ins/suspense#async-setup) içeren bileşenler) karşılaşırsa, tümü çözülene kadar bekleyecektir.

  Suspense'ı `suspensible` olarak ayarlayarak, tüm asenkron bağımlılık işleme üst Suspense tarafından ele alınacaktır. [Uygulama ayrıntılarına](https://github.com/vuejs/core/pull/6736) bakın

- **Ayrıca bakınız** [Rehber - Suspense](/guide/built-ins/suspense)
