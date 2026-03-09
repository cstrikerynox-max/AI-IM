<!DOCTYPE html>
<html lang="az">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Ultra Cute AI World</title>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap" rel="stylesheet">
<style>

/* ---------------- Body & Background ---------------- */
body{
    margin:0;
    padding:0;
    font-family:'Poppins',sans-serif;
    background: linear-gradient(135deg,#6a0dad,#b96eff);
    color:#fff;
    overflow-x:hidden;
}
#app{
    display:flex;
    height:100vh;
}

/* ---------------- Sidebar ---------------- */
.sidebar{
    width:260px;
    background:rgba(0,0,0,0.35);
    backdrop-filter:blur(12px);
    height:100vh;
    padding:25px;
    display:flex;
    flex-direction:column;
    gap:18px;
    box-shadow:0 0 20px rgba(0,0,0,0.5);
    animation: slideIn 1s;
}
.sidebar h2{
    font-size:26px;
    font-weight:700;
    letter-spacing:1px;
    text-shadow:1px 1px 6px rgba(0,0,0,0.5);
}
.sidebar button{
    background:#9b30ff;
    border:none;
    padding:14px;
    font-size:16px;
    border-radius:12px;
    cursor:pointer;
    transition:.3s;
}
.sidebar button:hover{
    transform:scale(1.05);
    background:#c274ff;
}
@keyframes slideIn{
    from{transform:translateX(-250px);opacity:0;}
    to{transform:translateX(0);opacity:1;}
}

/* ---------------- Main Content ---------------- */
.main{
    flex:1;
    padding:30px;
    overflow-y:auto;
    position:relative;
}
.title{
    font-size:32px;
    margin-bottom:20px;
    text-shadow:2px 2px 6px rgba(0,0,0,0.5);
}
.panel{
    background:rgba(255,255,255,0.08);
    border-radius:18px;
    padding:25px;
    margin-bottom:20px;
    animation:fadeIn .7s;
}
@keyframes fadeIn{
    from{opacity:0;transform:translateY(20px);}
    to{opacity:1;transform:translateY(0);}
}

/* ---------------- Inputs & Buttons ---------------- */
input,textarea,select{
    width:100%;
    padding:12px;
    border-radius:10px;
    border:none;
    margin-top:12px;
    font-size:15px;
    outline:none;
}
.generate{
    background:#b084ff;
    border:none;
    padding:14px;
    margin-top:14px;
    border-radius:12px;
    cursor:pointer;
    font-weight:600;
    transition:.3s;
}
.generate:hover{
    transform:scale(1.05);
    background:#c77dff;
}

/* ---------------- Images Grid ---------------- */
.images{
    display:grid;
    grid-template-columns:repeat(auto-fill,minmax(160px,1fr));
    gap:12px;
    margin-top:20px;
}
.images img{
    width:100%;
    border-radius:14px;
    transition:.4s;
    box-shadow:0 6px 20px rgba(0,0,0,0.35);
}
.images img:hover{
    transform:scale(1.05);
}

/* ---------------- Chat Box ---------------- */
.chatbox{
    height:320px;
    overflow:auto;
    background:rgba(0,0,0,0.25);
    padding:12px;
    border-radius:12px;
    margin-bottom:12px;
}
.msg{
    margin:6px 0;
}
.msg b{
    color:#ffd1ff;
}

/* ---------------- Scrollbar ---------------- */
::-webkit-scrollbar{
    width:10px;
}
::-webkit-scrollbar-track{
    background:rgba(255,255,255,0.05);
    border-radius:10px;
}
::-webkit-scrollbar-thumb{
    background:#9b30ff;
    border-radius:10px;
}
::-webkit-scrollbar-thumb:hover{
    background:#c274ff;
}

/* ---------------- Background Animation Particles ---------------- */
#particles{
    position:fixed;
    top:0;left:0;
    width:100%;
    height:100%;
    z-index:-1;
    pointer-events:none;
}
.particle{
    position:absolute;
    width:6px;
    height:6px;
    border-radius:50%;
    background:rgba(255,255,255,0.6);
    animation:particleAnim linear infinite;
}
@keyframes particleAnim{
    from{transform:translateY(0) translateX(0);}
    to{transform:translateY(-1000px) translateX(1000px);}
}

</style>
</head>
<body>
<div id="particles"></div>
<div id="app">
    <div class="sidebar">
        <h2>Ultra Cute AI</h2>
        <button onclick="show('profile')">Profile</button>
        <button onclick="show('create')">Create</button>
        <button onclick="show('gallery')">Gallery</button>
        <button onclick="show('chat')">Chat</button>
    </div>
    <div class="main" id="content">
        <div class="title">Welcome</div>
        <div class="panel">Ultra Cute AI Generator</div>
    </div>
</div>

<script>
const content=document.getElementById("content");

/* ---------------- Sidebar Navigation ---------------- */
function show(page){
    if(page==="profile"){
        content.innerHTML=`
        <div class="title">Profile</div>
        <div class="panel">
            <input placeholder="Username">
            <input placeholder="Password" type="password">
            <button class="generate">Login</button>
            <button class="generate">Sign Up</button>
        </div>`;
    }
    if(page==="create"){
        content.innerHTML=`
        <div class="title">Create Image</div>
        <div class="panel">
            <select id="type">
                <option>Real</option>
                <option>Anime</option>
                <option>Fantasy</option>
            </select>
            <textarea id="prompt" placeholder="Prompt yaz..."></textarea>
            <button class="generate" onclick="generate()">Generate</button>
            <div class="images" id="result"></div>
        </div>`;
    }
    if(page==="gallery"){
        content.innerHTML=`
        <div class="title">Gallery</div>
        <div class="panel">
            <div class="images">
                <img src="https://images.pexels.com/photos/38554/girl-portrait-smile-pretty-38554.jpeg">
                <img src="https://images.pexels.com/photos/415829/pexels-photo-415829.jpeg">
                <img src="https://images.pexels.com/photos/247599/pexels-photo-247599.jpeg">
                <img src="https://images.pexels.com/photos/774909/pexels-photo-774909.jpeg">
                <img src="https://images.pexels.com/photos/1130626/pexels-photo-1130626.jpeg">
                <img src="https://i.pinimg.com/originals/ea/b9/e5/eab9e5af59e3dc1ae27e46c59bddf9fc.jpg">
                <img src="https://i.pinimg.com/originals/8c/7e/23/8c7e23d063a2ce9b9b50e8d1bcd8b5df.jpg">
                <img src="https://i.pinimg.com/originals/76/e3/0f/76e30f8b8a98ea6d8395cc39cea52f5f.jpg">
                <img src="https://i.pinimg.com/originals/6f/31/c0/6f31c0dc8f91e7c282a754826f0d77fa.jpg">
                <img src="https://i.pinimg.com/originals/3e/23/6a/3e236a5857a2772b2a861a24d40a5f97.jpg">
            </div>
        </div>`;
    }
    if(page==="chat"){
        content.innerHTML=`
        <div class="title">AI Chat</div>
        <div class="panel">
            <div class="chatbox" id="chatbox"></div>
            <input id="msg" placeholder="Mesaj yaz...">
            <button class="generate" onclick="send()">Send</button>
        </div>`;
    }
}

/* ---------------- Image Generation ---------------- */
function generate(){
    const p=document.getElementById("prompt").value;
    const type=document.getElementById("type").value;
    const r=document.getElementById("result");
    r.innerHTML="";
    let imgs=[];
    if(type==="Real") imgs=[
        "https://images.pexels.com/photos/38554/girl-portrait-smile-pretty-38554.jpeg",
        "https://images.pexels.com/photos/415829/pexels-photo-415829.jpeg",
        "https://images.pexels.com/photos/247599/pexels-photo-247599.jpeg",
        "https://images.pexels.com/photos/774909/pexels-photo-774909.jpeg",
        "https://images.pexels.com/photos/1130626/pexels-photo-1130626.jpeg"
    ];
    if(type==="Anime") imgs=[
        "https://i.pinimg.com/originals/ea/b9/e5/eab9e5af59e3dc1ae27e46c59bddf9fc.jpg",
        "https://i.pinimg.com/originals/8c/7e/23/8c7e23d063a2ce9b9b50e8d1bcd8b5df.jpg",
        "https://i.pinimg.com/originals/76/e3/0f/76e30f8b8a98ea6d8395cc39cea52f5f.jpg",
        "https://i.pinimg.com/originals/6f/31/c0/6f31c0dc8f91e7c282a754826f0d77fa.jpg",
        "https://i.pinimg.com/originals/3e/23/6a/3e236a5857a2772b2a861a24d40a5f97.jpg"
    ];
    if(type==="Fantasy") imgs=[
        "https://i.pinimg.com/originals/43/aa/e6/43aae68d1071198c52338bb4df56bb37.jpg",
        "https://i.pinimg.com/originals/1e/1a/4e/1e1a4e2f4e66db1ea8ea722c0aaf5d8a.jpg",
        "https://i.pinimg.com/originals/fc/90/8b/fc908b3e87f58d0ed63d83fd803a6d62.jpg",
        "https://i.pinimg.com/originals/70/d2/9e/70d29e1fa185be1071a8d9adcb3ed2c1.jpg",
        "https://i.pinimg.com/originals/df/66/80/df6680a4c4b8572b8a0c87e99a1193c0.jpg",
        "https://i.pinimg.com/originals/6b/04/33/6b0433bbef189fbf69043f3d9769f5d4.jpg",
        "https://i.pinimg.com/originals/9d/e9/8c/9de98c7d8d526a736ecd3d8eb1496768.jpg",
        "https://i.pinimg.com/originals/58/91/3c/58913c992a4a1b15d39643d9f887ab22.jpg",
        "https://i.pinimg.com/originals/2e/57/d7/2e57d7f7a7c7511fc6bdcbbd7c19f5d5.jpg",
        "https://i.pinimg.com/originals/5f/3a/90/5f3a90451f65132950c13afa8bc0f71d.jpg"
    ];
    imgs.forEach(src=>{
        let img=document.createElement("img");
        img.src=src;
        r.appendChild(img);
    });
}

/* ---------------- Chat Function ---------------- */
function send(){
    const m=document.getElementById("msg").value;
    const box=document.getElementById("chatbox");
    box.innerHTML+=`<div class='msg'><b>Sen:</b> ${m}</div>`;
    let reply="Bunu maraqlı sual hesab edirəm.";
    if(m.toLowerCase().includes("salam")) reply="Salam! Necə kömək edə bilərəm?";
    if(m.toLowerCase().includes("anime")) reply="Anime şəkillərini Create bölməsindən yarada bilərsən.";
    if(m.toLowerCase().includes("cute")) reply="Ultra cute görüntüləri burada görə bilərsən!";
    box.innerHTML+=`<div class='msg'><b>AI:</b> ${reply}</div>`;
    box.scrollTop=box.scrollHeight;
}

/* ---------------- Particle Background ---------------- */
function createParticles(){
    const container=document.getElementById("particles");
    for(let i=0;i<120;i++){
        let p=document.createElement("div");
        p.classList.add("particle");
        p.style.left=Math.random()*window.innerWidth+"px";
        p.style.top=Math.random()*window.innerHeight+"px";
        p.style.width=2+Math.random()*6+"px";
        p.style.height=2+Math.random()*6+"px";
        p.style.animationDuration=5+Math.random()*10+"s";
        container.appendChild(p);
    }
}
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
