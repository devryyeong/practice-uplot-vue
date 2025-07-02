<template>
  <div ref="root">
    <UplotVue
      :key="renderKey"
      :options="options"
      :data="data"
      @create="onCreate"
      @delete="onDelete"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import uPlot from "uplot";
import UplotVue from "uplot-vue";

// Define plugin
const dummyPlugin = (): uPlot.Plugin => ({
  hooks: {
    init(u, opts) {
      void u;
      void opts;
    },
  },
});

const root = ref<HTMLElement | null>(null);
const renderKey = ref("initial"); // force rerender if needed

const options = ref<uPlot.Options>({
  title: "Chart",
  width: 400,
  height: 300,
  series: [
    { label: "Date" },
    {
      label: "",
      points: { show: false },
      stroke: "blue",
      fill: "blue",
    },
  ],
  plugins: [dummyPlugin()],
  scales: { x: { time: false } },
});

const data = ref<uPlot.AlignedData>([[], []]);

onMounted(() => {
  const x = Array.from({ length: 100000 }, (_, i) => i);
  const y = Array.from({ length: 100000 }, (_, i) => i % 1000);
  data.value = [x, y];

  setInterval(() => {
    const nextX = [...data.value[0], data.value[0].length];
    const nextY = [...data.value[1], data.value[0].length % 1000];
    data.value = [nextX, nextY];

    options.value = {
      ...options.value,
      title: root.value?.id
        ? "Rendered with template"
        : "Rendered with function",
    };

    renderKey.value = Date.now().toString();
  }, 100);
});

function onCreate(/* chart: uPlot */) {
  console.log("Created from setup");
}

function onDelete(/* chart: uPlot */) {
  console.log("Deleted from setup");
}
</script>
