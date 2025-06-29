<script setup lang="ts">
import {
  Card,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";

import { Badge } from "@/components/ui/badge";

enum ProService {
  YES = 1,
  NO = 0,
}

interface ServiceProps {
  title: string;
  pro: ProService;
  description: string;
}

const serviceList: ServiceProps[] = [
  {
    title: "Face-API.js 情绪识别",
    description:
      "基于深度学习的面部表情识别技术，实时分析七种基本情绪状态。",
    pro: 1,
  },
  {
    title: "心电信号处理算法",
    description:
      "采用数字信号处理技术，分析心率变异性和自主神经系统功能。",
    pro: 1,
  },
  {
    title: "机器学习算法预测",
    description: "运用梯度提升算法结合SHAP可解释性分析，基于生活方式数据预测抑郁风险。",
    pro: 1,
  },
  {
    title: "AI智能诊断模型",
    description: "集成多模态数据的机器学习算法，提供精准的心理健康评估。",
    pro: 1,
  },
];
</script>

<template>
  <section
    id="services"
    class="container py-24 sm:py-32"
  >
    <h2 class="text-lg text-primary text-center mb-2 tracking-wider">
      核心技术
    </h2>

    <h2 class="text-3xl md:text-4xl text-center font-bold mb-4">
      先进的检测技术
    </h2>
    <h3 class="md:w-1/2 mx-auto text-xl text-center text-muted-foreground mb-8 whitespace-nowrap">
      融合人工智能、生物信息学和信号处理技术，为心理健康评估提供科学依据。
    </h3>
    <div class="grid sm:grid-cols-2 lg:grid-cols-3 gap-4"></div>

    <div
      class="grid sm:grid-cols-2 lg:grid-cols-2 gap-4 w-full lg:w-[60%] mx-auto"
    >
      <div
        v-for="{ title, description, pro } in serviceList"
        :key="title"
      >
        <Card class="bg-muted/60 dark:bg-card h-full relative">
          <CardHeader>
            <CardTitle>{{ title }}</CardTitle>
            <CardDescription>{{ description }}</CardDescription>
          </CardHeader>
          <Badge
            v-if="pro === ProService.YES"
            variant="secondary"
            class="absolute -top-2 -right-3"
            >PRO</Badge
          >
        </Card>
      </div>
    </div>
  </section>
</template>
