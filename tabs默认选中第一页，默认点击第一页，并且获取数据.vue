<template>
  <div style="padding:20px">
    <el-tabs v-model="activeName" type="border-card" @tab-click="handleClick">
      <el-tab-pane v-for="(item,index) in tabsObj" :key="item.name" :canshu="item" :label="item.label" :name="index+''">
        {{ item.label }}
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
export default {
  data () {
    return {
      activeName: '0',
      tabsObj: [
        { name: '1', label: '列表一' },
        { name: '2', label: '列表二' },
        { name: '3', label: '列表三' },
        { name: '4', label: '列表四' },
        { name: '5', label: '列表五' }
      ]
    }
  },
  created () {
    // click报错的话 加个延迟即可   默认点击一下第一个tabs页面，也可用于刷新数据。
    this.$nextTick(() => {
      setTimeout(() => {
        document.querySelector('.el-tabs--border-card>.el-tabs__header .el-tabs__item.is-active').click()
      }, 500)
    })
  },
  methods: {
    handleClick (tab, event) {
      console.log('页面加载完成后自动选择第一个tab页面，数据为：' + tab.label, tab.$attrs.canshu)
    }
  }
}
</script>

<style>

</style>
