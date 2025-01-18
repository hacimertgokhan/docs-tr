# Seçenekler: Render Etme {#options-rendering}

## template {#template}

Bileşen için bir string şablonu.

- **Tür**

  ```ts
  interface ComponentOptions {
    template?: string
  }
  ```

- **Detaylar**

  `template` seçeneği aracılığıyla sağlanan bir şablon, çalışma zamanında anında derlenecektir. Yalnızca şablon derleyicisini içeren bir Vue yapısı kullanılırken desteklenir. Şablon derleyicisi, adlarında `runtime` kelimesi bulunan Vue yapılarında **YOKTUR**, örneğin `vue.runtime.esm-bundler.js`. Farklı yapılar hakkında daha fazla ayrıntı için [dist dosyası kılavuzuna](https://github.com/vuejs/core/tree/main/packages/vue#which-dist-file-to-use) bakın.

  String `#` ile başlıyorsa, bir `querySelector` olarak kullanılacak ve seçilen öğenin `innerHTML`'sini şablon stringi olarak kullanacaktır. Bu, kaynak şablonun yerel `<template>` öğeleri kullanılarak yazılmasına olanak tanır.

  Aynı bileşende `render` seçeneği de varsa, `template` göz ardı edilecektir.

  Uygulamanızın kök bileşeninde belirtilen bir `template` veya `render` seçeneği yoksa, Vue bunun yerine monte edilmiş öğenin `innerHTML`'sini şablon olarak kullanmayı deneyecektir.

  :::warning Güvenlik Notu
  Yalnızca güvenebileceğiniz şablon kaynaklarını kullanın. Kullanıcı tarafından sağlanan içeriği şablonunuz olarak kullanmayın. Daha fazla ayrıntı için [Güvenlik Kılavuzu](/guide/best-practices/security#rule-no-1-never-use-non-trusted-templates) bölümüne bakın.
  :::

## render {#render}

Bileşenin sanal DOM ağacını programlı olarak döndüren bir fonksiyon.

- **Tür**

  ```ts
  interface ComponentOptions {
    render?(this: ComponentPublicInstance) => VNodeChild
  }

  type VNodeChild = VNodeChildAtom | VNodeArrayChildren

  type VNodeChildAtom =
    | VNode
    | string
    | number
    | boolean
    | null
    | undefined
    | void

  type VNodeArrayChildren = (VNodeArrayChildren | VNodeChildAtom)[]
  ```

- **Detaylar**

  `render`, bileşenin render çıktısını bildirmek için JavaScript'in tam programatik gücünden yararlanmanıza olanak tanıyan string şablonlara bir alternatiftir.

  Önceden derlenmiş şablonlar, örneğin Tek Dosyalı Bileşenlerdeki şablonlar, derleme zamanında `render` seçeneğine derlenir. Bir bileşende hem `render` hem de `template` varsa, `render` daha yüksek önceliğe sahip olacaktır.

- **Ayrıca bakınız**
  - [Render Mekanizması](/guide/extras/rendering-mechanism)
  - [Render Fonksiyonları](/guide/extras/render-function)

## compilerOptions {#compileroptions}

Bileşenin şablonu için çalışma zamanı derleyici seçeneklerini yapılandırın.

- **Tür**

  ```ts
  interface ComponentOptions {
    compilerOptions?: {
      isCustomElement?: (tag: string) => boolean
      whitespace?: 'condense' | 'preserve' // varsayılan: 'condense'
      delimiters?: [string, string] // varsayılan: ['{{', '}}']
      comments?: boolean // varsayılan: false
    }
  }
  ```

- **Detaylar**

  Bu yapılandırma seçeneğine yalnızca tam derlemeyi (yani tarayıcıda şablonları derleyebilen bağımsız `vue.js`) kullanırken uyulur. Uygulama düzeyi [app.config.compilerOptions](/api/application#app-config-compileroptions) ile aynı seçenekleri destekler ve geçerli bileşen için daha yüksek önceliğe sahiptir.

- **Ayrıca bakınız** [app.config.compilerOptions](/api/application#app-config-compileroptions)

## slots<sup class="vt-badge ts"/> {#slots}

- Yalnızca 3.3+ sürümünde desteklenir

Render fonksiyonlarında programlı olarak slotlar kullanırken tür çıkarımına yardımcı olacak bir seçenek.

- **Detaylar**

  Bu seçeneğin çalışma zamanı değeri kullanılmaz. Gerçek türler, `SlotsType` tür yardımcısı kullanılarak tür atama yoluyla bildirilmelidir:

  ```ts
  import { SlotsType } from 'vue'

  defineComponent({
    slots: Object as SlotsType<{
      default: { foo: string; bar: number }
      item: { data: number }
    }>,
    setup(props, { slots }) {
      expectType<
        undefined | ((scope: { foo: string; bar: number }) => any)
      >(slots.default)
      expectType<undefined | ((scope: { data: number }) => any)>(
        slots.item
      )
    }
  })
  ```
