<template>
  <main class="h-full w-full pt-20">
    <p class="pl-5 md:pl-24 text-2xl font-[500] mb-10">Dein Rückwand Auswählen</p>
    <div class="w-full h-full flex flex-col md:flex-row gap-10 px-5 md:px-24">
      <div class="w-full">
        <div class="bg-slate-50 rounded-xl w-full h-96 relative overflow-hidden" ref="canvas">
          <div
            class="curtain absolute inset-0 transition-all duration-500 ease-in-out"
            :class="[
              wand === 1 ? 'bg-[url(/wand1.png)] bg-cover' : '',
              wand === 2 ? 'bg-[url(/wand2.png)] bg-cover' : '',
              wand === 3 ? 'bg-gradient-to-r from-purple-500 to-pink-500' : '',
              wand === 4 ? 'bg-[url(/wand3.png)] bg-cover' : '',
              wand === 5 ? 'bg-gradient-to-t from-red-200 to-blue-600' : '',
              isChanging ? 'translate-x-full' : 'translate-x-0',
            ]"
          ></div>
          <div
            v-for="(circle, index) in circles"
            :key="index"
            :class="`circle absolute border border-white rounded-full w-[32px] h-[32px] cursor-pointer`"
            :style="{ top: circle.y + 'px', left: circle.x + 'px', backgroundColor: colors[index] ? colors[index] : '#000000' }"
            @mousedown.prevent="startDragging(index)"
          ></div>
        </div>
        <div v-if="isSubmitted" class="flex flex-col gap-5">
          <p v-if="wand">
            <span class="font-bold text-xl mr-2">Material:</span><span>{{ materials[wand - 1] }}</span>
          </p>
          <div v-if="circles.length" v-for="(circle, i) in circles" class="border border-slate-400 rounded-md flex flex-col gap-2 p-5">
            <p class="font-bold text-xl">Kreise {{ i + 1 }}</p>
            <p>
              <span class="font-bold mr-2">X</span><span class="mr-5">{{ circle.x }}</span
              ><span class="font-bold mr-2">Y</span><span>{{ circle.y }}</span>
            </p>
            <p>
              <span class="font-bold mr-2">X%</span><span class="mr-5">{{ circle.xprocent }}</span
              ><span class="font-bold mr-2">Y%</span><span>{{ circle.yprocent }}</span>
            </p>
          </div>
        </div>
      </div>

      <aside class="rounded w-full md:w-2/3 h-full p-3 flex flex-col gap-5">
        <div v-for="(circle, index) in circles">
          <p>Kreise {{ index + 1 }}</p>
          <div class="flex flex-col md:flex-row gap-5 mb-5 w-full">
            <div>
              <label class="w-full flex justify-center font-bold" for="xcoord">X</label>
              <input
                class="rounded-md border border-slate-400 p-2 w-full"
                id="xcoord"
                name="xcoord"
                type="number"
                :value="circle.x"
                @input="onInputChange(index, 'x', ($event.target as HTMLInputElement).value)"
              />
            </div>
            <div>
              <label class="w-full flex justify-center font-bold" for="ycoord">Y</label>
              <input
                class="rounded-md border border-slate-400 p-2 w-full"
                id="ycoord"
                name="ycoord"
                type="number"
                :value="circle.y"
                @input="onInputChange(index, 'y', ($event.target as HTMLInputElement).value)"
              />
            </div>
          </div>
        </div>
        <button @click="addCircle" class="p-2 bg-blue-500 text-white rounded-md">Kreis hinzufügen</button>
        <p class="text-xl text-slate-600 font-[500]"><span class="text-slate-800">Modell.</span> Welches ist das richtige Modell für dich?</p>
        <div class="overflow-hidden border cursor-pointer border-slate-400 rounded-md relative" v-for="i in 5" :key="i">
          <div class="flex w-full h-full justify-center items-center absolute -z-50">
            <p v-if="wand === i" class="text-center text-slate-400">
              {{ materials[i - 1] }}
            </p>
          </div>
          <div
            @click="setWand(i)"
            class="border cursor-pointer border-slate-400 w-full h-24 rounded-md transition-all duration-500 ease-in-out"
            :class="[
              nextwand === i ? '-translate-x-full' : '',
              i === 1 ? 'bg-[url(/wand1.png)] bg-cover' : '',
              i === 2 ? 'bg-[url(/wand2.png)] bg-cover' : '',
              i === 3 ? 'bg-gradient-to-r from-purple-500 to-pink-500' : '',
              i === 4 ? 'bg-[url(/wand3.png)] bg-cover' : '',
              i === 5 ? 'bg-gradient-to-t from-red-200 to-blue-600' : '',
            ]"
          ></div>
        </div>
        <button @click="sendParams" class="p-2 bg-blue-500 text-white rounded-md">Submit</button>
      </aside>
    </div>
  </main>
</template>

<style scoped>
.curtain {
  transform-origin: right;
}

.overflow-hidden {
  margin-bottom: 1.25rem;
}
</style>

<script setup lang="ts">
import { colors } from "../utilities/colors";
import { materials } from "../utilities/materials";
const wand = ref<number | null>(null);
const nextwand = ref<number | null>(null);
const circles = ref<{ x: number; y: number; xprocent?: string; yprocent?: string }[]>([]);
const canvas = ref<HTMLElement | null>(null);
let draggingIndex = ref<number | null>(null);
const stopMoving = ref(false);
const isChanging = ref(false);
const isSubmitted = ref();

const setWand = (wandNumber: number) => {
  isChanging.value = true;

  setTimeout(() => {
    wand.value = null;
    nextwand.value = wandNumber;
    isChanging.value = false;
    wand.value = wandNumber;
  }, 500);
};

const addCircle = () => {
  if (canvas.value && circles.value.length < 4) {
    if (!circles.value.length) {
      circles.value.push({
        x: canvas.value.offsetWidth / 2,
        y: canvas.value.offsetHeight / 2,
      });
    } else {
      circles.value.push({
        x: circles.value[circles.value.length - 1].x + 32,
        y: circles.value[circles.value.length - 1].y + 32,
      });
    }
  }
};

const startDragging = (index: number) => {
  draggingIndex.value = index;
  stopMoving.value = false;
};

const onMouseMove = (event: MouseEvent) => {
  if (draggingIndex.value !== null && canvas.value) {
    const newX = event.clientX - canvas.value.getBoundingClientRect().left - 16;
    const newY = event.clientY - canvas.value.getBoundingClientRect().top - 16;
    updateCirclePosition(draggingIndex.value, newX, newY);
  }
};

const onMouseUp = () => {
  draggingIndex.value = null;
};

const onInputChange = (index: number, coord: "x" | "y", val: number | string) => {
  const value = Number(val);
  updateCirclePosition(index, coord === "x" ? value : circles.value[index].x, coord === "y" ? value : circles.value[index].y);
};

const checkCollision = (circle1: { x: number; y: number }, circle2: { x: number; y: number }): boolean => {
  const distX = circle1.x - circle2.x;
  const distY = circle1.y - circle2.y;
  const distance = Math.sqrt(distX * distX + distY * distY);
  return distance < 32; // circle is 32 pixels w-[32px]
};

const updateCirclePosition = (index: number, newX: number, newY: number) => {
  if (!canvas.value) return;

  const canvasWidth = canvas.value.offsetWidth;
  const canvasHeight = canvas.value.offsetHeight;

  // The circ. stays in the canvas boundaries
  newX = Math.max(0, Math.min(newX, canvasWidth - 32));
  newY = Math.max(0, Math.min(newY, canvasHeight - 32));

  let collides = false;

  // Check for collisions with other circles
  circles.value.forEach((otherCircle, otherIndex) => {
    if (otherIndex !== index) {
      if (checkCollision({ x: newX, y: newY }, otherCircle)) {
        collides = true;
      }
    }
  });

  if (!collides) {
    circles.value[index].x = newX;
    circles.value[index].y = newY;
  }
};

watch(
  circles,
  (newCircles) => {
    newCircles.forEach((circle, index) => {
      updateCirclePosition(index, circle.x, circle.y);
    });
  },
  { deep: true }
);

const sendParams = () => {
  circles.value.map((circle) => {
    circle.xprocent = Math.round((circle.x * 100) / canvas.value!.offsetWidth) + "%";
    circle.yprocent = Math.round((circle.y * 100) / canvas.value!.offsetHeight) + "%";
  });

  if (circles.value.length) console.log("Kreise:", circles.value);
  if (wand.value) console.log("Material:", materials[wand.value ? wand.value - 1 : 900]);
  isSubmitted.value = true;
};

onMounted(() => {
  window.addEventListener("mousemove", onMouseMove);
  window.addEventListener("mouseup", onMouseUp);
});

onUnmounted(() => {
  window.removeEventListener("mousemove", onMouseMove);
  window.removeEventListener("mouseup", onMouseUp);
});
</script>
