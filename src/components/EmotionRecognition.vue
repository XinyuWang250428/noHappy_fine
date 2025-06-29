<template>
  <section id="emotion" class="py-24 sm:py-32">
    <div class="emotion-container">
      <div class="mb-10 text-center">
        <h2 class="text-3xl font-bold tracking-tight sm:text-4xl mb-4">
          情绪识别评估
        </h2>
        <p class="text-lg text-gray-600 max-w-none mx-auto whitespace-nowrap">
          基于人工智能的面部表情分析技术，通过实时捕捉和分析用户的面部表情变化，评估情绪表达能力和情绪调节能力
        </p>
      </div>

      <!-- 当前状态显示 -->
      <div class="flex justify-center mb-8">
        <div class="bg-white/90 rounded-full px-6 py-3 shadow-lg border border-orange-200 min-w-[400px] text-center">
          <div class="flex items-center justify-center gap-3">
            <span class="text-lg font-bold text-orange-800">系统状态:</span>
            <div class="w-3 h-3 rounded-full" :class="systemStatus.color"></div>
            <span class="text-gray-700 font-medium">{{ systemStatus.text }}</span>
          </div>
        </div>
      </div>



      <!-- 情绪识别测试阶段 -->
      <div v-if="currentStage === 'testing'" class="max-w-5xl mx-auto">

        <!-- 情绪提示区域和视频组件融合 -->
        <div class="flex justify-center mb-6">
          <div class="relative w-[720px] bg-white rounded-2xl overflow-hidden shadow-xl border-3 border-orange-300">
            <!-- 情绪提示区域 -->
            <div class="bg-gradient-to-br from-orange-100 via-orange-50 to-orange-200 p-4 text-center border-b-2 border-orange-300 relative overflow-hidden">
              <!-- 装饰背景元素 -->
              <div class="absolute top-0 left-0 w-16 h-16 bg-orange-300/20 rounded-full -translate-x-8 -translate-y-8"></div>
              <div class="absolute bottom-0 right-0 w-12 h-12 bg-orange-400/20 rounded-full translate-x-6 translate-y-6"></div>
              
              <!-- 文字内容 -->
              <div class="relative z-10 flex items-center justify-center gap-3">
                <div class="w-2 h-2 bg-orange-500 rounded-full animate-pulse"></div>
                <span class="text-xl font-bold text-orange-900 drop-shadow-sm">{{ currentPrompt }}</span>
                <div class="w-2 h-2 bg-orange-500 rounded-full animate-pulse animation-delay-300"></div>
              </div>
              
              <!-- 倒计时显示 -->
              <div v-if="countdown > 0" class="absolute top-2 right-4 bg-white/90 rounded-lg px-3 py-1 shadow-md">
                <span class="text-lg font-bold text-red-600">{{ countdown }}</span>
              </div>
            </div>
            
            <!-- 视频区域 -->
            <div class="relative w-full h-[540px] bg-white overflow-hidden">
            <video 
              ref="videoElement"
              class="w-full h-full object-cover"
              autoplay 
              muted 
              playsinline
            ></video>
            <canvas 
              ref="canvasElement"
              class="absolute top-0 left-0 pointer-events-none"
              width="720" 
              height="540"
            ></canvas>
            </div>
          </div>
        </div>

        <!-- 控制按钮 -->
        <div class="flex gap-5 justify-center items-center flex-wrap mb-6">
          <button 
            @click="startAssessment"
            :disabled="!isSystemReady || isAssessmentRunning"
            class="bg-orange-500 text-white px-8 py-3 rounded-full text-lg font-bold 
                   hover:bg-orange-600 transform hover:-translate-y-1 transition-all duration-300 
                   shadow-lg hover:shadow-xl disabled:bg-gray-400 disabled:cursor-not-allowed disabled:transform-none"
          >
            {{ isAssessmentRunning ? '评估进行中...' : '开始评估' }}
          </button>
          <button 
            @click="stopAssessment"
            :disabled="!isAssessmentRunning"
            class="bg-white text-orange-500 border-2 border-orange-500 px-8 py-3 rounded-full text-lg font-bold 
                   hover:bg-orange-50 transition-all duration-300 disabled:opacity-50 disabled:cursor-not-allowed"
          >
            停止评估
          </button>
          <button 
            v-if="assessmentResults"
            @click="viewReport"
            class="bg-blue-500 text-white px-8 py-3 rounded-full text-lg font-bold 
                   hover:bg-blue-600 transform hover:-translate-y-1 transition-all duration-300 shadow-lg hover:shadow-xl"
          >
            查看报告
          </button>
        </div>

        <!-- 实时情绪统计 -->
        <div v-if="currentEmotions" class="bg-white rounded-xl p-6 shadow-lg max-w-2xl mx-auto">
          <h4 class="text-lg font-bold text-gray-800 mb-4 text-center">实时情绪识别</h4>
          <div class="space-y-3">
            <!-- 第一行：开心、悲伤、愤怒、惊讶 -->
            <div class="grid grid-cols-4 gap-3 mb-3">
              <div v-for="emotion in emotionList.slice(0, 4)" :key="emotion.name" class="relative">
                <div class="flex justify-between items-center mb-1">
                  <span class="text-sm font-medium text-gray-700">{{ emotion.name }}</span>
                  <span class="text-sm text-gray-600">{{ Math.round(emotion.value * 100) }}%</span>
                </div>
                <div class="emotion-bar">
                  <div 
                    class="emotion-bar-fill"
                    :style="{ width: `${emotion.value * 100}%` }"
                  ></div>
                </div>
              </div>
            </div>
            <!-- 第二行：平静 -->
            <div class="relative">
              <div class="flex justify-between items-center mb-1">
                <span class="text-sm font-medium text-gray-700">{{ emotionList[4].name }}</span>
                <span class="text-sm text-gray-600">{{ Math.round(emotionList[4].value * 100) }}%</span>
              </div>
              <div class="emotion-bar">
                <div 
                  class="emotion-bar-fill"
                  :style="{ width: `${emotionList[4].value * 100}%` }"
                ></div>
              </div>
            </div>
          </div>
        </div>

        <!-- 测试提示 -->
        <div class="bg-gradient-to-r from-orange-100 to-orange-200 rounded-xl p-6 mt-6 shadow-lg border-2 border-orange-300 max-w-2xl mx-auto text-center relative overflow-hidden">
          <!-- 装饰背景 -->
          <div class="absolute top-0 right-0 w-24 h-24 bg-orange-300/20 rounded-full -translate-y-8 translate-x-8"></div>
          <div class="absolute bottom-0 left-0 w-16 h-16 bg-orange-400/20 rounded-full translate-y-4 -translate-x-4"></div>
          
          <!-- 图标装饰 -->
          <div class="flex items-center justify-center mb-3">
            <div class="bg-orange-500 text-white rounded-full p-2 mr-3 shadow-md">
              💡
            </div>
            <h4 class="text-lg font-bold text-orange-800">测试提示</h4>
          </div>
          
          <p class="text-orange-900 text-sm leading-relaxed relative z-10">
            请确保<span class="font-semibold text-orange-700">面部清晰可见，光线充足</span>，按照屏幕提示表达相应情绪。<br>
            每个情绪会有<span class="font-semibold text-orange-700">5秒</span>的表达时间，系统将自动记录您的表现。
          </p>
          
          <!-- 底部装饰条 -->
          <div class="absolute bottom-0 left-0 right-0 h-1 bg-gradient-to-r from-orange-400 to-orange-600"></div>
        </div>
      </div>



      <!-- 加载覆盖层 -->
      <div v-if="showLoadingOverlay" class="fixed inset-0 bg-white/90 flex flex-col justify-center items-center z-50">
        <div class="w-12 h-12 border-4 border-orange-500 border-t-transparent rounded-full animate-spin mb-4"></div>
        <p class="text-orange-800 text-lg font-medium">{{ loadingMessage }}</p>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted, onUnmounted, nextTick } from 'vue'

// 组件状态
const currentStage = ref<'consent' | 'testing' | 'report'>('testing')
const isSystemReady = ref(false)
const isAssessmentRunning = ref(false)
const showLoadingOverlay = ref(false)
const loadingMessage = ref('正在初始化系统...')
const aiAnalysisLoading = ref(false)

// 视频和画布引用
const videoElement = ref<HTMLVideoElement>()
const canvasElement = ref<HTMLCanvasElement>()

// 系统状态
const systemStatus = reactive({
  text: '正在初始化...',
  color: 'bg-yellow-400'
})

// 评估相关数据
const currentPrompt = ref('准备开始...')
const countdown = ref(0)
const currentEmotions = ref<any>(null)
const assessmentResults = ref<any>(null)

// 情绪列表
const emotionList = ref([
  { name: '开心', value: 0 },
  { name: '悲伤', value: 0 },
  { name: '愤怒', value: 0 },
  { name: '惊讶', value: 0 },
  { name: '平静', value: 0 }
])

// AI分析结果
const aiAnalysis = reactive({
  summary: '',
  strengths: '',
  suggestions: ''
})

// Face-API 相关变量
let faceApi: any = null
let stream: MediaStream | null = null
let detectionInterval: number | null = null
let emotionSequence: string[] = []
let currentEmotionIndex = 0
let emotionResults: Record<string, any> = {}
let countdownInterval: number | null = null

// 情绪序列定义
const emotions = ['happy', 'sad', 'angry', 'surprised', 'neutral']
const emotionPrompts: Record<string, string> = {
  'happy': '请表现出开心的表情 😊',
  'sad': '请表现出悲伤的表情 😢',
  'angry': '请表现出愤怒的表情 😠',
  'surprised': '请表现出惊讶的表情 😮',
  'neutral': '请保持平静自然的表情 😐'
}

// 组件挂载时初始化
onMounted(async () => {
  await initializeSystem()
})

// 组件卸载时清理
onUnmounted(() => {
  cleanup()
})

// 初始化系统
async function initializeSystem() {
  try {
    showLoadingOverlay.value = true
    loadingMessage.value = '正在加载Face-API模型...'
    
    // 加载Face-API
    const script = document.createElement('script')
    script.src = 'https://cdn.jsdelivr.net/npm/@vladmandic/face-api@1.7.12/dist/face-api.js'
    script.onload = async () => {
      await loadFaceApiModels()
    }
    document.head.appendChild(script)
  } catch (error) {
    console.error('系统初始化失败:', error)
    systemStatus.text = '初始化失败'
    systemStatus.color = 'bg-red-400'
    showLoadingOverlay.value = false
  }
}

// 加载Face-API模型
async function loadFaceApiModels() {
  try {
    // @ts-ignore
    faceApi = window.faceapi
    
    if (!faceApi) {
      throw new Error('Face-API未加载')
    }

    loadingMessage.value = '正在加载人脸检测模型...'
    
    // 加载模型
    await Promise.all([
      faceApi.nets.tinyFaceDetector.loadFromUri('/models'),
      faceApi.nets.faceLandmark68Net.loadFromUri('/models'),
      faceApi.nets.faceExpressionNet.loadFromUri('/models')
    ])

    loadingMessage.value = '正在初始化摄像头...'
    await initializeCamera()
    
      systemStatus.text = '系统就绪'
  systemStatus.color = 'bg-orange-400'
    isSystemReady.value = true
    showLoadingOverlay.value = false
  } catch (error) {
    console.error('模型加载失败:', error)
    systemStatus.text = '模型加载失败'
    systemStatus.color = 'bg-red-400'
    showLoadingOverlay.value = false
  }
}

// 初始化摄像头
async function initializeCamera() {
  try {
    stream = await navigator.mediaDevices.getUserMedia({
      video: { width: 720, height: 540 }
    })
    
    if (videoElement.value) {
      videoElement.value.srcObject = stream
      await videoElement.value.play()
    }
  } catch (error) {
    console.error('摄像头初始化失败:', error)
    throw error
  }
}



// 开始评估
async function startAssessment() {
  if (!isSystemReady.value || !faceApi) {
    alert('系统未就绪，请稍后再试')
    return
  }

  isAssessmentRunning.value = true
  emotionSequence = [...emotions].sort(() => Math.random() - 0.5) // 随机排序
  currentEmotionIndex = 0
  emotionResults = {}
  
  await nextTick()
  startEmotionDetection()
  runEmotionSequence()
}

// 停止评估
function stopAssessment() {
  isAssessmentRunning.value = false
  if (detectionInterval) {
    clearInterval(detectionInterval)
    detectionInterval = null
  }
  if (countdownInterval) {
    clearInterval(countdownInterval)
    countdownInterval = null
  }
  currentPrompt.value = '评估已停止'
  countdown.value = 0
}

// 开始情绪检测
function startEmotionDetection() {
  if (!videoElement.value || !canvasElement.value || !faceApi) return

  const canvas = canvasElement.value
  const ctx = canvas.getContext('2d')
  if (!ctx) return

  detectionInterval = window.setInterval(async () => {
    try {
      const detections = await faceApi
        .detectAllFaces(videoElement.value, new faceApi.TinyFaceDetectorOptions({ inputSize: 224, scoreThreshold: 0.5 }))
        .withFaceLandmarks()
        .withFaceExpressions()

      // 清除画布
      ctx.clearRect(0, 0, canvas.width, canvas.height)

      if (detections.length > 0) {
        const detection = detections[0]
        
        // 设置画布显示尺寸
        const displaySize = { width: videoElement.value.videoWidth, height: videoElement.value.videoHeight }
        const resizedDetections = faceApi.resizeResults(detections, displaySize)
        
        // 绘制检测结果：人脸框、特征点、表情置信度
        faceApi.draw.drawDetections(canvas, resizedDetections)
        faceApi.draw.drawFaceLandmarks(canvas, resizedDetections)
        faceApi.draw.drawFaceExpressions(canvas, resizedDetections)

        // 更新情绪数据
        const expressions = detection.expressions
        currentEmotions.value = expressions
        updateEmotionList(expressions)

        // 记录当前情绪数据（如果正在测试特定情绪）
        if (isAssessmentRunning.value && currentEmotionIndex < emotionSequence.length) {
          const currentEmotion = emotionSequence[currentEmotionIndex]
          if (!emotionResults[currentEmotion]) {
            emotionResults[currentEmotion] = {
              maxConfidence: 0,
              timeToConfidence: 5000,
              confidenceHistory: [],
              timestamps: []
            }
          }

          const confidence = expressions[currentEmotion] || 0
          emotionResults[currentEmotion].confidenceHistory.push(confidence)
          emotionResults[currentEmotion].timestamps.push(Date.now())
          
          if (confidence > emotionResults[currentEmotion].maxConfidence) {
            emotionResults[currentEmotion].maxConfidence = confidence
          }

          // 检查是否达到置信度阈值
          if (confidence >= 0.6 && emotionResults[currentEmotion].timeToConfidence === 5000) {
            const startTime = emotionResults[currentEmotion].timestamps[0]
            emotionResults[currentEmotion].timeToConfidence = Date.now() - startTime
          }
        }
      }
    } catch (error) {
      console.error('检测错误:', error)
    }
  }, 100)
}

// 更新情绪列表显示
function updateEmotionList(expressions: any) {
  const emotionMap: Record<string, string> = {
    'happy': '开心',
    'sad': '悲伤',
    'angry': '愤怒',
    'surprised': '惊讶',
    'neutral': '平静'
  }

  emotionList.value = Object.entries(emotionMap).map(([key, name]) => ({
    name,
    value: expressions[key] || 0
  }))
}

// 运行情绪序列
function runEmotionSequence() {
  if (currentEmotionIndex >= emotionSequence.length) {
    // 评估完成
    completeAssessment()
    return
  }

  const currentEmotion = emotionSequence[currentEmotionIndex]
  currentPrompt.value = emotionPrompts[currentEmotion]
  
  // 初始化结果记录
  emotionResults[currentEmotion] = {
    maxConfidence: 0,
    timeToConfidence: 5000,
    confidenceHistory: [],
    timestamps: [Date.now()]
  }

  // 开始倒计时
  countdown.value = 5
  countdownInterval = window.setInterval(() => {
    countdown.value--
    if (countdown.value <= 0) {
      clearInterval(countdownInterval!)
      currentEmotionIndex++
      setTimeout(() => runEmotionSequence(), 1000) // 1秒间隔后下一个情绪
    }
  }, 1000)
}

// 完成评估
function completeAssessment() {
  isAssessmentRunning.value = false
  if (detectionInterval) {
    clearInterval(detectionInterval)
    detectionInterval = null
  }
  
  assessmentResults.value = emotionResults
  localStorage.setItem('emotionAssessmentData', JSON.stringify(emotionResults))
  
  currentPrompt.value = '评估已完成！'
  countdown.value = 0
  
  // 生成AI分析
  generateAIAnalysis()
}

// 生成AI分析
function generateAIAnalysis() {
  aiAnalysisLoading.value = true
  
  // 模拟AI分析过程
  setTimeout(() => {
    const results = assessmentResults.value
    
    // 计算平均置信度
    const avgConfidence = Object.values(results).reduce((sum: number, emotion: any) => sum + emotion.maxConfidence, 0) / Object.keys(results).length
    
    // 计算平均响应时间
    const avgResponseTime = Object.values(results).reduce((sum: number, emotion: any) => sum + emotion.timeToConfidence, 0) / Object.keys(results).length
    
    // 生成分析结果
    if (avgConfidence > 0.7) {
      aiAnalysis.summary = '您的情绪表达能力表现优秀，能够清晰准确地传达各种情绪状态。'
      aiAnalysis.strengths = '情绪表达清晰度高，面部表情变化明显，情绪识别准确率较高。'
    } else if (avgConfidence > 0.5) {
      aiAnalysis.summary = '您的情绪表达能力处于中等水平，大部分情绪能够较好地表达。'
      aiAnalysis.strengths = '基本情绪表达良好，在某些情绪类型上表现突出。'
    } else {
      aiAnalysis.summary = '您的情绪表达相对较为内敛，建议通过练习来提升表达的清晰度。'
      aiAnalysis.strengths = '情绪控制能力较强，表达方式较为含蓄。'
    }
    
    if (avgResponseTime > 3000) {
      aiAnalysis.suggestions = '建议多进行情绪表达练习，提高情绪转换的速度和流畅度。可以尝试在镜子前练习不同的表情。'
    } else {
      aiAnalysis.suggestions = '继续保持良好的情绪表达习惯，可以尝试挑战更复杂的情绪表达组合。'
    }
    
    aiAnalysisLoading.value = false
  }, 2000)
}

// 查看报告
function viewReport() {
  // 直接滚动到综合评估报告部分
  const reportElement = document.querySelector('#testimonials')
  if (reportElement) {
    reportElement.scrollIntoView({ behavior: 'smooth' })
  }
}



// 清理资源
function cleanup() {
  if (stream) {
    stream.getTracks().forEach(track => track.stop())
  }
  if (detectionInterval) {
    clearInterval(detectionInterval)
  }
  if (countdownInterval) {
    clearInterval(countdownInterval)
  }
}
</script>

<style scoped>
.emotion-container {
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

.emotion-container::before {
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

.animation-delay-300 {
  animation-delay: 300ms;
}

/* 情绪进度条样式 */
.emotion-bar {
  background: #f5f5f5;
  border-radius: 10px;
  overflow: hidden;
  height: 30px;
  position: relative;
  min-width: 120px;
}

.emotion-bar-fill {
  height: 100%;
  background: linear-gradient(45deg, #f97316, #fb923c);
  width: 0%;
  transition: width 0.3s ease;
}

.emotion-label {
  position: absolute;
  left: 10px;
  top: 50%;
  transform: translateY(-50%);
  color: #333;
  font-size: 0.9rem;
  z-index: 1;
  font-weight: 500;
}

.emotion-value {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  color: #333;
  font-size: 0.9rem;
  z-index: 1;
  font-weight: 500;
}

/* 标题样式调整 */
.emotion-container h2 {
  color: rgb(250, 250, 249) !important;
  font-size: 36px;
  font-weight: 700;
  letter-spacing: -0.025em;
}

.emotion-container p {
  color: rgba(255, 255, 255, 0.9) !important;
}

/* 状态显示卡片 */
.emotion-container .bg-white\/90 {
  background: rgba(255, 255, 255, 0.1) !important;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2) !important;
}

.emotion-container .bg-white\/90 h3 {
  color: rgba(168, 162, 158, 1) !important;
}

.emotion-container .bg-white\/90 span {
  color: rgba(255, 255, 255, 0.9) !important;
}

/* 测试界面标题 */
.emotion-container h3 {
  color: rgb(250, 250, 249) !important;
}

/* 提示区域 */
.emotion-container .bg-white\/90.rounded-xl {
  background: rgba(255, 255, 255, 0.9) !important;
}

.emotion-container .bg-white\/80 {
  background: rgba(255, 255, 255, 0.8) !important;
}

/* 按钮样式调整 */
.emotion-container .bg-green-500 {
  background-color: rgb(234, 88, 12) !important;
}

.emotion-container .bg-green-500:hover {
  background-color: rgba(234, 88, 12, 0.9) !important;
}

.emotion-container .bg-blue-500 {
  background-color: rgb(234, 88, 12) !important;
}

.emotion-container .bg-blue-500:hover {
  background-color: rgba(234, 88, 12, 0.9) !important;
}

/* 自定义样式 */
.border-3 {
  border-width: 3px;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .video-container {
    width: 100% !important;
    height: auto !important;
    aspect-ratio: 4 / 3;
  }
  
  .emotion-container {
    padding: 15px;
  }
}
</style> 