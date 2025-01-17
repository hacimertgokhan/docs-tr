# Sürümler {#surumler}

<script setup>
import { ref, onMounted } from 'vue'

const version = ref()

onMounted(async () => {
  const res = await fetch('https://api.github.com/repos/vuejs/core/releases/latest')
  version.value = (await res.json()).name
})
</script>

<p v-if="version">
Vue'nun şu anki en son kararlı sürümü <strong>{{ version }}</strong>.
</p>
<p v-else>
Son sürüm kontrol ediliyor...
</p>

Geçmiş sürümlerin tam değişiklik günlüğü, [GitHub](https://github.com/vuejs/core/blob/main/CHANGELOG.md)'da mevcuttur.

## Sürüm Döngüsü {#surum-dongusu}

Vue sabit bir sürüm döngüsüne sahip değildir.

- Yama sürümleri gerektiği şekilde yayınlanır.
- Küçük sürümler her zaman yeni özellikler içerir ve aralarında genellikle 3 ila 6 aylık bir zaman dilimi olur. Küçük sürümler her zaman bir beta ön sürüm aşamasından geçer.
- Büyük sürümler önceden duyurulacak ve erken tartışma, alfa/beta ön sürüm aşamalarından geçecektir.

## Semantik Sürümleme İstisnaları {#semantik-surumleme-istisnalari}

Vue sürümleri, birkaç istisna ile [Semantik Sürümleme](https://semver.org/) kurallarını takip eder.

### TypeScript Tanımları {#typescript-tanimlari}

TypeScript tanımlarına uyumsuz değişiklikler **küçük** sürümler arasında gelebilir. Bunun sebepleri:

1. Bazen TypeScript, küçük sürümler arasında uyumsuz değişiklikler getirir ve Vue'nun bunlara uyum sağlaması gerekir.
2. Yeni TypeScript özelliklerini benimsemek gerekebilir ve bu, minimum TypeScript sürümünü yükseltmeyi gerektirebilir.

TypeScript kullanıyorsanız, mevcut küçük sürümü sabitleyen bir semver aralığı kullanabilir ve yeni bir Vue sürümü çıktığında manuel olarak yükseltebilirsiniz.

### Derlenmiş Kodun Eski Çalışma Zamanıyla Uyumluluğu {#derlenmis-kodun-uyumlulugu}

Yeni bir **küçük** Vue derleyicisi, eski bir küçük çalışma zamanı ile uyumlu olmayan kod üretebilir. Örneğin, Vue 3.2 derleyicisi tarafından üretilen kod, Vue 3.1 çalışma zamanı ile tam uyumlu olmayabilir.

Bu durum yalnızca kütüphane geliştiricilerini ilgilendirir. Çünkü uygulamalarda, derleyici ve çalışma zamanı sürümleri her zaman aynıdır. Bir sürüm uyumsuzluğu, yalnızca derlenmiş Vue bileşen kodlarını paket olarak yayınlayıp, eski bir Vue sürümü kullanan bir projede tüketilirse ortaya çıkabilir. Bu nedenle, paketinize minimum bir Vue sürüm gereksinimi eklemeniz gerekebilir.

## Ön Sürümler {#on-surumler}

Küçük sürümler genellikle belirsiz sayıda beta sürümden geçer. Büyük sürümler bir alfa ve bir beta aşamasından geçer.

Ayrıca, her hafta `main` ve `minor` dallarından kanarya sürümleri yayınlarız. Bu sürümler, kararlı kanalın npm meta verilerini şişirmemek için farklı paketler olarak yayınlanır. Bunları sırasıyla `npx install-vue@canary` veya `npx install-vue@canary-minor` komutlarıyla yükleyebilirsiniz.

Ön sürümler, entegrasyon/stabilite testi ve erken benimseyenlerin kararsız özellikler için geri bildirim sağlaması içindir. Ön sürümleri prodüksiyonda kullanmayın. Tüm ön sürümler kararsız kabul edilir ve aralarındaki sürümlerde uyumsuz değişiklikler olabilir. Bu nedenle ön sürüm kullanıyorsanız her zaman tam sürümü sabitleyin.

## Kullanım Dışı Bırakmalar {#kullanim-disi-birakmalar}

Yeni, daha iyi alternatiflere sahip olan özellikler küçük sürümlerde kullanım dışı bırakılabilir. Kullanım dışı bırakılan özellikler çalışmaya devam eder, ancak bir sonraki büyük sürümde kaldırılır.

## RFC'ler {#rfcler}

Önemli API yüzeyine sahip yeni özellikler ve Vue'deki büyük değişiklikler **Görüş İstekleri** (RFC) sürecinden geçer. RFC süreci, yeni özelliklerin framework'e girmesi için tutarlı ve kontrol edilen bir yol sağlar ve kullanıcıların tasarım sürecine katılmasına ve geri bildirim sağlamasına olanak tanır.

RFC süreci [vuejs/rfcs](https://github.com/vuejs/rfcs) GitHub deposunda yürütülmektedir.

## Deneysel Özellikler {#deneysel-ozellikler}

Bazı özellikler, Vue'nun kararlı bir sürümünde yayınlanır ve belgelenir, ancak deneysel olarak işaretlenir. Deneysel özellikler genellikle tasarım sorunlarının çoğu kağıt üzerinde çözülmüş, ancak gerçek dünya kullanımı geri bildirimi eksik olan özelliklerdir.

Deneysel özelliklerin amacı, kullanıcıların kararsız bir Vue sürümü kullanmak zorunda kalmadan, bir prodüksiyon ortamında test ederek geri bildirim sağlamasını sağlamaktır. Ancak deneysel özellikler kararsız kabul edilir ve herhangi bir sürüm türü arasında değişiklik gösterebilir. Bu nedenle, kontrollü bir şekilde ve özelliklerin değişebileceği beklentisiyle kullanılmalıdır.
