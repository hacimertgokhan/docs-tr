# Global API: Genel {#global-api-general}

## version {#version}

Vue'nun mevcut sürümünü gösterir.

- **Tür:** `string`

- **Örnek**

  ```js
  import { version } from 'vue'

  console.log(version)
  ```

## nextTick() {#nexttick}

Sonraki DOM güncelleme akışını beklemek için bir yardımcı program.

- **Tür**

  ```ts
  function nextTick(callback?: () => void): Promise<void>
  ```

- **Detaylar**

  Vue'da reaktif durumu değiştirdiğinizde, ortaya çıkan DOM güncellemeleri eşzamanlı olarak uygulanmaz. Bunun yerine, yaptığınız durum değişikliği sayısından bağımsız olarak her bileşenin yalnızca bir kez güncellenmesini sağlamak için Vue bunları "sonraki tıklamaya" kadar arabelleğe alır.

  DOM güncellemelerinin tamamlanmasını beklemek için bir durum değişikliğinden hemen sonra `nextTick()` kullanılabilir. Bir argüman olarak bir geri çağrı geçirebilir veya döndürülen Promise'i bekleyebilirsiniz.

- **Örnek**

  <div class="composition-api">

  ```vue
  <script setup>
  import { ref, nextTick } from 'vue'

  const count = ref(0)

  async function increment() {
    count.value++

    // DOM henüz güncellenmedi
    console.log(document.getElementById('counter').textContent) // 0

    await nextTick()
    // DOM şimdi güncellendi
    console.log(document.getElementById('counter').textContent) // 1
  }
  </script>

  <template>
    <button id="counter" @click="increment">{{ count }}</button>
  </template>
  ```

  </div>
  <div class="options-api">

  ```vue
  <script>
  import { nextTick } from 'vue'

  export default {
    data() {
      return {
        count: 0
      }
    },
    methods: {
      async increment() {
        this.count++

        // DOM henüz güncellenmedi
        console.log(document.getElementById('counter').textContent) // 0

        await nextTick()
        // DOM şimdi güncellendi
        console.log(document.getElementById('counter').textContent) // 1
      }
    }
  }
  </script>

  <template>
    <button id="counter" @click="increment">{{ count }}</button>
  </template>
  ```

  </div>

- **Ayrıca bakınız** [`this.$nextTick()`](/api/component-instance#nexttick)

## defineComponent() {#definecomponent}

Tür çıkarımı ile bir Vue bileşeni tanımlamak için bir tür yardımcı program.

- **Tür**

  ```ts
  // seçenekler sözdizimi
  function defineComponent(
    component: ComponentOptions
  ): ComponentConstructor

  // fonksiyon sözdizimi (3.3+ gerektirir)
  function defineComponent(
    setup: ComponentOptions['setup'],
    extraOptions?: ComponentOptions
  ): () => any
  ```

  > Tür, okunabilirlik için basitleştirilmiştir.

- **Detaylar**

  İlk argüman, bir bileşen seçenekleri nesnesi bekler. Dönüş değeri, fonksiyon yalnızca tür çıkarımı amaçları için esasen bir çalışma zamanı no-op olduğu için aynı seçenekler nesnesi olacaktır.

  Dönüş türünün biraz özel olduğuna dikkat edin: örneğin türü, seçeneklere göre çıkarılan bileşen örneği türü olan bir oluşturucu türü olacaktır. Bu, döndürülen tür TSX'te bir etiket olarak kullanıldığında tür çıkarımı için kullanılır.

  Bir bileşenin örnek türünü (`this`'in seçeneklerindeki türüne eşdeğer) `defineComponent()`'ın dönüş türünden şu şekilde çıkarabilirsiniz:

  ```ts
  const Foo = defineComponent(/* ... */)

  type FooInstance = InstanceType<typeof Foo>
  ```

  ### Fonksiyon İmzası {#function-signature}

  - Yalnızca 3.3+ sürümünde desteklenir

  `defineComponent()`, Composition API ve [render fonksiyonları veya JSX](/guide/extras/render-function.html) ile kullanılmak üzere tasarlanmış alternatif bir imzaya da sahiptir.

  Bir seçenek nesnesi geçirmek yerine, bunun yerine bir fonksiyon beklenir. Bu fonksiyon, Composition API [`setup()`](/api/composition-api-setup.html#composition-api-setup) fonksiyonuyla aynı şekilde çalışır: prop'ları ve kurulum bağlamını alır. Dönüş değeri, bir render fonksiyonu olmalıdır - hem `h()` hem de JSX desteklenir:

  ```js
  import { ref, h } from 'vue'

  const Comp = defineComponent(
    (props) => {
      // <script setup>'taki gibi burada Composition API kullanın
      const count = ref(0)

      return () => {
        // render fonksiyonu veya JSX
        return h('div', count.value)
      }
    },
    // ekstra seçenekler, örneğin prop'ları ve emit'leri bildirme
    {
      props: {
        /* ... */
      }
    }
  )
  ```

  Bu imza için ana kullanım durumu, TypeScript (ve özellikle TSX) ile birlikte, genel türleri desteklediği için:

  ```tsx
  const Comp = defineComponent(
    <T extends string | number>(props: { msg: T; list: T[] }) => {
      // <script setup>'taki gibi burada Composition API kullanın
      const count = ref(0)

      return () => {
        // render fonksiyonu veya JSX
        return <div>{count.value}</div>
      }
    },
    // manuel çalışma zamanı props bildirimi şu anda hala gereklidir.
    {
      props: ['msg', 'list']
    }
  )
  ```

  Gelecekte, çalışma zamanı prop'larının (SFC'lerdeki `defineProps` gibi) otomatik olarak çıkarılmasını ve enjekte edilmesini sağlayan bir Babel eklentisi sağlamayı planlıyoruz, böylece çalışma zamanı prop bildirimi atlanabilir.

  ### webpack Treeshaking Hakkında Not {#note-on-webpack-treeshaking}

  `defineComponent()` bir fonksiyon çağrısı olduğundan, bazı derleme araçlarına, örneğin webpack'e yan etkiler üretecek gibi görünebilir. Bu, bileşen hiç kullanılmasa bile bileşenin ağaç silkelenmesini (tree-shaking) engelleyecektir.

  Webpack'e bu fonksiyon çağrısının ağaç silkeleme için güvenli olduğunu söylemek için, fonksiyon çağrısından önce bir `/*#__PURE__*/` yorum gösterimi ekleyebilirsiniz:

  ```js
  export default /*#__PURE__*/ defineComponent(/* ... */)
  ```

  Vite kullanıyorsanız, bunun gerekli olmadığını unutmayın, çünkü Rollup (Vite tarafından kullanılan temel üretim paketleyicisi), manuel açıklamalar yapılmasına gerek kalmadan `defineComponent()`'ın aslında yan etkisiz olduğunu belirleyecek kadar akıllıdır.

- **Ayrıca bakınız** [Rehber - TypeScript ile Vue Kullanımı](/guide/typescript/overview#general-usage-notes)

## defineAsyncComponent() {#defineasynccomponent}

Yalnızca render edildiğinde tembelce yüklenen bir asenkron bileşen tanımlayın. Argüman, bir yükleyici fonksiyonu veya yükleme davranışının daha gelişmiş kontrolü için bir seçenek nesnesi olabilir.

- **Tür**

  ```ts
  function defineAsyncComponent(
    source: AsyncComponentLoader | AsyncComponentOptions
  ): Component

  type AsyncComponentLoader = () => Promise<Component>

  interface AsyncComponentOptions {
    loader: AsyncComponentLoader
    loadingComponent?: Component
    errorComponent?: Component
    delay?: number
    timeout?: number
    suspensible?: boolean
    onError?: (
      error: Error,
      retry: () => void,
      fail: () => void,
      attempts: number
    ) => any
  }
  ```

- **Ayrıca bakınız** [Rehber - Asenkron Bileşenler](/guide/components/async)
