
<template>
    <div class="viewer-card">
      <div class="viewer-heading">
        <div>
          <span class="viewer-label">TECHSHOP 3D EXPERIENCE</span>
          <h3>Khám phá sản phẩm</h3>
          <p>Kéo chuột để xoay · Cuộn để phóng to</p>
        </div>
  
        <span class="demo-badge">3D DEMO</span>
      </div>
  
      <div class="viewer-stage">
        <div ref="container3D" class="three-container"></div>
  
        <div v-if="loadingError" class="viewer-error">
          {{ loadingError }}
        </div>
  
        <div class="viewer-controls">
          <button type="button" @click="toggleExplode">
            <i class="fa-solid fa-layer-group"></i>
            {{ isExploded ? "Lắp ráp lại" : "Tách linh kiện" }}
          </button>
  
          <button
            type="button"
            class="reset-button"
            @click="resetView"
            title="Đặt lại góc nhìn"
          >
            <i class="fa-solid fa-rotate-left"></i>
          </button>
        </div>
      </div>
  
      <div class="viewer-footer">
        <span>
          <i class="fa-solid fa-arrows-rotate"></i>
          Xoay tự do
        </span>
  
        <span>
          <i class="fa-solid fa-magnifying-glass-plus"></i>
          Zoom
        </span>
  
        <span>
          <i class="fa-solid fa-cubes"></i>
          Tách lớp
        </span>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, onBeforeUnmount } from "vue";
  
  import * as THREE from "three";
  
  import { OrbitControls } from
    "three/addons/controls/OrbitControls.js";
  
  import { RoundedBoxGeometry } from
    "three/addons/geometries/RoundedBoxGeometry.js";
  
  const container3D = ref(null);
  const isExploded = ref(false);
  const loadingError = ref("");
  
  let scene = null;
  let camera = null;
  let renderer = null;
  let controls = null;
  let animationId = null;
  
  let resizeObserver = null;
  
  const parts = [];
  
  // Tạo vật liệu
  const material = (color, roughness = 0.45, metalness = 0.05) => {
    return new THREE.MeshStandardMaterial({
      color,
      roughness,
      metalness,
    });
  };
  
  // Tạo khối có cạnh bo tròn
  function roundedBox(width, height, depth, color, radius = 0.04) {
    const geometry = new RoundedBoxGeometry(
      width,
      height,
      depth,
      3,
      radius
    );
  
    const mesh = new THREE.Mesh(
      geometry,
      material(color)
    );
  
    mesh.castShadow = true;
    mesh.receiveShadow = true;
  
    return mesh;
  }
  
  // Thêm một lớp có thể tách ra
  function addPart(group, baseY, distance) {
    group.position.y = baseY;
  
    group.userData.baseY = baseY;
    group.userData.targetY = baseY;
    group.userData.explodeDistance = distance;
  
    scene.add(group);
    parts.push(group);
  }
  
  // Tạo mô hình bàn phím thử nghiệm
  function createKeyboard() {
    // VỎ BÀN PHÍM
    const caseGroup = new THREE.Group();
  
    const shell = roundedBox(
      7.5,
      0.44,
      2.85,
      "#343A40",
      0.13
    );
  
    shell.position.y = 0;
  
    caseGroup.add(shell);
  
    addPart(caseGroup, 0, 0);
  
    // LỚP ĐỆM
    const foamGroup = new THREE.Group();
  
    const foam = roundedBox(
      7.05,
      0.045,
      2.45,
      "#777F87",
      0.02
    );
  
    foamGroup.add(foam);
  
    addPart(foamGroup, 0.25, 0.35);
  
    // PCB
    const pcbGroup = new THREE.Group();
  
    const pcb = roundedBox(
      7.05,
      0.055,
      2.45,
      "#285A45",
      0.025
    );
  
    pcbGroup.add(pcb);
  
    addPart(pcbGroup, 0.32, 0.70);
  
    // PLATE
    const plateGroup = new THREE.Group();
  
    const plate = roundedBox(
      7.2,
      0.07,
      2.58,
      "#B6BEC7",
      0.025
    );
  
    plateGroup.add(plate);
  
    addPart(plateGroup, 0.42, 1.05);
  
    // TẠO BỐ CỤC PHÍM
    const switchGroup = new THREE.Group();
    const keycapGroup = new THREE.Group();
  
    const unit = 0.405;
    const gap = 0.045;
    const keyDepth = 0.34;
    const rowGap = 0.11;
  
    const layouts = [
      Array(15).fill(1),
      Array(15).fill(1),
      Array(15).fill(1),
      Array(15).fill(1),
      [1, 1, 1, 1, 1, 5, 1, 1, 1, 1],
    ];
  
    layouts.forEach((row, rowIndex) => {
      const totalWidth =
        row.reduce((sum, value) => sum + value * unit, 0) +
        (row.length - 1) * gap;
  
      let cursorX = -totalWidth / 2;
  
      const z =
        (rowIndex - (layouts.length - 1) / 2) *
        (keyDepth + rowGap);
  
      row.forEach((widthUnits) => {
        const keyWidth =
          widthUnits * unit + (widthUnits - 1) * gap - gap;
  
        const x = cursorX + keyWidth / 2;
  
        // SWITCH
        const switchMesh = roundedBox(
          0.17,
          0.15,
          0.17,
          "#A94942",
          0.025
        );
  
        switchMesh.position.set(x, 0.055, z);
        switchGroup.add(switchMesh);
  
        // KEYCAP
        const cap = roundedBox(
          keyWidth,
          0.15,
          keyDepth,
          "#E5E1D9",
          0.045
        );
  
        cap.position.set(x, 0.09, z);
        keycapGroup.add(cap);
  
        cursorX += keyWidth + gap;
      });
    });
  
    // NÚM XOAY
    const knob = new THREE.Mesh(
      new THREE.CylinderGeometry(0.17, 0.17, 0.13, 32),
      material("#AEB8C2", 0.27, 0.7)
    );
  
    knob.position.set(3.05, 0.12, -1.05);
    knob.castShadow = true;
  
    keycapGroup.add(knob);
  
    addPart(switchGroup, 0.55, 1.40);
    addPart(keycapGroup, 0.74, 1.75);
  }
  
  // Khởi tạo Three.js
  function init3D() {
    const container = container3D.value;
  
    if (!container) return;
  
    try {
      scene = new THREE.Scene();
  
      scene.background = new THREE.Color("#E9EDF1");
  
      camera = new THREE.PerspectiveCamera(
        35,
        container.clientWidth / container.clientHeight,
        0.1,
        100
      );
  
      camera.position.set(0, 5.8, 9);
  
      renderer = new THREE.WebGLRenderer({
        antialias: true,
        alpha: false,
        powerPreference: "high-performance",
      });
  
      renderer.setPixelRatio(
        Math.min(window.devicePixelRatio || 1, 1.5)
      );
  
      renderer.setSize(
        container.clientWidth,
        container.clientHeight
      );
  
      renderer.shadowMap.enabled = true;
      renderer.shadowMap.type = THREE.PCFSoftShadowMap;
  
      renderer.outputColorSpace = THREE.SRGBColorSpace;
  
      renderer.toneMapping = THREE.ACESFilmicToneMapping;
      renderer.toneMappingExposure = 1.15;
  
      container.appendChild(renderer.domElement);
  
      // Điều khiển camera
      controls = new OrbitControls(
        camera,
        renderer.domElement
      );
  
      controls.enableDamping = true;
      controls.dampingFactor = 0.06;
  
      controls.enablePan = false;
      controls.minDistance = 6;
      controls.maxDistance = 15;
  
      controls.target.set(0, 0.65, 0);
  
      // ÁNH SÁNG MÔI TRƯỜNG
      const ambient = new THREE.HemisphereLight(
        "#FFFFFF",
        "#8B929A",
        2.0
      );
  
      scene.add(ambient);
  
      // ÁNH SÁNG CHÍNH
      const mainLight = new THREE.DirectionalLight(
        "#FFFFFF",
        3.0
      );
  
      mainLight.position.set(-4, 8, 6);
      mainLight.castShadow = true;
  
      mainLight.shadow.mapSize.set(1024, 1024);
  
      scene.add(mainLight);
  
      // ÁNH SÁNG PHỤ
      const fillLight = new THREE.DirectionalLight(
        "#DCE8F5",
        1.3
      );
  
      fillLight.position.set(5, 4, -5);
  
      scene.add(fillLight);
  
      // MẶT SÀN
      const floor = new THREE.Mesh(
        new THREE.PlaneGeometry(200, 200),
        material("#E9EDF1", 0.85, 0)
      );
  
      floor.rotation.x = -Math.PI / 2;
      floor.position.y = -0.26;
      floor.receiveShadow = true;
  
      scene.add(floor);
  
      // TẠO BÀN PHÍM
      createKeyboard();
  
      // VÒNG LẶP RENDER
      function animate() {
        animationId = requestAnimationFrame(animate);
  
        // Di chuyển từng lớp khi bung/lắp
        parts.forEach((part) => {
          part.position.y +=
            (part.userData.targetY - part.position.y) * 0.12;
        });
  
        controls.update();
  
        renderer.render(scene, camera);
      }
  
      animate();
  
      // Tự điều chỉnh khi đổi kích thước màn hình
      resizeObserver = new ResizeObserver(() => {
        resizeViewer();
      });
  
      resizeObserver.observe(container);
  
    } catch (error) {
      console.error("Lỗi khởi tạo 3D:", error);
  
      loadingError.value =
        "Không thể khởi tạo mô hình 3D trên trình duyệt này.";
    }
  }
  
  // Điều chỉnh kích thước
  function resizeViewer() {
    if (!container3D.value || !renderer || !camera) return;
  
    const width = container3D.value.clientWidth;
    const height = container3D.value.clientHeight;
  
    if (!width || !height) return;
  
    camera.aspect = width / height;
    camera.updateProjectionMatrix();
  
    renderer.setSize(width, height);
  }
  
  // Tách và lắp linh kiện
  function toggleExplode() {
    isExploded.value = !isExploded.value;
  
    parts.forEach((part) => {
      const baseY = part.userData.baseY;
      const distance = part.userData.explodeDistance;
  
      part.userData.targetY = isExploded.value
        ? baseY + distance
        : baseY;
    });
  }
  
  // Đặt lại góc nhìn
  function resetView() {
    if (!camera || !controls) return;
  
    controls.reset();
  
    isExploded.value = false;
  
    parts.forEach((part) => {
      part.userData.targetY = part.userData.baseY;
    });
  }
  
  // Bắt đầu
  onMounted(() => {
    init3D();
  });
  
  // Dọn tài nguyên khi rời component
  onBeforeUnmount(() => {
    if (animationId) {
      cancelAnimationFrame(animationId);
    }
  
    if (resizeObserver) {
      resizeObserver.disconnect();
    }
  
    if (controls) {
      controls.dispose();
    }
  
    if (scene) {
      scene.traverse((object) => {
        if (object.geometry) {
          object.geometry.dispose();
        }
  
        if (object.material) {
          const materials = Array.isArray(object.material)
            ? object.material
            : [object.material];
  
          materials.forEach((item) => item.dispose());
        }
      });
    }
  
    if (renderer) {
      renderer.dispose();
  
      if (renderer.domElement.parentNode) {
        renderer.domElement.parentNode.removeChild(
          renderer.domElement
        );
      }
    }
  });
  </script>
  
  <style scoped>
  .viewer-card {
    width: 100%;
    overflow: hidden;
    border: 1px solid #e5e8ec;
    border-radius: 16px;
    background: #ffffff;
    color: #252b32;
    box-shadow: 0 12px 35px rgba(20, 35, 50, 0.06);
  }
  
  .viewer-heading {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    gap: 15px;
    padding: 22px 24px 17px;
  }
  
  .viewer-label {
    color: #82909e;
    font-size: 10px;
    letter-spacing: 1.6px;
    font-weight: 750;
  }
  
  .viewer-heading h3 {
    margin: 7px 0 5px;
    font-size: 21px;
    font-weight: 750;
    color: #26313d;
  }
  
  .viewer-heading p {
    margin: 0;
    font-size: 12px;
    color: #84909b;
  }
  
  .demo-badge {
    padding: 7px 11px;
    border-radius: 20px;
    color: #3b6c91;
    background: #eaf2f8;
    font-size: 10px;
    font-weight: 750;
    letter-spacing: 1px;
  }
  
  .viewer-stage {
    position: relative;
    width: 100%;
    height: 460px;
    overflow: hidden;
    background: #e9edf1;
  }
  
  .three-container {
    width: 100%;
    height: 100%;
  }
  
  .three-container canvas {
    display: block;
    width: 100%;
    height: 100%;
  }
  
  .viewer-controls {
    position: absolute;
    right: 18px;
    bottom: 18px;
    display: flex;
    gap: 9px;
  }
  
  .viewer-controls button {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    min-height: 40px;
    padding: 0 15px;
    border: 1px solid rgba(255,255,255,0.8);
    border-radius: 9px;
    background: rgba(255,255,255,0.94);
    color: #354453;
    font-size: 12px;
    font-weight: 650;
    cursor: pointer;
    box-shadow: 0 4px 14px rgba(30,45,60,0.08);
  }
  
  .viewer-controls button:hover {
    background: #ffffff;
    color: #176fc1;
  }
  
  .viewer-controls .reset-button {
    width: 40px;
    padding: 0;
  }
  
  .viewer-footer {
    display: flex;
    justify-content: center;
    gap: 30px;
    padding: 15px;
    color: #7b8997;
    font-size: 11px;
  }
  
  .viewer-footer span {
    display: flex;
    align-items: center;
    gap: 7px;
  }
  
  .viewer-error {
    position: absolute;
    inset: 0;
    display: grid;
    place-items: center;
    padding: 20px;
    text-align: center;
    color: #a52a2a;
    background: #f5eeee;
  }
  
  @media (max-width: 600px) {
    .viewer-stage {
      height: 330px;
    }
  
    .viewer-heading {
      padding: 17px;
    }
  
    .viewer-heading h3 {
      font-size: 18px;
    }
  
    .viewer-footer {
      gap: 13px;
      flex-wrap: wrap;
    }
  
    .viewer-controls {
      right: 10px;
      bottom: 10px;
    }
  }
  </style>
  