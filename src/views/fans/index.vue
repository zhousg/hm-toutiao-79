<template>
  <div class="container">
    <el-card>
      <div slot="header">
        <my-bread>粉丝管理</my-bread>
      </div>
      <!-- 标签页 tabs -->
      <el-tabs v-model="activeName" type="card">
        <el-tab-pane label="粉丝列表" name="list">
          <div class="fans_list">
            <!-- id 默认是对象类型 使用了json-bigint  key需要字符串 数字类型 -->
            <div class="fans_item" v-for="item in fansList" :key="item.id.toString()">
              <el-avatar :size="80" :src="item.photo"></el-avatar>
              <p>{{item.name}}</p>
              <el-button type="primary" plain size="small">+关注</el-button>
            </div>
          </div>
          <!-- 分页 -->
          <el-pagination
            style="text-align:center"
            background
            layout="prev, pager, next"
            :total="total"
            :page-size="reqParams.per_page"
            :current-page="reqParams.page"
            @current-change="changePager"
            hide-on-single-page
          ></el-pagination>
        </el-tab-pane>
        <el-tab-pane label="粉丝画像" name="photo">
          <div ref="bar" style="width: 600px;height:400px;"></div>
        </el-tab-pane>
      </el-tabs>
    </el-card>
  </div>
</template>

<script>
import echarts from 'echarts'
export default {
  data () {
    return {
      activeName: 'photo',
      reqParams: {
        page: 1,
        per_page: 24
      },
      fansList: [],
      total: 0
    }
  },
  created () {
    this.getFans()
  },
  mounted () {
    // 使用echart必须在dom渲染完毕后
    this.initBar()
  },
  methods: {
    initBar () {
      // 初始化柱状图
      const myEcharts = echarts.init(this.$refs.bar)
      // 什么样的配置 对应的去找 示例
      const option = {
        color: ['#3398DB'],
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            // 坐标轴指示器，坐标轴触发有效
            type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
          }
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: [
          {
            type: 'category',
            data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
            axisTick: {
              alignWithLabel: true
            }
          }
        ],
        yAxis: [
          {
            type: 'value'
          }
        ],
        series: [
          {
            name: '直接访问',
            type: 'bar',
            barWidth: '60%',
            data: [10, 52, 200, 334, 390, 330, 220]
          }
        ]
      }
      myEcharts.setOption(option)
    },
    changePager (newPage) {
      this.reqParams.page = newPage
      this.getFans()
    },
    async getFans () {
      const {
        data: { data }
      } = await this.$http.get('followers', { params: this.reqParams })
      this.fansList = data.results
      this.total = data.total_count
    }
  }
}
</script>

<style scoped lang='less'>
.fans_item {
  width: 120px;
  height: 170px;
  border: 1px dashed #ddd;
  text-align: center;
  padding-top: 10px;
  display: inline-block;
  margin-right: 50px;
  margin-bottom: 20px;
  p {
    font-size: 12px;
  }
}
</style>
