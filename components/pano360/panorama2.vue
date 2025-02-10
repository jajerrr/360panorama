<template>
  <div :style="safeAreaStyle">
    <div id="container" class="panorama-container"></div>
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
import { onMounted } from "vue";

const safeAreaStyle = computed(() => ({
  height: `calc(100dvh - env(safe-area-inset-bottom, 0px))`,
  paddingLeft: `env(safe-area-inset-left, 0px)`,
  paddingRight: `env(safe-area-inset-right, 0px)`,
}));

const modelPosition = ref({ x: "50%", y: "50%" }); // กลางจอ (ปรับตามต้องการ)

// Use a dynamic import for Panolens
let PANOLENS, THREE;
const showModel = ref(false);

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

    

  // Panorama 1
panorama = new PANOLENS.ImagePanorama("/image/Thai.png");

panorama.addEventListener("enter-fade-start", () => {
//   console.log("LookAt Position:", viewer.getCamera().position);
// console.log("Infospot Position:", viewer.getControl().target);
  viewer.setCameraFov(100); // ตั้งค่า FOV ให้เริ่มกว้างก่อน
  viewer.tweenControlCenter(lookAtPositions[0], 1500); // ใช้เวลา 1.5 วินาที
  setTimeout(() => viewer.setCameraFov(70, 1000), 500); // ซูมเข้าหลังจาก 0.5 วินาที

 


});

// Panorama 2
panorama2 = new PANOLENS.ImagePanorama("/image/Muslim.png");
panorama2.addEventListener("enter-fade-start", () => {
  console.log("Entering Panorama 2 with smooth fade...");
  viewer.setCameraFov(100);
  viewer.tweenControlCenter(lookAtPositions[1], 2500);
  setTimeout(() => viewer.setCameraFov(70, 1000), 500);


//   const getLookAtTarget = () => {
//   const direction = new THREE.Vector3();
//   viewer.getCamera().getWorldDirection(direction); // ดึงทิศทางที่กล้องกำลังมอง
//   const cameraPosition = viewer.getCamera().position;
//   const lookAtTarget = cameraPosition.clone().add(direction.multiplyScalar(5000)); // ขยายไปข้างหน้า
//   console.log("Camera Position:", cameraPosition);
//   console.log("LookAt Target (Calculated):", lookAtTarget);
//   return lookAtTarget;
// };
// viewer.getControl().addEventListener("change", () => {
//   getLookAtTarget();
// });

});

// Panorama 3
panorama3 = new PANOLENS.ImagePanorama("/image/Mon.png");
panorama3.addEventListener("enter-fade-start", () => {
  viewer.setCameraFov(100);
  viewer.tweenControlCenter(lookAtPositions[2], 1500);
  setTimeout(() => viewer.setCameraFov(70, 1000), 500);



});


    // Link Panoramas
    panorama.link(panorama2, infospotPositions[0]);
    panorama2.link(panorama3, infospotPositions[3]);
    panorama2.link(panorama, infospotPositions[1]); 
    panorama3.link(panorama, infospotPositions[1]);
    panorama3.link(panorama2, infospotPositions[3]);

    // Add infospot to panorama 1
    infospot = new PANOLENS.Infospot(350, PANOLENS.DataImage.Info);
    infospot.position.set(0, -2000, -5000);
    infospot.addHoverText("Welcome to Panorama 1"); 
    infospot.addEventListener("click", () => {
      showModel.value = !showModel.value;
    });// Add hover text
    panorama.add(infospot);

    // Add infospot to panorama 3
    const infospot2 = new PANOLENS.Infospot(350, PANOLENS.DataImage.Info);
    infospot2.position.set(0, 1000, -5000);
    infospot2.addHoverText("Explore Panorama 2!"); // Add hover text
    panorama2.add(infospot2);

    // Add infospot to panorama 3
    const infospot3 = new PANOLENS.Infospot(350, PANOLENS.DataImage.Info);
    infospot3.position.set(0,-2000, -5000);
    infospot3.addHoverText("Explore Panorama 3!"); // Add hover text
    panorama3.add(infospot3);

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

/* ✅ ตำแหน่งโมเดลถูกยึดไว้ ไม่ขยับตาม Panorama */
.model-overlay {
  position: fixed;
  transform: translate(-50%, -50%);
  z-index: 1000;
}
</style>
