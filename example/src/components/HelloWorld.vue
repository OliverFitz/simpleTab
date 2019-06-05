<template>
  <div class="hello">
    <simple-tab
      :tabs="tabs"
      @changeTab="changeTab"
      :contentClass="'content_item'"
    >
      <div class="content_item" v-for="(item, index) in tabs" :key="index">
        <span>{{item}}</span>
      </div>
    </simple-tab>
  </div>
</template>

<script>
import simpleTab from '../../lib/index'
export default {
  name: 'HelloWorld',
  components: {
    simpleTab
  },
  data() {
    return {
      tabs: ['可预约', '已预约', '预约记录', '预约订单', '全部预约', '可预约', '已预约', '预约记录', '预约订单', '全部预约'],
      // tabs: ['可预约', '已预约', '预约记录'],
      currentTab: 0
    }
  },
  methods: {
    changeTab(index) {
      this.currentTab = index
    }
  }
}
</script>

<style lang="less">
  .hello{
    height: 100%;
    width: 100%;
  }
  .content_item{
    width: 100vw;
    display: inline-block;
    background-color: #f5f5f5;
    color: #f00;
    height: 50vh;
  }
</style>
