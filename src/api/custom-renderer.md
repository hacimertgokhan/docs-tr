# Özel Render Edici API'si {#custom-renderer-api}

## createRenderer() {#createrenderer}

Özel bir render edici oluşturur. Platforma özgü düğüm oluşturma ve manipülasyon API'leri sağlayarak, Vue'nun temel çalışma zamanını DOM dışı ortamları hedeflemek için kullanabilirsiniz.

- **Tür**

  ```ts
  function createRenderer<HostNode, HostElement>(
    options: RendererOptions<HostNode, HostElement>
  ): Renderer<HostElement>

  interface Renderer<HostElement> {
    render: RootRenderFunction<HostElement>
    createApp: CreateAppFunction<HostElement>
  }

  interface RendererOptions<HostNode, HostElement> {
    patchProp(
      el: HostElement,
      key: string,
      prevValue: any,
      nextValue: any,
      // geri kalanı çoğu özel render edici için kullanılmıyor
      isSVG?: boolean,
      prevChildren?: VNode<HostNode, HostElement>[],
      parentComponent?: ComponentInternalInstance | null,
      parentSuspense?: SuspenseBoundary | null,
      unmountChildren?: UnmountChildrenFn
    ): void
    insert(
      el: HostNode,
      parent: HostElement,
      anchor?: HostNode | null
    ): void
    remove(el: HostNode): void
    createElement(
      type: string,
      isSVG?: boolean,
      isCustomizedBuiltIn?: string,
      vnodeProps?: (VNodeProps & { [key: string]: any }) | null
    ): HostElement
    createText(text: string): HostNode
    createComment(text: string): HostNode
    setText(node: HostNode, text: string): void
    setElementText(node: HostElement, text: string): void
    parentNode(node: HostNode): HostElement | null
    nextSibling(node: HostNode): HostNode | null

    // isteğe bağlı, DOM'a özgü
    querySelector?(selector: string): HostElement | null
    setScopeId?(el: HostElement, id: string): void
    cloneNode?(node: HostNode): HostNode
    insertStaticContent?(
      content: string,
      parent: HostElement,
      anchor: HostNode | null,
      isSVG: boolean
    ): [HostNode, HostNode]
  }
  ```

- **Örnek**

  ```js
  import { createRenderer } from '@vue/runtime-core'

  const { render, createApp } = createRenderer({
    patchProp,
    insert,
    remove,
    createElement
    // ...
  })

  // `render`, düşük seviyeli API'dir
  // `createApp`, bir uygulama örneği döndürür
  export { render, createApp }

  // Vue core API'lerini yeniden dışa aktar
  export * from '@vue/runtime-core'
  ```

  Vue'nun kendi `@vue/runtime-dom`'u [aynı API kullanılarak uygulanmıştır](https://github.com/vuejs/core/blob/main/packages/runtime-dom/src/index.ts). Daha basit bir uygulama için, Vue'nun kendi birim testi için özel bir paket olan [`@vue/runtime-test`](https://github.com/vuejs/core/blob/main/packages/runtime-test/src/index.ts) paketine göz atın.
