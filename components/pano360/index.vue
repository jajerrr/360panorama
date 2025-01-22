<template>
    <div id="viewer" style="width: 100vw; height: 100vh;"></div>
  </template>
  
  <script setup>
  import { onMounted } from 'vue';
  import { Viewer } from 'photo-sphere-viewer';
  import 'photo-sphere-viewer/dist/photo-sphere-viewer.css';
  
  onMounted(() => {
    new Viewer({
      container: document.getElementById('viewer'),
      panorama: '/image/Muslim.png', // Replace with your panorama image
    });

    
  });
  </script>
  