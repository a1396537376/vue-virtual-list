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
        v-for="item of renderList"
        :style="{ height: `${pageModel}` === 'dyanmic' ? `${itemSize}px`: 'auto' }"
        :key="item.index"
        :id="item._index">
        <slot :data="item"></slot>
      </div>    
    </div>       
  </div>
</template>
<script>
/**
* @param scrollTop  滚动高度
* @param listHeight 列表总高度
* @param startIndex 视图渲染的起始索引
* @param endIndex 视图渲染的结束索引
* @param renderCount 可显示的列表项数
* @param startOffset 偏移量
*/
export default {
    props: {
      // data is required  
      data: { type: Array, required: true },
      // render window height
      viewHeight: { type: Number },
      bufferScale: { type: Number, default: 0.5 },
      itemSize: { type: Number, default: 24 },
      // normal | dynamic | log
      pageModel: { type: String, default: 'normal' },
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
      },
    },
    created() {
      this.initPositions();
    },
    mounted() {
      this.end = this.start + this.renderCount;
    },
    data() {
      return {
        positions: [],  // all item position info
        startOffset: 0, // view content offset
        start: 0,
        end: 0,
      };
    },
    methods: {
      initPositions() {
        this.positions = this.data.map((item,index) => {
          return {
            index,
            height: this.itemSize,
            top: index * this.itemSize,
            bottom: (index + 1) * this.itemSize,
            isUpdate: false,
          }
        });
      },
      binarySearch(list,value) {
        let start = 0;
        let end = list.length - 1;
        let tempIndex = null;
        while(start <= end) {
          let midIndex = ~~((start + end)/2);
          let midValue = list[midIndex].bottom;
          if(midValue === value){
            return midIndex + 1;
          }else if(midValue < value){
            start = midIndex + 1;
          }else if(midValue > value){
            if(tempIndex === null || tempIndex > midIndex){
              tempIndex = midIndex;
            }
              end = end - 1;
            }
          }
          return tempIndex;
        },
    },
}
</script>
<style lang="css" scoped>
.virtual_container {
    overflow: scroll;
}
.phantom{
    position: absolute;
    z-index: -1;
    top: 0;
    left: 0;
    right: 0;
}
.view_content{
    position: relative;
}
</style>