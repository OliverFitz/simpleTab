<template>
  <div class="simple_tab">
    <div class="tab_header">
      <div class="inner_header" ref="inner_header">
        <div :class="['tab_header_item', currentTab === index ? 'activity' : '']" v-for="(item, index) in tabs" :key="index" @click="selectTab(index)">
          <span>{{item}}</span>
        </div>
      </div>
    </div>
    <div class="tab_content">
      <div class="inner_content" ref="inner_content">
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'simpleTab',
  props: {
    tabs: {
      type: Array,
      default: () => {
        return []
      }
    },
    contentClass: {
      type: String,
      default: 'content_item'
    }
  },
  data() {
    return {
      currentTab: 0
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.setItemWith()
      this.setHeaderTouch()
      this.setContentTouch()
    })
  },
  methods: {
    selectTab(index) {
      this.currentTab = index
      this.$emit('changeTab', index)
      this.slideTo(index, 'header', 'inner_header', 'tab_header_item')
      this.slideTo(index, 'content', 'inner_content', this.contentClass)
    },
    setItemWith() {
      const allItems = document.getElementsByClassName('tab_header_item')
      if (this.tabs.length === 1) {
        allItems[0].style.width = '100%'
      } else if (this.tabs.length === 2) {
        allItems[0].style.width = '50%'
        allItems[1].style.width = '50%'
      } else if (this.tabs.length === 3) {
        allItems[0].style.width = '33.33%'
        allItems[1].style.width = '33.33%'
        allItems[2].style.width = '33.33%'
      } else {
        this.tabs.forEach((tab, index) => {
          allItems[index].style.width = 'auto'
        })
      }
    },
    setHeaderTouch() {
      const _header = this.$refs.inner_header
      const headerWidth = _header.clientWidth
      const bodyWidth = document.body.clientWidth
      let maxDistance = headerWidth - bodyWidth
      maxDistance = maxDistance > 0 ? maxDistance : 0
      let startX = 0
      let startY = 0
      let endX = 0
      let endY = 0
      let lastX = 0
      _header.addEventListener('touchstart', (e) => {
        startX = e.touches[0].clientX
        startY = e.touches[0].clientY
      })
      _header.addEventListener('touchmove', (e) => {
        e.preventDefault()
        endX = e.changedTouches[0].clientX
        endY = e.changedTouches[0].clientY
        if (Math.abs(endX - startX) > 10 && Math.abs(endY - startY) < 10) {
          lastX = _header.style.transform.replace(/[^0-9\-,]/g, '') - 0
          const distance = endX - startX
          let allDistance = distance + lastX
          allDistance = allDistance > 0 ? 0 : allDistance
          allDistance = -allDistance > maxDistance ? -maxDistance : allDistance
          lastX = allDistance
          _header.style.transform = `translateX(${allDistance}px)`
        }
      })
    },
    slideTo(index, model, warpRef, targetClass) {
      const _wrap = this.$refs[warpRef]
      const headerWidth = _wrap.clientWidth
      const bodyWidth = document.body.clientWidth
      let maxDistance = headerWidth - bodyWidth
      maxDistance = maxDistance > 0 ? maxDistance : 0
      const targetItem = document.getElementsByClassName(targetClass)[index]
      const targetLeft = targetItem.offsetLeft
      let targetX = model === 'header' ? (targetLeft - (bodyWidth / 2 - 50)) : targetLeft
      targetX = targetX > 0 ? targetX : 0
      targetX = targetX > maxDistance ? maxDistance : targetX
      _wrap.style.transform = `translateX(${-targetX}px)`
      _wrap.style.transition = `all 0.5s`
    },
    setContentTouch() {
      const _content = this.$refs.inner_content
      const bodyWidth = document.body.clientWidth
      const allContentItem = document.getElementsByClassName(this.contentClass)
      let startX = 0
      let startY = 0
      let moveX = 0
      let moveY = 0
      let endX = 0
      let endY = 0
      let lastX = 0
      _content.addEventListener('touchstart', (e) => {
        startX = e.touches[0].clientX
        startY = e.touches[0].clientY
        lastX = _content.style.transform.replace(/[^0-9\-,]/g, '') - 0
      })
       _content.addEventListener('touchmove', (e) => {
        moveX = e.changedTouches[0].clientX
        moveY = e.changedTouches[0].clientY
        if (Math.abs(moveX - startX) > 20 && Math.abs(moveY - startY) < 40) {
          e.preventDefault()
          let distance = moveX - startX
          distance = distance > bodyWidth ? bodyWidth : distance
          distance = distance < -bodyWidth ? -bodyWidth : distance
          distance = distance + lastX
          _content.style.transform = `translateX(${distance}px)`
        }
      })
      _content.addEventListener('touchend', (e) => {
        endX = e.changedTouches[0].clientX
        endY = e.changedTouches[0].clientY
        if (Math.abs(endX - startX) > 20 && Math.abs(endY - startY) < 40) {
          const distance = endX - startX
          const currentItem = allContentItem[this.currentTab]
          const currentItemWidth = currentItem.clientWidth
          if (distance > 0) {
            this.currentTab > 0 && this.currentTab--
          } else {
            this.currentTab < this.tabs.length - 1 && this.currentTab++
          }
          this.selectTab(this.currentTab)
        } else {
          _content.style.transform = `translateX(${lastX}px)`
        }
      })
    }
  }
}
</script>

<style lang="less">
  .simple_tab{
    width: 100%;
    height: 44px;
    line-height: 44px;
    font-size: 14px;
  }
  .tab_header{
    width: 100%;
    height: 100%;
    overflow: hidden;
    background-color: #fff;
    white-space: nowrap;
  }
  .inner_header{
    min-width: 100%;
    width: auto;
    height: 100%;
    display: inline-block;
    position: relative;
  }
  .tab_header_item {
    height: 100%;
    display: inline-block;
    flex-wrap: nowrap;
    text-align: center;
    padding: 0 10px;
  }
  .tab_header_item span{
    display: inline-block;
    height: 100%;
    padding: 0 5px;
  }
  .tab_header_item.activity span{
    border-bottom: solid 2px #df3448;
  }
  .tab_content{
    width: 100%;
    overflow: hidden;
    white-space: nowrap;
    .inner_content{
      min-width: 100%;
      width: auto;
      display: inline-block;
      position: relative;
    }
  }
</style>