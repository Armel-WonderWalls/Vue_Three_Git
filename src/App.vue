<script setup></script>

<template>
  <canvas ref="canvas"></canvas>
</template>

<script>
import * as THREE from "three";
import { USDZLoader } from "three/examples/jsm/loaders/USDZLoader";
//import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";

export default {
  mounted() {
    const canvas = this.$refs.canvas;
    const renderer = new THREE.WebGLRenderer({ canvas });
    renderer.setSize(window.innerWidth, window.innerHeight);

    const scene = new THREE.Scene();

    const camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );

    const light = new THREE.AmbientLight(0xffffff, 2);
    scene.add(light);
    camera.position.z = 5;

    function animate() {
      requestAnimationFrame(animate);
      renderer.render(scene, camera);
    }

    animate();

    function onWindowResize() {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    }

    window.addEventListener("resize", onWindowResize);

    const usdzLoader = new USDZLoader();
    usdzLoader.load(
      "/Room.usdz",
      (usdz) => {
        console.log(usdz);
        scene.add(usdz.scene);
      },
      undefined,
      (error) => {
        console.error(error);
      }
    );

    //GLTF LOADING IS WORKING...

    // const gltfLoader = new GLTFLoader();
    // gltfLoader.load(
    //   "/buster_drone/scene.gltf",
    //   (gltf) => {
    //     console.log(gltf);
    //     scene.add(gltf.scene);
    //   },
    //   undefined,
    //   (error) => {
    //     console.error(error);
    //   }
    // );
  },
};
</script>

<style>
body {
  margin: 0;
  overflow: hidden;
}
canvas {
  display: block;
  width: 100vw;
  height: 100vh;
}
</style>
