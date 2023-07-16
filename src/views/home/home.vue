<template>
  <div class="home">
    <button class="btn" @click="handleLogin">登录</button>

    <div class="div"></div>
    <div class="div"></div>
    <div class="div"></div>
    <div class="div"></div>
    <div class="div"></div>
    <div class="div"></div>
  </div>

  <my-dialog ref="myDialogRef" custom-class="mlDialog">
    <div class="box">123</div>
  </my-dialog>
</template>

<script lang="ts" setup>
import MyDialog from '@/components/my-dialog/my-dialog.vue'
import { ref } from 'vue'

const myDialogRef = ref<any>()

const handleLogin = () => {
  myDialogRef.value?.openDialog()
}
</script>

<style lang="less" scoped>
.btn {
  width: 100px;
  height: 50px;
  border: 1px solid pink;
  cursor: pointer;
}
.div {
  width: 200px;
  height: 500px;
  border: 1px solid red;
}

.box {
  font-size: 30px;
  color: cyan;
  height: 800px;
}

:global(.mlDialog) {
  .close {
    color: red;
  }
}
</style>
