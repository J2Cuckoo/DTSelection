<script setup lang="ts">
import {reactive} from 'vue'

// defineProps<{ msg: string }>()

interface ISelection {
  name: string
  inx: number
}

interface ITimeSelection {
  unit: "hour" | "minute"
  times: ISelection[]
  weeks: ISelection[]
  activeDate: string[]
  click?: boolean
}

const state = reactive<ITimeSelection>({
  unit: "hour",
  times: [
    {name: "00:00", inx: 0},
    {name: "01:00", inx: 1},
    {name: "02:00", inx: 2},
    {name: "03:00", inx: 3},
    {name: "04:00", inx: 4},
    {name: "05:00", inx: 5},
    {name: "06:00", inx: 6},
    {name: "07:00", inx: 7},
    {name: "08:00", inx: 8},
    {name: "09:00", inx: 9},
    {name: "10:00", inx: 10},
    {name: "11:00", inx: 11},
    {name: "12:00", inx: 12},
    {name: "13:00", inx: 13},
    {name: "14:00", inx: 14},
    {name: "15:00", inx: 15},
    {name: "16:00", inx: 16},
    {name: "17:00", inx: 17},
    {name: "18:00", inx: 18},
    {name: "19:00", inx: 19},
    {name: "20:00", inx: 20},
    {name: "21:00", inx: 21},
    {name: "22:00", inx: 22},
    {name: "23:00", inx: 23}
  ],
  weeks: [{name: "星期一", inx: 1}, {name: "星期二", inx: 2},
    {name: "星期三", inx: 3}, {name: "星期四", inx: 4},
    {name: "星期五", inx: 5}, {name: "星期六", inx: 6},
    {name: "星期日", inx: 7}],
  activeDate: [],
  click: false
})
const doGetItemCls = (inx: string): string => {
  if (state.activeDate.includes(inx)) {
    return "item active"
  }
  return "item"
}
const doPushActiveDate = (inx: string, click: boolean) => {
  if (click) {
    if (state.activeDate.includes(inx)) {
      state.activeDate = [...state.activeDate.filter((a: string) => a !== inx)]
    } else {
      state.activeDate = [...state.activeDate, inx]
    }
    console.log("选中结果：", state.activeDate)
  }
}
const handleMouseChange = (type: number, inx?: string) => {
  const c: boolean = type === 1;
  state.click = c;
  if (c && inx) doPushActiveDate(inx, c)
}

/**
 * <p>选择头部</p>
 * @param inx
 * @param type 0 代表点击的是每周的哪一天（左边） 、1 代表选中的是什么时间（顶上）
 */
const doClickTitle = (inx: string, type: number) => {
  switch (type) {
    case 0:
      state.activeDate = [...state.activeDate, ...state.times.map((si: ISelection) => `${inx}-${si.inx}`)]
      break
    case 1:
      state.activeDate = [...state.activeDate, ...state.weeks.map((si: ISelection) => `${si.inx}-${inx}`)]
      break
  }
}
</script>

<template>
  <div class="grid-container">
    <div class="menus">
      <div class="item title"></div>
      <div class="menu item" @click="doClickTitle(`${item.inx}`,0)" v-for="item in state.weeks" :key="item.inx">
        {{ item.name }}
      </div>
    </div>
    <div class="content">
      <div class="headers">
        <div class="item title" @click="doClickTitle(`${item.inx}`,1)" v-for="item in state.times" :key="item.inx">
          {{ item.name }}
        </div>
      </div>
      <div class="body">
        <div class="line" v-for="w_item in state.weeks" :key="w_item.inx">
          <div
              :style="state.click?'cursor:crosshair !important':''"
              :class="doGetItemCls(`${w_item.inx}-${item.inx}`)"
              v-for="item in state.times"
              :key="`${w_item.inx}-${item.inx}`"
              @mousedown="handleMouseChange(1,`${w_item.inx}-${item.inx}`)"
              @mouseenter="doPushActiveDate(`${w_item.inx}-${item.inx}`,state.click||false)"
              @mouseup="handleMouseChange(0)"
          >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="less">
.grid-container {
  min-width: 900px;
  background-color: white;
  display: flex;
  @layout_width: 140px;
  @layout_height: 80px;


  .menus {
    width: @layout_width;
    display: flex;
    flex-direction: column;

    .title {
      background-color: white !important;
      border-right: 1px solid #eee;
      cursor: default !important;
    }

    .item {
      height: @layout_height;
      display: flex;
      justify-content: center;
      align-items: center;
      cursor: pointer;
      -webkit-user-select: none;
      -moz-user-select: none;
      -ms-user-select: none;
      user-select: none;
    }

    .menu {
      background-color: aqua;
    }

    .menu:nth-child(2n-1) {
      background-color: #646cff;
    }
  }


  .content {
    width: calc(100% - @layout_width);
    height: 100%;
    background-color: #dedede;
    overflow: auto;

    .body {
      width: 100%;
      height: calc(7 * @layout_height);
    }


    .headers, .line {
      display: flex;
      white-space: nowrap;

      .title {
        background-color: white !important;
      }

      .active {
        background-color: blue !important;
      }

      .item {
        flex: 0 0 auto;
        width: @layout_width;
        height: @layout_height;
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: #dedede;
        border-right: 1px solid #eee;
        position: relative;
        cursor: pointer;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;

        &::before {
          width: 100%;
          position: absolute;
          content: "";
          height: 1px;
          background-color: #eee;
          bottom: 0;
        }
      }
    }

  }
}
</style>
