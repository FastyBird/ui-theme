<template>
  <li class="fb-layout-navigation-item__container">
    <a
      v-if="type === menuItemTypes.LINK"
      :href="link"
      @click.prevent="$emit('click', $event)"
    >
      <span
        v-if="'icon' in $slots"
        class="fb-layout-navigation-item__icon"
      >
        <slot name="icon" />
      </span>
      <span class="fb-layout-navigation-item__label">{{ label }}</span>
    </a>

    <nuxt-link
      v-else-if="type === menuItemTypes.NUXT_LINK"
      :to="link"
      active-class="fb-layout-navigation-item__active"
      @click.prevent="$emit('click', $event)"
    >
      <span
        v-if="'icon' in $slots"
        class="fb-layout-navigation-item__icon"
      >
        <slot name="icon" />
      </span>
      <span class="fb-layout-navigation-item__label">{{ label }}</span>
    </nuxt-link>

    <button
      v-else-if="type === menuItemTypes.BUTTON"
      @click.prevent="$emit('click', $event)"
    >
      <span
        v-if="'icon' in $slots"
        class="fb-layout-navigation-item__icon"
      >
        <slot name="icon" />
      </span>
      <span class="fb-layout-navigation-item__label">{{ label }}</span>
    </button>
  </li>
</template>

<script lang="ts">
import {
  defineComponent,
  PropType,
} from '@vue/composition-api'

import { FbMenuItemTypes } from '@/types'

export default defineComponent({

  name: 'FbLayoutNavigationItem',

  props: {

    type: {
      type: String as PropType<FbMenuItemTypes>,
      required: true,
      validator: (value: FbMenuItemTypes) => {
        // The value must match one of these strings
        return [
          FbMenuItemTypes.BUTTON,
          FbMenuItemTypes.LINK,
          FbMenuItemTypes.NUXT_LINK,
        ].includes(value)
      },
    },

    label: {
      type: String as PropType<string>,
      required: true,
    },

    link: {
      type: String as PropType<string>,
      default: null,
    },

  },

  setup() {
    return {
      menuItemTypes: FbMenuItemTypes,
    }
  },

})
</script>

<style lang="scss">
@import 'index';
</style>
