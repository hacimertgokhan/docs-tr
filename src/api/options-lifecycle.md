# Seçenekler: Yaşam Döngüsü {#options-lifecycle}

:::info Ayrıca bakınız
Yaşam döngüsü kancalarının ortak kullanımı için [Rehber - Yaşam Döngüsü Kancaları](/guide/essentials/lifecycle) bölümüne bakın.
:::

## beforeCreate {#beforecreate}

Örnek başlatıldığında çağrılır.

- **Tür**

  ```ts
  interface ComponentOptions {
    beforeCreate?(this: ComponentPublicInstance): void
  }
  ```

- **Detaylar**

  Örnek başlatıldığında ve prop'lar çözümlendiğinde hemen çağrılır.

  Ardından prop'lar reaktif özellikler olarak tanımlanır ve `data()` veya `computed` gibi durum ayarlanır.

  Composition API'nin `setup()` kancasının, `beforeCreate()` dahil olmak üzere herhangi bir Options API kancasından önce çağrıldığına dikkat edin.

## created {#created}

Örnek, durumla ilgili tüm seçenekleri işlemeyi bitirdikten sonra çağrılır.

- **Tür**

  ```ts
  interface ComponentOptions {
    created?(this: ComponentPublicInstance): void
  }
  ```

- **Detaylar**

  Bu kanca çağrıldığında, aşağıdakiler ayarlanmıştır: reaktif veri, hesaplanan özellikler, metotlar ve izleyiciler. Ancak, montaj aşaması başlatılmamıştır ve `$el` özelliği henüz kullanılamaz olacaktır.

## beforeMount {#beforemount}

Bileşen monte edilmeden hemen önce çağrılır.

- **Tür**

  ```ts
  interface ComponentOptions {
    beforeMount?(this: ComponentPublicInstance): void
  }
  ```

- **Detaylar**

  Bu kanca çağrıldığında, bileşen reaktif durumunu ayarlamayı bitirmiştir, ancak henüz DOM düğümleri oluşturulmamıştır. İlk kez DOM render etkisini yürütmek üzeredir.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

## mounted {#mounted}

Bileşen monte edildikten sonra çağrılır.

- **Tür**

  ```ts
  interface ComponentOptions {
    mounted?(this: ComponentPublicInstance): void
  }
  ```

- **Detaylar**

  Bir bileşen, aşağıdakilerden sonra monte edilmiş kabul edilir:

  - Tüm eşzamanlı alt bileşenleri monte edilmiştir (asenkron bileşenleri veya `<Suspense>` ağaçlarının içindeki bileşenleri içermez).

  - Kendi DOM ağacı oluşturulmuş ve üst kapsayıcıya yerleştirilmiştir. Uygulamanın kök kapsayıcısı da belgenin içindeyse, yalnızca bileşenin DOM ağacının belgenin içinde olduğunu garanti ettiğini unutmayın.

  Bu kanca genellikle bileşenin render edilmiş DOM'una erişim gerektiren yan etkileri gerçekleştirmek veya bir [sunucu tarafı render edilmiş uygulamada](/guide/scaling-up/ssr) DOM ile ilgili kodu istemciyle sınırlamak için kullanılır.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

## beforeUpdate {#beforeupdate}

Bileşen, reaktif bir durum değişikliği nedeniyle DOM ağacını güncellemeden hemen önce çağrılır.

- **Tür**

  ```ts
  interface ComponentOptions {
    beforeUpdate?(this: ComponentPublicInstance): void
  }
  ```

- **Detaylar**

  Bu kanca, Vue DOM'u güncellemeden önce DOM durumuna erişmek için kullanılabilir. Bu kanca içinde bileşen durumunu değiştirmek de güvenlidir.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

## updated {#updated}

Bileşen, reaktif bir durum değişikliği nedeniyle DOM ağacını güncelledikten sonra çağrılır.

- **Tür**

  ```ts
  interface ComponentOptions {
    updated?(this: ComponentPublicInstance): void
  }
  ```

- **Detaylar**

  Bir üst bileşenin güncellenmiş kancası, alt bileşenlerinkinden sonra çağrılır.

  Bu kanca, bileşenin farklı durum değişikliklerinden kaynaklanabilen herhangi bir DOM güncellemesinden sonra çağrılır. Belirli bir durum değişikliğinden sonra güncellenmiş DOM'a erişmeniz gerekiyorsa, bunun yerine [nextTick()](/api/general#nexttick) kullanın.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

  :::warning
  Güncellenmiş kancada bileşen durumunu değiştirmeyin; bu muhtemelen sonsuz bir güncelleme döngüsüne yol açacaktır!
  :::

## beforeUnmount {#beforeunmount}

Bir bileşen örneği kaldırılmadan hemen önce çağrılır.

- **Tür**

  ```ts
  interface ComponentOptions {
    beforeUnmount?(this: ComponentPublicInstance): void
  }
  ```

- **Detaylar**

  Bu kanca çağrıldığında, bileşen örneği hala tamamen işlevseldir.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

## unmounted {#unmounted}

Bileşen kaldırıldıktan sonra çağrılır.

- **Tür**

  ```ts
  interface ComponentOptions {
    unmounted?(this: ComponentPublicInstance): void
  }
  ```

- **Detaylar**

  Bir bileşen, aşağıdakilerden sonra kaldırılmış kabul edilir:

  - Tüm alt bileşenleri kaldırılmıştır.

  - Tüm ilişkili reaktif etkileri (`setup()` sırasında oluşturulan render etkisi ve hesaplananlar / izleyiciler) durdurulmuştur.

  Zamanlayıcılar, DOM olay dinleyicileri veya sunucu bağlantıları gibi manuel olarak oluşturulan yan etkileri temizlemek için bu kancayı kullanın.

  **Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

## errorCaptured {#errorcaptured}

Bir alt bileşenden yayılan bir hata yakalandığında çağrılır.

- **Tür**

  ```ts
  interface ComponentOptions {
    errorCaptured?(
      this: ComponentPublicInstance,
      err: unknown,
      instance: ComponentPublicInstance | null,
      info: string
    ): boolean | void
  }
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

## renderTracked <sup class="vt-badge dev-only" /> {#rendertracked}

Bileşenin render etkisi tarafından bir reaktif bağımlılığın izlendiği zaman çağrılır.

**Bu kanca yalnızca geliştirme modu içindir ve sunucu tarafı render etme sırasında çağrılmaz.**

- **Tür**

  ```ts
  interface ComponentOptions {
    renderTracked?(this: ComponentPublicInstance, e: DebuggerEvent): void
  }

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TrackOpTypes /* 'get' | 'has' | 'iterate' */
    key: any
  }
  ```

- **Ayrıca bakınız** [Reaktivite Derinlemesine](/guide/extras/reactivity-in-depth)

## renderTriggered <sup class="vt-badge dev-only" /> {#rendertriggered}

Bir reaktif bağımlılığın, bileşenin render etkisinin yeniden çalıştırılması için tetiklediği zaman çağrılır.

**Bu kanca yalnızca geliştirme modu içindir ve sunucu tarafı render etme sırasında çağrılmaz.**

- **Tür**

  ```ts
  interface ComponentOptions {
    renderTriggered?(this: ComponentPublicInstance, e: DebuggerEvent): void
  }

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

## activated {#activated}

Bileşen örneği, [`<KeepAlive>`](/api/built-in-components#keepalive) tarafından önbelleğe alınan bir ağacın parçası olarak DOM'a eklendikten sonra çağrılır.

**Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

- **Tür**

  ```ts
  interface ComponentOptions {
    activated?(this: ComponentPublicInstance): void
  }
  ```

- **Ayrıca bakınız** [Rehber - Önbelleğe Alınmış Örneğin Yaşam Döngüsü](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## deactivated {#deactivated}

Bileşen örneği, [`<KeepAlive>`](/api/built-in-components#keepalive) tarafından önbelleğe alınan bir ağacın parçası olarak DOM'dan kaldırıldıktan sonra çağrılır.

**Bu kanca, sunucu tarafı render etme sırasında çağrılmaz.**

- **Tür**

  ```ts
  interface ComponentOptions {
    deactivated?(this: ComponentPublicInstance): void
  }
  ```

- **Ayrıca bakınız** [Rehber - Önbelleğe Alınmış Örneğin Yaşam Döngüsü](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## serverPrefetch <sup class="vt-badge" data-text="Yalnızca SSR" /> {#serverprefetch}

Bileşen örneği sunucuda render edilmeden önce çözümlenecek asenkron fonksiyon.

- **Tür**

  ```ts
  interface ComponentOptions {
    serverPrefetch?(this: ComponentPublicInstance): Promise<any>
  }
  ```

- **Detaylar**

  Kanca bir Promise döndürürse, sunucu render edicisi, bileşeni render etmeden önce Promise çözülene kadar bekleyecektir.

  Bu kanca yalnızca sunucu tarafı render etme sırasında çağrılır ve yalnızca sunucu tarafında veri getirme işlemleri gerçekleştirmek için kullanılabilir.

- **Örnek**

  ```js
  export default {
    data() {
      return {
        data: null
      }
    },
    async serverPrefetch() {
      // bileşen, ilk isteğin bir parçası olarak render edilir
      // istemciden daha hızlı olduğu için verileri sunucuda önceden getir
      this.data = await fetchOnServer(/* ... */)
    },
    async mounted() {
      if (!this.data) {
        // montaj sırasında veri null ise, bileşenin
        // dinamik olarak istemcide render edildiği anlamına gelir. Bunun yerine
        // bir istemci tarafı getirme gerçekleştirin.
        this.data = await fetchOnClient(/* ... */)
      }
    }
  }
  ```

- **Ayrıca bakınız** [Sunucu Tarafı Render Etme](/guide/scaling-up/ssr)
