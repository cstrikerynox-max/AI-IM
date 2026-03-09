<!-- BEGIN SECOND PART OF SITE CODE -->
<script>
// Chat funksionallığı
const chatInput = document.getElementById("chat-input");
const chatSend = document.getElementById("chat-send");
const chatBox = document.getElementById("chat-box");

chatSend.addEventListener("click", () => {
    const msg = chatInput.value.trim();
    if(msg === "") return;
    const userMsg = document.createElement("div");
    userMsg.classList.add("chat-message", "user");
    userMsg.innerText = msg;
    chatBox.appendChild(userMsg);
    chatInput.value = "";
    chatBox.scrollTop = chatBox.scrollHeight;

    // Bot reply simulation
    setTimeout(() => {
        const botMsg = document.createElement("div");
        botMsg.classList.add("chat-message", "bot");
        botMsg.innerText = generateBotResponse(msg);
        chatBox.appendChild(botMsg);
        chatBox.scrollTop = chatBox.scrollHeight;
    }, 800);
});

function generateBotResponse(msg) {
    // Basic responses with anime style
    msg = msg.toLowerCase();
    if(msg.includes("salam")) return "Salam! Necəsən? 🌸";
    if(msg.includes("necəsən")) return "Mən yaxşıyam! Sən necə hiss edirsən?";
    if(msg.includes("anime")) return "Anime sevirsən? Mən də çox sevirəm! 💖";
    if(msg.includes("nsfw")) return "Bu mövzuda danışmaq mümkün deyil 😅";
    return "Hmmm... maraqlıdır! 😺";
}

// Gallery interaktivliyi
const galleryImages = document.querySelectorAll(".gallery img");
galleryImages.forEach(img => {
    img.addEventListener("click", () => {
        const modal = document.getElementById("modal");
        const modalImg = document.getElementById("modal-img");
        modal.style.display = "flex";
        modalImg.src = img.src;
    });
});

const modalClose = document.getElementById("modal-close");
modalClose.addEventListener("click", () => {
    document.getElementById("modal").style.display = "none";
});

// Create animasiya effekti
const createBtn = document.getElementById("create-btn");
createBtn.addEventListener("click", () => {
    const createArea = document.getElementById("create-area");
    createArea.classList.add("active");
    setTimeout(() => {
        createArea.classList.remove("active");
    }, 1200);
});

// Floating hearts effect for anime feel
function createHeart() {
    const heart = document.createElement("div");
    heart.classList.add("heart");
    heart.style.left = Math.random() * 100 + "vw";
    heart.style.animationDuration = (2 + Math.random() * 3) + "s";
    document.body.appendChild(heart);
    setTimeout(() => heart.remove(), 5000);
}

setInterval(createHeart, 400);

// Dark/Light mode toggle
const themeToggle = document.getElementById("theme-toggle");
themeToggle.addEventListener("click", () => {
    document.body.classList.toggle("dark-mode");
});

// Fancy scrolling effect
window.addEventListener("scroll", () => {
    const elements = document.querySelectorAll(".fade-on-scroll");
    const scrollTop = window.scrollY + window.innerHeight;
    elements.forEach(el => {
        if(scrollTop > el.offsetTop + 50){
            el.classList.add("visible");
        }
    });
});
</script>

<style>
/* SECOND PART CSS */
.chat-message {
    padding: 10px 15px;
    margin: 8px 0;
    border-radius: 15px;
    max-width: 70%;
    word-wrap: break-word;
    font-family: 'Segoe UI', sans-serif;
    transition: 0.3s all;
}
.chat-message.user {
    background: linear-gradient(135deg, #ff9a9e, #fad0c4);
    color: #fff;
    align-self: flex-end;
}
.chat-message.bot {
    background: linear-gradient(135deg, #a18cd1, #fbc2eb);
    color: #fff;
    align-self: flex-start;
}
#modal {
    position: fixed;
    top:0; left:0;
    width:100%; height:100%;
    background: rgba(0,0,0,0.8);
    display:none;
    justify-content: center;
    align-items: center;
    z-index: 1000;
}
#modal img {
    max-width: 90%; max-height: 80%;
    border-radius: 12px;
}
#modal-close {
    position: absolute;
    top: 20px; right: 30px;
    font-size: 2rem;
    color: #fff;
    cursor: pointer;
}
#gallery {
    display:grid;
    grid-template-columns: repeat(auto-fit,minmax(180px,1fr));
    gap:10px;
}
#gallery img {
    width:100%;
    border-radius:10px;
    cursor:pointer;
    transition: transform 0.3s, box-shadow 0.3s;
}
#gallery img:hover {
    transform: scale(1.05);
    box-shadow: 0 8px 20px rgba(0,0,0,0.3);
}
#theme-toggle {
    position:fixed;
    top:10px; right:10px;
    background: #fff;
    border:none;
    padding:8px 12px;
    border-radius:8px;
    cursor:pointer;
    box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}
body.dark-mode {
    background:#1a1a2e;
    color:#f0f0f0;
}
body.dark-mode #theme-toggle {
    background:#444;
    color:#fff;
}
.heart {
    position: fixed;
    bottom: -20px;
    font-size: 20px;
    color: #ff6b81;
    animation: rise 5s linear forwards;
    z-index:999;
}
@keyframes rise {
    0% { transform: translateY(0) scale(1); opacity:1; }
    100% { transform: translateY(-100vh) scale(0.5); opacity:0; }
}
.fade-on-scroll {
    opacity: 0;
    transform: translateY(30px);
    transition: all 0.8s ease-out;
}
.fade-on-scroll.visible {
    opacity:1;
    transform: translateY(0);
}
#create-area {
    position:fixed;
    bottom:20px; right:20px;
    width:200px; height:50px;
    background: #ff6b81;
    border-radius:25px;
    display:flex;
    justify-content:center;
    align-items:center;
    color:#fff;
    cursor:pointer;
    transition:0.3s all;
}
#create-area.active {
    transform: scale(1.3);
    box-shadow: 0 0 20px rgba(255,107,129,0.5);
}
</style>
<!-- END SECOND PART OF SITE CODE -->
<!-- BEGIN SECOND + THIRD PART OF SITE CODE -->
<script>
// --- Chat funksionallığı (davamı) ---
const chatHistory = [];

function generateBotResponseAdvanced(msg) {
    msg = msg.toLowerCase();
    if(msg.includes("salam")) return "Salam! 🌸 Nə xəbər?";
    if(msg.includes("necəsən")) return "Mən yaxşıyam, sən necə hiss edirsən? 😺";
    if(msg.includes("anime")) return "Anime möhtəşəmdir! Hər növ sevirsən? 💖";
    if(msg.includes("nsfw")) return "Bu mövzu burada mövcud deyil 😅";
    if(msg.includes("şəkil")) return "Gallerydən bir şəkil seçə bilərsən!";
    return "Hmmm… maraqlı fikir! ✨";
}

// --- Gallery və modal funksiyaları ---
const galleryContainer = document.getElementById("gallery");
function addGalleryImage(url) {
    const img = document.createElement("img");
    img.src = url;
    img.classList.add("fade-on-scroll");
    galleryContainer.appendChild(img);
    img.addEventListener("click", () => {
        document.getElementById("modal").style.display = "flex";
        document.getElementById("modal-img").src = url;
    });
}

// Demo images (real URLs)
const demoImages = [
    "https://i.ibb.co/0VbYr2k/anime1.jpg",
    "https://i.ibb.co/3rM2V5P/anime2.jpg",
    "https://i.ibb.co/7Gsv6xD/anime3.jpg",
    "https://i.ibb.co/ZVPwDkt/anime4.jpg",
    "https://i.ibb.co/2k5L9hR/anime5.jpg"
];
demoImages.forEach(url => addGalleryImage(url));

// --- Create button / prompt area ---
const promptInput = document.getElementById("prompt-input");
const createOutput = document.getElementById("create-output");
document.getElementById("create-btn").addEventListener("click", () => {
    const prompt = promptInput.value.trim();
    if(prompt === "") return;
    createOutput.innerText = `Yeni anime yaradıldı: "${prompt}" 🌸`;
    createOutput.classList.add("fade-on-scroll");
    setTimeout(() => createOutput.classList.remove("fade-on-scroll"), 1500);
    promptInput.value = "";
});

// --- Parallax background effect ---
window.addEventListener("mousemove", e => {
    const x = e.clientX / window.innerWidth;
    const y = e.clientY / window.innerHeight;
    document.body.style.backgroundPosition = `${50 + x*10}% ${50 + y*10}%`;
});

// --- Floating sparkles ---
function createSparkle() {
    const sparkle = document.createElement("div");
    sparkle.classList.add("sparkle");
    sparkle.style.left = Math.random() * 100 + "vw";
    sparkle.style.top = Math.random() * 100 + "vh";
    document.body.appendChild(sparkle);
    setTimeout(() => sparkle.remove(), 4000);
}
setInterval(createSparkle, 500);

// --- Smooth scroll to top button ---
const topBtn = document.getElementById("top-btn");
topBtn.addEventListener("click", () => window.scrollTo({top:0, behavior:"smooth"}));
window.addEventListener("scroll", () => {
    if(window.scrollY > 300) topBtn.style.display = "block";
    else topBtn.style.display = "none";
});
</script>

<style>
/* --- SECOND + THIRD PART CSS --- */
#top-btn {
    position: fixed;
    bottom: 30px; right: 30px;
    padding: 10px 15px;
    border:none;
    border-radius: 10px;
    background: #ff9a9e;
    color:#fff;
    cursor:pointer;
    display:none;
    box-shadow: 0 4px 12px rgba(0,0,0,0.3);
    z-index: 1000;
    transition: 0.3s all;
}
#top-btn:hover {
    transform: scale(1.1);
}
.sparkle {
    position: fixed;
    width: 6px; height: 6px;
    border-radius:50%;
    background: #fff;
    opacity:0.9;
    animation: sparkleAnim 1.5s linear forwards;
    z-index:999;
}
@keyframes sparkleAnim {
    0% { transform: scale(1) rotate(0deg); opacity:1; }
    100% { transform: scale(0) rotate(360deg); opacity:0; }
}

/* Prompt create area */
#create-area-wrapper {
    position: fixed;
    bottom: 80px; right: 20px;
    display:flex; flex-direction:column; align-items:flex-end;
    gap:5px;
}
#create-output {
    padding:10px 15px;
    background: linear-gradient(135deg, #f6d365, #fda085);
    border-radius:15px;
    color:#fff;
    font-weight:600;
}

/* Footer styling */
footer {
    margin-top:50px;
    padding:20px;
    text-align:center;
    font-size:0.9rem;
    color:#888;
}

/* Responsive tweaks */
@media(max-width:600px){
    #gallery { grid-template-columns: repeat(auto-fit,minmax(120px,1fr)); }
    #create-area, #create-area-wrapper { width:150px; height:45px; }
    .chat-message { max-width:85%; font-size:0.85rem; }
}
</style>

<!-- END SECOND + THIRD PART OF SITE CODE -->
