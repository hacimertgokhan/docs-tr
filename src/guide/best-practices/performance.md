---
outline: deep
---

# Performans {#performans}

## Genel Bakış {#genel-bakış}

Vue, manuel optimizasyonlara fazla ihtiyaç duymadan en yaygın kullanım durumları için performanslı olacak şekilde tasarlanmıştır. Ancak, ekstra ince ayar yapılması gereken zorlu senaryolar her zaman vardır. Bu bölümde, Vue uygulamasında performans söz konusu olduğunda nelere dikkat etmeniz gerektiğini tartışacağız.

Öncelikle, web performansının iki ana yönünü tartışalım:

-   **Sayfa Yükleme Performansı**: Uygulamanın ilk ziyarette içeriği ne kadar hızlı gösterdiği ve etkileşimli hale geldiği. Bu genellikle [En Büyük İçerikli Boyama (LCP)](https://web.dev/lcp/) ve [İlk Giriş Gecikmesi (FID)](https://web.dev/fid/) gibi temel web metrikleri kullanılarak ölçülür.

-   **Güncelleme Performansı**: Uygulamanın kullanıcı girdisine yanıt olarak ne kadar hızlı güncellendiği. Örneğin, kullanıcı bir arama kutusuna yazdığında bir liste ne kadar hızlı güncellenir veya kullanıcı Tek Sayfa Uygulamasında (SPA) bir gezinme bağlantısını tıkladığında sayfa ne kadar hızlı değişir.

Her ikisini de en üst düzeye çıkarmak ideal olsa da, farklı ön uç mimarileri bu yönlerde istenen performansı elde etmeyi ne kadar kolay etkilemektedir. Ek olarak, oluşturduğunuz uygulama türü, performans açısından neleri önceliklendirmeniz gerektiğini büyük ölçüde etkiler. Bu nedenle, optimum performansı sağlamanın ilk adımı, oluşturduğunuz uygulama türü için doğru mimariyi seçmektir:

-   Vue'yu farklı şekillerde nasıl kullanabileceğinizi görmek için [Vue Kullanım Yolları](/guide/extras/ways-of-using-vue) bölümüne bakın.

-   Jason Miller, [Uygulama Holotipleri](https://jasonformat.com/application-holotypes/) bölümünde web uygulamalarının türlerini ve bunların ilgili ideal uygulama/teslimatını tartışıyor.

## Profilleme Seçenekleri {#profilleme-seçenekleri}

Performansı artırmak için öncelikle onu nasıl ölçeceğimizi bilmemiz gerekir. Bu konuda yardımcı olabilecek bir dizi harika araç var:

Üretim dağıtımlarının yükleme performansını profillemek için:

-   [PageSpeed Insights](https://pagespeed.web.dev/)
-   [WebPageTest](https://www.webpagetest.org/)

Yerel geliştirme sırasında performansı profillemek için:

-   [Chrome DevTools Performans Paneli](https://developer.chrome.com/docs/devtools/evaluate-performance/)
  -   [`app.config.performance`](/api/application#app-config-performance), Chrome DevTools'un performans zaman çizelgesinde Vue'ya özgü performans işaretleyicilerini etkinleştirir.
-   [Vue DevTools Uzantısı](/guide/scaling-up/tooling#browser-devtools) da bir performans profilleme özelliği sağlar.

## Sayfa Yükleme Optimizasyonları {#sayfa-yükleme-optimizasyonları}

Sayfa yükleme performansını optimize etmek için birçok çerçeveden bağımsız yön vardır - kapsamlı bir özet için [bu web.dev kılavuzuna](https://web.dev/fast/) göz atın. Burada, öncelikle Vue'ya özgü tekniklere odaklanacağız.

### Doğru Mimarinin Seçilmesi {#doğru-mimarinin-seçilmesi}

Kullanım durumunuz sayfa yükleme performansına duyarlıysa, onu saf bir istemci tarafı SPA olarak göndermekten kaçının. Sunucunuzun, kullanıcıların görmek istediği içeriği içeren HTML'yi doğrudan göndermesini istersiniz. Saf istemci tarafı oluşturma, içeriğe yavaş ulaşma süresinden muzdariptir. Bu, [Sunucu Tarafı Oluşturma (SSR)](/guide/extras/ways-of-using-vue#fullstack-ssr) veya [Statik Site Üretimi (SSG)](/guide/extras/ways-of-using-vue#jamstack-ssg) ile hafifletilebilir. Vue ile SSR'yi gerçekleştirme hakkında bilgi edinmek için [SSR Kılavuzuna](/guide/scaling-up/ssr) göz atın. Uygulamanızın zengin etkileşim gereksinimleri yoksa, HTML'yi işlemek ve istemcide Vue ile geliştirmek için geleneksel bir arka uç sunucusu da kullanabilirsiniz.

Ana uygulamanızın bir SPA olması gerekiyorsa, ancak pazarlama sayfaları (açılış, hakkında, blog) varsa, bunları ayrı olarak gönderin! Pazarlama sayfalarınız ideal olarak SSG kullanılarak minimum JS ile statik HTML olarak dağıtılmalıdır.

### Paket Boyutu ve Ağaç Sallama {#paket-boyutu-ve-ağaç-sallama}

Sayfa yükleme performansını iyileştirmenin en etkili yollarından biri daha küçük JavaScript paketleri göndermektir. Vue kullanırken paket boyutunu azaltmanın birkaç yolu şunlardır:

-   Mümkünse bir derleme adımı kullanın.

  -   Vue'nun API'lerinin çoğu, modern bir derleme aracıyla paketlenirse ["ağaç sallanabilir"](https://developer.mozilla.org/en-US/docs/Glossary/Tree_shaking). Örneğin, yerleşik `<Transition>` bileşenini kullanmazsanız, son üretim paketine dahil edilmez. Ağaç sallama, kaynak kodunuzdaki diğer kullanılmayan modülleri de kaldırabilir.

  -   Bir derleme adımı kullanırken, şablonlar önceden derlenir, böylece Vue derleyicisini tarayıcıya göndermemiz gerekmez. Bu, **14 kb** min+gzipped JavaScript'i kurtarır ve çalışma zamanı derleme maliyetinden kaçınır.

-   Yeni bağımlılıklar tanıtırken boyut konusunda dikkatli olun! Gerçek dünya uygulamalarında, şişirilmiş paketler en sık farkında olmadan ağır bağımlılıklar tanıtmanın bir sonucudur.

  -   Bir derleme adımı kullanılıyorsa, ES modülü biçimleri sunan ve ağaç sallamaya uygun olan bağımlılıkları tercih edin. Örneğin, `lodash` yerine `lodash-es` tercih edin.

  -   Bir bağımlılığın boyutunu kontrol edin ve sağladığı işlevselliğe değip değmediğini değerlendirin. Bağımlılığın ağaç sallamaya uygun olup olmadığını unutmayın; gerçek boyut artışı, ondan gerçekten içe aktardığınız API'lere bağlı olacaktır. [bundlejs.com](https://bundlejs.com/) gibi araçlar hızlı kontroller için kullanılabilir, ancak gerçek derleme kurulumunuzla ölçüm yapmak her zaman en doğru olacaktır.

-   Öncelikle aşamalı geliştirme için Vue kullanıyorsanız ve bir derleme adımından kaçınmayı tercih ediyorsanız, bunun yerine [petite-vue](https://github.com/vuejs/petite-vue) (**yalnızca 6kb**) kullanmayı düşünün.

### Kod Bölme {#kod-bölme}

Kod bölme, bir derleme aracının uygulama paketini daha küçük, birden çok parçaya böldüğü ve bunların daha sonra isteğe bağlı olarak veya paralel olarak yüklenebildiği yerdir. Doğru kod bölme ile, sayfa yüklemede gerekli özellikler hemen indirilebilir, ek parçalar yalnızca gerektiğinde tembel bir şekilde yüklenir, böylece performans artırılır.

Rollup (Vite'nin dayandığı) veya webpack gibi paketleyiciler, ESM dinamik içe aktarma sözdizimini algılayarak otomatik olarak bölünmüş parçalar oluşturabilir:

```js
// lazy.js ve bağımlılıkları ayrı bir parçaya bölünecek
// ve yalnızca `loadLazy()` çağrıldığında yüklenecek.
function loadLazy() {
  return import('./lazy.js')
}
```

Tembel yükleme, ilk sayfa yüklemesinden hemen sonra gerekli olmayan özelliklerde en iyi şekilde kullanılır. Vue uygulamalarında bu, bileşen ağaçları için bölünmüş parçalar oluşturmak için Vue'nun [Eşzamansız Bileşen](/guide/components/async) özelliğiyle birlikte kullanılabilir:

```js
import { defineAsyncComponent } from 'vue'

// Foo.vue ve bağımlılıkları için ayrı bir parça oluşturulur.
// yalnızca eşzamansız bileşen
// sayfada işlendiğinde isteğe bağlı olarak getirilir.
const Foo = defineAsyncComponent(() => import('./Foo.vue'))
```

Vue Router kullanan uygulamalar için, rota bileşenleri için tembel yükleme kullanılması şiddetle tavsiye edilir. Vue Router, `defineAsyncComponent`'ten ayrı olarak tembel yükleme için açık desteğe sahiptir. Daha fazla ayrıntı için [Tembel Yükleme Rotaları](https://router.vuejs.org/guide/advanced/lazy-loading.html) bölümüne bakın.

## Güncelleme Optimizasyonları {#güncelleme-optimizasyonları}

### Prop İstikrarı {#prop-istikrarı}

Vue'da, bir alt bileşen yalnızca aldığı prop'larından en az biri değiştiğinde güncellenir. Aşağıdaki örneği inceleyin:

```vue-html
<ListItem
  v-for="item in list"
  :id="item.id"
  :active-id="activeId" />
```

`<ListItem>` bileşeninin içinde, o anda etkin olan öğe olup olmadığını belirlemek için `id` ve `activeId` prop'larını kullanır. Bu işe yararken sorun şu ki, `activeId` her değiştiğinde listedeki **her** `<ListItem>`'ın güncellenmesi gerekiyor!

İdeal olarak, yalnızca etkin durumu değişen öğeler güncellenmelidir. Etkin durum hesaplamasını ana öğeye taşıyarak ve `<ListItem>`'ın bunun yerine doğrudan bir `active` prop'u kabul etmesini sağlayarak bunu başarabiliriz:

```vue-html
<ListItem
  v-for="item in list"
  :id="item.id"
  :active="item.id === activeId" />
```

Şimdi, çoğu bileşen için `active` prop'u `activeId` değiştiğinde aynı kalacak, bu nedenle artık güncellenmeleri gerekmiyor. Genel olarak, fikir, alt bileşenlere geçirilen prop'ları olabildiğince kararlı tutmaktır.

### `v-once` {#v-once}

`v-once`, çalışma zamanı verilerine dayanan ancak asla güncellenmesi gerekmeyen içeriği işlemek için kullanılabilen yerleşik bir direktiftir. Üzerinde kullanıldığı tüm alt ağaç, gelecekteki tüm güncellemeler için atlanacaktır. Daha fazla ayrıntı için [API referansına](/api/built-in-directives#v-once) bakın.

### `v-memo` {#v-memo}

`v-memo`, büyük alt ağaçların veya `v-for` listelerinin güncellenmesini koşullu olarak atlamak için kullanılabilen yerleşik bir direktiftir. Daha fazla ayrıntı için [API referansına](/api/built-in-directives#v-memo) bakın.

### Hesaplanan İstikrar {#hesaplanan-istikrar}

Vue 3.4 ve üzeri sürümlerde, hesaplanmış bir özellik yalnızca hesaplanan değeri bir öncekinden değiştiğinde efektleri tetikler. Örneğin, aşağıdaki `isEven` hesaplanmış özelliği, döndürülen değer `true` değerinden `false` değerine veya tam tersi şekilde değiştiyse efektleri tetikler:

```js
const count = ref(0)
const isEven = computed(() => count.value % 2 === 0)

watchEffect(() => console.log(isEven.value)) // true

// hesaplanan değer `true` olarak kaldığı için yeni günlükleri tetiklemeyecek
count.value = 2
count.value = 4
```

Bu, gereksiz efekt tetikleyicilerini azaltır, ancak hesaplanan her hesaplamada yeni bir nesne oluşturursa ne yazık ki işe yaramaz:

```js
const computedObj = computed(() => {
  return {
    isEven: count.value % 2 === 0
  }
})
```

Her seferinde yeni bir nesne oluşturulduğundan, yeni değer teknik olarak her zaman eski değerden farklıdır. `isEven` özelliği aynı kalsa bile, Vue eski değeri ve yeni değeri derinlemesine karşılaştırmadıkça bunu bilemez. Bu tür bir karşılaştırma pahalı olabilir ve büyük olasılıkla buna değmez.

Bunun yerine, yeni değeri eski değerle manuel olarak karşılaştırarak ve hiçbir şeyin değişmediğini biliyorsak koşullu olarak eski değeri döndürerek bunu optimize edebiliriz:

```js
const computedObj = computed((oldValue) => {
  const newValue = {
    isEven: count.value % 2 === 0
  }
  if (oldValue && oldValue.isEven === newValue.isEven) {
    return oldValue
  }
  return newValue
})
```

[Oyun alanında deneyin](https://play.vuejs.org/#eNqVVMtu2zAQ/JUFgSZK4UpuczMkow/40AJ9IC3aQ9mDIlG2EokUyKVt1PC/d0lKtoEminMQQC1nZ4c7S+7Yu66L11awGUtNoesOwQi03ZzLuu2URtiBFtUECtV2FkU5gU2OxWpRVaJA2EOlVQuXxHDJJZeFkgYJayVC5hKj6dUxLnzSjZXmV40rZfFrh3Vb/82xVrLH//5DCQNNKPkweNiNVFP+zBsrIJvDjksgGrRahjVAbRZrIWdBVLz2yBfwBrIsg6mD7LncPyryfIVnywupUmz68HOEEqqCI+XFBQzrOKR79MDdx66GCn1jhpQDZx8f0oZ+nBgdRVcH/aMuBt1xZ80qGvGvh/X6nlXwnGpPl6qsLLxTtitzFFTNl0oSN/79AKOCHHQuS5pw4XorbXsr9ImHZN7nHFdx1SilI78MeOJ7Ca+nbvgd+GgomQOv6CNjSQqXaRJuHd03+kHRdg3JoT+A3a7XsfcmpbcWkQS/LZq6uM84C8o5m4fFuOg0CemeOXXX2w2E6ylsgj2gTgeYio/f1l5UEqj+Z3yC7lGuNDlpApswNNTrql7Gd0ZJeqW8TZw5t+tGaMdDXnA2G4acs7xp1OaTj6G2YjLEi5Uo7h+I35mti3H2TQsj9Jp6etjDXC8Fhu3F9y9iS+vDZqtK2xB6ZPNGGNVYpzHA3ltZkuwTnFf70b+1tVz+MIstCmmGQzmh/p56PGf00H4YOfpR7nV8PTxubP8P2GAP9Q==)

Eski değeri karşılaştırmadan ve döndürmeden önce her zaman tam hesaplamayı yapmanız gerektiğini unutmayın, böylece her çalıştırmada aynı bağımlılıklar toplanabilir.

## Genel Optimizasyonlar {#genel-optimizasyonlar}

> Aşağıdaki ipuçları hem sayfa yükleme hem de güncelleme performansını etkiler.

### Büyük Listeleri Sanallaştırma {#büyük-listeleri-sanallaştırma}

Tüm ön uç uygulamalarındaki en yaygın performans sorunlarından biri büyük listeleri işlemektir. Bir çerçeve ne kadar performanslı olursa olsun, tarayıcının işlemesi gereken çok sayıda DOM düğümü nedeniyle binlerce öğe içeren bir liste işlemek **yavaş** olacaktır.

Ancak, tüm bu düğümleri önceden oluşturmamız gerekmez. Çoğu durumda, kullanıcının ekran boyutu, büyük listemizin yalnızca küçük bir alt kümesini görüntüleyebilir. Büyük bir listede yalnızca mevcut görüntü alanında veya yakında bulunan öğeleri işleme tekniği olan **liste sanallaştırma** ile performansı büyük ölçüde iyileştirebiliriz.

Liste sanallaştırmasını uygulamak kolay değildir, neyse ki doğrudan kullanabileceğiniz mevcut topluluk kitaplıkları vardır:

-   [vue-virtual-scroller](https://github.com/Akryum/vue-virtual-scroller)
-   [vue-virtual-scroll-grid](https://github.com/rocwang/vue-virtual-scroll-grid)
-   [vueuc/VVirtualList](https://github.com/07akioni/vueuc)

### Büyük Değişmez Yapılar İçin Reaktivite Yükünü Azaltma {#büyük-değişmez-yapılar-için-reaktivite-yükünü-azaltma}

Vue'nun reaktivite sistemi varsayılan olarak derindir. Bu, durum yönetimini sezgisel hale getirirken, veri boyutu büyük olduğunda belirli bir düzeyde yük oluşturur, çünkü her özellik erişimi, bağımlılık izlemesi gerçekleştiren proxy tuzaklarını tetikler. Bu, genellikle, tek bir işlemin 100.000'den fazla özelliğe erişmesi gereken derin iç içe yerleştirilmiş nesnelerin büyük dizileriyle uğraşırken fark edilir hale gelir, bu nedenle yalnızca çok özel kullanım durumlarını etkilemelidir.

Vue, [`shallowRef()`](/api/reactivity-advanced#shallowref) ve [`shallowReactive()`](/api/reactivity-advanced#shallowreactive) kullanarak derin reaktiviteden vazgeçmek için bir çıkış yolu sağlar. Sığ API'ler yalnızca kök düzeyinde reaktif olan ve tüm iç içe yerleştirilmiş nesneleri dokunulmamış olarak ortaya çıkaran bir durum oluşturur. Bu, tüm iç içe yerleştirilmiş nesneleri değiştirilemez olarak ele almamız ve güncellemelerin yalnızca kök durumu değiştirerek tetiklenebileceği ticareti ile iç içe yerleştirilmiş özellik erişimini hızlı tutar:

```js
const shallowArray = shallowRef([
  /* derin nesnelerin büyük listesi */
])

// bu güncellemeleri tetiklemeyecek...
shallowArray.value.push(newObject)
// bu tetikleyecek:
shallowArray.value = [...shallowArray.value, newObject]

// bu güncellemeleri tetiklemeyecek...
shallowArray.value[0].foo = 1
// bu tetikleyecek:
shallowArray.value = [
  {
    ...shallowArray.value[0],
    foo: 1
  },
  ...shallowArray.value.slice(1)
]
```

### Gereksiz Bileşen Soyutlamalarından Kaçının {#gereksiz-bileşen-soyutlamalarından-kaçının}

Bazen daha iyi soyutlama veya kod organizasyonu için [rendersiz bileşenler](/guide/components/slots#renderless-components) veya yüksek sıralı bileşenler (yani, diğer bileşenleri ekstra prop'larla işleyen bileşenler) oluşturabiliriz. Bunda yanlış bir şey olmamakla birlikte, bileşen örneklerinin düz DOM düğümlerinden çok daha maliyetli olduğunu ve soyutlama kalıpları nedeniyle çok fazla oluşturmanın performans maliyetlerine neden olacağını unutmayın.

Yalnızca birkaç örneğin azaltılmasının fark edilir bir etkisi olmayacağını unutmayın, bu nedenle bileşen uygulamada yalnızca birkaç kez işleniyorsa endişelenmeyin. Bu optimizasyonu değerlendirmek için en iyi senaryo yine büyük listelerdedir. Her öğe bileşeninin birçok alt bileşen içerdiği 100 öğelik bir liste hayal edin. Burada gereksiz bir bileşen soyutlamasını kaldırmak, yüzlerce bileşen örneğinin azaltılmasıyla sonuçlanabilir.
