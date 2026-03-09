<!DOCTYPE html>
<html lang="az">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Süni İntellekt Yaradıcısı - AZ Studio</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root {
            --purple-main: #330055;
            --purple-light: #5a0099;
            --purple-button: #7b00cc;
            --text-color: #f1f1f1;
            --transition: all 0.3s ease-out;
        }

        body {
            margin: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            color: var(--text-color);
            background-color: var(--purple-main);
            background-image: linear-gradient(rgba(51, 0, 85, 0.8), rgba(51, 0, 85, 0.9)),
                              url('https://cdn.pixabay.com/photo/2023/12/11/14/08/anime-8443585_1280.png');
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
            height: 100vh;
            display: flex;
            overflow: hidden;
        }

        /* Sidebar - Sol tərəf */
        .sidebar {
            width: 80px;
            background: rgba(0,0,0,0.7);
            display: flex;
            flex-direction: column;
            align-items: center;
            padding-top: 30px;
            border-right: 2px solid var(--purple-button);
            box-shadow: 2px 0 15px rgba(0,0,0,0.5);
            transition: var(--transition);
        }

        .sidebar-item {
            font-size: 28px;
            color: #bdc3c7;
            margin-bottom: 30px;
            cursor: pointer;
            transition: var(--transition);
            width: 100%;
            text-align: center;
            padding: 10px 0;
        }

        .sidebar-item:hover {
            color: white;
            background: rgba(123, 0, 204, 0.3);
            border-radius: 5px;
        }

        /* Main Content - Sağ tərəf */
        .main-content {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
        }

        /* Qeydiyyat düymələri */
        .auth-bar {
            display: flex;
            justify-content: flex-end;
            gap: 15px;
            margin-bottom: 40px;
        }

        .btn {
            padding: 10px 25px;
            border-radius: 20px;
            border: none;
            cursor: pointer;
            font-weight: bold;
            font-size: 14px;
            transition: var(--transition);
        }

        .btn-login {
            background: transparent;
            color: white;
            border: 2px solid white;
        }

        .btn-login:hover { background: rgba(255,255,255,0.1); }

        .btn-signup {
            background: var(--purple-button);
            color: white;
            box-shadow: 0 4px 10px rgba(123, 0, 204, 0.4);
        }

        .btn-signup:hover { background: #9120e2; }

        /* Başlıq */
        .page-header {
            text-align: center;
            font-size: 28px;
            margin-bottom: 30px;
            text-transform: uppercase;
            letter-spacing: 2px;
            color: white;
            text-shadow: 0 0 10px rgba(255,255,255,0.5);
        }

        /* Seçim Kateqoriyaları Paneli */
        .options-panel {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-bottom: 40px;
            animation: fadeIn 0.8s ease-out;
        }

        .option-button {
            flex: 1;
            max-width: 200px;
            padding: 15px;
            border-radius: 10px;
            background-color: var(--purple-button);
            border: 2px solid transparent;
            color: white;
            font-size: 18px;
            text-align: center;
            cursor: pointer;
            box-shadow: 0 6px 15px rgba(0,0,0,0.3);
            transition: var(--transition);
        }

        .option-button:hover {
            transform: translateY(-5px);
            background-color: #9120e2;
        }

        .option-button.active {
            background-color: white;
            color: var(--purple-main);
            border: 2px solid var(--purple-button);
            font-weight: bold;
        }

        /* Şəkil Qalereyası Grid */
        .gallery-container {
            display: flex;
            justify-content: center;
            width: 100%;
        }

        .gallery-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 15px;
            width: 100%;
            max-width: 1200px;
            padding: 10px;
            animation: fadeIn 1s ease-out;
        }

        .gallery-grid-3x4 {
            grid-template-columns: repeat(4, 1fr);
            width: 80%;
        }

        .gallery-grid-2x5 {
            grid-template-columns: repeat(5, 1fr);
        }

        .gallery-item {
            position: relative;
            overflow: hidden;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.5);
            transition: var(--transition);
            background: rgba(0,0,0,0.5);
            aspect-ratio: 1;
        }

        .gallery-item img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
            opacity: 0; /* Load olana qədər gizli */
            transition: opacity 0.5s ease-in;
        }

        .gallery-item img.loaded {
            opacity: 1;
        }

        .gallery-item:hover {
            transform: scale(1.03);
            box-shadow: 0 8px 20px rgba(123, 0, 204, 0.6);
        }

        /* Çat sahəsi */
        .chat-container {
            flex: 1;
            background: rgba(0,0,0,0.6);
            border-radius: 15px;
            border: 1px solid var(--purple-button);
            margin-top: 20px;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            max-width: 1200px;
            margin-left: auto;
            margin-right: auto;
            width: 100%;
            animation: fadeIn 1.2s ease-out;
        }

        #chat-messages {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
            scroll-behavior: smooth;
        }

        .message {
            margin-bottom: 15px;
            padding: 10px 15px;
            border-radius: 10px;
            max-width: 80%;
            display: inline-block;
            animation: slideInMessage 0.3s ease-out;
        }

        .user-message {
            background-color: var(--purple-button);
            align-self: flex-end;
            text-align: right;
            float: right;
            clear: both;
        }

        .bot-message {
            background-color: rgba(255,255,255,0.1);
            align-self: flex-start;
            text-align: left;
            float: left;
            clear: both;
        }

        .chat-input-area {
            display: flex;
            padding: 15px;
            background: rgba(0,0,0,0.4);
            border-top: 1px solid rgba(255,255,255,0.1);
        }

        #chat-input {
            flex: 1;
            padding: 12px;
            background: rgba(255,255,255,0.05);
            border: none;
            border-radius: 20px;
            color: white;
            font-size: 16px;
            outline: none;
            margin-right: 10px;
        }

        #chat-input::placeholder { color: #aaa; }

        .btn-send {
            background-color: var(--purple-button);
            border-radius: 50%;
            width: 45px;
            height: 45px;
            border: none;
            color: white;
            cursor: pointer;
            transition: var(--transition);
        }

        .btn-send:hover { transform: scale(1.1); background-color: #9120e2; }

        /* Arxa plan animasiya */
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes slideInMessage { from { transform: translateY(10px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }

    </style>
</head>
<body>

    <div class="sidebar">
        <div class="sidebar-item" id="nav-profile"><i class="fas fa-user-circle"></i></div>
        <div class="sidebar-item" id="nav-create"><i class="fas fa-plus-circle"></i></div>
        <div class="sidebar-item" id="nav-gallery"><i class="fas fa-images"></i></div>
        <div class="sidebar-item" id="nav-chat"><i class="fas fa-comment-dots"></i></div>
    </div>

    <div class="main-content">
        <div class="auth-bar">
            <button class="btn btn-login">Login</button>
            <button class="btn btn-signup">Sign Up</button>
        </div>

        <div id="content-area">
            </div>

    </div>

    <script>
        // --- MƏLUMAT BAZASI (CUTE / ŞİRİN ŞƏKİLLƏR) ---
        const imageDB = {
            anime: [
                "https://cdn.pixabay.com/photo/2023/11/24/11/47/anime-girl-8409944_640.jpg",
                "https://cdn.pixabay.com/photo/2023/07/22/09/04/anime-girl-8142959_640.png",
                "https://cdn.pixabay.com/photo/2023/11/20/14/08/ai-generated-8401341_640.png",
                "https://cdn.pixabay.com/photo/2024/02/10/20/38/anime-girl-8565416_640.png",
                "https://cdn.pixabay.com/photo/2024/01/01/00/39/anime-8480358_640.png",
                "https://cdn.pixabay.com/photo/2023/11/04/18/12/anime-girl-8365448_640.png",
                "https://cdn.pixabay.com/photo/2023/12/28/19/21/anime-8474686_640.jpg",
                "https://cdn.pixabay.com/photo/2023/12/10/11/49/ai-generated-8441198_640.png",
                "https://cdn.pixabay.com/photo/2023/11/20/14/06/ai-generated-8401323_640.png",
                "https://cdn.pixabay.com/photo/2023/07/22/09/04/ai-generated-8142953_640.png"
            ],
            fantasy: [
                // Hinata, Tsunade, Sakura
                "https://i.pinimg.com/736x/88/50/a8/8850a80e1a14f48b0c8b3e8b0b925f38.jpg",
                "https://i.pinimg.com/originals/94/a3/52/94a35205b38d72855f44da5a805a8d4a.jpg",
                "https://i.pinimg.com/originals/2d/7e/6a/2d7e6ae229497e70eb37497d31210955.jpg",
                // 7 əlavə cute fantasy
                "https://cdn.pixabay.com/photo/2024/02/21/08/33/ai-generated-8587123_640.png",
                "https://cdn.pixabay.com/photo/2024/01/06/20/54/ai-generated-8492061_640.png",
                "https://cdn.pixabay.com/photo/2023/04/16/09/08/ai-generated-7929457_640.jpg",
                "https://cdn.pixabay.com/photo/2023/11/20/14/07/ai-generated-8401334_640.png",
                "https://cdn.pixabay.com/photo/2024/01/24/07/11/ai-generated-8529061_640.jpg",
                "https://cdn.pixabay.com/photo/2023/11/20/14/08/ai-generated-8401344_640.png",
                "https://cdn.pixabay.com/photo/2023/04/16/09/10/ai-generated-7929467_640.jpg"
            ],
            real: [
                "https://cdn.pixabay.com/photo/2023/08/01/17/38/portrait-8163539_640.jpg",
                "https://cdn.pixabay.com/photo/2023/08/17/20/08/girl-8197177_640.jpg",
                "https://cdn.pixabay.com/photo/2023/08/04/06/17/ai-generated-8168536_640.jpg",
                "https://cdn.pixabay.com/photo/2023/09/25/11/32/ai-generated-8274719_640.jpg",
                "https://cdn.pixabay.com/photo/2023/08/01/11/30/ai-generated-8162817_640.jpg",
                "https://cdn.pixabay.com/photo/2023/11/17/17/36/ai-generated-8394668_640.jpg",
                "https://cdn.pixabay.com/photo/2023/10/26/18/31/portrait-8343336_640.jpg",
                "https://cdn.pixabay.com/photo/2023/09/27/12/37/girl-8279480_640.jpg",
                "https://cdn.pixabay.com/photo/2023/11/17/17/36/ai-generated-8394669_640.jpg",
                "https://cdn.pixabay.com/photo/2023/08/17/21/28/ai-generated-8197277_640.jpg"
            ]
        };

        // --- AZƏRBAYCAN DİLİNDƏ ÇAT LOGİKASI ---
        const botResponses = {
            "salam": "Salam! Necəsən? Bu gün sənə nə yaradaraq kömək edə bilərəm?",
            "necəsən": "Təşəkkür edirəm, mən bir süni intellektəm və həmişə işə hazıram! Sən necəsən?",
            "nə edirsən": "Hazırda sənin istəklərini dinləyirəm və şəkilləri tənzimləyirəm. Sən nəsə yaratmaq istəyirsən?",
            "yarad": "Hemen! Hansı kateqoriyanı seçək: Gerçək, Anime, yoxsa Fantaziya?",
            "real": "Gerçəkçi şəkillərimizi Qalereyada görə bilərsən. Sizin üçün 10 ədəd şirin gerçək insan şəkli hazırlamışam!",
            "anime": "Anime kateqoriyasını mən də sevirəm! 10 ədəd ən məşhur və şirin anime qızının şəkli qalereyada səni gözləyir.",
            "fantaziya": "Möhtəşəm seçim! Hinata, Sakura, Tsunade və digər 7 şirin fantaziya xarakterini hazırlamışam.",
            "fantazi": "Möhtəşəm seçim! Hinata, Sakura, Tsunade və digər 7 şirin fantaziya xarakterini hazırlamışam.",
            "sexy": "Mənim tənzimləmələrimə görə yalnız 'cute' (şirin) şəkillər yaradıram. Buyurun, şirin şəkillərə baxın!",
            "lut": "Bağışlayın, mən yalnız etik və təhlükəsiz məzmunlar (şirin anime/insan şəkilləri) göstərirəm.",
            "bax": "Qalereya bölməsinə keçib şəkillərə tam baxa bilərsiniz.",
            "şəkil": "Qalereya bölməsinə keçib şəkillərə tam baxa bilərsiniz.",
            "sekil": "Qalereya bölməsinə keçib şəkillərə tam baxa bilərsiniz.",
            "sağ ol": "Xoş idi! Yenidən gözləyirəm.",
            "tesekkur": "Mən təşəkkür edirəm! Sənin üçün buradayam.",
            "təşəkkür": "Mən təşəkkür edirəm! Sənin üçün buradayam."
        };

        const defaultResponse = "Bağışlayın, bunu tam başa düşmədim. Söhbəti Azərbaycanca davam etdirə bilərik, yaxud Qalereyada şəkillərə baxa bilərsiniz.";

        // --- JS INTERFEYS IDARƏETMƏSİ ---
        const contentArea = document.getElementById('content-area');

        function clearContent() { contentArea.innerHTML = ''; }

        // Şəkil Qalereyasını göstərən funksiya
        function showGallery(category = 'anime') {
            clearContent();
            contentArea.innerHTML = `
                <div class="page-header">Kateqoriya Seçin</div>
                <div class="options-panel">
                    <div class="option-button ${category === 'real' ? 'active' : ''}" onclick="showGallery('real')">Gerçək</div>
                    <div class="option-button ${category === 'anime' ? 'active' : ''}" onclick="showGallery('anime')">Anime</div>
                    <div class="option-button ${category === 'fantasy' ? 'active' : ''}" onclick="showGallery('fantasy')">Fantaziya</div>
                </div>
                <div class="gallery-container">
                    <div class="gallery-grid ${category === 'fantasy' ? 'gallery-grid-3x4' : 'gallery-grid-2x5'}" id="grid">
                        </div>
                </div>
            `;
            
            const grid = document.getElementById('grid');
            const images = imageDB[category];
            images.forEach(src => {
                const item = document.createElement('div');
                item.className = 'gallery-item';
                const img = document.createElement('img');
                img.src = src;
                img.alt = `Cute ${category}`;
                
                // Şəkil tam yükləndikdən sonra göstər (animasiya üçün)
                img.onload = () => img.classList.add('loaded');
                
                item.appendChild(img);
                grid.appendChild(item);
            });
        }

        // Çat interfeysini göstərən funksiya
        function showChat() {
            clearContent();
            contentArea.innerHTML = `
                <div class="page-header">Süni İntellekt Çat</div>
                <div class="chat-container">
                    <div id="chat-messages">
                        <div class="message bot-message">Salam! Azəri Studio-ya xoş gəlmisən. Mənimlə Azərbaycanca danışa bilərsən. Necəsən?</div>
                    </div>
                    <div class="chat-input-area">
                        <input type="text" id="chat-input" placeholder="Mesajınızı bura yazın...">
                        <button class="btn-send" onclick="sendMessage()"><i class="fas fa-paper-plane"></i></button>
                    </div>
                </div>
            `;
            // Enter düyməsini dinləmək
            document.getElementById('chat-input').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') sendMessage();
            });
        }

        function sendMessage() {
            const input = document.getElementById('chat-input');
            const messages = document.getElementById('chat-messages');
            const text = input.value.trim().toLowerCase();

            if (text === '') return;

            // İstifadəçi mesajını əlavə et
            messages.innerHTML += `<div class="message user-message">${text}</div>`;
            input.value = '';
            
            // Botun cavabı (kiçik fasilə ilə)
            setTimeout(() => {
                let responseText = defaultResponse;

                // Açar sözləri yoxla (Sadələşdirilmiş analiz)
                for (const key in botResponses) {
                    if (text.includes(key)) {
                        responseText = botResponses[key];
                        break; // İlk tapılan açar sözdə dayan
                    }
                }

                messages.innerHTML += `<div class="message bot-message">${responseText}</div>`;
                messages.scrollTop = messages.scrollHeight; // Avtomatik aşağı sürüşdür
            }, 600);
        }

        // --- SIDEBAR NAVİQASİYASI ---
        document.getElementById('nav-create').addEventListener('click', () => showGallery('anime'));
        document.getElementById('nav-gallery').addEventListener('click', () => showGallery('anime'));
        document.getElementById('nav-chat').addEventListener('click', showChat);
        document.getElementById('nav-profile').addEventListener('click', () => alert('Profil bölməsi hələ hazır deyil!'));

        // Sayt yüklənəndə qalereyanı göstər (Hədəf səhifə)
        showGallery('anime');

    </script>
</body>
</html>
