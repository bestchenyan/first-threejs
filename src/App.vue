<script setup>
import * as THREE from "three";
import Stats from "three/addons/libs/stats.module.js";
import { GLTFLoader, GPUStatsPanel } from "three/addons";
import { ref, onMounted } from "vue";

const target = ref();

const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);

// const camera = new THREE.PerspectiveCamera(
//   75,
//   window.innerWidth / window.innerHeight,
//   0.1,
//   1000
// );

const camera = new THREE.PerspectiveCamera(
  45,
  window.innerWidth / window.innerHeight,
  1,
  500
);
camera.position.set(0, 0, 100);
camera.lookAt(0, 0, 0);

const scene = new THREE.Scene();

const stats = new Stats();

let MeshCube = null;

onMounted(() => {
  loadStats();

  target.value.appendChild(renderer.domElement);

  drawBoxGeometry();
  // drawLines();

  // loadGLTF();

  window.addEventListener("resize", () => {
    renderer.setSize(window.innerWidth, window.innerHeight);
  });
});

function loadStats() {
  stats.setMode(0);
  document.body.appendChild(stats.dom);

  const gpuPanel = new GPUStatsPanel(renderer.getContext());
  stats.addPanel(gpuPanel);

  stats.showPanel(0);
}

function loadGLTF() {
  const loader = new GLTFLoader();

  loader.load(
    "/models/警卫人员/scene.gltf",
    (gltf) => {
      gltf.scene.name = "警卫人员";
      gltf.scene.scale.set(1.5, 1.5, 1.5);
      gltf.scene.position.set(-55, 0, 7.7);
      // 设置旋转Y轴45度
      gltf.scene.rotation.y = -0.5 * Math.PI;
      scene.add(gltf.scene);
    },
    undefined,
    (error) => {
      console.error(error);
    }
  );
}

function drawLines() {
  const material = new THREE.LineBasicMaterial({ color: 0x0000ff });
  const points = [];
  points.push(new THREE.Vector3(-10, 0, 0));
  points.push(new THREE.Vector3(0, 10, 0));
  points.push(new THREE.Vector3(10, 0, 0));

  const geometry = new THREE.BufferGeometry().setFromPoints(points);
  const line = new THREE.Line(geometry, material);

  scene.add(line);
  renderer.render(scene, camera);
}

function drawBoxGeometry() {
  const geometry = new THREE.BoxGeometry(1, 1, 1);
  const material = new THREE.MeshNormalMaterial();
  MeshCube = new THREE.Mesh(geometry, material);
  scene.add(MeshCube);

  camera.position.z = 5;

  animate();
}

function animate() {
  requestAnimationFrame(animate);
  stats.update();
  MeshCube.rotation.x += 0.01;
  MeshCube.rotation.y += 0.01;

  renderer.render(scene, camera);
}
</script>

<template>
  <div id="container" ref="target"></div>
</template>

<style scoped>
#container {
  width: 100%;
  height: 100%;
}
</style>
