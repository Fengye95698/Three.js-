<template>
  <div ref="container" class="container"></div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
export default {
  data() {
    return {
      scene: null,
      camera: null,
      mesh: null,
      renderer: null,
      controls: null,
      ball_mesh: null,
    };
  },
  mounted() {
    this.init();
    this.anmiation();
    this.resize();
  },
  methods: {
    init() {
      let container = this.$refs.container;
      // 1.创建场景
      this.scene = new THREE.Scene();
      // 2.创建摄像机
      this.camera = new THREE.PerspectiveCamera(
        90,
        container.clientWidth / container.clientHeight,
        0.1,
        1000
      );
      //    3.创建threejs渲染器
      this.renderer = new THREE.WebGLRenderer();
      // 4.设置渲染器场景的大小
      this.renderer.setClearColor(new THREE.Color(0xeeeeee));
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      // 设置渲染物体阴影
      this.renderer.shadowMap.enabled = true;
      // 把渲染器添加到页面当中去
      container.appendChild(this.renderer.domElement);
      // 创建地面
      const planeGeometry = new THREE.PlaneGeometry(80, 40);
      const planeMaterial = new THREE.MeshLambertMaterial({
        color: "0xcccccc",
      });
      const plane = new THREE.Mesh(planeGeometry, planeMaterial);
      // 接收阴影
      plane.receiveShadow = true;
      plane.rotation.x = -0.5 * Math.PI;
      plane.position.set(0, 0, 0);
      //   创建几何模型
      const geometry = new THREE.BoxGeometry(8, 8, 8);
      //   添加贴图
      const texture = new THREE.TextureLoader().load(
        require("../assets/img/logo.png")
      );
      //   创建材质
      const material = new THREE.MeshLambertMaterial({ map: texture });
      //   const material = new THREE.MeshBasicMaterial({ color: "orange" });
      //   创建网格对象
      this.mesh = new THREE.Mesh(geometry, material);
      this.mesh.position.set(0, 3, 0);
      this.mesh.castShadow = true;
      // 添加球体
      const ball = new THREE.SphereGeometry(5, 32, 32);
      const ball_material = new THREE.MeshLambertMaterial({ color: "purple" });
      this.ball_mesh = new THREE.Mesh(ball, ball_material);
      this.ball_mesh.position.set(15, 6, 0);
      this.ball_mesh.castShadow = true;

      //   添加辅助线
      const axishelper = new THREE.AxesHelper(20);
      //  创建控制器
      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      //  创建聚光灯
      const spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(0, 30, 40);
      spotLight.castShadow = true;
      this.camera.position.set(20, 20, 20);
      this.camera.lookAt(this.scene.position);
      //   把网格对象添加到场景中去
      this.scene.add(this.mesh, axishelper, plane, this.ball_mesh, spotLight);
    },
    // 动画
    anmiation() {
      requestAnimationFrame(this.anmiation);
      this.mesh.rotation.x += 0.01;
      this.mesh.rotation.y += 0.01;
      // this.ball_mesh.translateOnAxis(
      //   new THREE.Vector3(1, 0, 0).normalize(),
      //   10
      // );
      if (this.ball_mesh.position.y <= 20) {
        this.ball_mesh.position.y += 0.1;
      } else {
        this.ball_mesh.position.y -= 14;
      }
      // 渲染器渲染场景和摄像机
      this.renderer.render(this.scene, this.camera);
    },
    // 动态设置屏幕宽高占比
    resize() {
      window.addEventListener("resize", () => {
        // 初始化摄像机
        this.camera.aspect =
          this.$refs.container.clientWidth / this.$refs.container.clientHeight;
        this.camera.updateProjectionMatrix();
        this.renderer.setSize(
          this.$refs.container.clientWidth,
          this.$refs.container.clientHeight
        );
      });
    },
  },
};
</script>

<style>
.container {
  position: absolute;
  width: 100%;
  height: 100%;
}
</style>
