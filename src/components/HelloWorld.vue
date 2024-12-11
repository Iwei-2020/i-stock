<template>
  <div>
    {{ totalCount + '————' + successCount }}
    <div style="width: 1000px;margin: 20px auto;">
      <el-form label-width="200">
        <el-form-item label="fields1">
          <el-input v-model="formData.fields1"></el-input>
        </el-form-item>
        <el-form-item label="fields2">
          <el-input v-model="formData.fields2"></el-input>
        </el-form-item>
        <el-form-item label="klt(行情周期类型)">
          <el-input v-model="formData.klt"></el-input>
        </el-form-item>
        <el-form-item label="fqt(价格复权方式)">
          <el-input v-model="formData.fqt"></el-input>
        </el-form-item>
        <el-form-item label="secid(股票代码)">
          <el-input v-model="formData.secid"></el-input>
        </el-form-item>
        <el-form-item label="beg">
          <el-input v-model="formData.beg"></el-input>
        </el-form-item>
        <el-form-item label="end">
          <el-input v-model="formData.end"></el-input>
        </el-form-item>
      </el-form>
    </div>
    <div style="margin: 20px auto;text-align: center;">
      <el-button type="primary" @click="submitQuery2">查询</el-button>
      <el-button type="primary" @click="opt">操作</el-button>
    </div>
    <div style="width: 1000px;margin: 20px auto;max-height: 1000px;">
      <span>
        股票名 {{tableData.name}}
      </span>
    </div>
    <el-table :data="tableData" border style="width: 1600px;margin: 20px auto;" max-height="800">
      <el-table-column v-for="(item, index) in tableProp" :prop="item.prop" :label="item.label"></el-table-column>
    </el-table>
  </div>
</template>

<script setup>
  import { ref } from 'vue'
  import axios from 'axios'

  const formData = ref({
    fields1: 'f1,f2,f3,f4,f5,f6',
    fields2: 'f51,f52,f53,f54,f55,f56,f57,f58,f59,f60,f61',
    klt: '101',
    fqt: '1',
    secid: '0.000429',
    beg: '20241202',
    end: '20241211'
  })

  const secidList = ref([])

  const tableProp = [
    {
      label: '股票代码',
      prop: 'stockCode'
    },
    {
      label: '股票名',
      prop: 'stockName'
    },
    {
      label: '日期',
      prop: 'date'
    },
    {
      label: '开盘价',
      prop: 'openingPrice'
    },
    {
      label: '收盘价',
      prop: 'closingPrice'
    },
    {
      label: '高',
      prop: 'highestPrice'
    },
    {
      label: '低',
      prop: 'lowestPrice'
    },
    {
      label: '涨跌幅',
      prop: 'dayRate'
    },
    {
      label: '涨跌幅（价格）',
      prop: 'dayPriceRate'
    },
    {
      label: '换手率',
      prop: 'changeHand'
    },
    {
      label: '总手',
      prop: 'totalHand'
    },
    {
      label: '成交额',
      prop: 'businessVolume'
    },
    {
      label: '振幅',
      prop: 'swingRate'
    }
  ]

  // 7天内有一天涨停
  // 今天不能涨停
  // 今天最高点大于5%

  const totalCount = ref(0);
  const successCount = ref(0)
  const tableData = ref([])
  const tableData2 = ref([])
  const submitQuery = () => {
    const instance = axios.create({
      timeout: 20000
    });
    secidList.value.slice(1000, 1500).forEach(item => {
      formData.value.secid = item.secId;
      instance.get('http://push2his.eastmoney.com/api/qt/stock/kline/get', {
        params: formData.value
      }).then(res => {
        // tableData.value= []
        // tableData2.value= []
        if (!res?.data?.data) {
          console.log(222, item.secId)
        }
        const klines = res.data.data.klines

        let day1 = klines[0].split(",");
        let day2 = klines[1].split(",");
        let day3 = klines[2].split(",");
        let day4 = klines[3].split(",");
        let day5 = klines[4].split(",");
        let day6 = klines[5].split(",");
        let day7 = klines[6].split(",");
        let day8 = klines[7].split(",");
        const condition1 = (day8[3] - day7[2]) / day7[2] > 0.05
        const condition2 = day1[8] > 9.89 || day2[8] > 9.89 || day3[8] > 9.89 || day4[8] > 9.89 || day5[8] > 9.89 || day6[8] > 9.89 || day7[8] < 9.89 && condition1
        if (condition2) {
          dataInit(day1, item)
          dataInit(day2, item)
          dataInit(day3, item)
          dataInit(day4, item)
          dataInit(day5, item)
          dataInit(day6, item)
          dataInit(day7, item)
          dataInit(day8, item)
          // totalCount.value = totalCount.value + 1;
          // if ((day8[3] - day7[2]) / day7[2] > 0.06) {
          //   successCount.value = successCount.value + 1;
          // }
        }
      }).catch(err => {
        console.log(err)
      })
    })
  }

  const dataInit = (splitData, item) => {
    let tableItem = {
      stockCode:  item.stockCode,
      stockName: item.stockName
    };
    tableItem.date = splitData[0]
    tableItem.openingPrice = Number(splitData[1])
    tableItem.closingPrice = Number(splitData[2])
    tableItem.highestPrice = Number(splitData[3])
    tableItem.lowestPrice = Number(splitData[4])
    tableItem.totalHand = Number(splitData[5])
    tableItem.businessVolume = Number(splitData[6])
    tableItem.swingRate = Number(splitData[7])
    tableItem.dayRate = Number(splitData[8])
    tableItem.dayPriceRate = Number(splitData[9])
    tableItem.changeHand = Number(splitData[10])
    tableData.value.push(tableItem)
  }

  const submitQuery2 = () => {
      const instance = axios.create({
        timeout: 20000
      });
      instance.get('https://push2.eastmoney.com/api/qt/clist/get?fid=f184&po=1&pz=6000&pn=1&np=1&fltt=2&invt=2&fields=f2,f3,f12,f13,f14,f62,f184,f225,f165,f263,f109,f175,f264,f160,f100,f124,f265,f1&ut=b2884a393a59ad64002292a3e90d46a5&fs=m:0+t:6+f:!2,m:0+t:13+f:!2,m:0+t:80+f:!2,m:1+t:2').then(res => {
        // f2: 收盘价
        // f3: 涨幅
        // f12: 股票代码
        // f14: 股票名
        // f100: 行业
        const data = res.data.data.diff;
        tableData.value= []

        for (let index = 0; index < data.length; index++) {
          let dataItem = data[index];
          secidList.value.push({
            stockCode: dataItem.f12,
            stockName: dataItem.f14,
            secId: dataItem.f13 + '.' + dataItem.f12
          })
        }
        submitQuery();
      })
  }

  const opt = () => {

    if (day1.dayRate < 0 && day2.dayRate < 0 && day3.dayRate < 0) {
      tableData2.value.push()
    }
  }

</script>



<style scoped>
</style>
