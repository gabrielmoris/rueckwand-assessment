<template>
  <main class="h-full w-full pt-20">
    <p class="pl-24 text-2xl font-[500] mb-10">Dein Rückwand Auswählen</p>
    <div class="w-full h-full flex flex-col md:flex-row gap-10 px-24">
      <div
        class="bg-[#F7F7F7] rounded-xl w-full h-96"
        :class="[
          wand === 1 ? 'bg-[url(/wand1.png)] bg-cover' : '',
          wand === 2 ? 'bg-[url(/wand2.png)] bg-cover' : '',
          wand === 3 ? 'bg-gradient-to-r from-purple-500 to-pink-500' : '',
          wand === 4 ? 'bg-[url(/wand3.png)] bg-cover' : '',
          wand === 5 ? 'bg-gradient-to-t from-red-200 to-blue-600' : '',
        ]"
      ></div>
      <aside class="rounded w-2/3 h-full p-3 flex flex-col gap-5">
        <p class="text-xl text-[#727272] font-[500]"><span class="text-[#222222]">Modell.</span> Welches ist das richtige Modell für dich?</p>
        <div
          @click="setWand(1)"
          class="border cursor-pointer border-[#b4b4b4] w-full h-24 rounded-md bg-[url(/wand1.png)] bg-cover"
          :class="[wand === 1 ? 'border-blue-700 border-2' : '']"
        ></div>
        <div
          @click="setWand(2)"
          class="border cursor-pointer border-[#b4b4b4] w-full h-24 rounded-md bg-[url(/wand2.png)] bg-cover"
          :class="[wand === 2 ? 'border-blue-700 border-2' : '']"
        ></div>
        <div
          @click="setWand(3)"
          class="border cursor-pointer border-[#b4b4b4] w-full h-24 rounded-md bg-gradient-to-r from-purple-500 to-pink-500"
          :class="[wand === 3 ? 'border-blue-700 border-2' : '']"
        ></div>
        <div
          @click="setWand(4)"
          class="border cursor-pointer border-[#b4b4b4] w-full h-24 rounded-md bg-[url(/wand3.png)] bg-cover"
          :class="[wand === 4 ? 'border-blue-700 border-2' : '']"
        ></div>
        <div
          @click="setWand(5)"
          class="border cursor-pointer border-[#b4b4b4] w-full h-24 rounded-md bg-gradient-to-t from-red-200 to-blue-600"
          :class="[wand === 5 ? 'border-blue-700 border-2' : '']"
        ></div>
      </aside>
    </div>
  </main>
</template>

<script setup lang="ts">
const wand = ref<number | null>(null);

const setWand = (wandNumber: number) => {
  wand.value = wandNumber;
};
</script>
