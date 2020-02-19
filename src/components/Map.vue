<template>
  <div>
    <!-- 初始化echarts需要个有宽高的盒子 -->
    <div ref="mapbox" style="height:400px;width:600px;margin:100px auto;"></div>
  </div>
</template>

<script>
import echarts from "echarts";
import 'echarts/map/js/china.js'
import jsonp from 'jsonp'
const option = {
  title: {
    text:"疫情地图",
    // link:"https://www.baidu.com",
    subtext:"杜建武制作",
    // sublink:""
  },
  series:[{
    name:'确诊人数',
    type:'map', // 告诉echarts 要去渲染的是一个地图
    map:'china',
    label:{
      // 控制对应地区的汉字
      show:true,
      color:'#333', // 控制地区名的字体颜色
      fontSize:10
    },
    zoom:1.2, // 控制地图的放大和缩小
    roam:true, // 滚轮放大缩小地图
    itemStyle:{ 
      areaColor:'#eee',// 地图板块的背景色
      borderColor:'blue' // 边界线颜色
    },
    emphasis:{
      // 控制鼠标滑过之后的背景色和字体颜色
      label:{
        color:'#fff',
        fontSize:12
      },
      itemStyle:{
        areaColor:'#83b5e7'
      }
    },
    data:[] // 用后台获取数据
  }],
  visualMap:[{
    type:'piecewise',
    show:true,
    // splitNumber:4
    pieces:[
      // 分段
      {min:10000},
      {min:1000,max:9999},
      {min:100,max:999},
      {min:10,max:99},
      {min:1,max:9}
    ],
    // align:'right', // 默认left
    // orient:'horizontal' // 默认竖直
    // left right 这些属性控制 分段所在的位置
    inRange:{
      symbol:'rect',
      color:['#ffc0b1','#9c0505']
    },
    itemWidth:20,
    itemHeight:10
  }],
  tooltip:{
    trigger:'item'
  },
  toolbox:{
    show:true,
    orient:'vertical',
    left:'right',
    top:'center',
    feature:{
      dataView:{readOnly:false},
      restore:{},
      saveAsImage:{}
    }
  }
};
export default {
  mounted() {
    this.getData()
    this.mychart = echarts.init(this.$refs.mapbox)
    this.mychart.setOption(option)
  },
  methods: {
    getData() {
      jsonp('https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427',{},(err,data)=>{
        if (!err) {
          // 代表请求数据成功
          console.log(data);
          let list = data.data.list.map(item=>({name:item.name,value:item.value}))
          option.series[0].data = list
          this.mychart.setOption(option)
        }
      })
    }
  }
};
</script>

<style scoped>
</style>
