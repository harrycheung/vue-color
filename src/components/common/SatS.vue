<template>
  <div class="vc-sat-s" :style="{ background: bgColor }">
    <div :class="directionClass"></div>
    <div
      class="vc-sat-s-container"
      role="slider"
      :aria-valuenow="colors.hsv.s"
      aria-valuemin="0"
      aria-valuemax="100"
      ref="container"
      @mousedown="handleMouseDown"
      @touchmove="handleChange"
      @touchstart="handleChange"
    >
      <div
        class="vc-sat-s-pointer"
        :style="{ top: pointerTop, left: pointerLeft }"
        role="presentation"
      >
        <div
          class="vc-sat-s-picker"
          :class="{ hidden: disabled || hidePicker }"
        ></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SatS',
  props: {
    value: Object,
    direction: {
      type: String,
      // [horizontal | vertical]
      default: 'horizontal'
    },
    disabled: { type: Boolean, default: false },
    hidePicker: { type: Boolean, default: false }
  },
  data() {
    return {
      oldHue: 0,
      pullDirection: ''
    }
  },
  computed: {
    colors() {
      return this.value
    },
    bgColor() {
      // determine the lightness in the range [0,100]
      const l = (this.colors.hsv.v * 100) / 2
      let s = (this.colors.hsv.v * 100 * 100) / (l < 50 ? l * 2 : 200 - l * 2)

      // correct a division-by-zero error
      if (isNaN(s)) s = 0

      return `hsl(${this.colors.hsv.h},${s}%,${l}%)`
    },
    directionClass() {
      return {
        'vc-sat-s--horizontal': this.direction === 'horizontal',
        'vc-sat-s--vertical': this.direction === 'vertical'
      }
    },
    pointerTop() {
      if (this.direction === 'vertical') {
        if (this.colors.hsv.s === 0 && this.pullDirection === 'right') return 0
        return -this.colors.hsv.s * 100 + '%'
      } else {
        return 0
      }
    },
    pointerLeft() {
      if (this.direction === 'vertical') {
        return 0
      } else {
        if (this.colors.hsv.s === 0 && this.pullDirection === 'right') {
          return '100%'
        }
        return this.colors.hsv.s * 100 + '%'
      }
    }
  },
  methods: {
    handleChange(e, skip) {
      !skip && e.preventDefault()

      if (!this.disabled) {
        var container = this.$refs.container
        var containerWidth = container.clientWidth
        var containerHeight = container.clientHeight

        var xOffset =
          container.getBoundingClientRect().left + window.pageXOffset
        var yOffset = container.getBoundingClientRect().top + window.pageYOffset
        var pageX = e.pageX || (e.touches ? e.touches[0].pageX : 0)
        var pageY = e.pageY || (e.touches ? e.touches[0].pageY : 0)
        var left = pageX - xOffset
        var top = pageY - yOffset

        var s

        if (this.direction === 'vertical') {
          if (top < 0) {
            s = 100
          } else if (top > containerHeight) {
            s = 0
          } else {
            s = (-((top * 100) / containerHeight) + 100) / 100
          }

          if (this.colors.hsv.s !== s) {
            this.$emit('change', {
              h: this.colors.hsv.h,
              s,
              v: this.colors.hsv.v,
              source: 'hsv'
            })
          }
        } else {
          if (left < 0) {
            s = 0
          } else if (left > containerWidth) {
            s = 100
          } else {
            s = (left * 100) / containerWidth / 100
          }

          if (this.colors.hsv.s !== s) {
            this.$emit('change', {
              h: this.colors.hsv.h,
              s,
              v: this.colors.hsv.v,
              source: 'hsv'
            })
          }
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
    handleMouseUp(e) {
      this.unbindEventListeners()
    },
    unbindEventListeners() {
      window.removeEventListener('mousemove', this.handleChange)
      window.removeEventListener('mouseup', this.handleMouseUp)
    }
  }
}
</script>

<style>
.vc-sat-s,
.vc-sat-s--horizontal,
.vc-sat-s--vertical {
  position: absolute;
  top: 0px;
  right: 0px;
  bottom: 0px;
  left: 0px;
  border-radius: 2px;
}
.vc-sat-s--horizontal {
  background: linear-gradient(to right, #fff, rgba(255, 255, 255, 0));
}
.vc-sat-s--vertical {
  background: linear-gradient(to bottom, #fff, rgba(255, 255, 255, 0));
}
.vc-sat-s-container {
  cursor: pointer;
  margin: 0 2px;
  position: relative;
  height: 100%;
}
.vc-sat-s-pointer {
  z-index: 2;
  position: absolute;
}
.vc-sat-s-picker {
  cursor: pointer;
  margin-top: 1px;
  width: 4px;
  border-radius: 1px;
  height: 8px;
  box-shadow: 0 0 2px rgba(0, 0, 0, 0.6);
  background: #fff;
  transform: translateX(-2px);
}
.vc-sat-s-picker.hidden {
  display: none;
}
</style>
