<template>
  <div :style="safeAreaStyle">
    <div id="container" class="panorama-container"></div>
   
    <Pano360Model v-if="showModel" />
  </div>
</template>

<script setup>
import { Pano360Model } from "#components";
import { onMounted,ref } from "vue";
let PANOLENS, THREE;

const showModel = ref(false);

const safeAreaStyle = computed(() => ({
  height: `calc(100dvh - env(safe-area-inset-bottom, 0px))`,
  paddingLeft: `env(safe-area-inset-left, 0px)`,
  paddingRight: `env(safe-area-inset-right, 0px)`,
}));


// Use a dynamic import for Panolens
onMounted(async () => {
  if (process.client) {
    // Dynamically import Panolens and Three.js
    const panolensModule = await import("panolens");
    const threeModule = await import("three");

    PANOLENS = panolensModule;
    THREE = threeModule;


    

    // Initialize Panolens
    let panorama, panorama2, panorama3, viewer, container, infospot;

    const lookAtPositions = [
      new THREE.Vector3(559.8459919997684,100.35087531640147,-4966.538348494355),
      new THREE.Vector3( 3869.5684907464592,  93.40258122857671,  3163.4975348879307), // Fixed Position 2
  new THREE.Vector3(559.8459919997684,100.35087531640147,-4966.538348494355),   // Fixed Position 3
    ];

    const infospotPositions = [
      new THREE.Vector3(3136.06, 1216.3, -3690.14),
      new THREE.Vector3(559.8459919997684,100.35087531640147,-4966.538348494355),
      new THREE.Vector3(2000.0, 500.0, -1500.0), // Position for pano3
      new THREE.Vector3(3869.5684907464592,  93.40258122857671,  3163.4975348879307), // Position for pano3
    ];

    container = document.querySelector("#container");

panorama = new PANOLENS.ImagePanorama("/image/Thai.png");

// ✅ เพิ่มจุดใน Panorama ที่จะให้โมเดลแสดง
const modelPosition = new THREE.Vector3(1000, 0, -3000); // เปลี่ยนตำแหน่งตามต้องการ
infospot = new PANOLENS.Infospot(350, PANOLENS.DataImage.Info);
infospot.position.copy(modelPosition);
infospot.addHoverText("กดเพื่อดูโมเดล 3D");

// ✅ คลิก Infospot เพื่อแสดงโมเดล
infospot.addEventListener("click", () => {
  showModel.value = !showModel.value;
});
panorama.add(infospot);


panorama.addEventListener("enter-fade-start", () => {
//   console.log("LookAt Position:", viewer.getCamera().position);
// console.log("Infospot Position:", viewer.getControl().target);
  viewer.setCameraFov(100); // ตั้งค่า FOV ให้เริ่มกว้างก่อน
  viewer.tweenControlCenter(lookAtPositions[0], 1500); // ใช้เวลา 1.5 วินาที
  setTimeout(() => viewer.setCameraFov(70, 1000), 500); // ซูมเข้าหลังจาก 0.5 วินาที

});
    // Viewer
    viewer = new PANOLENS.Viewer({ 
      output: "console", 
      container: container,
      controlBar: false, 
      autoHideControlBar: true, 
      
    });
    viewer.add(panorama, panorama2, panorama3);
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
  background-color: black; /* ป้องกันขอบขาว */
  overflow: hidden;
}

.body {
  height: calc(100dvh - var(--nuxt-devtools-safe-area-top, 0px));
}
html,
body {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
  background-color: black;
}

#container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: black; /* ป้องกันขอบขาว */
  overflow: hidden;
}

html,
body,
#container {
  margin: 0 !important;
  padding: 0 !important;
  width: 100% !important;
  height: 100% !important;
  background: black !important;
}

button {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  padding: 10px 20px;
  background: white;
  border: none;
  cursor: pointer;
}
</style>
