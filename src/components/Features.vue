<script setup lang="ts">
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";

import {
  Paintbrush,
  MessageCircle,
  TabletSmartphone,
  BadgeCheck,
  Goal,
  PictureInPicture,
  MousePointerClick,
  Newspaper,
} from "lucide-vue-next";

interface FeaturesProps {
  icon: string;
  title: string;
  description: string;
}

const featureList: FeaturesProps[] = [
  {
    icon: "tabletSmartphone",
    title: "移动端友好",
    description:
      "完全响应式设计，确保在所有设备上都能完美展示。",
  },
  {
    icon: "badgeCheck",
    title: "社会认可",
    description:
      "展示用户评价和社交证明，增强品牌可信度。",
  },
  {
    icon: "goal",
    title: "目标内容",
    description:
      "为目标受众提供量身定制的内容和解决方案。",
  },
  {
    icon: "pictureInPicture",
    title: "强大的视觉效果",
    description:
      "使用高质量的图像和动画，提升用户体验。",
  },
  {
    icon: "mousePointerClick",
    title: "清晰的行动号召",
    description:
      "优化转化率的按钮设计和文案策略。",
  },
  {
    icon: "newspaper",
    title: "清晰的标题",
    description:
      "简洁有力的标题传达核心价值主张。",
  },
];

const iconMap: Record<
  string,
  | typeof TabletSmartphone
  | typeof BadgeCheck
  | typeof Goal
  | typeof PictureInPicture
  | typeof Paintbrush
  | typeof MousePointerClick
  | typeof MessageCircle
  | typeof Newspaper
> = {
  tabletSmartphone: TabletSmartphone,
  badgeCheck: BadgeCheck,
  goal: Goal,
  pictureInPicture: PictureInPicture,
  paintbrush: Paintbrush,
  mousePointerClick: MousePointerClick,
  messageCircle: MessageCircle,
  newspaper: Newspaper,
};
</script>

<template>
  <section
    id="features"
    class="container py-24 sm:py-32"
  >
    <h2 class="text-lg text-primary text-center mb-2 tracking-wider">
      功能特点
    </h2>

    <h2 class="text-3xl md:text-4xl text-center font-bold mb-4">
      我们的与众不同
    </h2>

    <h3 class="md:w-1/2 mx-auto text-xl text-center text-muted-foreground mb-8">
      探索我们独特的功能特点，这些创新设计将帮助您的业务脱颖而出。
    </h3>

    <div class="grid sm:grid-cols-2 lg:grid-cols-3 gap-4">
      <div
        v-for="{ icon, title, description } in featureList"
        :key="title"
      >
        <Card class="h-full bg-background border-0 shadow-none">
          <CardHeader class="flex justify-center items-center">
            <div
              class="bg-primary/20 p-2 rounded-full ring-8 ring-primary/10 mb-4"
            >
              <component
                :is="iconMap[icon]"
                class="size-6 text-primary"
              />
            </div>

            <CardTitle>
              {{ title }}
            </CardTitle>
          </CardHeader>

          <CardContent class="text-muted-foreground text-center">
            {{ description }}
          </CardContent>
        </Card>
      </div>
    </div>
  </section>
</template>

<style lang="less" scoped></style>
