# Seçenekler: Composition {#options-composition}

## provide {#provide}

Alt bileşenler tarafından enjekte edilebilecek değerler sağlar.

- **Tür**

  ```ts
  interface ComponentOptions {
    provide?: object | ((this: ComponentPublicInstance) => object)
  }
  ```

- **Detaylar**

  `provide` ve [`inject`](#inject), aynı ebeveyn zincirinde oldukları sürece, bileşen hiyerarşisi ne kadar derin olursa olsun, bir ata bileşenin tüm alt öğeleri için bir bağımlılık enjektörü olarak görev yapmasına izin vermek için birlikte kullanılır.

  `provide` seçeneği, bir nesne veya bir nesne döndüren bir fonksiyon olmalıdır. Bu nesne, alt öğelerine enjekte edilmek üzere kullanılabilen özellikleri içerir. Bu nesnede anahtar olarak Semboller kullanabilirsiniz.

- **Örnek**

  Temel kullanım:

  ```js
  const s = Symbol()

  export default {
    provide: {
      foo: 'foo',
      [s]: 'bar'
    }
  }
  ```

  Bileşen başına durum sağlamak için bir fonksiyon kullanma:

  ```js
  export default {
    data() {
      return {
        msg: 'foo'
      }
    },
    provide() {
      return {
        msg: this.msg
      }
    }
  }
  ```

  Yukarıdaki örnekte, sağlanan `msg`'nin reaktif **OLMAYACAĞINA** dikkat edin. Daha fazla ayrıntı için [Reaktivite ile Çalışma](/guide/components/provide-inject#working-with-reactivity) bölümüne bakın.

- **Ayrıca bakınız** [Sağlama / Enjekte Etme](/guide/components/provide-inject)

## inject {#inject}

Atalardan gelen sağlayıcılardan konumlandırarak geçerli bileşene enjekte edilecek özellikleri bildirin.

- **Tür**

  ```ts
  interface ComponentOptions {
    inject?: ArrayInjectOptions | ObjectInjectOptions
  }

  type ArrayInjectOptions = string[]

  type ObjectInjectOptions = {
    [key: string | symbol]:
      | string
      | symbol
      | { from?: string | symbol; default?: any }
  }
  ```

- **Detaylar**

  `inject` seçeneği şu şekilde olmalıdır:

  - Bir string dizisi veya
  - Anahtarların yerel bağlama adı olduğu ve değerin şu olduğu bir nesne:
    - Kullanılabilir enjeksiyonlarda aranacak anahtar (string veya Sembol) veya
    - Şu şekilde olduğu bir nesne:
      - `from` özelliği, kullanılabilir enjeksiyonlarda aranacak anahtardır (string veya Sembol) ve
      - `default` özelliği, geri dönüş değeri olarak kullanılır. Props varsayılan değerlerine benzer şekilde, birden fazla bileşen örneği arasında değer paylaşımını önlemek için nesne türleri için bir fabrika işlevi gereklidir.

  Eşleşen bir özellik veya varsayılan değer sağlanmadıysa, enjekte edilmiş bir özellik `undefined` olacaktır.

  Enjekte edilmiş bağlamaların reaktif **OLMADIĞINA** dikkat edin. Bu kasıtlıdır. Ancak, enjekte edilen değer reaktif bir nesneyse, o nesnedeki özellikler reaktif kalır. Daha fazla ayrıntı için [Reaktivite ile Çalışma](/guide/components/provide-inject#working-with-reactivity) bölümüne bakın.

- **Örnek**

  Temel kullanım:

  ```js
  export default {
    inject: ['foo'],
    created() {
      console.log(this.foo)
    }
  }
  ```

  Enjekte edilmiş bir değeri bir prop için varsayılan olarak kullanma:

  ```js
  const Child = {
    inject: ['foo'],
    props: {
      bar: {
        default() {
          return this.foo
        }
      }
    }
  }
  ```

  Enjekte edilmiş bir değeri veri girişi olarak kullanma:

  ```js
  const Child = {
    inject: ['foo'],
    data() {
      return {
        bar: this.foo
      }
    }
  }
  ```

  Enjeksiyonlar, varsayılan değerle isteğe bağlı olabilir:

  ```js
  const Child = {
    inject: {
      foo: { default: 'foo' }
    }
  }
  ```

  Farklı bir ada sahip bir özellikten enjekte edilmesi gerekiyorsa, kaynak özelliğini belirtmek için `from` kullanın:

  ```js
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: 'foo'
      }
    }
  }
  ```

  Prop varsayılanlarına benzer şekilde, temel olmayan değerler için bir fabrika işlevi kullanmanız gerekir:

  ```js
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: () => [1, 2, 3]
      }
    }
  }
  ```

- **Ayrıca bakınız** [Sağlama / Enjekte Etme](/guide/components/provide-inject)

## mixins {#mixins}

Geçerli bileşene karıştırılacak seçenek nesneleri dizisi.

- **Tür**

  ```ts
  interface ComponentOptions {
    mixins?: ComponentOptions[]
  }
  ```

- **Detaylar**

  `mixins` seçeneği, mixin nesnelerinden oluşan bir dizi kabul eder. Bu mixin nesneleri, normal örnek nesneleri gibi örnek seçenekleri içerebilir ve belirli seçenek birleştirme mantığı kullanılarak sonuçta elde edilen seçeneklere karşı birleştirilir. Örneğin, mixin'iniz bir `created` kancası içeriyorsa ve bileşenin kendisinde de bir tane varsa, her iki fonksiyon da çağrılır.

  Mixin kancaları, sağlandıkları sırayla çağrılır ve bileşenin kendi kancalarından önce çağrılır.

  :::warning Artık Tavsiye Edilmiyor
  Vue 2'de, mixinler, yeniden kullanılabilir bileşen mantığı parçaları oluşturmak için kullanılan birincil mekanizmaydı. Mixinler Vue 3'te desteklenmeye devam ederken, [Composition API kullanan Birleştirilebilir fonksiyonlar](/guide/reusability/composables) artık bileşenler arasında kod yeniden kullanımı için tercih edilen yaklaşımdır.
  :::

- **Örnek**

  ```js
  const mixin = {
    created() {
      console.log(1)
    }
  }

  createApp({
    created() {
      console.log(2)
    },
    mixins: [mixin]
  })

  // => 1
  // => 2
  ```

## extends {#extends}

Uzantı yapılacak bir "temel sınıf" bileşeni.

- **Tür**

  ```ts
  interface ComponentOptions {
    extends?: ComponentOptions
  }
  ```

- **Detaylar**

  Bir bileşenin, bileşen seçeneklerini devralarak başka bir bileşeni genişletmesine olanak tanır.

  Uygulama açısından bakıldığında, `extends`, `mixins`'e neredeyse özdeştir. `extends` tarafından belirtilen bileşen, sanki ilk mixin'miş gibi değerlendirilecektir.

  Ancak, `extends` ve `mixins` farklı niyetler ifade eder. `mixins` seçeneği öncelikle işlevsellik parçalarını oluşturmak için kullanılırken, `extends` öncelikle kalıtımla ilgilidir.

  `mixins`'te olduğu gibi, tüm seçenekler (`setup()` hariç), ilgili birleştirme stratejisi kullanılarak birleştirilecektir.

- **Örnek**

  ```js
  const CompA = { ... }

  const CompB = {
    extends: CompA,
    ...
  }
  ```

  :::warning Composition API İçin Tavsiye Edilmez
  `extends`, Options API için tasarlanmıştır ve `setup()` kancasının birleştirilmesini işlemez.

  Composition API'de, mantık yeniden kullanımı için tercih edilen zihinsel model, "kalıtım" yerine "birleştirme"dir. Bir bileşenden başka birinde yeniden kullanılması gereken mantığınız varsa, ilgili mantığı bir [Birleştirilebilir](/guide/reusability/composables#composables) içine çıkarmayı düşünün.

  Composition API kullanarak bir bileşeni hala "genişletmeyi" planlıyorsanız, temel bileşenin `setup()`'ını genişleten bileşenin `setup()`'ında çağırabilirsiniz:

  ```js
  import Base from './Base.js'
  export default {
    extends: Base,
    setup(props, ctx) {
      return {
        ...Base.setup(props, ctx),
        // yerel bağlamalar
      }
    }
  }
  ```
  :::
