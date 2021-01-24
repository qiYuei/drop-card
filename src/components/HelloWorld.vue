<template>
  <div style="width:300px;height:200px;margin:0 auto;">
    <dropCard :data="cards" card-width="300" card-height="180" card-bg-color="#ffffff" drag-direction="horizontal" />
  </div>

</template>

<script>
import dropCard from '@/components/drop-card'
export default {
  components: {
    dropCard
  },
  data() {
    return {
      cards: [
        {
          title: 'js数组怎么遍历删除指定元素，最后全部删除？',
          followcount: 6
        },
        {
          title: '前端自学，前后端的交互应该学哪些，要重点学哪里？',
          followcount: 13
        },
        {
          title:
            '我们花那么多时间在游戏《塞尔达传说：旷野之息》里，到底得到了什么？',
          followcount: 29
        },
        {
          title: '跪求这个图片用HTML，CSS如何做出来？',
          followcount: 6
        }
      ]
    }
  }
}
</script>

<style>

</style>
