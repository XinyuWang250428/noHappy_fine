<template>
  <section class="py-24 sm:py-32">
    <div class="container">
      <div class="header">
        <h1>心电筛查</h1>
      </div>
      
      <div class="control-panel">
        <div class="file-input-wrapper">
          <label class="custom-file-input">
            <span>选择文件</span>
            <input type="file" class="hidden" @change="handleFileChange" accept=".txt">
          </label>
          <div class="file-name">{{ fileName }}</div>
          <div class="file-tip">请选择txt格式的心电数据文件</div>
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
      <button class="return-button" v-show="showResultDisplay" @click="returnToMain">返回主页</button>
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

const parseECGData = (content: string) => {
  const lines = content.split('\n')
  let isDataSection = false
  
  patientData.channel1 = []
  patientData.channel2 = []
  
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

  if (patientData.channel1.length > 0) {
    displayECGWave('channel1', patientData.channel1)
    displayECGWave('channel2', patientData.channel2)
  } else {
    alert('未找到有效的心电数据')
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

const returnToMain = () => {
  localStorage.setItem('lastScreen', 'screening')
  const element = document.getElementById('population-risk')
  if (element) {
    element.scrollIntoView({ behavior: 'smooth' })
  }
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
  font-size: 2.5em;
  margin-bottom: 10px;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
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
  width: 200px;
  margin: 0 auto;
  padding: 15px 30px;
  background: linear-gradient(45deg, #4CAF50, #45a049);
  color: white;
  border: none;
  border-radius: 25px;
  font-size: 1.2em;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
}

.result-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.result-display {
  margin-top: 20px;
  padding: 20px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 10px;
  text-align: center;
  font-size: 1.2em;
}

.return-button {
  width: 200px;
  margin: 20px auto 0;
  padding: 12px 25px;
  background: linear-gradient(45deg, #9c27b0, #6a1b9a);
  color: white;
  border: none;
  border-radius: 25px;
  font-size: 1.1em;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
}

.return-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}
</style> 