<template>
  <div 
    class="virtual_container"
    ref="virtual_list"
    :style="{ height: `${viewHeight}px`}">
    <div class="phantom" :style="{ height: `${listHeight}px`}"></div>
    <div class="view_content" :style="{ transform: transformValue }">
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
/**
@params scrollTop  滚动高度
@params listHeight 列表总高度
@params startIndex 视图渲染的起始索引
@params endIndex 视图渲染的结束索引

*/
export default {
    props: {
      // data is required  
      data: { type: Array, required: true },
      // render window height
      viewHeight: { type: Number },
      bufferScale: { type: Number, default: 0.5 },
      itemSize: { type: Number, default: 24 },
      // nomal | dynamic | log
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
      transformValue() {
        return `translate3d(0,${this.startOffset}px,0)`;
      },
      aboveCount() {
        return Math.min(this.start,this.bufferScale * this.renderCount)
      },
      belowCount() {
        return Math.min(this.data.length - this.end,this.bufferScale * this.renderCount);
      },
      renderCount() {
        return ~~(this.viewHeight / this.itemSize);
      },
      renderList() {
        let start = this.start - this.aboveCount;
        let end = this.end + this.belowCount;
        return this.data.slice(start, end);
      }
    },
    data() {
      return {
        positions: [],  // all item position info
        startOffset: 0, // view content offset
        start: 0,
        end: -1,
      };
    }
}
</script>
<style lang="css" scoped>
.virtual_container {
    overflow: scroll;
}
</style>