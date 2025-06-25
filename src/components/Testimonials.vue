<script setup lang="ts">
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar";

import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";

import {
  Carousel,
  CarouselContent,
  CarouselItem,
  CarouselNext,
  CarouselPrevious,
} from "@/components/ui/carousel";

import { Star } from "lucide-vue-next";

interface ReviewProps {
  image: string;
  name: string;
  userName: string;
  comment: string;
  rating: number;
}

const reviewList: ReviewProps[] = [
  {
    image: "https://github.com/shadcn.png",
    name: "张明",
    userName: "产品经理",
    comment:
      "Vue + Shadcn-Vue 太棒了！这个模板让我可以轻松地更改颜色、字体和图片，完美匹配我的品牌形象。",
    rating: 5.0,
  },
  {
    image: "https://github.com/shadcn.png",
    name: "李婷",
    userName: "网络安全分析师",
    comment:
      "作为一个安全专家，我非常看重代码的质量和安全性。这个框架在这两方面都表现出色。",
    rating: 4.8,
  },
  {
    image: "https://github.com/shadcn.png",
    name: "王强",
    userName: "首席技术官",
    comment:
      "这个框架的架构设计非常优秀，组件复用性强，大大提高了我们团队的开发效率。",
    rating: 4.9,
  },
  {
    image: "https://github.com/shadcn.png",
    name: "陈浩",
    userName: "数据科学家",
    comment:
      "框架的性能优化做得很好，即使在处理大量数据的情况下，页面响应速度依然很快。",
    rating: 5.0,
  },
  {
    image: "https://github.com/shadcn.png",
    name: "林小美",
    userName: "IT项目经理",
    comment:
      "文档详细清晰，学习曲线平缓，团队成员很快就能上手使用，这对项目进度帮助很大。",
    rating: 5.0,
  },
  {
    image: "https://github.com/shadcn.png",
    name: "赵雪",
    userName: "DevOps工程师",
    comment:
      "部署和维护都很方便，与现有的CI/CD流程完美集成，运维工作变得更加轻松。",
    rating: 4.9,
  },
];
</script>

<template>
  <section
    id="testimonials"
    class="container py-24 sm:py-32"
  >
    <div class="text-center mb-8">
      <h2 class="text-lg text-primary text-center mb-2 tracking-wider">
        用户评价
      </h2>

      <h2 class="text-3xl md:text-4xl text-center font-bold mb-4">
        听听我们1000+客户怎么说
      </h2>
    </div>

    <Carousel
      :opts="{
        align: 'start',
      }"
      class="relative w-[80%] sm:w-[90%] lg:max-w-screen-xl mx-auto"
    >
      <CarouselContent>
        <CarouselItem
          v-for="review in reviewList"
          :key="review.name"
          class="md:basis-1/2 lg:basis-1/3"
        >
          <Card class="bg-muted/50 dark:bg-card">
            <CardContent class="pt-6 pb-0">
              <div class="flex gap-1 pb-6">
                <Star class="size-4 fill-primary text-primary" />
                <Star class="size-4 fill-primary text-primary" />
                <Star class="size-4 fill-primary text-primary" />
                <Star class="size-4 fill-primary text-primary" />
                <Star class="size-4 fill-primary text-primary" />
              </div>

              "{{ review.comment }}"
            </CardContent>

            <CardHeader>
              <div class="flex flex-row items-center gap-4">
                <Avatar>
                  <AvatarImage
                    src="https://www.radix-vue.com/logo.svg"
                    alt="@radix-vue"
                  />
                  <AvatarFallback>SV</AvatarFallback>
                </Avatar>

                <div class="flex flex-col">
                  <CardTitle class="text-lg">{{ review.name }}</CardTitle>
                  <CardDescription>{{ review.userName }}</CardDescription>
                </div>
              </div>
            </CardHeader>
          </Card>
        </CarouselItem>
      </CarouselContent>
      <CarouselPrevious />
      <CarouselNext />
    </Carousel>
  </section>
</template>
