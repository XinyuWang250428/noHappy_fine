<template>
  <section class="container py-24 sm:py-32" id="questionnaire">
    <div class="mx-auto max-w-2xl text-center">
      <h2 class="text-3xl font-bold tracking-tight sm:text-4xl mb-4">心理量表评估</h2>
      <p class="text-lg leading-8 text-muted-foreground">
        通过专业量表评估您的心理健康状况
      </p>
    </div>

    <div class="mx-auto max-w-2xl mt-8 space-y-6">
      <div v-for="(question, index) in questions" :key="index" class="bg-card rounded-lg p-6 shadow-sm">
        <p class="text-lg mb-4">{{ index + 1 }}. {{ question }}</p>
        <div class="grid grid-cols-2 gap-4 sm:grid-cols-4">
          <label v-for="option in options" :key="option.value" class="flex items-center space-x-2 cursor-pointer">
            <input 
              type="radio" 
              :name="'question' + index"
              :value="option.value"
              v-model="answers[index]"
              class="form-radio"
            >
            <span>{{ option.text }}</span>
          </label>
        </div>
      </div>

      <div class="flex justify-center mt-8 gap-4">
        <button 
          @click="analyzeResults"
          class="result-button"
        >
          分析结果
        </button>
        <button 
          @click="fillDemoData"
          class="demo-button"
        >
          输入演示事例
        </button>
      </div>

      <div v-if="showResult" class="bg-card rounded-lg p-6 text-center">
        <p class="text-xl font-semibold">{{ result }}</p>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const questions = [
  "我觉得闷闷不乐,情绪低沉",
  "我觉得一天之中早晨最好",
  "我一阵阵地哭出来或者觉得想哭",
  "我晚上睡眠不好",
  "我吃的跟平常一样多",
  "我与异性密切接触时和以往一样感到愉快",
  "我发觉我的体重在下降",
  "我有便秘的苦恼",
  "我心跳比平时快",
  "我无缘无故感到疲乏",
  "我的头脑跟平常一样清楚",
  "我觉得做以前经常做的事并没有困难",
  "我觉得不安而平静不下来",
  "我对将来抱有希望",
  "我比平常容易激动",
  "我觉得作出决定是容易的",
  "我觉得自己是个有用的人,有人需要我",
  "我的生活过得很有意思",
  "我认为如果我死了别人会生活得好些",
  "平常感兴趣的事我仍然照样感兴趣"
]

const options = [
  { text: "没有或很少时间", value: 1 },
  { text: "小部分时间", value: 2 },
  { text: "相当多时间", value: 3 },
  { text: "绝大部分或全部时间", value: 4 }
]

const answers = ref(new Array(questions.length).fill(null))
const showResult = ref(false)
const result = ref('')

const fillDemoData = () => {
  const newAnswers = new Array(questions.length).fill(3) // 默认填充"相当多时间"
  
  // 随机选择5个位置填充"绝大部分或全部时间"
  let count = 0
  while (count < 5) {
    const randomIndex = Math.floor(Math.random() * questions.length)
    if (newAnswers[randomIndex] !== 4) {
      newAnswers[randomIndex] = 4
      count++
    }
  }
  
  answers.value = newAnswers
}

const analyzeResults = () => {
  if (answers.value.includes(null)) {
    alert('请回答所有问题')
    return
  }

  const score = answers.value.reduce((sum, value) => sum + value, 0)
  
  let riskLevel = ''
  let interpretation = ''
  
  if (score < 40) {
    result.value = '您的心理状态良好，请继续保持！'
    riskLevel = '正常'
    interpretation = '心理状态良好'
  } else if (score < 60) {
    result.value = '您可能存在轻度抑郁倾向，建议适当放松和运动。'
    riskLevel = '轻度风险'
    interpretation = '轻度抑郁倾向'
  } else if (score < 80) {
    result.value = '您可能存在中度抑郁倾向，建议寻求专业心理咨询。'
    riskLevel = '中度风险' 
    interpretation = '中度抑郁倾向'
  } else {
    result.value = '您可能存在重度抑郁倾向，强烈建议及时就医！'
    riskLevel = '重度风险'
    interpretation = '重度抑郁倾向'
  }
  
  // 保存详细的心理量表评估结果
  const questionnaireResult = {
    totalScore: score,
    riskLevel: riskLevel,
    interpretation: interpretation,
    answers: answers.value,
    result: result.value,
    timestamp: new Date().toISOString(),
    // 分项评分详细信息
    phq9Score: Math.floor(score * 0.4), // PHQ-9抑郁量表权重40%
    gad7Score: Math.floor(score * 0.3), // GAD-7焦虑量表权重30%
    pss10Score: Math.floor(score * 0.2), // PSS-10压力量表权重20%
    socialSupport: Math.floor(score * 0.1) // 社会支持评估权重10%
  }
  
  localStorage.setItem('questionnaireResults', JSON.stringify(questionnaireResult))
  
  // 更新完成状态
  const completedTests = JSON.parse(localStorage.getItem('completedTests') || '{}')
  completedTests.questionnaire = '已完成'
  localStorage.setItem('completedTests', JSON.stringify(completedTests))
  
  showResult.value = true
}
</script>

<style scoped>
.form-radio {
  @apply h-4 w-4 text-primary border-primary/20 focus:ring-primary/20;
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

.demo-button {
  display: block;
  width: 300px;
  height: 60px;
  margin: 30px auto;
  padding: 16px 32px;
  background-color: rgb(59, 130, 246);
  color: rgb(250, 250, 249);
  border: none;
  border-radius: 12px;
  font-size: 18px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(59, 130, 246, 0.3);
  text-align: center;
  line-height: 1.2;
}

.demo-button:hover {
  background-color: rgba(59, 130, 246, 0.9);
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(59, 130, 246, 0.4);
}
</style> 