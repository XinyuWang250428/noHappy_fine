<script setup lang="ts">
import { useColorMode } from "@vueuse/core";
const mode = useColorMode();
// 确保主题模式响应性
void mode;

import { Badge } from "@/components/ui/badge";
import { Button } from "@/components/ui/button";
import { ArrowRight } from "lucide-vue-next";
import { Sparkles } from "@/components/ui/sparkles";
</script>

<template>
  <section class="container">
    <div
      class="grid place-items-center lg:max-w-screen-xl gap-8 mx-auto py-20 md:py-32"
    >
      <div class="text-center space-y-8">
        <Badge
          variant="outline"
          class="text-sm py-2"
        >
          <span class="mr-2 text-primary">
            <Badge>新</Badge>
          </span>
          <span> 设计已发布！ </span>
        </Badge>

        <div
          class="max-w-screen-md mx-auto text-center text-5xl md:text-6xl font-bold relative"
        >
          <div class="absolute inset-0" style="min-height: 100px;">
            <Sparkles
              background="transparent"
              :minSize="1"
              :maxSize="2"
              :particleDensity="200"
              :speed="2"
              class="w-full h-full"
              particleColor="#FFFFFF"
            />
          </div>
          <h1 class="relative z-10">
            意遇重生
          </h1>
        </div>

                  <p class="max-w-screen-sm mx-auto text-xl text-muted-foreground">
            基于多模态数字疗法的抑郁症智能平台
          </p>

        <div class="space-y-4 md:space-y-0 md:space-x-4">
          <Button class="w-5/6 md:w-1/4 font-bold group/arrow">
            开始使用
            <ArrowRight
              class="size-5 ml-2 group-hover/arrow:translate-x-1 transition-transform"
            />
          </Button>

          <Button
            variant="secondary"
            class="w-5/6 md:w-1/4 font-bold"
          >
            了解更多
          </Button>
        </div>
      </div>

      <div class="relative group mt-14">
        <!-- gradient shadow -->
        <div
          class="absolute -top-6 right-12 w-[90%] h-12 lg:h-[80%] bg-primary/50 blur-3xl rounded-full img-shadow-animation"
        ></div>

        <img
          class="w-full md:w-[1200px] mx-auto rounded-lg relative rouded-lg leading-none flex items-center border border-t-2 border-t-primary/30 img-border-animation"
          src="/概念.png"
          alt="dashboard using shadcn-vue"
        />

        <!-- gradient effect img -->
        <div
          class="absolute bottom-0 left-0 w-full h-20 md:h-28 bg-gradient-to-b from-background/0 via-background/50 to-background rounded-lg"
        ></div>
      </div>
    </div>
  </section>
</template>

<style scoped lang="postcss">
.img-shadow-animation {
  animation-name: img-shadow-animation;
  animation-iteration-count: infinite;
  animation-duration: 2s;
  animation-timing-function: linear;
  animation-direction: alternate;
}

.img-border-animation {
  animation-name: img-border-animation;
  animation-iteration-count: infinite;
  animation-duration: 2s;
  animation-timing-function: linear;
  animation-direction: alternate;
}

@keyframes img-shadow-animation {
  from {
    opacity: 0.5;
    transform: translateY(30px);
  }

  to {
    opacity: 1;
    transform: translateY(0px);
  }
}
@keyframes img-border-animation {
  from {
    @apply border-t-primary/10;
  }

  to {
    @apply border-t-primary/60;
  }
}
</style>
