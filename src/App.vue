<script setup>
import { ref, onMounted, nextTick } from 'vue'
import * as echarts from 'echarts'

const activeMenu = ref('1')
const handleSelect = (index) => {
  activeMenu.value = index
  nextTick(() => {
    if (index === '1') { initRadar(); initLine(); }
    if (index === '2') initSankey();
  })
}

// 模拟从你的截图提取的真实数据
const stats = [
  { label: '自然流超越', value: '49.4%' },
  { label: '互动率潜力', value: '10.2%' },
  { label: '粉丝回访率', value: '47.1%' },
  { label: '获客单价(预估)', value: '¥ 0.42' }
]

// 1. 账号潜力：五维雷达图（基于你的雷达图截图）
const initRadar = () => {
  const chart = echarts.init(document.getElementById('radarChart'))
  chart.setOption({
    title: { text: '账号综合能力诊断', left: 'center' },
    radar: {
      indicator: [
        { name: '播放量', max: 100 }, { name: '完播率', max: 100 },
        { name: '粉丝净增', max: 100 }, { name: '投稿量', max: 100 }, { name: '互动率', max: 100 }
      ]
    },
    series: [{
      type: 'radar',
      data: [{ value: [65, 40, 30, 20, 85], name: '当前表现', areaStyle: { color: 'rgba(64, 158, 255, 0.4)' } }]
    }]
  })
}

// 2. 流量去向：深度桑基图（拉投资核心：展示变现路径）
const initSankey = () => {
  const chart = echarts.init(document.getElementById('sankeyChart'))
  chart.setOption({
    series: [{
      type: 'sankey',
      emphasis: { focus: 'adjacent' },
      data: [
        { name: '自然推荐(截图569次)' }, { name: '主页访问' }, 
        { name: '粉丝转化' }, { name: '商单转化' }, { name: '流失' }
      ],
      links: [
        { source: '自然推荐(截图569次)', target: '主页访问', value: 120 },
        { source: '自然推荐(截图569次)', target: '流失', value: 449 },
        { source: '主页访问', target: '粉丝转化', value: 68 }, // 对应你截图的68个粉丝
        { source: '主页访问', target: '商单转化', value: 12 }
      ]
    }]
  })
}

// 3. 增长趋势图（基于你截图中的曲线）
const initLine = () => {
  const chart = echarts.init(document.getElementById('lineChart'))
  chart.setOption({
    xAxis: { type: 'category', data: ['09.01', '09.02', '09.03', '09.04', '09.05', '09.06', '09.07'] },
    yAxis: { type: 'value' },
    series: [{ data: [25, 30, 20, 68, 45, 52, 38], type: 'line', smooth: true, lineStyle: { width: 4 } }]
  })
}

onMounted(() => { initRadar(); initLine(); })
</script>
<style>
body { margin: 0; }
.layout-container { height: 100vh; }
</style>
