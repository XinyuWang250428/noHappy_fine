<script setup lang="ts">
import { Marquee } from "@selemondev/vue3-marquee";
import "@selemondev/vue3-marquee/dist/style.css";

import {
  Code2,
  Layers3,
  Zap,
  Palette,
  Server,
  Shield,
  Database,
  Cpu,
} from "lucide-vue-next";

interface techStackProps {
  icon: string;
  name: string;
  type: 'frontend' | 'backend';
}

const techStack: techStackProps[] = [
  {
    icon: "code2",
    name: "Vue 3",
    type: "frontend",
  },
  {
    icon: "layers3",
    name: "TypeScript",
    type: "frontend",
  },
  {
    icon: "zap",
    name: "Vite",
    type: "frontend",
  },
  {
    icon: "palette",
    name: "Tailwind CSS",
    type: "frontend",
  },
  {
    icon: "server",
    name: "Spring Boot",
    type: "backend",
  },
  {
    icon: "shield",
    name: "RuoYi",
    type: "backend",
  },
  {
    icon: "database",
    name: "MySQL",
    type: "backend",
  },
  {
    icon: "cpu",
    name: "Redis",
    type: "backend",
  },
];

const iconMap: Record<
  string,
  | typeof Code2
  | typeof Layers3
  | typeof Zap
  | typeof Palette
  | typeof Server
  | typeof Shield
  | typeof Database
  | typeof Cpu
> = {
  code2: Code2,
  layers3: Layers3,
  zap: Zap,
  palette: Palette,
  server: Server,
  shield: Shield,
  database: Database,
  cpu: Cpu,
};
</script>

<template>
  <section
    id="sponsors"
    class="max-w-[75%] mx-auto pb-24 sm:pb-32"
  >
    <h2 class="text-3xl md:text-4xl text-center font-bold mb-4">项目技术栈</h2>

    <div class="mx-auto">
      <Marquee
        class="gap-[3rem]"
        :pauseOnHover="true"
        :fade="true"
        innerClassName="gap-[3rem]"
      >
        <div
          v-for="{ icon, name, type } in techStack"
          :key="name"
          class="flex items-center text-xl md:text-2xl font-medium whitespace-nowrap"
        >
          <component
            :is="iconMap[icon]"
            class="mr-2"
            stroke-width="3"
          />
          {{ name }}
        </div>
      </Marquee>
    </div>
  </section>
</template>
