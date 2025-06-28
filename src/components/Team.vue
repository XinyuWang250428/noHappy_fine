<script setup lang="ts">
import {
  Card,
  CardContent,
  CardHeader,
  CardTitle,
  CardFooter,
} from "@/components/ui/card";

import { Mail } from "lucide-vue-next";

interface TeamProps {
  imageUrl: string;
  firstName: string;
  lastName: string;
  positions: string[];
  email: string;
}

const teamList: TeamProps[] = [
  {
    imageUrl: "/翁灿.jpg",
    firstName: "翁",
    lastName: "灿",
    positions: ["项目负责人", "算法工程师"],
    email: "wengcan@yiyuzhongsheng.com",
  },
  {
    imageUrl: "/王昕宇.jpg",
    firstName: "王",
    lastName: "昕宇",
    positions: ["前端开发工程师", "CV算法工程师"],
    email: "wangxinyu@yiyuzhongsheng.com",
  },
  {
    imageUrl: "/周恩骏.jpg",
    firstName: "周",
    lastName: "恩骏",
    positions: ["算法工程师", "数据分析师"],
    email: "zhouenjun@yiyuzhongsheng.com",
  },
  {
    imageUrl: "/白宸瑞.jpg",
    firstName: "白",
    lastName: "宸瑞",
    positions: ["后端开发工程师", "产品设计师"],
    email: "baichenrui@yiyuzhongsheng.com",
  },
];
</script>

<template>
  <section
    id="team"
    class="container lg:w-[75%] py-24 sm:py-32"
  >
    <div class="text-center mb-8">
      <h2 class="text-lg text-primary text-center mb-2 tracking-wider">团队</h2>

      <h2 class="text-3xl md:text-4xl text-center font-bold">
        上海理工大学健康科学与工程学院Health IT科创团队
      </h2>
    </div>

    <div
      class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-8"
    >
      <Card
        v-for="{
          imageUrl,
          firstName,
          lastName,
          positions,
          email,
        } in teamList"
        :key="imageUrl"
        class="bg-muted/60 dark:bg-card flex flex-col h-full overflow-hidden group/hoverimg"
      >
        <CardHeader class="p-0 gap-0">
          <div class="h-full overflow-hidden">
            <img
              :src="imageUrl"
              alt=""
              class="w-full aspect-square object-cover saturate-100 brightness-90 transition-all duration-200 ease-linear size-full group-hover/hoverimg:saturate-125 group-hover/hoverimg:brightness-110 group-hover/hoverimg:scale-[1.01]"
            />
          </div>
          <CardTitle class="py-6 pb-4 px-6"
            >{{ firstName }}
            <span class="text-primary">{{ lastName }}</span>
          </CardTitle>
        </CardHeader>

        <CardContent
          v-for="(position, index) in positions"
          :key="index"
          :class="{
            'pb-0 text-muted-foreground ': true,
            'pb-4': index === positions.length - 1,
          }"
        >
          {{ position }}<span v-if="index < positions.length - 1">,</span>
        </CardContent>

        <CardFooter class="space-x-4 mt-auto">
          <a
            :href="'mailto:' + email"
            target="_blank"
            class="hover:opacity-80 transition-all"
            :aria-label="'Send email to ' + firstName + ' ' + lastName"
          >
            <Mail class="size-5" />
          </a>
        </CardFooter>
      </Card>
    </div>
  </section>
</template>
