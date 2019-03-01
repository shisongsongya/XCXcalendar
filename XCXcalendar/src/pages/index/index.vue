<template>
  <div class="content">
    <div class="yearMonth">
      <div class="left" @click="change('-')">-</div>
      <div class="right" @click="change('+')">+</div>
      <view class="section">
        <picker mode="date" :value="chanekDate"  @change="DateChange" fields="month">
          <p class="month">{{months[dateObj.month-1]}}月</p>
          <p class="year">{{dateObj.year}}</p>
        </picker>
      </view>
    </div>
    <div class="days">
      <span v-for="(v,i) in days" :key="i">{{v}}</span>
    </div>

    <div class="dates">
      <p v-for="(v,i) in dateObj.dates" :key="i" class="dateItem">
        <span
          class="date"
          :class="{blueround:v.date===todayDate&&dateObj.year===todayYear&&dateObj.month===todayMonth}"
        >{{v.now?v.date:""}}</span>
        <span
          class="lunarCalendar"
          v-if="v.now"
          :class="{red:v.oldDate.holiday||v.oldDate.oldHoliday}"
        >{{v.oldDate.holiday||v.oldDate.oldHoliday?v.oldDate.oldHoliday||v.oldDate.holiday:v.oldDate.lunarCalendar}}</span>
        <span class="purple round" v-if="v.now"></span>
        <span class="blue round" v-if="v.now"></span>
        <span class="orange round" v-if="v.now"></span>
        <span class="yellow round" v-if="v.now"></span>
        <span class="green round" v-if="v.now"></span>
      </p>
      <div class="bigMonth">{{dateObj.month}}</div>
    </div>
  </div>
</template>

<script>
import { DateConvert } from "../../utils/index";
export default {
  data() {
    return {
      days: ["日", "一", "二", "三", "四", "五", "六"],
      months: [
        "一",
        "二",
        "三",
        "四",
        "五",
        "六",
        "七",
        "八",
        "九",
        "十",
        "十一",
        "十二"
      ],
      nowTime: new Date(),
      dateObj: {
        dates: [],
        month: "",
        year: "",
        date: ""
      },
      todayYear: new Date().getFullYear(),
      todayMonth: new Date().getMonth() + 1,
      todayDate: new Date().getDate(),
      chanekDate: ""//选中的时间
    };
  },
  methods: {
    dateInit(newTime) {
      this.dateObj = {
        dates: [],
        month: "",
        year: "",
        date: ""
      };
      newTime = new Date(newTime);
      console.log(newTime)
      //计算这个月有多少天
      let nowDate = new Date(
        newTime.getFullYear(),
        newTime.getMonth() + 1,
        0
      ).getDate();
      //计算这个月第一天星期几
      let nowDay = new Date(newTime.getFullYear(), newTime.getMonth()).getDay();
      let nowYear = new Date(
        newTime.getFullYear(),
        newTime.getMonth()
      ).getFullYear();
      let nowMonth =
        new Date(newTime.getFullYear(), newTime.getMonth()).getMonth() + 1;
      //计算上个月最后一天是几号
      let lastDate =
        new Date(newTime.getFullYear(), newTime.getMonth(), 0).getDate() + 1;
      let lastYera = new Date(
        newTime.getFullYear(),
        newTime.getMonth(),
        0
      ).getFullYear();
      let lastMonth =
        new Date(newTime.getFullYear(), newTime.getMonth(), 0).getMonth() + 1;

      //计算上个月多出的天数
      for (let i = 1; i <= nowDay; i++) {
        lastDate -= 1;
        this.dateObj.dates.unshift({
          date: lastDate,
          now: false,
          oldDate: DateConvert.convertSolarToLunar(
            lastYera,
            lastMonth,
            lastDate
          )
        });
      }
      //算出这个月
      for (let i = 1; i <= nowDate; i++) {
        this.dateObj.dates.push({
          date: i,
          now: true,
          oldDate: DateConvert.convertSolarToLunar(nowYear, nowMonth, i)
        });
      }
      //算出下个月多出的时间
      for (let i = 1; i <= 35 - nowDate - nowDay; i++) {
        this.dateObj.dates.push({
          date: i,
          now: false
        });
      }
      //算出当前是几月
      this.dateObj.month = newTime.getMonth() + 1;
      this.dateObj.year = newTime.getFullYear();
      this.dateObj.date = newTime.getDate();
     // console.log(this.dateObj.dates);
    },
    change(str) {
      this.nowTime = new Date(this.nowTime);
      if (str === "+")
        this.nowTime = this.nowTime.setMonth(this.nowTime.getMonth() + 1);
      if (str === "-")
        this.nowTime = this.nowTime.setMonth(this.nowTime.getMonth() - 1);
      this.nowTime = new Date(this.nowTime);
      this.dateInit(this.nowTime);
    },
    DateChange(e){
      console.log(e.mp.detail.value)
      this.nowTime=new Date(e.mp.detail.value)
      this.dateInit(this.nowTime)
    }
  },

  created() {
    wx.setNavigationBarTitle({
      title: "简单日历"
    });
    this.dateInit(this.nowTime);
  }
};
</script>

<style lang="less" scoped>
.content {
  .days {
    display: flex;
    background: rgb(90, 72, 242);
    height: 74rpx;
    align-items: center;
    margin-bottom:6rpx; 
    span {
      width: calc(100% / 7);
      text-align: center;
      color: white;
    }
  }
  .yearMonth {
    padding: 0 10rpx;
    text-align: center;
    border-bottom: 1rpx solid #ccc;
    position: relative;
    .month {
      font-size: 30rpx;
      color: rgb(94, 122, 136);
    }
    .year {
      font-size: 24rpx;
      color: rgb(153, 153, 153);
    }
    .left {
      width: 74rpx;
      height: 74rpx;
      position: absolute;
      text-align: center;
      line-height: 74rpx;
      left: 8rpx;
      top: 0rpx;
      font-size: 60rpx;
    }
    .right {
      width: 74rpx;
      height: 74rpx;
      position: absolute;
      text-align: center;
      line-height: 74rpx;
      right: 8rpx;
      top: 0rpx;
      font-size: 60rpx;
    }
  }
  .dates {
    display: flex;
    flex-wrap: wrap;
    position: relative;
    .dateItem {
      width: calc(100% / 7);
      text-align: center;
      color: rgb(209, 209, 209);
      height: 190rpx;
      display: flex;
      flex-direction: column;
      align-items: center;
      z-index: 1;
      .round {
        width: 14rpx;
        height: 14rpx;
        border-radius: 50%;
        margin-top: 4rpx;
      }
      //紫色
      .purple {
        background: rgb(90, 81, 230);
      }
      //蓝色
      .blue {
        background: rgb(83, 205, 240);
      }
      //橘色
      .orange {
        background: rgb(238, 159, 77);
      }
      //黄色
      .yellow {
        background: rgb(246, 238, 76);
      }
      //绿色
      .green{
        background:  rgb(23, 196, 95);;
      }
      //节假日颜色
      .red {
        color: red !important;
      }
      //今天的日期蓝圈
      .date {
        width: 54rpx;
        height: 54rpx;
        line-height: 54rpx;
        text-align: center;
      }
      .lunarCalendar {
        font-size: 20rpx;
        color: black;
      }
      .blueround {
        border: 2rpx solid rgb(90, 81, 230);
        border-radius: 50%;
      }
    }
    .bigMonth {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 500rpx;
      height: 500rpx;
      font-size: 500rpx;
      line-height: 500rpx;
      text-align: center;
      transform: translate(-50%, -50%);
      color: rgb(243, 243, 243);
    }
  }
}
</style>

