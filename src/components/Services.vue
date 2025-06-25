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
    title: "自定义域名集成",
    description:
      "轻松集成您的自定义域名，打造专业的品牌形象。",
    pro: 0,
  },
  {
    title: "社交媒体集成",
    description:
      "无缝对接各大社交媒体平台，扩大品牌影响力。",
    pro: 0,
  },
  {
    title: "邮件营销集成",
    description: "强大的邮件营销工具，提升客户互动。",
    pro: 0,
  },
  {
    title: "SEO优化",
    description: "专业的搜索引擎优化服务，提升网站排名。",
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
      服务
    </h2>

    <h2 class="text-3xl md:text-4xl text-center font-bold mb-4">
      助力企业成长
    </h2>
    <h3 class="md:w-1/2 mx-auto text-xl text-center text-muted-foreground mb-8">
      从营销和销售到运营和战略，我们拥有帮助您实现目标的专业知识。
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
