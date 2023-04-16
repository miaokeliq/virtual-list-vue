<script setup>
import { ref, shallowReactive, computed, onMounted } from "vue";

let start = ref(0);
let end = ref(30);
const height = 20;
const viewCount = 30; //显示多少条

// ref
let viewport = ref();
let scrollbar = ref();
let list = ref();

const bigList = shallowReactive(
  new Array(200000).fill(null).map((ele, index) => {
    return index + 1;
  })
);

const onScroll = () => {
  // 卷进去的高度 除 行高 = 起始索引 + 页显示数
  // 起始索引 + 页显示数 = 结束
  const scrollTop = viewport.value.scrollTop;
  start.value = Math.round(scrollTop / height);
  end.value = start.value + viewCount;
  // 把展示的list盒子偏移移动回顶部
  list.value.style.transform = `translateY(${scrollTop}px)`;
};

const showList = computed(() => {
  return bigList.slice(start.value, end.value);
});

onMounted(() => {
  viewport.value.style.height = height * viewCount + "px";
  scrollbar.value.style.height = bigList.length * height * "px";
});
</script>

<template>
  <div>
    <!-- 用户看到的区域 -->
    <div
      class="viewport"
      @scroll="onScroll"
      ref="viewport"
      :style="{ '--rowHeight': height + 'px' }"
    >
      <!-- 用高度撑出滚动条 -->
      <div class="scrollbar" ref="scrollbar"></div>
      <div class="list" ref="list">
        <div v-for="(item, index) in showList" :key="index">
          <div class="row">
            {{ item }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.viewport {
  background-color: yellowgreen;
  width: 300px;
  border: 1px solid grey;
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  overflow: scroll;
}

.list {
  /*  父属性只要不是static就行，不一定都是relative */
  position: absolute;
  left: 0;
  top: 0;
}

.row {
  height: var(--rowHeight);
  border: 1px solid red;
}
</style>
