<template>
  <div class="account">
    <el-card class="box-card">
      <p>基本账户信息</p>
      <el-table :data="tableData" border style="width: 100%">
        <el-table-column prop="key" label="key">
          <template slot-scope="scope">{{ scope.row.key | chinese() }}</template>
        </el-table-column>
        <el-table-column prop="value" label="Value"></el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: []
    }
  },
  sockets: {
    connect: function() {
      console.log('账户已连接')
    },
    account: function(res) {
      let returnData = res.data
      if (returnData.success === false) {
        this.$message({
          showClose: true,
          message: returnData.msg,
          type: 'error'
        })
      } else {
        this.tableData = returnData
      }
    }
  },
  filters: {
    chinese(value) {
      const obj = {
        accountid: '账户名',
        available: '账户可用',
        balance: '余额',
        frozen: '冻结',
        gateway_name: '接口',
        local_account_id: '本地账户名'
      }
      return obj[value]
    }
  }
}
</script>

<style lang="scss" scoped>
.account {
  padding: 20px;
  p {
    color: #666;
  }
}
</style>
