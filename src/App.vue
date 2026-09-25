<script setup lang="ts">
import TheHeader from "@/components/TheHeader.vue";
import TheFooter from "@/components/TheFooter.vue";
</script>

<template>
  <div class="h-screen flex flex-col bg-zinc-900">
    <TheHeader />

    <main class="pt-[80px] bg-zinc-800 font-extralight text-white">
      <RouterView />

      <TheFooter
        hours="Mon–Sun • 9:00 AM – 8:00 PM"
        phone="(330) 958-3587"
        phoneHref="tel:+13309583587"
        email="info@hartvilleelectronicsrepair.com"
        emailHref="mailto:info@hartvilleelectronicsrepair.com"
        serviceArea="Hartville, OH + surrounding areas"
      />
    </main>
  </div>
</template>
