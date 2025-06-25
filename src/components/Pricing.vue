<script setup lang="ts">
import { Button } from "@/components/ui/button";
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";

import { Check } from "lucide-vue-next";

enum PopularPlan {
  NO = 0,
  YES = 1,
}

interface PlanProps {
  title: string;
  popular: PopularPlan;
  price: number;
  description: string;
  buttonText: string;
  benefitList: string[];
}

const plans: PlanProps[] = [
  {
    title: "免费版",
    popular: 0,
    price: 0,
    description:
      "适合个人用户和小型团队开始使用我们的服务。",
    buttonText: "开始免费试用",
    benefitList: [
      "1个团队成员",
      "1GB存储空间",
      "最多2个页面",
      "社区支持",
      "AI辅助",
    ],
  },
  {
    title: "高级版",
    popular: 1,
    price: 45,
    description:
      "为成长中的团队提供更多功能和更好的支持。",
    buttonText: "立即开始",
    benefitList: [
      "4个团队成员",
      "8GB存储空间",
      "最多6个页面",
      "优先支持",
      "AI辅助",
    ],
  },
  {
    title: "企业版",
    popular: 0,
    price: 120,
    description:
      "为大型企业提供定制化解决方案和全方位支持。",
    buttonText: "联系我们",
    benefitList: [
      "10个团队成员",
      "20GB存储空间",
      "最多10个页面",
      "电话和邮件支持",
      "AI辅助",
    ],
  },
];
</script>

<template>
  <section class="container py-24 sm:py-32">
    <h2 class="text-lg text-primary text-center mb-2 tracking-wider">
      定价
    </h2>

    <h2 class="text-3xl md:text-4xl text-center font-bold mb-4">
      获取无限访问权限
    </h2>

    <h3
      class="md:w-1/2 mx-auto text-xl text-center text-muted-foreground pb-14"
    >
      选择最适合您需求的套餐，开启您的成功之旅。
    </h3>

    <div class="grid md:grid-cols-2 lg:grid-cols-3 gap-8 lg:gap-4">
      <Card
        v-for="{
          title,
          popular,
          price,
          description,
          buttonText,
          benefitList,
        } in plans"
        :key="title"
        :class="{
          'drop-shadow-xl shadow-black/10 dark:shadow-white/10 border-[1.5px] border-primary lg:scale-[1.1]':
            popular === PopularPlan?.YES,
        }"
      >
        <CardHeader>
          <CardTitle class="pb-2">
            {{ title }}
          </CardTitle>

          <CardDescription class="pb-4">{{ description }}</CardDescription>

          <div>
            <span class="text-3xl font-bold">${{ price }}</span>
            <span class="text-muted-foreground"> /month</span>
          </div>
        </CardHeader>

        <CardContent class="flex">
          <div class="space-y-4">
            <span
              v-for="benefit in benefitList"
              :key="benefit"
              class="flex"
            >
              <Check class="text-primary mr-2" />
              <h3>{{ benefit }}</h3>
            </span>
          </div>
        </CardContent>

        <CardFooter>
          <Button
            :variant="popular === PopularPlan?.NO ? 'secondary' : 'default'"
            class="w-full"
          >
            {{ buttonText }}
          </Button>
        </CardFooter>
      </Card>
    </div>
  </section>
</template>
