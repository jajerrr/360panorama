<template>
  <div :style="safeAreaStyle">
    <div id="container" class="panorama-container"></div>

    <!-- ✅ ตำแหน่งโมเดลจะถูกยึดกับหน้าจอ ไม่ขยับตาม Panorama -->
    <div 
      v-if="showModel" 
      class="model-overlay"
      :style="{ top: modelPosition.y + 'px', left: modelPosition.x + 'px' }"
    >
      <Pano360Model />
    </div>
  </div>
</template>

<script setup>
import { Pano360Model } from "#components";
import { onMounted, ref, computed } from "vue";
let PANOLENS, THREE;

const showModel = ref(false);
const modelPosition = ref({ x: "50%", y: "50%" }); // กลางจอ (ปรับตามต้องการ)

const safeAreaStyle = computed(() => ({
  height: `calc(100dvh - env(safe-area-inset-bottom, 0px))`,
  paddingLeft: `env(safe-area-inset-left, 0px)`,
  paddingRight: `env(safe-area-inset-right, 0px)`,
}));

onMounted(async () => {
  if (process.client) {
    const panolensModule = await import("panolens");
    const threeModule = await import("three");
    PANOLENS = panolensModule;
    THREE = threeModule;

    let panorama, viewer, container, infospot;
    container = document.querySelector("#container");
    panorama = new PANOLENS.ImagePanorama("/image/Thai.png");

    // ✅ Infospot ที่ใช้กดเพื่อเปิดโมเดล
    infospot = new PANOLENS.Infospot(350, PANOLENS.DataImage.Info);
    infospot.position.set(0, -2000, -5000);
    infospot.addHoverText("กดเพื่อดูโมเดล 3D");
    infospot.addEventListener("click", () => {
      showModel.value = !showModel.value;
    });
    panorama.add(infospot);

    // Viewer
    viewer = new PANOLENS.Viewer({ 
      output: "console", 
      container: container,
      controlBar: false, 
      autoHideControlBar: true, 
    });
    viewer.add(panorama);
  }
});
</script>

<style scoped>
.panorama-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: black;
  overflow: hidden;
}

/* ✅ ตำแหน่งโมเดลถูกยึดไว้ ไม่ขยับตาม Panorama */
.model-overlay {
  position: fixed;
  transform: translate(-50%, -50%);
  z-index: 1000;
}
</style>
