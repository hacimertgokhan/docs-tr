# Sunucu Tarafı Render Etme API'si {#server-side-rendering-api}

## renderToString() {#rendertostring}

- **`vue/server-renderer`'dan dışa aktarılmıştır**

- **Tür**

  ```ts
  function renderToString(
    input: App | VNode,
    context?: SSRContext
  ): Promise<string>
  ```

- **Örnek**

  ```js
  import { createSSRApp } from 'vue'
  import { renderToString } from 'vue/server-renderer'

  const app = createSSRApp({
    data: () => ({ msg: 'merhaba' }),
    template: `<div>{{ msg }}</div>`
  })

  ;(async () => {
    const html = await renderToString(app)
    console.log(html)
  })()
  ```

  ### SSR Bağlamı {#ssr-context}

  İsteğe bağlı bir bağlam nesnesi geçirebilirsiniz. Bu nesne, örneğin [Teleport'ların içeriğine erişme](/guide/scaling-up/ssr#teleports) gibi render sırasında ek verileri kaydetmek için kullanılabilir:

  ```js
  const ctx = {}
  const html = await renderToString(app, ctx)

  console.log(ctx.teleports) // { '#teleported': 'teleported içerik' }
  ```

  Bu sayfadaki diğer SSR API'lerinin çoğu da isteğe bağlı olarak bir bağlam nesnesi kabul eder. Bağlam nesnesine, bileşen kodunda [useSSRContext](#usessrcontext) yardımcı programı aracılığıyla erişilebilir.

- **Ayrıca bakınız** [Rehber - Sunucu Tarafı Render Etme](/guide/scaling-up/ssr)

## renderToNodeStream() {#rendertonodestream}

Girişi bir [Node.js Okunabilir akış](https://nodejs.org/api/stream.html#stream_class_stream_readable) olarak render eder.

- **`vue/server-renderer`'dan dışa aktarılmıştır**

- **Tür**

  ```ts
  function renderToNodeStream(
    input: App | VNode,
    context?: SSRContext
  ): Readable
  ```

- **Örnek**

  ```js
  // bir Node.js http işleyicisi içinde
  renderToNodeStream(app).pipe(res)
  ```

  :::tip Not
  Bu yöntem, Node.js ortamlarından ayrılmış `vue/server-renderer`'ın ESM yapısında desteklenmez. Bunun yerine [`pipeToNodeWritable`](#pipetonodewritable) kullanın.
  :::

## pipeToNodeWritable() {#pipetonodewritable}

Mevcut bir [Node.js Yazılabilir akış](https://nodejs.org/api/stream.html#stream_writable_streams) örneğine render edin ve akış sağlayın.

- **`vue/server-renderer`'dan dışa aktarılmıştır**

- **Tür**

  ```ts
  function pipeToNodeWritable(
    input: App | VNode,
    context: SSRContext = {},
    writable: Writable
  ): void
  ```

- **Örnek**

  ```js
  // bir Node.js http işleyicisi içinde
  pipeToNodeWritable(app, {}, res)
  ```

## renderToWebStream() {#rendertowebstream}

Girişi bir [Web OkunabilirAkışı](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) olarak render eder.

- **`vue/server-renderer`'dan dışa aktarılmıştır**

- **Tür**

  ```ts
  function renderToWebStream(
    input: App | VNode,
    context?: SSRContext
  ): ReadableStream
  ```

- **Örnek**

  ```js
  // ReadableStream desteği olan bir ortam içinde
  return new Response(renderToWebStream(app))
  ```

  :::tip Not
  Global kapsamda `ReadableStream` oluşturucusunu göstermeyen ortamlarda, bunun yerine [`pipeToWebWritable()`](#pipetowebwritable) kullanılmalıdır.
  :::

## pipeToWebWritable() {#pipetowebwritable}

Mevcut bir [Web YazılabilirAkışı](https://developer.mozilla.org/en-US/docs/Web/API/WritableStream) örneğine render edin ve akış sağlayın.

- **`vue/server-renderer`'dan dışa aktarılmıştır**

- **Tür**

  ```ts
  function pipeToWebWritable(
    input: App | VNode,
    context: SSRContext = {},
    writable: WritableStream
  ): void
  ```

- **Örnek**

  Bu, tipik olarak [`TransformStream`](https://developer.mozilla.org/en-US/docs/Web/API/TransformStream) ile birlikte kullanılır:

  ```js
  // TransformStream, CloudFlare worker'ları gibi ortamlarda kullanılabilir.
  // Node.js'de, TransformStream'in 'stream/web'den açıkça içe aktarılması gerekir
  const { readable, writable } = new TransformStream()
  pipeToWebWritable(app, {}, writable)

  return new Response(readable)
  ```

## renderToSimpleStream() {#rendertosimplestream}

Basit bir okunabilir arabirim kullanarak girişi akış modunda render eder.

- **`vue/server-renderer`'dan dışa aktarılmıştır**

- **Tür**

  ```ts
  function renderToSimpleStream(
    input: App | VNode,
    context: SSRContext,
    options: SimpleReadable
  ): SimpleReadable

  interface SimpleReadable {
    push(content: string | null): void
    destroy(err: any): void
  }
  ```

- **Örnek**

  ```js
  let res = ''

  renderToSimpleStream(
    app,
    {},
    {
      push(chunk) {
        if (chunk === null) {
          // tamamlandı
          console(`render tamamlandı: ${res}`)
        } else {
          res += chunk
        }
      },
      destroy(err) {
        // hatayla karşılaşıldı
      }
    }
  )
  ```

## useSSRContext() {#usessrcontext}

`renderToString()` veya diğer sunucu render API'lerine geçirilen bağlam nesnesini almak için kullanılan bir çalışma zamanı API'si.

- **Tür**

  ```ts
  function useSSRContext<T = Record<string, any>>(): T | undefined
  ```

- **Örnek**

  Alınan bağlam, son HTML'i render etmek için gereken bilgileri (örneğin, başlık meta verileri) eklemek için kullanılabilir.

  ```vue
  <script setup>
  import { useSSRContext } from 'vue'

  // yalnızca SSR sırasında çağrıldığından emin olun
  // https://vitejs.dev/guide/ssr.html#conditional-logic
  if (import.meta.env.SSR) {
    const ctx = useSSRContext()
    // ...bağlama özelliklerini bağlama
  }
  </script>
  ```

## data-allow-mismatch <sup class="vt-badge" data-text="3.5+" /> {#data-allow-mismatch}

[Hidrasyon uyuşmazlığı](/guide/scaling-up/ssr#hydration-mismatch) uyarılarını bastırmak için kullanılabilen özel bir öznitelik.

- **Örnek**

  ```html
  <div data-allow-mismatch="text">{{ data.toLocaleString() }}</div>
  ```

  Değer, izin verilen uyuşmazlığı belirli bir türe sınırlayabilir. İzin verilen değerler şunlardır:

  - `text`
  - `children` (yalnızca doğrudan çocuklar için uyuşmazlığa izin verir)
  - `class`
  - `style`
  - `attribute`

  Değer sağlanmazsa, tüm uyuşmazlık türlerine izin verilir.
