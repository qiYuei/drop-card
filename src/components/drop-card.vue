<template>
  <div class="drop-card-container">
    <div
      class="card-box drop-card-border drop-card-box-shadow drop-card-box-radius"
      :class="{animation:isAnimations}"
      :style="{
        left: cardLeft+'px',
        top: cardTop+'px',
        height: cardHeight+'px',
        width: cardWidth+'px',
        backgroundColor: cardBgColor,
        zIndex: baseCardIndex+copyData.length,}"
      @touchstart="touchStart"
      @touchmove="touchMove"
      @touchcancel="touchCancel"
      @touchend="touchCancel"
    >
      <p>{{ firstItem.title }}</p>
    </div>
    <div
      v-for="(item,index) in copyData"
      :key="index"
      class="card-box drop-card-border drop-card-box-shadow drop-card-box-radius"
      :style="{
        height: cardHeight+'px',
        width: cardWidth-(index+1)*10+'px',
        zIndex:baseCardIndex+copyData.length-index-1,
        top:(index*10)+'px',
        marginLeft:(index+1)*5+'px',
        backgroundColor: cardBgColor,
      }"
    >
      <p>{{ item.title }}</p>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    baseCardIndex: {
      type: Number,
      default: 15
    },
    // 卡片数据源
    data: {
      type: Array,
      default: () => []
    },
    // 单个卡片 (首个)
    cardWidth: {
      type: String,
      default: '300px'
    },
    cardHeight: {
      type: String,
      default: '150px'
    },
    // 卡片背景颜色
    cardBgColor: {
      type: String,
      default: '#FFFFFF'
    },
    dragDirection: {
      type: String,
      default: 'all' // all,vertical,horizontal
    },
    // 卡片可活动的距离
    dragSafeDistance: {
      type: Number,
      default: 100
    },
    // 边界值 超过就 抛出卡片
    throwDistance: {
      type: Number,
      default: 100
    }
  },
  data() {
    return {
      cardLeft: 0,
      cardTop: 0,
      startLeft: 0,
      startTop: 0,
      copyData: [], // 拷贝一份数据
      firstItem: {},
      isAnimations: false,
      tmp: {}
    }
  },
  watch: {
    data: {
      handler: function(newV) {
        this.copyData = this.copyData.concat(newV)
        this.firstItem = this.copyData.shift()
      },
      deep: true,
      immediate: true
    }
  },
  methods: {
    touchStart(e) {
      if (this.isAnimations) return
      var curTouch = e.touches[0]
      // 记录手指点击时 的坐标
      this.startLeft = curTouch.clientX - this.cardLeft
      this.startTop = curTouch.clientY - this.cardTop
    },
    touchMove(e) {
      if (this.isAnimations) return
      var curTouch = e.touches[0]
      // 计算 当前位置对于可视区域的距离
      if (this.dragDirection === 'all' || this.dragDirection === 'horizontal') {
        this.cardLeft = curTouch.clientX - this.startLeft
      }

      if (this.dragDirection === 'all' || this.dragDirection === 'vertical') {
        this.cardTop = curTouch.clientX - this.startLeft
      }
    },
    touchCancel(e) {
      if (this.isAnimations) return
      // 说明已经放下 那就要计算 开启点的距离 跟 现在 元素top 是否大于某个距离
      // this.cardLeft, this.cardTop, this.startLeft, this.startTop
      const instance = this.getPointDistances(0, 0, this.cardLeft, this.cardTop)
      console.log(instance, 'instance')
      if (instance > this.throwDistance) {
        // 抛出当前卡片
        this.throwCard()
      } else {
        // 让卡片回复原位
        this.resetCard()
      }
    },
    // 计算两点间的距离
    getPointDistances(x1, y1, x2, y2) {
      const _X = Math.abs(x1 - x2)
      const _Y = Math.abs(y1 - y2)

      const distance = Math.sqrt(_X * _X + _Y * _Y)
      return distance
    },
    throwCard() {
      if (this.copyData.length === 0) {
        this.resetCard()
        return
      }
      this.isAnimations = true
      this.tmp = this.copyData.shift()

      const angle = Math.atan2((this.cardTop - 0), (this.cardLeft - 0))
      this.cardLeft = Math.cos(angle) * this.throwDistance
      this.cardTop = Math.sin(angle) * this.throwDistance

      setTimeout(() => {
        this.isAnimations = false
        this.resetCard()
      }, 400)
    },
    resetCard() {
      // 全部恢复原来的值
      this.cardLeft = 0
      this.cardTop = 0
      this.startTop = 0
      this.startLeft = 0
      this.firstItem = this.tmp
    }
  }
}
</script>

<style lang="less" scoped>
.drop-card-container {
  position: relative;
}
.card-box {
  position: absolute;
  overflow: hidden;
  box-sizing: border-box;
  padding:12px;
}
.drop-card-container .drop-card-border{
  border: 1px solid #ccc;
}

.drop-card-container .drop-card-box-shadow{
  box-shadow: 0px 4px 20px rgba(0,0,0,.3);
}
.drop-card-container .drop-card-box-radius{
  border-radius: 10px;
}
.drop-card-container .animation{
		transition: opacity .4s ease-out,left .4s ease-out,top .4s ease-out,width .4s ease-out,height .4s ease-out;
	}
</style>
