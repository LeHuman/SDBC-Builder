<template>
    <div style="height: 100vh; width: 100vw">
        <baklava-editor :view-model="baklava" />
    </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { EditorComponent, useBaklava } from "@baklavajs/renderer-vue";
import CANNode from "./node/CANNode";

export default defineComponent({
    components: {
        "baklava-editor": EditorComponent,
    },
    setup() {
        const baklava = useBaklava();
        console.log(baklava);
        baklava.editor.registerNodeType(CANNode);
        return { baklava };
    },
});
</script>