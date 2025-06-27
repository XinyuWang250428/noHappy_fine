<script lang="ts" setup>
import { ref } from "vue";
import { useColorMode } from "@vueuse/core";
const mode = useColorMode();

import {
  NavigationMenu,
  NavigationMenuContent,
  NavigationMenuItem,
  NavigationMenuLink,
  NavigationMenuList,
  NavigationMenuTrigger,
} from "@/components/ui/navigation-menu";
import {
  Sheet,
  SheetContent,
  SheetFooter,
  SheetHeader,
  SheetTitle,
  SheetTrigger,
} from "@/components/ui/sheet";

import { Button } from "@/components/ui/button";
import { Separator } from "@/components/ui/separator";

import { Menu } from "lucide-vue-next";
import GithubIcon from "@/icons/GithubIcon.vue";
import ToggleTheme from "./ToggleTheme.vue";

interface RouteProps {
  href: string;
  label: string;
}

interface FeatureProps {
  title: string;
  description: string;
}

const routeList: RouteProps[] = [
  {
    href: "#testimonials",
    label: "查看报告",
  },
  {
    href: "#team",
    label: "团队",
  },
  {
    href: "#contact",
    label: "联系我们",
  },
  {
    href: "#faq",
    label: "常见问题",
  },
];

const featureList: FeatureProps[] = [
  {
    title: "📋 心理量表评估",
    description: "通过专业量表评估您的心理健康状况，为临床诊断提供科学依据",
  },
  {
    title: "💓 心电检测评估",
    description: "利用心电信号分析技术，检测心理应激状态下的生理指标变化",
  },
  {
    title: "🧬 基因辅助分析",
    description: "分析基因数据，评估遗传性抑郁症风险因子，助力精准诊疗",
  },
  {
    title: "😊 情绪识别评估",
    description: "基于AI的面部表情分析，实时评估情绪表达和调节能力",
  },
];

const isOpen = ref<boolean>(false);
</script>

<template>
  <header
    :class="{
      'shadow-light': mode === 'light',
      'shadow-dark': mode === 'dark',
      'w-[90%] md:w-[70%] lg:w-[75%] lg:max-w-screen-xl top-5 mx-auto sticky border z-40 rounded-2xl flex justify-between items-center p-2 bg-card shadow-md': true,
    }"
  >
    <a
      href="/"
      class="font-bold text-lg flex items-center"
    >
      <div class="bg-gradient-to-tr from-primary via-primary/70 to-primary rounded-lg w-9 h-9 mr-2 border flex items-center justify-center">
        <img
          src="/图标.svg"
          class="w-7 h-7 dark:invert"
        />
      </div>
      意遇重生</a
    >
    <!-- Mobile -->
    <div class="flex items-center lg:hidden">
      <Sheet v-model:open="isOpen">
        <SheetTrigger as-child>
          <Menu
            @click="isOpen = true"
            class="cursor-pointer"
          />
        </SheetTrigger>

        <SheetContent
          side="left"
          class="flex flex-col justify-between rounded-tr-2xl rounded-br-2xl bg-card"
        >
          <div>
            <SheetHeader class="mb-4 ml-4">
              <SheetTitle class="flex items-center">
                <a
                  href="/"
                  class="flex items-center"
                >
                  <div class="bg-gradient-to-tr from-primary/70 via-primary to-primary/70 rounded-lg size-9 mr-2 border flex items-center justify-center">
                    <img
                      src="/图标.svg"
                      class="w-7 h-7 dark:invert"
                    />
                  </div>
                  意遇重生
                </a>
              </SheetTitle>
            </SheetHeader>

            <div class="flex flex-col gap-2">
              <Button
                v-for="{ href, label } in routeList"
                :key="label"
                as-child
                variant="ghost"
                class="justify-start text-base"
              >
                <a
                  @click="isOpen = false"
                  :href="href"
                >
                  {{ label }}
                </a>
              </Button>
            </div>
          </div>

          <SheetFooter class="flex-col sm:flex-col justify-start items-start">
            <Separator class="mb-2" />

            <ToggleTheme />
          </SheetFooter>
        </SheetContent>
      </Sheet>
    </div>

    <!-- Desktop -->
    <NavigationMenu class="hidden lg:block">
      <NavigationMenuList>
        <NavigationMenuItem>
          <NavigationMenuTrigger class="bg-card text-base">
            测试分析
          </NavigationMenuTrigger>
          <NavigationMenuContent>
            <ul class="grid w-[600px] gap-2 p-4">
              <li
                v-for="{ title, description } in featureList"
                :key="title"
                class="rounded-md p-3 text-sm hover:bg-muted cursor-pointer"
              >
                <NavigationMenuLink asChild>
                  <div>
                    <p class="mb-1 font-semibold leading-none text-foreground" v-html="title">
                    </p>
                    <p class="text-muted-foreground">
                      {{ description }}
                    </p>
                  </div>
                </NavigationMenuLink>
              </li>
            </ul>
          </NavigationMenuContent>
        </NavigationMenuItem>

        <NavigationMenuItem v-for="{ href, label } in routeList" :key="label">
          <NavigationMenuLink asChild>
            <a 
              :href="href"
              class="group inline-flex h-10 w-max items-center justify-center rounded-md bg-background px-4 py-2 text-sm font-medium transition-colors hover:bg-accent hover:text-accent-foreground focus:bg-accent focus:text-accent-foreground focus:outline-none disabled:pointer-events-none disabled:opacity-50 data-[active]:bg-accent/50 data-[state=open]:bg-accent/50"
            >
              {{ label }}
            </a>
          </NavigationMenuLink>
        </NavigationMenuItem>
      </NavigationMenuList>
    </NavigationMenu>

    <div class="hidden lg:flex">
      <ToggleTheme />

      <Button
        as-child
        size="sm"
        variant="ghost"
        aria-label="View on GitHub"
      >
        <a
          aria-label="View on GitHub"
          href="https://github.com/leoMirandaa/shadcn-vue-landing-page.git"
          target="_blank"
        >
          <GithubIcon class="size-5" />
        </a>
      </Button>
    </div>
  </header>
</template>

<style scoped>
.shadow-light {
  box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.085);
}

.shadow-dark {
  box-shadow: inset 0 0 5px rgba(255, 255, 255, 0.141);
}
</style>
