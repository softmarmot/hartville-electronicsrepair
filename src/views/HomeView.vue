<script lang="ts" setup>
import {ref} from 'vue'

import TheCard from "@/components/TheCard.vue";
import bgCardDiagnostic from '@/assets/services/diagnostic.webp'
import Benefits from "@/components/TheBenefits.vue";
import TheFaqItem from "@/components/TheFaqItem.vue";
import {faqItems} from "@/data/faq";
import {serviceCards} from '@/data/services'
import {benefits} from "@/data/benefits";
import bgV from '@/assets/repair-service-_v_.jpg'
import bgH from '@/assets/repair-service-_h_.jpg'

const openIndex = ref<number | null>(null)

const lgSpanClass = (i: number) => {
  const m = i % 4
  return m === 1 || m === 2 ? 'lg:col-span-1' : 'lg:col-span-2'
}
</script>

<template>
  <div class="mx-auto max-w-6xl">
    <section id="home"
             class="scroll-mt-24 relative rounded-2xl overflow-hidden m-6 h-[80vh] md:h-auto">
      <div :style="{ backgroundImage: `url(${bgV})` }"
           class="absolute inset-0 bg-cover bg-center md:hidden" />
      <div :style="{ backgroundImage: `url(${bgH})` }"
           class="absolute inset-0 bg-cover bg-center hidden md:block" />

      <div class="relative flex flex-col items-center md:items-start p-6 md:p-12">
        <div class="flex flex-col mb-4">
          <h2 class="text-left text-6xl text-gray-800">Electronics Repair</h2>
          <p class="text-right text-gray-800 text-xl -mt-2">Hartville</p>
        </div>

        <p class="max-w-2xl text-center md:text-left md:w-2/5 leading-8 text-gray-700">
          Professional repair of smartphones, tablets, laptops, gaming consoles, and other
          electronics in Hartville.
        </p>
      </div>
    </section>

    <section id="services" class="scroll-mt-18 p-6">
      <h1 class="text-4xl text-center">Our Services</h1>

      <div class="mt-6 grid gap-6 md:grid-cols-2 lg:grid-cols-3">
        <TheCard
          v-for="(card, i) in serviceCards"
          :key="card.title"
          :background-img="card.backgroundImg"
          :title="card.title"
          :class="lgSpanClass(i)"
        />
      </div>
    </section>

    <section id="benefits" class="scroll-mt-18 p-6">
      <ul class="grid grid-cols-2 gap-8 md:grid-cols-4">
        <li v-for="b in benefits" :key="b.title">
          <Benefits :badge="b.badge" :title="b.title">{{ b.text }}</Benefits>
        </li>
      </ul>
    </section>

    <section id="about-us" class="scroll-mt-18 p-6 lg:grid lg:grid-cols-3 lg:gap-x-6">
      <TheCard :background-img="bgCardDiagnostic" title="Free diagnostic"/>
      <div class="space-y-4 lg:col-span-2">
        <div>
          <h2 class="text-4xl">Hartville Electronics Repair</h2>
          <h3 class="text-2xl">Service Center</h3>
        </div>
        <p><strong class="font-bold">Hartville Electronics Repair</strong> provides professional repair of
          smartphones, tablets, laptops, and other electronic devices of almost all brands and
          models.
        </p>
        <p>We never charge money just to take a look. At <strong class="font-bold">Hartville Electronics Repair</strong>, diagnostics for your device are completely free.</p>
      </div>
    </section>

    <section id="faq" class="scroll-mt-18 p-6 space-y-4">
      <div class="mx-auto max-w-3xl">
        <h2 class="text-4xl text-center">F.A.Q.</h2>

        <div class="mt-6 space-y-4">
          <TheFaqItem
            v-for="(it, i) in faqItems"
            :key="it.q"
            :answer="it.a"
            :open="openIndex === i"
            :question="it.q"
            @toggle="openIndex = openIndex === i ? null : i"
          />
        </div>
      </div>
    </section>
  </div>
</template>
