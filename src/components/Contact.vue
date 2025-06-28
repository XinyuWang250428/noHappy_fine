<script setup lang="ts">
import { ref, reactive } from "vue";
import { Button } from "./ui/button";
import { Card, CardHeader, CardContent, CardFooter } from "./ui/card";
import { Label } from "./ui/label";
import { Input } from "./ui/input";
import {
  Select,
  SelectContent,
  SelectGroup,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "./ui/select";
import { Textarea } from "./ui/textarea";
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert";

import { AlertCircle, Building2, Phone, Mail, Clock } from "lucide-vue-next";

interface ContactFormeProps {
  firstName: string;
  lastName: string;
  email: string;
  subject: string;
  message: string;
}

const contactForm = reactive<ContactFormeProps>({
  firstName: "",
  lastName: "",
  email: "",
  subject: "平台收费问题",
  message: "",
});

const invalidInputForm = ref<boolean>(false);

const handleSubmit = () => {
  const { firstName, lastName, email, subject, message } = contactForm;
  console.log(contactForm);

  const mailToLink = `mailto:support@yiyuzhongsheng.com?subject=${subject}&body=您好，我是${firstName} ${lastName}，我的邮箱是${email}。%0D%0A${message}`;

  window.location.href = mailToLink;
};
</script>

<template>
  <section
    id="contact"
    class="container py-24 sm:py-32"
  >
    <section class="grid grid-cols-1 md:grid-cols-2 gap-8">
      <div>
        <div class="mb-4">
          <h2 class="text-lg text-primary mb-2 tracking-wider">联系我们</h2>

          <h2 class="text-3xl md:text-4xl font-bold">与我们取得联系</h2>
        </div>
        <p class="mb-8 text-muted-foreground lg:w-5/6">
          如果您有任何关于心理健康评估的问题或需要技术支持，请随时联系我们。我们的专业团队将为您提供及时的帮助和指导。
        </p>

        <div class="flex flex-col gap-4">
          <div>
            <div class="flex gap-2 mb-1">
              <Building2 />
              <div class="font-bold">找到我们</div>
            </div>

            <div>上海市杨浦区军工路516号</div>
          </div>

          <div>
            <div class="flex gap-2 mb-1">
              <Phone />
              <div class="font-bold">电话联系</div>
            </div>

            <div>+86 400-888-9999</div>
          </div>

          <div>
            <div class="flex gap-2 mb-1">
              <Mail />
              <div class="font-bold">邮件联系</div>
            </div>

            <div>support@yiyuzhongsheng.com</div>
          </div>

          <div>
            <div class="flex gap-2">
              <Clock />
              <div class="font-bold">服务时间</div>
            </div>

            <div>
              <div>周一至周五</div>
              <div>上午9:00 - 下午6:00</div>
            </div>
          </div>
        </div>
      </div>

      <!-- form -->
      <Card class="bg-muted/60 dark:bg-card">
        <CardHeader class="text-primary text-2xl"> </CardHeader>
        <CardContent>
          <form
            @submit.prevent="handleSubmit"
            class="grid gap-4"
          >
            <div class="flex flex-col md:flex-row gap-8">
              <div class="flex flex-col w-full gap-1.5">
                <Label for="first-name">姓</Label>
                <Input
                  id="first-name"
                  type="text"
                  placeholder="张"
                  v-model="contactForm.firstName"
                />
              </div>

              <div class="flex flex-col w-full gap-1.5">
                <Label for="last-name">名</Label>
                <Input
                  id="last-name"
                  type="text"
                  placeholder="三"
                  v-model="contactForm.lastName"
                />
              </div>
            </div>

            <div class="flex flex-col gap-1.5">
              <Label for="email">邮箱</Label>
              <Input
                id="email"
                type="email"
                placeholder="example@email.com"
                v-model="contactForm.email"
              />
            </div>

            <div class="flex flex-col gap-1.5">
              <Label for="subject">咨询主题</Label>

              <Select v-model="contactForm.subject">
                <SelectTrigger>
                  <SelectValue placeholder="请选择咨询主题" />
                </SelectTrigger>
                <SelectContent>
                  <SelectGroup>
                    <SelectItem value="平台收费问题">
                      平台收费问题
                    </SelectItem>
                    <SelectItem value="评估结果准确性">
                      评估结果准确性
                    </SelectItem>
                    <SelectItem value="个人信息保护"> 个人信息保护 </SelectItem>
                    <SelectItem value="评估结果时间"> 评估结果时间 </SelectItem>
                    <SelectItem value="心理健康问题处理">
                      心理健康问题处理
                    </SelectItem>
                    <SelectItem value="其他问题">
                      其他问题
                    </SelectItem>
                  </SelectGroup>
                </SelectContent>
              </Select>
            </div>

            <div class="flex flex-col gap-1.5">
              <Label for="message">留言内容</Label>
              <Textarea
                id="message"
                placeholder="请详细描述您的问题或建议..."
                rows="5"
                v-model="contactForm.message"
              />
            </div>

            <Alert
              v-if="invalidInputForm"
              variant="destructive"
            >
              <AlertCircle class="w-4 h-4" />
              <AlertTitle>错误</AlertTitle>
              <AlertDescription>
                表单中存在错误，请检查您的输入。
              </AlertDescription>
            </Alert>

            <Button class="mt-4">发送消息</Button>
          </form>
        </CardContent>

        <CardFooter></CardFooter>
      </Card>
    </section>
  </section>
</template>
