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

        <!-- AI智能分析部分 -->
        <div class="bg-card p-6 rounded-lg shadow-md mt-8">
          <h3 class="text-2xl font-semibold mb-6 flex items-center">
            <span class="mr-2">🤖</span>
            AI智能分析
          </h3>

          <div v-if="aiAnalysisLoading" class="flex flex-col items-center justify-center py-8">
            <div class="w-12 h-12 border-4 border-primary border-t-transparent rounded-full animate-spin mb-4"></div>
            <p class="text-muted-foreground">正在进行智能分析...</p>
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
                <h4 class="text-lg font-semibold mb-3 text-primary">遗传风险评估</h4>
                <p class="text-muted-foreground">{{ aiAnalysisResult.geneticRisk }}</p>
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
import { ref, computed, onMounted, nextTick } from 'vue'

// 类型定义
interface AIAnalysisResult {
  overallRisk: string;
  psychologicalState: string;
  physiologicalState: string;
  emotionalExpression: string;
  geneticRisk: string;
  shortTermSuggestions: string[];
  longTermSuggestions: string[];
  warningPoints: string[];
}

// 模拟数据
const score = ref(75)
const aiAnalysisLoading = ref(false)
const aiAnalysisResult = ref<AIAnalysisResult | null>(null)

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

const performAIAnalysis = async () => {
  const API_KEY = "b1d1ccfab3c24fafba436a4150b60212.Kc9FBGkmVbtRR2ic";
  const API_URL = "https://open.bigmodel.cn/api/paas/v4/chat/completions";

  try {
    aiAnalysisLoading.value = true;

    // 构建分析提示词
    const analysisPrompt = `作为一位专业的心理健康分析专家，请基于以下多维度评估数据进行深入分析：

1. 心理量表评估 (权重40%)：
- PHQ-9抑郁量表：${psychologyScores.value[0].score}分 (${psychologyScores.value[0].interpretation})
- GAD-7焦虑量表：${psychologyScores.value[1].score}分 (${psychologyScores.value[1].interpretation})
- PSS-10压力量表：${psychologyScores.value[2].score}分 (${psychologyScores.value[2].interpretation})
- 社会支持评估：${psychologyScores.value[3].score}分 (${psychologyScores.value[3].interpretation})

2. 心电信号分析 (权重25%)：
- 心率变异性：${ecgScores.value[0].value} (${ecgScores.value[0].interpretation})
- 自主神经系统平衡：${ecgScores.value[1].value} (${ecgScores.value[1].interpretation})
- 心率规律性：${ecgScores.value[2].value} (${ecgScores.value[2].interpretation})
- 压力指数：${ecgScores.value[3].value} (${ecgScores.value[3].interpretation})

3. 情绪表情分析 (权重25%)：
${emotionScores.value.map(emotion => `- ${emotion.name}：${emotion.percentage}% (${emotion.interpretation})`).join('\n')}

4. 基因筛查分析 (权重10%)：
${geneScores.value.map(gene => `- ${gene.name}：${gene.value} (${gene.interpretation})`).join('\n')}

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

5. 遗传风险评估：
[基于基因筛查结果的专业解读，重点关注遗传易感性，100字以内]

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
    // 在UI中显示错误信息
  } finally {
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
    geneticRisk: '',
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
    } else if (section.includes('遗传风险评估')) {
      result.geneticRisk = section.split('\n')[1];
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
</script>

<style scoped>
.emotion-container {
  background-color: rgba(28, 28, 35, 0.8);
  border-radius: 1rem;
  padding: 2rem;
}
</style> 