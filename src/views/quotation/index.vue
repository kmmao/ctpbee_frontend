<template>
  <div class="quotation">
    <el-card class="box-card">
      <el-select v-model="value" filterable placeholder="请选择">
        <el-option v-for="(item , index) in options" :key="index" :value="item"></el-option>
      </el-select>
      <el-button type="success" @click="subscribe(value)" style="margin-left:20px;">订阅单个</el-button>
      <!-- <el-button type="primary" @click="handleEdit('ag1910')">订阅全部</el-button> -->
      <el-button type="primary" @click="getSymbol()">更新合约</el-button>
      <!-- <el-button type="primary" @click="handleEdit('ag1910')">下單</el-button> -->
      <h4>订阅列表</h4>
      <el-table :data="symbolArr" border style="width: 100%">
        <el-table-column prop="name" label="中文名"></el-table-column>
        <el-table-column prop="local_symbol" label="品种"></el-table-column>
        <el-table-column prop="last_price" label="行情"></el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button type="primary" size="medium" @click="order(scope.row.local_symbol)">
              前往下单
              <i class="el-icon-upload el-icon--right"></i>
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
export default {
  inject: ['reload'],
  data() {
    return {
      quotationUrl: 'market',
      tableData: [],
      options: [],
      value: '',
      symbol: '',
      symbolObj: {},
      symbolArr: []
    }
  },
  sockets: {
    connect: function() {
      console.log('行情已连接')
    },
    contract: function(res) {
      // console.log(res)
      this.options = res
    },
    tick: function(res) {
      console.log(res)
      this.symbolObj[res.data.symbol] = res.data
      this.symbolArr = []
      for (let i in this.symbolObj) {
        this.symbolArr.push(this.symbolObj[i])
      }
    }
  },
  methods: {
    subscribe(symbol) {
      if (!symbol) {
        return this.tips({
          type: 'error',
          msg: '请先选择',
          isTips: true
        })
      }
      this.$axios
        .post(this.quotationUrl,{ symbol: symbol })
        .then(res => {
          let returnData = res.data
          sessionStorage.setItem('symbolName', symbol)
          this.tips({
            type: returnData.success,
            msg: returnData.msg,
            isTips:true
          })
        })
        .catch(err => {
          console.log(err)
        })
    },
    order(symbol) {
      sessionStorage.setItem('symbolName', symbol)
      this.$router.push({ path: '/order/index' })
    },
    getSymbol() {
      this.$axios
        .put(this.quotationUrl)
        .then(res => {
          let returnData = res.data
          this.tips({
            type: returnData.success,
            msg: returnData.msg,
            isTips:true,
          })
        })
        .catch(err => {
          console.log(err)
        })
    }
  },
  mounted() {
    this.getSymbol()
  }
}
</script>

<style lang='scss' scoped>
.quotation {
  padding: 20px;
  h4 {
    color: #666;
    font-weight: normal;
  }
}
</style>
