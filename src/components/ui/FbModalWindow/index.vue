<template>
  <transition name="modal">
    <section
      v-show="show"
      id="fb-modal-container"
      v-body-scroll-lock="show"
      :class="['fb-ui-modal-window__container', {'fb-ui-modal-window__container-transparent': transparentBg}]"
      :data-size="size"
      :data-layout="layout"
      role="dialog"
      @keyup.esc="closeModal"
      @click="clickOverlay"
    >
      <div class="fb-ui-modal-window__window">
        <div
          ref="element"
          :style="{width: optionalWidth}"
          class="fb-ui-modal-window__dialog"
          role="document"
          tabindex="1"
        >
          <transition name="modal-bounce">
            <div
              v-if="loader"
              class="fb-ui-modal-window__loading"
            >
              <slot name="loader">
                <fb-ui-loading-box :size="sizeTypes.LARGE">
                  <template
                    v-if="'loading-icon' in $slots"
                    slot="icon"
                  >
                    <slot name="loading-icon" />
                  </template>

                  <slot name="loading-content" />
                </fb-ui-loading-box>
              </slot>
            </div>
          </transition>

          <slot name="content">
            <slot
              v-if="showHeader"
              name="header"
            >
              <fb-ui-modal-header
                :layout="layout"
                :right-btn-label="rightBtnLabel"
                :show-right-btn="showRightBtn"
                :left-btn-label="leftBtnLabel"
                :show-left-btn="showLeftBtn"
                :close-btn-label="closeBtnLabel"
                :enable-closing="enableClosing"
                @rightSubmit="$emit('rightSubmit', $event)"
                @leftSubmit="$emit('leftSubmit', $event)"
                @close="$emit('close', $event)"
              >
                <template
                  v-if="'title' in $slots"
                  slot="title"
                >
                  <slot name="title" />
                </template>

                <template
                  v-if="'subtitle' in $slots"
                  slot="subtitle"
                >
                  <slot name="subtitle" />
                </template>

                <template
                  v-if="'icon' in $slots"
                  slot="icon"
                >
                  <slot name="icon" />
                </template>

                <template
                  v-if="'left-button' in $slots"
                  slot="left-button"
                >
                  <slot name="left-button" />
                </template>

                <template
                  v-if="'right-button' in $slots"
                  slot="right-button"
                >
                  <slot name="right-button" />
                </template>
              </fb-ui-modal-header>
            </slot>

            <div class="fb-ui-modal-window__body">
              <fb-ui-transition-expand>
                <slot name="body" />
              </fb-ui-transition-expand>
            </div>

            <div
              v-if="showFooter && layout !== modalLayoutTypes.PHONE && layout !== modalLayoutTypes.TABLET"
              class="fb-ui-modal-window__footer"
            >
              <slot name="footer">
                <slot
                  v-if="showLeftBtn"
                  name="left-button"
                >
                  <fb-ui-button
                    :variant="buttonVariantTypes.LINK_DEFAULT"
                    :size="sizeTypes.LARGE"
                    uppercase
                    tabindex="2"
                    @click.prevent="$emit('leftSubmit', $event)"
                  >
                    {{ leftBtnLabel }}
                  </fb-ui-button>
                </slot>

                <slot
                  v-if="showRightBtn"
                  name="right-button"
                >
                  <fb-ui-button
                    :variant="buttonVariantTypes.OUTLINE_PRIMARY"
                    :size="sizeTypes.LARGE"
                    uppercase
                    tabindex="3"
                    @click.prevent="$emit('rightSubmit', $event)"
                  >
                    {{ rightBtnLabel }}
                  </fb-ui-button>
                </slot>
              </slot>
            </div>
          </slot>
        </div>
      </div>
    </section>
  </transition>
</template>

<script lang="ts">
import {
  computed,
  defineComponent,
  nextTick,
  onMounted,
  PropType,
  ref,
  SetupContext,
} from '@vue/composition-api'

import { FbSizeTypes, FbUiModalLayoutTypes, FbUiButtonVariantTypes } from '@/types'

import get from 'lodash/get'

import FbUiButton from './../FbButton/index.vue'
import FbUiModalHeader from './../FbModalHeader/index.vue'
import FbUiLoadingBox from './../FbLoadingBox/index.vue'
import FbUiTransitionExpand from './../FbTransitionExpand/index.vue'

interface FbUiModalWindowPropsInterface {
  size: FbSizeTypes
  layout: FbUiModalLayoutTypes
  width: string | number | null
  showHeader: boolean
  showFooter: boolean
  rightBtnLabel: string
  showRightBtn: boolean
  leftBtnLabel: string
  showLeftBtn: boolean
  closeBtnLabel: string
  enableClosing: boolean
  transparentBg: boolean
  loader: boolean
  show: boolean
}

export default defineComponent({

  name: 'FbUiModalWindow',

  components: {
    FbUiButton,
    FbUiModalHeader,
    FbUiLoadingBox,
    FbUiTransitionExpand,
  },

  props: {

    size: {
      type: String as PropType<FbSizeTypes>,
      default: FbSizeTypes.MEDIUM,
      validator: (value: FbSizeTypes) => {
        // The value must match one of these strings
        return [
          FbSizeTypes.SMALL,
          FbSizeTypes.MEDIUM,
          FbSizeTypes.LARGE,
        ].includes(value)
      },
    },

    layout: {
      type: String as PropType<FbUiModalLayoutTypes>,
      default: FbUiModalLayoutTypes.DEFAULT,
      validator: (value: FbUiModalLayoutTypes) => {
        // The value must match one of these strings
        return [
          FbUiModalLayoutTypes.DEFAULT,
          FbUiModalLayoutTypes.PHONE,
          FbUiModalLayoutTypes.TABLET,
        ].includes(value)
      },
    },

    width: {
      type: [String, Number],
      default: null,
    },

    showHeader: {
      type: Boolean as PropType<boolean>,
      default: true,
    },

    showFooter: {
      type: Boolean as PropType<boolean>,
      default: true,
    },

    rightBtnLabel: {
      type: String as PropType<string>,
      default: 'Ok',
    },

    showRightBtn: {
      type: Boolean as PropType<boolean>,
      default: true,
    },

    leftBtnLabel: {
      type: String as PropType<string>,
      default: 'Close',
    },

    showLeftBtn: {
      type: Boolean as PropType<boolean>,
      default: true,
    },

    closeBtnLabel: {
      type: String as PropType<string>,
      default: 'Close',
    },

    enableClosing: {
      type: Boolean as PropType<boolean>,
      default: true,
    },

    transparentBg: {
      type: Boolean as PropType<boolean>,
      default: false,
    },

    loader: {
      type: Boolean as PropType<boolean>,
      default: false,
    },

    show: {
      type: Boolean as PropType<boolean>,
      default: true,
    },

  },

  setup(props: FbUiModalWindowPropsInterface, context: SetupContext) {
    const element = ref<HTMLElement | null>(null)

    const optionalWidth = computed<string | null>((): string | null => {
      if (props.width === null) {
        return null
      } else if (typeof props.width === 'number') {
        return `${props.width}px`
      }

      return `${props.width}`
    })

    const clickOverlay = (e: Event): void => {
      if (get(e, 'target.id', null) === 'fb-modal-container') {
        if (props.enableClosing) {
          context.emit('close', e)
        }
      }
    }

    const closeModal = (e): void => {
      if (props.enableClosing) {
        context.emit('close', e)
      }
    }

    onMounted((): void => {
      nextTick(() => {
        if (element.value !== null) {
          element.value.focus()
        }
      })
    })

    return {
      element,
      optionalWidth,
      clickOverlay,
      closeModal,
      sizeTypes: FbSizeTypes,
      modalLayoutTypes: FbUiModalLayoutTypes,
      buttonVariantTypes: FbUiButtonVariantTypes,
    }
  },

})
</script>

<style rel="stylesheet/scss"
       lang="scss">
@import 'index';
</style>
