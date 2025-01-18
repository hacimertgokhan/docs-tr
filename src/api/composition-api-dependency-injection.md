# Composition API: <br>Bağımlılık Enjeksiyonu {#composition-api-dependency-injection}

## provide() {#provide}

Alt bileşenler tarafından enjekte edilebilecek bir değer sağlar.

- **Tür**

  ```ts
  function provide<T>(key: InjectionKey<T> | string, value: T): void
  ```

- **Detaylar**

  `provide()`, iki argüman alır: bir string veya sembol olabilen anahtar ve enjekte edilecek değer.

  TypeScript kullanırken, anahtar, `provide()` ve `inject()` arasında değer türünü senkronize etmek için kullanılabilen, `Symbol`'u genişleten Vue tarafından sağlanan bir yardımcı tür olan `InjectionKey` olarak işaretlenebilir.

  Yaşam döngüsü kancası kayıt API'lerine benzer şekilde, `provide()`, bir bileşenin `setup()` aşamasında eşzamanlı olarak çağrılmalıdır.

- **Örnek**

  ```vue
  <script setup>
  import { ref, provide } from 'vue'
  import { countSymbol } from './injectionSymbols'

  // statik değer sağla
  provide('path', '/project/')

  // reaktif değer sağla
  const count = ref(0)
  provide('count', count)

  // Sembol anahtarlarla sağla
  provide(countSymbol, count)
  </script>
  ```

- **Ayrıca bakınız**
  - [Rehber - Sağlama / Enjekte Etme](/guide/components/provide-inject)
  - [Rehber - Sağlama / Enjekte Etme Türlendirme](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

## inject() {#inject}

Bir üst bileşen veya uygulama ( `app.provide()` yoluyla) tarafından sağlanan bir değeri enjekte eder.

- **Tür**

  ```ts
  // varsayılan değer olmadan
  function inject<T>(key: InjectionKey<T> | string): T | undefined

  // varsayılan değer ile
  function inject<T>(key: InjectionKey<T> | string, defaultValue: T): T

  // fabrika ile
  function inject<T>(
    key: InjectionKey<T> | string,
    defaultValue: () => T,
    treatDefaultAsFactory: true
  ): T
  ```

- **Detaylar**

  İlk argüman, enjeksiyon anahtarıdır. Vue, eşleşen bir anahtara sahip sağlanan bir değeri bulmak için ebeveyn zincirinde yukarı doğru ilerleyecektir. Ebeveyn zincirindeki birden fazla bileşen aynı anahtarı sağlıyorsa, enjekte eden bileşene en yakın olanı, zincirdeki daha yukarıdakileri "gölgeler". Eşleşen anahtara sahip bir değer bulunamazsa, bir varsayılan değer sağlanmadığı sürece `inject()`, `undefined` değerini döndürür.

  İkinci argüman isteğe bağlıdır ve eşleşen bir değer bulunamadığında kullanılacak varsayılan değerdir.

  İkinci argüman, oluşturulması maliyetli değerler döndüren bir fabrika işlevi de olabilir. Bu durumda, işlevin değerin kendisi yerine bir fabrika olarak kullanılması gerektiğini belirtmek için üçüncü argüman olarak `true` geçirilmelidir.

  Yaşam döngüsü kancası kayıt API'lerine benzer şekilde, `inject()`, bir bileşenin `setup()` aşamasında eşzamanlı olarak çağrılmalıdır.

  TypeScript kullanırken, anahtar, `provide()` ve `inject()` arasında değer türünü senkronize etmek için kullanılabilen, `Symbol`'u genişleten Vue tarafından sağlanan bir yardımcı tür olan `InjectionKey` türünde olabilir.

- **Örnek**

  Bir üst bileşenin önceki `provide()` örneğinde gösterildiği gibi değerler sağladığını varsayarsak:

  ```vue
  <script setup>
  import { inject } from 'vue'
  import { countSymbol } from './injectionSymbols'

  // varsayılan olmadan statik değeri enjekte et
  const path = inject('path')

  // reaktif değeri enjekte et
  const count = inject('count')

  // Sembol anahtarlarla enjekte et
  const count2 = inject(countSymbol)

  // varsayılan değerle enjekte et
  const bar = inject('path', '/default-path')

  // fonksiyon varsayılan değeriyle enjekte et
  const fn = inject('function', () => {})

  // varsayılan değer fabrikasıyla enjekte et
  const baz = inject('factory', () => new ExpensiveObject(), true)
  </script>
  ```

- **Ayrıca bakınız**
  - [Rehber - Sağlama / Enjekte Etme](/guide/components/provide-inject)
  - [Rehber - Sağlama / Enjekte Etme Türlendirme](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

## hasInjectionContext() {#has-injection-context}

- Yalnızca 3.3+ sürümünde desteklenir

Yanlış yerde (örneğin `setup()` dışında) çağrıldığı konusunda uyarı vermeden [inject()](#inject) kullanılabilirse true döndürür. Bu yöntem, uç kullanıcıya bir uyarı tetiklemeden dahili olarak `inject()` kullanmak isteyen kitaplıklar tarafından kullanılmak üzere tasarlanmıştır.

- **Tür**

  ```ts
  function hasInjectionContext(): boolean
  ```
