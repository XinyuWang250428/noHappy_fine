<script setup lang="ts">
import { StagewiseToolbar } from '@stagewise/toolbar-vue'
import { VuePlugin } from '@stagewise-plugins/vue'
import Navbar from "./components/Navbar.vue";
import Hero from "./components/Hero.vue";
import Benefits from "./components/Benefits.vue";
import Features from "./components/Features.vue";
import Services from "./components/Services.vue";
import HowItWorks from "./components/HowItWorks.vue";
import Sponsors from "./components/Sponsors.vue";
import Testimonials from "./components/Testimonials.vue";
import Team from "./components/Team.vue";
import Pricing from "./components/Pricing.vue";
import Community from "./components/Community.vue";
import Contact from "./components/Contact.vue";
import FAQ from "./components/FAQ.vue";
import Footer from "./components/Footer.vue";

const config = {
  plugins: [VuePlugin],
}
</script>

<template>
  <StagewiseToolbar :config="config" />
  <Navbar />
  <Hero />
  <Sponsors />
  <Benefits />
  <Features />
  <Services />
  <HowItWorks />
  <Testimonials />
  <Team />
  <Community />
  <Pricing />
  <Contact />
  <FAQ />
  <Footer />
</template>
