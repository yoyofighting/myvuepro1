<template>
  <div class="number-container d-flex justify-content-center align-items-center">
    <!-- 减 1 的按钮 -->
    <button type="button" class="btn btn-light btn-sm" @click="sub">-</button>
    <!-- 购买的数量 -->
    <span class="number-box">{{num}}</span>
    <!-- 加 1 的按钮 -->
    <button type="button" class="btn btn-light btn-sm" @click="add">+</button>
  </div>
</template>

<script>
// import bus from '@/components/eventBus.js'

export default {
  props: {
    num: {
      type: Number,
      default: 1,
    },
    id: {
      type: Number,
      required: true,
    }
  },
  methods: {
    add() {
      // 这是孙传子 子传父的复杂做法
      /* const obj = {id: this.id, value: this.num + 1}
      bus.$emit('share', obj) */
      // 这是使用slot插槽一步到位，使用了自定义事件的方法
      this.$emit('num-change', this.num + 1)
    },
    sub() {
      if(this.num - 1 === 0) return
      /* const sub1 = this.num - 1
      const obj = {id: this.id, value: sub1}
      bus.$emit('share', obj) */
      this.$emit('num-change', this.num - 1)
    } 
  }
}
</script>

<style lang="less" scoped>
.number-box {
  min-width: 30px;
  text-align: center;
  margin: 0 5px;
  font-size: 12px;
}

.btn-sm {
  width: 30px;
}
</style>
