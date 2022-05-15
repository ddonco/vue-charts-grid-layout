<template>
  <div>
    <div style="text-align: center">
      <div class="site-title">Data Grid</div>
      <span v-if="!preview">
        <button
          class="text-sm px-5 py-1 mr-2 mb-2 text-gray-900 bg-white border border-gray-300 focus:outline-none hover:bg-gray-100 font-medium rounded-lg dark:bg-gray-800 dark:text-white dark:border-gray-600 dark:hover:bg-gray-700 dark:hover:border-gray-600"
          @click="addChartGridItem"
        >
          Chart
        </button>
      </span>
      <button
        class="text-sm px-5 py-1 mr-2 mb-2 text-gray-900 bg-white border border-gray-300 focus:outline-none hover:bg-gray-100 font-medium rounded-lg dark:bg-gray-800 dark:text-white dark:border-gray-600 dark:hover:bg-gray-700 dark:hover:border-gray-600"
        @click="disableGrid"
      >
        <span v-if="preview"> Edit </span>
        <span v-else="preview"> Publish </span>
      </button>
    </div>
    <hr />
    <div>
      <grid-layout
        :layout="getResources"
        :col-num="10"
        :row-height="20"
        :is-draggable="isDraggable"
        :is-resizable="isResizable"
        :vertical-compact="true"
        :margin="[5, 5]"
        :use-css-transforms="true"
      >
        <grid-item
          v-for="(item, index) in getResources"
          :key="index"
          :class="{ editMode: !preview }"
          :autoSize="true"
          :x="item.x"
          :y="item.y"
          :w="item.w"
          :h="item.h"
          :i="item.i"
          :ref="item.i"
          :style="gridItemBackground()"
          dragIgnoreFrom=".echarts,.x-number,.card-title,.right-buttons"
          @resize="resizeEvent"
          @moved="movedEvent"
          class="chart-item"
        >
          <div
            v-if="!preview"
            @click="removeItem({ key: index })"
            style="position: absolute; bottom: 0px; left: 4px"
          >
            <i class="fa fa-trash" aria-hidden="true"></i>
          </div>
          <chart-widget
            v-if="item.type == 'chart'"
            :preview="preview"
            :contenteditable="contenteditable"
            :item="item"
            :itemIndex="index"
          >
            Chart Here
          </chart-widget>
        </grid-item>
      </grid-layout>
    </div>
    <ChartConfigModal :show="showChartConfig" />
  </div>
</template>
<script>
import { mapGetters, mapActions } from 'vuex';
import { GridLayout, GridItem } from 'vue-grid-layout';
import ChartWidget from '/@/components/ChartWidget.vue';
import ChartConfigModal from '../components/ChartConfigModal.vue';
import { useStore } from '/@/store/index';

export default {
  name: 'gridview',
  components: { GridLayout, ChartWidget, ChartConfigModal },
  computed: {
    ...mapGetters(['getResources']),
    showChartConfig: function () {
      const store = useStore();
      let showChart = store.state.showChartConfig;
      return showChart;
    },
  },
  data() {
    return {
      isDraggable: false,
      isResizable: false,
      preview: true,
      contenteditable: false,
      widthpx: 400,
      heighpx: 400,
    };
  },
  methods: {
    ...mapActions(['resourceSizeChange']),
    disableGrid() {
      this.isDraggable = !this.isDraggable;
      this.isResizable = !this.isResizable;
      this.preview = !this.preview;
      this.contenteditable = !this.contenteditable;
    },
    resizeEvent: function (i, newH, newW, newHPx, newWPx) {
      const msg =
        'RESIZE i=' +
        i +
        ', H=' +
        newH +
        ', W=' +
        newW +
        ', H(px)=' +
        newHPx +
        ', W(px)=' +
        newWPx;
      console.log(msg);
      this.resourceSizeChange({
        id: i,
        h: newH,
        w: newW,
        hpx: newHPx,
        wpx: newWPx,
      });
    },
    movedEvent(i, newX, newY) {
      console.log('movedEvent');
      const msg = 'MOVE i=' + i + ', X=' + newX + ', Y=' + newY;
      console.log(msg);
    },
    gridItemBackground() {
      return {
        background: '#fff',
      };
    },
  },
};
</script>
<style scoped>
.dashboard {
  width: 100%;
  min-height: 800px;
}
.grid {
  position: relative;
}
.grip-modal {
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  position: fixed;
  z-index: 50;
  background-color: rgba(0, 0, 0, 0.5);
}
.chart-item {
  z-index: 5;
  display: block;
  border-radius: 6px;
  background-color: #fff;
  color: black;
  box-shadow: 0 0 40px rgba(226, 226, 226, 0.5);
  padding: 15px;
  box-sizing: border-box;
}
.vue-grid-item:not(.vue-grid-placeholder) {
  background: #eee;
  border: 1px solid black;
}
.vue-grid-item .no-drag {
  z-index: 5;
  height: 100%;
  width: 100%;
}
.editMode {
  background-color: #fafafa;
  border-radius: 5px;
}
.site-title {
  font-family: 'Lilita One', cursive;
  font-size: 50px;
  color: #2e885e;
  text-align: center;
}
.content {
  font-family: 'Patrick Hand', cursive;
  font-size: 20px;
  color: #2196f3;
}
</style>
