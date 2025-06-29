<template>
  <section id="ecg" class="py-24 sm:py-32">
    <div class="container">
      <div class="header">
        <h1>心电检测评估</h1>
        <p class="description">利用心电信号分析技术，检测心理应激状态下的生理指标变化</p>
      </div>
      
      <div class="control-panel">
        <div class="file-input-wrapper">
          <label class="custom-file-input">
            <span>选择文件</span>
            <input type="file" class="hidden" @change="handleFileChange" accept=".csv,.txt">
          </label>
          <div class="file-name">{{ fileName }}</div>
          <div class="file-tip">请选择CSV或TXT格式的心电数据文件</div>
          <div class="file-format-description">
            <p class="format-info">
              📄 每行一个数值 • 🔍 自动过滤标准化 • 📊 动态滚动显示
            </p>
          </div>
        </div>
      </div>

      <div class="ecg-display">
        <div class="ecg-channel">
          <div class="channel-label">心电波形显示</div>
          <canvas ref="ecgCanvas" width="800" height="450"></canvas>
        </div>
      </div>

      <button class="result-button" @click="showResult" :disabled="isAnalyzing">
        {{ isAnalyzing ? '分析中...' : '显示结果' }}
      </button>
      
      <!-- 进度条 -->
      <div v-if="isAnalyzing" class="progress-container">
        <div class="progress-bar">
          <div class="progress-fill" :style="{ width: analysisProgress + '%' }"></div>
        </div>
        <p class="progress-text">正在分析心电数据... {{ analysisProgress }}%</p>
        <p class="data-info">已处理 {{ processedDataPoints }} / {{ totalDataPoints }} 个数据点</p>
      </div>
      
      <div class="result-display" v-show="showResultDisplay">{{ resultText }}</div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const fileName = ref('未选择文件')
const showResultDisplay = ref(false)
const resultText = ref('')
const ecgCanvas = ref<HTMLCanvasElement | null>(null)
const isAnalyzing = ref(false)
const analysisProgress = ref(0)
const processedDataPoints = ref(0)
const totalDataPoints = ref(0)

let waveformData: number[] = []
let startIndex = 0
let animationId: number | null = null
const samplingRate = 500 // 500Hz采样率
const timeWindow = 5 // 显示5秒的数据
const dataPointsPerWindow = samplingRate * timeWindow

const handleFileChange = (e: Event) => {
  const target = e.target as HTMLInputElement
  const file = target.files?.[0]
  
  if (file) {
    fileName.value = file.name
    const reader = new FileReader()
    
    reader.onload = (event) => {
      try {
        const content = event.target?.result as string
        parseECGData(content)
      } catch (error) {
        alert('读取文件时发生错误：' + (error as Error).message)
      }
    }
    
    reader.onerror = () => {
      alert('读取文件失败')
    }
    
    reader.readAsText(file)
  } else {
    fileName.value = '未选择文件'
  }
}

// 解析CSV数据（参考web.html的实现）
const parseECGData = (csvData: string) => {
  const lines = csvData.split('\n')
  const data: number[] = []
  
  lines.forEach(line => {
    const value = parseFloat(line.trim())
    if (!isNaN(value)) {
      data.push(value)
    }
  })
  
  console.log(`解析到 ${data.length} 个数据点`)
  
  // 过滤数据：移除小于20000的值（参考web.html）
  const filteredData = filterData(data)
  console.log(`过滤后剩余 ${filteredData.length} 个数据点`)
  
  if (filteredData.length === 0) {
    alert('没有找到有效的心电数据（大于20000的值）')
    return
  }
  
  // 标准化数据到[0,1]范围
  waveformData = normalizeData(filteredData)
  
  // 开始动态绘制
  startIndex = 0
  drawWaveform()
  
  alert(`成功导入${waveformData.length}个有效数据点，开始动态显示`)
  
  // 保存数据用于分析
  const ecgData = {
    rawData: waveformData,
    timestamp: new Date().toISOString(),
    dataPoints: waveformData.length
  }
  localStorage.setItem('ecgData', JSON.stringify(ecgData))
}

// 过滤数据：移除小于20000的值
const filterData = (data: number[]) => {
  return data.filter(value => value >= 20000)
}

// 标准化数据到[0,1]范围
const normalizeData = (data: number[]) => {
  const max = Math.max(...data)
  const min = Math.min(...data)
  return data.map(value => (value - min) / (max - min))
}

// 动态绘制心电波形
const drawWaveform = () => {
  if (!ecgCanvas.value || waveformData.length === 0) return
  
  const canvas = ecgCanvas.value
  const ctx = canvas.getContext('2d')
  if (!ctx) return
  
  const canvasWidth = canvas.width
  const canvasHeight = canvas.height
  
  const xScale = canvasWidth / dataPointsPerWindow
  const yScale = canvasHeight * 0.6 // 使用60%的高度显示波形幅度
  const yOffset = canvasHeight * 0.75 // 将波形整体下移到更靠下的位置
  
  // 清除画布
  ctx.clearRect(0, 0, canvasWidth, canvasHeight)
  
  // 绘制背景网格
  drawGrid(ctx, canvasWidth, canvasHeight)
  
  // 绘制心电波形（中间位置显示）
  ctx.strokeStyle = '#FF8C00'
  ctx.lineWidth = 2.5
  ctx.lineCap = 'round'
  ctx.lineJoin = 'round'
  ctx.beginPath()
  
  const endIndex = Math.min(startIndex + dataPointsPerWindow, waveformData.length)
  
  for (let i = startIndex; i < endIndex; i++) {
    const x = (i - startIndex) * xScale
    // 让波形在canvas中间位置显示
    const y = yOffset - (waveformData[i] - 0.5) * yScale
    
    if (i === startIndex) {
      ctx.moveTo(x, y)
    } else {
      ctx.lineTo(x, y)
    }
  }
  
  ctx.stroke()
  
  // 绘制信息
  drawInfo(ctx, canvasWidth, canvasHeight)
  
  // 更新startIndex实现滚动效果
  if (waveformData.length > dataPointsPerWindow) {
    startIndex = (startIndex + 3) % (waveformData.length - dataPointsPerWindow)
  }
  
  // 继续动画
  animationId = requestAnimationFrame(drawWaveform)
}

// 绘制背景网格
const drawGrid = (ctx: CanvasRenderingContext2D, width: number, height: number) => {
  // 背景
  ctx.fillStyle = '#1a1a1a'
  ctx.fillRect(0, 0, width, height)
  
  // 网格线
  ctx.strokeStyle = '#333'
  ctx.lineWidth = 0.5
  
  // 垂直网格线
  const gridSpacing = 40
  for (let x = 0; x <= width; x += gridSpacing) {
    ctx.beginPath()
    ctx.moveTo(x, 0)
    ctx.lineTo(x, height)
    ctx.stroke()
  }
  
  // 水平网格线
  for (let y = 0; y <= height; y += gridSpacing) {
    ctx.beginPath()
    ctx.moveTo(0, y)
    ctx.lineTo(width, y)
    ctx.stroke()
  }
  

}



// 绘制信息
const drawInfo = (ctx: CanvasRenderingContext2D, width: number, height: number) => {
  const textX = width - 180
  const textY = 20
  
  // 科技感数据文字 - 发光效果，更大字体
  ctx.fillStyle = '#00FFFF'
  ctx.font = 'bold 16px "Courier New", monospace'
  ctx.textAlign = 'left'
  ctx.shadowColor = '#00FFFF'
  ctx.shadowOffsetX = 0
  ctx.shadowOffsetY = 0
  ctx.shadowBlur = 10
  
  ctx.fillText(`▶ DATA: ${waveformData.length.toLocaleString()}`, textX, textY + 20)
  ctx.fillText(`▶ POS: ${startIndex.toLocaleString()}`, textX, textY + 45)
  ctx.fillText(`▶ WIN: ${timeWindow}s`, textX, textY + 70)
  
  // 重置阴影
  ctx.shadowColor = 'transparent'
  ctx.shadowBlur = 0
}

const showResult = async () => {
  if (waveformData.length === 0) {
    alert('请先导入心电数据文件')
    return
  }
  
  isAnalyzing.value = true
  analysisProgress.value = 0
  totalDataPoints.value = waveformData.length
  processedDataPoints.value = 0
  
  // 使用更精确的进度计算
  const totalPoints = waveformData.length
  let currentPoints = 0
  
  while (currentPoints < totalPoints) {
    // 每次处理固定比例的数据点
    currentPoints = Math.min(currentPoints + Math.ceil(totalPoints * 0.01), totalPoints)
    
    // 计算实际进度百分比
    const progress = Math.floor((currentPoints / totalPoints) * 100)
    
    processedDataPoints.value = currentPoints
    analysisProgress.value = progress
    
    // 等待一小段时间模拟处理
    await new Promise(resolve => setTimeout(resolve, 30))
  }
  
  // 确保最终状态为100%
  processedDataPoints.value = totalPoints
  analysisProgress.value = 100
  
  // 分析完成
  isAnalyzing.value = false
  showResultDisplay.value = true
  
  // 简化的分析结果
  const dataStats = {
    totalPoints: waveformData.length,
    duration: (waveformData.length / samplingRate).toFixed(2),
    avgValue: (waveformData.reduce((a, b) => a + b, 0) / waveformData.length).toFixed(3)
  }
  
  // 模拟风险评估
  const riskLevel = Math.random() > 0.5 ? '正常' : '需要关注'
  const riskPercentage = Math.floor(Math.random() * 40) + 20
  
  resultText.value = `心电分析结果：${riskLevel} (${riskPercentage}%)\n` +
                    `数据时长：${dataStats.duration}秒\n` +
                    `平均值：${dataStats.avgValue}`
  
  // 保存详细的ECG分析结果
  const ecgResult = {
    riskLevel: riskLevel,
    riskPercentage: riskPercentage,
    duration: dataStats.duration,
    avgValue: dataStats.avgValue,
    totalPoints: dataStats.totalPoints,
    timestamp: new Date().toISOString()
  }
  localStorage.setItem('ecgResult', JSON.stringify(ecgResult))
  
  // 保存结果
  const completedTests = JSON.parse(localStorage.getItem('completedTests') || '{}')
  completedTests.ecg = '已完成'
  localStorage.setItem('completedTests', JSON.stringify(completedTests))
}

// 生命周期管理
onMounted(() => {
  // 组件挂载时设置canvas尺寸
  if (ecgCanvas.value) {
    const canvas = ecgCanvas.value
    canvas.width = 800
    canvas.height = 450
  }
})

onUnmounted(() => {
  // 清理动画
  if (animationId) {
    cancelAnimationFrame(animationId)
  }
})
</script>

<style scoped>
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  background: rgba(28, 28, 35, 0.8);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2),
              0 0 15px rgba(255, 140, 0, 0.1),
              0 0 30px rgba(255, 140, 0, 0.05);
  border: 1px solid rgba(255, 140, 0, 0.1);
  position: relative;
}

.container::before {
  content: '';
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  background: linear-gradient(45deg, rgba(255, 140, 0, 0.1), transparent 60%);
  border-radius: 22px;
  z-index: -1;
  animation: glowPulse 3s infinite;
}

@keyframes glowPulse {
  0% {
    opacity: 0.5;
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0.5;
  }
}

.header {
  text-align: center;
  color: white;
  margin-bottom: 40px;
}

.header h1 {
  font-size: 36px;
  font-weight: 700;
  color: rgb(250, 250, 249);
  margin-bottom: 16px;
  letter-spacing: -0.025em;
}

.header .description {
  font-size: 18px;
  color: rgba(168, 162, 158, 1);
  line-height: 2;
  margin-top: 10px;
  max-width: 600px;
  margin-left: auto;
  margin-right: auto;
}

.control-panel {
  display: flex;
  gap: 20px;
  margin-bottom: 30px;
  flex-wrap: wrap;
}

.file-input-wrapper {
  flex: 1;
  min-width: 300px;
}

.custom-file-input {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  white-space: nowrap;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 500;
  height: 40px;
  padding: 8px 16px;
  background-color: rgb(234, 88, 12);
  color: rgb(250, 250, 249);
  cursor: pointer;
  transition: background-color 0.2s;
}

.custom-file-input:hover {
  background-color: rgba(234, 88, 12, 0.9);
}

.hidden {
  display: none;
}

.file-name {
  margin-top: 10px;
  padding: 10px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 10px;
  min-height: 40px;
  color: #000;
}

.file-tip {
  margin-top: 8px;
  color: rgba(255, 255, 255, 0.8);
  font-size: 0.9em;
  text-align: center;
}

.file-format-description {
  margin-top: 15px;
  padding: 12px 15px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  border-left: 4px solid rgb(234, 88, 12);
}

.file-format-description p {
  color: rgba(255, 255, 255, 0.9);
  font-size: 0.85em;
  margin: 5px 0;
  line-height: 1.4;
}

.file-format-description .format-info {
  color: rgb(250, 250, 249);
  font-size: 1.1em;
  font-weight: 600;
  text-align: center;
  margin: 8px 0;
  line-height: 1.5;
  letter-spacing: 0.5px;
}

.file-format-description strong {
  color: rgb(250, 250, 249);
}

.ecg-display {
  display: flex;
  justify-content: center;
  margin: 40px 0;
  width: 100%;
}

.ecg-channel {
  background: rgba(255, 255, 255, 0.05);
  border-radius: 12px;
  padding: 20px;
  border: 1px solid rgba(255, 140, 0, 0.2);
  backdrop-filter: blur(5px);
  position: relative;
  min-height: 500px;
  width: 100%;
  max-width: 1000px;
}

.channel-label {
  color: white;
  font-weight: 600;
  margin-bottom: 15px;
  font-size: 18px;
  text-align: center;
  padding: 8px;
  background: rgba(255, 140, 0, 0.1);
  border-radius: 8px;
  border: 1px solid rgba(255, 140, 0, 0.3);
  position: relative;
  z-index: 2;
}

.ecg-channel canvas {
  width: 100% !important;
  height: 450px !important;
  background: rgba(0, 0, 0, 0.3);
  border-radius: 8px;
  border: 1px solid rgba(255, 140, 0, 0.2);
  display: block;
  object-fit: contain;
}

@media (max-width: 768px) {
  .ecg-display {
    margin: 20px 0;
  }
  
  .ecg-channel {
    min-height: 350px;
    padding: 15px;
  }
  
  .ecg-channel canvas {
    height: 350px !important;
  }
}

.result-button {
  display: block;
  width: 300px;
  height: 60px;
  margin: 30px auto;
  padding: 16px 32px;
  background-color: rgb(234, 88, 12);
  color: rgb(250, 250, 249);
  border: none;
  border-radius: 12px;
  font-size: 18px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(234, 88, 12, 0.3);
  text-align: center;
  line-height: 1.2;
}

.result-button:hover {
  background-color: rgba(234, 88, 12, 0.9);
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(234, 88, 12, 0.4);
}

.result-button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
  background-color: rgba(234, 88, 12, 0.5);
}

.result-display {
  margin-top: 20px;
  padding: 30px;
  background: linear-gradient(135deg, rgba(255, 140, 0, 0.95) 0%, rgba(255, 165, 0, 0.9) 50%, rgba(255, 140, 0, 0.95) 100%);
  border-radius: 20px;
  text-align: center;
  font-size: 1.4em;
  font-weight: 700;
  color: #1a1a1a;
  box-shadow: 
    0 8px 32px rgba(255, 140, 0, 0.3),
    0 0 20px rgba(255, 140, 0, 0.2),
    inset 0 1px 0 rgba(255, 255, 255, 0.3);
  border: 2px solid rgba(255, 140, 0, 0.4);
  position: relative;
  overflow: hidden;
  animation: resultGlow 2s ease-in-out infinite alternate;
}

.result-display::before {
  content: '';
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  background: linear-gradient(45deg, rgba(255, 140, 0, 0.6), transparent 30%, rgba(255, 140, 0, 0.6));
  border-radius: 22px;
  z-index: -1;
  animation: borderShine 3s linear infinite;
}

.result-display::after {
  content: '📊';
  position: absolute;
  top: 10px;
  right: 15px;
  font-size: 1.5em;
  opacity: 0.7;
}

@keyframes resultGlow {
  0% {
    box-shadow: 
      0 8px 32px rgba(255, 140, 0, 0.3),
      0 0 20px rgba(255, 140, 0, 0.2),
      inset 0 1px 0 rgba(255, 255, 255, 0.3);
  }
  100% {
    box-shadow: 
      0 12px 40px rgba(255, 140, 0, 0.4),
      0 0 30px rgba(255, 140, 0, 0.3),
      inset 0 1px 0 rgba(255, 255, 255, 0.4);
  }
}

@keyframes borderShine {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(100%);
  }
}

.progress-container {
  margin: 20px 0;
  padding: 20px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  text-align: center;
  border: 1px solid rgba(255, 140, 0, 0.2);
}

.progress-bar {
  height: 24px;
  background: rgba(0, 0, 0, 0.3);
  border-radius: 12px;
  overflow: hidden;
  margin-bottom: 15px;
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.2);
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, rgb(234, 88, 12), rgb(255, 140, 0));
  border-radius: 12px;
  transition: width 0.3s ease;
  box-shadow: 0 2px 8px rgba(234, 88, 12, 0.4);
  animation: progressGlow 2s infinite alternate;
}

@keyframes progressGlow {
  0% {
    box-shadow: 0 2px 8px rgba(234, 88, 12, 0.4);
  }
  100% {
    box-shadow: 0 2px 15px rgba(234, 88, 12, 0.6);
  }
}

.progress-text, .data-info {
  margin: 8px 0;
  font-size: 14px;
  color: rgba(255, 255, 255, 0.9);
  font-weight: 500;
}

</style> 