<template>
  <teleport to="body">
    <transition name="fade">
      <div
        class="my-dialog-mask"
        :class="customClass"
        v-if="isShow"
        :style="{ backgroundColor }"
        @click.self="handleCloseOutside"
      >
        <div class="my-dialog" :style="{ top, ...dialogStyle }">
          <div class="close">X</div>

          <div class="my-dialog-header">
            <slot name="header"></slot>
          </div>

          <div class="my-dialog-main">
            <slot></slot>
          </div>

          <div class="my-dialog-footer">
            <slot name="footer"></slot>
          </div>
        </div>
      </div>
    </transition>
  </teleport>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import { useScrollLock } from '@vueuse/core'

// ====== props
interface IProps {
  customClass: string
  isAllowedCloseOutside?: boolean
  top?: string
  backgroundColor?: string
  dialogStyle?: object
}
const props = withDefaults(defineProps<IProps>(), {
  isAllowedCloseOutside: true,
  top: '20vh',
  backgroundColor: 'rgba(0, 0, 0, 0.5)'
})

// ====== data
const isLocked = useScrollLock(document.body)
const isShow = ref(false)

// ====== methods
const openDialog = () => {
  isShow.value = true
  isLocked.value = true
}
const closeDialog = () => {
  isShow.value = false
  isLocked.value = false
}
const handleCloseOutside = () => {
  if (!props.isAllowedCloseOutside) return
  closeDialog()
}

// ====== expose
defineExpose({ openDialog, closeDialog })
</script>

<style lang="less" scoped>
.my-dialog-mask {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  width: 100vw;
  height: 100vh;
  overflow-y: auto;
  box-sizing: border-box;
  z-index: 1997;

  .my-dialog {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    min-width: 400px;
    min-height: 400px;
    background-color: #ffffff;
    border-radius: 10px;

    .close {
      font-size: 20px;
      position: absolute;
      top: 10px;
      right: -40px;
      cursor: pointer;
    }
  }
  .my-dialog::after {
    content: '';
    position: absolute;
    bottom: -50px;
    display: block;
    width: 100%;
    height: 1px;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: all 0.3s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>

<!-- 
1.显示和隐藏
  - 控制isShow私有化，只暴露对应方法
  - 是否可点击遮罩层隐藏

2.样式和mask遮罩层
  - 全屏遮罩层
  - 添加一个显示和隐藏的过渡效果（transition标签）
  - 考虑如果溢出怎么滚动
  - 考虑如果想要底部留出部分空隙的方法（利用伪元素）

3.自定义内容
  - 利用插槽

4.页面元素滚动与弹窗滚动之间产生的bug
  - 开启时设置body的overflow为hidden，关闭时去除添加的属性

5.多实例之间样式冲突问题
  - 给每个实例传递单独的类名（如果想针对某个弹窗设置样式，可以利用:global()，为了互不影响请通过自定义的类名来修改）
-->
