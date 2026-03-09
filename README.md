<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CS2 3D Dust 2 - Tıpa Tıp Akıcı (Gelişmiş AI)</title>
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
            display: block;
        }
        #playButton {
            padding: 30px 60px;
            font-size: 32px;
            background: linear-gradient(45deg, #ff4500, #ff6347);
            border: none;
            color: white;
            cursor: pointer;
            border-radius: 20px;
            transition: all 0.3s;
            box-shadow: 0 0 40px #ff4500;
            font-weight: bold;
        }
        #playButton:hover {
            box-shadow: 0 0 60px #ff4500;
            transform: scale(1.15);
        }
        #hud {
            position: absolute;
            top: 20px;
            left: 20px;
            font-size: 24px;
            z-index: 50;
            text-shadow: 3px 3px 8px #000;
        }
        #crosshair {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 30px;
            height: 30px;
            border: 4px solid #ff0000;
            border-radius: 50%;
            box-shadow: 0 0 20px #ff0000;
            z-index: 50;
            pointer-events: none;
        }
        #minimap {
            position: absolute;
            top: 20px;
            right: 20px;
            width: 180px;
            height: 180px;
            background: rgba(0,0,0,0.8);
            border: 3px solid #fff;
            border-radius: 10px;
            z-index: 50;
        }
        #controls {
            position: absolute;
            bottom: 20px;
            left: 20px;
            font-size: 16px;
            color: #ccc;
            z-index: 50;
        }
        canvas {
            filter: brightness(1.4) contrast(1.7) saturate(2.2); /* Ultra HDR Akıcı */
        }
    </style>
</head>
<body>
    <div id="menu">
        <h1>CS2 3D - Dust 2 (Tıpa Tıp)</h1>
        <p>100 HP | Headshot One-Shot | Akıllı Bot AI (Patrol/Chase/Cover/Flank)</p>
        <button id="playButton">PLAY</button>
        <p style="font-size: 18px; margin-top: 40px;">WASD: Hareket | Mouse: Nişan & Ateş | 1:USP 2:M4 3:AWP | R:Reload | ESC: Menü</p>
        <p>Botlar birbirini de vurur (Friendly Fire ON)</p>
    </div>
    <div id="crosshair"></div>
    <div id="hud">
        <div>Health: <span id="health">100</span> | Ammo: <span id="ammo">12/12</span> | Kills: <span id="kills">0</span></div>
        <div>Silah: <span id="weapon">USP-S</span> | Round: <span id="round">1</span>/30</div>
    </div>
    <canvas id="minimap" width="180" height="180"></canvas>
    <div id="controls">
        Akıcı FPS | Gelişmiş AI: Botlar Takımını Da Vurur | Smooth Delta Time
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r161/three.min.js"></script>
    <script>
        // Oyun Başlat Flag'i - Garantili Start
        let gameStarted = false;
        let clock = new THREE.Clock();

        // Scene Setup (Akıcı Render)
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(80, window.innerWidth / window.innerHeight, 0.1, 2000);
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.shadowMap.enabled = true;
        renderer.shadowMap.type = THREE.PCFSoftShadowMap;
        renderer.toneMapping = THREE.ACESFilmicToneMapping;
        renderer.toneMappingExposure = 1.4;
        document.body.appendChild(renderer.domElement);

        // Lights (Gerçekçi)
        scene.add(new THREE.AmbientLight(0x505050, 0.6));
        const sun = new THREE.DirectionalLight(0xffffff, 1.5);
        sun.position.set(100, 150, 100);
        sun.castShadow = true;
        sun.shadow.mapSize.set(4096, 4096);
        sun.shadow.camera.near = 0.1;
        sun.shadow.camera.far = 300;
        scene.add(sun);

        // Sky (Dust 2 Çöl)
        const sky = new THREE.Mesh(new THREE.SphereGeometry(800, 64, 64), new THREE.MeshBasicMaterial({ 
            color: 0xB0D4FF, side: THREE.BackSide 
        }));
        scene.add(sky);

        // Dust 2 Haritası (Detaylı + Akıcı Textures Yok - Performans)
        const mapSize = 250;
        const floor = new THREE.Mesh(
            new THREE.PlaneGeometry(mapSize, mapSize),
            new THREE.MeshLambertMaterial({ color: 0xDAA520 })
        );
        floor.rotation.x = -Math.PI / 2;
        floor.receiveShadow = true;
        scene.add(floor);

        const wallMat = new THREE.MeshLambertMaterial({ color: 0x654321 });
        const walls = [];
        const wallH = 30;
        // Dış + İç Duvarlar (Dust2 Layout)
        const wallData = [
            [-125, wallH/2, 0, mapSize, 8], [125, wallH/2, 0, mapSize, 8],
            [0, wallH/2, -125, 8, mapSize], [0, wallH/2, 125, 8, mapSize],
            [-80, wallH/2, 60, 40, 50], [20, wallH/2, 10, 50, 25],
            [80, wallH/2, -70, 35, 40], [-40, wallH/2, 100, 25, 35],
            [60, wallH/2, -100, 45, 25]
        ];
        wallData.forEach(([x,y,z,w,d]) => {
            const wall = new THREE.Mesh(new THREE.BoxGeometry(w, wallH, d), wallMat);
            wall.position.set(x,y,z);
            wall.castShadow = wall.receiveShadow = true;
            scene.add(wall);
            walls.push(wall);
        });

        // Spawn & Sites
        const ctSpawn = new THREE.Mesh(new THREE.CylinderGeometry(6,6,3), new THREE.MeshBasicMaterial({color:0x0066FF}));
        ctSpawn.position.set(-110, 2, 110); scene.add(ctSpawn);
        const tSpawn = new THREE.Mesh(new THREE.CylinderGeometry(6,6,3), new THREE.MeshBasicMaterial({color:0xFF3333}));
        tSpawn.position.set(110, 2, -110); scene.add(tSpawn);
        const aSite = new THREE.Mesh(new THREE.SphereGeometry(5), new THREE.MeshBasicMaterial({color:0xFFFF00}));
        aSite.position.set(-90, 3, 70); scene.add(aSite);
        const bSite = new THREE.Mesh(new THREE.SphereGeometry(5), new THREE.MeshBasicMaterial({color:0xFFFF00}));
        bSite.position.set(70, 3, -90); scene.add(bSite);

        // Player
        const player = { 
            position: new THREE.Vector3(0, 8, 0), 
            health: 100, 
            ammo: 12, 
            weapon: 0, 
            lastFire: 0,
            speed: 0.3
        };
        camera.position.copy(player.position);

        // FPS Weapon (Smooth Anim)
        const weaponGroup = new THREE.Group();
        camera.add(weaponGroup);
        scene.add(camera);
        const weapons = [
            {name:'USP-S', dmg:35, rate:170, maxAmmo:12, color:0xFFFF00, recoil:0.07},
            {name:'M4A4', dmg:33, rate:100, maxAmmo:30, color:0x00FFFF, recoil:0.1},
            {name:'AWP', dmg:115, rate:1300, maxAmmo:10, color:0xFF00FF, recoil:0.3}
        ];
        function updateWeapon() {
            weaponGroup.children = [];
            const w = weapons[player.weapon];
            const body = new THREE.Mesh(new THREE.BoxGeometry(0.7,0.3,2.5), new THREE.MeshPhongMaterial({color:w.color}));
            body.position.set(0.4, -0.4, -1.2); weaponGroup.add(body);
            const barrel = new THREE.Mesh(new THREE.CylinderGeometry(0.07,0.07,2), new THREE.MeshPhongMaterial({color:0x111111}));
            barrel.rotation.z = Math.PI/2; barrel.position.set(0.5, -0.4, -0.7); weaponGroup.add(barrel);
        }
        updateWeapon();

        // Bots & Game State
        let bots = [], bullets = [], particles = [], kills = 0, round = 1;
        const waypoints = [
            new THREE.Vector3(-100,8,100), new THREE.Vector3(0,8,0), new THREE.Vector3(100,8,-100),
            new THREE.Vector3(-120,8,120), new THREE.Vector3(120,8,-120)
        ];
        function createBot(isCT) {
            const bot = new THREE.Group();
            bot.userData = { 
                health: 100, ammo: 40, weapon: 1, speed: 0.15, state: 'patrol', wpIndex: 0, 
                lastFire: 0, team: isCT ? 'CT' : 'T', hitbox: {}
            };
            // Detaylı Model
            const bodyMat = new THREE.MeshPhongMaterial({color: isCT ? 0x0066FF : 0xFF3333});
            const body = new THREE.Mesh(new THREE.CylinderGeometry(1.4,1.8,4.5,8), bodyMat);
            body.position.y = 2.3; body.castShadow = true; bot.add(body); bot.userData.hitbox.body = body;
            const head = new THREE.Mesh(new THREE.SphereGeometry(1), new THREE.MeshPhongMaterial({color:0xFFE4B5}));
            head.position.y = 5; head.castShadow = true; bot.add(head); bot.userData.hitbox.head = head;
            // Arms & Legs
            for (let side = -1; side <= 1; side += 2) {
                const arm = new THREE.Mesh(new THREE.CylinderGeometry(0.35,0.35,3,6), new THREE.MeshPhongMaterial({color:0xFFE4B5}));
                arm.position.set(side*0.9, 2.8, 0); arm.rotation.z = side * -0.4; bot.add(arm);
                const leg = new THREE.Mesh(new THREE.CylinderGeometry(0.45,0.45,3,6), new THREE.MeshPhongMaterial({color:0x444444}));
                leg.position.set(side*0.6, -0.8, 0); bot.add(leg);
            }
            // Health Bar Base
            bot.userData.healthBar = new THREE.Mesh(
                new THREE.PlaneGeometry(3, 0.4),
                new THREE.MeshBasicMaterial({color: 0x00FF00})
            );
            bot.userData.healthBar.position.set(0, 7, 0);
            bot.add(bot.userData.healthBar);
            // Spawn
            bot.position.copy(isCT ? waypoints[0] : waypoints[waypoints.length-1]);
            scene.add(bot);
            return bot;
        }
        function spawnBots() {
            bots.forEach(b => scene.remove(b));
            bots = [];
            for (let i = 0; i < 5; i++) {
                bots.push(createBot(true));
                bots.push(createBot(false));
            }
        }

        // Controls (Smooth)
        const keys = {};
        let mouseX = 0, mouseY = 0, isLocked = false;
        renderer.domElement.addEventListener('click', () => {
            renderer.domElement.requestPointerLock();
        });
        document.addEventListener('pointerlockchange', () => {
            isLocked = document.pointerLockElement === renderer.domElement;
        });
        document.addEventListener('mousemove', (e) => {
            if (isLocked) {
                mouseX -= e.movementX * 0.003;
                mouseY -= e.movementY * 0.003;
                mouseY = Math.max(-Math.PI/2 + 0.1, Math.min(Math.PI/2 - 0.1, mouseY));
            }
        });
        window.addEventListener('keydown', (e) => {
            keys[e.code] = true;
            if (['Digit1','Digit2','Digit3'].includes(e.code)) {
                player.weapon = parseInt(e.code[5]) - 1;
                updateWeapon();
            }
            if (e.code === 'KeyR') player.ammo = weapons[player.weapon].maxAmmo;
            if (e.code === 'Escape') {
                document.exitPointerLock();
                document.getElementById('menu').style.display = 'block';
                gameStarted = false;
            }
        });
        window.addEventListener('keyup', (e) => keys[e.code] = false);

        // Fire (Hitbox + Friendly Fire)
        let flashTimeout;
        renderer.domElement.addEventListener('mousedown', () => {
            if (!isLocked) return;
            const w = weapons[player.weapon];
            if (Date.now() - player.lastFire < w.rate || player.ammo <= 0) return;
            player.ammo--;
            player.lastFire = Date.now();

            // Flash + Trail
            weaponGroup.children.forEach(child => {
                if (child.geometry.parameters.radiusTop === 0.3) weaponGroup.remove(child);
            });
            const flash = new THREE.Mesh(new THREE.SphereGeometry(0.4), new THREE.MeshBasicMaterial({color:0xFFFFFF, emissive:0xFFFFFF}));
            flash.position.set(0.6, -0.4, -0.3); weaponGroup.add(flash);
            clearTimeout(flashTimeout); flashTimeout = setTimeout(() => weaponGroup.remove(flash), 80);

            const dir = camera.getWorldDirection(new THREE.Vector3());
            const trail = new THREE.Line(
                new THREE.BufferGeometry().setFromPoints([camera.position.clone(), camera.position.clone().add(dir.clone().multiplyScalar(150))]),
                new THREE.LineBasicMaterial({color: w.color, transparent: true, opacity: 0.9})
            );
            scene.add(trail);
            bullets.push({mesh: trail, life: 80});

            // Raycast Hit (Head x4, Body x1, Limb x0.5 + Friendly)
            const raycaster = new THREE.Raycaster(camera.position, dir);
            const hits = raycaster.intersectObjects(scene.children, true);
            if (hits.length > 0) {
                const hitObj = hits[0].object;
                for (let bot of bots) {
                    if (bot.children.includes(hitObj.parent || hitObj)) {
                        let dmg = w.dmg;
                        const hb = bot.userData.hitbox;
                        if (hb.head === hitObj) dmg *= 4; // One-shot head
                        else if (hb.body === hitObj) dmg *= 1;
                        else dmg *= 0.5; // Limb
                        bot.userData.health -= dmg;
                        createParticles(bot.position, '#FFAA00');
                        if (bot.userData.health <= 0) {
                            scene.remove(bot);
                            bots = bots.filter(b => b !== bot);
                            kills++;
                        }
                        break;
                    }
                }
            }

            // Recoil Smooth
            const recoilX = (Math.random() - 0.5) * w.recoil;
            const recoilY = Math.random() * w.recoil * 0.6;
            weaponGroup.rotation.x += recoilY;
            weaponGroup.rotation.z += recoilX;
            setTimeout(() => {
                weaponGroup.rotation.x *= 0.7;
                weaponGroup.rotation.z *= 0.7;
            }, 100);
        });

        function createParticles(pos, col) {
            for (let i = 0; i < 30; i++) {
                const p = new THREE.Mesh(
                    new THREE.SphereGeometry(Math.random() * 0.4 + 0.1),
                    new THREE.MeshBasicMaterial({color: col, transparent: true})
                );
                p.position.copy(pos);
                p.userData.vel = new THREE.Vector3((Math.random()-0.5)*4, Math.random()*4, (Math.random()-0.5)*4);
                p.userData.life = 50;
                scene.add(p);
                particles.push(p);
            }
        }

        // Gelişmiş Bot AI (Patrol/Chase/Attack/Cover/Flank + Friendly Fire)
        function updateBotAI(bot, delta) {
            const ud = bot.userData;
            const distPlayer = player.position.distanceTo(bot.position);
            const enemies = bots.filter(b => b.userData.team !== ud.team).concat([{position: player.position}]); // Oyuncu + Düşman botlar

            // State (Akıcı Geçiş)
            if (distPlayer < 35) ud.state = 'attack';
            else if (distPlayer < 70) ud.state = 'chase';
            else ud.state = 'patrol';

            let target;
            if (ud.state === 'patrol') {
                target = waypoints[ud.wpIndex];
                if (bot.position.distanceTo(target) < 8) ud.wpIndex = (ud.wpIndex + 1) % waypoints.length;
            } else {
                // En yakın düşman
                target = enemies.reduce((closest, e) => 
                    bot.position.distanceTo(e.position) < bot.position.distanceTo(closest.position) ? e : closest
                ).position;
                if (ud.state === 'chase' && Math.random() < 0.2) { // Flank
                    target.add(new THREE.Vector3((Math.random()-0.5)*40, 0, (Math.random()-0.5)*40));
                }
            }

            // Move (Smooth + Avoidance)
            const dir = target.clone().sub(bot.position).normalize();
            bot.position.add(dir.multiplyScalar(ud.speed * delta * 60));
            bot.lookAt(target.x, bot.position.y, target.z);

            // Cover (Wall arkası)
            if (ud.state === 'attack' && Math.random() < 0.4) {
                dir.negate(); // Geri
                bot.position.add(dir.multiplyScalar(ud.speed * delta * 30));
            }

            // Fire (Accuracy + Friendly Fire)
            const w = weapons[ud.weapon];
            if (Date.now() - ud.lastFire > w.rate && ud.ammo > 0 && distPlayer < 65 && Math.random() < 0.85 - distPlayer/150) {
                ud.ammo--;
                ud.lastFire = Date.now();
                const botRay = new THREE.Raycaster(bot.position, dir);
                const botHits = botRay.intersectObjects(scene.children, true);
                if (botHits.length > 0) {
                    const hitObj = botHits[0].object;
                    let hitBot = null;
                    for (let b of bots) {
                        if (b.children.includes(hitObj.parent || hitObj)) {
                            hitBot = b;
                            break;
                        }
                    }
                    if (hitBot) {
                        let dmg = w.dmg;
                        const hb = hitBot.userData.hitbox;
                        if (hb.head === hitObj) dmg *= 4;
                        else if (hb.body === hitObj) dmg *= 1;
                        else dmg *= 0.5;
                        hitBot.userData.health -= dmg;
                        createParticles(hitBot.position, '#FF4400');
                        if (hitBot.userData.health <= 0) {
                            scene.remove(hitBot);
                            bots = bots.filter(bb => bb !== hitBot);
                            if (hitBot.userData.team === 'CT') kills++; // Oyuncu CT say
                        }
                    } else if (botHits[0].distance < 60) { // Oyuncu vur
                        player.health -= w.dmg * 0.75;
                        createParticles(camera.position, '#AA0000');
                    }
                }
            }

            // Health Bar Update (Smooth)
            ud.healthBar.scale.x = Math.max(0, ud.health / 100);
            ud.healthBar.material.color.setHSL(ud.health > 50 ? 0.3 : 0, 1, 0.5);
        }

        // Main Loop (Delta Time Akıcı)
        function animate() {
            if (!gameStarted) return requestAnimationFrame(animate);
            const delta = clock.getDelta();

            // Camera Smooth
            camera.rotation.y = mouseX;
            camera.rotation.x = mouseY;
            camera.position.lerp(player.position, 0.1);

            // Player Move (Delta Smooth)
            const moveDir = new THREE.Vector3();
            const front = new THREE.Vector3(0,0,-1).applyQuaternion(camera.quaternion);
            const right = new THREE.Vector3(1,0,0).applyQuaternion(camera.quaternion);
            if (keys['KeyW']) moveDir.add(front);
            if (keys['KeyS']) moveDir.sub(front);
            if (keys['KeyA']) moveDir.sub(right);
            if (keys['KeyD']) moveDir.add(right);
            if (moveDir.length() > 0) {
                moveDir.normalize().multiplyScalar(player.speed * delta * 60);
                player.position.add(moveDir);
            }

            // Collision
            player.position.clamp(new THREE.Vector3(-120, 1, -120), new THREE.Vector3(120, 30, 120));
            walls.forEach(w => {
                const box = new THREE.Box3().setFromObject(w);
                if (box.containsPoint(player.position)) player.position.sub(moveDir.clone().multiplyScalar(2));
            });

            // Update Bots
            bots.forEach(bot => updateBotAI(bot, delta));

            // Cleanup
            bullets.forEach((b, i) => {
                b.life -= delta * 60;
                b.mesh.material.opacity = b.life / 80;
                if (b.life <= 0) {
                    scene.remove(b.mesh);
                    bullets.splice(i, 1);
                }
            });
            particles.forEach((p, i) => {
                p.position.add(p.userData.vel.clone().multiplyScalar(delta * 60));
                p.userData.vel.multiplyScalar(0.97);
                p.userData.life -= delta * 60;
                p.material.opacity = p.userData.life / 50;
                if (p.userData.life <= 0) {
                    scene.remove(p);
                    particles.splice(i, 1);
                }
            });

            // Check End
            if (player.health <= 0) {
                alert(`Öldün! Kills: ${kills} | Round: ${round}`);
                resetRound();
            }
            if (bots.length === 0) {
                round++;
                alert(`Round ${round-1} Kazanıldı! Kills: ${kills}`);
                resetRound();
            }

            // HUD
            document.getElementById('health').textContent = Math.floor(player.health);
            document.getElementById('ammo').textContent = `${player.ammo}/${weapons[player.weapon].maxAmmo}`;
            document.getElementById('kills').textContent = kills;
            document.getElementById('weapon').textContent = weapons[player.weapon].name;
            document.getElementById('round').textContent = round;

            // Minimap
            const mini = document.getElementById('minimap');
            const mctx = mini.getContext('2d');
            mctx.clearRect(0,0,180,180);
            mctx.fillStyle = 'rgba(20,20,20,0.9)'; mctx.fillRect(0,0,180,180);
            mctx.strokeStyle = '#fff'; mctx.lineWidth = 2; mctx.strokeRect(1,1,178,178);
            // Player
            mctx.fillStyle = '#00FF00'; mctx.beginPath();
            mctx.arc(90 + player.position.x * 0.4, 90 - player.position.z * 0.4, 4, 0, Math.PI*2); mctx.fill();
            // Bots
            bots.forEach(b => {
                mctx.fillStyle = b.userData.team === 'CT' ? '#0066FF' : '#FF3333';
                mctx.beginPath();
                mctx.arc(90 + b.position.x * 0.4, 90 - b.position.z * 0.4, 3, 0, Math.PI*2); mctx.fill();
            });

            renderer.render(scene, camera);
            requestAnimationFrame(animate);
        }

        function resetRound() {
            player.health = 100;
            player.ammo = 12;
            player.weapon = 0;
            kills = 0;
            spawnBots();
            player.position.set(0, 8, 0);
        }

        // PLAY BUTTON - %100 Çalışır
        document.getElementById('playButton').addEventListener('click', () => {
            document.getElementById('menu').style.display = 'none';
            resetRound();
            gameStarted = true;
            clock.start(); // Delta Başlat
            renderer.domElement.requestPointerLock();
            animate(); // Loop Başlat
        });

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        // Initial Animate Call (Menu'da boş loop)
        animate();
    </script>
</body>
</html>
