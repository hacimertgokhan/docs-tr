# Özel Öğeler API'si {#custom-elements-api}

## defineCustomElement() {#definecustomelement}

Bu metot, [`defineComponent`](#definecomponent) ile aynı argümanı kabul eder, ancak bunun yerine yerel bir [Özel Öğeler](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements) sınıf oluşturucusu döndürür.

- **Tür**

  ```ts
  function defineCustomElement(
    component:
      | (ComponentOptions & CustomElementsOptions)
      | ComponentOptions['setup'],
    options?: CustomElementsOptions
  ): {
    new (props?: object): HTMLElement
  }

  interface CustomElementsOptions {
    styles?: string[]

    // aşağıdaki seçenekler 3.5+
    configureApp?: (app: App) => void
    shadowRoot?: boolean
    nonce?: string
  }
  ```

  > Tip, okunabilirlik için basitleştirilmiştir.

- **Detaylar**

  Normal bileşen seçeneklerine ek olarak, `defineCustomElement()` özel öğelere özgü bir dizi seçeneği de destekler:

  - **`styles`**: öğenin gölge köküne enjekte edilmesi gereken CSS sağlamak için satır içi CSS dizeleri dizisi.

  - **`configureApp`** <sup class="vt-badge" data-text="3.5+"/>: özel öğe için Vue uygulama örneğini yapılandırmak için kullanılabilen bir işlev.

  - **`shadowRoot`** <sup class="vt-badge" data-text="3.5+"/>: `boolean`, varsayılan olarak `true`. Özel öğeyi gölge kökü olmadan render etmek için `false` olarak ayarlayın. Bu, özel öğe SFC'lerindeki `<style>`'ın artık kapsüllenmeyeceği anlamına gelir.

  - **`nonce`** <sup class="vt-badge" data-text="3.5+"/>: `string`, sağlanırsa, gölge köküne enjekte edilen stil etiketlerine `nonce` özelliği olarak ayarlanacaktır.

  Bu seçeneklerin, bileşenin kendisinin bir parçası olarak geçirilmek yerine ikinci bir argüman aracılığıyla da geçirilebileceğini unutmayın:

  ```js
  import Element from './MyElement.ce.vue'

  defineCustomElement(Element, {
    configureApp(app) {
      // ...
    }
  })
  ```

  Dönüş değeri, [`customElements.define()`](https://developer.mozilla.org/en-US/docs/Web/API/CustomElementRegistry/define) kullanılarak kaydedilebilen bir özel öğe oluşturucusudur.

- **Örnek**

  ```js
  import { defineCustomElement } from 'vue'

  const MyVueElement = defineCustomElement({
    /* bileşen seçenekleri */
  })

  // Özel öğeyi kaydet.
  customElements.define('my-vue-element', MyVueElement)
  ```

- **Ayrıca bakınız**

  - [Rehber - Vue ile Özel Öğeler Oluşturma](/guide/extras/web-components#building-custom-elements-with-vue)

  - Ayrıca, Tek Dosyalı Bileşenlerle kullanıldığında `defineCustomElement()`'ın [özel yapılandırma](/guide/extras/web-components#sfc-as-custom-element) gerektirdiğini unutmayın.

## useHost() <sup class="vt-badge" data-text="3.5+"/> {#usehost}

Mevcut Vue özel öğesinin ana öğesini döndüren bir Composition API yardımcısı.

## useShadowRoot() <sup class="vt-badge" data-text="3.5+"/> {#useshadowroot}

Mevcut Vue özel öğesinin gölge kökünü döndüren bir Composition API yardımcısı.

## this.$host <sup class="vt-badge" data-text="3.5+"/> {#this-host}

Mevcut Vue özel öğesinin ana öğesini gösteren bir Options API özelliği.
