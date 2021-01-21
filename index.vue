<template>
  <div class="mdrawer" :class="drawerStyleList" ref="mdrawer">
    <div class="mdrawer-wrap">
      <div class="mdrawer-close" @click="close"></div>
      <header class="mdrawer-header">
        <h5 class="title">{{ title }}</h5>
        <div class="actions">
          <!-- 头部操作区域插槽 -->
          <slot name="actions"></slot>
        </div>
      </header>
      <main class="mdrawer-content">
        <!-- 内容区域默认插槽 -->
        <slot></slot>
      </main>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Emit, Ref } from 'vue-property-decorator'
@Component({
  components: {
  },
})
export default class MumuDrawer extends Vue {
  @Prop({
    type: Boolean,
    default: false
  }) private value!: boolean
  @Prop({
    type: String,
    default: ''
  }) private title!: string
  @Prop({
    type: String,
    default: 'fixed'
  }) private position!: string
  @Prop(String) private width: string

  @Ref('mdrawer') readonly mDrawer: HTMLDivElement

  // computed
  get drawerStyleList () {
    // 如果是打开侧拉，给侧拉添加打开样式
    let style = []
    if (this.value) {
      if (this.position && this.position === 'absolute') {
        style = ['absolute', 'out']
      } else {
        style = ['fix', 'out']
      }
    } else {
      // 如果是关闭侧拉
      if (this.position && this.position === 'absolute') {
        style = ['absolute']
      } else {
        style = ['fix']
      }
    }
    return style
  }

  mounted() {
    this.init()
  }

  // methods
  public init() {
    const mDrawer = this.mDrawer
    // 设置侧拉宽度、初始定位、过渡效果
    if (this.width) {
      const numReg = /^[1-9]{1}\d*$/
      const pxReg = /^[1-9]{1}\d*(px)$/

      if (numReg.test(this.width)) {
        mDrawer.style.marginRight = -parseInt(this.width, 10) - 24 + 'px'
        mDrawer.style.width = this.width + 'px'
      } else if (pxReg.test(this.width)) {
        const w = parseInt(this.width.replace('px', ''), 10)
        mDrawer.style.marginRight = -(w + 24) + 'px'
        mDrawer.style.width = this.width
      }
    }
    setTimeout(() => {
      mDrawer.style.transition = 'margin .2s ease'
    })
  }
  /**
   * 关闭侧拉
   */
  @Emit('input')
  public close() {
    return false
  }
}
</script>
<style lang="less" scoped>
.mdrawer {
  background: white;
  box-shadow: -6px 0px 20px 0px #f7f6f6;
  margin-right: ~'calc(500px - 24px - 100vw)';
  width: ~'calc(100vw - 500px)';
  &.fix {
    position: fixed;
    top: 110px;
    right: 0;
    height: ~'calc(100vh - 110px - 10px)';
    z-index: 99;
    &.out {
      margin-right: 0 !important;
    }
  }
  &.absolute {
    position: absolute;
    top: 0;
    right: 0;
    height: 100%;
    z-index: 99;
    &.out {
      margin-right: 0 !important;
    }
  }
  .mdrawer-wrap {
    position: relative;
    box-sizing: border-box;
    width: 100%;
    height: 100%;
    .mdrawer-close {
      position: absolute;
      top: 0;
      left: -24px;
      height: 56px;
      width: 24px;
      border-radius: 6px 0 0 6px;
      background: black;
      opacity: 0.7;
      cursor: pointer;
      &::after {
        content: '';
        position: absolute;
        top: 19px;
        left: 9px;
        border: 8px solid transparent;
        border-left-color: #ffffff;
      }
    }
    .mdrawer-header {
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-weight: 700;
      padding: 16px 24px;
      box-sizing: border-box;
      margin: 0;
      padding: 0 24px;
      height: 56px;
      box-shadow: 0 0px 10px 4px #f6f6f6;
      border: 1px solid #ebebeb;
      .title {
        color: @primary-color;
        font-size: 18px;
        font-weight: 400;
      }
      .actions {
      }
    }
    .mdrawer-content {
      box-sizing: border-box;
      padding: 0 0;
      height: ~'calc(100% - 56px)';
      overflow: hidden;
      overflow-y: auto;
    }
  }
}
</style>
