<template>
  <div>
    <div class="charts" >
    <div id="main" style="width: 900px;height:400px;"></div>
  </div>

    <v-card max-width="1024" class="mx-auto">
      <v-container fluid>
        <v-card-title>有效排行榜前五</v-card-title>
        <v-card-subtitle>指如果前五有不是20级的成员，在多加一位直到是20级为止（最多展示10个人）</v-card-subtitle>
        <v-simple-table
          fixed-header
          height="320px"
        >
          <template #default>
            <thead>
              <tr>
                <th>学号</th>
                <th>姓名</th>
                <th>部门</th>
                <th>地点</th>
                <th>总时长</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="item in tableData"
                :key="item.userId"
              >
                <td>{{item.userId}}</td>
                <td>{{item.userName}}</td>
                <td>{{item.userDept}}</td>
                <td>{{item.userLocation}}</td>
                <td>{{item.totalTime}}</td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-container>
    </v-card>
  </div>
</template>

<script>
  // 按需引入 引入 ECharts 主模块
var echarts = require('echarts/lib/echarts')
// 引入柱状图
require('echarts/lib/chart/bar')
// 引入提示框和标题组件
require('echarts/lib/component/tooltip')
require('echarts/lib/component/title')
export default {
  data() {
    return {

      chart:null,
      username:[],
      signTime:[],
      rankList: {
        headers: [
          {
            text: '学号',
            align: 'center',
            sortable: false,
            value: 'userid'
          },
          {
            text: '姓名',
            align: 'center',
            sortable: false,
            value: 'username',
            class: 'data-table-td-mw100'
          },
          {
            text: '部门',
            align: 'center',
            sortable: false,
            value: 'dept',
            class: 'data-table-td-mw100'
          },
          {
            text: '地点',
            align: 'center',
            sortable: false,
            value: 'location'
          },
          {
            text: '总时长',
            align: 'center',
            sortable: false,
            value: 'allTime'
          },
          {
            text: '排名',
            align: 'center',
            sortable: false,
            value: 'index'
          }
        ],
        items: []
      }
    }
  },
  mounted() {
    this.initCharts();
  },
  beforeMount() {
    
    //get rankList
    this.$http
      .get('/TopFive')
      .then(res => {
        this.rankList.items = res.data.list
        this.rankList.items.forEach((item, index) => {
          item.index = index + 1
          this.username.push(item.dept+'-'+item.username)
          this.signTime.push(item.allTime)
          
        })
        this.chart.setOption(
          {
            xAxis: {
            data: this.username
        },
        series: [{
            // 根据名字对应到相应的系列
            name: '周签到时长',
            data: this.signTime
        }]
          }
        )
        //console.log(this.chart_data)
      })
      .catch(err => {
        throw err
      })
  },
  methods: {
    initCharts() {
    this.chart = echarts.init(document.getElementById('main'));
    this.setOptions();
  },
  setOptions() {
    this.chart.setOption({
      title: {
        text: '签到时长排名',
        textStyle:{
        color:'#b03a5b',
        fontSize:"18", 
      },
      left:'center',
      top:'20'
      },
      color:['#46afe4'],
      tooltip: {
        show:true
      },
      toolbox:{
        show:true,
        orient:'horizontal',
        feature:{
          saveAsImage:{
            show:true,
            name:'签到时长排名'
          }
        }
      },
      xAxis: {
        data: [],
        axisLabel: {
            fontSize: 14,
            color:'#0282fd'
        },
        axisLine: {
            lineStyle: {
                color: '#90caf9'
            }
        },
      },
      yAxis: {
        axisLabel: {
            fontSize: 14,
            color:'#0282fd'
        },
        axisLine: {
            lineStyle: {
                color: '#e7e7e7'
            }
        },
      },
      series: [{
        name: '签到时长',
        type: 'bar',
        data: [],
        label:{
          show:true,
          position:'top'
        },
        itemStyle:{
          normal: {
                color: (params) => {
                    let colors = [ '#28bf7e', '#ed7c2f', '#dfcb56', '#b6c2ff', '#e33e33']
                    return colors[params.dataIndex]
                }
            }
        }
      }]
    })
  }
  },
}
</script>

<style>
  .charts{
    width: 100%;
    height: 100%;
    display: flex;            
    display: -webkit-flex;            
    justify-content: center;            
    align-items: center; 
    margin-bottom: -60px;
  }
</style>