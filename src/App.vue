<script setup></script>

<template>
  <canvas ref="canvas"></canvas>
</template>

<script>
import * as THREE from "three";
import { USDZLoader } from "three-usdz-loader";
import axios from "axios";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

export default {
  data() {
    return {
      modelData: null,
      loading: true,
    };
  },
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

    const controls = new OrbitControls(camera, renderer.domElement);

    const light = new THREE.AmbientLight(0xffffff, 2);
    scene.add(light);

    camera.position.z = 5;
    controls.update();

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

    const loader = new USDZLoader();
    const group = new THREE.Group();
    scene.add(group);
    axios
      .get("/livingroom.usdz", {
        responseType: "arraybuffer",
      })
      .then((response) => {
        this.modelData = response.data;
        this.loading = false;
        // do something with the model data here
        loadUSDZ(this.modelData, group);
      });

    async function loadUSDZ(modelData, group) {
      const file = new File([modelData], "model.usdz", {
        type: "model/vnd.usdz+zip",
      });
      return await loader.loadFile(file, group);
    }
    console.log(group);
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
