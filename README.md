# Vue.js + Three.js

Base project for further tests

## Creating a Vue.js + Three.js App

### Step 1: Create the Project

1. Install **Node.js 16.0** or higher.
2. Open a new Project on **Visual Studio Code**.  
   Open Folder > Select where you want to save your project.
3. On **Visual Studio Code**, Open a new Terminal and execute `npm init vue@latest`.  
   Name your project and package then skip (answer `no`) all the following questions. It will create the base of the app.

### Step 2: Clean the Boilerplate

4. Delete the useless boilerplates.  
   a) Remove `import './assets/main.css'` from `main.js`.  
   b) Delete everything within the `<script setup>`, `<template>`, and `<style scoped>` tags in `App.vue`.  
   c) You can remove everything within the `src/assets` and `components`.

### Step 3: Install Modules

5. Now the boilerplate is empty, move your terminal location into the newly created project folder `cd project-name`.
6. Install all the modules who comes with the boilerplate `npm install`
7. Add `three.js` using `npm install three`.

### Step 4: Add Code

8. Within `<template>` brackets in `App.vue` add the `canvas` which will contain our `three.js` scene:

   ```html
   <template>
     <canvas ref="canvas"></canvas>
   </template>
   ```

9. After it, add the following `<script>` tag:

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

10. Remove the "scoped" from `<style>` tag and add the following code within it:

    ```html
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
    ```

11. After it, you can run the app using the command line `npm run dev`, and you should have a rotating cube in the middle of your browser. The base project is now complete!

### Ponahoum's three-usdz-loader package

1. Install package:
   `npm install three-usdz-loader`
2. Add import to `App.vue` <script>:
   `import { USDZLoader } from "three-usdz-loader"
3. create a new instance of `USDZLoader` and call the function `loadFile()` on it
   a) `loadFile()` is an asynchronous function and neet to be call into a `async` function
   b) `loadFile()` takes a File as first argument

- Within App.vue, add `data()` to the `export default`

```js
data() {
  return {
    modelData: null,
    loading: true,
  };
},
```

- Use `Axios` to fetch the binary file for the .usdz

```js
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
```

- Call the async function `loadUSDZ()` passing the axios' fetched file as argument

```js
async function loadUSDZ(modelData, group) {
  const file = new File([modelData], "model.usdz", {
    type: "model/vnd.usdz+zip",
  });
  return await loader.loadFile(file, group);
}
```
