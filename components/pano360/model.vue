<template>
    <div ref="threeContainer" class="fullscreen-container"></div>
  </template>
  
  <script setup>
  import { onMounted, ref, onUnmounted } from "vue";
  import * as THREE from "three";
  import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
  
  const threeContainer = ref(null);
  let scene, camera, renderer, controls, model;
  
  const updateCanvasSize = () => {
    if (renderer) {
      renderer.setSize(window.innerWidth, window.innerHeight, false);
      renderer.setPixelRatio(window.devicePixelRatio);
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
    }
  };
  
  onMounted(() => {
    if (process.client) {
      // ✅ สร้าง Scene
      scene = new THREE.Scene();
  
      // ✅ ตั้งค่ากล้อง
      camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        100
      );
      camera.position.set(0, 0, 5);
  
      // ✅ ตั้งค่า Renderer (เปิด `alpha: true` และทำให้โปร่งใส)
      renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
      renderer.setClearColor(0x000000, 0); // ✅ ทำให้โปร่งใส
      renderer.setSize(window.innerWidth, window.innerHeight, false);
      renderer.setPixelRatio(window.devicePixelRatio);
      threeContainer.value.appendChild(renderer.domElement);
  
      // ✅ ตั้งค่าแสง
      const ambientLight = new THREE.AmbientLight(0xffffff, 2);
      scene.add(ambientLight);
  
      const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
      directionalLight.position.set(5, 5, 5);
      scene.add(directionalLight);
  
      // ✅ เพิ่ม OrbitControls (หมุน/ซูมโมเดล)
      controls = new OrbitControls(camera, renderer.domElement);
      controls.enableDamping = true;
      controls.dampingFactor = 0.2;
      controls.minDistance = 1;
      controls.maxDistance = 10;
  
      // ✅ โหลดโมเดล 3D
      const loader = new GLTFLoader();
      loader.load(
        "http://27.254.62.100:30021/pscm/models/heart.glb",
        (gltf) => {
          model = gltf.scene;
          model.scale.set(1, 1, 1);
          model.position.set(0, -0.5, 0);
          scene.add(model);
        },
        undefined,
        (error) => {
          console.error("❌ Error loading 3D model:", error);
        }
      );
  
      // ✅ Loop การเรนเดอร์
      const animate = () => {
        requestAnimationFrame(animate);
        controls.update();
        renderer.render(scene, camera);
      };
      animate();
  
      // ✅ ปรับขนาด `canvas` อัตโนมัติ
      window.addEventListener("resize", updateCanvasSize);
    }
  });
  
  onUnmounted(() => {
    window.removeEventListener("resize", updateCanvasSize);
    renderer.dispose();
    controls.dispose();
  });
  </script>
  
  <style scoped>
  .fullscreen-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
  }
  canvas {
    display: block;
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw !important;
    height: 100vh !important;
    background: transparent !important; /* ✅ ไม่มีพื้นหลังดำ */
    pointer-events: none; /* ✅ ป้องกันการบัง Panorama */
  }
  </style>
  