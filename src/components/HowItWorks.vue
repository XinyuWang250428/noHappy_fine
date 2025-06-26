<script setup lang="ts">
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Badge } from "@/components/ui/badge";

interface HowItWorksProps {
  badgeTitle: string;
  title: string;
  description: string;
  image: string;
}

const HowItWorksList: HowItWorksProps[] = [
  {
    badgeTitle: "步骤一",
    title: "阅读说明书",
    description:
      "了解测试步骤、操作流程，为测试做好准备",
    image: "roboto.png",
  },
  {
    badgeTitle: "步骤二",
    title: "检查设备",
    description:
      "确保摄像头等功能可以被正常使用",
    image: "runner.png",
  },
  {
    badgeTitle: "步骤三",
    title: "开始测试",
    description:
      "根据说明书的指示，导入相关文件或进行相应操作",
    image: "pacheco.png",
  },
  {
    badgeTitle: "步骤四",
    title: "查看报告",
    description:
      "查看基于测试分析的专业报告，通过高度可视化的结果了解初步的干预措施建议",
    image: "gamestation.png",
  },
];
</script>

<template>
  <section
    id="features"
    class="container py-24 sm:py-32"
  >
    <div class="text-center mb-8">
      <h2 class="text-lg text-primary text-center mb-2 tracking-wider">
        工作流程
      </h2>

      <h2 class="text-3xl md:text-4xl text-center font-bold">
        分步操作流程
      </h2>
    </div>

    <div class="lg:w-[80%] mx-auto relative">
      <div
        v-for="(
          { badgeTitle, title, description, image }, index
        ) in HowItWorksList"
        :key="title"
        :class="[
          'flex mb-8 items-center',
          { ' flex-row-reverse': index % 2 !== 0 },
        ]"
      >
        <Card class="h-full bg-transparent border-0 shadow-none">
          <CardHeader>
            <div class="pb-4">
              <Badge>{{ badgeTitle }}</Badge>
            </div>

            <CardTitle>
              {{ title }}
            </CardTitle>
          </CardHeader>

          <CardContent class="text-muted-foreground w-[80%]">
            {{ description }}
          </CardContent>
        </Card>

        <img
          :src="image"
          :alt="`Image describing ${title} `"
          className="w-[150px]  md:w-[250px] lg:w-[300px] mx-auto -scale-x-100 "
        />
        <div
          :class="[
            '-z-10 absolute right-0 w-44 h-72  lg:w-64 lg:h-80 rounded-full bg-primary/15 dark:bg-primary/10 blur-3xl',
            {
              'left-0': index % 2 !== 0,
            },
          ]"
        ></div>
      </div>
    </div>
  </section>
</template>
