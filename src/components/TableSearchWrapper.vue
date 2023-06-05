<script setup>
import {
  IconSearch,
  IconRefresh,
  IconUp,
  IconDown
} from '@arco-design/web-vue/es/icon';
import {
  computed,
  useSlots,
  toRaw,
  onMounted,
  reactive,
  onUnmounted,
  ref
} from 'vue';

const collapsed = ref(false);

const cols = { xs: 1, sm: 2, md: 3, lg: 4, xl: 5, xxl: 6 };

const slots = useSlots();
const allSlotsName = computed(() => Object.keys(toRaw(slots)));

const props = defineProps({
  form: { type: Object, required: true },
  onSearchQuery: { type: Function, default: () => {} },
  onResetQuery: { type: Function, default: () => {} }
});

const colArr = ['xxl', 'xl', 'lg', 'md', 'sm', 'xs'];
const responsiveMap = {
  xs: '(max-width: 575px)',
  sm: '(min-width: 576px)',
  md: '(min-width: 768px)',
  lg: '(min-width: 992px)',
  xl: '(min-width: 1200px)',
  xxl: '(min-width: 1600px)'
};
// 用来保存当前页面尺寸处于哪个尺寸区间
const screens = reactive({
  xs: true,
  sm: true,
  md: true,
  lg: true,
  xl: true,
  xxl: true
});
// 计算当前页面尺寸下一行中可以有几个元素
const resultCol = computed(() => {
  let res = 1;
  for (let i = 0; i < colArr.length; i++) {
    const breakpoint = colArr[i];
    if (screens[breakpoint] || breakpoint === 'xs') {
      res = cols[breakpoint];
      break;
    }
  }
  return res;
});
const multipleRows = computed(() => {
  if (collapsed.value || allSlotsName.value.length <= resultCol.value) {
    return 1;
  }
  return Math.ceil(allSlotsName.value.length / resultCol.value);
  return !collapsed.value && allSlotsName.value.length > resultCol.value;
});

const matchHandlers = {};
onMounted(() => {
  Object.keys(responsiveMap).forEach(screen => {
    const matchMediaQuery = responsiveMap[screen];
    if (!matchMediaQuery) return;
    const listener = ({ matches }) => {
      screens[screen] = matches;
    };
    const mql = window.matchMedia(matchMediaQuery);
    if (mql.addEventListener) {
      mql.addEventListener('change', listener);
    } else {
      mql.addListener(listener);
    }
    matchHandlers[matchMediaQuery] = {
      mql,
      listener
    };
    listener(mql);
  });
});
onUnmounted(() => {
  // 移除监听器
  Object.keys(responsiveMap).forEach(screen => {
    const matchMediaQuery = responsiveMap[screen];
    if (!matchMediaQuery) return;
    const handler = matchHandlers[matchMediaQuery];
    if (handler && handler.mql && handler.listener) {
      if (handler.mql.removeEventListener) {
        handler.mql.removeEventListener('change', handler.listener);
      } else {
        handler.mql.removeListener(handler.listener);
      }
    }
  });
});
</script>

<template>
  <div style="overflow: hidden; position: relative">
    <a-row>
      <a-col :flex="1">
        <!-- 此处就是用来显示输入框、选择器这类组件的地方 -->
        <a-form :model="props.form" :auto-label-width="true">
          <a-grid
            :col-gap="16"
            :row-gap="0"
            :cols="cols"
            :collapsed="collapsed"
          >
            <a-grid-item v-for="item in allSlotsName" :key="item">
              <slot :name="item"></slot>
            </a-grid-item>
          </a-grid>
        </a-form>
      </a-col>
      <a-col :flex="multipleRows > 1 ? '108px' : '198px'" style="display: flex">
        <a-divider
          direction="vertical"
          :style="{ height: multipleRows > 1 ? '84px' : '32px' }"
        />
        <!-- 放置按钮区域 -->
        <div
          style="
            flex: 1;
            display: flex;
            justify-content: space-between;
            flex-wrap: wrap;
          "
        >
          <a-button
            :style="{
              marginBottom: multipleRows > 1 ? '20px' : '0px'
            }"
            @click="onSearchQuery"
            type="primary"
          >
            <template #icon>
              <icon-search />
            </template>
            查询
          </a-button>
          <a-button @click="onResetQuery">
            <template #icon>
              <icon-refresh />
            </template>
            重置
          </a-button>
        </div>
      </a-col>
    </a-row>
    <a
      v-if="resultCol < allSlotsName.length"
      :class="['collapsed-action', multipleRows <= 2 ? 'normal' : 'position']"
      @click="collapsed = !collapsed"
    >
      {{ collapsed ? '展开' : '收起' }}
      <IconDown v-if="collapsed" />
      <IconUp v-else />
    </a>
  </div>
</template>

<style scoped>
.collapsed-action {
  cursor: pointer;
}
.normal {
  float: right;
  margin-top: -8px;
}
.position {
  position: absolute;
  right: 0px;
  top: 92px;
}
</style>
