<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>18+ NSFW Site</title>
    <link rel="stylesheet" href="style.css">
    <script>
        // Yaş doğrulaması (basit popup)
        window.onload = function() {
            if (!confirm("Bu site 18+ yetişkin içeriği içerir. 18 yaşından büyük mısınız?")) {
                window.location.href = "https://www.google.com"; // Reddetirse yönlendir
            }
        };
    </script>
</head>
<body>
    <header>
        <h1>Hoş Geldiniz - 18+ NSFW Site</h1>
        <nav>
            <a href="#galeri">Galeri</a>
            <a href="#resim-olustur">Resim Oluştur</a>
        </nav>
    </header>

    <section id="galeri">
        <h2>Resim Galerisi</h2>
        <p>Buraya NSFW resimler ekleyebilirsiniz (yerel dosyalar veya URL'ler).</p>
        <img src="ornek-resim1.jpg" alt="NSFW Örnek 1" class="galeri-resim">
        <img src="ornek-resim2.jpg" alt="NSFW Örnek 2" class="galeri-resim">
        <!-- Gerçek sitede daha fazla ekleyin, ama yasal içerik kullanın -->
    </section>

    <section id="resim-olustur">
        <h2>Resim Oluştur</h2>
        <p>Metin girin, basit bir resim oluşturun (canvas ile).</p>
        <input type="text" id="metin" placeholder="Metin girin (örneğin: NSFW Metin)">
        <button onclick="resimOlustur()">Oluştur</button>
        <canvas id="canvas" width="400" height="300"></canvas>
    </section>

    <footer>
        <p>&copy; 2026 - Tüm hakları saklıdır. 18+ Sadece.</p>
    </footer>

    <script src="script.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    background-color: #f0f0f0;
    color: #333;
    margin: 0;
    padding: 0;
}

header {
    background-color: #ff6347; /* Kırmızımsı, NSFW temalı */
    color: white;
    text-align: center;
    padding: 20px;
}

nav a {
    color: white;
    margin: 0 15px;
    text-decoration: none;
}

section {
    padding: 20px;
    margin: 20px;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0,0,0,0.1);
}

.galeri-resim {
    width: 200px;
    height: auto;
    margin: 10px;
    border: 1px solid #ccc;
}

#canvas {
    border: 1px solid #000;
    display: block;
    margin-top: 20px;
}

footer {
    text-align: center;
    padding: 10px;
    background-color: #333;
    color: white;
}
function resimOlustur() {
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    const metin = document.getElementById('metin').value || 'Varsayılan NSFW Metin';

    // Arka planı temizle ve doldur
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = '#ff69b4'; // Pembe, NSFW temalı renk
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Metni ekle
    ctx.fillStyle = '#fff';
    ctx.font = '30px Arial';
    ctx.textAlign = 'center';
    ctx.fillText(metin, canvas.width / 2, canvas.height / 2);

    // İndirme linki ekle (opsiyonel)
    const indirmeLink = document.createElement('a');
    indirmeLink.href = canvas.toDataURL('image/png');
    indirmeLink.download = 'olusturulan-resim.png';
    indirmeLink.textContent = 'İndir';
    canvas.parentNode.appendChild(indirmeLink);
}
