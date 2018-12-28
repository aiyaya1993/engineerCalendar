<!--suppress ALL -->
<template>
    <div class="root">
      <div style="background-color: aliceblue">{{msg}}</div>
      <div class="content">
        <div class="contentLfet">
          <div class="letfTitle">
            {{newlunar.tg}}{{newlunar.dz}}{{newlunar.year}}年
            {{newlunar.month}}月{{newlunar.day}}
          </div>
          <div class="rightTitle">
            {{dateNum.month}}月{{dateNum.day}}日
            {{weekday[dateNum.dayNo]}}
          </div>
        </div>
        <div class="contentRight">
          <div class="contentTop">
            <div class="myday">{{dateNum.day}}</div>
            <div class="mymonth">{{months[dateNum.month-1]}}</div>
          </div>
          <div v-show="isShowConetent">{{todayContent}}</div>
        </div>
      </div>
      <div class="bottom">
        <div class="bottomLfet">
          <div class="suit">宜</div>
          <div v-if="isShow" class="item">{{todos[0]}}</div>
          <div class="item">{{todos[1]}}</div>
          <div class="item">{{todos[2]}}</div>
        </div>
        <div class="bottomRight">
          <div class="suit">不宜</div>
          <div v-if="isShow" class="item">{{notTodos[0]}}</div>
          <div class="item">{{notTodos[1]}}</div>
          <div class="item">{{notTodos[2]}}</div>
        </div>
      </div>
      <div class="state">copyright@不谷 染竹</div>
    </div>
</template>

<script>
export default {
  name: 'calendar',
  data: function () {
    return {
      msg: '程序员日历',
      randomNum: [],
      randomThings: {},
      randomThings1: {},
      todos: [],
      notTodos: [],
      todayContent: '',
      isShow: false,
      isShowConetent: false,
      dateNum: {
        year: '',
        month: '',
        day: '',
        dayNo: 0
      },
      // 农历
      lunar: {
        tg: '甲乙丙丁戊己庚辛壬癸',
        dz: '子丑寅卯辰巳午未申酉戌亥',
        number: '一二三四五六七八九十',
        year: '鼠牛虎兔龙蛇马羊猴鸡狗猪',
        month: '正二三四五六七八九十冬腊',
        monthadd: [0, 31, 59, 90, 120, 151, 181, 212, 243, 273, 304, 334],
        calendar: [0xA4B, 0x5164B, 0x6A5, 0x6D4, 0x415B5, 0x2B6, 0x957, 0x2092F, 0x497, 0x60C96, 0xD4A, 0xEA5, 0x50DA9, 0x5AD, 0x2B6, 0x3126E, 0x92E, 0x7192D, 0xC95, 0xD4A, 0x61B4A, 0xB55, 0x56A, 0x4155B, 0x25D, 0x92D, 0x2192B, 0xA95, 0x71695, 0x6CA, 0xB55, 0x50AB5, 0x4DA, 0xA5B, 0x30A57, 0x52B, 0x8152A, 0xE95, 0x6AA, 0x615AA, 0xAB5, 0x4B6, 0x414AE, 0xA57, 0x526, 0x31D26, 0xD95, 0x70B55, 0x56A, 0x96D, 0x5095D, 0x4AD, 0xA4D, 0x41A4D, 0xD25, 0x81AA5, 0xB54, 0xB6A, 0x612DA, 0x95B, 0x49B, 0x41497, 0xA4B, 0xA164B, 0x6A5, 0x6D4, 0x615B4, 0xAB6, 0x957, 0x5092F, 0x497, 0x64B, 0x30D4A, 0xEA5, 0x80D65, 0x5AC, 0xAB6, 0x5126D, 0x92E, 0xC96, 0x41A95, 0xD4A, 0xDA5, 0x20B55, 0x56A, 0x7155B, 0x25D, 0x92D, 0x5192B, 0xA95, 0xB4A, 0x416AA, 0xAD5, 0x90AB5, 0x4BA, 0xA5B, 0x60A57, 0x52B, 0xA93, 0x40E95]
      },
      weekday: ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'],
      months: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'June', 'July', 'Aug', 'Sept', 'Oct', 'Nov', 'Dec'],
      newlunar: {}
    }
  },
  watch: {
    dateNum: 'getJsonData'
  },
  methods: {
    getRandom: function (min, max) {
      let num = Math.round(Math.random() * (max - min) + min)
      return num
    },
    getDate: function () {
      let myDate = new Date()
      this.dateNum.year = myDate.getFullYear()
      this.dateNum.month = myDate.getMonth() + 1
      this.dateNum.day = myDate.getDate()
      this.dateNum.dayNo = myDate.getDay()
    },
    getJsonData: function () {
      this.getDate()
      let myNum = this.getRandom(0, 6)
      for (var i = 0 ; ; i++){
        if (this.randomNum.length >= 6) {
          break
        } else {
          this.generateRandom()
        }
      }
      let promise = this.$http.get('../../static/mysuitData.json').then((response) => {
        console.log(this.randomNum)
        this.randomThings = response.data
        for(let i = 0; i < 3; i++) {
          this.todos.push(this.randomThings[this.randomNum[i]].todo)
          // this.notTodos.push(this.randomThings[this.randomNum[i]].notodo)
        }
        this.isShow = true
      })
      let promise1 = this.$http.get('../../static/notsuitData.json').then((response) => {
        console.log(this.todos)
        this.randomThings1 = response.data
        for(let i = 0; i < 3; i++) {
            this.notTodos.push(this.randomThings1[this.randomNum[i]].notodo)
        }
        this.isShow = true
      })
    },
    getTodayContent: function () {
      let sort = Math.ceil((new Date() - new Date(new Date().getFullYear().toString())) / (24 * 60 * 60 * 1000)) + 1
      console.log(sort)
      this.$http.get('../../static/contentData.json').then((response) => {
        this.isShowConetent = true
        this.todayContent = response.data[sort].content
      })
      return sort
    },
    // 生成随机数
     generateRandom: function(){
      let rand = this.getRandom(0, 6)
      for (let i = 0 ; i < this.randomNum.length; i++){
        if(this.randomNum[i] === rand){
        return false
        }
      }
       this.randomNum.push(rand)
    },
    // 农历转化
    getLunarDate: function (date) {

      var year, month, day
      if (!date) {
        date = new Date(), year = date.getFullYear(), month = date.getMonth(), day = date.getDate()
      } else {
        date = date.split('-'), year = parseInt(date[0]), month = date[1] - 1, day = parseInt(date[2])
      }

      if (year < 1921 || year > 2020) {
        return {}
      }

      var total, m, n, k, bit, lunarYear, lunarMonth, lunarDay;
      var isEnd = false
      var tmp = year
      if (tmp < 1900) {
        tmp += 1900
      }
      total = (tmp - 1921) * 365 + Math.floor((tmp - 1921) / 4) + this.lunar.monthadd[month] + day - 38;
      if (year % 4 == 0 && month > 1) {
        total++
      }
      for (m = 0;; m++) {
        k = (this.lunar.calendar[m] < 0xfff) ? 11 : 12;
        for (n = k; n >= 0; n--) {
        bit = (this.lunar.calendar[m] >> n) & 1;
           if (total <= 29 + bit) {
              isEnd = true
              break
           }
           total = total - 29 - bit;
        }
        if (isEnd) break
      }
      lunarYear = 1921 + m
      lunarMonth = k - n + 1
      lunarDay = total
      if (k == 12) {
        if (lunarMonth == Math.floor(this.lunar.calendar[m] / 0x10000) + 1) {
          lunarMonth = 1 - lunarMonth;
      }
      if (lunarMonth > Math.floor(this.lunar.calendar[m] / 0x10000) + 1) {
        lunarMonth--
      }
    }

  return {
    lunarYear: lunarYear,
    lunarMonth: lunarMonth,
    lunarDay: lunarDay,
  };
},
    getLunarDateString:function (lunarDate) {
      if (!lunarDate.lunarDay) return;
      var data = {},
      lunarYear = lunarDate.lunarYear,
      lunarMonth = lunarDate.lunarMonth,
      lunarDay = lunarDate.lunarDay;

    data.tg = this.lunar.tg.charAt((lunarYear - 4) % 10);
    data.dz = this.lunar.dz.charAt((lunarYear - 4) % 12);
    data.year = this.lunar.year.charAt((lunarYear - 4) % 12);
    data.month = lunarMonth < 1 ? '(闰)' + this.lunar.month.charAt(-lunarMonth - 1) : this.lunar.month.charAt(lunarMonth - 1)
      data.day = (lunarDay < 11) ? "初" : ((lunarDay < 20) ? "十" : ((lunarDay < 30) ? "廿" : "三十"));
    if (lunarDay % 10 != 0 || lunarDay == 10) {
      data.day += this.lunar.number.charAt((lunarDay - 1) % 10);
    }

    return data;
  },
    getlunarDay: function() {
      this.getDate()
      let a =  this.dateNum.year + '-' +  this.dateNum.month + '-' +  this.dateNum.day
      console.log(a)
      var lunarDate = this.getLunarDate(a)
      this.newlunar = this.getLunarDateString(lunarDate)
      console.log(lunarDate)
      console.log(this.newlunar)
    }
 },
  mounted: function () {
    this.getDate()
    this.getTodayContent()
    this.getlunarDay()
    this.getJsonData()
    // setInterval(this.getJsonData(), 1000)
  }
}
</script>

<style scoped>
  .content {
    width: 100%;
    height: 300px;
    display: flex;
  }
  .contentLfet {
    width: 40%;
    height: 300px;
    background-color: #99CCFF;
    display: flex;
  }
  .contentRight {
    width: 60%;
    height: 300px;
    /*background-color:#99CCFF;*/
  }
  .contentTop {
    width: 100%;
    height: 200px;
    background-color: white;
    display: flex;
  }
  .bottom {
    width: 100%;
    /*background-color: aqua;*/
    height: 180px;
    display: flex;
  }
  .bottomLfet {
    width: 50%;
    height: 180px;
  }
  .bottomRight {
    width: 50%;
    height: 180px;
    background-color: #6699CC;
  }
  .myday {
    font-size: 80px;
    margin-top: 30px;
    margin-left: 30%
  }
  .mymonth {
    margin-top: 90px;
  }
  .root {
    width: 400px;
    height: 500px;
    margin:0 auto;
  }
  .state {
    height: 20px;
    width: 100%;
    background-color: aliceblue;
    font-size: 10px;
    font-weight: bold;
  }
  .letfTitle {
    width: 20px;
    height: 250px;
    margin-top: 20px;
    margin-left: 20px;
    font-weight: bold;
    font-size: 20px;
  }
  .rightTitle {
    width: 20px;
    height: 250px;
    margin-top: 120px;
    margin-left: 80px;
    font-size: 15px;
  }
  .suit {
    font-size: 25px;
    font-weight: bold;
  }
  .item {
    margin-top: 10px;
  }

</style>
