<script setup lang="ts">
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";

import {
  Sparkle,
  Tag,
  Paintbrush,
  Blocks,
  LineChart,
  Wallet,
} from "lucide-vue-next";

interface BenefitsProps {
  icon: string;
  title: string;
  description: string;
}

const benefitList: BenefitsProps[] = [
  {
    icon: "blocks",
    title: "透明可信·科学壁垒",
    description:
      "通过可视化的专业机构指导的AI辅助诊疗，建立用户的信任和认可",
  },
  {
    icon: "lineChart",
    title: "模式延展·多元方案",
    description:
      "持续追踪前沿研究，构建可扩展性数字化精神健康生态的基础平台",
  },
  {
    icon: "wallet",
    title: "商业拓展·多元场景",
    description:
      "通过不断迭代提升、交流合作快速衍生企业、保险、临床研究等多类B2B解决方案。",
  },
  {
    icon: "sparkle",
    title: "技术开放·未来潜能",
    description:
      "通过进一步开发、扩张，未来力求扩展为多元数据驱动的全面精神健康服务生态。",
  },
];

const iconMap: Record<
  string,
  | typeof Sparkle
  | typeof Tag
  | typeof Paintbrush
  | typeof Blocks
  | typeof LineChart
  | typeof Wallet
> = {
  sparkle: Sparkle,
  tag: Tag,
  paintbrush: Paintbrush,
  blocks: Blocks,
  lineChart: LineChart,
  wallet: Wallet,
};
</script>

<template>
  <section
    id="benefits"
    class="container py-24 sm:py-32"
  >
    <div class="grid lg:grid-cols-2 place-items-center lg:gap-24">
      <div>
        <h2 class="text-lg text-primary mb-2 tracking-wider">优势</h2>

        <h2 class="text-3xl md:text-4xl font-bold mb-4">
          多模态整合的、科学严谨的个性化平台
        </h2>
        <p class="text-xl text-muted-foreground mb-8">
          基于多维度数据整合与科学保障，我们精准重塑个体精神健康认知，并提供个性化干预方案，让每个人都能主动管理健康。
        </p>
      </div>

      <div class="grid lg:grid-cols-2 gap-4 w-full">
        <Card
          v-for="({ icon, title, description }, index) in benefitList"
          :key="title"
          class="bg-muted/50 dark:bg-card hover:bg-background dark:hover:bg-background transition-all delay-75 group/number"
        >
          <CardHeader>
            <div class="flex justify-between">
              <component
                class="size-8 mb-6 text-primary"
                :is="iconMap[icon]"
              />

              <span
                class="text-5xl text-muted-foreground/15 font-medium transition-all delay-75 group-hover/number:text-muted-foreground/30"
                >0{{ index + 1 }}</span
              >
            </div>

            <CardTitle>{{ title }}</CardTitle>
          </CardHeader>

          <CardContent class="text-muted-foreground">
            {{ description }}
          </CardContent>
        </Card>
      </div>
    </div>
  </section>
</template>
