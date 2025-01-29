<template>
  <div class="body">
    <div id="container"></div>
  </div>
</template>

<script setup>
import { onMounted } from 'vue';

onMounted(() => {
  if (process.client) {
    import('panolens').then((PANOLENS) => {
      const container = document.querySelector('#container');

      // สร้าง Panorama ทั้ง 3 ชุด
      const panorama = new PANOLENS.ImagePanorama('/image/Main.png');
      const panorama2 = new PANOLENS.ImagePanorama('/image/Muslim.png');
      const panorama3 = new PANOLENS.ImagePanorama('/image/Mon.png');

      // สร้าง Viewer
      const viewer = new PANOLENS.Viewer({ container: container });
      viewer.add(panorama, panorama2, panorama3);

      // Infospot สำหรับลิงก์ไปยัง Panorama 2
      const infospot1 = new PANOLENS.Infospot(500, PANOLENS.DataImage.Info);
      infospot1.position.set(-100, -500, -5000);
      infospot1.addHoverText('Go to Panorama 2');
      infospot1.addEventListener('click', () => {
        viewer.setPanorama(panorama2);
      });
      panorama.add(infospot1);

      // Infospot สำหรับลิงก์ไปยัง Panorama 3
      const infospot2 = new PANOLENS.Infospot(500, PANOLENS.DataImage.Info);
      infospot2.position.set(1000, -500, -3000);
      infospot2.addHoverText('Go to Panorama 3');
      infospot2.addEventListener('click', () => {
        viewer.setPanorama(panorama3);
      });
      panorama.add(infospot2);

      // Infospot ใน Panorama 2 สำหรับลิงก์กลับไปยัง Panorama 1
      const infospotBack1 = new PANOLENS.Infospot(500, PANOLENS.DataImage.Info);
      infospotBack1.position.set(-2000, 0, 1000);
      infospotBack1.addHoverText('Back to Panorama 1');
      infospotBack1.addEventListener('click', () => {
        viewer.setPanorama(panorama);
      });
      panorama2.add(infospotBack1);

      // Infospot ใน Panorama 3 สำหรับลิงก์กลับไปยัง Panorama 1
      const infospotBack2 = new PANOLENS.Infospot(500, PANOLENS.DataImage.Info);
      infospotBack2.position.set(2000, 500, -2000);
      infospotBack2.addHoverText('Back to Panorama 1');
      infospotBack2.addEventListener('click', () => {
        viewer.setPanorama(panorama);
      });
      panorama3.add(infospotBack2);
    });
  }
});
</script>

<style scoped>
.body {
  margin: 0px !important;
  overflow: hidden;
 
}

#container {
  position: relative;
  width: 100%;
  height: 100dvh;
  background: #000;
}
</style>
