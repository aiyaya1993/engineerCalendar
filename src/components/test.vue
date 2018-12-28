<!--二手车评估弹窗-->
<template>
  <div>
    <div>
      <el-form :model="model" :default-model="defaultModel">
        <el-form-item label="车型" class="default">
          <el-input
            class="info-input"
            v-model="model.carLicenseModel"
            placeholder="请输入车型"
          ></el-input>
        </el-form-item>
        <!--<el-form-item label="证件编号" class="default">-->
        <!--<el-input-->
        <!--class="info-input"-->
        <!--:disabled="true"-->
        <!--v-model="model.idcardNo"-->
        <!--placeholder="请输入证件编号"-->
        <!--&gt;</el-input>-->
        <!--</el-form-item>-->
        <!--<el-form-item label="证件类型" class="default">-->
        <!--<el-input-->
        <!--class="info-input"-->
        <!--:disabled="true"-->
        <!--v-model="model.idcardType"-->
        <!--placeholder="请输入证件类型"-->
        <!--&gt;</el-input>-->
        <!--</el-form-item>-->
        <el-form-item label="首次上牌时间" class="default">
          <el-date-picker
            type="date"
            v-model="model.firstLicensePlateDate"
            placeholder="选择日期">
          </el-date-picker>
        </el-form-item>
        <el-form-item label="线下评估价格" class="default">
        <el-input
        class="info-input"
        :disabled="true"
        v-model="model.price"
        placeholder="请输入价格"
        ></el-input>
        </el-form-item>
        <el-form-item label="行驶里程" class="default">
        <el-input
        class="info-input"
        :disabled="true"
        v-model="model.miles"
        placeholder="请输入行驶里程"
        ></el-input>
        </el-form-item>
        <div class="box addrBox">
        <label class="form-item-label">所在城市</label>
        <el-select
        class="info-select flex"
        placeholder='请选择省'
        v-model="model.province"
        >
        <el-option
        v-for="item in provinceList"
        :disabled="item.disabled"
        :key="item.regionCode"
        :label="item.regionName"
        :value="item.regionCode"
        @click.native="handleProviceChange"
        >
        </el-option>
        </el-select>
        <el-select
        class="info-select flex"
        placeholder='请选择市'
        v-model="model.city"
        >
        <el-option
        v-for="item in cityList"
        :key="item.regionCode"
        :label="item.regionName"
        :value="item.regionCode">
        </el-option>
        </el-select>
        </div>
      </el-form>

    </div>
  </div>
</template>

<script>
  export default {
    name: 'usedCarApply',
    data () {
      return {
        defaultModel: null,
        // ！！！目前自己定的变量名 有接口之后需修改
        model: {
          orderno: '', // 订单号
          carLicenseModel: '', // 车型
          firstLicensePlateDate: '', // 首次上牌时间
          // idcardNo: '', // 证件号码
          // idcardType: '', // 证件类型
          // date: '', // 首次上牌时间
          // price: 0, // 线下评估价格
          // miles: 0, // 行驶里程数
          // province: '', // 所在省
          // city: '' //  所在城市
        },
        // provinceList: [],
        // cityList: []
      }
    },
    watch: {
      // 监听订单号的变化
      orderNo(val) {
        if (val) {
          this.model.orderNo = val
          console.log('ICBCCard orderNo', val)
        }
      },
      'model.province'(val) {
        this.model.province = this.getCityNameById(val, this.provinceList) || this.model.province
        if (this.model.province) {
          if (!['黑龙江省', '内蒙古'].includes(this.model.province)) {
            this.model.province = this.model.province.substr(0, 2)
          } else {
            if (this.model.province === '黑龙江省') {
              this.model.province = '黑龙江'
            }
          }

        }
        this.getCityList(val, 'cityList')
      },
    },
    props: {
      orderNo: {
        type: String,
        default: ''
      }
    },
    methods: {
      获取城市信息列表
      getCityList(cityNo, type) {
        //
        this.$http.get(`/city/detail/${cityNo}`)
          .then((res) => {
            if (res.data.success) {
              const cityList = res.data.data
              if ((cityNo + '') === '0') {
                this.$set(this, 'provinceList', cityList)
              } else {
                this.$set(this, type, cityList)
              }
            }
          })
          .catch((res) => {

          })
      },
      getCityNameById(cityNo, list) {
        let result = ''
        list && list.every((it) => {
          if (it.regionCode === parseInt(cityNo)) {
            result = it.regionName
          }
          return !result
        })
        return result
      },
      getCityList(cityNo, type) {
        //
        this.$http.get(`/city/detail/${cityNo}`)
          .then((res) => {
            if (res.data.success) {
              const cityList = res.data.data
              if ((cityNo + '') === '0') {
                this.$set(this, 'provinceList', cityList)
              } else {
                this.$set(this, type, cityList)
              }
            }
          })
          .catch((res) => {

          })
      },

    },
    mounted() {
      this.getCityList(0)
    }
  }
</script>
<style>
  .addrBox .el-select{
    margin-right: 12px;
  }
  .addrBox .el-select:last-child {
    margin-right: 0px;
  }
</style>
<style scoped>
  .root{
    height: 100%;
    width: 100%;
    box-sizing: border-box;
    /*padding-top: 20px;*/
    padding-left: 205px;
    /*padding-right: 170px; */
    overflow: auto;
    display: flex;
    flex-direction: column;
    align-content: start;
    /*justify-content: center;*/

  }
  .addrBox {
    margin-bottom: 12px;
    align-items: center;
  }
  .form-item-label {
    color: #909399;
    margin-right: 12px;
    margin-left: -15px;
    width: 7em;
    text-align: right;
  }
</style>
