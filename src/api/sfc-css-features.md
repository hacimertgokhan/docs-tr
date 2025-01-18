# SFC CSS Özellikleri {#sfc-css-features}

## Kapsamlı CSS {#scoped-css}

Bir `<style>` etiketinde `scoped` özelliği varsa, CSS'i yalnızca geçerli bileşenin öğelerine uygulanır. Bu, Shadow DOM'da bulunan stil kapsüllemeye benzer. Bazı uyarılarla birlikte gelir, ancak herhangi bir polifil gerektirmez. Aşağıdakileri dönüştürmek için PostCSS kullanılarak elde edilir:

```vue
<style scoped>
.example {
  color: red;
}
</style>

<template>
  <div class="example">merhaba</div>
</template>
```

Aşağıdaki şekilde:

```vue
<style>
.example[data-v-f3f3eg9] {
  color: red;
}
</style>

<template>
  <div class="example" data-v-f3f3eg9>merhaba</div>
</template>
```

### Alt Bileşen Kök Öğeleri {#child-component-root-elements}

`scoped` ile üst bileşenin stilleri alt bileşenlere sızmaz. Ancak, bir alt bileşenin kök düğümü hem üst öğenin kapsamlı CSS'sinden hem de alt öğenin kapsamlı CSS'sinden etkilenecektir. Bu, üst öğenin düzen amaçları için alt kök öğesini stillendirebilmesi için tasarlanmıştır.

### Derin Seçiciler {#deep-selectors}

`scoped` stillerinde bir seçicinin "derin" olmasını, yani alt bileşenleri etkilemesini istiyorsanız, `:deep()` sözde sınıfını kullanabilirsiniz:

```vue
<style scoped>
.a :deep(.b) {
  /* ... */
}
</style>
```

Yukarıdaki aşağıdaki gibi derlenecektir:

```css
.a[data-v-f3f3eg9] .b {
  /* ... */
}
```

:::tip
`v-html` ile oluşturulan DOM içeriği, kapsamlı stillerden etkilenmez, ancak yine de derin seçiciler kullanarak onları stillendirebilirsiniz.
:::

### Yuvalanmış Seçiciler {#slotted-selectors}

Varsayılan olarak, kapsamlı stiller `<slot/>` tarafından render edilen içeriği etkilemez, çünkü bunlar, onları içine geçiren üst bileşene ait kabul edilir. Slot içeriğini açıkça hedeflemek için `:slotted` sözde sınıfını kullanın:

```vue
<style scoped>
:slotted(div) {
  color: red;
}
</style>
```

### Global Seçiciler {#global-selectors}

Yalnızca bir kuralın global olarak uygulanmasını istiyorsanız, başka bir `<style>` oluşturmak yerine `:global` sözde sınıfını kullanabilirsiniz (aşağıya bakın):

```vue
<style scoped>
:global(.red) {
  color: red;
}
</style>
```

### Yerel ve Global Stilleri Karıştırma {#mixing-local-and-global-styles}

Aynı bileşende hem kapsamlı hem de kapsamlı olmayan stilleri de içerebilirsiniz:

```vue
<style>
/* global stiller */
</style>

<style scoped>
/* yerel stiller */
</style>
```

### Kapsamlı Stil İpuçları {#scoped-style-tips}

- **Kapsamlı stiller, sınıflara olan ihtiyacı ortadan kaldırmaz**. Tarayıcıların çeşitli CSS seçicilerini render etme biçimi nedeniyle, `p { color: red }`, kapsamlı olduğunda (yani bir öznitelik seçiciyle birleştirildiğinde) birçok kez daha yavaş olacaktır. Bunun yerine sınıfları veya kimlikleri kullanırsanız, örneğin `.example { color: red }`'de olduğu gibi, bu performans düşüşünü neredeyse ortadan kaldırırsınız.

- **Özyinelemeli bileşenlerde alt seçicilere dikkat edin!** `.a .b` seçicisine sahip bir CSS kuralı için, `.a` ile eşleşen öğe özyinelemeli bir alt bileşen içeriyorsa, o zaman o alt bileşendeki tüm `.b`'ler kural tarafından eşleştirilecektir.

## CSS Modülleri {#css-modules}

Bir `<style module>` etiketi [CSS Modülleri](https://github.com/css-modules/css-modules) olarak derlenir ve ortaya çıkan CSS sınıflarını bileşene `$style` anahtarı altında bir nesne olarak gösterir:

```vue
<template>
  <p :class="$style.red">Bu kırmızı olmalı</p>
</template>

<style module>
.red {
  color: red;
}
</style>
```

Ortaya çıkan sınıflar, çakışmayı önlemek için karma hale getirilir ve CSS'in yalnızca geçerli bileşene kapsamlanmasıyla aynı etkiyi elde eder.

[Global istisnalar](https://github.com/css-modules/css-modules/blob/master/docs/composition.md#exceptions) ve [bileşim](https://github.com/css-modules/css-modules/blob/master/docs/composition.md#composition) gibi daha fazla ayrıntı için [CSS Modülleri spesifikasyonuna](https://github.com/css-modules/css-modules) bakın.

### Özel Enjeksiyon Adı {#custom-inject-name}

`module` özelliğine bir değer vererek, enjekte edilen sınıflar nesnesinin özellik anahtarını özelleştirebilirsiniz:

```vue
<template>
  <p :class="classes.red">kırmızı</p>
</template>

<style module="classes">
.red {
  color: red;
}
</style>
```

### Composition API ile Kullanım {#usage-with-composition-api}

Enjekte edilen sınıflara, `useCssModule` API'si aracılığıyla `setup()` ve `<script setup>` içinde erişilebilir. Özel enjeksiyon adlarına sahip `<style module>` blokları için, `useCssModule`, eşleşen `module` özellik değerini ilk argüman olarak kabul eder:

```js
import { useCssModule } from 'vue'

// setup() kapsamı içinde...
// varsayılan, <style module> için sınıflar döndürür
useCssModule()

// adlandırılmış, <style module="classes"> için sınıflar döndürür
useCssModule('classes')
```

- **Örnek**

```vue
<script setup lang="ts">
import { useCssModule } from 'vue'

const classes = useCssModule()
</script>

<template>
  <p :class="classes.red">kırmızı</p>
</template>

<style module>
.red {
  color: red;
}
</style>
```

## CSS'de `v-bind()` {#v-bind-in-css}

SFC `<style>` etiketleri, CSS değerlerini `v-bind` CSS fonksiyonunu kullanarak dinamik bileşen durumuna bağlamayı destekler:

```vue
<template>
  <div class="text">merhaba</div>
</template>

<script>
export default {
  data() {
    return {
      color: 'red'
    }
  }
}
</script>

<style>
.text {
  color: v-bind(color);
}
</style>
```

Sözdizimi, [`<script setup>`](./sfc-script-setup) ile çalışır ve JavaScript ifadelerini destekler (tırnak işaretleri içine alınmalıdır):

```vue
<script setup>
import { ref } from 'vue'
const theme = ref({
    color: 'red',
})
</script>

<template>
  <p>merhaba</p>
</template>

<style scoped>
p {
  color: v-bind('theme.color');
}
</style>
```

Gerçek değer, karma bir CSS özel özelliğine derleneceğinden CSS hala statiktir. Özel özellik, bileşenin kök öğesine satır içi stiller aracılığıyla uygulanacak ve kaynak değeri değişirse reaktif olarak güncellenecektir.
