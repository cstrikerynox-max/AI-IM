<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CS2 3D Dust 2 - Gerçekçi FPS</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background: #000;
            color: #fff;
            font-family: 'Courier New', monospace;
            overflow: hidden;
        }
        #menu {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            text-align: center;
            z-index: 100;
        }
        #playButton {
            padding: 20px 40px;
            font-size: 24px;
            background: linear-gradient(45deg, #ff4500, #ff6347);
            border: none;
            color: white;
            cursor: pointer;
            border-radius: 10px;
            transition: all 0.3s;
            box-shadow: 0 0 20px #ff4500;
        }
        #playButton:hover {
            box-shadow: 0 0 40px #ff4500;
            transform: scale(1.05);
        }
        #hud {
            position: absolute;
            top: 20px;
            left: 20px;
            font-size: 20px;
            z-index: 50;
            text-shadow: 2px 2px 4px #000;
        }
        #crosshair {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 20px;
            height: 20px;
            border: 2px solid #ff0000;
            border-radius: 50%;
            z-index: 50;
            pointer-events: none;
        }
        #controls {
            position: absolute;
            bottom: 20px;
            left: 20px;
            font-size: 14px;
            color: #aaa;
            z-index: 50;
        }
        canvas {
            filter: brightness(1.2) contrast(1.5) saturate(1.8); /* Ultra HDR */
        }
    </style>
</head>
<body>
    <div id="menu">
        <h1>CS2 3D - Dust 2</h1>
        <p>Gerçekçi FPS | 5v5 Botlar</p>
        <button id="playButton">PLAY</button>
        <p style="font-size: 14px; margin-top: 20px;">WASD: Hareket | Mouse: Bakış | Click: Ateş | 1:USP 2:M4 3:AWP | R:Reload | ESC: Menü</p>
    </div>
    <div id="crosshair"></div>
    <div id="hud">
        <div>Health: <span id="health">100</span> | Ammo: <span id="ammo">12/12</span> | Kills: <span id="kills">0</span></div>
        <div>Silah: <span id="weapon">USP-S</span></div>
    </div>
    <div id="controls">
        Mouse Sensitivity: Orta | Ultra HDR Etkin
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r161/three.min.js"></script>
    <script>
        // Three.js 3D CS2 Dust 2
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.shadowMap.enabled = true;
        renderer.shadowMap.type = THREE.PCFSoftShadowMap;
        renderer.toneMapping = THREE.ACESFilmicToneMapping;
        renderer.toneMappingExposure = 1.2; // HDR simülasyonu
        document.body.appendChild(renderer.domElement);

        // Işıklar (gerçekçi gölgeler)
        const ambientLight = new THREE.AmbientLight(0x404040, 0.4);
        scene.add(ambientLight);
        const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
        directionalLight.position.set(50, 100, 50);
        directionalLight.castShadow = true;
        directionalLight.shadow.mapSize.width = 2048;
        directionalLight.shadow.mapSize.height = 2048;
        scene.add(directionalLight);

        // Skybox (Dust 2 çöl gökyüzü)
        const skyGeometry = new THREE.SphereGeometry(500, 32, 32);
        const skyMaterial = new THREE.MeshBasicMaterial({
            color: 0x87CEEB,
            side: THREE.BackSide
        });
        scene.add(new THREE.Mesh(skyGeometry, skyMaterial));

        // Dust 2 Haritası (3D - Gerçekçi ölçek)
        const mapSize = 200;
        const floorGeometry = new THREE.PlaneGeometry(mapSize, mapSize);
        const floorMaterial = new THREE.MeshLambertMaterial({ color: 0xD2B48C }); // Kum rengi
        const floor = new THREE.Mesh(floorGeometry, floorMaterial);
        floor.rotation.x = -Math.PI / 2;
        floor.receiveShadow = true;
        scene.add(floor);

        // Duvarlar (Dust 2 layout: Long A, Mid, B Site)
        const walls = [];
        const wallMaterial = new THREE.MeshLambertMaterial({ color: 0x8B4513 });
        const wallHeight = 20;
        // Dış duvarlar
        walls.push(new THREE.Mesh(new THREE.BoxGeometry(mapSize, wallHeight, 5), wallMaterial)); // Sol
        walls[0].position.set(-mapSize/2 + 2.5, wallHeight/2, 0);
        walls.push(new THREE.Mesh(new THREE.BoxGeometry(mapSize, wallHeight, 5), wallMaterial)); // Sağ
        walls[1].position.set(mapSize/2 - 2.5, wallHeight/2, 0);
        walls.push(new THREE.Mesh(new THREE.BoxGeometry(5, wallHeight, mapSize), wallMaterial)); // Arka
        walls[2].position.set(0, wallHeight/2, -mapSize/2 + 2.5);
        walls.push(new THREE.Mesh(new THREE.BoxGeometry(5, wallHeight, mapSize), wallMaterial)); // Ön
        walls[3].position.set(0, wallHeight/2, mapSize/2 - 2.5);

        // İç yapılar (A Long, Catwalk, B Boxes)
        walls.push(new THREE.Mesh(new THREE.BoxGeometry(30, wallHeight, 40), wallMaterial)); // A Site duvar
        walls[4].position.set(-60, wallHeight/2, 40);
        walls.push(new THREE.Mesh(new THREE.BoxGeometry(40, wallHeight, 20), wallMaterial)); // Mid
        walls[5].position.set(0, wallHeight/2, 0);
        walls.push(new THREE.Mesh(new THREE.BoxGeometry(25, 15, 25), wallMaterial)); // B Site kutu
        walls[6].position.set(50, 7.5, -50);

        walls.forEach(wall => {
            wall.castShadow = true;
            wall.receiveShadow = true;
            scene.add(wall);
        });

        // A/B Site işaretleri (Glow)
        const aSite = new THREE.Mesh(new THREE.CylinderGeometry(3, 3, 1, 8), new THREE.MeshBasicMaterial({ color: 0xFFFF00 }));
        aSite.position.set(-70, 1, 45);
        scene.add(aSite);
        const bSite = new THREE.Mesh(new THREE.CylinderGeometry(3, 3, 1, 8), new THREE.MeshBasicMaterial({ color: 0xFFFF00 }));
        bSite.position.set(55, 1, -55);
        scene.add(bSite);

        // Oyuncu değişkenleri
        const player = {
            position: new THREE.Vector3(0, 5, 0),
            velocity: new THREE.Vector3(),
            health: 100,
            ammo: 12,
            maxAmmo: 12,
            weapon: 0,
            lastFire: 0
        };
        camera.position.copy(player.position);

        // FPS Silah Modeli (Gerçekçi - Grup)
        const weaponGroup = new THREE.Group();
        camera.add(weaponGroup);
        scene.add(camera);

        function updateWeapon() {
            weaponGroup.clear();
            const w = weapons[player.weapon];
            // Silah gövdesi
            const gunBody = new THREE.Mesh(new THREE.BoxGeometry(0.5, 0.2, 2), new THREE.MeshPhongMaterial({ color: w.color }));
            gunBody.position.set(0.3, -0.3, -1);
            weaponGroup.add(gunBody);
            // Namlu
            const barrel = new THREE.Mesh(new THREE.CylinderGeometry(0.05, 0.05, 1.5), new THREE.MeshPhongMaterial({ color: 0x333333 }));
            barrel.rotation.z = Math.PI / 2;
            barrel.position.set(0.4, -0.3, -0.5);
            weaponGroup.add(barrel);
        }
        updateWeapon();

        // Silah Verileri (Gerçekçi CS2 stats)
        const weapons = [
            { name: 'USP-S', damage: 35, fireRate: 170, maxAmmo: 12, color: 0xFFFF00, recoil: 0.05 },
            { name: 'M4A4', damage: 33, fireRate: 100, maxAmmo: 30, color: 0x00FFFF, recoil: 0.08 },
            { name: 'AWP', damage: 143, fireRate: 1200, maxAmmo: 10, color: 0xFF00FF, recoil: 0.2 }
        ];

        // Botlar (3D modeller - Basit humanoid)
        let bots = [];
        let bullets = [];
        let particles = [];
        let kills = 0;
        function createBot(isCT = true) {
            const botGroup = new THREE.Group();
            // Vücut
            const body = new THREE.Mesh(new THREE.CylinderGeometry(1, 1.5, 3, 8), new THREE.MeshPhongMaterial({ color: isCT ? 0x0000FF : 0xFF0000 }));
            body.position.y = 1.5;
            botGroup.add(body);
            // Kafa
            const head = new THREE.Mesh(new THREE.SphereGeometry(0.8), new THREE.MeshPhongMaterial({ color: 0xFDBCB4 }));
            head.position.y = 3.5;
            botGroup.add(head);
            botGroup.position.set((Math.random() - 0.5) * 150, 5, (Math.random() - 0.5) * 150);
            botGroup.userData = { health: 100, ammo: 30, lastFire: 0, speed: 0.1, weapon: 1 };
            scene.add(botGroup);
            return botGroup;
        }
        function spawnBots() {
            bots = [];
            for (let i = 0; i < 5; i++) {
                bots.push(createBot(true)); // CT
                bots.push(createBot(false)); // T
            }
        }

        // Raycaster (Vurma sistemi)
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2(0, 0);

        // Kontroller
        const keys = {};
        let mouseX = 0, mouseY = 0;
        let isLocked = false;

        // Pointer Lock
        renderer.domElement.addEventListener('click', () => {
            if (!isLocked) renderer.domElement.requestPointerLock();
        });
        document.addEventListener('pointerlockchange', () => {
            isLocked = document.pointerLockElement === renderer.domElement;
        });
        document.addEventListener('mousemove', (event) => {
            if (!isLocked) return;
            mouseX -= event.movementX * 0.002;
            mouseY -= event.movementY * 0.002;
            mouseY = Math.max(-Math.PI/2, Math.min(Math.PI/2, mouseY));
        });

        // Klavye
        window.addEventListener('keydown', (e) => {
            keys[e.code] = true;
            if (e.code === 'Digit1') { player.weapon = 0; updateWeapon(); }
            if (e.code === 'Digit2') { player.weapon = 1; updateWeapon(); }
            if (e.code === 'Digit3') { player.weapon = 2; updateWeapon(); }
            if (e.code === 'KeyR') { player.ammo = weapons[player.weapon].maxAmmo; }
            if (e.code === 'Escape') {
                document.exitPointerLock();
                showMenu();
            }
        });
        window.addEventListener('keyup', (e) => { keys[e.code] = false; });

        // Ateş
        renderer.domElement.addEventListener('mousedown', () => {
            const w = weapons[player.weapon];
            if (Date.now() - player.lastFire < w.fireRate || player.ammo <= 0) return;
            player.ammo--;
            player.lastFire = Date.now();

            // Mermi efekti (line trail)
            const bulletGeometry = new THREE.BufferGeometry().setFromPoints([camera.position, camera.position.clone().add(camera.getWorldDirection(new THREE.Vector3()).multiplyScalar(100))]);
            const bulletMaterial = new THREE.LineBasicMaterial({ color: w.color });
            const bulletLine = new THREE.Line(bulletGeometry, bulletMaterial);
            scene.add(bulletLine);
            bullets.push({ mesh: bulletLine, life: 50 });

            // Raycast vurma
            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects(bots.map(b => b.children).flat());
            if (intersects.length > 0) {
                const hitBot = intersects[0].object.parent;
                hitBot.userData.health -= w.damage;
                createExplosion(hitBot.position);
                if (hitBot.userData.health <= 0) {
                    scene.remove(hitBot);
                    bots = bots.filter(b => b !== hitBot);
                    kills++;
                }
            }

            // Recoil animasyon
            weaponGroup.rotation.z = (Math.random() - 0.5) * w.recoil;
            weaponGroup.rotation.x = Math.random() * w.recoil * 0.5;
            setTimeout(() => { weaponGroup.rotation.set(0,0,0); }, 100);
        });

        function createExplosion(pos) {
            for (let i = 0; i < 20; i++) {
                const particle = new THREE.Mesh(new THREE.SphereGeometry(0.2), new THREE.MeshBasicMaterial({ color: 0xFF4400 }));
                particle.position.copy(pos);
                particle.velocity = new THREE.Vector3((Math.random() - 0.5)*2, Math.random()*2, (Math.random() - 0.5)*2);
                scene.add(particle);
                particles.push(particle);
            }
        }

        // Oyun Loop
        function animate() {
            requestAnimationFrame(animate);

            // Kamera rotasyon
            camera.rotation.order = 'YXZ';
            camera.rotation.y = mouseX;
            camera.rotation.x = mouseY;

            // Hareket
            const direction = new THREE.Vector3();
            const frontVector = new THREE.Vector3(0, 0, -1).applyQuaternion(camera.quaternion);
            const sideVector = new THREE.Vector3(1, 0, 0).applyQuaternion(camera.quaternion);
            if (keys['KeyW']) direction.add(frontVector);
            if (keys['KeyS']) direction.sub(frontVector);
            if (keys['KeyA']) direction.sub(sideVector);
            if (keys['KeyD']) direction.add(sideVector);
            direction.normalize().multiplyScalar(0.2);
            player.position.add(direction);
            camera.position.copy(player.position);

            // Sınır
            player.position.clamp(new THREE.Vector3(-90, 1, -90), new THREE.Vector3(90, 20, 90));

            // Bot AI
            bots.forEach(bot => {
                const dir = player.position.clone().sub(bot.position).normalize();
                bot.position.add(dir.multiplyScalar(bot.userData.speed));
                bot.lookAt(player.position);

                // Bot ateş (rastgele)
                if (Math.random() < 0.01 && bot.userData.ammo > 0) {
                    bot.userData.ammo--;
                    const botRay = new THREE.Raycaster(bot.position, dir);
                    const botIntersects = botRay.intersectObject(camera);
                    if (botIntersects.length > 0 && botIntersects[0].distance < 50) {
                        player.health -= 20;
                        createExplosion(camera.position);
                    }
                }
            });

            // Mermi temizle
            bullets.forEach((b, i) => {
                b.life--;
                if (b.life <= 0) {
                    scene.remove(b.mesh);
                    bullets.splice(i, 1);
                }
            });

            // Particle güncelle
            particles.forEach((p, i) => {
                p.position.add(p.velocity);
                p.velocity.multiplyScalar(0.95);
                p.material.opacity = p.scale.x;
                if (p.velocity.length() < 0.01) {
                    scene.remove(p);
                    particles.splice(i, 1);
                }
            });

            // Kazan/Öl
            if (player.health <= 0) {
                alert('Öldün! Kills: ' + kills);
                reset();
            }
            if (bots.length === 0) {
                alert('Kazandın! Kills: ' + kills);
                reset();
            }

            // HUD
            document.getElementById('health').textContent = Math.max(0, player.health);
            document.getElementById('ammo').textContent = `${player.ammo}/${weapons[player.weapon].maxAmmo}`;
            document.getElementById('kills').textContent = kills;
            document.getElementById('weapon').textContent = weapons[player.weapon].name;

            renderer.render(scene, camera);
        }

        function reset() {
            player.health = 100;
            player.ammo = 12;
            player.weapon = 0;
            kills = 0;
            bots.forEach(b => scene.remove(b));
            spawnBots();
            player.position.set(0, 5, 0);
        }

        function showMenu() {
            document.getElementById('menu').style.display = 'block';
            isLocked = false;
        }

        // Başlat
        const playButton = document.getElementById('playButton');
        playButton.addEventListener('click', () => {
            document.getElementById('menu').style.display = 'none';
            spawnBots();
            animate();
        });

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>
</html>
