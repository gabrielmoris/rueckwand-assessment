<template>
  <main class="h-full w-full pt-20">
    <p class="pl-24 text-2xl font-[500] mb-10">Dein Rückwand Auswählen</p>
    <div class="w-full h-full flex flex-col md:flex-row gap-10 px-24">
      <div
        class="bg-[#F7F7F7] rounded-xl w-full h-96 relative"
        ref="canvas"
        :class="[
          wand === 1 ? 'bg-[url(/wand1.png)] bg-cover' : '',
          wand === 2 ? 'bg-[url(/wand2.png)] bg-cover' : '',
          wand === 3 ? 'bg-gradient-to-r from-purple-500 to-pink-500' : '',
          wand === 4 ? 'bg-[url(/wand3.png)] bg-cover' : '',
          wand === 5 ? 'bg-gradient-to-t from-red-200 to-blue-600' : '',
        ]"
      >
        <div
          v-for="(circle, index) in circles"
          :key="index"
          :class="`circle absolute border border-white rounded-full w-[32px] h-[32px] cursor-pointer`"
          :style="{ top: circle.y + 'px', left: circle.x + 'px', backgroundColor: colors[index] ? colors[index] : '#000000' }"
          @mousedown.prevent="startDragging(index)"
        ></div>
      </div>
      <aside class="rounded w-full md:w-2/3 h-full p-3 flex flex-col gap-5">
        <div v-for="(circle, index) in circles">
          <p>Kreise {{ index + 1 }}</p>
          <div class="flex flex-col md:flex-row gap-5 mb-5 w-full">
            <div>
              <label class="w-full flex justify-center font-bold" for="xcoord">X</label>
              <input
                class="rounded-md border border-[#b4b4b4] p-2 w-full"
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
                class="rounded-md border border-[#b4b4b4] p-2 w-full"
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
        <button @click="sendParams" class="p-2 bg-blue-500 text-white rounded-md">Submit</button>
      </aside>
    </div>
  </main>
</template>

<script setup lang="ts">
import { colors } from "../utilities/colors";
import { materials } from "../utilities/materials";
const wand = ref<number | null>(null);
const circles = ref<{ x: number; y: number; xprocent?: string; yprocent?: string }[]>([]);
const canvas = ref<HTMLElement | null>(null);
let draggingIndex = ref<number | null>(null);
const stopMoving = ref(false);

const setWand = (wandNumber: number) => {
  wand.value = wandNumber;
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
  return distance < 32; // circle is 32 pixels
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
