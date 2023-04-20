# Vue.js + Three.js
Base project for further tests
Three.js_USDZLoader branch:  
This branch is used to test the use of **Three.js** official Add-on `USDZLoader`.

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

### Three.js USDZLoader() tests

12. Add import at the start of the script:  
`import { USDZLoader } from "three/examples/jsm/loaders/USDZLoader";`
13. Add this code snippet to the end of the script:
      ```js
      // Create a new USDZ loader instance
      const usdzLoader = new USDZLoader();

      // Load the USDZ model from the specified path
      usdzLoader.load(
        "/Room.usdz", // Path to the USDZ model file
        (usdz) => { // Callback function executed when the model is loaded
          scene.add(usdz.scene); // Add the model's scene object to the three.js scene
        },
        undefined, // Optional callback function executed while the model is loading
        (error) => { // Callback function executed if there is an error while loading the model
          console.error(error); // Print the error message to the console
        }
      );
      ```
**Files test results:**
1. /buster_drone/scene.gltf  
   a) USDZLoader  
   `invalid zip file`  
   b) GLTFLoader  
   `Working fine!`  
2. /buster_drone.zip  
   a) USDZLoader  
   `THREE.USDZLoader: No usda file found.`  
   b) GLTFLoader  
   `SyntaxError: Unexpected token 'P', ... is not valid JSON`  
3. /buster_drone.usdz  
   Same result as previous .zip test.  
4. /Room.usdz & /livingroom.usdz  
   a) USDZLoader  
   `TypeError: Cannot use 'in' operator to search for 'def ""' in undefined`  
   b) GLTFLoader  
   `SyntaxError: Unexpected token 'P', ... is not valid JSON`  
   
### Conclusion:  
I opened a [issue](https://github.com/mrdoob/three.js/issues/25743) on the [three.js collaborative github](https://github.com/mrdoob/three.js).  
It seems `three.js` is out of date with the support of `.usdz` format generated by **RoomPlan** API (and other sources using `usdc` and not `usda` meta file).  
 
