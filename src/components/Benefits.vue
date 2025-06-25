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
    title: "建立品牌信任",
    description:
      "通过专业的设计和优质的内容，建立用户对品牌的信任和认可。",
  },
  {
    icon: "lineChart",
    title: "更多潜在客户",
    description:
      "优化的转化漏斗和精准的目标受众定位，帮助您获取更多优质潜在客户。",
  },
  {
    icon: "wallet",
    title: "提高转化率",
    description:
      "通过数据驱动的优化和A/B测试，不断提升您的营销转化效果。",
  },
  {
    icon: "sparkle",
    title: "测试营销创意",
    description:
      "快速验证您的营销创意和策略，找到最适合您业务的营销方案。",
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
          您成功的捷径
        </h2>
        <p class="text-xl text-muted-foreground mb-8">
          我们提供全方位的营销解决方案，帮助您的业务快速增长，
          让您在竞争中占据优势。
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
