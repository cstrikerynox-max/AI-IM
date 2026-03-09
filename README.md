<!DOCTYPE html>
<html lang="az">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Purple Anime AI Studio</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/js/all.min.js"></script>
    <style>
        :root {
            --main-purple: #6a0dad;
            --light-purple: #9b59b6;
            --dark-purple: #2c003e;
            --text-white: #ffffff;
        }

        body {
            margin: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: url('https://images.alphacoders.com/132/1322514.png') no-repeat center center fixed;
            background-size: cover;
            color: var(--text-white);
            overflow-x: hidden;
        }

        /* Arxa fon bulaniqliq */
        body::before {
            content: "";
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(44, 0, 62, 0.7);
            z-index: -1;
        }

        /* Sidebar */
        .sidebar {
            position: fixed;
            left: 0;
            top: 0;
            width: 80px;
            height: 100%;
            background: rgba(20, 0, 30, 0.9);
            display: flex;
            flex-direction: column;
            align-items: center;
            padding-top: 20px;
            border-right: 2px solid var(--main-purple);
            z-index: 100;
        }

        .sidebar-item {
            margin: 20px 0;
            font-size: 24px;
            cursor: pointer;
            transition: 0.3s;
            color: var(--light-purple);
        }

        .sidebar-item:hover {
            color: white;
            transform: scale(1.2);
        }

        /* Main Content */
        .main-content {
            margin-left: 100px;
            padding: 20px;
            animation: fadeIn 1s ease-in;
        }

        /* Auth Buttons */
        .auth-bar {
            display: flex;
            justify-content: flex-end;
            gap: 15px;
            margin-bottom: 50px;
        }

        .btn {
            padding: 10px 25px;
            border: none;
            border-radius: 20px;
            cursor: pointer;
            font-weight: bold;
            transition: 0.3s;
        }

        .btn-login { background: transparent; border: 2px solid white; color: white; }
        .btn-signup { background: var(--main-purple); color: white; }
        .btn:hover { transform: translateY(-3px); box-shadow: 0 5px 15px rgba(155, 89, 182, 0.4); }

        /* Create Modal */
        .modal {
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: var(--dark-purple);
            padding: 30px;
            border-radius: 15px;
            border: 2px solid var(--main-purple);
            text-align: center;
            z-index: 1000;
            width: 80%;
            max-width: 600px;
            animation: slideUp 0.5s ease-out;
        }

        .options-container {
            display: flex;
            justify-content: space-around;
            margin-top: 20px;
        }

        .option-box {
            background: var(--main-purple);
            padding: 20px;
            border-radius: 10px;
            cursor: pointer;
            width: 100px;
            transition: 0.3s;
        }

        .option-box:hover { background: var(--light-purple); }

        /* Chat/Prompt Area */
        .prompt-section {
            background: rgba(0, 0, 0, 0.5);
            padding: 40px;
            border-radius: 20px;
            border: 1px solid var(--main-purple);
            max-width: 800px;
            margin: auto;
        }

        input[type="text"] {
            width: 80%;
            padding: 15px;
            border-radius: 25px;
            border: none;
            outline: none;
            background: rgba(255, 255, 255, 0.1);
            color: white;
            font-size: 16px;
        }

        .gallery-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
            gap: 15px;
            margin-top: 20px;
            display: none;
        }

        .gallery-item img {
            width: 100%;
            border-radius: 10px;
            transition: 0.3s;
        }

        .gallery-item img:hover { transform: scale(1.05); }

        /* Animations */
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes slideUp { from { transform: translate(-50%, 0%); opacity: 0; } to { transform: translate(-50%, -50%); opacity: 1; } }

        .overlay {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.8);
            z-index: 999;
        }
    </style>
</head>
<body>

    <div class="sidebar">
        <div class="sidebar-item" onclick="showSection('profile')"><i class="fas fa-user-circle"></i></div>
        <div class="sidebar-item" onclick="toggleCreate()"><i class="fas fa-plus-circle"></i></div>
        <div class="sidebar-item" onclick="showSection('gallery')"><i class="fas fa-images"></i></div>
    </div>

    <div class="main-content">
        <div class="auth-bar">
            <button class="btn btn-login">Login</button>
            <button class="btn btn-signup">Sign Up</button>
        </div>

        <div class="prompt-section">
            <h2>AI Cute Creator & Chat</h2>
            <div id="chat-box" style="height: 200px; overflow-y: auto; margin-bottom: 20px; text-align: left; padding: 10px; border-bottom: 1px solid #444;">
                <p><i>Sistem: Salam! Mənə nə yaratmaq istədiyini de...</i></p>
            </div>
            <input type="text" id="promptInput" placeholder="Bura promt yazın (məsələn: cute anime girl eating cake)...">
            <button class="btn btn-signup" onclick="generateImage()">Yarat / Göndər</button>
        </div>

        <div id="gallery" class="gallery-grid">
            </div>
    </div>

    <div class="overlay" id="overlay" onclick="toggleCreate()"></div>
    <div class="modal" id="createModal">
        <h3>Kateqoriya Seçin</h3>
        <div class="options-container">
            <div class="option-box" onclick="loadContent('real')">Real</div>
            <div class="option-box" onclick="loadContent('anime')">Anime</div>
            <div class="option-box" onclick="loadContent('fantasy')">Fantasy</div>
        </div>
        <div id="modal-images" class="gallery-grid" style="display: grid; margin-top: 20px;"></div>
    </div>

    <script>
        function toggleCreate() {
            const modal = document.getElementById('createModal');
            const overlay = document.getElementById('overlay');
            const isOpen = modal.style.display === 'block';
            modal.style.display = isOpen ? 'none' : 'block';
            overlay.style.display = isOpen ? 'none' : 'block';
        }

        function showSection(section) {
            const gallery = document.getElementById('gallery');
            if(section === 'gallery') {
                gallery.style.display = 'grid';
                loadContent('anime', 'gallery');
            } else {
                alert(section + " bölməsi tezliklə aktiv olacaq.");
            }
        }

        const data = {
            anime: [
                "https://cdn.pixabay.com/photo/2023/04/14/11/49/ai-generated-7924945_1280.jpg",
                "https://cdn.pixabay.com/photo/2023/02/14/18/55/anime-7790131_1280.jpg",
                "https://cdn.pixabay.com/photo/2023/03/05/19/21/anime-7832070_1280.jpg",
                "https://cdn.pixabay.com/photo/2022/12/12/11/36/anime-girl-7650849_1280.jpg",
                "https://cdn.pixabay.com/photo/2023/06/15/09/56/anime-8064972_1280.jpg",
                "https://cdn.pixabay.com/photo/2023/01/24/13/23/anime-7741003_1280.jpg",
                "https://cdn.pixabay.com/photo/2023/01/21/13/44/anime-7734123_1280.jpg",
                "https://cdn.pixabay.com/photo/2023/02/12/11/50/anime-7784869_1280.jpg",
                "https://cdn.pixabay.com/photo/2023/04/07/11/04/anime-7906105_1280.jpg",
                "https://cdn.pixabay.com/photo/2023/05/11/15/45/anime-7986705_1280.jpg"
            ],
            fantasy: [
                "https://img.freepik.com/premium-photo/cute-anime-girl-with-pink-hair-flowers_863013-41.jpg",
                "https://img.freepik.com/premium-photo/portrait-anime-girl-with-magical-powers_124507-64057.jpg",
                "https://img.freepik.com/premium-photo/beautiful-anime-character-fantasy-world_863013-125.jpg"
            ],
            real: [
                "https://img.freepik.com/free-photo/view-beautiful-anime-character-with-realistic-features_23-2151121857.jpg"
            ]
        };

        function loadContent(type, target = 'modal-images') {
            const container = document.getElementById(target);
            container.innerHTML = '';
            const items = data[type] || [];
            
            items.forEach(src => {
                const div = document.createElement('div');
                div.className = 'gallery-item';
                div.innerHTML = `<img src="${src}" alt="Cute ${type}">`;
                container.appendChild(div);
            });
            
            if(target === 'modal-images') {
                container.style.display = 'grid';
            }
        }

        function generateImage() {
            const input = document.getElementById('promptInput');
            const chatBox = document.getElementById('chat-box');
            
            if(input.value.trim() === "") return;

            // Chat simulasiyası
            chatBox.innerHTML += `<p><strong>Sən:</strong> ${input.value}</p>`;
            
            setTimeout(() => {
                chatBox.innerHTML += `<p><strong>AI:</strong> Bu möhtəşəm bir fikir! Sənin üçün cute anime üslubunda şəkil hazırlanır...</p>`;
                chatBox.scrollTop = chatBox.scrollHeight;
                
                // Qalereyaya bir şəkil əlavə edək
                const gallery = document.getElementById('gallery');
                gallery.style.display = 'grid';
                const div = document.createElement('div');
                div.className = 'gallery-item';
                div.innerHTML = `<img src="${data.anime[Math.floor(Math.random()*10)]}" style="border: 2px solid var(--light-purple)">`;
                gallery.prepend(div);
            }, 1000);

            input.value = "";
        }
    </script>
</body>
</html>
