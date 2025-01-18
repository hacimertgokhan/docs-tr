# Composition API: Yardımcılar {#composition-api-helpers}

## useAttrs() {#useattrs}

Mevcut bileşenin [geçiş özellikleri](/guide/components/attrs#fallthrough-attributes) dahil olmak üzere [Kurulum Bağlamı](/api/composition-api-setup#setup-context)'ndan `attrs` nesnesini döndürür. Bu, kurulum bağlamı nesnesinin kullanılamadığı `<script setup>` içinde kullanılmak üzere tasarlanmıştır.

- **Tür**

  ```ts
  function useAttrs(): Record<string, unknown>
  ```

## useSlots() {#useslots}

Sanal DOM düğümleri döndüren çağrılabilir fonksiyonlar olarak üstten geçirilen slotları içeren [Kurulum Bağlamı](/api/composition-api-setup#setup-context)'ndan `slots` nesnesini döndürür. Bu, kurulum bağlamı nesnesinin kullanılamadığı `<script setup>` içinde kullanılmak üzere tasarlanmıştır.

TypeScript kullanılıyorsa, bunun yerine [`defineSlots()`](/api/sfc-script-setup#defineslots) tercih edilmelidir.

- **Tür**

  ```ts
  function useSlots(): Record<string, (...args: any[]) => VNode[]>
  ```

## useModel() {#usemodel}

Bu, [`defineModel()`](/api/sfc-script-setup#definemodel)'e güç veren temel yardımcıdır. `<script setup>` kullanılıyorsa, bunun yerine `defineModel()` tercih edilmelidir.

- Yalnızca 3.4+ sürümünde kullanılabilir

- **Tür**

  ```ts
  function useModel(
    props: Record<string, any>,
    key: string,
    options?: DefineModelOptions
  ): ModelRef

  type DefineModelOptions<T = any> = {
    get?: (v: T) => any
    set?: (v: T) => any
  }

  type ModelRef<T, M extends PropertyKey = string, G = T, S = T> = Ref<G, S> & [
    ModelRef<T, M, G, S>,
    Record<M, true | undefined>
  ]
  ```

- **Örnek**

  ```js
  export default {
    props: ['count'],
    emits: ['update:count'],
    setup(props) {
      const msg = useModel(props, 'count')
      msg.value = 1
    }
  }
  ```

- **Detaylar**

  `useModel()`, SFC olmayan bileşenlerde, örneğin ham `setup()` fonksiyonu kullanıldığında kullanılabilir. İlk argüman olarak `props` nesnesini ve ikinci argüman olarak model adını bekler. İsteğe bağlı üçüncü argüman, ortaya çıkan model ref'i için özel getter ve setter bildirmek için kullanılabilir. `defineModel()`'den farklı olarak, props ve emitleri kendiniz bildirmeniz gerektiğini unutmayın.

## useTemplateRef() <sup class="vt-badge" data-text="3.5+" /> {#usetemplateref}

Değeri, eşleşen bir ref özelliği olan şablon öğesi veya bileşenle senkronize edilecek olan sığ bir ref döndürür.

- **Tür**

  ```ts
  function useTemplateRef<T>(key: string): Readonly<ShallowRef<T | null>>
  ```

- **Örnek**

  ```vue
  <script setup>
  import { useTemplateRef, onMounted } from 'vue'

  const inputRef = useTemplateRef('input')

  onMounted(() => {
    inputRef.value.focus()
  })
  </script>

  <template>
    <input ref="input" />
  </template>
  ```

- **Ayrıca bakınız**
  - [Rehber - Şablon Referansları](/guide/essentials/template-refs)
  - [Rehber - Şablon Referansları Türlendirme](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />
  - [Rehber - Bileşen Şablonu Referansları Türlendirme](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

## useId() <sup class="vt-badge" data-text="3.5+" /> {#useid}

Erişilebilirlik özellikleri veya form öğeleri için uygulama başına benzersiz kimlikler oluşturmak için kullanılır.

- **Tür**

  ```ts
  function useId(): string
  ```

- **Örnek**

  ```vue
  <script setup>
  import { useId } from 'vue'

  const id = useId()
  </script>

  <template>
    <form>
      <label :for="id">Ad:</label>
      <input :id="id" type="text" />
    </form>
  </template>
  ```

- **Detaylar**

  `useId()` tarafından oluşturulan kimlikler, uygulama başına benzersizdir. Form öğeleri ve erişilebilirlik özellikleri için kimlikler oluşturmak için kullanılabilir. Aynı bileşende birden fazla çağrı farklı kimlikler oluşturacaktır; `useId()` çağıran aynı bileşenin birden fazla örneği de farklı kimliklere sahip olacaktır.

  `useId()` tarafından oluşturulan kimliklerin sunucu ve istemci renderları arasında kararlı olmaları da garanti edilir, bu nedenle SSR uygulamalarında hidrasyon uyuşmazlıklarına yol açmadan kullanılabilirler.

  Aynı sayfada birden fazla Vue uygulama örneğiniz varsa, her uygulamaya [`app.config.idPrefix`](/api/application#app-config-idprefix) aracılığıyla bir kimlik ön eki vererek kimlik çakışmalarını önleyebilirsiniz.
