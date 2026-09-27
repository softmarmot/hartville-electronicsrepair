<script lang="ts" setup>
import {nextTick, ref} from "vue"
import router from "@/router"

const menuExpanded = ref(false)

const goTo = async (id: string) => {
  await router.push({ name: "home" })
  await nextTick()
  menuExpanded.value = false
  await nextTick()

  document.getElementById(id)?.scrollIntoView({ behavior: "smooth", block: "start" })
}
</script>

<template>
  <header
    class="fixed top-0 left-0 w-screen z-50 px-4 bg-zinc-900 text-zinc-300 border-b border-b-zinc-700 md:flex py-2 md:py-0">
    <div class="flex grid-cols-2">
      <img alt="Hartville Electronics Repair Logo" class="h-14 my-auto" src="@/assets/logo.webp">
      <div class="w-full flex justify-end">
        <button class="md:hidden mr-2" @click="menuExpanded = !menuExpanded">
          <svg aria-hidden="true" class="w-8 h-8" fill="none" viewBox="0 0 17 14"
               xmlns="http://www.w3.org/2000/svg">
            <path v-show="!menuExpanded" d="M1 1h15M1 7h15M1 13h15" stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2" />
            <path v-show="menuExpanded" d="M 1 1 15 15 M 15 1 1 15" stroke="currentColor"
                  stroke-linecap="round" stroke-linejoin="round"
                  stroke-width="2" />
          </svg>
        </button>
      </div>
    </div>
    <ul :class="[!menuExpanded ? 'hidden' : 'flex']"
        class="grid grid-cols-1 md:flex md:justify-evenly mt-4 md:mt-0 uppercase md:w-full">
      <li class="border-b md:border-b-0 border-zinc-700 pb-6 md:py-6">
        <a class="w-full text-center" @click.prevent="goTo('home')">home</a>
      </li>
      <li class="border-b md:border-b-0 border-zinc-700 py-6">
        <a class="w-full text-center" @click.prevent="goTo('services')">services</a>
      </li>
      <li class="border-b md:border-b-0 border-zinc-700 py-6">
        <a class="w-full text-center" @click.prevent="goTo('benefits')">benefits</a>
      </li>
      <li class="border-b md:border-b-0 border-zinc-700 py-6">
        <a class="w-full text-center" @click.prevent="goTo('about-us')">About Us</a>
      </li>
      <li class="border-b md:border-b-0 border-zinc-700 py-6">
        <a class="w-full text-center" @click.prevent="goTo('faq')">FAQ</a>
      </li>
      <li class="py-6"><a class="w-full text-center" @click.prevent="goTo('footer')">Contacts</a></li>
    </ul>
  </header>
</template>

<style scoped>
@reference "tailwindcss";

ul li {
  @apply flex justify-end hover:font-bold hover:cursor-pointer;
}
</style>
