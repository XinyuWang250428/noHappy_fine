<script setup lang="ts">
import {
  Accordion,
  AccordionContent,
  AccordionItem,
  AccordionTrigger,
} from "@/components/ui/accordion";

interface FAQProps {
  question: string;
  answer: string;
  value: string;
}

const FAQList: FAQProps[] = [
  {
    question: "这个平台是免费的吗？",
    answer: "我们提供免费且完整的基础评估服务。",
    value: "item-1",
  },
  {
    question: "评估结果的准确性如何？",
    answer:
      "我们的评估系统基于专业的心理学量表和先进的AI技术，结合多维度数据分析，具有较高的准确性。但请注意，评估结果仅供参考，不能替代专业医疗诊断。",
    value: "item-2",
  },
  {
    question: "我的个人信息会被保护吗？",
    answer:
      "我们严格遵守隐私保护法规，采用加密技术保护您的个人信息，不会向第三方泄露您的数据。",
    value: "item-3",
  },
  {
    question: "多久可以看到评估结果？",
    answer: "完成所有评估项目后，系统会立即生成综合报告，整个过程通常需要15-30分钟。",
    value: "item-4",
  },
  {
    question: "如果评估发现心理健康问题，我该怎么办？",
    answer: "建议您咨询专业的心理健康专家或医生，我们也会在报告中提供相应的建议和资源链接。",
    value: "item-5",
  },
];
</script>

<template>
  <section
    id="faq"
    class="container md:w-[700px] py-24 sm:py-32"
  >
    <div class="text-center mb-8">
      <h2 class="text-lg text-primary text-center mb-2 tracking-wider">常见问题</h2>

      <h2 class="text-3xl md:text-4xl text-center font-bold">
        用户疑问解答
      </h2>
    </div>

    <Accordion
      type="single"
      collapsible
      class="AccordionRoot"
    >
      <AccordionItem
        v-for="{ question, answer, value } in FAQList"
        :key="value"
        :value="value"
      >
        <AccordionTrigger class="text-left"> {{ question }} </AccordionTrigger>

        <AccordionContent>{{ answer }}</AccordionContent>
      </AccordionItem>
    </Accordion>

    <h3 class="font-medium mt-4">
      还有其他问题？
      <a
        href="#"
        class="text-muted-foreground underline"
      >联系我们</a>
    </h3>
  </section>
</template>
