# Vue_Three_Git
 Base project  for further tests

# Creating Vue.js + Three.js app

### Step 1 : Create the project
0. Install Node.js  
1. Open a new Project on `Visual Studio Code`.  
Open Folder > Select where you want to save your project.
2. On Visual Studio Code, Open a new Terminal and execute `npm init vue@latest`.  
Name your project and package then skip (answer `no`) all the following questions. It will create the base of the app.
### Step 2 : Clean the boilerplate
3. Delete the useless boilerplates.  
a) Remove `import './assets/main.css'` from `main.js`.  
b) Delete everything within the `<script setup>`, `<template>` and `<style scoped>` tags in `App.vue`.  
c) You can remove everything within the `src/assets` and `components`.
### Step 3 : Install modules
4. Now the boilerplate is empty, move your terminal location into the newly created project folder `cd project-name`.  
5. Install all the modules who comes with the boilerplate `npm install`
6. Add `three.js` using `npm install three`.
### Step 4 : Add code
Within <template> brackets in `App.vue` add the `canvas` which will contain our `three.js` scene:
```html
<template>
  <canvas ref="canvas"></canvas>
</template>
```
After it, add the following <script> tag:
```html
<script>
import * as THREE from "three";

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

    const geometry = new THREE.BoxGeometry(1, 1, 1);
    const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);

    camera.position.z = 5;

    function animate() {
      requestAnimationFrame(animate);
      cube.rotation.x += 0.01;
      cube.rotation.y += 0.01;
      renderer.render(scene, camera);
    }

    animate();

    function onWindowResize() {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    }

    window.addEventListener("resize", onWindowResize);
  },
};
</script>
```
Remove the "setup" from <style> tag and add the following code withing it.
```html
body {
  margin: 0;
  overflow: hidden;
}
canvas {
  display: block;
  width: 100vw;
  height: 100vh;
}
```
After it you can run the app using the command line `npm run dev` and you should have a cube rotating in the middle of your browser.
The project base **is complete !**
