# Composition API: Yaşam Döngüsü Kancaları {#composition-api-lifecycle-hooks}

:::info Kullanım Notu
Bu sayfada listelenen tüm API'ler, bir bileşenin `setup()` aşamasında eşzamanlı olarak çağrılmalıdır. Daha fazla ayrıntı için [Rehber - Yaşam Döngüsü Kancaları](/guide/essentials/lifecycle) bölümüne bakın.
:::

## onMounted() {#onmounted}

Bileşen monte edildikten sonra çağrılacak bir geri çağrı kaydeder.

- **Tür**

  ```ts
  function onMounted(callback: () => void): void
  ```

- **Detaylar**

  Bir bileşen, aşağıdakilerden sonra monte edilmiş kabul edilir:

  - Tüm eşzamanlı alt bileşenleri monte edilmiştir (asenkron bileşenleri veya `<Suspense>` ağaçlarının içindeki bileşenleri içermez).

  - Kendi DOM ağacı oluşturulmuş ve üst kapsayıcıya yerleştirilmiştir. Uygulamanın kök kapsayıcısı da belgenin içindeyse, yalnızca bileşenin DOM ağacının belgenin içinde olduğunu garanti ettiğini unutmayın.

  Bu kanca genellikle bileşenin render edilmiş DOM'una erişim gerektiren yan etkileri gerçekleştirmek veya bir [sunucu tarafı render edilmiş uygulamada](/guide/scaling-up/ssr) DOM ile ilgili kodu istemciyle sınırlamak için kullanılır.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

- **Örnek**

  Şablon referansı aracılığıyla bir öğeye erişme:

  ```vue
  <script setup>
  import { ref, onMounted } from 'vue'

  const el = ref()

  onMounted(() => {
    el.value // <div>
  })
  </script>

  <template>
    <div ref="el"></div>
  </template>
  ```

## onUpdated() {#onupdated}

Bileşen reaktif bir durum değişikliği nedeniyle DOM ağacını güncelledikten sonra çağrılacak bir geri çağrı kaydeder.

- **Tür**

  ```ts
  function onUpdated(callback: () => void): void
  ```

- **Detaylar**

  Bir üst bileşenin güncellenmiş kancası, alt bileşenlerinkinden sonra çağrılır.

  Bu kanca, bileşenin herhangi bir DOM güncellemesinden sonra çağrılır, bu da farklı durum değişikliklerinden kaynaklanabilir, çünkü performans nedenleriyle birden fazla durum değişikliği tek bir render döngüsünde toplanabilir. Belirli bir durum değişikliğinden sonra güncellenmiş DOM'a erişmeniz gerekiyorsa, bunun yerine [nextTick()](/api/general#nexttick) kullanın.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

  :::warning
  Güncellenmiş kancada bileşen durumunu değiştirmeyin; bu muhtemelen sonsuz bir güncelleme döngüsüne yol açacaktır!
  :::

- **Örnek**

  Güncellenmiş DOM'a erişme:

  ```vue
  <script setup>
  import { ref, onUpdated } from 'vue'

  const count = ref(0)

  onUpdated(() => {
    // metin içeriği, mevcut `count.value` ile aynı olmalıdır
    console.log(document.getElementById('count').textContent)
  })
  </script>

  <template>
    <button id="count" @click="count++">{{ count }}</button>
  </template>
  ```

## onUnmounted() {#onunmounted}

Bileşen kaldırıldıktan sonra çağrılacak bir geri çağrı kaydeder.

- **Tür**

  ```ts
  function onUnmounted(callback: () => void): void
  ```

- **Detaylar**

  Bir bileşen, aşağıdakilerden sonra kaldırılmış kabul edilir:

  - Tüm alt bileşenleri kaldırılmıştır.

  - Tüm ilişkili reaktif etkileri (`setup()` sırasında oluşturulan render etkisi ve hesaplananlar / izleyiciler) durdurulmuştur.

  Zamanlayıcılar, DOM olay dinleyicileri veya sunucu bağlantıları gibi manuel olarak oluşturulan yan etkileri temizlemek için bu kancayı kullanın.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

- **Örnek**

  ```vue
  <script setup>
  import { onMounted, onUnmounted } from 'vue'

  let intervalId
  onMounted(() => {
    intervalId = setInterval(() => {
      // ...
    })
  })

  onUnmounted(() => clearInterval(intervalId))
  </script>
  ```

## onBeforeMount() {#onbeforemount}

Bileşen monte edilmeden hemen önce çağrılacak bir kanca kaydeder.

- **Tür**

  ```ts
  function onBeforeMount(callback: () => void): void
  ```

- **Detaylar**

  Bu kanca çağrıldığında, bileşen reaktif durumunu ayarlamayı bitirmiştir, ancak henüz DOM düğümleri oluşturulmamıştır. İlk kez DOM render etkisini yürütmek üzeredir.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

## onBeforeUpdate() {#onbeforeupdate}

Bileşen, reaktif bir durum değişikliği nedeniyle DOM ağacını güncellemeden hemen önce çağrılacak bir kanca kaydeder.

- **Tür**

  ```ts
  function onBeforeUpdate(callback: () => void): void
  ```

- **Detaylar**

  Bu kanca, Vue DOM'u güncellemeden önce DOM durumuna erişmek için kullanılabilir. Bu kanca içinde bileşen durumunu değiştirmek de güvenlidir.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

## onBeforeUnmount() {#onbeforeunmount}

Bir bileşen örneği kaldırılmadan hemen önce çağrılacak bir kanca kaydeder.

- **Tür**

  ```ts
  function onBeforeUnmount(callback: () => void): void
  ```

- **Detaylar**

  Bu kanca çağrıldığında, bileşen örneği hala tamamen işlevseldir.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

## onErrorCaptured() {#onerrorcaptured}

Bir alt bileşenden yayılan bir hata yakalandığında çağrılacak bir kanca kaydeder.

- **Tür**

  ```ts
  function onErrorCaptured(callback: ErrorCapturedHook): void

  type ErrorCapturedHook = (
    err: unknown,
    instance: ComponentPublicInstance | null,
    info: string
  ) => boolean | void
  ```

- **Detaylar**

  Hatalar aşağıdaki kaynaklardan yakalanabilir:

  - Bileşen renderları
  - Olay işleyicileri
  - Yaşam döngüsü kancaları
  - `setup()` fonksiyonu
  - İzleyiciler
  - Özel direktif kancaları
  - Geçiş kancaları

  Kanca, üç argüman alır: hata, hatayı tetikleyen bileşen örneği ve hata kaynak türünü belirten bir bilgi dizesi.

  :::tip
  Üretim ortamında, 3. argüman (`info`), tam bilgi dizesi yerine kısaltılmış bir kod olacaktır. Koddan dizeye eşleştirmeyi [Üretim Hata Kodu Referansı](/error-reference/#runtime-errors) bölümünde bulabilirsiniz.
  :::

  Kullanıcıya bir hata durumu görüntülemek için `errorCaptured()` içinde bileşen durumunu değiştirebilirsiniz. Ancak, hata durumunun hataya neden olan orijinal içeriği render etmemesi önemlidir; aksi takdirde bileşen sonsuz bir render döngüsüne girecektir.

  Kanca, hatanın daha fazla yayılmasını durdurmak için `false` döndürebilir. Aşağıdaki hata yayılma ayrıntılarına bakın.

  **Hata Yayılma Kuralları**

  - Varsayılan olarak, tanımlanmışsa, tüm hatalar hala uygulama düzeyindeki [`app.config.errorHandler`](/api/application#app-config-errorhandler)'a gönderilir, böylece bu hatalar hala tek bir yerden bir analiz hizmetine raporlanabilir.

  - Bir bileşenin kalıtım zincirinde veya üst zincirinde birden fazla `errorCaptured` kancası varsa, hepsi aynı hatada aşağıdan yukarıya doğru çağrılır. Bu, yerel DOM olaylarının kabarcıklanma mekanizmasına benzer.

  - `errorCaptured` kancasının kendisi bir hata verirse, hem bu hata hem de orijinal yakalanan hata `app.config.errorHandler`'a gönderilir.

  - Bir `errorCaptured` kancası, hatanın daha fazla yayılmasını engellemek için `false` döndürebilir. Bu esasen "bu hata ele alındı ve yok sayılmalı" anlamına gelir. Bu hata için ek `errorCaptured` kancalarının veya `app.config.errorHandler`'ın çağrılmasını engelleyecektir.

## onRenderTracked() <sup class="vt-badge dev-only" /> {#onrendertracked}

Bir bileşenin render etkisi tarafından bir reaktif bağımlılığın izlendiği zaman çağrılacak bir hata ayıklama kancası kaydeder.

**Bu kanca yalnızca geliştirme modu içindir ve sunucu tarafı render etme sırasında çağrılmaz.**

- **Tür**

  ```ts
  function onRenderTracked(callback: DebuggerHook): void

  type DebuggerHook = (e: DebuggerEvent) => void

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TrackOpTypes /* 'get' | 'has' | 'iterate' */
    key: any
  }
  ```

- **Ayrıca bakınız** [Reaktivite Derinlemesine](/guide/extras/reactivity-in-depth)

## onRenderTriggered() <sup class="vt-badge dev-only" /> {#onrendertriggered}

Bir reaktif bağımlılığın, bileşenin render etkisinin yeniden çalıştırılması için tetiklediği zaman çağrılacak bir hata ayıklama kancası kaydeder.

**Bu kanca yalnızca geliştirme modu içindir ve sunucu tarafı render etme sırasında çağrılmaz.**

- **Tür**

  ```ts
  function onRenderTriggered(callback: DebuggerHook): void

  type DebuggerHook = (e: DebuggerEvent) => void

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TriggerOpTypes /* 'set' | 'add' | 'delete' | 'clear' */
    key: any
    newValue?: any
    oldValue?: any
    oldTarget?: Map<any, any> | Set<any>
  }
  ```

- **Ayrıca bakınız** [Reaktivite Derinlemesine](/guide/extras/reactivity-in-depth)

## onActivated() {#onactivated}

Bileşen örneği, [`<KeepAlive>`](/api/built-in-components#keepalive) tarafından önbelleğe alınan bir ağacın parçası olarak DOM'a eklendikten sonra çağrılacak bir geri çağrı kaydeder.

**Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

- **Tür**

  ```ts
  function onActivated(callback: () => void): void
  ```

- **Ayrıca bakınız** [Rehber - Önbelleğe Alınmış Örneğin Yaşam Döngüsü](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## onDeactivated() {#ondeactivated}

Bileşen örneği, [`<KeepAlive>`](/api/built-in-components#keepalive) tarafından önbelleğe alınan bir ağacın parçası olarak DOM'dan kaldırıldıktan sonra çağrılacak bir geri çağrı kaydeder.

**Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

- **Tür**

  ```ts
  function onDeactivated(callback: () => void): void
  ```

- **Ayrıca bakınız** [Rehber - Önbelleğe Alınmış Örneğin Yaşam Döngüsü](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## onServerPrefetch() <sup class="vt-badge" data-text="Yalnızca SSR" /> {#onserverprefetch}

Bileşen örneği sunucuda render edilmeden önce çözümlenmesi gereken bir asenkron işlev kaydeder.

- **Tür**

  ```ts
  function onServerPrefetch(callback: () => Promise<any>): void
  ```

- **Detaylar**

  Geri çağrı bir Promise döndürürse, sunucu render edicisi, bileşeni render etmeden önce Promise çözülene kadar bekleyecektir.

  Bu kanca yalnızca sunucu tarafı render etme sırasında çağrılır ve yalnızca sunucu tarafında veri getirme işlemleri gerçekleştirmek için kullanılabilir.

- **Örnek**

  ```vue
  <script setup>
  import { ref, onServerPrefetch, onMounted } from 'vue'

  const data = ref(null)

  onServerPrefetch(async () => {
    // bileşen, ilk isteğin bir parçası olarak render edilir
    // istemciden daha hızlı olduğu için verileri sunucuda önceden getir
    data.value = await fetchOnServer(/* ... */)
  })

  onMounted(async () => {
    if (!data.value) {
      // montaj sırasında veri null ise, bileşenin
      // dinamik olarak istemcide render edildiği anlamına gelir. Bunun yerine
      // bir istemci tarafı getirme gerçekleştirin.
      data.value = await fetchOnClient(/* ... */)
    }
  })
  </script>
  ```

- **Ayrıca bakınız** [Sunucu Tarafı Render Etme](/guide/scaling-up/ssr)
