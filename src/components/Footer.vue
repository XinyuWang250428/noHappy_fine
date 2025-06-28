<script setup lang="ts">
import Separator from "./ui/separator/Separator.vue";
</script>

<template>
  <footer
    id="footer"
    class="container py-24 pb-16 sm:py-32 sm:pb-24"
  >
    <div class="p-10 bg-muted/50 dark:bg-card border rounded-2xl">
      <div
        class="grid grid-cols-2 md:grid-cols-4 xl:grid-cols-6 gap-x-12 gap-y-8"
      >
        <div class="col-span-full xl:col-span-2">
          <a
            href="/"
            class="flex font-bold items-center"
          >
            <div class="bg-gradient-to-tr from-primary via-primary/70 to-primary rounded-lg w-12 h-12 mr-3 border flex items-center justify-center">
              <img
                src="/图标.svg"
                class="w-10 h-10 dark:invert"
              />
            </div>
            <h3 class="text-3xl">意遇重生</h3>
          </a>
        </div>

        <div class="flex flex-col gap-2">
          <h3 class="font-bold text-lg">联系</h3>
          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              Github
            </a>
          </div>

          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              Twitter
            </a>
          </div>

          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              Instagram
            </a>
          </div>
        </div>

        <div class="flex flex-col gap-2">
          <h3 class="font-bold text-lg">平台</h3>
          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              iOS
            </a>
          </div>

          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              Android
            </a>
          </div>

          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              网站
            </a>
          </div>
        </div>

        <div class="flex flex-col gap-2">
          <h3 class="font-bold text-lg">帮助</h3>
          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              联系我们
            </a>
          </div>

          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              常见问题
            </a>
          </div>

          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              反馈
            </a>
          </div>
        </div>

        <div class="flex flex-col gap-2">
          <h3 class="font-bold text-lg">社交媒体</h3>
          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              Twitch
            </a>
          </div>

          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              Discord
            </a>
          </div>

          <div>
            <a
              href="#"
              class="opacity-60 hover:opacity-100"
            >
              Dribbble
            </a>
          </div>
        </div>
      </div>

      <Separator class="my-6" />
      <section className="">
        <h3 class="">
          设计和开发者：
          <span class="text-orange-500">翁灿 王昕宇 周恩骏 白宸瑞</span>
        </h3>
      </section>
    </div>
  </footer>
</template>
