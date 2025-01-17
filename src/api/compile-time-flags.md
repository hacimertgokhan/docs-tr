---
outline: deep
---

# Derleme Zamanı İşaretleri {#derleme-zamani-isaretleri}

:::tip
Derleme zamanı işaretleri yalnızca Vue'nun `esm-bundler` yapısını (yani `vue/dist/vue.esm-bundler.js`) kullanırken geçerlidir.
:::

Vue'yi bir derleme adımıyla kullanırken, belirli özellikleri etkinleştirmek / devre dışı bırakmak için bir dizi derleme zamanı işareti yapılandırmak mümkündür. Derleme zamanı işaretlerini kullanmanın faydası, bu şekilde devre dışı bırakılan özelliklerin ağaç sallama yoluyla son paketten kaldırılabilmesidir.

Bu işaretler açıkça yapılandırılmamış olsa bile Vue çalışacaktır. Ancak, ilgili özelliklerin mümkün olduğunda düzgün şekilde kaldırılabilmesi için bunları her zaman yapılandırmanız önerilir.

Derleme aracınıza bağlı olarak bunları nasıl yapılandıracağınız hakkında [Yapılandırma Kılavuzları](#yapılandırma-kılavuzları) bölümüne bakın.

## `__VUE_OPTIONS_API__` {#VUE_OPTIONS_API}

- **Varsayılan:** `true`

  Options API desteğini etkinleştirin / devre dışı bırakın. Bunu devre dışı bırakmak daha küçük paketlerle sonuçlanır, ancak Options API'sine bağımlı olmaları durumunda 3. taraf kütüphanelerle uyumluluğu etkileyebilir.

## `__VUE_PROD_DEVTOOLS__` {#VUE_PROD_DEVTOOLS}

- **Varsayılan:** `false`

  Üretim yapılarında devtools desteğini etkinleştirin / devre dışı bırakın. Bu, pakete daha fazla kodun dahil edilmesine neden olur, bu nedenle bunu yalnızca hata ayıklama amaçları için etkinleştirmeniz önerilir.

## `__VUE_PROD_HYDRATION_MISMATCH_DETAILS__` {#VUE_PROD_HYDRATION_MISMATCH_DETAILS}

- **Varsayılan:** `false`

  Üretim yapılarında hidrasyon uyuşmazlıkları için ayrıntılı uyarıları etkinleştirin/devre dışı bırakın. Bu, pakete daha fazla kodun dahil edilmesine neden olur, bu nedenle bunu yalnızca hata ayıklama amaçları için etkinleştirmeniz önerilir.

- Yalnızca 3.4+ sürümünde mevcuttur

## Yapılandırma Kılavuzları {#yapılandırma-kılavuzları}

### Vite {#vite}

`@vitejs/plugin-vue`, bu işaretler için otomatik olarak varsayılan değerler sağlar. Varsayılan değerleri değiştirmek için Vite'in [`define` yapılandırma seçeneğini](https://vitejs.dev/config/shared-options.html#define) kullanın:

```js
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
  define: {
    // üretim yapısında hidrasyon uyuşmazlığı ayrıntılarını etkinleştir
    __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'true'
  }
})
```

### vue-cli {#vue-cli}

`@vue/cli-service`, bu işaretlerin bazıları için otomatik olarak varsayılan değerler sağlar. Değerleri yapılandırmak / değiştirmek için:

```js
// vue.config.js
module.exports = {
  chainWebpack: (config) => {
    config.plugin('define').tap((definitions) => {
      Object.assign(definitions[0], {
        __VUE_OPTIONS_API__: 'true',
        __VUE_PROD_DEVTOOLS__: 'false',
        __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
      })
      return definitions
    })
  }
}
```

### webpack {#webpack}

İşaretler, webpack'in [DefinePlugin](https://webpack.js.org/plugins/define-plugin/) kullanılarak tanımlanmalıdır:

```js
// webpack.config.js
module.exports = {
  // ...
  plugins: [
    new webpack.DefinePlugin({
      __VUE_OPTIONS_API__: 'true',
      __VUE_PROD_DEVTOOLS__: 'false',
      __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
    })
  ]
}
```

### Rollup {#rollup}

İşaretler, [@rollup/plugin-replace](https://github.com/rollup/plugins/tree/master/packages/replace) kullanılarak tanımlanmalıdır:

```js
// rollup.config.js
import replace from '@rollup/plugin-replace'

export default {
  plugins: [
    replace({
      __VUE_OPTIONS_API__: 'true',
      __VUE_PROD_DEVTOOLS__: 'false',
      __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
    })
  ]
}
```
