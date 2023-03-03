<template>
  <div class="app-container">
    <!-- header头部区域 -->
    <Header></Header>

    <!-- goods商品区域 渲染每个商品的信息 -->
    <Goods v-for="item in list" 
           :key="item.id"
           :id="item.id" 
           :title="item.goods_name" 
           :pic="item.goods_img" 
           :price="item.goods_price" 
           :state="item.goods_state"
           :count="item.goods_count" 
           @state-change="getNewState"
           >
           <!-- counter组件被渲染到goods中的slot上 -->
          <Counter :num="item.goods_count" @num-change="getNewNum(item, $event)"></Counter>
    </Goods>

    <!-- footer区域 -->
    <Footer :isfull="fullState" :amount="amt" :all="total" @full-change="getFullState"></Footer>
    <!-- header头部区域 -->
  </div>
</template>

<script>
// 导入 axios 请求库
import axios from 'axios'

// 导入需要的组件
import Header from '@/components/Header/Header.vue'
import Goods from '@/components/Goods/Goods.vue'
import Footer from '@/components/Footer/Footer.vue'
// import bus from '@/components/eventBus.js'
import Counter from '@/components/Counter/Counter.vue'
export default {
  data() {
    return {
      // list用来存储列表数据
      list: [],
    }
  },

  created() {
    // this指向app这个vue实例
    this.initCartList()
    // 这是使用这是孙传子 子传父的复杂做法
    /* bus.$on('share', val => {
      this.list.some(item => {
        if(item.id === val.id){
          item.goods_count = val.value 
          return true
        }
        
      })
    }) */
  },

  components: {
    Header,
    Goods,
    Footer,
    Counter,
  },
  methods: {
    // 封装请求列表数据的函数 并在created()中调用
    async initCartList() {
      const {data: res}= await axios.get('https://www.escook.cn/api/cart')
      // 只要请求回来的数据要用到则必须转存在data中
      if(res.status === 200) {
        // 这里是this.list, 不是this.data.list
        this.list = res.list
      }
    },
    // 形参中的e就是子组件通过$emit传递到父组件中的数据
    getNewState(e) {
      console.log(e)
      // e的格式为{id, value}
      this.list.some(item => {
        if(item.id === e.id) {
          item.goods_state = e.value
          return true
        }
      })
    },
    // 接受footer子组件传递过来的全选键状态
    getFullState(val) {
      this.list.forEach(item => (item.goods_state = val))
    },
    // 这是使用slot插槽一步到位，使用了自定义事件的方法
    getNewNum(item, val) {
      item.goods_count = val
    }
  },
  computed: {
    // 子组件只要有一个false即为false 父传子
    fullState() {
      return this.list.every(item => item.goods_state)
    },
    // 已勾选商品的总价格
    amt() {
      return this.list
      .filter(item => item.goods_state)
      .reduce((total, item) => (total += item.goods_price * item.goods_count), 0)
    },
    // 已勾选商品的总数量
    total() {
      return this.list.filter(item => item.goods_state).reduce((t, item) => (t += item.goods_count), 0)

    }
  }
}
</script>

<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
