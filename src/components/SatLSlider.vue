<template>
  <div
    role="application"
    aria-label="Slider color picker"
    class="vc-slider"
    :class="{ disabled }"
  >
    <div class="vc-slider-sat-warp">
      <sat-l
        v-model="colors"
        @change="satLChange"
        :disabled="disabled"
        :hide-picker="hidePicker"
      ></sat-l>
    </div>
  </div>
</template>

<script>
import colorMixin from '../mixin/color'
import satL from './common/SatL.vue'

export default {
  name: 'SatLSlider',
  mixins: [colorMixin],
  props: {
    disabled: { type: Boolean, default: false },
    hidePicker: { type: Boolean, default: false }
  },
  components: {
    satL
  },
  methods: {
    satLChange(data) {
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
.vc-slider-sat-warp {
  height: 20px;
  position: relative;
}
.vc-slider-sat-warp >>> .vc-sat-l-picker {
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
