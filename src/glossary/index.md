# Sözlük {#sözlük}

Bu sözlük, Vue hakkında konuşurken yaygın olarak kullanılan teknik terimlerin anlamları hakkında rehberlik sağlamayı amaçlamaktadır. Terimlerin yaygın olarak nasıl kullanıldığını *tanımlayıcı* bir şekilde açıklamayı hedefler, nasıl kullanılması gerektiğine dair *kuralcı* bir belirtme değildir. Bazı terimler, çevreleyen bağlama bağlı olarak biraz farklı anlamlara veya nüanslara sahip olabilir.

[[TOC]]

## eşzamansız bileşen {#eşzamansız-bileşen}

Bir *eşzamansız bileşen*, sarmalanan bileşenin tembel yüklenmesine olanak tanıyan başka bir bileşenin etrafındaki bir sarmalayıcıdır. Bu genellikle, oluşturulan `.js` dosyalarının boyutunu küçültmenin bir yolu olarak kullanılır ve bunların yalnızca gerektiğinde yüklenen daha küçük parçalara bölünmesine olanak tanır.

Vue Router'da [rota bileşenlerinin tembel yüklenmesi](https://router.vuejs.org/guide/advanced/lazy-loading.html) için benzer bir özellik bulunur, ancak bu, Vue'nun eşzamansız bileşenler özelliğini kullanmaz.

Daha fazla ayrıntı için bkz:
- [Rehber - Eşzamansız Bileşenler](/guide/components/async.html)

## derleyici makrosu {#derleyici-makrosu}

Bir *derleyici makrosu*, bir derleyici tarafından işlenen ve başka bir şeye dönüştürülen özel bir koddur. Bunlar etkili bir şekilde akıllı bir dize değiştirme biçimidir.

Vue'nun [SFC](#tek-dosya-bileşen) derleyicisi, `defineProps()`, `defineEmits()` ve `defineExpose()` gibi çeşitli makroları destekler. Bu makrolar, normal JavaScript işlevleri gibi görünmeleri için kasıtlı olarak tasarlanmıştır, böylece JavaScript/TypeScript etrafındaki aynı ayrıştırıcı ve tür çıkarım araçlarından yararlanabilirler. Ancak, bunlar tarayıcıda çalıştırılan gerçek işlevler değildir. Bunlar, derleyicinin algıladığı ve aslında çalışacak gerçek JavaScript koduyla değiştirdiği özel dizelerdir.

Makroların, normal JavaScript koduna uygulanmayan kullanımında sınırlamalar vardır. Örneğin, `const dp = defineProps` yazmanın `defineProps` için bir takma ad oluşturmanıza izin vereceğini düşünebilirsiniz, ancak bu aslında bir hataya yol açacaktır. Derleyici tarafından ve çalışma zamanında değil işlenmesi gerektiğinden, `defineProps()` öğesine hangi değerlerin geçirilebileceğine dair sınırlamalar da vardır.

Daha fazla ayrıntı için bkz:
- [`<script setup>` - `defineProps()` & `defineEmits()`](/api/sfc-script-setup.html#defineprops-defineemits)
- [`<script setup>` - `defineExpose()`](/api/sfc-script-setup.html#defineexpose)

## bileşen {#bileşen}

*Bileşen* terimi Vue'ya özgü değildir. Birçok UI çerçevesinde yaygındır. Bir düğme veya onay kutusu gibi UI'nin bir parçasını tanımlar. Bileşenler, daha büyük bileşenler oluşturmak için de birleştirilebilir.

Bileşenler, Vue tarafından hem sürdürülebilirliği iyileştirmek hem de kodun yeniden kullanılmasını sağlamak için bir UI'yi daha küçük parçalara ayırmak için sağlanan temel mekanizmadır.

Bir Vue bileşeni bir nesnedir. Tüm özellikler isteğe bağlıdır, ancak bileşenin işlenmesi için bir şablon veya render işlevi gereklidir. Örneğin, aşağıdaki nesne geçerli bir bileşen olacaktır:

```js
const HelloWorldComponent = {
  render() {
    return 'Merhaba dünya!'
  }
}
```

Uygulamada, çoğu Vue uygulaması [Tek Dosya Bileşenleri](#tek-dosya-bileşen) (`.vue` dosyaları) kullanılarak yazılır. Bu bileşenler ilk bakışta nesne gibi görünmese de, SFC derleyicisi bunları dosya için varsayılan dışa aktarma olarak kullanılan bir nesneye dönüştürür. Dış bir bakış açısından, bir `.vue` dosyası yalnızca bir bileşen nesnesini dışa aktaran bir ES modülüdür.

Bileşen nesnesinin özelliklerine genellikle *seçenekler* denir. [Seçenekler API'sinin](#seçenekler-api) adı buradan gelmektedir.

Bir bileşen için seçenekler, o bileşenin örneklerinin nasıl oluşturulacağını tanımlar. Bileşenler kavramsal olarak sınıflara benzer, ancak Vue onları tanımlamak için gerçek JavaScript sınıflarını kullanmaz.

Bileşen terimi, bileşen örneklerini ifade etmek için daha serbest bir şekilde de kullanılabilir.

Daha fazla ayrıntı için bkz:
- [Rehber - Bileşen Temelleri](/guide/essentials/component-basics.html)

'Bileşen' kelimesi başka birkaç terimde de yer alır:
- [eşzamansız bileşen](#eşzamansız-bileşen)
- [dinamik bileşen](#dinamik-bileşen)
- [işlevsel bileşen](#işlevsel-bileşen)
- [Web Bileşeni](#web-bileşeni)

## birleştirilebilir {#birleştirilebilir}

*Birleştirilebilir* terimi, Vue'da yaygın bir kullanım modelini tanımlar. Vue'nun ayrı bir özelliği değildir, yalnızca çerçevenin [Kompozisyon API'sini](#kompozisyon-api) kullanmanın bir yoludur.

* Birleştirilebilir bir işlevdir.
* Birleştirilebilirler, durumlu mantığı kapsüllemek ve yeniden kullanmak için kullanılır.
* İşlev adı genellikle `use` ile başlar, böylece diğer geliştiriciler bunun bir birleştirilebilir olduğunu anlar.
* İşlevin tipik olarak bir bileşenin `setup()` işlevinin (veya eşdeğer olarak bir `<script setup>` bloğunun) eşzamanlı yürütülmesi sırasında çağrılması beklenir. Bu, örneğin `provide()`, `inject()` veya `onMounted()` çağrıları yoluyla birleştirilebilir öğenin çağrılmasını mevcut bileşen bağlamına bağlar.
* Birleştirilebilirler tipik olarak reaktif bir nesne değil, düz bir nesne döndürür. Bu nesne genellikle referanslar ve işlevler içerir ve çağıran kod içinde yapılandırılmamış olması beklenir.

Birçok modelde olduğu gibi, belirli kodun etiket için uygun olup olmadığı konusunda bazı anlaşmazlıklar olabilir. Tüm JavaScript yardımcı işlevleri birleştirilebilir değildir. Bir işlev Kompozisyon API'sini kullanmıyorsa, muhtemelen birleştirilebilir değildir. `setup()` öğesinin eşzamanlı yürütülmesi sırasında çağrılması beklenmiyorsa, muhtemelen birleştirilebilir değildir. Birleştirilebilirler, özellikle durumlu mantığı kapsüllemek için kullanılır, sadece işlevler için bir adlandırma kuralı değildir.

Birleştirilebilirler yazma hakkında daha fazla ayrıntı için [Rehber - Birleştirilebilirler](/guide/reusability/composables.html) bölümüne bakın.

## Kompozisyon API'si {#kompozisyon-api}

*Kompozisyon API'si*, Vue'da bileşenler ve birleştirilebilirler yazmak için kullanılan bir işlevler koleksiyonudur.

Terim, bileşenler yazmak için kullanılan iki ana stilden birini tanımlamak için de kullanılır, diğeri [Seçenekler API'sidir](#seçenekler-api). Kompozisyon API'si kullanılarak yazılan bileşenler `<script setup>` veya açık bir `setup()` işlevi kullanır.

Daha fazla ayrıntı için [Kompozisyon API'si SSS](/guide/extras/composition-api-faq) bölümüne bakın.

## özel öğe {#özel-öğe}

*Özel öğe*, modern web tarayıcılarında uygulanan [Web Bileşenleri](#web-bileşeni) standardının bir özelliğidir. HTML işaretlemenizde sayfada o noktaya bir Web Bileşeni eklemek için özel bir HTML öğesi kullanma yeteneğini ifade eder.

Vue, özel öğeleri işlemeye yerleşik desteğe sahiptir ve bunların doğrudan Vue bileşen şablonlarında kullanılmasına izin verir.

Özel öğeler, Vue bileşenlerini başka bir Vue bileşeninin şablonunda etiket olarak ekleme yeteneğiyle karıştırılmamalıdır. Özel öğeler, Vue bileşenleri değil Web Bileşenleri oluşturmak için kullanılır.

Daha fazla ayrıntı için bkz:
- [Rehber - Vue ve Web Bileşenleri](/guide/extras/web-components.html)

## direktif {#direktif}

*Direktif* terimi, `v-` ön ekiyle başlayan şablon özniteliklerini veya bunların eşdeğer kısaltmalarını ifade eder.

Yerleşik direktifler arasında `v-if`, `v-for`, `v-bind`, `v-on` ve `v-slot` bulunur.

Vue, özel direktifler oluşturmayı da destekler, ancak bunlar genellikle yalnızca doğrudan DOM düğümlerini manipüle etmek için bir 'kaçış yolu' olarak kullanılır. Özel direktifler genellikle yerleşik direktiflerin işlevselliğini yeniden oluşturmak için kullanılamaz.

Daha fazla ayrıntı için bkz:
- [Rehber - Şablon Sözdizimi - Direktifler](/guide/essentials/template-syntax.html#directives)
- [Rehber - Özel Direktifler](/guide/reusability/custom-directives.html)

## dinamik bileşen {#dinamik-bileşen}

*Dinamik bileşen* terimi, hangi alt bileşenin işleneceğine dair seçimin dinamik olarak yapılması gereken durumları açıklamak için kullanılır. Genellikle bu, `<component :is="type">` kullanılarak gerçekleştirilir.

Dinamik bir bileşen, özel bir bileşen türü değildir. Herhangi bir bileşen dinamik bileşen olarak kullanılabilir. Bileşenin kendisinden ziyade dinamik olan bileşen seçimidir.

Daha fazla ayrıntı için bkz:
- [Rehber - Bileşen Temelleri - Dinamik Bileşenler](/guide/essentials/component-basics.html#dynamic-components)

## efekt {#efekt}

Bkz. [reaktif efekt](#reaktif-efekt) ve [yan efekt](#yan-efekt).

## olay {#olay}

Bir programın farklı bölümleri arasında iletişim kurmak için olayların kullanılması, programlamanın birçok farklı alanında yaygındır. Vue içinde, terim yaygın olarak hem yerel HTML öğesi olaylarına hem de Vue bileşen olaylarına uygulanır. Şablonlarda her iki olay türünü de dinlemek için `v-on` direktifi kullanılır.

Daha fazla ayrıntı için bkz:
- [Rehber - Olay İşleme](/guide/essentials/event-handling.html)
- [Rehber - Bileşen Olayları](/guide/components/events.html)

## parça {#parça}

*Parça* terimi, diğer VNode'ler için bir üst öğe olarak kullanılan, ancak kendisi herhangi bir öğe oluşturmayan özel bir [VNode](#vnode) türünü ifade eder.

Adı, yerel DOM API'sinde benzer bir [`DocumentFragment`](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment) kavramından gelir.

Parçalar, birden çok kök düğüme sahip bileşenleri desteklemek için kullanılır. Bu tür bileşenler birden çok köke sahip gibi görünse de, perde arkasında 'kök' düğümlerin üst öğesi olarak tek bir kök olarak bir parça düğümü kullanırlar.

Parçalar, şablon derleyicisi tarafından birden çok dinamik düğümü sarmalamanın bir yolu olarak da kullanılır, örneğin `v-for` veya `v-if` aracılığıyla oluşturulanlar. Bu, [VDOM](#sanal-dom) düzeltme algoritmasına fazladan ipuçları iletilmesine olanak tanır. Bunun çoğu dahili olarak işlenir, ancak bunu doğrudan karşılaşabileceğiniz bir yer, `v-for` ile `<template>` etiketinde bir `key` kullanmaktır. Bu senaryoda, `key` parça VNode'sine bir [prop](#prop) olarak eklenir.

Parça düğümleri şu anda DOM'a boş metin düğümleri olarak işlenir, ancak bu bir uygulama ayrıntısıdır. `$el` kullanırsanız veya yerleşik tarayıcı API'leriyle DOM'da gezinmeye çalışırsanız bu metin düğümlerine rastlayabilirsiniz.

## işlevsel bileşen {#işlevsel-bileşen}

Bir bileşen tanımı genellikle seçenekler içeren bir nesnedir. `<script setup>` kullanıyorsanız böyle görünmeyebilir, ancak `.vue` dosyasından dışa aktarılan bileşen hala bir nesne olacaktır.

*İşlevsel bir bileşen*, bunun yerine bir işlev kullanılarak bildirilen alternatif bir bileşen biçimidir. Bu işlev, bileşen için [render işlevi](#render-işlevi) olarak işlev görür.

İşlevsel bir bileşenin kendine ait hiçbir durumu olamaz. Ayrıca normal bileşen yaşam döngüsünden geçmez, bu nedenle yaşam döngüsü kancaları kullanılamaz. Bu, onları normal, durumlu bileşenlerden biraz daha hafif yapar.

Daha fazla ayrıntı için bkz:
- [Rehber - Render İşlevleri ve JSX - İşlevsel Bileşenler](/guide/extras/render-function.html#functional-components)

## yükseltme {#yükseltme}

*Yükseltme* terimi, bir kod bölümünü, diğer kodların önüne geçerek, ulaşılmadan önce çalıştırmak için kullanılır. Yürütme, daha erken bir noktaya 'yukarı çekilir'.

JavaScript, `var`, `import` ve işlev bildirimleri gibi bazı yapılar için yükseltmeyi kullanır.

Vue bağlamında, şablon derleyicisi performansı iyileştirmek için *statik yükseltme* uygular. Bir şablonu bir render işlevine dönüştürürken, statik içeriğe karşılık gelen VNode'ler bir kez oluşturulabilir ve ardından yeniden kullanılabilir. Bu statik VNode'ler, render işlevinin dışında, çalışmadan önce oluşturuldukları için yükseltilmiş olarak tanımlanır. Şablon derleyicisi tarafından oluşturulan statik nesneler veya diziler için de benzer bir yükseltme biçimi uygulanır.

Daha fazla ayrıntı için bkz:
- [Rehber - İşleme Mekanizması - Statik Yükseltme](/guide/extras/rendering-mechanism.html#static-hoisting)

## DOM içi şablon {#dom-içi-şablon}

Bir bileşen için bir şablon belirtmenin çeşitli yolları vardır. Çoğu durumda şablon bir dize olarak sağlanır.

*DOM içi şablon* terimi, şablonun bir dize yerine DOM düğümleri biçiminde sağlandığı senaryoyu ifade eder. Vue daha sonra DOM düğümlerini `innerHTML` kullanarak bir şablon dizesine dönüştürür.

Tipik olarak, bir DOM içi şablon, doğrudan sayfanın HTML'sine yazılmış HTML işaretlemesi olarak başlar. Tarayıcı daha sonra bunu DOM düğümlerine ayrıştırır ve Vue daha sonra `innerHTML` içeriğini okumak için bu düğümleri kullanır.

Daha fazla ayrıntı için bkz:
- [Rehber - Bir Uygulama Oluşturma - DOM İçi Kök Bileşen Şablonu](/guide/essentials/application.html#in-dom-root-component-template)
- [Rehber - Bileşen Temelleri - DOM İçi Şablon Ayrıştırma Uyarıları](/guide/essentials/component-basics.html#in-dom-template-parsing-caveats)
- [Seçenekler: İşleme - şablon](/api/options-rendering.html#template)

## enjekte et {#enjekte-et}

Bkz. [sağlama / enjekte et](#sağlama-enjekte-et).

## yaşam döngüsü kancaları {#yaşam-döngüsü-kancaları}

Bir Vue bileşen örneği bir yaşam döngüsünden geçer. Örneğin, oluşturulur, monte edilir, güncellenir ve sökülür.

*Yaşam döngüsü kancaları*, bu yaşam döngüsü olaylarını dinlemenin bir yoludur.

Seçenekler API'si ile her kanca ayrı bir seçenek olarak sağlanır, örneğin `mounted`. Kompozisyon API'si bunun yerine `onMounted()` gibi işlevler kullanır.

Daha fazla ayrıntı için bkz:
- [Rehber - Yaşam Döngüsü Kancaları](/guide/essentials/lifecycle.html)

## makro {#makro}

Bkz. [derleyici makrosu](#derleyici-makrosu).

## adlandırılmış yuva {#adlandırılmış-yuva}

Bir bileşen, adlarına göre ayrılan birden çok yuvaya sahip olabilir. Varsayılan yuva dışındaki yuvalara *adlandırılmış yuvalar* denir.

Daha fazla ayrıntı için bkz:
- [Rehber - Yuvalar - Adlandırılmış Yuvalar](/guide/components/slots.html#named-slots)

## Seçenekler API'si {#seçenekler-api}

Vue bileşenleri nesneler kullanılarak tanımlanır. Bu bileşen nesnelerinin özelliklerine *seçenekler* denir.

Bileşenler iki stilde yazılabilir. Bir stil, `setup` ile birlikte [Kompozisyon API'sini](#kompozisyon-api) kullanır (ya bir `setup()` seçeneği ya da `<script setup>` aracılığıyla). Diğer stil, benzer bir sonuç elde etmek için çeşitli bileşen seçenekleri kullanmak yerine, Kompozisyon API'sinin çok az doğrudan kullanımını yapar. Bu şekilde kullanılan bileşen seçeneklerine *Seçenekler API'si* denir.

Seçenekler API'si, `data()`, `computed`, `methods` ve `created()` gibi seçenekleri içerir.

`props`, `emits` ve `inheritAttrs` gibi bazı seçenekler, her iki API ile de bileşen yazılırken kullanılabilir. Bunlar bileşen seçenekleri olduğundan, Seçenekler API'sinin bir parçası olarak kabul edilebilirler. Ancak, bu seçenekler `setup()` ile birlikte de kullanıldığından, genellikle iki bileşen stili arasında paylaşılan olarak düşünmek daha kullanışlıdır.

`setup()` işlevi de bir bileşen seçeneğidir, bu nedenle Seçenekler API'sinin bir parçası olarak *tanımlanabilir*. Ancak, 'Seçenekler API'si' terimi normalde bu şekilde kullanılmaz. Bunun yerine, `setup()` işlevi Kompozisyon API'sinin bir parçası olarak kabul edilir.

## eklenti {#eklenti}

*Eklenti* terimi çok çeşitli bağlamlarda kullanılabilse de, Vue'nun bir uygulamaya işlevsellik eklemenin bir yolu olarak belirli bir eklenti kavramı vardır.

Eklentiler, `app.use(plugin)` çağrılarak bir uygulamaya eklenir. Eklentinin kendisi bir işlev veya bir `install` işlevine sahip bir nesnedir. Bu işlev uygulama örneğine geçirilir ve daha sonra yapması gereken her şeyi yapabilir.

Daha fazla ayrıntı için bkz:
- [Rehber - Eklentiler](/guide/reusability/plugins.html)

## prop {#prop}

Vue'da *prop* teriminin üç yaygın kullanımı vardır:

* Bileşen prop'ları
* VNode prop'ları
* Yuva prop'ları

*Bileşen prop'ları*, çoğu insanın prop olarak düşündüğü şeydir. Bunlar, bir bileşen tarafından ya `defineProps()` ya da `props` seçeneği kullanılarak açıkça tanımlanır.

*VNode prop'ları* terimi, `h()` öğesine ikinci bağımsız değişken olarak geçirilen nesnenin özelliklerini ifade eder. Bunlar, bileşen prop'larını içerebilir, ancak bileşen olaylarını, DOM olaylarını, DOM özniteliklerini ve DOM özelliklerini de içerebilir. Genellikle, doğrudan VNode'leri manipüle etmek için render işlevleriyle çalışıyorsanız VNode prop'larına rastlarsınız.

*Yuva prop'ları*, kapsamlı bir yuvaya geçirilen özelliklerdir.

Her durumda, prop'lar başka bir yerden geçirilen özelliklerdir.

Prop kelimesi *properties* kelimesinden türetilmiş olsa da, prop terimi Vue bağlamında çok daha özel bir anlama sahiptir. Özelliklerin kısaltması olarak kullanmaktan kaçınmalısınız.

Daha fazla ayrıntı için bkz:
- [Rehber - Prop'lar](/guide/components/props.html)
- [Rehber - Render İşlevleri ve JSX](/guide/extras/render-function.html)
- [Rehber - Yuvalar - Kapsamlı Yuvalar](/guide/components/slots.html#scoped-slots)

## sağlama / enjekte et {#sağlama-enjekte-et}

`provide` ve `inject`, bileşenler arası iletişimin bir biçimidir.

Bir bileşen bir değer *sağladığında*, o bileşenin tüm alt öğeleri daha sonra `inject` kullanarak bu değeri almayı seçebilir. Prop'larda olduğu gibi, sağlayan bileşen hangi bileşenin değeri aldığını tam olarak bilmez.

`provide` ve `inject`, bazen *prop sondajını* önlemek için kullanılır. Ayrıca bir bileşenin yuva içeriğiyle iletişim kurmasının örtük bir yolu olarak da kullanılabilirler.

`provide`, uygulama düzeyinde de kullanılabilir ve bir değeri o uygulamadaki tüm bileşenlerin kullanımına sunar.

Daha fazla ayrıntı için bkz:
- [Rehber - sağlama / enjekte et](/guide/components/provide-inject.html)

## reaktif efekt {#reaktif-efekt}

*Reaktif efekt*, Vue'nun reaktivite sisteminin bir parçasıdır. Bir işlevin bağımlılıklarını izleme ve bu bağımlılıkların değerleri değiştiğinde o işlevi yeniden çalıştırma sürecini ifade eder.

`watchEffect()`, bir efekt oluşturmanın en doğrudan yoludur. Vue'nun çeşitli diğer bölümleri efektleri dahili olarak kullanır, örneğin bileşen işleme güncellemeleri, `computed()` ve `watch()`.

Vue, yalnızca reaktif bir efekt içinde reaktif bağımlılıkları izleyebilir. Bir özelliğin değeri reaktif bir efektin dışında okunursa, Vue o özelliğin daha sonra değişmesi durumunda ne yapacağını bilmeyeceği anlamında reaktivitesini 'kaybeder'.

Terim, 'yan efekt'ten türetilmiştir. Efekt işlevini çağırmak, özellik değerinin değiştirilmesinin bir yan etkisidir.

Daha fazla ayrıntı için bkz:
- [Rehber - Derinlemesine Reaktivite](/guide/extras/reactivity-in-depth.html)

## reaktivite {#reaktivite}

Genel olarak *reaktivite*, veri değişikliklerine yanıt olarak otomatik olarak eylemler gerçekleştirme yeteneğini ifade eder. Örneğin, bir veri değeri değiştiğinde DOM'u güncellemek veya bir ağ isteği yapmak.

Vue bağlamında reaktivite, bir özellikler koleksiyonunu tanımlamak için kullanılır. Bu özellikler, [Reaktivite API'si](#reaktivite-api) aracılığıyla kullanıma sunulan bir *reaktivite sistemi* oluşturmak için birleşir.

Bir reaktivite sisteminin uygulanabileceği çeşitli farklı yollar vardır. Örneğin, bağımlılıklarını belirlemek için kodun statik analizi ile yapılabilir. Ancak, Vue bu reaktivite sistemi biçimini kullanmaz.

Bunun yerine, Vue'nun reaktivite sistemi, özellik erişimini çalışma zamanında izler. Bunu, hem Proxy sarmalayıcılarını hem de özellikler için [getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description)/[setter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set#description) işlevlerini kullanarak yapar.

Daha fazla ayrıntı için bkz:
- [Rehber - Reaktivite Temelleri](/guide/essentials/reactivity-fundamentals.html)
- [Rehber - Derinlemesine Reaktivite](/guide/extras/reactivity-in-depth.html)

## Reaktivite API'si {#reaktivite-api}

*Reaktivite API'si*, [reaktivite](#reaktivite) ile ilgili temel Vue işlevlerinin bir koleksiyonudur. Bunlar, bileşenlerden bağımsız olarak kullanılabilir. `ref()`, `reactive()`, `computed()`, `watch()` ve `watchEffect()` gibi işlevleri içerir.

Reaktivite API'si, Kompozisyon API'sinin bir alt kümesidir.

Daha fazla ayrıntı için bkz:
- [Reaktivite API'si: Çekirdek](/api/reactivity-core.html)
- [Reaktivite API'si: Yardımcı Programlar](/api/reactivity-utilities.html)
- [Reaktivite API'si: Gelişmiş](/api/reactivity-advanced.html)

## ref {#ref}

> Bu girdi, reaktivite için `ref` kullanımı hakkındadır. Şablonlarda kullanılan `ref` özniteliği için bunun yerine [şablon ref](#şablon-ref) bölümüne bakın.

Bir `ref`, Vue'nun reaktivite sisteminin bir parçasıdır. `value` adlı tek bir reaktif özelliğe sahip bir nesnedir.

Çeşitli farklı ref türleri vardır. Örneğin, referanslar `ref()`, `shallowRef()`, `computed()` ve `customRef()` kullanılarak oluşturulabilir. Bir nesnenin bir ref olup olmadığını kontrol etmek için `isRef()` işlevi ve ref'in değerinin doğrudan yeniden atanmasına izin verip vermediğini kontrol etmek için `isReadonly()` kullanılabilir.

Daha fazla ayrıntı için bkz:
- [Rehber - Reaktivite Temelleri](/guide/essentials/reactivity-fundamentals.html)
- [Reaktivite API'si: Çekirdek](/api/reactivity-core.html)
- [Reaktivite API'si: Yardımcı Programlar](/api/reactivity-utilities.html)
- [Reaktivite API'si: Gelişmiş](/api/reactivity-advanced.html)

## render işlevi {#render-işlevi}

Bir *render işlevi*, işleme sırasında kullanılan VNode'leri oluşturan bir bileşenin parçasıdır. Şablonlar, render işlevlerine derlenir.

Daha fazla ayrıntı için bkz:
- [Rehber - Render İşlevleri ve JSX](/guide/extras/render-function.html)

## zamanlayıcı {#zamanlayıcı}

*Zamanlayıcı*, Vue'nun dahili yapısının [reaktif efektlerin](#reaktif-efekt) ne zaman çalıştırılacağının zamanlamasını kontrol eden parçasıdır.

Reaktif durum değiştiğinde, Vue işleme güncellemelerini hemen tetiklemez. Bunun yerine, bunları bir kuyruk kullanarak birlikte toplar. Bu, altta yatan verilerde birden fazla değişiklik yapılsa bile, bir bileşenin yalnızca bir kez yeniden işlenmesini sağlar.

[Gözlemciler](/guide/essentials/watchers.html) de zamanlayıcı kuyruğu kullanılarak toplanır. `flush: 'pre'` (varsayılan) olan gözlemciler, bileşen işlemeden önce çalışırken, `flush: 'post'` olanlar bileşen işlemeden sonra çalışır.

Zamanlayıcıdaki işler, bazı [yaşam döngüsü kancalarını](#yaşam-döngüsü-kancaları) tetiklemek ve [şablon referanslarını](#şablon-ref) güncellemek gibi çeşitli diğer dahili görevleri gerçekleştirmek için de kullanılır.

## kapsamlı yuva {#kapsamlı-yuva}

*Kapsamlı yuva* terimi, [prop](#prop) alan bir [yuva](#yuva) anlamına gelmek için kullanılır.

Geçmişte Vue, kapsamlı ve kapsamlı olmayan yuvalar arasında çok daha büyük bir ayrım yapmıştır. Bir dereceye kadar, ortak bir şablon sözdizimi arkasında birleştirilmiş iki ayrı özellik olarak kabul edilebilirler.

Vue 3'te, tüm yuvaların kapsamlı yuvalar gibi davranmasını sağlamak için yuva API'leri basitleştirildi. Ancak, kapsamlı ve kapsamlı olmayan yuvalar için kullanım durumları genellikle farklılık gösterir, bu nedenle terim hala prop'ları olan yuvalara başvurmanın bir yolu olarak yararlı olduğunu kanıtlıyor.

Bir yuvaya geçirilen prop'lar, yalnızca yuvanın içeriğini tanımlamaktan sorumlu olan üst şablonun belirli bir bölgesinde kullanılabilir. Şablonun bu bölgesi, prop'lar için bir değişken kapsamı gibi davranır, dolayısıyla 'kapsamlı yuva' adı verilir.

Daha fazla ayrıntı için bkz:
- [Rehber - Yuvalar - Kapsamlı Yuvalar](/guide/components/slots.html#scoped-slots)

## SFC {#sfc}

Bkz. [Tek Dosya Bileşeni](#tek-dosya-bileşen).

## yan efekt {#yan-efekt}

*Yan efekt* terimi Vue'ya özgü değildir. Yerel kapsamlarının ötesinde bir şeyler yapan işlemleri veya işlevleri açıklamak için kullanılır.

Örneğin, `user.name = null` gibi bir özelliği ayarlama bağlamında, bunun `user.name` değerini değiştirmesi beklenir. Ayrıca Vue'nun reaktivite sistemini tetiklemek gibi başka bir şey de yapıyorsa, bu bir yan etki olarak tanımlanır. Vue içinde [reaktif efekt](#reaktif-efekt) teriminin kökeni budur.

Bir işlevin yan etkileri olduğu tanımlandığında, işlevin yalnızca bir değer döndürmenin yanı sıra, işlevin dışında gözlemlenebilir bir tür eylem gerçekleştirdiği anlamına gelir. Bu, durumdaki bir değeri güncellediği veya bir ağ isteğini tetiklediği anlamına gelebilir.

Terim genellikle işleme veya hesaplanmış özellikleri açıklarken kullanılır. İşlemenin hiçbir yan etkisi olmaması en iyi uygulama olarak kabul edilir. Aynı şekilde, hesaplanmış bir özelliğin alıcı işlevinin de yan etkisi olmamalıdır.

## Tek Dosya Bileşeni {#tek-dosya-bileşen}

*Tek Dosya Bileşeni* veya SFC terimi, Vue bileşenleri için yaygın olarak kullanılan `.vue` dosya biçimini ifade eder.

Ayrıca bkz:
- [Rehber - Tek Dosya Bileşenleri](/guide/scaling-up/sfc.html)
- [SFC Sözdizimi Belirtimi](/api/sfc-spec.html)

## yuva {#yuva}

Yuvalar, alt bileşenlere içerik aktarmak için kullanılır. Prop'lar veri değerlerini geçirmek için kullanılırken, yuvalar HTML öğelerinden ve diğer Vue bileşenlerinden oluşan daha zengin içeriği geçirmek için kullanılır.

Daha fazla ayrıntı için bkz:
- [Rehber - Yuvalar](/guide/components/slots.html)

## şablon ref {#şablon-ref}

*Şablon referansı* terimi, bir şablon içindeki bir etiket üzerinde `ref` özniteliğini kullanmayı ifade eder. Bileşen işlendikten sonra, bu öznitelik, şablondaki etikete karşılık gelen HTML öğesi veya bileşen örneği ile karşılık gelen bir özelliği doldurmak için kullanılır.

Seçenekler API'sini kullanıyorsanız, referanslar `$refs` nesnesinin özellikleri aracılığıyla kullanıma sunulur.

Kompozisyon API'si ile şablon referansları, aynı ada sahip reaktif bir [ref](#ref)'i doldurur.

Şablon referansları, Vue'nun reaktivite sisteminde bulunan reaktif referanslarla karıştırılmamalıdır.

Daha fazla ayrıntı için bkz:
- [Rehber - Şablon Referansları](/guide/essentials/template-refs.html)

## VDOM {#vdom}

Bkz. [sanal DOM](#sanal-dom).

## sanal DOM {#sanal-dom}

*Sanal DOM* (VDOM) terimi Vue'ya özgü değildir. UI'deki güncellemeleri yönetmek için çeşitli web çerçeveleri tarafından kullanılan yaygın bir yaklaşımdır.

Tarayıcılar, sayfanın mevcut durumunu temsil etmek için bir düğüm ağacı kullanır. Bu ağaç ve onunla etkileşimde bulunmak için kullanılan JavaScript API'lerine *belge nesne modeli* veya *DOM* denir.

DOM'u manipüle etmek önemli bir performans darboğazıdır. Sanal DOM, bunu yönetmek için bir strateji sunar.

Vue bileşenleri, doğrudan DOM düğümleri oluşturmak yerine, istedikleri DOM düğümlerinin bir açıklamasını oluşturur. Bu tanımlayıcılar, VNode'ler (sanal DOM düğümleri) olarak bilinen düz JavaScript nesneleridir. VNode'ler oluşturmak nispeten ucuzdur.

Bir bileşen her yeniden işlediğinde, yeni VNode ağacı, önceki VNode ağacıyla karşılaştırılır ve ardından herhangi bir farklılık gerçek DOM'a uygulanır. Hiçbir şey değişmemişse, DOM'a dokunulmasına gerek yoktur.

Vue, [Derleyici Bilgilendirilmiş Sanal DOM](/guide/extras/rendering-mechanism.html#compiler-informed-virtual-dom) dediğimiz karma bir yaklaşım kullanır. Vue'nun şablon derleyicisi, şablonun statik analizine dayalı olarak performans optimizasyonları uygulayabilir. Bir bileşenin eski ve yeni VNode ağaçlarının çalışma zamanında tam bir karşılaştırmasını yapmak yerine, Vue derleyici tarafından çıkarılan bilgileri kullanarak karşılaştırmayı ağacın yalnızca gerçekten değişebilecek kısımlarına indirebilir.

Daha fazla ayrıntı için bkz:
- [Rehber - İşleme Mekanizması](/guide/extras/rendering-mechanism.html)
- [Rehber - Render İşlevleri ve JSX](/guide/extras/render-function.html)

## VNode {#vnode}

Bir *VNode*, bir *sanal DOM düğümüdür*. [`h()`](/api/render-function.html#h) işlevi kullanılarak oluşturulabilirler.

Daha fazla bilgi için [sanal DOM](#sanal-dom) bölümüne bakın.

## Web Bileşeni {#web-bileşeni}

*Web Bileşenleri* standardı, modern web tarayıcılarında uygulanan bir özellikler koleksiyonudur.

Vue bileşenleri Web Bileşenleri değildir, ancak bir Vue bileşeninden bir [özel öğe](#özel-öğe) oluşturmak için `defineCustomElement()` kullanılabilir. Vue ayrıca Vue bileşenlerinin içinde özel öğelerin kullanılmasını da destekler.

Daha fazla ayrıntı için bkz:
- [Rehber - Vue ve Web Bileşenleri](/guide/extras/web-components.html)
