<template>
  <section class="py-24 sm:py-32">
    <div class="emotion-container max-w-7xl mx-auto px-6 lg:px-8">
      <!-- 标题部分 -->
      <div class="mb-10 text-center">
        <h2 class="text-3xl font-bold tracking-tight sm:text-4xl mb-4">
          综合评估报告
        </h2>
        <p class="text-lg text-gray-600 max-w-3xl mx-auto">
          基于多维度数据分析的个性化心理健康评估报告，为您提供专业的心理健康指导
        </p>
      </div>

      <div class="space-y-8">
        <!-- 总体风险等级和评分 -->
        <div :class="[
          'p-8 rounded-2xl shadow-xl border-2',
          score >= 80 ? 'bg-gradient-to-r from-green-100 via-green-50 to-green-200 dark:from-green-900 dark:via-green-800 dark:to-green-900 border-green-300 dark:border-green-700' :
          score >= 60 ? 'bg-gradient-to-r from-orange-100 via-orange-50 to-orange-200 dark:from-orange-900 dark:via-orange-800 dark:to-orange-900 border-orange-300 dark:border-orange-700' :
          score >= 40 ? 'bg-gradient-to-r from-red-100 via-red-50 to-red-200 dark:from-red-900 dark:via-red-800 dark:to-red-900 border-red-300 dark:border-red-700' :
          'bg-gradient-to-r from-rose-200 via-rose-100 to-rose-300 dark:from-rose-900 dark:via-rose-800 dark:to-rose-900 border-rose-400 dark:border-rose-700'
        ]">
          <h2 class="text-4xl font-bold text-center mb-6" :class="[
            score >= 80 ? 'text-green-900 dark:text-green-100' :
            score >= 60 ? 'text-orange-900 dark:text-orange-100' :
            score >= 40 ? 'text-red-900 dark:text-red-100' :
            'text-rose-900 dark:text-rose-100'
          ]">综合评估结果</h2>
          <div class="flex justify-around items-center">
            <div class="text-center">
              <p class="text-lg font-semibold" :class="[
                score >= 80 ? 'text-green-800 dark:text-green-200' :
                score >= 60 ? 'text-orange-800 dark:text-orange-200' :
                score >= 40 ? 'text-red-800 dark:text-red-200' :
                'text-rose-800 dark:text-rose-200'
              ]">总体风险等级</p>
              <p class="text-3xl font-bold mt-2" :class="[
                score >= 80 ? 'text-green-600 dark:text-green-400' :
                score >= 60 ? 'text-orange-600 dark:text-orange-400' :
                score >= 40 ? 'text-red-600 dark:text-red-400' :
                'text-rose-600 dark:text-rose-400'
              ]">{{ getRiskLevel }}</p>
            </div>
            <div class="text-center">
              <p class="text-lg font-semibold" :class="[
                score >= 80 ? 'text-green-800 dark:text-green-200' :
                score >= 60 ? 'text-orange-800 dark:text-orange-200' :
                score >= 40 ? 'text-red-800 dark:text-red-200' :
                'text-rose-800 dark:text-rose-200'
              ]">综合评分</p>
              <p class="text-5xl font-bold mt-2" :class="[
                score >= 80 ? 'text-green-900 dark:text-green-100' :
                score >= 60 ? 'text-orange-900 dark:text-orange-100' :
                score >= 40 ? 'text-red-900 dark:text-red-100' :
                'text-rose-900 dark:text-rose-100'
              ]">{{ score }}<span class="text-2xl">分</span></p>
            </div>
          </div>
        </div>

        <!-- 四大维度评分 -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
          <div v-for="(score, index) in dimensionScores" :key="index" 
               class="bg-card p-6 rounded-lg shadow-md">
            <h3 class="text-xl font-semibold mb-4">{{ score.name }}</h3>
            <div class="flex justify-between items-center">
              <span class="text-3xl font-bold">{{ score.value }}</span>
              <span class="text-sm text-muted-foreground">权重: {{ score.weight }}</span>
            </div>
            <p class="mt-4 text-sm text-muted-foreground">{{ score.description }}</p>
          </div>
        </div>

        <!-- 详细分析部分 -->
        <div class="space-y-8">
          <!-- 心理量表评估 -->
          <div class="bg-card p-6 rounded-lg shadow-md">
            <h3 class="text-2xl font-semibold mb-4">心理量表评估</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div v-for="(item, index) in psychologyScores" :key="index" 
                   class="p-4 bg-muted rounded">
                <p class="font-medium">{{ item.name }}</p>
                <p class="text-2xl font-bold mt-2">{{ item.score }}</p>
                <p class="text-sm text-muted-foreground mt-1">{{ item.interpretation }}</p>
              </div>
            </div>
          </div>

          <!-- 心电信号分析 -->
          <div class="bg-card p-6 rounded-lg shadow-md">
            <h3 class="text-2xl font-semibold mb-4">心电信号分析</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div v-for="(item, index) in ecgScores" :key="index" 
                   class="p-4 bg-muted rounded">
                <p class="font-medium">{{ item.name }}</p>
                <p class="text-2xl font-bold mt-2">{{ item.value }}</p>
                <p class="text-sm text-muted-foreground mt-1">{{ item.interpretation }}</p>
              </div>
            </div>
          </div>

          <!-- 情绪表情分析 -->
          <div class="bg-card p-6 rounded-lg shadow-md">
            <h3 class="text-2xl font-semibold mb-4">情绪表情分析</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div v-for="(item, index) in emotionScores" :key="index" 
                   class="p-4 bg-muted rounded">
                <p class="font-medium">{{ item.name }}</p>
                <p class="text-2xl font-bold mt-2">{{ item.percentage }}%</p>
                <p class="text-sm text-muted-foreground mt-1">{{ item.interpretation }}</p>
              </div>
            </div>
          </div>

          <!-- 基因筛查分析 -->
          <div class="bg-card p-6 rounded-lg shadow-md">
            <h3 class="text-2xl font-semibold mb-4">基因筛查分析</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div v-for="(item, index) in geneScores" :key="index" 
                   class="p-4 bg-muted rounded">
                <p class="font-medium">{{ item.name }}</p>
                <p class="text-2xl font-bold mt-2">{{ item.value }}</p>
                <p class="text-sm text-muted-foreground mt-1">{{ item.interpretation }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- 综合建议 -->
        <div class="bg-card p-6 rounded-lg shadow-md">
          <h3 class="text-2xl font-semibold mb-4">综合建议</h3>
          <div class="space-y-4">
            <div v-for="(suggestion, index) in suggestions" :key="index" 
                 class="p-4 bg-muted rounded">
              <p class="font-medium">{{ suggestion.title }}</p>
              <p class="text-sm text-muted-foreground mt-2">{{ suggestion.content }}</p>
            </div>
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
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

// 模拟数据
const score = ref(75)

// 计算风险等级
const getRiskLevel = computed(() => {
  if (score.value >= 80) return '低风险'
  if (score.value >= 60) return '轻度风险'
  if (score.value >= 40) return '中度风险'
  return '高度风险'
})

// 四大维度评分
const dimensionScores = ref([
  {
        name: '心理量表评估',
     value: 78,
     weight: '40%',
     description: '心理状态总体稳定，建议保持'
  },
  {
    name: '心电信号分析',
    value: 82,
    weight: '25%',
    description: '自主神经系统功能良好'
  },
  {
    name: '情绪表情识别',
    value: 65,
    weight: '25%',
    description: '情绪表达能力有待提升'
  },
  {
        name: '基因筛查',
     value: 75,
     weight: '10%',
     description: '遗传风险相对较低'
  }
])

// 心理量表评分
const psychologyScores = ref([
  {
    name: 'PHQ-9抑郁量表',
    score: 5,
    interpretation: '轻微抑郁症状'
  },
  {
    name: 'GAD-7焦虑量表',
    score: 4,
    interpretation: '轻微焦虑症状'
  },
  {
    name: 'PSS-10压力量表',
    score: 15,
    interpretation: '中等压力水平'
  },
  {
    name: '社会支持评估',
    score: 35,
    interpretation: '社会支持良好'
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
const emotionScores = ref([
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

// 基因筛查分析
const geneScores = ref([
  {
    name: '基因位点变异',
    value: '低风险',
    interpretation: '未发现高风险变异'
  },
  {
    name: '遗传风险评分',
    value: '75分',
    interpretation: '遗传风险相对较低'
  },
  {
    name: '药物代谢能力',
    value: '正常',
    interpretation: '药物代谢功能正常'
  },
  {
    name: '表观遗传标记',
    value: '稳定',
    interpretation: '表观遗传状态稳定'
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
</script>

<style scoped>
.emotion-container {
  background-color: rgba(28, 28, 35, 0.8);
  border-radius: 1rem;
  padding: 2rem;
}
</style> 