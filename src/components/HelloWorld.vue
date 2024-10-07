<template>
  <div>
    <div style="width: 1000px;margin: 20px auto;text-align: center;">
      f51:日期
      f52:开盘价
      f53:收盘价
      f54:最高价
      f55:最低价
      f56:成交量
      f57:成交额
      f58:振幅
      f59:下跌百分比
      f60:涨跌百分比
      f61:涨跌几块钱
    </div>
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
      <el-button type="primary" @click="submitQuery">查询</el-button>
      <el-button type="primary" @click="opt">操作</el-button>
    </div>
    <div style="width: 1000px;margin: 20px auto;max-height: 1000px;">
      <span>
        股票名 {{tableData.name}}
      </span>
    </div>
    <el-table :data="tableData" border style="width: 1000px;margin: 20px auto;" max-height="800">
      <el-table-column v-for="(item, index) in tableProp" :prop="item.prop" :label="item.label"></el-table-column>
    </el-table>
    <div style="width: 1000px;margin: 20px auto;max-height: 1000px;">
      <span>
        买入跌幅 {{ tableData.buyInRate }}%
      </span>
      <span style="margin-left: 20px;">
        平均最大收益率 {{tableData2.averageMaxIncomeRate}}%
      </span>
      <span style="margin-left: 20px;">
        平均最小收益率 {{ tableData2.averageMinIncomeRate }}%
      </span>
      <span style="margin-left: 20px;">
        总最大收益率 {{ tableData2.total1 }}%
      </span>
      <span style="margin-left: 20px;">
        总最小收益率 {{ tableData2.total2 }}%
      </span>
    </div>
    <el-table :data="tableData2" border style="width: 1000px;margin: 20px auto;" max-height="800">
      <el-table-column v-for="(item, index) in tableProp2" :prop="item.prop" :label="item.label" :formatter="item.formatter"></el-table-column>
    </el-table>
    <el-table :data="tableData3" border style="width: 1000px;margin: 20px auto;" max-height="800">
      <el-table-column v-for="(item, index) in tableProp3" :prop="item.prop" :label="item.label" :formatter="item.formatter" sortable></el-table-column>
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
    beg: '20230820',
    end: '20240820'
  })

  const secidList = ['0.002884']

  const tableProp = [
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

  const tableProp2 = [
    {
      label: '操作日期',
      prop: 'optDate',
      formatter: (row) => row.optDate
    },
    {
      label: '最大收益率(在最低点买)',
      prop: 'maxIncomeRate',
      formatter: (row) => row.maxIncomeRate + "%"
    },
    {
      label: '最小收益率',
      prop: 'minIncomeRate',
      formatter: (row) => row.minIncomeRate + "%"
    },
    {
      label: '最低点跌幅',
      prop: 'lowPointRate',
      formatter: (row) => row.lowPointRate + "%"
    },
    {
      label: '收盘跌幅',
      prop: 'dayRate',
      formatter: (row) => row.dayRate + "%"
    }
  ]

  const tableProp3 = [
    {
      label: '股票名',
      prop: 'name'
    },
    {
      label: '买入跌幅',
      prop: 'buyInRate',
      formatter: (row) => row.buyInRate + "%"
    },
    {
      label: '总最大收益率',
      prop: 'total1',
      formatter: (row) => row.total1 + "%"
    },
    {
      label: '平均最大收益率',
      prop: 'averageMaxIncomeRate',
      formatter: (row) => row.averageMaxIncomeRate + "%"
    },
    {
      label: '总最小收益率',
      prop: 'total2',
      formatter: (row) => row.total2 + "%"
    },
    {
      label: '平均最小收益率',
      prop: 'averageMinIncomeRate',
      formatter: (row) => row.averageMinIncomeRate + "%"
    },
    {
      label: '操作次数',
      prop: 'optTime'
    }
  ]

  const tableData = ref([])
  const tableData2 = ref([])
  const tableData3 = ref([])
  const submitQuery = () => {
    tableData3.value = []
      secidList.forEach(secid => {
        formData.value.secid = secid
        axios.get('http://push2his.eastmoney.com/api/qt/stock/kline/get', {
          params: formData.value
        }).then(res => {
          tableData.value= []
          tableData2.value= []
          // tableData3.value= []
          if (!res?.data?.data) {
            console.log(222, secid)
          }
          const klines = res.data.data.klines

          for (let index = klines.length - 1; index >= 0; index--) {
            let splitData = klines[index].split(",");
            let tableItem = {};
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
          tableData.value.name = res.data.data.name;
          // optSingle()
          opt()
        })
    })
  }

  const opt = () => {
    const buyInRateInterval= [Number(-1.9), Number(-3.0)];
    for(let buyInRate = buyInRateInterval[1]; buyInRate <= buyInRateInterval[0]; buyInRate += 0.10) {
      const optArr = []
      let optData = tableData.value;
      for(let index = optData.length - 1; index >= 1; index--) {
        if (index === optData.length - 1) {
          continue;
        }
        let currentData = optData[index];
        let preDate = optData[index + 1];
        let lowPointRate = (currentData.lowestPrice - preDate.closingPrice) / preDate.closingPrice * 100; // 最低点涨跌幅
        
        if (lowPointRate <= buyInRate) {
          let maxIncomeRate = currentData.dayRate - lowPointRate
          let minIncomeRate = currentData.dayRate - (buyInRate);

          optArr.push({
            optDate: currentData.date,
            maxIncomeRate: maxIncomeRate.toFixed(3),
            minIncomeRate: minIncomeRate.toFixed(3),
            lowPointRate: lowPointRate.toFixed(3),
            dayRate: currentData.dayRate.toFixed(3),
          })
        }
      }
      let total1 = Number(0.0);
      let total2 = Number(0.0);
      let count = Number(0.0);
      optArr.forEach(item => {
        total1 += Number(item.maxIncomeRate);
        total2 += Number(item.minIncomeRate);
        count++;
      })
      
      tableData3.value.push({
        name: tableData.value.name,
        buyInRate: buyInRate.toFixed(3),
        averageMaxIncomeRate: count > 0.02 ? Number(total1 / count).toFixed(3) : 0,
        averageMinIncomeRate: count > 0.02 ? Number(total2 / count).toFixed(3) : 0,
        total1: total1.toFixed(3),
        total2: total2.toFixed(3),
        optTime: count
      })
    }
  }

  const optSingle = () => {
    const buyInRate = -3.0;
    const optArr = []
      let optData = tableData.value;
      for(let index = optData.length - 1; index >= 1; index--) {
        if (index === optData.length - 1) {
          continue;
        }
        let currentData = optData[index];
        let preDate = optData[index + 1];
        let lowPointRate = (currentData.lowestPrice - preDate.closingPrice) / preDate.closingPrice * 100; // 最低点涨跌幅
        
        if (lowPointRate <= buyInRate) {
          let maxIncomeRate = currentData.dayRate - lowPointRate
          let minIncomeRate = currentData.dayRate - (buyInRate);

          optArr.push({
            optDate: currentData.date,
            maxIncomeRate: maxIncomeRate.toFixed(3),
            minIncomeRate: minIncomeRate.toFixed(3),
            lowPointRate: lowPointRate.toFixed(3),
            dayRate: currentData.dayRate.toFixed(3)
          })
        }
      }
      let total1 = Number(0.0);
      let total2 = Number(0.0);
      let count = Number(0.0);
     
      optArr.forEach(item => {
        total1 += Number(item.maxIncomeRate);
        total2 += Number(item.minIncomeRate);
        count++;
      })
      tableData2.value = optArr;
      if (count > 0) {
        tableData2.value.averageMaxIncomeRate = Number(total1 / count).toFixed(3);
        tableData2.value.averageMinIncomeRate = Number(total2 / count).toFixed(3);
      } else {
        tableData2.value.averageMaxIncomeRate = 0.0;
        tableData2.value.averageMinIncomeRate = 0.0;
      }
      tableData2.value.total1 = Number(total1).toFixed(3);
      tableData2.value.total2 = Number(total2).toFixed(3);
      tableData.value.buyInRate = buyInRate.toFixed(3);
  }
</script>



<style scoped>
</style>
