<template>
  <div class="vc-sat-l" :style="{ background: bgColor }">
    <div :class="directionClass"></div>
    <div
      class="vc-sat-l-container"
      role="slider"
      :aria-valuenow="colors.hsl.l"
      aria-valuemin="0"
      aria-valuemax="100"
      ref="container"
      @mousedown="handleMouseDown"
      @touchmove="handleChange"
      @touchstart="handleChange"
    >
      <div
        class="vc-sat-l-pointer"
        :style="{ top: pointerTop, left: pointerLeft }"
        role="presentation"
      >
        <div
          class="vc-sat-l-picker"
          :class="{ hidden: disabled || hidePicker }"
        ></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SatL',
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
      return `hsl(${this.colors.hsl.h}, ${this.colors.hsl.s * 100}%, 50%)`
    },
    directionClass() {
      return {
        'vc-sat-l--horizontal': this.direction === 'horizontal',
        'vc-sat-l--vertical': this.direction === 'vertical'
      }
    },
    pointerTop() {
      if (this.direction === 'vertical') {
        if (this.colors.hsl.l === 0 && this.pullDirection === 'right') return 0
        return -this.colors.hsl.l * 100 + '%'
      } else {
        return 0
      }
    },
    pointerLeft() {
      if (this.direction === 'vertical') {
        return 0
      } else {
        if (this.colors.hsl.h === 0 && this.pullDirection === 'right') {
          return '100%'
        }
        return this.colors.hsl.l * 100 + '%'
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

        var l

        if (this.direction === 'vertical') {
          if (top < 0) {
            l = 100
          } else if (top > containerHeight) {
            l = 0
          } else {
            l = (-((top * 100) / containerHeight) + 100) / 100
          }

          if (this.colors.hsl.l !== l) {
            this.$emit('change', {
              h: this.colors.hsl.h,
              s: this.colors.hsl.s,
              l,
              a: this.colors.hsl.a,
              source: 'hsl'
            })
          }
        } else {
          if (left < 0) {
            l = 0
          } else if (left > containerWidth) {
            l = 100
          } else {
            l = (left * 100) / containerWidth / 100
          }

          if (this.colors.hsl.l !== l) {
            this.$emit('change', {
              h: this.colors.hsl.h,
              s: this.colors.hsl.s,
              l,
              a: this.colors.hsl.a,
              source: 'hsl'
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
.vc-sat-l,
.vc-sat-l--horizontal,
.vc-sat-l--vertical {
  position: absolute;
  top: 0px;
  right: 0px;
  bottom: 0px;
  left: 0px;
  border-radius: 2px;
}
.vc-sat-l--horizontal {
  background: linear-gradient(to right, #000, rgba(0, 0, 0, 0));
}
.vc-sat-l--vertical {
  background: linear-gradient(to bottom, #000, rgba(0, 0, 0, 0));
}
.vc-sat-l-container {
  cursor: pointer;
  margin: 0 2px;
  position: relative;
  height: 100%;
}
.vc-sat-l-pointer {
  z-index: 2;
  position: absolute;
}
.vc-sat-l-picker {
  cursor: pointer;
  margin-top: 1px;
  width: 4px;
  border-radius: 1px;
  height: 8px;
  box-shadow: 0 0 2px rgba(0, 0, 0, 0.6);
  background: #fff;
  transform: translateX(-2px);
}
.vc-sat-l-picker.hidden {
  display: none;
}
</style>
