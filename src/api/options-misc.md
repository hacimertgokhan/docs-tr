# Seçenekler: Çeşitli {#options-misc}

## name {#name}

Bileşen için bir görünen ad açıkça bildirin.

- **Tür**

  ```ts
  interface ComponentOptions {
    name?: string
  }
  ```

- **Detaylar**

  Bir bileşenin adı, aşağıdakiler için kullanılır:

  - Bileşenin kendi şablonunda özyinelemeli kendine referans
  - Vue Geliştirici Araçları'nın bileşen inceleme ağacında görüntüleme
  - Uyarı bileşeni izlerinde görüntüleme

  Tek Dosyalı Bileşenler kullandığınızda, bileşen zaten kendi adını dosya adından çıkarır. Örneğin, `MyComponent.vue` adlı bir dosyanın çıkarılmış görünen adı "MyComponent" olacaktır.

  Başka bir durum ise, bir bileşen [`app.component`](/api/application#app-component) ile global olarak kaydedildiğinde, global ID'nin otomatik olarak adı olarak ayarlanmasıdır.

  `name` seçeneği, çıkarılan adı geçersiz kılmanıza veya ad çıkarılamadığında (örneğin, derleme araçları kullanılmadığında veya satır içi bir SFC olmayan bileşen) açıkça bir ad sağlamanıza olanak tanır.

  `name`'in açıkça gerekli olduğu bir durum vardır: [`<KeepAlive>`](/guide/built-ins/keep-alive)'da `include / exclude` prop'ları aracılığıyla önbelleğe alınabilir bileşenlere karşı eşleşirken.

  :::tip
  3.2.34 sürümünden bu yana, `<script setup>` kullanan tek dosya bileşeni, dosya adına göre `name` seçeneğini otomatik olarak çıkaracak ve `<KeepAlive>` ile kullanıldığında bile adı manuel olarak bildirme ihtiyacını ortadan kaldıracaktır.
  :::

## inheritAttrs {#inheritattrs}

Varsayılan bileşen özelliği geçiş davranışının etkinleştirilip etkinleştirilmeyeceğini kontrol eder.

- **Tür**

  ```ts
  interface ComponentOptions {
    inheritAttrs?: boolean // varsayılan: true
  }
  ```

- **Detaylar**

  Varsayılan olarak, prop'lar olarak tanınmayan üst kapsam özellik bağlamaları "geçer". Bu, tek köklü bir bileşenimiz olduğunda, bu bağlamaların normal HTML özellikleri olarak alt bileşenin kök öğesine uygulanacağı anlamına gelir. Bir hedef öğeyi veya başka bir bileşeni saran bir bileşen yazarken, bu her zaman istenen davranış olmayabilir. `inheritAttrs`'ı `false` olarak ayarlayarak bu varsayılan davranış devre dışı bırakılabilir. Özelliklere `$attrs` örnek özelliği aracılığıyla erişilebilir ve `v-bind` kullanılarak açıkça kök olmayan bir öğeye bağlanabilir.

- **Örnek**

  <div class="options-api">

  ```vue
  <script>
  export default {
    inheritAttrs: false,
    props: ['label', 'value'],
    emits: ['input']
  }
  </script>

  <template>
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      />
    </label>
  </template>
  ```

  </div>
  <div class="composition-api">

  Bu seçeneği `<script setup>` kullanan bir bileşende bildirirken, [`defineOptions`](/api/sfc-script-setup#defineoptions) makrosunu kullanabilirsiniz:

  ```vue
  <script setup>
  defineProps(['label', 'value'])
  defineEmits(['input'])
  defineOptions({
    inheritAttrs: false
  })
  </script>

  <template>
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      />
    </label>
  </template>
  ```

  </div>

- **Ayrıca bakınız**

  - [Geçiş Özellikleri](/guide/components/attrs)
    <div class="composition-api">
  - [Normal `<script>` içinde `inheritAttrs` Kullanma](/api/sfc-script-setup.html#usage-alongside-normal-script)
    </div>

## components {#components}

Bileşen örneği için kullanılabilir hale getirilecek bileşenleri kaydeden bir nesne.

- **Tür**

  ```ts
  interface ComponentOptions {
    components?: { [key: string]: Component }
  }
  ```

- **Örnek**

  ```js
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'

  export default {
    components: {
      // kısayol
      Foo,
      // farklı bir ad altında kaydet
      RenamedBar: Bar
    }
  }
  ```

- **Ayrıca bakınız** [Bileşen Kaydı](/guide/components/registration)

## directives {#directives}

Bileşen örneği için kullanılabilir hale getirilecek direktifleri kaydeden bir nesne.

- **Tür**

  ```ts
  interface ComponentOptions {
    directives?: { [key: string]: Directive }
  }
  ```

- **Örnek**

  ```js
  export default {
    directives: {
      // şablonda v-focus'u etkinleştirir
      focus: {
        mounted(el) {
          el.focus()
        }
      }
    }
  }
  ```

  ```vue-html
  <input v-focus>
  ```

- **Ayrıca bakınız** [Özel Direktifler](/guide/reusability/custom-directives)
