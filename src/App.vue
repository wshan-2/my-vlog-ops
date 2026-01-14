<template>
  <el-container class="layout-container">
    <el-aside width="200px" style="background-color: #304156;">
      <el-menu active-text-color="#ffd04b" background-color="#304156" text-color="#fff" :default-active="activeMenu" @select="handleSelect">
        <el-menu-item index="1">📊 项目总览</el-menu-item>
        <el-menu-item index="2">🔗 流量桑基图</el-menu-item>
        <el-menu-item index="3">💰 ROI 分析表</el-menu-item>
      </el-menu>
    </el-aside>
    <el-container>
      <el-header style="border-bottom: 1px solid #dcdfe6; display: flex; align-items: center; justify-content: space-between;">
        <strong style="font-size: 18px;">VlogOps 创作者经济罗盘</strong>
        <el-tag type="danger" effect="dark">Beta 内部版</el-tag>
      </el-header>
      <el-main>
        <div v-show="activeMenu === '1'">
          <el-row :gutter="20">
            <el-col :span="6" v-for="i in stats" :key="i.label">
              <el-card shadow="never" style="text-align: center;">
                <div style="color: #909399; font-size: 14px;">{{ i.label }}</div>
                <div style="font-size: 24px; font-weight: bold; margin-top: 10px; color: #409EFF;">{{ i.value }}</div>
              </el-card>
            </el-col>
          </el-row>
          <el-card shadow="never" style="margin-top: 20px;">
            <template #header>📈 收益预测趋势 (未来7天)</template>
            <div id="incomeChart" style="width: 100%; height: 350px;"></div>
          </el-card>
        </div>
        <div v-show="activeMenu === '2'">
          <el-card shadow="never">
            <template #header>🎬 转化路径：曝光 → 成交</template>
            <div id="sankeyChart" style="width: 100%; height: 500px;"></div>
          </el-card>
        </div>
        <div v-show="activeMenu === '3'">
          <el-table :data="tableData" stripe style="width: 100%; border: 1px solid #ebeef5;">
            <el-table-column prop="date" label="日期" /><el-table-column prop="name" label="Vlog标题" />
            <el-table-column prop="cost" label="投入" /><el-table-column prop="income" label="产出" />
            <el-table-column label="ROI"><template #default="scope"><el-tag :type="scope.row.roi >= 1.5 ? 'success' : 'warning'">{{ scope.row.roi }}</el-tag></template></el-table-column>
          </el-table>
        </div>
      </el-main>
    </el-container>
  </el-container>
</template>

<script setup>
import { ref, onMounted, nextTick } from 'vue'
import * as echarts from 'echarts'

const activeMenu = ref('1')
const handleSelect = (index) => {
  activeMenu.value = index
  nextTick(() => {
    if (index === '1') initIncomeChart()
    if (index === '2') initSankeyChart()
  })
}

const stats = [{ label: '本月总 GMV', value: '¥ 84,200' }, { label: '平均 ROI', value: '2.14' }, { label: '新增粉丝', value: '+12,403' }, { label: '获客成本', value: '¥ 0.85' }]
const tableData = [{ date: '2023-10-01', name: '挑战最辣火锅', cost: '¥2,000', income: '¥5,800', roi: 2.9 }, { date: '2023-10-10', name: '巴黎买手店', cost: '¥4,500', income: '¥9,200', roi: 2.05 }]

const initIncomeChart = () => {
  const dom = document.getElementById('incomeChart')
  if (!dom) return
  const myChart = echarts.init(dom)
  myChart.setOption({
    tooltip: { trigger: 'axis' },
    xAxis: { type: 'category', data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日'] },
    yAxis: { type: 'value' },
    series: [{ data: [820, 932, 901, 934, 1290, 1330, 1320], type: 'line', smooth: true, areaStyle: {color: 'rgba(64, 158, 255, 0.2)'} }]
  })
}

const initSankeyChart = () => {
  const dom = document.getElementById('sankeyChart')
  if (!dom) return
  const myChart = echarts.init(dom)
  myChart.setOption({
    series: [{
      type: 'sankey',
      data: [{ name: '视频曝光' }, { name: '橱窗点击' }, { name: '下单支付' }, { name: '流失' }],
      links: [
        { source: '视频曝光', target: '橱窗点击', value: 5000 },
        { source: '橱窗点击', target: '下单支付', value: 1500 },
        { source: '橱窗点击', target: '流失', value: 3500 }
      ]
    }]
  })
}
onMounted(() => { initIncomeChart() })
</script>

<style>body { margin: 0; } .layout-container { height: 100vh; }</style>
