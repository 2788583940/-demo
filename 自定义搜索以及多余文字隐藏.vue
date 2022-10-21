<template>
  <div>
    <!-- 输入框 -->
    <el-input v-model="keyword" placeholder="输入关键字" />
    <div class="list">
      <!-- 循环获取内容 -->
      <div v-for="item in fruitCopy" :key="item" class="item">
        <!-- filterAmount 是当文字大于10的时候自动回增加...将内容省略 -->
        {{ item.value| filterAmount(10) }}
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  data() {
    return {
      keyword: '', // 用户输入的内容
      fruit: [ // 这里是从接口中获取的数据
        { id: '1', value: '苹果' },
        { id: '2', value: '香蕉' },
        { id: '3', value: '椰子' },
        { id: '4', value: '芒果' },
        { id: '5', value: '荔枝' },
        { id: '6', value: '栗子' },
        { id: '7', value: '橘子橘子橘子橘子橘子橘子橘子橘子' }
      ]
    }
  },
  computed: {
    // fruitCopy 是新生成的数组，用于HTML中循环。
    'fruitCopy'() {
      // 如果关键字为空，返回所有的水果
      if (this.keyword === '') {
        return this.fruit
      } else {
        // filter为过滤
        // 当Frui里面某一项文字包含keyword文字那么就把当前数据保留
        return this.fruit.filter(item => {
          console.log(item)
          return item.value.includes(this.keyword)
        })
      }
    }

  },
  created() {
    // 过滤左侧树的文字显示是否超过10个  超过10个后显示。。。
    Vue.filter('filterAmount', function(value, n) {
      if (!n) n = 10
      if (value && value.length > n) {
        value = value.substring(0, n) + '...'
      }
      return value
    }
    )
  }
}
</script>

<style lang="scss" scoped>

</style>
