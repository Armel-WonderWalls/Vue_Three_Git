# Vue.js + Three.js
Base project for further tests  
Ponahoum's three-usdz-loader package branch
## Creating a Vue.js + Three.js App

### Step 1: Create the Project
1. Install **Node.js 16.0** or higher.
2. Open a new Project on **Visual Studio Code**.  
Open Folder > Select where you want to save your project.
3. On **Visual Studio Code**, Open a new Terminal and execute `npm init vue@latest`.  
Name your project and package then skip (answer `no`) all the following questions. It will create the base of the app.

### Step 2: Clean the Boilerplate
4. Delete the useless boilerplates.  
a) Remove `import './assets/main.css'` from `/src/main.js`.  
b) Delete everything within the `<script setup>`, `<template>`, and `<style scoped>` tags in `/src/App.vue`.  
c) You can remove everything within the `/src/assets` and `components`.

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
   // Importing the Three.js library
   import * as THREE from "three";
   
   // Exporting a Vue.js component
   export default {
     mounted() {
       // Accessing the canvas element from the template
       const canvas = this.$refs.canvas;
       
       // Creating a WebGL renderer and attaching it to the canvas element
       const renderer = new THREE.WebGLRenderer({ canvas });
       
       // Setting the size of the renderer to the full size of the window
       renderer.setSize(window.innerWidth, window.innerHeight);

       // Creating a Three.js scene
       const scene = new THREE.Scene();
       
       // Creating a camera with perspective projection
       const camera = new THREE.PerspectiveCamera(
         75, // Field of view (FOV) in degrees
         window.innerWidth / window.innerHeight, // Aspect ratio
         0.1, // Near clipping plane
         1000 // Far clipping plane
       );
       
       // Creating a cube mesh with a green basic material
       const geometry = new THREE.BoxGeometry(1, 1, 1);
       const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 }); // Green color
       const cube = new THREE.Mesh(geometry, material); // Combining the geometry and material into a mesh
       scene.add(cube); // Adding the cube mesh to the scene

       // Positioning the camera away from the origin
       camera.position.z = 5;

       // Defining an animation loop using requestAnimationFrame
       function animate() {
         requestAnimationFrame(animate); // Scheduling the next frame to be rendered
         
         // Rotating the cube mesh around its x and y axes
         cube.rotation.x += 0.01;
         cube.rotation.y += 0.01;
         
         // Rendering the scene with the camera using the WebGL renderer
         renderer.render(scene, camera);
       }
       
       // Starting the animation loop
       animate();
       
       // Handling window resize events by updating the camera aspect ratio and renderer size
       function onWindowResize() {
         camera.aspect = window.innerWidth / window.innerHeight;
         camera.updateProjectionMatrix();
         renderer.setSize(window.innerWidth, window.innerHeight);
       }
       
       // Registering the window resize event listener
       window.addEventListener("resize", onWindowResize);
     },
   };
   </script>
   ```

10. Remove the "scoped" from `<style>` tag and add the following code within it:

    ```css
    /* Set margin to 0 to remove body's margin and hide any overflow scrollbars */
    body {
      margin: 0;
      overflow: hidden;
    }
    /* Display canvas as a block element and make it fill the entire viewport */
    canvas {
      display: block;
      width: 100vw;
      height: 100vh;
    }
    ```

11. After it, you can run the app using the command line `npm run dev`, and you should have a rotating cube in the middle of your browser. The base project is now complete!

### Ponahoum's three-usdz-loader package
12. Remove the code for the cube and it's animation
13. Install packages with `npm install three-usdz-loader` and `npm install axios`.  
14. Add imports to `App.vue` <script>:  
         ```js
         import { USDZLoader } from "three-usdz-loader";
         import axios from "axios";
         ```  
15. Add the `.usdz` file into the `public` directory.
16. To use the package we need to create a new instance of `USDZLoader` and call the function `loadFile()` on it.  
   a) `loadFile()` is an asynchronous function and neet to be call into a `async` function.  
   b) `loadFile()` takes a File as first argument.  
  
      - Within App.vue, add `data()` to the `export default`, it will hold the data we will fetch with Axios API.

         ```js
         data() {
           return {
             modelData: null,
             loading: true,
           };
         },
         ```
   
      - Call the async function `loadUSDZ()` passing the axios' fetched file as argument

         ```js
         const loader = new USDZLoader();
         const group = new THREE.Group();
         scene.add(group);
         async function loadUSDZ(modelData, group) {
           const file = new File([modelData], "model.usdz", {
             type: "model/vnd.usdz+zip",
           });
           return await loader.loadFile(file, group);
         }
         ```

      - Use `Axios` to fetch the binary file given the path of our .usdz file.

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



### Conclusion:
IT WORKS !  
... code is throwing a bunch or warnings and errors but... "MEH!" at lease the .usdz is loaded into the `three.js` scene and the web app do not crash.
