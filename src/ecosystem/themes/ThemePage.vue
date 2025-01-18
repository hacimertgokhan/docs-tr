<script setup lang="ts">
import ThemeHero from './ThemeHero.vue'
import ThemeList from './ThemeList.vue'
import ThemeContact from './ThemeContact.vue'
</script>

<template>
  <div class="ThemePage">
    <ThemeHero>
      <template #title>Temalar</template>
      <template #lead>Temaları, UI Kit'lerini ve eklentileri inceleyin. İş ortaklarımız tarafından Vue ile gerçek dünyada bir uygulamanın nasıl oluşturulduğunu görebilirsiniz..</template>
    </ThemeHero>

    <ThemeList />

    <ThemeContact>
      Want to feature your themes here? <a class="link" href="mailto:evan@vuejs.org?subject=Theme+affiliation">Contact us!</a>
    </ThemeContact>
  </div>
</template>
