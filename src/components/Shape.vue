<style lang="scss" scoped>
.shapeDisplay {
    width: 64px;
    height: 64px;
    canvas {
        width: 100%;
        height: 100%;
    }
    &:hover {
        canvas {
            width: 200%;
            height: 200%;
        }
        position: absolute;
        z-index: 9999;
    }
}
</style>

<template lang="pug">
.shapeDisplay
    canvas(ref="mainCanvas", width="512", height="512")
</template>

<script lang="ts" setup>
import { defineProps, onMounted, watch, ref } from "vue";
import { RenderShape } from "@/components/ShapeRendering";

var props = defineProps({
    shape: {
        type: String,
        required: true,
    },
});

var mainCanvas = ref(null);

function StartRenderShape() {
    console.log("RENDER: " + props.shape + " on " + mainCanvas.value);

    RenderShape(mainCanvas.value, props.shape);
}

onMounted(() => {
    StartRenderShape();
});
</script>
