<template>
  <div 
    class="virtual_container"
    ref="virtual_list"
    :style="{ height: `${viewHeight}px`}">
    <div class="phantom" :style="{ height: `${listHeight}px`}"></div>
    <div class="view_content">
      <div
        ref="items"
        class="view_items"
        v-for="(item,index) of data"
        :key="index">
        <slot :data="item"></slot>
      </div>    
    </div>       
  </div>
</template>
<script>
export default {
    props: {
      // data is required  
      data: { type: Array, required: true },
      // render window height
      viewHeight: { type: Number },
      pageModel: { type: String, default: 'normal' }
    },
    computed: {
      listHeight() {
          if (this.positions.length) {
              return this.positions[this.positions.length - 1].bottom;
          } else {
              throw Error('data is required!');
          }
      },
    },
    data() {
      return {
        positions: [],
      };
    }
}
</script>
<style lang="css" scoped>
.virtual_container {
    overflow: scroll;
}
</style>