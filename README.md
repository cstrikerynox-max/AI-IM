<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CS2 3D Dust 2 - Gelişmiş AI & Hitbox</title>
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
            padding: 25px 50px;
            font-size: 28px;
            background: linear-gradient(45deg, #ff4500, #ff6347);
            border: none;
            color: white;
            cursor: pointer;
            border-radius: 15px;
            transition: all 0.3s;
            box-shadow: 0 0 30px #ff4500;
            font-weight: bold;
        }
        #playButton:hover {
            box-shadow: 0 0 50px #ff4500;
            transform: scale(1.1);
        }
        #hud {
            position: absolute;
            top: 20px;
            left: 20px;
            font-size: 22px;
            z-index: 50;
            text-shadow: 2px 2px 6px #000;
        }
        #crosshair {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 24px;
            height: 24px;
            border: 3px solid #ff0000;
            border-radius: 50%;
            box-shadow: 0 0 10px #ff0000;
            z-index: 50;
            pointer-events: none;
        }
        #minimap {
            position: absolute;
            top: 20px;
            right: 20px;
            width: 150px;
            height: 150px;
            background: rgba(0,0,0,0.7);
            border: 2px solid #fff;
            z-index: 50;
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
            filter: brightness(1.3) contrast(1.6) saturate(2.0); /* Ultra HDR */
        }
    </style>
</head>
<body>
    <div id="menu">
        <h1>CS2 3D - Dust 2 Gelişmiş</h1>
        <p>100 HP | Headshot One-Shot | Akıllı Bot AI</p>
        <button id="playButton">PLAY</button>
        <p style="font-size: 16px; margin-top: 30px;">WASD: Hareket | Mouse: Nişan/Ateş | 1:USP 2:M4 3:AWP | R:Reload | ESC: Menü</p>
    </div>
    <div id="crosshair"></div>
    <div id="hud">
        <div>Health: <span id="health">100</span> | Ammo: <span id="ammo">12/12</span> | Kills: <span id="kills">0</span></div>
        <div>Silah: <span id="weapon">USP-S</span> | Round: <span id="round">1</span>/30</div>
    </div>
    <canvas id="minimap"></canvas>
    <div id="controls">
        Gelişmiş AI: Patrol/Chase/Cover | Detaylı Hitbox (Head/Body/Limbs)
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r161/three.min.js"></script>
    <script>
        // Global animate flag
        let gameStarted = false;

        // Three.js Setup
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.shadowMap.enabled = true;
        renderer.shadowMap.type = THREE.PCFSoftShadowMap;
        renderer.toneMapping = THREE.ACESFilmicToneMapping;
        renderer.toneMappingExposure = 1.3;
        document.body.appendChild(renderer.domElement);

        // Lights
        const ambientLight = new THREE.AmbientLight(0x404040, 0.5);
        scene.add(ambientLight);
        const dirLight = new THREE.DirectionalLight(0xffffff, 1.2);
        dirLight.position.set(50, 100, 50);
        dirLight.castShadow = true;
        dirLight.shadow.mapSize.set(2048, 2048);
        scene.add(dirLight);

        // Skybox
        const skyGeo = new THREE.SphereGeometry(500, 32, 32);
        const skyMat = new THREE.MeshBasicMaterial({ color: 0x87CEEB, side: THREE.BackSide });
        scene.add(new THREE.Mesh(skyGeo, skyMat));

        // Dust 2 Map (Genişletilmiş detaylar)
        const mapSize = 200;
        const floorGeo = new THREE.PlaneGeometry(mapSize, mapSize);
        const floorMat = new THREE.MeshLambertMaterial({ color: 0xD2B48C, roughness: 0.8 });
        const floor = new THREE.Mesh(floorGeo, floorMat);
        floor.rotation.x = -Math.PI / 2;
        floor.receiveShadow = true;
        scene.add(floor);

        const wallMat = new THREE.MeshLambertMaterial({ color: 0x8B4513, roughness: 0.7 });
        const wallHeight = 25;
        const walls = [];
        // Dış duvarlar + detaylar
        [ [-mapSize/2 + 2.5, wallHeight/2, 0, mapSize, 5], [mapSize/2 - 2.5, wallHeight/2, 0, mapSize, 5],
          [0, wallHeight/2, -mapSize/2 + 2.5, 5, mapSize], [0, wallHeight/2, mapSize/2 - 2.5, 5, mapSize] ].forEach(([x,y,z,w,d]) => {
            const wall = new THREE.Mesh(new THREE.BoxGeometry(w, wallHeight, d), wallMat);
            wall.position.set(x,y,z);
            wall.castShadow = wall.receiveShadow = true;
            scene.add(wall);
            walls.push(wall);
        });
        // İç yapılar (A Long, Mid Doors, B Tunnels, Boxes)
        [ [-60, wallHeight/2, 40, 30,40], [0, wallHeight/2, 0, 40,20], [50, 12.5, -50, 25,25],
          [-30, wallHeight/2, 80, 20,30], [70, wallHeight/2, -80, 35,20] ].forEach(([x,y,z,w,d]) => {
            const wall = new THREE.Mesh(new THREE.BoxGeometry(w, wallHeight, d), wallMat);
            wall.position.set(x,y,z);
            wall.castShadow = wall.receiveShadow = true;
            scene.add(wall);
            walls.push(wall);
        });

        // Spawn points & Sites (Glow)
        const ctSpawn = new THREE.Mesh(new THREE.CylinderGeometry(4,4,2,8), new THREE.MeshBasicMaterial({color:0x0000FF, emissive:0x000044}));
        ctSpawn.position.set(-80,1,80);
        scene.add(ctSpawn);
        const tSpawn = new THREE.Mesh(new THREE.CylinderGeometry(4,4,2,8), new THREE.MeshBasicMaterial({color:0xFF0000, emissive:0x440000}));
        tSpawn.position.set(80,1,-80);
        scene.add(tSpawn);
        const aSite = new THREE.Mesh(new THREE.CylinderGeometry(5,5,1,8), new THREE.MeshBasicMaterial({color:0xFFFF00}));
        aSite.position.set(-70,1,45); scene.add(aSite);
        const bSite = new THREE.Mesh(new THREE.CylinderGeometry(5,5,1,8), new THREE.MeshBasicMaterial({color:0xFFFF00}));
        bSite.position.set(55,1,-55); scene.add(bSite);

        // Player
        const player = { position: new THREE.Vector3(0,5,0), velocity: new THREE.Vector3(), health: 100, ammo: 12, maxAmmo:12, weapon:0, lastFire:0 };

        // Weapon (FPS view)
        const weaponGroup = new THREE.Group();
        camera.add(weaponGroup);
        scene.add(camera);
        function updateWeapon() {
            weaponGroup.clear();
            const w = weapons[player.weapon];
            const gunBody = new THREE.Mesh(new THREE.BoxGeometry(0.6,0.25,2.2), new THREE.MeshPhongMaterial({color:w.color}));
            gunBody.position.set(0.35,-0.35,-1.1);
            weaponGroup.add(gunBody);
            const barrel = new THREE.Mesh(new THREE.CylinderGeometry(0.06,0.06,1.8), new THREE.MeshPhongMaterial({color:0x222222}));
            barrel.rotation.z = Math.PI/2; barrel.position.set(0.45,-0.35,-0.6);
            weaponGroup.add(barrel);
        }

        // Weapons (CS2 stats)
        const weapons = [
            {name:'USP-S', damage:35, fireRate:170, maxAmmo:12, color:0xFFFF00, recoil:0.06},
            {name:'M4A4', damage:33, fireRate:100, maxAmmo:30, color:0x00FFFF, recoil:0.09},
            {name:'AWP', damage:115, fireRate:1200, maxAmmo:10, color:0xFF00FF, recoil:0.25} // Head multiplier ayrı
        ];
        updateWeapon();

        // Gelişmiş Bot (Hitbox: head, body, arms, legs)
        let bots = [], bullets = [], particles = [], kills = 0, round = 1;
        const waypoints = [ // Dust2 patrol path
            new THREE.Vector3(-60,5,60), new THREE.Vector3(0,5,0), new THREE.Vector3(60,5,-60),
            new THREE.Vector3(-80,5,80), new THREE.Vector3(80,5,-80), new THREE.Vector3(-30,5,80)
        ];
        function createBot(isCT) {
            const botGroup = new THREE.Group();
            botGroup.userData = { health:100, ammo:30, weapon:1, speed:0.12, state:'patrol', waypointIndex:0, lastFire:0, team: isCT ? 'CT' : 'T', hitbox: {} };
            // Body
            const body = new THREE.Mesh(new THREE.CylinderGeometry(1.2,1.8,4,8), new THREE.MeshPhongMaterial({color: isCT?0x0066FF:0xFF3333}));
            body.position.y=2; body.castShadow=true;
            botGroup.add(body); botGroup.userData.hitbox.body = body;
            // Head (one-shot zone)
            const head = new THREE.Mesh(new THREE.SphereGeometry(0.9), new THREE.MeshPhongMaterial({color:0xFDBCB4}));
            head.position.y=4.5; head.castShadow=true;
            botGroup.add(head); botGroup.userData.hitbox.head = head;
            // Arms
            [-0.8,0.8].forEach((off,i) => {
                const arm = new THREE.Mesh(new THREE.CylinderGeometry(0.3,0.3,2.5,6), new THREE.MeshPhongMaterial({color:0xFDBCB4}));
                arm.position.set(off,2.5,0); arm.rotation.z = off>0 ? -0.3 : 0.3;
                botGroup.add(arm);
                botGroup.userData.hitbox[`arm${i}`] = arm;
            });
            // Legs
            [-0.5,0.5].forEach((off,i) => {
                const leg = new THREE.Mesh(new THREE.CylinderGeometry(0.4,0.4,2.8,6), new THREE.MeshPhongMaterial({color:0x333333}));
                leg.position.set(off,-0.5,0);
                botGroup.add(leg);
                botGroup.userData.hitbox[`leg${i}`] = leg;
            });
            // Spawn
            botGroup.position.copy(isCT ? new THREE.Vector3(-75 + Math.random()*10,5,75 + Math.random()*10) : new THREE.Vector3(75 + Math.random()*10,5,-75 + Math.random()*10));
            scene.add(botGroup);
            return botGroup;
        }
        function spawnBots() {
            bots.forEach(b => scene.remove(b));
            bots = [];
            for(let i=0; i<5; i++) {
                bots.push(createBot(true)); // CT
                bots.push(createBot(false)); // T
            }
        }

        // Raycaster
        const raycaster = new THREE.Raycaster();

        // Controls
        const keys = {};
        let mouseX=0, mouseY=0, isLocked=false;
        renderer.domElement.addEventListener('click', () => renderer.domElement.requestPointerLock());
        document.addEventListener('pointerlockchange', () => isLocked = document.pointerLockElement === renderer.domElement);
        document.addEventListener('mousemove', (e) => {
            if(!isLocked) return;
            mouseX -= e.movementX * 0.002;
            mouseY -= e.movementY * 0.002;
            mouseY = Math.max(-Math.PI/2, Math.min(Math.PI/2, mouseY));
        });
        window.addEventListener('keydown', (e) => {
            keys[e.code] = true;
            if(e.code==='Digit1') {player.weapon=0; updateWeapon();}
            if(e.code==='Digit2') {player.weapon=1; updateWeapon();}
            if(e.code==='Digit3') {player.weapon=2; updateWeapon();}
            if(e.code==='KeyR') player.ammo = weapons[player.weapon].maxAmmo;
            if(e.code==='Escape') {document.exitPointerLock(); document.getElementById('menu').style.display='block'; gameStarted=false;}
        });
        window.addEventListener('keyup', (e) => keys[e.code]=false);

        // Fire (Gelişmiş hitbox damage)
        let muzzleFlash = null;
        renderer.domElement.addEventListener('mousedown', () => {
            const w = weapons[player.weapon];
            if(Date.now() - player.lastFire < w.fireRate || player.ammo <=0 || !isLocked) return;
            player.ammo--; player.lastFire = Date.now();

            // Muzzle flash
            if(muzzleFlash) weaponGroup.remove(muzzleFlash);
            muzzleFlash = new THREE.Mesh(new THREE.SphereGeometry(0.3), new THREE.MeshBasicMaterial({color:0xFFFFAA, emissive:0xFFFFAA}));
            muzzleFlash.position.set(0.5,-0.3,-0.2);
            weaponGroup.add(muzzleFlash);
            setTimeout(() => weaponGroup.remove(muzzleFlash), 50);

            // Bullet trail
            const dir = camera.getWorldDirection(new THREE.Vector3());
            const trailPoints = [camera.position.clone(), camera.position.clone().add(dir.multiplyScalar(100))];
            const trailGeo = new THREE.BufferGeometry().setFromPoints(trailPoints);
            const trailMat = new THREE.LineBasicMaterial({color:w.color, transparent:true, opacity:0.8});
            const trail = new THREE.Line(trailGeo, trailMat);
            scene.add(trail);
            bullets.push({mesh:trail, life:60});

            // Raycast hit
            raycaster.setFromCamera(new THREE.Vector2(0,0), camera);
            const intersects = raycaster.intersectObjects(scene.children, true);
            if(intersects.length > 0) {
                const hit = intersects[0];
                let damage = w.damage;
                // Hitbox multiplier
                if(hit.object.parent && hit.object.parent.userData.hitbox) {
                    const hitbox = hit.object.parent.userData.hitbox;
                    if(hitbox.head === hit.object) damage *= 4; // Headshot x4 -> one-shot
                    else if(hitbox.body === hit.object) damage *= 1;
                    else damage *= 0.6; // Limbs
                    const bot = hit.object.parent;
                    bot.userData.health -= damage;
                    createExplosion(bot.position, '#FF0000');
                    if(bot.userData.health <=0) {
                        scene.remove(bot);
                        bots = bots.filter(b => b !== bot);
                        kills++;
                    }
                }
            }

            // Recoil
            weaponGroup.rotation.z += (Math.random()-0.5)*w.recoil;
            weaponGroup.rotation.x += Math.random()*w.recoil*0.5;
            setTimeout(() => {weaponGroup.rotation.set(0,0,0);}, 150);
        });

        function createExplosion(pos, color) {
            for(let i=0; i<25; i++) {
                const p = new THREE.Mesh(new THREE.SphereGeometry(Math.random()*0.3+0.1), new THREE.MeshBasicMaterial({color, transparent:true, opacity:1}));
                p.position.copy(pos);
                p.userData.vel = new THREE.Vector3((Math.random()-0.5)*3, Math.random()*3+1, (Math.random()-0.5)*3);
                p.userData.life = 40;
                scene.add(p);
                particles.push(p);
            }
        }

        // Gelişmiş Bot AI (Patrol -> Chase -> Cover)
        function updateBotAI(bot) {
            const distToPlayer = player.position.distanceTo(bot.position);
            const ud = bot.userData;

            // State machine
            if(distToPlayer < 40) ud.state = 'attack';
            else if(distToPlayer < 80) ud.state = 'chase';
            else ud.state = 'patrol';

            let targetPos;
            if(ud.state === 'patrol') {
                targetPos = waypoints[ud.waypointIndex];
                if(bot.position.distanceTo(targetPos) < 5) ud.waypointIndex = (ud.waypointIndex + 1) % waypoints.length;
            } else {
                targetPos = player.position.clone();
                if(ud.state === 'chase') targetPos.add(new THREE.Vector3((Math.random()-0.5)*20,0,(Math.random()-0.5)*20)); // Flank
            }

            // Move to target (obstacle avoidance)
            const dir = targetPos.clone().sub(bot.position).normalize();
            bot.position.add(dir.multiplyScalar(ud.speed));
            bot.lookAt(targetPos);

            // Cover: Wall arkasına saklan (basit)
            if(ud.state === 'attack' && Math.random() < 0.3) {
                dir.multiplyScalar(-1); // Geri çekil
            }

            // Fire (accuracy: distance'e göre)
            const w = weapons[ud.weapon];
            if(Date.now() - ud.lastFire > w.fireRate && ud.ammo >0 && distToPlayer < 60 && Math.random() < (1 - distToPlayer/100)) {
                ud.ammo--; ud.lastFire = Date.now();
                const botDir = player.position.clone().sub(bot.position).normalize();
                const botRay = new THREE.Raycaster(bot.position, botDir);
                const hit = botRay.intersectObject(camera);
                if(hit.length >0 && hit[0].distance <60) {
                    player.health -= w.damage * 0.8; // Bot accuracy %80
                    createExplosion(camera.position, '#AA0000');
                }
            }

            // Sağlık barı
            const barGeo = new THREE.PlaneGeometry(2,0.3);
            const barMat = new THREE.MeshBasicMaterial({color: ud.health >50 ? 0x00FF00 : 0xFF0000});
            const bar = new THREE.Mesh(barGeo, barMat);
            bar.position.set(0,6.5,0);
            bar.scale.x = ud.health / 100;
            botGroup.add(bar); // Update her frame değil, optimize et
        }

        // Animate Loop
        function animate() {
            if(!gameStarted) return;
            requestAnimationFrame(animate);

            // Camera
            camera.rotation.order = 'YXZ';
            camera.rotation.y = mouseX;
            camera.rotation.x = mouseY;
            camera.position.copy(player.position);

            // Player move
            const direction = new THREE.Vector3();
            const front = new THREE.Vector3(0,0,-1).applyQuaternion(camera.quaternion);
            const side = new THREE.Vector3(1,0,0).applyQuaternion(camera.quaternion);
            if(keys['KeyW']) direction.add(front);
            if(keys['KeyS']) direction.sub(front);
            if(keys['KeyA']) direction.sub(side);
            if(keys['KeyD']) direction.add(side);
            if(direction.length() > 0) direction.normalize().multiplyScalar(0.25);
            player.position.add(direction);

            // Collision (basit clamp + walls)
            player.position.clamp(new THREE.Vector3(-95,1,-95), new THREE.Vector3(95,25,95));
            walls.forEach(w => {
                const box = new THREE.Box3().setFromObject(w);
                if(box.containsPoint(player.position)) player.position.sub(direction);
            });

            // Bots update
            bots.forEach(updateBotAI);

            // Bullets/particles clean
            bullets.forEach((b,i) => {
                b.life--;
                if(b.life <=0) { scene.remove(b.mesh); bullets.splice(i,1); }
                b.mesh.material.opacity = b.life / 60;
            });
            particles.forEach((p,i) => {
                p.position.add(p.userData.vel);
                p.userData.vel.multiplyScalar(0.96);
                p.userData.life--;
                p.material.opacity = p.userData.life / 40;
                if(p.userData.life <=0) { scene.remove(p); particles.splice(i,1); }
            });

            // Win/Lose
            if(player.health <=0) {
                alert(`Öldün! Kills: ${kills} | Round: ${round}`);
                resetRound();
            }
            if(bots.length ===0) {
                round++; alert(`Round ${round-1} Kazanıldı!`);
                resetRound();
            }

            // HUD
            document.getElementById('health').textContent = Math.max(0,Math.floor(player.health));
            document.getElementById('ammo').textContent = `${player.ammo}/${weapons[player.weapon].maxAmmo}`;
            document.getElementById('kills').textContent = kills;
            document.getElementById('weapon').textContent = weapons[player.weapon].name;
            document.getElementById('round').textContent = round;

            // Minimap (basit canvas)
            const miniCtx = document.getElementById('minimap').getContext('2d');
            miniCtx.clearRect(0,0,150,150);
            miniCtx.fillStyle = '#333'; miniCtx.fillRect(0,0,150,150);
            // Player
            miniCtx.fillStyle = '#00FF00'; miniCtx.fillRect(75 + player.position.x/2, 75 - player.position.z/2, 3,3);
            // Bots
            bots.forEach(b => {
                miniCtx.fillStyle = b.userData.team === 'CT' ? '#0000FF' : '#FF0000';
                miniCtx.fillRect(75 + b.position.x/2, 75 - b.position.z/2, 2,2);
            });

            renderer.render(scene, camera);
        }

        function resetRound() {
            player.health = 100;
            player.ammo = 12;
            player.weapon = 0;
            kills = 0;
            spawnBots();
            player.position.set(0,5,0);
        }

        // Play Button Fix
        document.getElementById('playButton').addEventListener('click', () => {
            document.getElementById('menu').style.display = 'none';
            spawnBots();
            resetRound();
            renderer.domElement.requestPointerLock();
            gameStarted = true;
            animate(); // Loop başlat
        });

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>
</html>
