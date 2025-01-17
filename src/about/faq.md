# Sıkça Sorulan Sorular {#sikca-sorulan-sorular}

## Vue'yi kim geliştiriyor? {#vueyi-kim-gelistiriyor}

Vue, bağımsız ve topluluk odaklı bir projedir. 2014 yılında [Evan You](https://twitter.com/youyuxi) tarafından kişisel bir yan proje olarak oluşturulmuştur. Bugün Vue, [dünyanın dört bir yanından tam zamanlı ve gönüllü ekip üyeleri tarafından aktif olarak geliştirilmektedir](/about/team). Evan proje lideri olarak görev yapmaktadır. Vue'nin hikayesini daha ayrıntılı olarak bu [belgeselde](https://www.youtube.com/watch?v=OrxmtDw4pVI) öğrenebilirsiniz.

Vue'nin geliştirilmesi esas olarak sponsorluklarla finanse edilmektedir ve 2016'dan beri mali açıdan sürdürülebilir durumdadır. Siz veya işletmeniz Vue'den faydalanıyorsa, Vue'nun gelişimini desteklemek için [bize sponsor olmayı](/sponsor/) düşünebilirsiniz!

## Vue 2 ve Vue 3 arasındaki fark nedir? {#vue-2-ve-vue-3-arasindaki-fark-nedir}

Vue 3, Vue'nun mevcut ve en son ana sürümüdür. Teleport, Suspense ve şablon başına birden fazla kök öğesi gibi Vue 2'de bulunmayan yeni özellikler içerir. Ayrıca, Vue 2 ile uyumsuz hale getiren önemli değişiklikler de vardır. Tüm detaylar [Vue 3 Geçiş Rehberi'nde](https://v3-migration.vuejs.org/) belgelenmiştir.

Farklara rağmen, iki sürüm arasında çoğu API aynıdır. Bu nedenle, Vue 2'deki bilginiz büyük ölçüde Vue 3'te çalışmaya devam eder. Önemli bir özellik olan Kompozisyon API'si başlangıçta yalnızca Vue 3'e özgüydü, ancak şimdi Vue 2.7'de geri uyarlanmıştır. Daha fazla bilgi için [Vue 2.7 sürüm notlarına](https://github.com/vuejs/vue/blob/main/CHANGELOG.md#270-2022-07-01) bakabilirsiniz.

Genel olarak, Vue 3 daha küçük paket boyutları, daha iyi performans, ölçeklenebilirlik ve TypeScript/IDE desteği sunar. Bugün yeni bir proje başlatıyorsanız, Vue 3 önerilen seçenektir. Ancak, şu durumlarda Vue 2'yi düşünebilirsiniz:

- IE11 desteğine ihtiyacınız varsa. Vue 3, modern JavaScript özelliklerinden faydalanır ve IE11'i desteklemez.

Vue 2'den Vue 3'e geçiş yapmak istiyorsanız, [geçiş rehberine](https://v3-migration.vuejs.org/) başvurabilirsiniz.

## Vue 2 hala destekleniyor mu? {#vue-2-hala-destekleniyor-mu}

Vue 2.7, Temmuz 2022'de yayımlanan, Vue 2 serisinin son küçük sürümüdür. Vue 2 artık bakım modundadır: Yeni özellikler sunulmayacak, ancak 2.7 sürüm tarihinden itibaren 18 ay boyunca kritik hata düzeltmeleri ve güvenlik güncellemeleri alacaktır. Bu, **Vue 2'nin yaşam döngüsünün 31 Aralık 2023'te sona ereceği** anlamına gelir.

Ekiplerin ve projelerin bu süre zarfında Vue 3'e geçiş yapmasını bekliyoruz. Ancak, bu zaman çizelgesine uyamayan ve güvenlik/gereksinimlerini karşılaması gereken ekipler için [Vue 2 Genişletilmiş LTS](https://v2.vuejs.org/lts/) ile uzun vadeli destek sağlıyoruz.

## Vue hangi lisansı kullanıyor? {#vue-hangi-lisansi-kullaniyor}

Vue, [MIT Lisansı](https://opensource.org/licenses/MIT) altında yayımlanan, ücretsiz ve açık kaynaklı bir projedir.

## Vue hangi tarayıcıları destekliyor? {#vue-hangi-tarayicilari-destekliyor}

Vue'nun en son sürümü (3.x), yalnızca [yerel ES2016 desteğine sahip tarayıcıları](https://caniuse.com/es2016) destekler. Bu, IE11'i dışlar. Vue 3.x, eski tarayıcılarda polyfill edilemeyen ES2016 özelliklerini kullanır, bu nedenle eski tarayıcıları desteklemeniz gerekiyorsa Vue 2.x kullanmanız gerekir.

## Vue güvenilir mi? {#vue-guvenilir-mi}

Vue olgun ve güvenilir bir framework'tür. Bugün dünya çapında üretimde en yaygın kullanılan JavaScript framework'lerinden biridir ve npm üzerinden ayda yaklaşık 10 milyon kez indirilir. NASA, Apple, Google, Microsoft gibi birçok ünlü kuruluş Vue'yu üretimde kullanmaktadır.

## Vue hızlı mı? {#vue-hizli-mi}

Vue 3, performansı yüksek framework'lerden biridir ve genellikle manuel optimizasyona gerek kalmadan web uygulamalarında mükemmel sonuçlar verir. Daha fazla detay için [Performans Optimizasyonu Rehberi'ne](/guide/best-practices/performance) göz atabilirsiniz.
