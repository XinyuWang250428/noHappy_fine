<script setup lang="ts">
import { ref, onMounted, computed } from 'vue';
import { loadSlim } from "@tsparticles/slim";
import { tsParticles } from "@tsparticles/engine";
import Particles from "@tsparticles/vue3";
import { cn } from "@/lib/utils";
import type { ISourceOptions } from "@tsparticles/engine";

const props = defineProps<{
  id?: string;
  className?: string;
  background?: string;
  particleSize?: number;
  minSize?: number;
  maxSize?: number;
  speed?: number;
  particleColor?: string;
  particleDensity?: number;
}>();

const init = ref(false);

onMounted(async () => {
  await loadSlim(tsParticles);
  init.value = true;
});

const particlesLoaded = async (container?: any) => {
  if (container) {
    // 动画效果可以在这里添加
  }
};

const particlesOptions = computed((): ISourceOptions => ({
  background: {
    color: {
      value: props.background || 'transparent',
    },
  },
  fullScreen: {
    enable: false,
    zIndex: 1,
  },
  fpsLimit: 120,
  particles: {
    color: {
      value: props.particleColor || '#ffffff',
    },
    move: {
      enable: true,
      speed: props.speed || 2,
      direction: "none",
      random: true,
      straight: false,
      outModes: {
        default: "bounce",
      },
    },
    number: {
      density: {
        enable: true,
        width: 200,
        height: 200,
      },
      value: props.particleDensity || 200,
    },
    opacity: {
      value: {
        min: 0.3,
        max: 0.8,
      },
      animation: {
        enable: true,
        speed: props.speed || 2,
      },
    },
    size: {
      value: {
        min: props.minSize || 1,
        max: props.maxSize || 2,
      },
    },
    links: {
      enable: false,
    },
  },
  detectRetina: true,
}));
</script>

<template>
  <div :class="cn('opacity-100', props.className)">
    <Particles
      v-if="init"
      :id="props.id || 'tsparticles'"
      :class="cn('h-full w-full')"
      :particlesLoaded="particlesLoaded"
      :options="particlesOptions"
    />
  </div>
</template> 