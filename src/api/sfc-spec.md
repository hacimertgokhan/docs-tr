# SFC Sözdizimi Belirtimi {#sfc-syntax-specification}

## Genel Bakış {#overview}

Geleneksel olarak `*.vue` dosya uzantısını kullanan bir Vue Tek Dosyalı Bileşen (SFC), bir Vue bileşenini tanımlamak için HTML benzeri bir sözdizimi kullanan özel bir dosya biçimidir. Bir Vue SFC, sözdizimsel olarak HTML ile uyumludur.

Her `*.vue` dosyası üç tür üst düzey dil bloğundan oluşur: `<template>`, `<script>` ve `<style>` ve isteğe bağlı olarak ek özel bloklar:

```vue
<template>
  <div class="example">{{ msg }}</div>
</template>

<script>
export default {
  data() {
    return {
      msg: 'Merhaba dünya!'
    }
  }
}
</script>

<style>
.example {
  color: red;
}
</style>

<custom1>
  Bu örneğin bileşen için dokümantasyon olabilir.
</custom1>
```

## Dil Blokları {#language-blocks}

### `<template>` {#template}

- Her `*.vue` dosyası en fazla bir üst düzey `<template>` bloğu içerebilir.

- İçerikler ayıklanacak ve JavaScript render fonksiyonlarına önceden derlenmiş olarak `@vue/compiler-dom`'a geçirilecek ve `render` seçeneği olarak dışa aktarılan bileşene eklenecektir.

### `<script>` {#script}

- Her `*.vue` dosyası, en fazla bir `<script>` bloğu içerebilir ([`<script setup>`](/api/sfc-script-setup) hariç).

- Komut dosyası bir ES Modülü olarak yürütülür.

- **Varsayılan dışa aktarma**, düz bir nesne olarak veya [defineComponent](/api/general#definecomponent)'in dönüş değeri olarak bir Vue bileşen seçenekleri nesnesi olmalıdır.

### `<script setup>` {#script-setup}

- Her `*.vue` dosyası en fazla bir `<script setup>` bloğu içerebilir (normal `<script>` hariç).

- Komut dosyası önceden işlenir ve bileşenin `setup()` fonksiyonu olarak kullanılır, yani **bileşenin her örneği için** yürütülür. `<script setup>` içindeki üst düzey bağlamalar otomatik olarak şablona gösterilir. Daha fazla ayrıntı için, [`<script setup>` hakkında özel belgelere](/api/sfc-script-setup) bakın.

### `<style>` {#style}

- Tek bir `*.vue` dosyası birden çok `<style>` etiketi içerebilir.

- Bir `<style>` etiketi, stilleri yalnızca geçerli bileşene kapsüllemeye yardımcı olmak için `scoped` veya `module` özelliklerine sahip olabilir (daha fazla ayrıntı için [SFC Stil Özellikleri](/api/sfc-css-features) bölümüne bakın). Aynı bileşende farklı kapsülleme modlarına sahip birden çok `<style>` etiketi karıştırılabilir.

### Özel Bloklar {#custom-blocks}

Ek özel bloklar, `*.vue` dosyasına, örneğin `<docs>` bloğu gibi projeye özel ihtiyaçlar için dahil edilebilir. Özel blokların bazı gerçek dünya örnekleri şunları içerir:

- [Gridsome: `<page-query>`](https://gridsome.org/docs/querying-data/)
- [vite-plugin-vue-gql: `<gql>`](https://github.com/wheatjs/vite-plugin-vue-gql)
- [vue-i18n: `<i18n>`](https://github.com/intlify/bundle-tools/tree/main/packages/unplugin-vue-i18n#i18n-custom-block)

Özel Blokların işlenmesi araçlara bağlı olacaktır. Kendi özel blok entegrasyonlarınızı oluşturmak istiyorsanız, daha fazla ayrıntı için [SFC özel blok entegrasyonları araçları bölümüne](/guide/scaling-up/tooling#sfc-custom-block-integrations) bakın.

## Otomatik Ad Çıkarımı {#automatic-name-inference}

Bir SFC, aşağıdaki durumlarda bileşenin adını **dosya adından** otomatik olarak çıkarır:

- Geliştirici uyarısı biçimlendirmesi
- Geliştirici araçları incelemesi
- Özyinelemeli kendine referans; örneğin, `FooBar.vue` adlı bir dosya, şablonunda kendisine `<FooBar/>` olarak başvurabilir. Bunun, açıkça kaydedilmiş/içe aktarılmış bileşenlerden daha düşük önceliği vardır.

## Ön İşleyiciler {#pre-processors}

Bloklar, `lang` özelliğini kullanarak ön işlemci dillerini bildirebilir. En yaygın durum, `<script>` bloğu için TypeScript kullanmaktır:

```vue-html
<script lang="ts">
  // TypeScript kullan
</script>
```

`lang` herhangi bir bloğa uygulanabilir; örneğin, [Sass](https://sass-lang.com/) ile `<style>` ve [Pug](https://pugjs.org/api/getting-started.html) ile `<template>` kullanabiliriz:

```vue-html
<template lang="pug">
p {{ msg }}
</template>

<style lang="scss">
  $primary-color: #333;
  body {
    color: $primary-color;
  }
</style>
```

Çeşitli ön işlemcilerle entegrasyonun araç zincirine göre farklılık gösterebileceğine dikkat edin. Örnekler için ilgili belgelere göz atın:

- [Vite](https://vitejs.dev/guide/features.html#css-pre-processors)
- [Vue CLI](https://cli.vuejs.org/guide/css.html#pre-processors)
- [webpack + vue-loader](https://vue-loader.vuejs.org/guide/pre-processors.html#using-pre-processors)

## `src` İçe Aktarmaları {#src-imports}

`*.vue` bileşenlerinizi birden çok dosyaya bölmeyi tercih ederseniz, bir dil bloğu için harici bir dosya içe aktarmak için `src` özelliğini kullanabilirsiniz:

```vue
<template src="./template.html"></template>
<style src="./style.css"></style>
<script src="./script.js"></script>
```

`src` içe aktarmalarının, webpack modül istekleriyle aynı yol çözümleme kurallarını izlediğine dikkat edin, bu şu anlama gelir:

- Göreli yollar `./` ile başlamalıdır
- Kaynakları npm bağımlılıklarından içe aktarabilirsiniz:

```vue
<!-- kurulu "todomvc-app-css" npm paketinden bir dosya içe aktarın -->
<style src="todomvc-app-css/index.css" />
```

`src` içe aktarmaları özel bloklarla da çalışır, örn:

```vue
<unit-test src="./unit-test.js">
</unit-test>
```

:::warning Not
`src` içinde takma adları kullanırken `~` ile başlamayın, bundan sonraki her şey bir modül isteği olarak yorumlanır. Bu, node modüllerinin içindeki varlıklara referans verebileceğiniz anlamına gelir:
```vue
<img src="~some-npm-package/foo.png">
```
:::

## Yorumlar {#comments}

Her blok içinde, kullanılan dilin yorum sözdizimini (HTML, CSS, JavaScript, Pug, vb.) kullanmanız gerekir. Üst düzey yorumlar için HTML yorum sözdizimini kullanın: `<!-- yorum içeriği burada -->`
