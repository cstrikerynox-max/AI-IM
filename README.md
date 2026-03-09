<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CS2 Oyun Sitesi - Dust 2</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: #000;
            color: #fff;
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            overflow: hidden;
        }
        #menu {
            text-align: center;
            z-index: 10;
        }
        #playButton {
            padding: 20px 40px;
            font-size: 24px;
            background-color: #ff4500;
            border: none;
            color: white;
            cursor: pointer;
            border-radius: 5px;
            transition: background-color 0.3s;
        }
        #playButton:hover {
            background-color: #cc3700;
        }
        #gameCanvas {
            display: none;
            width: 100vw;
            height: 100vh;
            background-color: #111;
        }
        /* Ultra HDR simülasyonu için filtre (gerçek HDR web'de sınırlı, filtre ile taklit) */
        canvas {
            filter: brightness(1.2) contrast(1.5) saturate(1.5);
        }
    </style>
</head>
<body>
    <div id="menu">
        <h1>CS2 - Dust 2 Haritası</h1>
        <button id="playButton">Play (5v5 Botlarla)</button>
    </div>
    <canvas id="gameCanvas"></canvas>

    <script>
        // Three.js kütüphanesini inline dahil et (GitHub Pages için external kullanabilirsin, ama tek parça için basit tutuyorum)
        // Not: Gerçek Three.js çok büyük, burada basit canvas 2D ile simüle ediyorum. Tam 3D için external script ekle.
        // Bu, basit bir 2D top-down Dust 2 simülasyonu: Oyuncu hareket, botlar rastgele hareket.

        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const playButton = document.getElementById('playButton');
        const menu = document.getElementById('menu');

        // Oyun değişkenleri
        let player = { x: 400, y: 300, size: 20, color: '#00ff00', speed: 5 }; // Oyuncu (yeşil)
        let bots = []; // 5 CT + 5 T botlar
        let keys = {}; // Klavye kontrolleri
        let gameRunning = false;

        // Dust 2 haritası basit çizim (2D top-down, duvarlar vs.)
        const mapWalls = [
            // Örnek Dust 2 duvarları (koordinatlar basitçe tanımlı)
            { x: 0, y: 0, width: 800, height: 10 }, // Üst sınır
            { x: 0, y: 590, width: 800, height: 10 }, // Alt sınır
            { x: 0, y: 0, width: 10, height: 600 }, // Sol sınır
            { x: 790, y: 0, width: 10, height: 600 }, // Sağ sınır
            // Dust 2'ye benzer iç duvarlar (A site, B site simülasyonu)
            { x: 100, y: 100, width: 200, height: 10 },
            { x: 500, y: 200, width: 10, height: 200 },
            { x: 300, y: 400, width: 200, height: 10 },
            // Bomba siteleri
            { x: 150, y: 150, width: 50, height: 50, color: '#ffff00', label: 'A Site' },
            { x: 600, y: 400, width: 50, height: 50, color: '#ffff00', label: 'B Site' }
        ];

        // Bot oluştur
        function createBots() {
            bots = [];
            for (let i = 0; i < 5; i++) {
                // CT botlar (mavi)
                bots.push({ x: Math.random() * 800, y: Math.random() * 600, size: 15, color: '#0000ff', dx: (Math.random() - 0.5) * 2, dy: (Math.random() - 0.5) * 2 });
            }
            for (let i = 0; i < 5; i++) {
                // T botlar (kırmızı)
                bots.push({ x: Math.random() * 800, y: Math.random() * 600, size: 15, color: '#ff0000', dx: (Math.random() - 0.5) * 2, dy: (Math.random() - 0.5) * 2 });
            }
        }

        // Çarpışma kontrolü
        function checkCollision(obj, walls) {
            for (let wall of walls) {
                if (obj.x < wall.x + wall.width && obj.x + obj.size > wall.x &&
                    obj.y < wall.y + wall.height && obj.y + obj.size > wall.y) {
                    return true;
                }
            }
            return false;
        }

        // Oyun döngüsü
        function gameLoop() {
            if (!gameRunning) return;

            // Temizle
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Haritayı çiz
            ctx.fillStyle = '#333'; // Zemin
            ctx.fillRect(0, 0, 800, 600);
            for (let wall of mapWalls) {
                ctx.fillStyle = wall.color || '#808080';
                ctx.fillRect(wall.x, wall.y, wall.width, wall.height);
                if (wall.label) {
                    ctx.fillStyle = '#000';
                    ctx.fillText(wall.label, wall.x + 10, wall.y + 30);
                }
            }

            // Oyuncuyu çiz ve hareket ettir
            ctx.fillStyle = player.color;
            ctx.fillRect(player.x, player.y, player.size, player.size);

            if (keys['ArrowUp'] || keys['w']) player.y -= player.speed;
            if (keys['ArrowDown'] || keys['s']) player.y += player.speed;
            if (keys['ArrowLeft'] || keys['a']) player.x -= player.speed;
            if (keys['ArrowRight'] || keys['d']) player.x += player.speed;

            // Sınır ve duvar çarpışma
            if (player.x < 0) player.x = 0;
            if (player.x > 780) player.x = 780;
            if (player.y < 0) player.y = 0;
            if (player.y > 580) player.y = 580;
            if (checkCollision(player, mapWalls.filter(w => !w.label))) {
                // Geri al hareket (basit çarpışma)
                if (keys['ArrowUp'] || keys['w']) player.y += player.speed;
                if (keys['ArrowDown'] || keys['s']) player.y -= player.speed;
                if (keys['ArrowLeft'] || keys['a']) player.x += player.speed;
                if (keys['ArrowRight'] || keys['d']) player.x -= player.speed;
            }

            // Botları çiz ve hareket ettir
            bots.forEach(bot => {
                bot.x += bot.dx;
                bot.y += bot.dy;

                // Sınır çarpışma
                if (bot.x < 0 || bot.x > 780) bot.dx *= -1;
                if (bot.y < 0 || bot.y > 580) bot.dy *= -1;

                // Duvar çarpışma
                if (checkCollision(bot, mapWalls.filter(w => !w.label))) {
                    bot.dx *= -1;
                    bot.dy *= -1;
                }

                ctx.fillStyle = bot.color;
                ctx.fillRect(bot.x, bot.y, bot.size, bot.size);
            });

            // Bot-oyuncu çarpışma (basit etkileşim, örneğin "ateş" simüle)
            bots.forEach((bot, index) => {
                if (Math.abs(bot.x - player.x) < 20 && Math.abs(bot.y - player.y) < 20) {
                    // Örnek: Botu kaldır (öldür)
                    bots.splice(index, 1);
                }
            });

            requestAnimationFrame(gameLoop);
        }

        // Olay dinleyicileri
        playButton.addEventListener('click', () => {
            menu.style.display = 'none';
            canvas.style.display = 'block';
            canvas.width = 800;
            canvas.height = 600;
            createBots();
            gameRunning = true;
            gameLoop();
        });

        window.addEventListener('keydown', (e) => { keys[e.key] = true; });
        window.addEventListener('keyup', (e) => { keys[e.key] = false; });

        // Fare ile ateş simüle (basit)
        canvas.addEventListener('click', (e) => {
            const rect = canvas.getBoundingClientRect();
            const clickX = e.clientX - rect.left;
            const clickY = e.clientY - rect.top;
            // Örnek: Yakın botu öldür
            bots = bots.filter(bot => Math.hypot(bot.x - clickX, bot.y - clickY) > 50);
        });
    </script>
</body>
</html>
