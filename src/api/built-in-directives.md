# Dahili Direktifler {#dahili-direktifler}

## v-text {#v-text}

Öğenin metin içeriğini güncelleyin.

- **Beklenen:** `string`

- **Detaylar**

  `v-text`, öğenin [textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent) özelliğini ayarlayarak çalışır, bu nedenle öğenin içindeki mevcut herhangi bir içeriğin üzerine yazacaktır. `textContent`'in bir bölümünü güncellemeniz gerekiyorsa, bunun yerine [bıyık enterpolasyonlarını](/guide/essentials/template-syntax#text-interpolation) kullanmalısınız.

- **Örnek**

  ```vue-html
  <span v-text="msg"></span>
  <!-- aynı şey -->
  <span>{{msg}}</span>
  ```

- **Ayrıca bakınız** [Şablon Sözdizimi - Metin Enterpolasyonu](/guide/essentials/template-syntax#text-interpolation)

## v-html {#v-html}

Öğenin [innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML)'ini güncelleyin.

- **Beklenen:** `string`

- **Detaylar**

  `v-html`'nin içeriği düz HTML olarak eklenir - Vue şablon sözdizimi işlenmeyecektir. `v-html` kullanarak şablon oluşturmaya çalışıyorsanız, bunun yerine bileşenleri kullanarak çözümü yeniden düşünmeye çalışın.

  ::: warning Güvenlik Notu
  Web sitenizde rastgele HTML'yi dinamik olarak render etmek çok tehlikeli olabilir, çünkü kolayca [XSS saldırılarına](https://en.wikipedia.org/wiki/Cross-site_scripting) yol açabilir. `v-html`'yi yalnızca güvenilir içerikte ve **asla** kullanıcı tarafından sağlanan içerikte kullanmayın.
  :::

  [Tek Dosyalı Bileşenlerde](/guide/scaling-up/sfc), `scoped` stilleri `v-html` içindeki içeriğe uygulanmaz, çünkü bu HTML, Vue'nin şablon derleyicisi tarafından işlenmez. Kapsamlı CSS ile `v-html` içeriğini hedeflemek istiyorsanız, bunun yerine [CSS modüllerini](./sfc-css-features#css-modules) veya BEM gibi manuel bir kapsam stratejisiyle ek, global bir `<style>` öğesi kullanabilirsiniz.

- **Örnek**

  ```vue-html
  <div v-html="html"></div>
  ```

- **Ayrıca bakınız** [Şablon Sözdizimi - Ham HTML](/guide/essentials/template-syntax#raw-html)

## v-show {#v-show}

Öğenin görünürlüğünü, ifade değerinin doğruluğuna göre değiştirin.

- **Beklenen:** `any`

- **Detaylar**

  `v-show`, satır içi stiller aracılığıyla `display` CSS özelliğini ayarlayarak çalışır ve öğe görünür olduğunda başlangıç `display` değerine uymaya çalışır. Koşulu değiştiğinde geçişleri de tetikler.

- **Ayrıca bakınız** [Koşullu Render - v-show](/guide/essentials/conditional#v-show)

## v-if {#v-if}

İfade değerinin doğruluğuna göre bir öğeyi veya bir şablon parçasını koşullu olarak render edin.

- **Beklenen:** `any`

- **Detaylar**

  Bir `v-if` öğesi değiştirildiğinde, öğe ve içerdiği direktifler / bileşenler yok edilir ve yeniden oluşturulur. Başlangıç koşulu yanlışsa, iç içerik hiç render edilmeyecektir.

  Yalnızca metin veya birden çok öğe içeren koşullu bir bloğu belirtmek için `<template>` üzerinde kullanılabilir.

  Bu direktif, koşulu değiştiğinde geçişleri tetikler.

  Birlikte kullanıldığında, `v-if`, `v-for`'dan daha yüksek önceliğe sahiptir. Bu iki direktifi tek bir öğe üzerinde birlikte kullanmanızı önermiyoruz — ayrıntılar için [liste render rehberine](/guide/essentials/list#v-for-with-v-if) bakın.

- **Ayrıca bakınız** [Koşullu Render - v-if](/guide/essentials/conditional#v-if)

## v-else {#v-else}

`v-if` veya bir `v-if` / `v-else-if` zinciri için "else bloğunu" belirtin.

- **İfade beklemez**

- **Detaylar**

  - Kısıtlama: önceki kardeş öğede `v-if` veya `v-else-if` olmalıdır.

  - Yalnızca metin veya birden çok öğe içeren koşullu bir bloğu belirtmek için `<template>` üzerinde kullanılabilir.

- **Örnek**

  ```vue-html
  <div v-if="Math.random() > 0.5">
    Şimdi beni görüyorsun
  </div>
  <div v-else>
    Şimdi görmüyorsun
  </div>
  ```

- **Ayrıca bakınız** [Koşullu Render - v-else](/guide/essentials/conditional#v-else)

## v-else-if {#v-else-if}

`v-if` için "else if bloğunu" belirtin. Zincirlenebilir.

- **Beklenen:** `any`

- **Detaylar**

  - Kısıtlama: önceki kardeş öğede `v-if` veya `v-else-if` olmalıdır.

  - Yalnızca metin veya birden çok öğe içeren koşullu bir bloğu belirtmek için `<template>` üzerinde kullanılabilir.

- **Örnek**

  ```vue-html
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else-if="type === 'C'">
    C
  </div>
  <div v-else>
    A/B/C değil
  </div>
  ```

- **Ayrıca bakınız** [Koşullu Render - v-else-if](/guide/essentials/conditional#v-else-if)

## v-for {#v-for}

Kaynak verilerine göre öğeyi veya şablon bloğunu birden çok kez render edin.

- **Beklenen:** `Array | Object | number | string | Iterable`

- **Detaylar**

  Direktifin değeri, üzerinde yinelendiği geçerli öğe için bir takma ad sağlamak üzere `alias in expression` özel sözdizimini kullanmalıdır:

  ```vue-html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  Alternatif olarak, dizin için bir takma ad (veya bir Nesne üzerinde kullanılıyorsa anahtar) da belirtebilirsiniz:

  ```vue-html
  <div v-for="(item, index) in items"></div>
  <div v-for="(value, key) in object"></div>
  <div v-for="(value, name, index) in object"></div>
  ```

  `v-for`'un varsayılan davranışı, öğeleri hareket ettirmeden yerinde yama yapmaya çalışacaktır. Öğeleri yeniden sıralamaya zorlamak için, `key` özel özniteliğiyle bir sıralama ipucu sağlamalısınız:

  ```vue-html
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```

  `v-for` ayrıca, yerel `Map` ve `Set` dahil olmak üzere [Yinelenebilir Protokolü](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol)'ü uygulayan değerler üzerinde de çalışabilir.

- **Ayrıca bakınız**
  - [Liste Render](/guide/essentials/list)

## v-on {#v-on}

Öğeye bir olay dinleyicisi ekleyin.

- **Kısa yol:** `@`

- **Beklenen:** `Function | Satır İçi İfade | Nesne (argüman olmadan)`

- **Argüman:** `event` (Nesne sözdizimi kullanılıyorsa isteğe bağlı)

- **Değiştiriciler**

  - `.stop` - `event.stopPropagation()`'ı çağırın.
  - `.prevent` - `event.preventDefault()`'ı çağırın.
  - `.capture` - yakalama modunda olay dinleyicisi ekleyin.
  - `.self` - yalnızca olay bu öğeden gönderildiyse işleyiciyi tetikleyin.
  - `.{keyAlias}` - yalnızca belirli tuşlarda işleyiciyi tetikleyin.
  - `.once` - işleyiciyi en fazla bir kez tetikleyin.
  - `.left` - yalnızca sol düğme fare olayları için işleyiciyi tetikleyin.
  - `.right` - yalnızca sağ düğme fare olayları için işleyiciyi tetikleyin.
  - `.middle` - yalnızca orta düğme fare olayları için işleyiciyi tetikleyin.
  - `.passive` - ` { passive: true }` ile bir DOM olayı ekler.

- **Detaylar**

  Olay türü, argümanla belirtilir. İfade bir metot adı, satır içi bir ifade olabilir veya değiştiriciler varsa atlanabilir.

  Normal bir öğe üzerinde kullanıldığında, yalnızca [**yerel DOM olaylarını**](https://developer.mozilla.org/en-US/docs/Web/Events) dinler. Özel bir öğe bileşeni üzerinde kullanıldığında, o alt bileşende yayılan **özel olayları** dinler.

  Yerel DOM olaylarını dinlerken, metot yerel olayı tek argüman olarak alır. Satır içi ifade kullanılıyorsa, ifade özel `$event` özelliğine erişebilir: `v-on:click="handle('ok', $event)"`.

  `v-on`, ayrıca bir olay / dinleyici çiftleri nesnesine argüman olmadan bağlanmayı destekler. Nesne sözdizimini kullanırken, herhangi bir değiştiriciyi desteklemediğini unutmayın.

- **Örnek**

  ```vue-html
  <!-- metot işleyicisi -->
  <button v-on:click="doThis"></button>

  <!-- dinamik olay -->
  <button v-on:[event]="doThis"></button>

  <!-- satır içi ifade -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- kısa yol -->
  <button @click="doThis"></button>

  <!-- kısa yol dinamik olay -->
  <button @[event]="doThis"></button>

  <!-- durdurma yayılımı -->
  <button @click.stop="doThis"></button>

  <!-- önleme varsayılanı -->
  <button @click.prevent="doThis"></button>

  <!-- ifadesiz varsayılanı önleme -->
  <form @submit.prevent></form>

  <!-- zincir değiştiriciler -->
  <button @click.stop.prevent="doThis"></button>

  <!-- keyAlias kullanarak tuş değiştiricisi -->
  <input @keyup.enter="onEnter" />

  <!-- tıklama olayı en fazla bir kez tetiklenecektir -->
  <button v-on:click.once="doThis"></button>

  <!-- nesne sözdizimi -->
  <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
  ```

  Bir alt bileşende özel olayları dinleme ("my-event" alt öğede yayımlandığında işleyici çağrılır):

  ```vue-html
  <MyComponent @my-event="handleThis" />

  <!-- satır içi ifade -->
  <MyComponent @my-event="handleThis(123, $event)" />
  ```

- **Ayrıca bakınız**
  - [Olay İşleme](/guide/essentials/event-handling)
  - [Bileşenler - Özel Olaylar](/guide/essentials/component-basics#listening-to-events)

## v-bind {#v-bind}

Bir veya daha fazla özniteliği veya bir bileşen prop'unu dinamik olarak bir ifadeye bağlayın.

- **Kısa yol:**
  - `:` veya `.` (`.prop` değiştiricisi kullanılırken)
  - Değerin atlanması (öznitelik ve bağlı değer aynı ada sahip olduğunda, 3.4+ sürümünü gerektirir)

- **Beklenen:** `any (argümanlı) | Nesne (argümansız)`

- **Argüman:** `attrOrProp (isteğe bağlı)`

- **Değiştiriciler**

  - `.camel` - kebab-case öznitelik adını camelCase'e dönüştürün.
  - `.prop` - bağlamanın bir DOM özelliği olarak ayarlanmasını zorlar (3.2+).
  - `.attr` - bağlamanın bir DOM özniteliği olarak ayarlanmasını zorlar (3.2+).

- **Kullanım**

  `class` veya `style` özniteliğini bağlamak için kullanıldığında, `v-bind`, Dizi veya Nesneler gibi ek değer türlerini destekler. Daha fazla ayrıntı için aşağıdaki bağlantılı kılavuz bölümüne bakın.

  Bir öğede bağlama ayarlarken, Vue varsayılan olarak öğenin `in` operatör kontrolünü kullanarak bir özellik olarak tanımlanan anahtara sahip olup olmadığını kontrol eder. Özellik tanımlanmışsa, Vue değeri bir öznitelik yerine DOM özelliği olarak ayarlayacaktır. Bu çoğu durumda işe yaramalıdır, ancak `.prop` veya `.attr` değiştiricilerini açıkça kullanarak bu davranışı geçersiz kılabilirsiniz. Bu, özellikle [özel öğelerle çalışırken](/guide/extras/web-components#passing-dom-properties) bazen gereklidir.

  Bileşen prop bağlama için kullanıldığında, prop alt bileşende uygun şekilde bildirilmelidir.

  Bir argüman olmadan kullanıldığında, öznitelik adı-değer çiftleri içeren bir nesneyi bağlamak için kullanılabilir.

- **Örnek**

  ```vue-html
  <!-- bir özniteliği bağla -->
  <img v-bind:src="imageSrc" />

  <!-- dinamik öznitelik adı -->
  <button v-bind:[key]="value"></button>

  <!-- kısa yol -->
  <img :src="imageSrc" />

   <!-- aynı ad kısa yolu (3.4+), :src="src" olarak genişler -->
  <img :src />

  <!-- kısa yol dinamik öznitelik adı -->
  <button :[key]="value"></button>

  <!-- satır içi dize birleştirmeyle -->
  <img :src="'/path/to/images/' + fileName" />

  <!-- sınıf bağlama -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]"></div>

  <!-- stil bağlama -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- özniteliklerin nesnesini bağlama -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!-- prop bağlama. "prop", alt bileşende bildirilmelidir. -->
  <MyComponent :prop="someThing" />

  <!-- bir alt bileşenle ortak olan üst prop'ları aşağı aktar -->
  <MyComponent v-bind="$props" />

  <!-- XLink -->
  <svg><a :xlink:special="foo"></a></svg>
  ```

  `.prop` değiştiricisinin özel bir kısa yolu da vardır: `.`:

  ```vue-html
  <div :someProperty.prop="someObject"></div>

  <!-- eşdeğer -->
  <div .someProperty="someObject"></div>
  ```

  `.camel` değiştiricisi, DOM içi şablonlar kullanırken bir `v-bind` öznitelik adının, örn. SVG `viewBox` özniteliği:

  ```vue-html
  <svg :view-box.camel="viewBox"></svg>
  ```

  Dize şablonları kullanıyorsanız veya şablonu bir derleme adımıyla önceden derliyorsanız, `.camel` gerekmez.

- **Ayrıca bakınız**
  - [Sınıf ve Stil Bağlamaları](/guide/essentials/class-and-style)
  - [Bileşenler - Prop Geçirme Ayrıntıları](/guide/components/props#prop-passing-details)

## v-model {#v-model}

Bir form giriş öğesinde veya bir bileşende çift yönlü bir bağlama oluşturun.

- **Beklenen:** form girişleri öğesinin değerine veya bileşenlerin çıktısına bağlı olarak değişir

- **Sınırlı olduğu yerler:**

  - `<input>`
  - `<select>`
  - `<textarea>`
  - bileşenler

- **Değiştiriciler**

  - [`.lazy`](/guide/essentials/forms#lazy) - `input` yerine `change` olaylarını dinleyin
  - [`.number`](/guide/essentials/forms#number) - geçerli giriş dizelerini sayılara yayın
  - [`.trim`](/guide/essentials/forms#trim) - girişi kırp

- **Ayrıca bakınız**

  - [Form Giriş Bağlamaları](/guide/essentials/forms)
  - [Bileşen Olayları - `v-model` ile Kullanım](/guide/components/v-model)

## v-slot {#v-slot}

Prop'ları almayı bekleyen adlandırılmış yuvaları veya kapsamlı yuvaları belirtin.

- **Kısa yol:** `#`

- **Beklenen:** Yıkma desteği de dahil olmak üzere, bir fonksiyon argüman konumu için geçerli olan JavaScript ifadesi. İsteğe bağlı - yalnızca yuvaya props'ların iletilmesini bekliyorsanız gereklidir.

- **Argüman:** yuva adı (isteğe bağlı, varsayılan olarak `default`)

- **Sınırlı olduğu yerler:**

  - `<template>`
  - [bileşenler](/guide/components/slots#scoped-slots) (prop'larla tek bir varsayılan yuva için)

- **Örnek**

  ```vue-html
  <!-- Adlandırılmış yuvalar -->
  <BaseLayout>
    <template v-slot:header>
      Başlık içeriği
    </template>

    <template v-slot:default>
      Varsayılan yuva içeriği
    </template>

    <template v-slot:footer>
      Alt bilgi içeriği
    </template>
  </BaseLayout>

  <!-- prop'ları alan adlandırılmış yuva -->
  <InfiniteScroll>
    <template v-slot:item="slotProps">
      <div class="item">
        {{ slotProps.item.text }}
      </div>
    </template>
  </InfiniteScroll>

  <!-- Yıkma ile prop'ları alan varsayılan yuva -->
  <Mouse v-slot="{ x, y }">
    Fare konumu: {{ x }}, {{ y }}
  </Mouse>
  ```

- **Ayrıca bakınız**
  - [Bileşenler - Yuvalar](/guide/components/slots)

## v-pre {#v-pre}

Bu öğe ve tüm alt öğeleri için derlemeyi atlayın.

- **İfade beklemez**

- **Detaylar**

  `v-pre`'li öğenin içinde, tüm Vue şablon sözdizimi korunacak ve olduğu gibi render edilecektir. Bunun en yaygın kullanım örneği, ham bıyık etiketlerini görüntülemektir.

- **Örnek**

  ```vue-html
  <span v-pre>{{ bu derlenmeyecek }}</span>
  ```

## v-once {#v-once}

Öğeyi ve bileşeni yalnızca bir kez render edin ve gelecekteki güncellemeleri atlayın.

- **İfade beklemez**

- **Detaylar**

  Sonraki yeniden renderlamalarda, öğe/bileşen ve tüm alt öğeleri statik içerik olarak ele alınacak ve atlanacaktır. Bu, güncelleme performansını optimize etmek için kullanılabilir.

  ```vue-html
  <!-- tek öğe -->
  <span v-once>Bu asla değişmeyecek: {{msg}}</span>
  <!-- öğenin alt öğeleri var -->
  <div v-once>
    <h1>Yorum</h1>
    <p>{{msg}}</p>
  </div>
  <!-- bileşen -->
  <MyComponent v-once :comment="msg"></MyComponent>
  <!-- `v-for` direktifi -->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

  3.2'den itibaren, [`v-memo`](#v-memo) kullanarak geçersiz kılma koşullarıyla şablonun bir bölümünü de ezberleyebilirsiniz.

- **Ayrıca bakınız**
  - [Veri Bağlama Sözdizimi - enterpolasyonlar](/guide/essentials/template-syntax#text-interpolation)
  - [v-memo](#v-memo)

## v-memo {#v-memo}

- Yalnızca 3.2+ sürümlerinde desteklenir

- **Beklenen:** `any[]`

- **Detaylar**

  Şablonun bir alt ağacını ezberleyin. Hem öğeler hem de bileşenler üzerinde kullanılabilir. Direktif, ezberleme için karşılaştırmak üzere sabit uzunlukta bir bağımlılık değeri dizisi bekler. Dizideki her değer, son render ile aynıysa, tüm alt ağaç için güncellemeler atlanacaktır. Örneğin:

  ```vue-html
  <div v-memo="[valueA, valueB]">
    ...
  </div>
  ```

  Bileşen yeniden render olduğunda, hem `valueA` hem de `valueB` aynı kalırsa, bu `<div>` ve alt öğeleri için tüm güncellemeler atlanacaktır. Aslında, alt ağacın ezberlenmiş kopyası yeniden kullanılabileceğinden, Sanal DOM VNode oluşturma da atlanacaktır.

  Ezberleme dizisini doğru şekilde belirtmek önemlidir, aksi takdirde aslında uygulanması gereken güncellemeleri atlayabiliriz. Boş bir bağımlılık dizisine sahip `v-memo` (`v-memo="[]"`) işlevsel olarak `v-once`'e eşdeğer olacaktır.

  **`v-for` ile kullanım**

  `v-memo`, yalnızca performansa duyarlı senaryolarda mikro optimizasyonlar için sağlanır ve nadiren ihtiyaç duyulmalıdır. Bunun yararlı olabileceği en yaygın durum, büyük `v-for` listeleri (burada `length > 1000`) render ederken geçerlidir:

  ```vue-html
  <div v-for="item in list" :key="item.id" v-memo="[item.id === selected]">
    <p>ID: {{ item.id }} - seçildi: {{ item.id === selected }}</p>
    <p>...daha fazla alt düğüm</p>
  </div>
  ```

  Bileşenin `selected` durumu değiştiğinde, öğelerin çoğu tam olarak aynı kalsa bile çok sayıda VNode oluşturulacaktır. Buradaki `v-memo` kullanımı temelde "yalnızca bu öğe seçili olmayan durumdan seçili duruma veya tam tersine geçtiyse güncelleyin" diyor. Bu, etkilenmeyen her öğenin önceki VNode'unu yeniden kullanmasına ve tamamen farklılaşmayı atlamasına olanak tanır. Burada `item.id`'yi ezberleme bağımlılık dizisine dahil etmemize gerek olmadığını unutmayın, çünkü Vue bunu öğenin `:key`'inden otomatik olarak çıkarır.

  :::warning
  `v-for` ile `v-memo` kullanırken, aynı öğe üzerinde kullanıldığından emin olun. **`v-memo` `v-for` içinde çalışmaz.**
  :::

  `v-memo`, alt bileşen güncelleme kontrolünün optimize edildiği belirli uç durumlarda istenmeyen güncellemeleri manuel olarak önlemek için bileşenlerde de kullanılabilir. Ancak yine, gerekli güncellemeleri atlamaktan kaçınmak için doğru bağımlılık dizilerini belirtmek geliştiricinin sorumluluğundadır.

- **Ayrıca bakınız**
  - [v-once](#v-once)

## v-cloak {#v-cloak}

Derlenmemiş şablonu hazır olana kadar gizlemek için kullanılır.

- **İfade beklemez**

- **Detaylar**

  **Bu direktif yalnızca derleme adımı olmayan kurulumlarda gereklidir.**

  DOM içi şablonlar kullanırken, "derlenmemiş şablonların parlaması" olabilir: kullanıcı, monte edilmiş bileşen bunları render edilmiş içerikle değiştirene kadar ham bıyık etiketlerini görebilir.

  `v-cloak`, ilişkili bileşen örneği monte edilene kadar öğede kalacaktır. `[v-cloak] { display: none }` gibi CSS kurallarıyla birlikte, bileşen hazır olana kadar ham şablonları gizlemek için kullanılabilir.

- **Örnek**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```vue-html
  <div v-cloak>
    {{ message }}
  </div>
  ```

  `<div>`, derleme tamamlanana kadar görünür olmayacaktır.
