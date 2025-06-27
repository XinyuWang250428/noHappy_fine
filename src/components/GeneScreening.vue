<script lang="ts" setup>
import { ref, onMounted } from 'vue';

const geneFile = ref<HTMLInputElement | null>(null);
const fileName = ref('未选择文件');
const csvContent = ref('');
const resultArea = ref(false);
const resultDisplay = ref('');
const showReturnButton = ref(false);

const handleFileChange = (event: Event) => {
  const target = event.target as HTMLInputElement;
  const file = target.files?.[0];
  
  if (file) {
    fileName.value = file.name;
    
    const reader = new FileReader();
    reader.onload = (e) => {
      try {
        const content = e.target?.result as string;
        displayCsvAsTable(content);
      } catch (error) {
        csvContent.value = '读取文件时发生错误：' + (error as Error).message;
      }
    };
    reader.onerror = () => {
      csvContent.value = '读取文件失败';
    };
    reader.readAsText(file);
  } else {
    fileName.value = '未选择文件';
    csvContent.value = '';
  }
};

const displayCsvAsTable = (csvContentStr: string) => {
  const lines = csvContentStr.split('\n');
  if (lines.length === 0) {
    csvContent.value = 'CSV文件内容为空。';
    return;
  }

  let tableHtml = '<table style="width: 100%; border-collapse: collapse;">';
  
  // 假设第一行是标题
  const headers = lines[0].split(',');
  tableHtml += '<thead><tr>';
  headers.forEach(header => {
    tableHtml += `<th style="border: 1px solid #ddd; padding: 8px; background-color: #f2f2f2;">${header.trim()}</th>`;
  });
  tableHtml += '</tr></thead><tbody>';

  // 处理数据行
  for (let i = 1; i < lines.length; i++) {
    const line = lines[i].trim();
    if (line === '') continue;
    
    const columns = line.split(',');
    tableHtml += '<tr>';
    columns.forEach(column => {
      tableHtml += `<td style="border: 1px solid #ddd; padding: 8px;">${column.trim()}</td>`;
    });
    tableHtml += '</tr>';
  }

  tableHtml += '</tbody></table>';
  csvContent.value = tableHtml;
};

const showResultSections = () => {
  resultArea.value = true;
};

const handleImageSelection = (boxId: string, inputId: string) => {
  const input = document.getElementById(inputId) as HTMLInputElement;
  input?.click();

  input.onchange = (event) => {
    const file = (event.target as HTMLInputElement).files?.[0];
    const box = document.getElementById(boxId);

    if (file && box) {
      box.innerHTML = '';

      if (file.type.startsWith('image/')) {
        const reader = new FileReader();
        reader.onload = (e) => {
          const img = document.createElement('img');
          img.src = e.target?.result as string;
          img.style.maxWidth = '100%';
          img.style.height = 'auto';
          box.appendChild(img);
        };
        reader.readAsDataURL(file);
      } else if (file.type === 'application/pdf') {
        const fileNameElement = document.createElement('p');
        fileNameElement.textContent = `已选择文件: ${file.name}`;
        const messageElement = document.createElement('p');
        messageElement.textContent = "PDF文件无法直接显示，请下载查看。";
        box.appendChild(fileNameElement);
        box.appendChild(messageElement);
      } else {
        box.innerHTML = '<p>不支持的文件格式。</p>';
      }
    }
  };
};

const analyzeResults = () => {
  const randomScore = Math.floor(Math.random() * 9) + 1;
  resultDisplay.value = `抑郁等级评分：${randomScore}级`;
  showReturnButton.value = true;

  const completedTests = JSON.parse(localStorage.getItem('completedTests') || '{}');
  completedTests.gene = `${randomScore}级`;
  localStorage.setItem('completedTests', JSON.stringify(completedTests));
};

const returnToMain = () => {
  localStorage.setItem('lastScreen', 'screening');
  // 在Vue应用中可以使用路由跳转或其他方式处理
  // window.location.href = 'population_risk.html';
};

onMounted(() => {
  // 添加点击监听器
  setTimeout(() => {
    const mendelianBox = document.getElementById('mendelianResultBox');
    const comorbidityBox = document.getElementById('comorbidityResultBox');

    mendelianBox?.addEventListener('click', () => {
      handleImageSelection('mendelianResultBox', 'mendelianImageInput');
    });

    comorbidityBox?.addEventListener('click', () => {
      handleImageSelection('comorbidityResultBox', 'comorbidityImageInput');
    });
  }, 100);
});
</script>

<template>
  <section id="gene-screening" class="py-24 sm:py-32">
    <div class="container">
      <div class="header">
        <h1>基因辅助分析</h1>
        <div class="description-panel">
          <h2>系统功能</h2>
          <ul class="steps-list">
            <li>🧬 基因数据分析与可视化 📊</li>
            <li>🔬 孟德尔随机化评估 ⚗️</li>
            <li>🏥 共病富集分析 📋</li>
            <li>🕸️ 蛋白质网络分析 🔗</li>
          </ul>
        </div>
      </div>

      <div class="control-panel">
        <div class="file-input-wrapper">
          <label class="custom-file-input">
            <span>选择文件</span>
            <input 
              type="file" 
              class="hidden" 
              ref="geneFile"
              @change="handleFileChange"
              accept=".csv"
            >
          </label>
          <div class="file-name">{{ fileName }}</div>
          <div class="file-tip">请选择csv格式的基因数据文件</div>
        </div>
      </div>

      <div class="csv-display" v-html="csvContent"></div>

      <button class="result-button" @click="showResultSections">显示结果</button>

      <div v-if="resultArea" class="result-area">
        <h2>分析结果</h2>
        <div class="result-display-container">
          <div class="result-display-box" id="mendelianResultBox">
            <p>点击选择孟德尔随机化结果图</p>
            <input type="file" class="hidden-file-input" id="mendelianImageInput" accept="image/*,application/pdf">
          </div>
          <div class="result-display-box" id="comorbidityResultBox">
            <p>点击选择共病富集分析和蛋白质分析图</p>
            <input type="file" class="hidden-file-input" id="comorbidityImageInput" accept="image/*,application/pdf">
          </div>
        </div>
      </div>

      <div v-if="resultDisplay" class="result-display">
        {{ resultDisplay }}
      </div>
      
      <button v-if="showReturnButton" class="return-button" @click="returnToMain">
        返回主页
      </button>
    </div>
  </section>
</template>

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

.description-panel {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 15px;
  padding: 20px;
  margin-top: 20px;
  color: white;
}

.description-panel h2 {
  margin-bottom: 15px;
  font-size: 1.5em;
  color: rgba(168, 162, 158, 1);
}

.steps-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.steps-list li {
  margin-bottom: 12px;
  padding-left: 0;
  color: rgba(255, 255, 255, 0.9);
  font-size: 1em;
  line-height: 1.4;
}

.steps-list li::before {
  content: '';
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

.csv-display {
  width: 100%;
  height: 250px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 10px;
  margin-bottom: 30px;
  padding: 15px;
  overflow: auto;
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

.result-area {
  margin-top: 15px;
}

.result-area h2 {
  color: white;
  margin-bottom: 15px;
  font-size: 1.6em;
}

.result-display-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 15px;
  margin-top: 15px;
}

.result-display-box {
  min-height: 300px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 10px;
  padding: 15px;
  text-align: center;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  cursor: pointer;
}

.result-display-box:hover {
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
}

.hidden-file-input {
  display: none;
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