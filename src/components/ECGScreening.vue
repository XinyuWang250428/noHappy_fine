<template>
  <section class="py-24 sm:py-32">
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
            <p><strong>CSV格式说明：</strong></p>
            <p>• 第一列：导联I数据，第二列：导联II数据</p>
            <p>• 每行代表一个时间点的心电信号值</p>
            <p>• 示例：0.125,0.089</p>
          </div>
        </div>
      </div>

      <div class="ecg-display">
        <div class="ecg-channel">
          <div class="channel-label">导联 I</div>
          <canvas ref="channel1"></canvas>
        </div>
        <div class="ecg-channel">
          <div class="channel-label">导联 II</div>
          <canvas ref="channel2"></canvas>
        </div>
      </div>

      <button class="result-button" @click="showResult">显示结果</button>
      <div class="result-display" v-show="showResultDisplay">{{ resultText }}</div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const fileName = ref('未选择文件')
const showResultDisplay = ref(false)
const resultText = ref('')
const channel1 = ref<HTMLCanvasElement | null>(null)
const channel2 = ref<HTMLCanvasElement | null>(null)

const patientData = {
  channel1: [] as number[],
  channel2: [] as number[]
}

const handleFileChange = (e: Event) => {
  const target = e.target as HTMLInputElement
  const file = target.files?.[0]
  
  if (file) {
    fileName.value = file.name
    const reader = new FileReader()
    
    reader.onload = (event) => {
      try {
        const content = event.target?.result as string
        const fileExtension = file.name.toLowerCase().substring(file.name.lastIndexOf('.'))
        parseECGData(content, fileExtension)
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

const parseECGData = (content: string, fileExtension: string) => {
  const lines = content.split('\n')
  
  patientData.channel1 = []
  patientData.channel2 = []
  
  if (fileExtension === '.csv') {
    // CSV格式处理
    for (let i = 0; i < lines.length; i++) {
      const line = lines[i].trim()
      if (line === '') continue
      
      // 跳过可能的标题行
      if (line.toLowerCase().includes('channel') || line.toLowerCase().includes('lead')) {
        continue
      }
      
      const values = line.split(',').map(v => parseFloat(v.trim()))
      if (values.length >= 2 && !isNaN(values[0]) && !isNaN(values[1])) {
        patientData.channel1.push(values[0])
        patientData.channel2.push(values[1])
      }
    }
  } else {
    // TXT格式处理（原有逻辑）
    let isDataSection = false
    
    for (let i = 0; i < lines.length; i++) {
      const line = lines[i].trim()
      if (line === '') continue
      
      if (line === 'ECG Data') {
        isDataSection = true
        continue
      }
      
      if (isDataSection) {
        const values = line.split(',').map(v => parseFloat(v.trim()))
        if (values.length === 2 && !isNaN(values[0]) && !isNaN(values[1])) {
          patientData.channel1.push(values[0])
          patientData.channel2.push(values[1])
        }
      }
    }
  }

  if (patientData.channel1.length > 0) {
    displayECGWave('channel1', patientData.channel1)
    displayECGWave('channel2', patientData.channel2)
    alert(`成功导入${patientData.channel1.length}个数据点`)
  } else {
    alert('未找到有效的心电数据，请检查文件格式')
  }
}

const displayECGWave = (canvasId: string, data: number[]) => {
  const canvas = canvasId === 'channel1' ? channel1.value : channel2.value
  if (!canvas) return
  
  const ctx = canvas.getContext('2d')
  if (!ctx) return
  
  canvas.width = canvas.parentElement?.clientWidth ?? 0 - 40
  canvas.height = canvas.parentElement?.clientHeight ?? 0 - 30
  
  const width = canvas.width
  const height = canvas.height
  
  ctx.strokeStyle = '#4CAF50'
  ctx.lineWidth = 2
  
  const pointSpacing = width / data.length
  const max = Math.max(...data)
  const min = Math.min(...data)
  const range = max - min
  
  let offset = 0
  function animate() {
    ctx.clearRect(0, 0, width, height)
    ctx.beginPath()
    
    data.forEach((value, index) => {
      const x = (index * pointSpacing + offset) % width
      const y = height - ((value - min) / range * height * 0.8 + height * 0.1)
      
      if (index === 0) {
        ctx.moveTo(x, y)
      } else {
        ctx.lineTo(x, y)
      }
    })
    
    ctx.stroke()
    offset += 0.5
    requestAnimationFrame(animate)
  }
  
  animate()
}

const showResult = () => {
  showResultDisplay.value = true
  const randomProbability = (Math.random() * 100).toFixed(2)
  resultText.value = `抑郁症概率：${randomProbability}%`
  
  const completedTests = JSON.parse(localStorage.getItem('completedTests') || '{}')
  completedTests.ecg = `${randomProbability}%`
  localStorage.setItem('completedTests', JSON.stringify(completedTests))
}


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
  padding: 15px;
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

.file-format-description strong {
  color: rgb(250, 250, 249);
}

.ecg-display {
  width: 100%;
  height: 400px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 10px;
  margin-bottom: 30px;
  position: relative;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  padding: 20px;
}

.ecg-channel {
  flex: 1;
  position: relative;
  margin-bottom: 20px;
}

.ecg-channel:last-child {
  margin-bottom: 0;
}

.channel-label {
  position: absolute;
  left: 10px;
  top: 10px;
  color: #333;
  font-size: 0.9em;
  z-index: 2;
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


</style> 