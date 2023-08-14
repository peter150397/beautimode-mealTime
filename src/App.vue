<script>
import $ from "jquery";

export default {
  data() {
    return {
      mealTime: {
        week_day0: "000000000000000000000000000000000000000000000000",
        week_day1: "000000000000000000000000000000000000000000000000",
        week_day2: "000000000000000000000000000000000000000000000000",
        week_day3: "000000000000000000000000000000000000000000000000",
        week_day4: "000000000000000000000000000000000000000000000000",
        week_day5: "000000000000000000000000000000000000000000000000",
        week_day6: "000000000000000000000000000000000000000000000000"
      },
      itemDataList: [
        {
          weekDay: '星期日',
          provideMeals: false,
          id: 'sunday',
          startTime: 1,
          endTime: 0,
          index: 0
        },
        {
          weekDay: '星期一',
          provideMeals: false,
          id: 'monday',
          startTime: 1,
          endTime: 0,
          index: 1
        },
        {
          weekDay: '星期二',
          provideMeals: false,
          id: 'tuesday',
          startTime: 1,
          endTime: 0,
          index: 2
        },
        {
          weekDay: '星期三',
          provideMeals: false,
          id: 'wednesday',
          startTime: 1,
          endTime: 0,
          index: 3
        },
        {
          weekDay: '星期四',
          provideMeals: false,
          id: 'thursday',
          startTime: 1,
          endTime: 0,
          index: 4
        },
        {
          weekDay: '星期五',
          provideMeals: false,
          id: 'friday',
          startTime: 1,
          endTime: 0,
          index: 5
        },
        {
          weekDay: '星期六',
          provideMeals: false,
          id: 'Saturday',
          startTime: 1,
          endTime: 0,
          index: 6
        },
      ]
    }
  },
  methods: {
    // 輸出option名稱的小時
    showHour(value) {
      let hour = Math.floor((value - 1) / 2);

      if (value > 48) {
        return hour - 1
      } else if (hour < 10) {
        return `0${hour}`
      } else {
        return hour
      }
    },
    // 輸出option名稱的分鐘
    showMinute(value) {
      if (value > 48) {
        return '59';
      } else if (value % 2 === 1) {
        return '00';
      } else {
        return '30';
      }
    },
    // 開啟供餐
    openMealTime(item) {
      if (!item.provideMeals) {
        this.itemDataList[item.index].endTime = 48;
        $(`#${item.id}checkbox > div`).addClass('active');
        item.provideMeals = true;
      } else {
        this.itemDataList[item.index].endTime = 0;
        $(`#${item.id}checkbox > div`).removeClass('active');
        item.provideMeals = false
      }
    },
    // 當開始時間、結束時間更改時，將不符合條件的選項改成disabled
    changeFromSelectTime(isStartTime, item) {
      if (isStartTime) {
        $(`#${item.id}EndTime > *`).removeAttr("disabled");
        for (let index = item.startTime - 1; index > 0; index--) {
          $(`#${item.id}EndTime > [value = "${index}"]`).attr("disabled", true);
        }
      } else {
        $(`#${item.id}StartTime > *`).removeAttr("disabled");
        for (let index = item.endTime + 1; index <= 48; index++) {
          $(`#${item.id}StartTime > [value = "${index}"]`).attr("disabled", true);
        }
      }
    }
  },
  watch: {
    itemDataList: {
      // 監聽list，當時間改變時，就更改mealTime內對應的值
      handler(list) {
        list.forEach(item => {
          this.mealTime[`week_day${item.index}`] = "";
          for (let index = 1; index < 49; index++) {
            if (index < item.startTime || index > item.endTime) {
              this.mealTime[`week_day${item.index}`] += "0";
            } else {
              this.mealTime[`week_day${item.index}`] += "1";
            }
          }
        });
      },
      deep: true
    }
  },
}
</script>

<template>
  <div>
    <h1>供餐時間選擇器</h1>
    <div class="container">
      <div class="item" v-for="item in itemDataList" :key="item.id">
        <p>{{ item.weekDay }}</p>
        <div>
          <div :id="item.id + 'checkbox'" class="checkbox" @click="openMealTime(item)">
            <div></div>
          </div>
          <p class="dontProvideMeals" v-if="!item.provideMeals">今日不供餐</p>
          <p class="provideMeals" v-else>今日有供餐</p>
        </div>
        <div v-if="item.provideMeals">
          <select :id="item.id + 'StartTime'" v-model="item.startTime" @change="changeFromSelectTime(true, item)">
            <option value="" disabled>請選擇時間</option>
            <option :value="num" v-for="num in 48">{{ showHour(num) }} : {{ showMinute(num) }}</option>
          </select>
          <p>~</p>
          <select :id="item.id + 'EndTime'" v-model="item.endTime" @change="changeFromSelectTime(false, item)">
            <option value="" disabled>請選擇時間</option>
            <option :value="num" v-for="num in 48">{{ showHour(num + 1) }} : {{ showMinute(num + 1) }}</option>
          </select>
        </div>
      </div>
    </div>
  </div>
</template>
