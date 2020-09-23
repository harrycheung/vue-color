<template>
  <div class="vc-light">
    <div class="vc-light-gradient" :style="{ background: gradientColor }"></div>
    <div
      class="vc-light-container"
      ref="container"
      @mousedown="handleMouseDown"
      @touchmove="handleChange"
      @touchstart="handleChange"
    >
      <div
        class="vc-light-pointer"
        :style="{ left: (1 - colors.hsl.l) * 100 + '%' }"
      >
        <div class="vc-light-picker"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Light',
  props: {
    value: Object,
    onChange: Function,
    disabled: { type: Boolean, default: false }
  },
  computed: {
    colors() {
      return this.value
    },
    gradientColor() {
      return 'linear-gradient(to right, #ffffff 0%, #000000 100%)'
    }
  },
  methods: {
    handleChange(e, skip) {
      !skip && e.preventDefault()

      if (!this.disabled) {
        var container = this.$refs.container
        var containerWidth = container.clientWidth

        var xOffset =
          container.getBoundingClientRect().left + window.pageXOffset
        var pageX = e.pageX || (e.touches ? e.touches[0].pageX : 0)
        var left = pageX - xOffset

        var l
        if (left < 0) {
          l = 1
        } else if (left > containerWidth) {
          l = 0
        } else {
          l = 1 - Math.round((left * 100) / containerWidth) / 100
        }

        if (this.colors.hsl.l !== l) {
          this.$emit('change', {
            h: this.colors.hsl.h,
            s: this.colors.hsl.s,
            l: l,
            a: this.colors.a,
            source: 'rgba'
          })
        }
      }
    },
    handleMouseDown(e) {
      if (!this.disabled) {
        this.handleChange(e, true)
        window.addEventListener('mousemove', this.handleChange)
        window.addEventListener('mouseup', this.handleMouseUp)
      }
    },
    handleMouseUp() {
      this.unbindEventListeners()
    },
    unbindEventListeners() {
      window.removeEventListener('mousemove', this.handleChange)
      window.removeEventListener('mouseup', this.handleMouseUp)
    }
  }
}
</script>

<style scoped>
.vc-light {
  position: absolute;
  top: 0px;
  right: 0px;
  bottom: 0px;
  left: 0px;
}
.vc-light-gradient {
  position: absolute;
  top: 0px;
  right: 0px;
  bottom: 0px;
  left: 0px;
}
.vc-light-container {
  cursor: pointer;
  position: relative;
  z-index: 2;
  height: 100%;
  margin: 0 3px;
}
.vc-light-pointer {
  z-index: 2;
  position: absolute;
}
.vc-light-picker {
  cursor: pointer;
  width: 4px;
  border-radius: 1px;
  height: 8px;
  box-shadow: 0 0 2px rgba(0, 0, 0, 0.6);
  background: #fff;
  margin-top: 1px;
  transform: translateX(-2px);
}
</style>
