<template>
  <div
    role="application"
    aria-label="Slider color picker"
    class="vc-slider"
    :class="{ disabled }"
  >
    <div class="vc-slider-gray-warp">
      <light
        v-model="colors"
        @change="lightChange"
        :disabled="disabled"
        :hide-picker="hidePicker"
      ></light>
    </div>
  </div>
</template>

<script>
import colorMixin from '../mixin/color'
import light from './common/Light.vue'

export default {
  name: 'LightSlider',
  mixins: [colorMixin],
  props: {
    swatches: {
      type: Array,
      default() {
        return ['.80', '.65', '.50', '.35', '.20']
      }
    },
    disabled: { type: Boolean, default: false },
    hidePicker: { type: Boolean, default: false }
  },
  components: {
    light
  },
  computed: {
    activeOffset() {
      const hasBlack = this.swatches.includes('0')
      const hasWhite = this.swatches.includes('1')
      const hsl = this.colors.hsl

      if (Math.round(hsl.s * 100) / 100 === 0.5) {
        return Math.round(hsl.l * 100) / 100
      } else if (hasBlack && hsl.l === 0) {
        return 0
      } else if (hasWhite && hsl.l === 1) {
        return 1
      }
      return -1
    }
  },
  methods: {
    lightChange(data) {
      this.colorChange(data)
    }
  }
}
</script>

<style scoped>
.vc-slider {
  position: relative;
  width: 410px;
}
.vc-slider-gray-warp {
  height: 20px;
  position: relative;
}
.vc-slider-gray-warp >>> .vc-light-picker {
  width: 8px;
  height: 24px;
  border-radius: 0;
  transform: translate(-4px, -3px);
  background-color: rgb(248, 248, 248);
  box-shadow: 0 1px 4px 0 rgba(0, 0, 0, 0.37);
}
.disabled {
  opacity: 0.3;
}
</style>
