<template functional>
  <div class="fb-layout-content__container">
    <div
      v-if="'header' in $slots"
      class="fb-layout-content__header"
    >
      <slot name="header" />
    </div>

    <div class="fb-layout-content__content">
      <slot />
    </div>

    <div
      v-if="'footer' in $slots"
      class="fb-layout-content__footer"
    >
      <slot name="footer" />
    </div>
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
} from '@vue/composition-api'

export default defineComponent({

  name: 'FbLayoutContent',

})
</script>

<style rel="stylesheet/scss" lang="scss">
@import 'index';
</style>
