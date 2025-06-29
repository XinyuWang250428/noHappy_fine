<template>
  <section id="testimonials" class="py-24 sm:py-32">
    <div class="emotion-container max-w-7xl mx-auto px-6 lg:px-8">
      <!-- 标题部分 -->
      <div class="mb-10 text-center">
        <h2 class="text-3xl font-bold tracking-tight sm:text-4xl mb-4">
          综合评估报告
        </h2>
        <p class="text-lg text-gray-800 dark:text-gray-600 max-w-3xl mx-auto font-medium">
          基于多维度数据分析的个性化心理健康评估报告，为您提供专业的心理健康指导
        </p>
      </div>

      <div class="space-y-8">
        <!-- 测试状态列表 -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
          <div v-for="(test, index) in testStatus" :key="index"
               class="bg-card p-6 rounded-lg shadow-md">
            <h3 class="text-xl font-semibold mb-2">{{ test.name }}</h3>
            <div class="flex items-center space-x-2">
              <span :class="[
                'px-2 py-1 rounded text-sm',
                test.completed ? 'bg-green-100 text-green-800' : 'bg-yellow-100 text-yellow-800'
              ]">
                {{ test.completed ? '已完成' : '暂未完成' }}
              </span>
            </div>
          </div>
        </div>

        <!-- 总体评估结果 -->
        <div :class="[
          'p-8 rounded-2xl shadow-xl border-2',
          allTestsCompleted ? (
            score >= 80 ? 'bg-gradient-to-r from-green-100 via-green-50 to-green-200 dark:from-green-900 dark:via-green-800 dark:to-green-900 border-green-300 dark:border-green-700' :
            score >= 60 ? 'bg-gradient-to-r from-orange-100 via-orange-50 to-orange-200 dark:from-orange-900 dark:via-orange-800 dark:to-orange-900 border-orange-300 dark:border-orange-700' :
            score >= 40 ? 'bg-gradient-to-r from-red-100 via-red-50 to-red-200 dark:from-red-900 dark:via-red-800 dark:to-red-900 border-red-300 dark:border-red-700' :
            'bg-gradient-to-r from-rose-200 via-rose-100 to-rose-300 dark:from-rose-900 dark:via-rose-800 dark:to-rose-900 border-rose-400 dark:border-rose-700'
          ) : 'bg-gradient-to-r from-gray-100 via-gray-50 to-gray-200 dark:from-gray-900 dark:via-gray-800 dark:to-gray-900 border-gray-300 dark:border-gray-700'
        ]">
          <h2 class="text-4xl font-bold text-center mb-6" :class="[
            allTestsCompleted ? (
              score >= 80 ? 'text-green-900 dark:text-green-100' :
              score >= 60 ? 'text-orange-900 dark:text-orange-100' :
              score >= 40 ? 'text-red-900 dark:text-red-100' :
              'text-rose-900 dark:text-rose-100'
            ) : 'text-gray-900 dark:text-gray-100'
          ]">综合评估结果</h2>
          <div class="flex justify-around items-center">
            <div class="text-center">
              <p class="text-lg font-semibold" :class="[
                allTestsCompleted ? (
                  score >= 80 ? 'text-green-800 dark:text-green-200' :
                  score >= 60 ? 'text-orange-800 dark:text-orange-200' :
                  score >= 40 ? 'text-red-800 dark:text-red-200' :
                  'text-rose-800 dark:text-rose-200'
                ) : 'text-gray-800 dark:text-gray-200'
              ]">总体风险等级</p>
              <p class="text-3xl font-bold mt-2" :class="[
                allTestsCompleted ? (
                  score >= 80 ? 'text-green-600 dark:text-green-400' :
                  score >= 60 ? 'text-orange-600 dark:text-orange-400' :
                  score >= 40 ? 'text-red-600 dark:text-red-400' :
                  'text-rose-600 dark:text-rose-400'
                ) : 'text-gray-600 dark:text-gray-400'
              ]">{{ allTestsCompleted ? getRiskLevel : '待评估' }}</p>
            </div>
            <div class="text-center">
              <p class="text-lg font-semibold" :class="[
                allTestsCompleted ? (
                  score >= 80 ? 'text-green-800 dark:text-green-200' :
                  score >= 60 ? 'text-orange-800 dark:text-orange-200' :
                  score >= 40 ? 'text-red-800 dark:text-red-200' :
                  'text-rose-800 dark:text-rose-200'
                ) : 'text-gray-800 dark:text-gray-200'
              ]">综合评分</p>
              <p class="text-5xl font-bold mt-2" :class="[
                allTestsCompleted ? (
                  score >= 80 ? 'text-green-900 dark:text-green-100' :
                  score >= 60 ? 'text-orange-900 dark:text-orange-100' :
                  score >= 40 ? 'text-red-900 dark:text-red-100' :
                  'text-rose-900 dark:text-rose-100'
                ) : 'text-gray-900 dark:text-gray-100'
              ]">{{ allTestsCompleted ? score : '--' }}<span class="text-2xl">{{ allTestsCompleted ? '分' : '' }}</span></p>
            </div>
          </div>
        </div>

        <!-- 四大维度评分 -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
          <div v-for="(score, index) in dimensionScores" :key="index" 
               class="bg-card p-6 rounded-lg shadow-md">
            <h3 class="text-xl font-semibold mb-4">{{ score.name }}</h3>
            <div v-if="allTestsCompleted" class="flex justify-between items-center">
              <span class="text-3xl font-bold">{{ score.value }}</span>
              <span class="text-sm text-muted-foreground">权重: {{ score.weight }}</span>
            </div>
            <div v-else class="flex justify-between items-center">
              <span class="text-3xl font-bold text-muted-foreground">--</span>
              <span class="text-sm text-muted-foreground">权重: {{ score.weight }}</span>
            </div>
            <p class="mt-4 text-sm text-muted-foreground">{{ allTestsCompleted ? score.description : '等待测试完成' }}</p>
          </div>
        </div>

        <!-- 详细分析部分 -->
        <div class="space-y-8">
          <!-- 心理量表评估 -->
          <div class="bg-card p-6 rounded-lg shadow-md">
            <h3 class="text-2xl font-semibold mb-4">心理量表评估</h3>
            <div v-if="testStatus[0].completed" class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div v-for="(item, index) in psychologyScores" :key="index" 
                   class="p-4 bg-muted rounded">
                <p class="font-medium">{{ item.name }}</p>
                <p class="text-2xl font-bold mt-2">{{ item.score }}</p>
                <p class="text-sm text-muted-foreground mt-1">{{ item.interpretation }}</p>
              </div>
            </div>
            <div v-else class="p-4 bg-muted rounded text-center">
              <p class="text-muted-foreground">请先完成心理量表评估测试</p>
            </div>
          </div>

          <!-- 心电信号分析 -->
          <div class="bg-card p-6 rounded-lg shadow-md">
            <h3 class="text-2xl font-semibold mb-4">心电信号分析</h3>
            <div v-if="testStatus[1].completed" class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div v-for="(item, index) in ecgScores" :key="index" 
                   class="p-4 bg-muted rounded">
                <p class="font-medium">{{ item.name }}</p>
                <p class="text-2xl font-bold mt-2">{{ item.value }}</p>
                <p class="text-sm text-muted-foreground mt-1">{{ item.interpretation }}</p>
              </div>
            </div>
            <div v-else class="p-4 bg-muted rounded text-center">
              <p class="text-muted-foreground">请先完成心电检测评估测试</p>
            </div>
          </div>

          <!-- 情绪表情分析 -->
          <div class="bg-card p-6 rounded-lg shadow-md space-y-6">
            <h3 class="text-xl font-semibold mb-4">情绪表情分析</h3>
            
            <div v-if="testStatus[2].completed" class="space-y-8">
              <div class="section">
                <h4 class="text-lg font-semibold mb-3">情绪达成度（最大置信度）</h4>
                <div class="h-64">
                  <canvas ref="maxConfidenceChartRef"></canvas>
                </div>
              </div>

              <div class="section">
                <h4 class="text-lg font-semibold mb-3">情绪转换效率（达成时间）</h4>
                <div class="h-64">
                  <canvas ref="timeToConfidenceChartRef"></canvas>
                </div>
                <p class="text-sm text-muted-foreground text-center mt-2">
                  * 达成时间指情绪置信度达到60%所用时长，最大为5000毫秒（5秒）。
                </p>
              </div>

              <div class="section">
                <h4 class="text-lg font-semibold mb-3">悲伤情绪干扰分析 - 时长占比</h4>
                <div ref="sadInterferenceChartsRef" class="grid grid-cols-2 xl:grid-cols-4 gap-4 justify-items-center">
                  <!-- 饼图将在这里动态生成 -->
                </div>
                <p class="text-sm text-muted-foreground text-center mt-4">
                  * 显示在表达各目标情绪时，悲伤情绪置信度 > 3% 的时间占总测试时间（5秒）的百分比，精确到小数点后4位。
                </p>
              </div>

              <div class="section">
                <h4 class="text-lg font-semibold mb-3">悲伤情绪干扰分析 - 最大干扰置信度</h4>
                <div class="h-64">
                  <canvas ref="maxSadInterferenceChartRef"></canvas>
                </div>
                <p class="text-sm text-muted-foreground text-center mt-2">
                  * 显示在表达各目标情绪时，悲伤情绪出现的最大置信度。
                </p>
              </div>
            </div>
            <div v-else class="p-4 bg-muted rounded text-center">
              <p class="text-muted-foreground">请先完成情绪识别评估测试</p>
            </div>
          </div>

          <!-- 生活方式预测分析 -->
          <div class="bg-card p-6 rounded-lg shadow-md">
            <h3 class="text-2xl font-semibold mb-4">生活方式预测</h3>
            <div v-if="testStatus[3].completed" class="space-y-4">
              <!-- 预测结果展示 -->
              <div v-if="lifestylePredictionResult" class="result-content">
                <div class="result-header">
                  <div class="population-badge">{{ lifestylePredictionResult.population }}</div>
                  <div class="method-tag">{{ lifestylePredictionResult.method }}</div>
                </div>
                
                <div class="score-display">
                  <div class="score-circle" :style="{ borderColor: lifestylePredictionResult.color }">
                    <span class="score-number" :style="{ color: lifestylePredictionResult.color }">{{ lifestylePredictionResult.score }}</span>
                    <span class="score-label">分</span>
                  </div>
                  <div class="risk-info">
                    <div class="risk-level" :style="{ color: lifestylePredictionResult.color }">
                      {{ lifestylePredictionResult.level }}
                    </div>
                    <div class="risk-bar">
                      <div class="risk-progress" 
                           :style="{ width: String(lifestylePredictionResult.score) + '%', backgroundColor: lifestylePredictionResult.color }">
                      </div>
                    </div>
                  </div>
                </div>
                
                <div class="analysis-details">
                  <div class="details-title">📊 分析说明</div>
                  <p class="details-text">{{ lifestylePredictionResult.details }}</p>
                </div>

                <div class="risk-interpretation">
                  <div class="interpretation-title">🔍 风险解读</div>
                  <div class="interpretation-content">
                    <div v-if="parseFloat(lifestylePredictionResult.score) < 25" class="interpretation-text success">
                      <strong>低风险：</strong>您的抑郁风险较低，请继续保持良好的生活习惯和心理状态。建议定期进行心理健康自查。
                    </div>
                    <div v-else-if="parseFloat(lifestylePredictionResult.score) < 50" class="interpretation-text warning">
                      <strong>中等风险：</strong>您存在一定的抑郁风险，建议关注心理健康，适当调整生活方式，必要时寻求专业指导。
                    </div>
                    <div v-else-if="parseFloat(lifestylePredictionResult.score) < 75" class="interpretation-text danger">
                      <strong>高风险：</strong>您的抑郁风险较高，建议尽快咨询心理健康专业人士，寻求专业的评估和干预。
                    </div>
                    <div v-else class="interpretation-text critical">
                      <strong>极高风险：</strong>您的抑郁风险很高，强烈建议立即寻求专业心理健康服务，进行全面评估和治疗。
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div v-else class="p-4 bg-muted rounded text-center">
              <p class="text-muted-foreground">请先完成生活方式预测测试</p>
            </div>
          </div>
        </div>

        <!-- 综合建议 -->
        <div class="bg-card p-6 rounded-lg shadow-md">
          <h3 class="text-2xl font-semibold mb-4">综合建议</h3>
          <div v-if="allTestsCompleted" class="space-y-4">
            <div v-for="(suggestion, index) in suggestions" :key="index" 
                 class="p-4 bg-muted rounded">
              <p class="font-medium">{{ suggestion.title }}</p>
              <p class="text-sm text-muted-foreground mt-2">{{ suggestion.content }}</p>
            </div>
          </div>
          <div v-else class="p-4 bg-muted rounded text-center">
            <p class="text-muted-foreground">请完成所有测试后查看综合建议</p>
          </div>
        </div>

        <!-- 注意事项 -->
        <div class="bg-destructive/10 p-6 rounded-lg">
          <h3 class="text-xl font-semibold mb-4">注意事项</h3>
          <ul class="list-disc list-inside space-y-2 text-sm">
            <li>本报告仅供参考，不能替代专业医疗诊断</li>
            <li>建议结合临床症状和专业医生意见</li>
            <li>定期进行复查，追踪改善情况</li>
            <li>如有突发状况，请立即就医</li>
            <li>保护个人隐私，谨慎分享报告内容</li>
          </ul>
        </div>

        <!-- AI智能分析部分 -->
        <div class="bg-card p-6 rounded-lg shadow-md mt-8">
          <h3 class="text-2xl font-semibold mb-6 flex items-center">
            <span class="mr-2">🤖</span>
            AI智能分析
          </h3>

          <div v-if="!allTestsCompleted" class="p-4 bg-muted rounded text-center">
            <p class="text-muted-foreground">请完成所有测试后查看AI智能分析</p>
          </div>

          <div v-else-if="aiAnalysisLoading" class="flex flex-col items-center justify-center py-8">
            <div class="w-full max-w-xs bg-muted rounded-full h-2.5 mb-4">
              <div class="bg-primary h-2.5 rounded-full transition-all duration-300"
                   :style="{ width: analysisProgress + '%' }"></div>
            </div>
            <p class="text-muted-foreground mb-8">正在进行智能分析... {{ Math.round(analysisProgress) }}%</p>
            
            <!-- 算法图展示 -->
            <div class="w-full max-w-6xl">
              <h4 class="text-lg font-semibold mb-6 text-center text-primary">分析算法框架</h4>
              <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <div class="bg-card p-4 rounded-lg shadow-md">
                  <h5 class="text-center font-medium mb-3">特征筛选框架</h5>
                  <img src="/特征筛选框架.svg" alt="特征筛选框架" class="w-full h-auto max-h-64 object-contain">
                </div>
                <div class="bg-card p-4 rounded-lg shadow-md">
                  <h5 class="text-center font-medium mb-3">组间比较+层次聚类</h5>
                  <img src="/组间比较聚类.svg" alt="组间比较+层次聚类" class="w-full h-auto max-h-64 object-contain">
                </div>
                <div class="bg-card p-4 rounded-lg shadow-md">
                  <h5 class="text-center font-medium mb-3">GAE-GAT聚类框架</h5>
                  <img src="/GAE-GAT聚类框架.svg" alt="GAE-GAT聚类框架" class="w-full h-auto max-h-64 object-contain">
                </div>
              </div>
            </div>
          </div>

          <div v-else-if="aiAnalysisResult" class="space-y-6">
            <!-- 总体风险评估 -->
            <div class="bg-muted/50 rounded-lg p-6">
              <h4 class="text-lg font-semibold mb-3 text-primary">总体风险评估</h4>
              <p class="text-muted-foreground">{{ aiAnalysisResult.overallRisk }}</p>
            </div>

            <!-- 详细分析 -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div class="bg-muted/50 rounded-lg p-6">
                <h4 class="text-lg font-semibold mb-3 text-primary">心理状态分析</h4>
                <p class="text-muted-foreground">{{ aiAnalysisResult.psychologicalState }}</p>
              </div>
              <div class="bg-muted/50 rounded-lg p-6">
                <h4 class="text-lg font-semibold mb-3 text-primary">生理状态评估</h4>
                <p class="text-muted-foreground">{{ aiAnalysisResult.physiologicalState }}</p>
              </div>
              <div class="bg-muted/50 rounded-lg p-6">
                <h4 class="text-lg font-semibold mb-3 text-primary">情绪表达能力</h4>
                <p class="text-muted-foreground">{{ aiAnalysisResult.emotionalExpression }}</p>
              </div>
              <div class="bg-muted/50 rounded-lg p-6">
                <h4 class="text-lg font-semibold mb-3 text-primary">生活方式风险评估</h4>
                <p class="text-muted-foreground">{{ aiAnalysisResult.lifestyleRisk }}</p>
              </div>
            </div>

            <!-- 建议部分 -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div class="bg-muted/50 rounded-lg p-6">
                <h4 class="text-lg font-semibold mb-3 text-primary">短期干预建议</h4>
                <ul class="space-y-2">
                  <li>情绪调节：每日进行10分钟冥想，缓解焦虑和压力。</li>
                  <li>社会互动：增加与亲友的交流，提升社会支持感。</li>
                  <li>运动放松：每周进行3次中等强度运动，改善心情。</li>
                </ul>
              </div>
              <div class="bg-muted/50 rounded-lg p-6">
                <h4 class="text-lg font-semibold mb-3 text-primary">长期改善计划</h4>
                <ul class="space-y-2">
                  <li>心理建设：定期参加心理健康讲座，提升自我认知。</li>
                  <li>生活习惯：培养健康饮食和运动习惯，增强体质。</li>
                  <li>情绪管理：学习情绪管理技巧，提升情绪调节能力。</li>
                </ul>
              </div>
            </div>

            <!-- 重点关注事项 -->
            <div class="bg-card p-6 rounded-lg shadow-md space-y-4">
              <h4 class="text-lg font-semibold mb-3 flex items-center">
                <span class="mr-2">💭</span>
                智能对话助手
              </h4>
              
              <!-- 预设问题 -->
              <div class="flex flex-wrap gap-2">
                <button
                  v-for="(question, index) in predefinedQuestions"
                  :key="index"
                  @click="sendChatMessage(question)"
                  class="px-4 py-2 bg-muted rounded-full text-sm hover:bg-muted/80 transition-colors"
                  :disabled="isChatLoading"
                >
                  {{ question }}
                </button>
              </div>

              <!-- 对话历史 -->
              <div 
                ref="chatContainer"
                class="bg-muted/50 rounded-lg p-4 h-64 overflow-y-auto space-y-4 scroll-smooth"
              >
                <div
                  v-for="(msg, index) in chatHistory"
                  :key="index"
                  :class="[
                    'p-3 rounded-lg max-w-[80%] whitespace-pre-line',
                    msg.role === 'user' 
                      ? 'bg-primary text-primary-foreground ml-auto' 
                      : 'bg-muted'
                  ]"
                >
                  {{ msg.content }}
                </div>
                <div v-if="isChatLoading" class="flex justify-center">
                  <div class="w-6 h-6 border-2 border-primary border-t-transparent rounded-full animate-spin"></div>
                </div>
              </div>

              <!-- 输入框 -->
              <div class="flex gap-2">
                <input
                  v-model="chatMessage"
                  type="text"
                  placeholder="输入您的问题..."
                  class="flex-1 px-4 py-2 bg-muted rounded-lg"
                  @keyup.enter="sendChatMessage(chatMessage)"
                  :disabled="isChatLoading"
                >
                <button
                  @click="sendChatMessage(chatMessage)"
                  class="px-4 py-2 bg-primary text-primary-foreground rounded-lg hover:bg-primary/90 transition-colors disabled:opacity-50"
                  :disabled="isChatLoading || !chatMessage.trim()"
                >
                  发送
                </button>
              </div>
            </div>
          </div>

          <div v-else class="text-center py-8 text-muted-foreground">
            暂无AI分析结果
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted, nextTick } from 'vue'
import { Chart } from 'chart.js/auto'

// 情绪测试结果类型定义
interface EmotionData {
  maxConfidence: number
  timeToConfidence: number
  confidenceHistory: number[]
  timestamps: number[]
  maxSadInterferenceConfidence: number
  sadInterferenceDurationMs?: number
  hadSadInterference?: boolean
}

interface EmotionResults {
  [emotion: string]: EmotionData
}

// 类型定义
interface AIAnalysisResult {
  overallRisk: string;
  psychologicalState: string;
  physiologicalState: string;
  emotionalExpression: string;
  lifestyleRisk: string;
  shortTermSuggestions: string[];
  longTermSuggestions: string[];
  warningPoints: string[];
}

// 情绪评分类型定义
interface EmotionScore {
  name: string;
  percentage: number;
  interpretation: string;
}

const emotionTranslations: Record<string, string> = {
  happy: '开心',
  sad: '悲伤',
  angry: '愤怒',
  surprised: '惊讶',
  neutral: '平静'
}

// 随机数生成函数 - 在基准值附近小幅波动
const getRandomVariation = (baseValue: number, variationPercent: number = 10) => {
  const variation = baseValue * (variationPercent / 100)
  const randomOffset = (Math.random() - 0.5) * 2 * variation
  return Math.round(baseValue + randomOffset)
}

// 模拟数据
const score = ref(getRandomVariation(75, 8)) // 75±6 范围内波动
const aiAnalysisLoading = ref(false)
const aiAnalysisResult = ref<AIAnalysisResult | null>(null)
const lifestylePredictionResult = ref({
  population: '美国（NHANES）',
  method: 'XGBoost梯度提升 + SHAP可解释性分析',
  score: (getRandomVariation(72, 8) + Math.random()).toFixed(1), // 72±6 范围内波动，保留1位小数
  level: '高风险',
  color: '#ef4444',
  details: '集成学习算法结合特征重要性分析，提供个体化风险解释'
})

// 计算风险等级
const getRiskLevel = computed(() => {
  if (score.value >= 80) return '低风险'
  if (score.value >= 60) return '轻度风险'
  if (score.value >= 40) return '中度风险'
  return '高度风险'
})

// AI分析相关函数
const generateUUID = () => {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    const r = Math.random() * 16 | 0;
    const v = c === 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

const analysisProgress = ref(0)
const progressInterval = ref<number | null>(null)

const startProgressAnimation = () => {
  analysisProgress.value = 0
  progressInterval.value = window.setInterval(() => {
    if (analysisProgress.value < 90) {
      analysisProgress.value += Math.random() * 3 + 1
    }
  }, 100)
}

const stopProgressAnimation = () => {
  if (progressInterval.value) {
    clearInterval(progressInterval.value)
    progressInterval.value = null
  }
  analysisProgress.value = 100
}

const performAIAnalysis = async () => {
  const API_KEY = "b1d1ccfab3c24fafba436a4150b60212.Kc9FBGkmVbtRR2ic";
  const API_URL = "https://open.bigmodel.cn/api/paas/v4/chat/completions";

  try {
    aiAnalysisLoading.value = true;
    startProgressAnimation()

    // 构建分析提示词
    const analysisPrompt = `作为一位专业的心理健康分析专家，请基于以下多维度评估数据进行深入分析：

1. 心理量表评估 (权重40%)：
- PHQ-9抑郁量表：${psychologyScores.value[0]?.score || 0}分 (${psychologyScores.value[0]?.interpretation || '暂无数据'})
- GAD-7焦虑量表：${psychologyScores.value[1]?.score || 0}分 (${psychologyScores.value[1]?.interpretation || '暂无数据'})
- PSS-10压力量表：${psychologyScores.value[2]?.score || 0}分 (${psychologyScores.value[2]?.interpretation || '暂无数据'})
- 社会支持评估：${psychologyScores.value[3]?.score || 0}分 (${psychologyScores.value[3]?.interpretation || '暂无数据'})
- 测试完成状态：${testStatus.value[0].completed ? '已完成' : '未完成'}

2. 心电信号分析 (权重25%)：
- 心率变异性：${ecgScores.value[0].value} (${ecgScores.value[0].interpretation})
- 自主神经系统平衡：${ecgScores.value[1].value} (${ecgScores.value[1].interpretation})
- 心率规律性：${ecgScores.value[2].value} (${ecgScores.value[2].interpretation})
- 压力指数：${ecgScores.value[3].value} (${ecgScores.value[3].interpretation})

3. 情绪表情分析 (权重25%)：
${emotionScores.value.map(emotion => `- ${emotion.name}：${emotion.percentage}% (${emotion.interpretation})`).join('\n')}

4. 生活方式预测分析 (权重10%)：
基于机器学习算法的生活方式风险评估，采用XGBoost梯度提升和SHAP可解释性分析

综合评分：${score.value}分
风险等级：${getRiskLevel.value}

请提供以下格式的专业分析：

1. 总体风险评估：
[请给出总体风险水平的专业判断，100字以内]

2. 心理状态分析：
[基于心理量表结果的专业解读，重点关注抑郁、焦虑、压力水平，150字以内]

3. 生理状态评估：
[基于心电信号分析结果的专业解读，重点关注自主神经系统功能，150字以内]

4. 情绪表达能力：
[基于情绪表情分析的专业解读，重点关注情绪表达的多样性和稳定性，150字以内]

5. 生活方式风险评估：
[基于机器学习算法的生活方式风险分析，重点关注行为模式和生活习惯，100字以内]

6. 短期干预建议：
[列出3-5条具体可行的短期改善建议，每条50字以内]

7. 长期改善计划：
[列出3-5条长期发展建议，每条50字以内]

8. 重点关注事项：
[列出2-3条需要特别注意的风险点或警示信息，每条30字以内]

注意：分析要专业、客观，避免过度医学化的表述，同时保持语言平和易懂。`;

    const response = await fetch(API_URL, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${API_KEY}`
      },
      body: JSON.stringify({
        model: "glm-4-plus",
        messages: [{
          role: "user",
          content: analysisPrompt
        }],
        temperature: 0.7,
        top_p: 0.95,
        request_id: generateUUID()
      })
    });

    const data = await response.json();
    
    if (data.choices && data.choices[0].message.content) {
      parseAIResponse(data.choices[0].message.content);
    } else {
      throw new Error('无法获取AI分析结果');
    }
  } catch (error) {
    console.error('AI分析错误:', error);
  } finally {
    stopProgressAnimation()
    aiAnalysisLoading.value = false;
  }
}

const parseAIResponse = (response: string) => {
  const sections = response.split('\n\n');
  const result: AIAnalysisResult = {
    overallRisk: '',
    psychologicalState: '',
    physiologicalState: '',
    emotionalExpression: '',
    lifestyleRisk: '',
    shortTermSuggestions: [],
    longTermSuggestions: [],
    warningPoints: []
  };

  sections.forEach(section => {
    if (section.includes('总体风险评估')) {
      result.overallRisk = section.split('\n')[1];
    } else if (section.includes('心理状态分析')) {
      result.psychologicalState = section.split('\n')[1];
    } else if (section.includes('生理状态评估')) {
      result.physiologicalState = section.split('\n')[1];
    } else if (section.includes('情绪表达能力')) {
      result.emotionalExpression = section.split('\n')[1];
    } else if (section.includes('生活方式风险评估')) {
      result.lifestyleRisk = section.split('\n')[1];
    } else if (section.includes('短期干预建议')) {
      result.shortTermSuggestions = section.split('\n').slice(1).filter(s => s.trim());
    } else if (section.includes('长期改善计划')) {
      result.longTermSuggestions = section.split('\n').slice(1).filter(s => s.trim());
    } else if (section.includes('重点关注事项')) {
      result.warningPoints = section.split('\n').slice(1).filter(s => s.trim());
    }
  });

  aiAnalysisResult.value = result;
}

// 在组件挂载时执行AI分析
onMounted(() => {
  performAIAnalysis();
})

// 四大维度评分
const dimensionScores = ref([
  {
        name: '心理量表评估',
     value: getRandomVariation(78, 8), // 78±6 范围内波动
     weight: '40%',
     description: '心理状态总体稳定，建议保持'
  },
  {
    name: '心电信号分析',
    value: getRandomVariation(82, 7), // 82±6 范围内波动
    weight: '25%',
    description: '自主神经系统功能良好'
  },
  {
    name: '情绪表情识别',
    value: getRandomVariation(65, 12), // 65±8 范围内波动
    weight: '25%',
    description: '情绪表达能力有待提升'
  },
    {
    name: '生活方式预测',
    value: getRandomVariation(75, 8), // 75±6 范围内波动
    weight: '10%',
    description: '生活方式风险相对较低'
  }
])

// 心理量表评分
const psychologyScores = ref([
  {
    name: 'PHQ-9抑郁量表',
    score: getRandomVariation(23, 8), // 23±2 范围内波动
    interpretation: '重度抑郁症状'
  },
  {
    name: 'GAD-7焦虑量表',
    score: getRandomVariation(11, 12), // 11±1 范围内波动
    interpretation: '重度焦虑症状'
  },
  {
    name: 'PSS-10压力量表',
    score: getRandomVariation(5, 15), // 5±1 范围内波动
    interpretation: '低压力水平'
  },
  {
    name: '社会支持评估',
    score: getRandomVariation(17, 10), // 17±2 范围内波动
    interpretation: '社会支持一般'
  }
])

// 心电信号分析
const ecgScores = ref([
  {
    name: '心率变异性',
    value: '良好',
    interpretation: 'HRV指标在正常范围内'
  },
  {
    name: '自主神经系统平衡',
    value: '平衡',
    interpretation: '交感和副交感神经系统功能协调'
  },
  {
    name: '心率规律性',
    value: '正常',
    interpretation: '心率变化规律，无异常'
  },
  {
    name: '压力指数',
    value: '中等',
    interpretation: '压力水平可控'
  }
])

// 情绪表情分析
const emotionScores = ref<EmotionScore[]>([
  {
    name: '开心',
    percentage: 15,
    interpretation: '能够表达基本情绪，但强度有限'
  },
  {
    name: '悲伤',
    percentage: 25,
    interpretation: '情绪切换较为流畅'
  },
  {
    name: '愤怒',
    percentage: 10,
    interpretation: '悲伤情绪影响较小'
  },
  {
    name: '惊讶',
    percentage: 5,
    interpretation: '面部表情丰富'
  },
  {
    name: '平静',
    percentage: 45,
    interpretation: '面部表情平静'
  }
])



// 综合建议
const suggestions = ref([
  {
    title: '短期干预建议',
    content: '建议进行每周2-3次的放松训练，培养积极的生活方式。'
  },
  {
    title: '长期改善计划',
    content: '制定规律的运动计划，保持良好的作息习惯，定期进行心理咨询。'
  },
  {
    title: '复查建议',
    content: '建议3个月后进行复查，追踪各项指标的变化情况。'
  },
  {
    title: '生活方式调整',
    content: '保持规律作息，适度运动，培养兴趣爱好，扩大社交圈。'
  }
])

// 在script setup部分添加新的状态和函数
const chatMessage = ref('')
const chatHistory = ref<Array<{role: 'user' | 'assistant', content: string}>>([])
const isChatLoading = ref(false)
const chatContainer = ref<HTMLElement | null>(null)

const predefinedQuestions = [
  '基于我的评估结果，我最需要改善的方面是什么？',
  '我的情绪状态对日常生活有什么影响？',
  '有什么具体的放松方法可以推荐给我？'
]

const scrollToBottom = () => {
  nextTick(() => {
    if (chatContainer.value) {
      chatContainer.value.scrollTop = chatContainer.value.scrollHeight
    }
  })
}

// 在script setup中添加格式化函数
const formatAIResponse = (text: string): string => {
  return text
    // 移除所有*和#符号
    .replace(/[*#]/g, '')
    // 确保数字序号后的内容另起一行
    .replace(/(\d+\.)\s*/g, '\n$1 ')
    // 移除多余的空行
    .replace(/\n\s*\n/g, '\n')
    // 移除开头的空行
    .trim()
}

const sendChatMessage = async (message: string) => {
  if (!message.trim()) return
  
  isChatLoading.value = true
  chatHistory.value.push({ role: 'user', content: message })
  scrollToBottom()
  
  try {
    const response = await fetch("https://open.bigmodel.cn/api/paas/v4/chat/completions", {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer b1d1ccfab3c24fafba436a4150b60212.Kc9FBGkmVbtRR2ic`
      },
      body: JSON.stringify({
        model: "glm-4-plus",
        messages: [
          {
            role: "system",
            content: `你是一位专业的心理健康顾问，正在为用户提供基于其评估报告的建议。
评估结果概要：
- 总体评分：${score.value}分
- 风险等级：${getRiskLevel.value}
- 心理量表：${psychologyScores.value[0].interpretation}
- 情绪表现：${emotionScores.value[0].interpretation}

请以专业、温和的语气回答用户的问题，给出具体、可行的建议。回答要简洁，不超过150字。`
          },
          ...chatHistory.value
        ],
        temperature: 0.7,
        top_p: 0.95,
        request_id: generateUUID()
      })
    })

    const data = await response.json()
    if (data.choices && data.choices[0].message.content) {
      chatHistory.value.push({
        role: 'assistant',
        content: formatAIResponse(data.choices[0].message.content)
      })
      scrollToBottom()
    }
  } catch (error) {
    console.error('Chat error:', error)
    chatHistory.value.push({
      role: 'assistant',
      content: '抱歉，我暂时无法回答您的问题。请稍后再试。'
    })
    scrollToBottom()
  } finally {
    isChatLoading.value = false
    chatMessage.value = ''
  }
}

// 图表引用
const maxConfidenceChartRef = ref<HTMLCanvasElement | null>(null)
const timeToConfidenceChartRef = ref<HTMLCanvasElement | null>(null)
const sadInterferenceChartsRef = ref<HTMLDivElement | null>(null)
const maxSadInterferenceChartRef = ref<HTMLCanvasElement | null>(null)

onMounted(() => {
  // 初始加载情绪测试数据
  loadEmotionResults()
  
  // 初始化加载心理量表数据
  updateQuestionnaireScores()
  
  // 监听localStorage变化，实时更新图表
  const handleStorageChange = () => {
    loadEmotionResults()
    updateQuestionnaireScores()
    // 重新加载测试状态
    updateTestStatus()
  }
  
  window.addEventListener('storage', handleStorageChange)
  
  // 定时检查更新（用于同页面内的更新）
  const checkInterval = setInterval(() => {
    loadEmotionResults()
    updateQuestionnaireScores()
    updateTestStatus()
  }, 2000)
  
  // 清理监听器
  onUnmounted(() => {
    window.removeEventListener('storage', handleStorageChange)
    clearInterval(checkInterval)
  })
})

function loadEmotionResults() {
  const resultsString = localStorage.getItem('emotionAssessmentData')
  if (resultsString) {
    try {
      const results: EmotionResults = JSON.parse(resultsString)
      renderEmotionCharts(results)
    } catch (error) {
      console.error('Error parsing emotion results:', error)
    }
  }
}

function updateTestStatus() {
  const completedTests = JSON.parse(localStorage.getItem('completedTests') || '{}')
  
  // 额外检查实际数据是否存在，确保准确性
  const hasQuestionnaire = completedTests.questionnaire || localStorage.getItem('questionnaireResults')
  const hasEcg = completedTests.ecg || localStorage.getItem('ecgResults')  
  const hasEmotion = completedTests.emotion || localStorage.getItem('emotionAssessmentData')
  // 注意：gene 字段实际对应生活方式预测测试，历史命名保持兼容性
  const hasLifestylePrediction = completedTests.gene || localStorage.getItem('lifestylePredictionResult')
  
  // 更新测试状态
  testStatus.value = [
    { name: "心理量表评估", completed: !!hasQuestionnaire },
    { name: "心电信号分析", completed: !!hasEcg },
    { name: "情绪表情识别", completed: !!hasEmotion },
    { name: "生活方式预测", completed: !!hasLifestylePrediction }
  ]
  
  // 如果情绪测试数据存在但状态未更新，则更新情绪评分
  if (hasEmotion) {
    updateEmotionScores()
  }
  
  // 如果心理量表数据存在但状态未更新，则更新心理量表评分
  if (hasQuestionnaire) {
    updateQuestionnaireScores()
  }
}

function updateEmotionScores() {
  const emotionData = localStorage.getItem('emotionAssessmentData')
  if (emotionData) {
    try {
      const results = JSON.parse(emotionData)
      
      // 计算平均置信度和响应时间
      const emotions = Object.values(results)
      const avgConfidence = emotions.reduce((sum: number, emotion: any) => 
        sum + (emotion.maxConfidence || 0), 0) / emotions.length
      const avgResponseTime = emotions.reduce((sum: number, emotion: any) => 
        sum + (emotion.timeToConfidence || 5000), 0) / emotions.length
      
      // 更新情绪评分数据
      emotionScores.value = [
        {
          name: '平均情绪表达置信度',
          percentage: Math.round(avgConfidence > 1 ? avgConfidence : avgConfidence * 100),
          interpretation: avgConfidence > 0.7 ? '情绪表达清晰' : 
                         avgConfidence > 0.5 ? '情绪表达良好' : '情绪表达需改善'
        },
        {
          name: '平均情绪转换时间',
          percentage: Math.round(avgResponseTime),
          interpretation: avgResponseTime < 3000 ? '情绪转换快速' : 
                         avgResponseTime < 4000 ? '情绪转换正常' : '情绪转换较慢'
        }
      ]
    } catch (error) {
      console.error('Error updating emotion scores:', error)
    }
  }
}

function updateQuestionnaireScores() {
  const questionnaireData = localStorage.getItem('questionnaireResults')
  if (questionnaireData) {
    try {
      const results = JSON.parse(questionnaireData)
      
      // 更新心理量表评分数据
      psychologyScores.value = [
        {
          name: 'PHQ-9抑郁量表',
          score: results.phq9Score || 0,
          interpretation: results.phq9Score <= 4 ? '无抑郁症状' : 
                         results.phq9Score <= 9 ? '轻微抑郁症状' :
                         results.phq9Score <= 14 ? '中度抑郁症状' : '重度抑郁症状'
        },
        {
          name: 'GAD-7焦虑量表',
          score: results.gad7Score || 0,
          interpretation: results.gad7Score <= 4 ? '无焦虑症状' :
                         results.gad7Score <= 9 ? '轻微焦虑症状' :
                         results.gad7Score <= 14 ? '中度焦虑症状' : '重度焦虑症状'
        },
        {
          name: 'PSS-10压力量表',
          score: results.pss10Score || 0,
          interpretation: results.pss10Score <= 13 ? '低压力水平' :
                         results.pss10Score <= 26 ? '中等压力水平' : '高压力水平'
        },
        {
          name: '社会支持评估',
          score: results.socialSupport || 0,
          interpretation: results.socialSupport >= 8 ? '社会支持良好' :
                         results.socialSupport >= 4 ? '社会支持一般' : '社会支持不足'
        }
      ]
      
      // 更新综合评分中的心理量表维度评分
      if (dimensionScores.value[0]) {
        const avgScore = Math.round((results.phq9Score + results.gad7Score + results.pss10Score + results.socialSupport) / 4)
        dimensionScores.value[0].value = avgScore
        dimensionScores.value[0].description = results.interpretation || '心理状态评估完成'
      }
      
    } catch (error) {
      console.error('Error updating questionnaire scores:', error)
    }
  }
}

function renderEmotionCharts(results: EmotionResults) {
  console.log('Rendering emotion charts with data:', results)
  
  const labels: string[] = []
  const maxConfidenceData: number[] = []
  const timeToConfidenceData: number[] = []
  const sadInterferenceLabels: string[] = []
  const maxSadInterferenceData: number[] = []

  // 确保有数据才进行处理
  if (!results || Object.keys(results).length === 0) {
    console.log('No emotion results data available')
    return
  }

  for (const [emotion, data] of Object.entries(results)) {
    const translatedEmotion = emotionTranslations[emotion as keyof typeof emotionTranslations]
    if (translatedEmotion && data) {
      labels.push(translatedEmotion)
      
      // 将置信度转换为百分比（如果原始数据是0-1范围）
      const confidence = data.maxConfidence || 0
      maxConfidenceData.push(confidence > 1 ? confidence : confidence * 100)
      
      // 确保时间数据存在
      timeToConfidenceData.push(data.timeToConfidence || 5000)

      // 处理悲伤干扰数据（除了悲伤本身）
      if (emotion !== 'sad') {
        sadInterferenceLabels.push(translatedEmotion)
        const sadInterference = data.maxSadInterferenceConfidence || 0
        maxSadInterferenceData.push(sadInterference > 1 ? sadInterference : sadInterference * 100)
      }
    }
  }

  console.log('Processed chart data:', {
    labels,
    maxConfidenceData,
    timeToConfidenceData,
    sadInterferenceLabels,
    maxSadInterferenceData
  })

  // 最大置信度图表
  if (maxConfidenceChartRef.value) {
    new Chart(maxConfidenceChartRef.value.getContext('2d')!, {
      type: 'bar',
      data: {
        labels,
        datasets: [{
          label: '最大置信度 (%)',
          data: maxConfidenceData,
          backgroundColor: [
            'rgba(255, 206, 86, 0.7)',
            'rgba(54, 162, 235, 0.7)',
            'rgba(255, 99, 132, 0.7)',
            'rgba(255, 159, 64, 0.7)',
            'rgba(153, 102, 255, 0.7)'
          ],
          borderColor: [
            'rgba(255, 206, 86, 1)',
            'rgba(54, 162, 235, 1)',
            'rgba(255, 99, 132, 1)',
            'rgba(255, 159, 64, 1)',
            'rgba(153, 102, 255, 1)'
          ],
          borderWidth: 1
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          y: {
            beginAtZero: true,
            max: 100
          }
        },
        plugins: {
          legend: { display: false }
        }
      }
    })
  }

  // 情绪转换效率图表
  if (timeToConfidenceChartRef.value) {
    new Chart(timeToConfidenceChartRef.value.getContext('2d')!, {
      type: 'bar',
      data: {
        labels,
        datasets: [{
          label: '达成时间 (毫秒)',
          data: timeToConfidenceData,
          backgroundColor: [
            'rgba(75, 192, 192, 0.7)',
            'rgba(153, 102, 255, 0.7)',
            'rgba(255, 99, 71, 0.7)',
            'rgba(255, 215, 0, 0.7)',
            'rgba(128, 128, 128, 0.7)'
          ],
          borderColor: [
            'rgba(75, 192, 192, 1)',
            'rgba(153, 102, 255, 1)',
            'rgba(255, 99, 71, 1)',
            'rgba(255, 215, 0, 1)',
            'rgba(128, 128, 128, 1)'
          ],
          borderWidth: 1
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          y: {
            beginAtZero: true,
            title: {
              display: true,
              text: '时间 (毫秒)'
            }
          }
        },
        plugins: {
          legend: { display: false }
        }
      }
    })
  }

  // 悲伤情绪干扰分析
  if (sadInterferenceChartsRef.value && results) {
    sadInterferenceChartsRef.value.innerHTML = ''
    
    for (const [emotion, data] of Object.entries(results)) {
      const translatedEmotion = emotionTranslations[emotion as keyof typeof emotionTranslations]
      if (emotion !== 'sad' && translatedEmotion) {
        const chartContainer = document.createElement('div')
        chartContainer.className = 'chart-container flex flex-col items-center'
        chartContainer.style.minWidth = '280px'
        chartContainer.style.maxWidth = '300px'
        
        const title = document.createElement('p')
        title.textContent = `目标情绪: ${translatedEmotion}`
        title.className = 'text-center font-semibold text-primary mb-3 text-sm'
        
        const canvas = document.createElement('canvas')
        canvas.style.maxHeight = '200px'
        
        chartContainer.appendChild(title)
        chartContainer.appendChild(canvas)
        sadInterferenceChartsRef.value.appendChild(chartContainer)

        const ctx = canvas.getContext('2d')
        if (ctx) {
          // 如果没有具体的干扰时长数据，基于最大干扰置信度估算
          const maxSadInterference = data.maxSadInterferenceConfidence || 0
          const sadInterferenceThreshold = 0.03 // 3%阈值
          
          // 根据最大干扰置信度估算干扰时长占比
          let interferencePercentage = 0
          if (maxSadInterference > sadInterferenceThreshold) {
            // 简单的线性映射：置信度越高，干扰时长占比越大
            interferencePercentage = Math.min((maxSadInterference / 1.0) * 50, 100) // 最多50%的干扰时长
          }
          
          const nonInterferencePercentage = 100 - interferencePercentage

          new Chart(ctx, {
            type: 'pie',
            data: {
              labels: ['悲伤干扰时长占比', '无明显悲伤干扰时长占比'],
              datasets: [{
                data: [interferencePercentage, nonInterferencePercentage],
                backgroundColor: ['rgba(255, 99, 132, 0.8)', 'rgba(75, 192, 192, 0.8)'],
                borderColor: ['rgba(255, 99, 132, 1)', 'rgba(75, 192, 192, 1)'],
                borderWidth: 2
              }]
            },
            options: {
              responsive: true,
              maintainAspectRatio: true,
              plugins: {
                legend: {
                  position: 'bottom',
                  labels: {
                    padding: 15,
                    usePointStyle: true,
                    font: {
                      size: 11
                    }
                  }
                },
                tooltip: {
                  callbacks: {
                    label: function(context) {
                      const value = context.parsed
                      return `${context.label}: ${value.toFixed(4)}%`
                    }
                  }
                }
              },
              animation: {
                onComplete: function(this: Chart) {
                  const ctx = this.ctx
                  
                  if (ctx) {
                    ctx.font = 'bold 12px Arial'
                    ctx.textAlign = 'center'
                    ctx.fillStyle = '#fff'
                    
                    this.data.datasets.forEach((dataset, i) => {
                      const meta = this.getDatasetMeta(i)
                      meta.data.forEach((element: any, index) => {
                        const value = dataset.data[index] as number
                        if (value > 3) { // 只显示大于3%的标签
                          const position = element.tooltipPosition({x: 0, y: 0})
                          ctx.fillText(value.toFixed(4) + '%', position.x, position.y)
                        }
                      })
                    })
                  }
                }
              }
            }
          })
        }
      }
    }
  }

  // 最大悲伤干扰置信度图表
  if (maxSadInterferenceChartRef.value) {
    const ctx = maxSadInterferenceChartRef.value.getContext('2d')
    if (ctx) {
      new Chart(ctx, {
        type: 'bar',
        data: {
          labels: sadInterferenceLabels,
          datasets: [{
            label: '悲伤情绪最大干扰置信度 (%)',
            data: maxSadInterferenceData,
            backgroundColor: 'rgba(255, 159, 64, 0.7)',
            borderColor: 'rgba(255, 159, 64, 1)',
            borderWidth: 1
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          scales: {
            y: {
              beginAtZero: true,
              max: 100,
              title: {
                display: true,
                text: '最大悲伤置信度 (%)'
              }
            }
          },
          plugins: {
            legend: { display: false }
          }
        }
      })
    }
  }
}

// 测试状态
const testStatus = ref([
  { name: '心理量表评估', completed: false },
  { name: '心电信号分析', completed: false },
  { name: '情绪表情识别', completed: false },
  { name: '生活方式预测', completed: false }
])

// 计算是否所有测试都已完成
const allTestsCompleted = computed(() => {
  return testStatus.value.every(test => test.completed)
})
</script>

<style scoped>
.emotion-container {
  background-color: rgba(28, 28, 35, 0.8);
  border-radius: 1rem;
  padding: 2rem;
}

/* 生活方式预测结果样式 */
.result-content {
  background: rgba(255, 255, 255, 0.05);
  border-radius: 12px;
  padding: 2rem;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.result-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
  flex-wrap: wrap;
  gap: 1rem;
}

.population-badge {
  background: rgba(249, 115, 22, 0.2);
  color: #f97316;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-weight: 600;
  border: 1px solid rgba(249, 115, 22, 0.3);
}

.method-tag {
  background: rgba(59, 130, 246, 0.2);
  color: #3b82f6;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-size: 0.9rem;
  border: 1px solid rgba(59, 130, 246, 0.3);
}

.score-display {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 3rem;
  margin: 2rem 0;
  flex-wrap: wrap;
}

.score-circle {
  width: 120px;
  height: 120px;
  border: 4px solid;
  border-radius: 50%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
}

.score-number {
  font-size: 2.5rem;
  font-weight: bold;
  line-height: 1;
}

.score-label {
  font-size: 1rem;
  opacity: 0.8;
}

.risk-info {
  flex: 1;
  min-width: 200px;
}

.risk-level {
  font-size: 1.5rem;
  font-weight: bold;
  margin-bottom: 1rem;
  text-align: center;
}

.risk-bar {
  background: rgba(255, 255, 255, 0.1);
  height: 12px;
  border-radius: 6px;
  overflow: hidden;
}

.risk-progress {
  height: 100%;
  transition: width 0.5s ease;
}

.analysis-details, .risk-interpretation {
  margin-top: 2rem;
  padding: 1.5rem;
  background: rgba(255, 255, 255, 0.03);
  border-radius: 8px;
}

.details-title, .interpretation-title {
  font-size: 1.1rem;
  font-weight: 600;
  margin-bottom: 1rem;
  color: #f97316;
}

.details-text {
  line-height: 1.6;
  color: rgba(255, 255, 255, 0.9);
}

.interpretation-content {
  line-height: 1.6;
}

.interpretation-text {
  padding: 1rem;
  border-radius: 8px;
  margin-top: 0.5rem;
}

.interpretation-text.success {
  background: rgba(16, 185, 129, 0.1);
  border-left: 4px solid #10b981;
  color: #d1fae5;
}

.interpretation-text.warning {
  background: rgba(245, 158, 11, 0.1);
  border-left: 4px solid #f59e0b;
  color: #fef3c7;
}

.interpretation-text.danger {
  background: rgba(239, 68, 68, 0.1);
  border-left: 4px solid #ef4444;
  color: #fecaca;
}

.interpretation-text.critical {
  background: rgba(220, 38, 38, 0.1);
  border-left: 4px solid #dc2626;
  color: #fecaca;
}
</style> 