<template>
    <div :style="safeAreaStyle">
      <div id="container" class="panorama-container"></div>
      <div id="panel" class="info-panel"></div>
    </div>
  </template>
  
  <script setup>
  import { onMounted, computed } from "vue";
  
  const safeAreaStyle = computed(() => ({
    height: `100vh`,
    width: `100vw`,
    position: "fixed",
    top: "0",
    left: "0",
  }));
  
  let PANOLENS, THREE;
  
  onMounted(async () => {
    if (process.client) {
      const panolensModule = await import("panolens");
      const threeModule = await import("three");
  
      PANOLENS = panolensModule;
      THREE = threeModule;
  
      let panorama,panorama2, viewer, container, panel ;

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
      panel = document.querySelector("#panel");
  
      // Create Panorama
      panorama = new PANOLENS.ImagePanorama(
       "/image/Thai.png"
      );

      panorama.addEventListener("enter-fade-start", () => {
//   console.log("LookAt Position:", viewer.getCamera().position);
// console.log("Infospot Position:", viewer.getControl().target);
  viewer.setCameraFov(100); // ตั้งค่า FOV ให้เริ่มกว้างก่อน
  viewer.tweenControlCenter(lookAtPositions[0], 1500); // ใช้เวลา 1.5 วินาที
  setTimeout(() => viewer.setCameraFov(70, 1000), 500); // ซูมเข้าหลังจาก 0.5 วินาที
});
panorama2 = new PANOLENS.ImagePanorama("/image/Muslim.png");
panorama2.addEventListener("enter-fade-start", () => {
  console.log("Entering Panorama 2 with smooth fade...");
  viewer.setCameraFov(100);
  viewer.tweenControlCenter(lookAtPositions[0], 2500);
  setTimeout(() => viewer.setCameraFov(70, 1000), 500);


});

panorama.link(panorama2, infospotPositions[0]);
  
      // Create Infospot
      const infospot = new PANOLENS.Infospot(350, PANOLENS.DataImage.Info);
      infospot.position.set(0, -2000, -5000);
      infospot.addHoverElement(panel, 150);
    //   infospot.addEventListener("click", () => {
    //   showModel.value = !showModel.value;
    // });
      panorama.add(infospot);


    const infospot2 = new PANOLENS.Infospot(350, PANOLENS.DataImage.Info);
infospot2.position.set(1000, 500, -3000);  // เปลี่ยนตำแหน่ง
infospot2.addHoverElement(panel, 150);  // ใช้ panel อีกตัวหนึ่งหรืออาจจะเปลี่ยนเป็นอันอื่น
panorama2.add(infospot2);
  




      // Create Viewer
      viewer = new PANOLENS.Viewer({ 
        container: container,
      controlBar: false, 
      autoHideControlBar: true, 

      });
      viewer.add(panorama,panorama2);
  
      // Three.js Scene
      const renderer = new THREE.WebGLRenderer();
      renderer.setClearColor(0xffffff);
      renderer.setSize(panel.clientWidth, panel.clientHeight);
      
      const camera = new THREE.PerspectiveCamera(
        45,
        panel.clientWidth / panel.clientHeight,
        1,
        2000
      );
  
      const scene = new THREE.Scene();
      infospot.element.appendChild(renderer.domElement);
  
      const box = new THREE.Mesh(
        new THREE.BoxGeometry(10, 10, 10),
        new THREE.MeshNormalMaterial()
      );
      box.position.z = -20;
      scene.add(box);
  
      viewer.addUpdateCallback(() => {
        renderer.render(scene, camera);
        box.rotation.y += 0.03;
      });
    }
  });
  </script>
  
  <style scoped>
  .panorama-container {
    width: 100vw;
    height: 100vh;
  }
  
  .info-panel {
    width: 320px;
    height: 240px;
    background-color: transparent;
    position: absolute;
    top: 20px;
    left: 20px;
    z-index: 10;
  }
  </style>
  