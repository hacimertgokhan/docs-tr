# Yardımcı Türler {#utility-types}

:::info
Bu sayfa yalnızca kullanımları için açıklamaya ihtiyaç duyabilecek yaygın olarak kullanılan birkaç yardımcı türü listeler. Dışa aktarılan türlerin tam listesi için [kaynak koduna](https://github.com/vuejs/core/blob/main/packages/runtime-core/src/index.ts#L131) bakın.
:::

## PropType\<T> {#proptype-t}

Çalışma zamanı prop bildirimleri kullanılırken bir prop'u daha gelişmiş türlerle açıklamak için kullanılır.

- **Örnek**

  ```ts
  import type { PropType } from 'vue'

  interface Book {
    title: string
    author: string
    year: number
  }

  export default {
    props: {
      book: {
        // `Object`'e daha özel bir tür sağla
        type: Object as PropType<Book>,
        required: true
      }
    }
  }
  ```

- **Ayrıca bakınız** [Rehber - Bileşen Prop'larını Türlendirme](/guide/typescript/options-api#typing-component-props)

## MaybeRef\<T> {#mayberef}

- Yalnızca 3.3+ sürümünde desteklenir

`T | Ref<T>` için takma ad. [Composable'ların](/guide/reusability/composables.html) argümanlarını açıklamak için kullanışlıdır.

## MaybeRefOrGetter\<T> {#maybereforgetter}

- Yalnızca 3.3+ sürümünde desteklenir

`T | Ref<T> | (() => T)` için takma ad. [Composable'ların](/guide/reusability/composables.html) argümanlarını açıklamak için kullanışlıdır.

## ExtractPropTypes\<T> {#extractproptypes}

Bir çalışma zamanı prop seçenekleri nesnesinden prop türlerini çıkarın. Çıkarılan türler içe dönüktür; yani bileşen tarafından alınan çözümlenmiş prop'lar. Bu, boolean prop'ların ve varsayılan değerlere sahip prop'ların gerekli olmasalar bile her zaman tanımlandığı anlamına gelir.

Genel kullanıma dönük prop'ları, yani üst öğenin geçirmesine izin verilen prop'ları çıkarmak için [`ExtractPublicPropTypes`](#extractpublicproptypes) kullanın.

- **Örnek**

  ```ts
  const propsOptions = {
    foo: String,
    bar: Boolean,
    baz: {
      type: Number,
      required: true
    },
    qux: {
      type: Number,
      default: 1
    }
  } as const

  type Props = ExtractPropTypes<typeof propsOptions>
  // {
  //   foo?: string,
  //   bar: boolean,
  //   baz: number,
  //   qux: number
  // }
  ```

## ExtractPublicPropTypes\<T> {#extractpublicproptypes}

- Yalnızca 3.3+ sürümünde desteklenir

Bir çalışma zamanı prop seçenekleri nesnesinden prop türlerini çıkarın. Çıkarılan türler genel kullanıma yöneliktir; yani üst öğenin geçirmesine izin verilen prop'lar.

- **Örnek**

  ```ts
  const propsOptions = {
    foo: String,
    bar: Boolean,
    baz: {
      type: Number,
      required: true
    },
    qux: {
      type: Number,
      default: 1
    }
  } as const

  type Props = ExtractPublicPropTypes<typeof propsOptions>
  // {
  //   foo?: string,
  //   bar?: boolean,
  //   baz: number,
  //   qux?: number
  // }
  ```

## ComponentCustomProperties {#componentcustomproperties}

Özel genel özellikleri desteklemek için bileşen örneği türünü artırmak için kullanılır.

- **Örnek**

  ```ts
  import axios from 'axios'

  declare module 'vue' {
    interface ComponentCustomProperties {
      $http: typeof axios
      $translate: (key: string) => string
    }
  }
  ```

  :::tip
  Artırmalar, bir modül `.ts` veya `.d.ts` dosyasına yerleştirilmelidir. Daha fazla ayrıntı için [Tür Artırma Yerleşimi](/guide/typescript/options-api#augmenting-global-properties) bölümüne bakın.
  :::

- **Ayrıca bakınız** [Rehber - Global Özellikleri Artırma](/guide/typescript/options-api#augmenting-global-properties)

## ComponentCustomOptions {#componentcustomoptions}

Özel seçenekleri desteklemek için bileşen seçenekleri türünü artırmak için kullanılır.

- **Örnek**

  ```ts
  import { Route } from 'vue-router'

  declare module 'vue' {
    interface ComponentCustomOptions {
      beforeRouteEnter?(to: any, from: any, next: () => void): void
    }
  }
  ```

  :::tip
  Artırmalar, bir modül `.ts` veya `.d.ts` dosyasına yerleştirilmelidir. Daha fazla ayrıntı için [Tür Artırma Yerleşimi](/guide/typescript/options-api#augmenting-global-properties) bölümüne bakın.
  :::

- **Ayrıca bakınız** [Rehber - Özel Seçenekleri Artırma](/guide/typescript/options-api#augmenting-custom-options)

## ComponentCustomProps {#componentcustomprops}

TSX öğelerinde bildirilmemiş prop'ları kullanmak için izin verilen TSX prop'larını artırmak için kullanılır.

- **Örnek**

  ```ts
  declare module 'vue' {
    interface ComponentCustomProps {
      hello?: string
    }
  }

  export {}
  ```

  ```tsx
  // artık hello bildirilen bir prop olmasa bile çalışıyor
  <MyComponent hello="dünya" />
  ```

  :::tip
  Artırmalar, bir modül `.ts` veya `.d.ts` dosyasına yerleştirilmelidir. Daha fazla ayrıntı için [Tür Artırma Yerleşimi](/guide/typescript/options-api#augmenting-global-properties) bölümüne bakın.
  :::

## CSSProperties {#cssproperties}

Stil özelliği bağlamalarında izin verilen değerleri artırmak için kullanılır.

- **Örnek**

  Herhangi bir özel CSS özelliğine izin verin

  ```ts
  declare module 'vue' {
    interface CSSProperties {
      [key: `--${string}`]: string
    }
  }
  ```

  ```tsx
  <div style={ { '--bg-color': 'blue' } }>
  ```

  ```html
  <div :style="{ '--bg-color': 'blue' }"></div>
  ```

:::tip
Artırmalar, bir modül `.ts` veya `.d.ts` dosyasına yerleştirilmelidir. Daha fazla ayrıntı için [Tür Artırma Yerleşimi](/guide/typescript/options-api#augmenting-global-properties) bölümüne bakın.
:::

:::info Ayrıca bakınız
SFC `<style>` etiketleri, CSS değerlerini `v-bind` CSS fonksiyonunu kullanarak dinamik bileşen durumuna bağlamayı destekler. Bu, tür artırmaya gerek kalmadan özel özelliklere izin verir.

- [CSS'de v-bind()](/api/sfc-css-features#v-bind-in-css)
  :::
