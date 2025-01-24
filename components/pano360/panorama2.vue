<template>
    <div id="container" class="panorama-container"></div>
  </template>
  
  <script setup>
  import { onMounted } from 'vue'
  
  // Use a dynamic import for Panolens
  let PANOLENS, THREE
  
  onMounted(async () => {
    if (process.client) {
      // Dynamically import Panolens and Three.js
      const panolensModule = await import('panolens')
      const threeModule = await import('three')
  
      PANOLENS = panolensModule
      THREE = threeModule
  
      // Initialize Panolens
      let panorama, panorama2, panorama3, viewer, container, infospot
  
      const lookAtPositions = [
        new THREE.Vector3(4871.39, 1088.07, -118.41),
        new THREE.Vector3(1278.27, 732.65, 4769.19),
        new THREE.Vector3(-2000.0, 1000.0, 3000.0) // Position for pano3
      ]
  
      const infospotPositions = [
        new THREE.Vector3(3136.06, 1216.3, -3690.14),
        new THREE.Vector3(3258.81, -295.5, 3771.13),
        new THREE.Vector3(2000.0, 500.0, -1500.0) // Position for pano3
      ]
  
      container = document.querySelector('#container')
  
      // Panorama 1
      panorama = new PANOLENS.ImagePanorama('/image/Thai.png')
      panorama.addEventListener('enter-fade-start', () => {
        viewer.tweenControlCenter(lookAtPositions[0], 0)
      })
  
      // Panorama 2
      panorama2 = new PANOLENS.ImagePanorama('/image/Muslim.png')
      panorama2.addEventListener('enter', () => {
        viewer.tweenControlCenter(lookAtPositions[1], 0)
      })
  
      // Panorama 3
      panorama3 = new PANOLENS.ImagePanorama('/image/Mon.png')
      panorama3.addEventListener('enter', () => {
        viewer.tweenControlCenter(lookAtPositions[2], 0)
      })
  
      // Link Panoramas
      panorama.link(panorama2, infospotPositions[0])
      panorama2.link(panorama, infospotPositions[1])
      panorama2.link(panorama3, infospotPositions[2]) // Link pano2 to pano3
      panorama3.link(panorama2, infospotPositions[1]) // Link pano3 back to pano2
  
      // Add infospot to panorama 1
      infospot = new PANOLENS.Infospot(350, PANOLENS.DataImage.Info)
      infospot.position.set(0, -2000, -5000)
      infospot.addHoverText('Welcome to Panorama 1') // Add hover text
      panorama.add(infospot)

          // Add infospot to panorama 3
    const infospot2 = new PANOLENS.Infospot(350, PANOLENS.DataImage.Info)
      infospot2.position.set(0, 1000, -5000)
      infospot2.addHoverText('Explore Panorama 2!') // Add hover text
      panorama2.add(infospot2)
  
      // Add infospot to panorama 3
      const infospot3 = new PANOLENS.Infospot(350, PANOLENS.DataImage.Info)
      infospot3.position.set(0, 1000, -5000)
      infospot3.addHoverText('Explore Panorama 3!') // Add hover text
      panorama3.add(infospot3)
  
      // Viewer
      viewer = new PANOLENS.Viewer({ output: 'console', container: container })
      viewer.add(panorama, panorama2, panorama3)
    }
  })
  </script>
  
  <style scoped>
  .panorama-container {
    width: 100%;
    height: 100vh;
    position: relative;
  }
  </style>
  