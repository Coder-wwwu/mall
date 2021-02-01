<!--  -->
<template>
  <div class="goods">
    <good-list-item v-for="item in goods" :goodItem="item"></good-list-item>
  </div>
</template>

<script>
import GoodListItem from './GoodListItem.vue'
export default {
  components: { GoodListItem },
  data () {
    return {
    }
  },
  props:{
    goods:{
      type:Array,
      default(){
        return []
      }
    }
  }
}
</script>

<style  scoped>
.goods{
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  padding: 2px;
}
</style>
