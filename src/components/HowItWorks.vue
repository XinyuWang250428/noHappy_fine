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
    badgeTitle: "贡献",
    title: "每一份贡献都能带来改变",
    description:
      "加入我们的开源社区，共同打造更好的产品，让每一份贡献都有价值。",
    image: "roboto.png",
  },
  {
    badgeTitle: "发现",
    title: "每一个选择都能产生影响",
    description:
      "探索创新的解决方案，发现新的可能性，让每一个决策都充满意义。",
    image: "runner.png",
  },
  {
    badgeTitle: "创新",
    title: "让每一个行动都有意义",
    description:
      "持续创新和改进，推动产品进化，为用户带来更好的体验。",
    image: "pacheco.png",
  },
  {
    badgeTitle: "协作",
    title: "携手应对挑战",
    description:
      "团队协作，共同解决问题，创造更大的价值。",
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
        分步骤流程
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
