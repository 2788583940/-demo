<template>
  <div style="padding:20px">
    <el-tree ref="zhaoning" :data="data" :expand-on-click-node="false" show-checkbox :props="defaultProps" @check="treeNodeClick" @node-click="handleNodeClick" />
  </div>
</template>

<script>
export default {
  data () {
    return {
      data: [
        {
          label: '一级 1',
          children: [
            {
              label: '二级 1-1',
              children: [
                {
                  label: '三级 1-1-1'
                }
              ]
            }
          ]
        },
        {
          label: '一级 2',
          children: [
            {
              label: '二级 2-1',
              children: [
                {
                  label: '三级 2-1-1'
                }
              ]
            },
            {
              label: '二级 2-2',
              children: [
                {
                  label: '三级 2-2-1'
                }
              ]
            }
          ]
        },
        {
          label: '一级 3',
          children: [
            {
              label: '二级 3-1',
              children: [
                {
                  label: '三级 3-1-1'
                }
              ]
            },
            {
              label: '二级 3-2',
              children: [
                {
                  label: '三级 3-2-1'
                }
              ]
            }
          ]
        }
      ],
      defaultProps: {
        children: 'children',
        label: 'label'
      }
    }
  },
  created () {
    // 默认选择第一个树节点
    setTimeout(() => {
      document.querySelector('.el-tree-node__content').click()
    }, 500)
  },
  methods: {
    handleNodeClick (data) {
      console.log('这是点击树获取的数据', data)
    },
    treeNodeClick (a, b) {
      console.log('这是点击复选框获取的数据', a, b)
    }
  }
}
</script>

<style>
</style>
