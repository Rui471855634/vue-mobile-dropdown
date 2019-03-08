<template lang="html">
  <div class="my-dropdown">
    <!-- <div class="dp-trigger" @click="showDropdown" v-if="rntValue.key">{{rntValue.value}}</div>
    <div class="dp-trigger placeholder" @click="showDropdown" v-else>{{placeholder}}</div> -->
    <slide-panel ref="slidePanel" v-if="show" :background="background">
      <template slot="header">
        <div class="slide-header">
          {{placeholder}}
          <div class="close-icon"><span class="iconfont">&#xe60f;</span></div>
        </div>
      </template>
      <template slot="main">
        <div class="dp-item-list">
          <div class="dp-item" v-for="(el, index) in options" :key="index" @click="select(el)">
            <div class="text" v-text="el.value"></div>
            <div class="check" v-if="el.check"><span class="iconfont">&#xe608;</span></div>
          </div>
        </div>
      </template>
    </slide-panel>
  </div>
</template>

<script type="ecmascript-6">
import SlidePanel from 'vue-slide-panel'
import './font/iconfont.css'
export default {
  data() {
    return {
      show: false,
      options: [],
      rntValue: {
        value: '',
        key: ''
      }
    }
  },
  props: {
    background: {
      type: String,
      default: 'transparent'
    },
    placeholder: {
      type: String,
      default: '请选择'
    },
    multiple: {
      type: Boolean,
      default: false
    },
    data: {
      type: Array,
      default: () => []
    }
  },
  components: {
    SlidePanel
  },
  mounted() {
    this.screenHeight = window.screen.height
    this.$nextTick(() => {
      this._initOptions()
    })
  },
  methods: {
    showDropdown(key) {
      this.show = true
      this.$nextTick(() => {
        this.$refs.slidePanel.showSlide()
        // 渲染选项
        this.rntValue.key = key
        this._initOptions()
      })
    },
    hideDropdown() {
      this.show = false
      this.zIndex = -1
    },
    select(el) {
      let tmp = []
      this.rntValue = {
        value: '',
        key: ''
      }
      this.options.map((e) => {
        if (e === el) {
          this.multiple ? el.check = !el.check : el.check = true
        } else if (!this.multiple) {
          e.check = false
        }
        if (e.check) {
          tmp.push(e)
        }
      })
      if (this.multiple) {
        tmp.map((e) => {
          this.rntValue.value += e.value + ' '
          this.rntValue.key += e.key + ' '
        })
      } else {
        this.rntValue = el
        setTimeout(() => {
          this.hideDropdown()
        }, 200)
      }
      this.$emit('change', this.rntValue.value.trim(), this.multiple ? this.rntValue.key.split(' ') : this.rntValue.key)
    },
    _initOptions() {
      Object.assign(this.options, this.data)
      let selectedList = this.rntValue.key.split(' ')
      this.options.map((el) => {
        this.$set(el, 'check', false)
        if (selectedList.indexOf(el.key) > -1) {
          el.check = true
        }
      })
    }
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
// @import '~./font/iconfont.css'
@import '~./mixin'
.my-dropdown
  .dp-trigger
    font-size: 14px
    height: 20px
    line-height: 20px
    &.placeholder
      color: #666
      font-size: 14px
  .slide-header
    padding-left: 20px
    position: relative
    .close-icon
      position: absolute
      right: 20px
      top: 50%
      transform: translateY(-50%)
  .dp-item-list
    .dp-item
      padding: 11px 15px
      border-top: 1px solid #dedede
      flex(row, ceter, space-between)
      font-size: 12px
      .text
        height: 16px
        line-height: 16px
      .check
        font-size: 12px
        color: rgb(64, 157, 49)

.slide-enter-active, .slide-leave-active
  transition: all 0.3s
.slide-enter, .slide-leave-to
  transform: translate3d(0, 100%, 0)
</style>
