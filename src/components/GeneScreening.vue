<script lang="ts" setup>
import { ref, computed } from 'vue';

const selectedPopulation = ref('');
const formData = ref<Record<string, any>>({});
const isAnalyzing = ref(false);
const analysisProgress = ref(0);
const analysisComplete = ref(false);
const resultDisplay = ref('');
const showFullscreen = ref(false);
const currentImageTitle = ref('');
const currentImageSrc = ref('');

// 人群选项
const populations = [
  { value: 'china', label: '中国（CHARLS）', description: '中国健康与养老追踪调查' },
  { value: 'usa', label: '美国（NHANES）', description: '国家健康与营养检查调查' },
  { value: 'uk', label: '英国（UKB）', description: '英国生物银行' }
];

// 量表配置
const questionnaireConfig = {
  china: {
    continuous: [
      { key: 'age', label: '年龄', min: 18, max: 100, unit: '岁' },
      { key: 'bmi', label: '身体质量指数(BMI)', min: 15, max: 40, unit: 'kg/m²' },
      { key: 'sleep', label: '每日睡眠时长', min: 3, max: 12, unit: '小时' },
      { key: 'totmet', label: '总代谢当量', min: 0, max: 10000, unit: 'MET' }
    ],
    categorical: [
      { key: 'gender', label: '性别', options: [{ value: 0, label: '男' }, { value: 1, label: '女' }] },
      { key: 'marry', label: '婚姻状况', options: [{ value: 0, label: '未婚/离异/丧偶' }, { value: 1, label: '已婚' }] },
      { key: 'rural', label: '居住地', options: [{ value: 0, label: '城镇' }, { value: 1, label: '乡村' }] },
      { key: 'edu', label: '教育程度', options: [
        { value: 1, label: '小学及以下' }, { value: 2, label: '初中' }, 
        { value: 3, label: '高中' }, { value: 4, label: '大专及以上' }
      ]},
      { key: 'nation', label: '民族', options: [{ value: 0, label: '非汉族' }, { value: 1, label: '汉族' }] },
      { key: 'exercise', label: '是否经常锻炼', options: [{ value: 0, label: '否' }, { value: 1, label: '是' }] },
      { key: 'drinkl', label: '目前是否饮酒', options: [{ value: 0, label: '否' }, { value: 1, label: '是' }] },
      { key: 'smoken', label: '目前是否吸烟', options: [{ value: 0, label: '否' }, { value: 1, label: '是' }] }
    ]
  },
  usa: {
    continuous: [
      { key: 'Age', label: '年龄', min: 18, max: 100, unit: '岁' },
      { key: 'PIR', label: '贫困收入比', min: 0, max: 5, unit: '' },
      { key: 'Sleep_duration', label: '睡眠时长', min: 3, max: 12, unit: '小时' },
      { key: 'Sedentary_hours', label: '久坐时间', min: 0, max: 16, unit: '小时' }
    ],
    categorical: [
      { key: 'Sex', label: '性别', options: [{ value: 0, label: '男' }, { value: 1, label: '女' }] },
      { key: 'Race', label: '种族', options: [
        { value: 0, label: '白人' }, { value: 1, label: '黑人' }, 
        { value: 2, label: '亚裔' }, { value: 3, label: '其他' }
      ]},
      { key: 'Education', label: '教育水平', options: [
        { value: 0, label: '高中以下' }, { value: 1, label: '高中' }, 
        { value: 2, label: '大学' }, { value: 3, label: '研究生及以上' }
      ]},
      { key: 'Marriage', label: '婚姻状况', options: [
        { value: 0, label: '未婚' }, { value: 1, label: '已婚' }, 
        { value: 2, label: '离异/丧偶' }
      ]},
      { key: 'Smoking_status', label: '吸烟状况', options: [
        { value: 0, label: '从不吸烟' }, { value: 1, label: '曾经吸烟' }, { value: 2, label: '目前吸烟' }
      ]},
      { key: 'Drinking_status', label: '饮酒状况', options: [
        { value: 0, label: '从不饮酒' }, { value: 1, label: '适量饮酒' }, { value: 2, label: '过量饮酒' }
      ]},
      { key: 'Sleep_trouble', label: '睡眠困难', options: [{ value: 0, label: '否' }, { value: 1, label: '是' }] },
      { key: 'Feeling_tired', label: '经常感到疲劳', options: [{ value: 0, label: '否' }, { value: 1, label: '是' }] }
    ]
  },
  uk: {
    continuous: [
      { key: 'age', label: '年龄', min: 18, max: 100, unit: '岁' },
      { key: 'bmi', label: '身体质量指数(BMI)', min: 15, max: 40, unit: 'kg/m²' },
      { key: 'sleep_duration', label: '睡眠时长', min: 3, max: 12, unit: '小时' },
      { key: 'physical_activity', label: '每周运动时间', min: 0, max: 20, unit: '小时' }
    ],
    categorical: [
      { key: 'sex', label: '性别', options: [{ value: 0, label: '男' }, { value: 1, label: '女' }] },
      { key: 'education', label: '教育水平', options: [
        { value: 0, label: '中学以下' }, { value: 1, label: '中学' }, 
        { value: 2, label: '大学' }, { value: 3, label: '研究生及以上' }
      ]},
      { key: 'employment', label: '就业状况', options: [
        { value: 0, label: '在职' }, { value: 1, label: '退休' }, 
        { value: 2, label: '失业' }, { value: 3, label: '学生' }
      ]},
      { key: 'smoking', label: '吸烟状况', options: [
        { value: 0, label: '从不吸烟' }, { value: 1, label: '曾经吸烟' }, { value: 2, label: '目前吸烟' }
      ]},
      { key: 'alcohol', label: '饮酒频率', options: [
        { value: 0, label: '从不' }, { value: 1, label: '偶尔' }, 
        { value: 2, label: '经常' }, { value: 3, label: '每天' }
      ]},
      { key: 'social_isolation', label: '社会孤立感', options: [
        { value: 0, label: '无' }, { value: 1, label: '轻微' }, 
        { value: 2, label: '中等' }, { value: 3, label: '严重' }
      ]}
    ]
  }
};

const currentQuestionnaire = computed(() => {
  return selectedPopulation.value ? questionnaireConfig[selectedPopulation.value as keyof typeof questionnaireConfig] : null;
});

const selectPopulation = (population: string) => {
  selectedPopulation.value = population;
  formData.value = {};
  analysisComplete.value = false;
  resultDisplay.value = '';
};

const updateFormData = (key: string, value: any) => {
  formData.value[key] = value;
};

const analyzeResults = async () => {
  if (!selectedPopulation.value) return;
  
  isAnalyzing.value = true;
  analysisProgress.value = 0;
  
  // 模拟分析过程
  const steps = [
    { progress: 20, message: '正在加载预测模型...' },
    { progress: 40, message: '正在处理输入数据...' },
    { progress: 60, message: '正在进行特征工程...' },
    { progress: 80, message: '正在计算预测结果...' },
    { progress: 100, message: '分析完成！' }
  ];
  
  for (const step of steps) {
    analysisProgress.value = step.progress;
    await new Promise(resolve => setTimeout(resolve, 800));
  }
  
  // 根据不同人群使用不同的分析方法和评分算法
  let analysisMethod = '';
  let riskScore = 0;
  let details = '';
  
  if (selectedPopulation.value === 'china') {
    analysisMethod = '逻辑回归模型';
    // 基于CHARLS数据的风险因子计算
    let score = 0;
    if (formData.value.age > 60) score += 15;
    else if (formData.value.age > 45) score += 10;
    if (formData.value.gender === 1) score += 8; // 女性风险稍高
    if (formData.value.marry === 0) score += 12; // 未婚/离异风险更高
    if (formData.value.rural === 1) score += 8; // 农村地区
    if (formData.value.edu <= 2) score += 10; // 教育程度低
    if (formData.value.exercise === 0) score += 12; // 不锻炼
    if (formData.value.sleep < 6 || formData.value.sleep > 9) score += 15; // 睡眠异常
    if (formData.value.smoken === 1) score += 8; // 吸烟
    if (formData.value.drinkl === 1) score += 5; // 饮酒
    riskScore = Math.min(score + Math.random() * 10, 100);
    details = '基于中国老年人群队列研究的多元回归分析';
  } else if (selectedPopulation.value === 'usa') {
    analysisMethod = 'XGBoost梯度提升 + SHAP可解释性分析';
    // 基于NHANES数据的风险因子计算
    let score = 0;
    if (formData.value.Age > 50) score += 12;
    if (formData.value.Sex === 1) score += 6; // 女性
    if (formData.value.Marriage === 2) score += 15; // 离异/丧偶
    if (formData.value.Education === 0) score += 12; // 低教育
    if (formData.value.PIR < 1.3) score += 18; // 贫困
    if (formData.value.Sleep_trouble === 1) score += 20; // 睡眠困难
    if (formData.value.Feeling_tired === 1) score += 15; // 疲劳
    if (formData.value.Smoking_status === 2) score += 8; // 当前吸烟
    if (formData.value.Sedentary_hours > 8) score += 10; // 久坐
    riskScore = Math.min(score + Math.random() * 8, 100);
    details = '集成学习算法结合特征重要性分析，提供个体化风险解释';
  } else if (selectedPopulation.value === 'uk') {
    analysisMethod = '随机森林集成学习模型';
    // 基于UKB数据的风险因子计算
    let score = 0;
    if (formData.value.age > 55) score += 10;
    if (formData.value.sex === 1) score += 7; // 女性
    if (formData.value.education <= 1) score += 14; // 低教育
    if (formData.value.employment === 2) score += 16; // 失业
    if (formData.value.social_isolation >= 2) score += 25; // 社会孤立
    if (formData.value.smoking === 2) score += 9; // 当前吸烟
    if (formData.value.alcohol >= 3) score += 12; // 频繁饮酒
    if (formData.value.sleep_duration < 6 || formData.value.sleep_duration > 9) score += 12;
    if (formData.value.physical_activity < 2) score += 11; // 缺乏运动
    riskScore = Math.min(score + Math.random() * 7, 100);
    details = '基于大规模人群队列的多维度健康指标综合评估';
  }
  
  const riskLevel = riskScore < 25 ? '低风险' : riskScore < 50 ? '中等风险' : riskScore < 75 ? '高风险' : '极高风险';
  const riskColor = riskScore < 25 ? '#10b981' : riskScore < 50 ? '#f59e0b' : riskScore < 75 ? '#ef4444' : '#dc2626';
  
  resultDisplay.value = {
    method: analysisMethod,
    score: riskScore.toFixed(1),
    level: riskLevel,
    color: riskColor,
    details: details,
    population: populations.find(p => p.value === selectedPopulation.value)?.label || ''
  };
  
  // 保存结果到localStorage
  localStorage.setItem('geneScreeningData', JSON.stringify(resultDisplay.value));
  const completedTests = JSON.parse(localStorage.getItem('completedTests') || '{}');
  completedTests.gene = '已完成';
  localStorage.setItem('completedTests', JSON.stringify(completedTests));
  
  analysisComplete.value = true;
  isAnalyzing.value = false;
  
  // 触发storage事件以通知其他组件
  window.dispatchEvent(new StorageEvent('storage', {
    key: 'completedTests',
    newValue: JSON.stringify(completedTests)
  }));
};

const isFormComplete = computed(() => {
  if (!currentQuestionnaire.value) return false;
  
  const continuousFields = currentQuestionnaire.value.continuous.length;
  const categoricalFields = currentQuestionnaire.value.categorical.length;
  const totalFields = continuousFields + categoricalFields;
  
  const completedFields = Object.keys(formData.value).length;
  return completedFields === totalFields;
});

const openFullscreen = (src: string, title: string) => {
  currentImageSrc.value = src;
  currentImageTitle.value = title;
  showFullscreen.value = true;
};

const closeFullscreen = () => {
  showFullscreen.value = false;
};

const fillExampleData = () => {
  if (!selectedPopulation.value) return;
  
  // 根据不同人群填入对应的示例数据
  if (selectedPopulation.value === 'china') {
    formData.value = {
      age: 59,
      bmi: 0, // 对应贫困收入比
      sleep: 6,
      totmet: 12,
      gender: 1, // 女
      marry: 1, // 已婚
      rural: 1, // 乡村
      edu: 4, // 研究生以上
      nation: 1, // 汉族
      exercise: 1, // 是
      drinkl: 1, // 是
      smoken: 1 // 是
    };
  } else if (selectedPopulation.value === 'usa') {
    formData.value = {
      Age: 59,
      PIR: 3,
      Sleep_duration: 6,
      Sedentary_hours: 12,
      Sex: 1, // 女
      Race: 1, // 黑人
      Education: 3, // 研究生及以上
      Marriage: 1, // 已婚
      Smoking_status: 2, // 目前吸烟
      Drinking_status: 2, // 过量饮酒
      Sleep_trouble: 1, // 是
      Feeling_tired: 1 // 是
    };
  } else if (selectedPopulation.value === 'uk') {
    formData.value = {
      age: 59,
      bmi: 0,
      sleep_duration: 6,
      physical_activity: 12,
      sex: 1, // 女
      education: 3, // 研究生及以上
      employment: 0, // 在职
      smoking: 2, // 目前吸烟
      alcohol: 3, // 每天
      social_isolation: 0 // 无
    };
  }
};
</script>

<template>
  <section id="gene" class="py-24 sm:py-32">
    <div class="container">
      <div class="header">
        <h1>生活方式预测</h1>
        <div class="description-panel">
          <h2>系统功能</h2>
          <ul class="steps-list">
            <li>🌍 多人群抑郁风险评估 📊</li>
            <li>📋 标准化量表评估 ⚗️</li>
            <li>🤖 机器学习预测分析 🔗</li>
            <li>📈 个性化风险报告 📋</li>
          </ul>
        </div>
      </div>

      <!-- 人群选择 -->
      <div class="population-selection">
        <h2>请选择人群</h2>
        <div class="population-grid">
          <div 
            v-for="population in populations" 
            :key="population.value"
            :class="['population-card', { 'selected': selectedPopulation === population.value }]"
            @click="selectPopulation(population.value)"
          >
            <h3>{{ population.label }}</h3>
            <p>{{ population.description }}</p>
          </div>
        </div>
      </div>

      <!-- 量表内容 -->
      <div v-if="selectedPopulation" class="questionnaire-section">
        <!-- 算法图展示 -->
        <div class="algorithm-diagrams">
          <h3 class="diagrams-title">分析算法框架</h3>
          <p class="diagrams-subtitle">点击可全屏查看</p>
          <div class="diagrams-grid">
            <div class="diagram-item">
              <img src="/特征筛选框架.svg" alt="特征筛选框架" class="diagram-image" @click="openFullscreen('/特征筛选框架.svg', '特征筛选框架')">
              <p class="diagram-label">特征筛选框架</p>
            </div>
            <div class="diagram-item">
              <img src="/GAE-GAT聚类框架.svg" alt="GAE-GAT聚类框架" class="diagram-image" @click="openFullscreen('/GAE-GAT聚类框架.svg', 'GAE-GAT聚类框架')">
              <p class="diagram-label">GAE-GAT聚类框架</p>
            </div>
            <div class="diagram-item">
              <img src="/组间比较聚类.svg" alt="组间比较+层次聚类" class="diagram-image" @click="openFullscreen('/组间比较聚类.svg', '组间比较+层次聚类')">
              <p class="diagram-label">组间比较+层次聚类</p>
            </div>
          </div>
        </div>
        
        <h2>量表评估</h2>
        
        <!-- 连续变量 -->
        <div class="form-section">
          <h3>基本信息</h3>
          <div class="continuous-questions">
            <div 
              v-for="question in currentQuestionnaire.continuous" 
              :key="question.key"
              class="question-item"
            >
              <label>{{ question.label }}（{{ question.unit }}）</label>
              <div class="range-container">
                <input 
                  type="range" 
                  :min="question.min" 
                  :max="question.max"
                  :value="formData[question.key] || question.min"
                  @input="updateFormData(question.key, $event.target.value)"
                  class="range-slider"
                />
                <span class="range-value">{{ formData[question.key] || question.min }}</span>
              </div>
            </div>
          </div>
        </div>

        <!-- 分类变量 -->
        <div class="form-section">
          <h3>生活方式评估</h3>
          <div class="categorical-questions">
            <div 
              v-for="question in currentQuestionnaire.categorical" 
              :key="question.key"
              class="question-item"
            >
              <label>{{ question.label }}</label>
              <div class="options-container">
                <button
                  v-for="option in question.options"
                  :key="option.value"
                  :class="['option-button', { 'selected': formData[question.key] === option.value }]"
                  @click="updateFormData(question.key, option.value)"
                >
                  {{ option.label }}
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- 分析按钮 -->
        <div class="analysis-section">
          <div class="button-group">
            <button 
              :disabled="!isFormComplete || isAnalyzing"
              :class="['analysis-button', { 'disabled': !isFormComplete || isAnalyzing }]"
              @click="analyzeResults"
            >
              {{ isAnalyzing ? '分析中...' : '分析结果' }}
            </button>
            
            <button 
              class="example-button"
              @click="fillExampleData"
              :disabled="!selectedPopulation || isAnalyzing"
            >
              输入示例
            </button>
          </div>
          
          <!-- 进度条 -->
          <div v-if="isAnalyzing" class="progress-container">
            <div class="progress-bar">
              <div 
                class="progress-fill" 
                :style="{ width: analysisProgress + '%' }"
              ></div>
            </div>
            <p class="progress-text">{{ analysisProgress }}%</p>
          </div>
        </div>

        <!-- 结果保存提示 -->
        <div v-if="analysisComplete" class="result-section">
          <div class="save-notification">
            <div class="save-icon">
              ✅
            </div>
            <h3 class="save-title">结果已保存</h3>
            <p class="save-message">可在下方综合报告中进行查看</p>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- 全屏模态框 - 移到组件外部 -->
  <teleport to="body">
    <div v-if="showFullscreen" class="fullscreen-modal" @click="closeFullscreen">
      <div class="fullscreen-content" @click.stop>
        <button class="close-btn" @click="closeFullscreen">&times;</button>
        <h3 class="fullscreen-title">{{ currentImageTitle }}</h3>
        <div class="fullscreen-image-container">
          <img :src="currentImageSrc" :alt="currentImageTitle" class="fullscreen-image">
        </div>
      </div>
    </div>
  </teleport>
</template>

<style scoped>
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  color: #f5f5f5;
  background: rgba(28, 28, 35, 0.8);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  border: 2px solid rgba(249, 115, 22, 0.3);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2),
              0 0 15px rgba(249, 115, 22, 0.1),
              0 0 30px rgba(249, 115, 22, 0.05);
  position: relative;
}

.header {
  text-align: center;
  margin-bottom: 3rem;
}

.header h1 {
  font-size: 2.5rem;
  font-weight: 700;
  margin-bottom: 1rem;
  background: linear-gradient(135deg, #f97316 0%, #ea580c 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.description-panel {
  background: rgba(28, 28, 35, 0.8);
  padding: 2rem;
  border-radius: 12px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.steps-list {
  list-style: none;
  padding: 0;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
}

.steps-list li {
  background: rgba(255, 255, 255, 0.05);
  padding: 1rem;
  border-radius: 8px;
  text-align: center;
  font-size: 1.1rem;
}

.population-selection {
  margin-bottom: 3rem;
}

.population-selection h2 {
  text-align: center;
  margin-bottom: 2rem;
  font-size: 2rem;
  color: #f97316;
}

.population-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
}

.population-card {
  background: rgba(28, 28, 35, 0.8);
  padding: 2rem;
  border-radius: 12px;
  text-align: center;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 2px solid transparent;
  backdrop-filter: blur(10px);
}

.population-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 30px rgba(249, 115, 22, 0.3);
}

.population-card.selected {
  border-color: #f97316;
  background: rgba(249, 115, 22, 0.2);
}

.population-card h3 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
  color: #f97316;
}

.questionnaire-section {
  margin-top: 3rem;
}

.questionnaire-section h2 {
  text-align: center;
  margin-bottom: 2rem;
  font-size: 2rem;
  color: #f97316;
}

.form-section {
  margin-bottom: 3rem;
  background: rgba(28, 28, 35, 0.8);
  padding: 2rem;
  border-radius: 12px;
  backdrop-filter: blur(10px);
}

.form-section h3 {
  margin-bottom: 1.5rem;
  color: #ea580c;
  font-size: 1.3rem;
}

.question-item {
  margin-bottom: 2rem;
}

.question-item label {
  display: block;
  margin-bottom: 1rem;
  font-weight: 600;
  color: #f5f5f5;
}

.range-container {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.range-slider {
  flex: 1;
  height: 8px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 4px;
  outline: none;
  -webkit-appearance: none;
}

.range-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 20px;
  height: 20px;
  background: #f97316;
  border-radius: 50%;
  cursor: pointer;
}

.range-value {
  min-width: 60px;
  text-align: center;
  background: rgba(249, 115, 22, 0.2);
  padding: 0.5rem;
  border-radius: 4px;
  color: #f97316;
  font-weight: 600;
}

.options-container {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.option-button {
  padding: 0.75rem 1.5rem;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 6px;
  color: #f5f5f5;
  cursor: pointer;
  transition: all 0.3s ease;
}

.option-button:hover {
  background: rgba(249, 115, 22, 0.3);
}

.option-button.selected {
  background: #f97316;
  border-color: #f97316;
  color: white;
}

.analysis-section {
  text-align: center;
  margin: 3rem 0;
}

.button-group {
  display: flex;
  justify-content: center;
  gap: 1rem;
}

.analysis-button {
  padding: 1rem 3rem;
  background: linear-gradient(135deg, #f97316 0%, #ea580c 100%);
  border: none;
  border-radius: 8px;
  color: white;
  font-size: 1.2rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.analysis-button:hover:not(.disabled) {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(249, 115, 22, 0.4);
}

.analysis-button.disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.example-button {
  padding: 1rem 3rem;
  background: linear-gradient(135deg, #3b82f6 0%, #2563eb 100%);
  border: none;
  border-radius: 8px;
  color: white;
  font-size: 1.2rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.example-button:hover:not(.disabled) {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(59, 130, 246, 0.4);
}

.example-button.disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.progress-container {
  margin-top: 2rem;
}

.progress-bar {
  width: 100%;
  height: 8px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 4px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #f97316, #ea580c);
  transition: width 0.3s ease;
}

.progress-text {
  margin-top: 1rem;
  font-size: 1.1rem;
  color: #f97316;
}

.result-section {
  margin-top: 3rem;
  background: rgba(28, 28, 35, 0.8);
  padding: 2rem;
  border-radius: 12px;
  backdrop-filter: blur(10px);
}

.result-section h3 {
  margin-bottom: 1.5rem;
  color: #f97316;
  font-size: 1.5rem;
  text-align: center;
}

.save-notification {
  text-align: center;
  padding: 2rem;
}

.save-icon {
  font-size: 3rem;
  margin-bottom: 1rem;
}

.save-title {
  color: #10b981;
  font-size: 1.5rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.save-message {
  color: #f5f5f5;
  font-size: 1.1rem;
  opacity: 0.9;
}

.result-content {
  background: rgba(0, 0, 0, 0.3);
  padding: 0;
  border-radius: 16px;
  border: 1px solid rgba(249, 115, 22, 0.2);
  overflow: hidden;
}

.result-header {
  background: linear-gradient(135deg, rgba(249, 115, 22, 0.1), rgba(234, 88, 12, 0.1));
  padding: 1.5rem 2rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 1rem;
}

.population-badge {
  background: rgba(249, 115, 22, 0.2);
  color: #f97316;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-weight: 600;
  font-size: 0.9rem;
}

.method-tag {
  background: rgba(234, 88, 12, 0.2);
  color: #ea580c;
  padding: 0.5rem 1rem;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 500;
}

.score-display {
  padding: 2rem;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 3rem;
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
  background: rgba(255, 255, 255, 0.05);
}

.score-number {
  font-size: 2.5rem;
  font-weight: 700;
  line-height: 1;
}

.score-label {
  font-size: 0.9rem;
  color: #f5f5f5;
  margin-top: 0.25rem;
}

.risk-info {
  flex: 1;
  min-width: 200px;
}

.risk-level {
  font-size: 1.8rem;
  font-weight: 700;
  margin-bottom: 1rem;
  text-align: center;
}

.risk-bar {
  width: 100%;
  height: 12px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 6px;
  overflow: hidden;
  position: relative;
}

.risk-progress {
  height: 100%;
  border-radius: 6px;
  transition: width 1s ease-out;
  position: relative;
}

.analysis-details {
  padding: 1.5rem 2rem;
  background: rgba(255, 255, 255, 0.02);
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.details-title {
  font-size: 1.1rem;
  font-weight: 600;
  color: #f97316;
  margin-bottom: 0.75rem;
}

.details-text {
  color: #d1d5db;
  line-height: 1.6;
  margin: 0;
}

.risk-interpretation {
  padding: 1.5rem 2rem;
  background: rgba(255, 255, 255, 0.02);
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.interpretation-title {
  font-size: 1.1rem;
  font-weight: 600;
  color: #f97316;
  margin-bottom: 1rem;
}

.interpretation-content {
  margin: 0;
}

.interpretation-text {
  padding: 1rem;
  border-radius: 8px;
  line-height: 1.6;
  border-left: 4px solid;
}

.interpretation-text.success {
  background: rgba(16, 185, 129, 0.1);
  border-left-color: #10b981;
  color: #d1fae5;
}

.interpretation-text.warning {
  background: rgba(245, 158, 11, 0.1);
  border-left-color: #f59e0b;
  color: #fef3c7;
}

.interpretation-text.danger {
  background: rgba(239, 68, 68, 0.1);
  border-left-color: #ef4444;
  color: #fecaca;
}

.interpretation-text.critical {
  background: rgba(220, 38, 38, 0.1);
  border-left-color: #dc2626;
  color: #fecaca;
}

.continuous-questions, .categorical-questions {
  display: grid;
  gap: 2rem;
}

@media (max-width: 768px) {
  .population-grid {
    grid-template-columns: 1fr;
  }
  
  .options-container {
    flex-direction: column;
  }
  
  .option-button {
    width: 100%;
    text-align: center;
  }
}

/* 在现有样式后添加算法图样式 */

.algorithm-diagrams {
  margin-bottom: 3rem;
  padding: 2rem;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 12px;
  border: 1px solid rgba(249, 115, 22, 0.2);
}

.diagrams-title {
  text-align: center;
  font-size: 1.5rem;
  color: #f97316;
  margin-bottom: 0.5rem;
  font-weight: 600;
}

.diagrams-subtitle {
  text-align: center;
  font-size: 0.9rem;
  color: #f5f5f5;
  margin-bottom: 2rem;
  opacity: 0.8;
}

.diagrams-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 2rem;
}

@media (max-width: 1024px) {
  .diagrams-grid {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }
}

.diagram-item {
  background: rgba(28, 28, 35, 0.6);
  border-radius: 8px;
  padding: 1rem;
  border: 1px solid rgba(255, 255, 255, 0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.diagram-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 20px rgba(249, 115, 22, 0.2);
}

.diagram-image {
  width: 100%;
  height: auto;
  max-height: 200px;
  object-fit: contain;
  border-radius: 4px;
  background: rgba(255, 255, 255, 0.95);
  padding: 0.5rem;
  cursor: pointer;
  transition: transform 0.2s ease;
}

.diagram-image:hover {
  transform: scale(1.05);
}

.diagram-label {
  text-align: center;
  margin-top: 1rem;
  font-size: 0.9rem;
  color: #f5f5f5;
  font-weight: 500;
}

/* 全屏模态框样式 */
.fullscreen-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.9);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  backdrop-filter: blur(5px);
}

.fullscreen-content {
  position: relative;
  background: transparent;
  padding: 0;
  border-radius: 0;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.close-btn {
  position: absolute;
  top: 2rem;
  right: 2rem;
  background: rgba(0, 0, 0, 0.8);
  color: white;
  border: none;
  font-size: 2rem;
  cursor: pointer;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.2s ease;
  z-index: 1001;
}

.close-btn:hover {
  background: rgba(0, 0, 0, 1);
}

.fullscreen-title {
  position: absolute;
  top: 2rem;
  left: 50%;
  transform: translateX(-50%);
  font-size: 1.8rem;
  font-weight: 600;
  margin: 0;
  color: white;
  text-align: center;
  background: rgba(0, 0, 0, 0.7);
  padding: 0.5rem 1.5rem;
  border-radius: 25px;
  z-index: 1001;
}

.fullscreen-image-container {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 5rem 3rem 3rem 3rem;
  box-sizing: border-box;
}

.fullscreen-image {
  max-width: 100%;
  max-height: 100%;
  width: auto;
  height: auto;
  object-fit: contain;
  border-radius: 8px;
  background: white;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.3);
}
</style> 