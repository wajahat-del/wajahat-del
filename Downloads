<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>3D Car Game</title>
  <style>
    body { margin: 0; overflow: hidden; }
    canvas { display: block; }
  </style>
</head>
<body>
  <script src="https://cdn.jsdelivr.net/npm/three@0.153.0/build/three.min.js"></script>
  <script>
    let scene, camera, renderer, car, ground;
    let speed = 0;
    let maxSpeed = 78; // ~280 km/h
    let acceleration = 0.5;
    let friction = 0.1;
    let keys = {};
    let cameraModes = [];
    let currentCameraIndex = 0;

    function init() {
      scene = new THREE.Scene();
      scene.background = new THREE.Color(0xa0d0f0);

      // Cameras
      for (let i = 0; i < 4; i++) {
        cameraModes[i] = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 5000);
      }
      camera = cameraModes[0];

      // Renderer
      renderer = new THREE.WebGLRenderer();
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(renderer.domElement);

      // Light
      const ambientLight = new THREE.AmbientLight(0x888888);
      scene.add(ambientLight);

      const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
      directionalLight.position.set(100, 200, 100);
      scene.add(directionalLight);

      const headlight = new THREE.PointLight(0xffffcc, 1, 50);
      
      // Ground (5 sq km = 2240 x 2240 units)
      const groundGeometry = new THREE.PlaneGeometry(2240, 2240);
      const groundMaterial = new THREE.MeshStandardMaterial({ color: 0x555555 });
      ground = new THREE.Mesh(groundGeometry, groundMaterial);
      ground.rotation.x = -Math.PI / 2;
      scene.add(ground);

      // Car
      const carGeometry = new THREE.BoxGeometry(2, 1, 4);
      const carMaterial = new THREE.MeshStandardMaterial({ color: 0xff0000 });
      car = new THREE.Mesh(carGeometry, carMaterial);
      car.position.set(0, 0.5, 0);
      scene.add(car);
      car.add(headlight);
      headlight.position.set(0, 0.5, 2.5);

      // Characters (placeholders)
      addCharacter('boy', -20, 0, 0, 0x0000ff);
      addCharacter('girl', 20, 0, 0, 0xff00ff);
      addCharacter('helmet', 0, 0, -20, 0xffff00);

      // Controls
      document.addEventListener('keydown', (e) => {
        keys[e.key.toLowerCase()] = true;
        if (e.key >= '1' && e.key <= '4') {
          currentCameraIndex = parseInt(e.key) - 1;
        }
        if (e.key === '5') {
          setTopDownCamera();
        }
      });
      document.addEventListener('keyup', (e) => {
        keys[e.key.toLowerCase()] = false;
      });

      animate();
    }

    function addCharacter(name, x, y, z, color) {
      const geo = new THREE.SphereGeometry(1, 16, 16);
      const mat = new THREE.MeshStandardMaterial({ color: color });
      const mesh = new THREE.Mesh(geo, mat);
      mesh.position.set(x, 1, z);
      scene.add(mesh);
    }

    function updateCarMovement() {
      // Forward/backward
      if (keys['w'] || keys['arrowup']) {
        speed += acceleration;
      } else if (keys['s'] || keys['arrowdown']) {
        speed -= acceleration;
      } else {
        // Friction
        if (speed > 0) speed -= friction;
        if (speed < 0) speed += friction;
        if (Math.abs(speed) < friction) speed = 0;
      }

      // Clamp speed
      speed = Math.max(-maxSpeed, Math.min(maxSpeed, speed));

      // Turning
      if (keys['a'] || keys['arrowleft']) {
        car.rotation.y += 0.03;
      }
      if (keys['d'] || keys['arrowright']) {
        car.rotation.y -= 0.03;
      }

      // Move car
      car.position.x -= Math.sin(car.rotation.y) * speed * 0.1;
      car.position.z -= Math.cos(car.rotation.y) * speed * 0.1;
    }

    function updateCamera() {
      let cam = cameraModes[currentCameraIndex];
      let offset;
      switch (currentCameraIndex) {
        case 0: // Behind
          offset = new THREE.Vector3(0, 5, 10);
          break;
        case 1: // Front
          offset = new THREE.Vector3(0, 3, -6);
          break;
        case 2: // Side
          offset = new THREE.Vector3(6, 3, 0);
          break;
        case 3: // Driver seat
          offset = new THREE.Vector3(0, 1.5, 1);
          break;
      }
      let carWorldPos = new THREE.Vector3();
      car.getWorldPosition(carWorldPos);

      let carDir = new THREE.Vector3(0, 0, 1);
      carDir.applyQuaternion(car.quaternion);
      let camPos = carWorldPos.clone().add(offset.applyQuaternion(car.quaternion));

      cam.position.copy(camPos);
      cam.lookAt(car.position);
      camera = cam;
    }

    function setTopDownCamera() {
      let cam = cameraModes[0];
      cam.position.set(car.position.x, 100, car.position.z);
      cam.lookAt(car.position);
      camera = cam;
    }

    function animate() {
      requestAnimationFrame(animate);
      updateCarMovement();
      updateCamera();
      renderer.render(scene, camera);
    }

    init();
  </script>
</body>
</html>