<!DOCTYPE html>
<html lang="az">
<head>
<meta charset="UTF-8">
<title>Ultra Cute AI Generator</title>
<style>
body{
  margin:0;
  font-family: 'Segoe UI', sans-serif;
  background: linear-gradient(135deg, #6a0dad, #a566ff);
  color:white;
  overflow:hidden;
}
#app{
  display:flex;
  height:100vh;
}
.sidebar{
  width:240px;
  background: rgba(0,0,0,0.35);
  backdrop-filter: blur(12px);
  padding:25px;
  display:flex;
  flex-direction:column;
  gap:15px;
  animation:slide 1s;
}
@keyframes slide{
  from{transform:translateX(-200px);opacity:0}
  to{transform:translateX(0);opacity:1}
}
.sidebar button{
  background:#9b59ff;
  border:none;
  padding:14px;
  color:white;
  border-radius:12px;
  cursor:pointer;
  transition:.3s;
}
.sidebar button:hover{
  transform:scale(1.05);
  background:#c77dff;
}
.main{
  flex:1;
  padding:30px;
  overflow:auto;
  position:relative;
}
.title{
  font-size:30px;
  margin-bottom:20px;
  text-shadow: 1px 1px 4px #000;
}
.panel{
  background: rgba(255,255,255,0.08);
  border-radius:15px;
  padding:20px;
  margin-bottom:20px;
  animation:fade .7s;
}
@keyframes fade{
  from{opacity:0;transform:translateY(20px)}
  to{opacity:1;transform:translateY(0)}
}
input,textarea,select{
  width:100%;
  padding:10px;
  border-radius:8px;
  border:none;
  margin-top:10px;
}
.generate{
  background:#b084ff;
  border:none;
  padding:12px;
  margin-top:10px;
  border-radius:10px;
  cursor:pointer;
}
.images{
  display:grid;
  grid-template-columns:repeat(auto-fill,minmax(160px,1fr));
  gap:10px;
  margin-top:20px;
}
.images img{
  width:100%;
  border-radius:12px;
  transition:.4s;
  box-shadow: 0 4px 15px rgba(0,0,0,0.3);
}
.images img:hover{
  transform:scale(1.05);
}
.chatbox{
  height:320px;
  overflow:auto;
  background: rgba(0,0,0,0.25);
  padding:10px;
  border-radius:12px;
  margin-bottom:10px;
}
.msg{
  margin:6px 0;
}
.msg b{
  color:#ffd1ff;
}
</style>
</head>
<body>
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
          <!-- Real Cute Girls -->
          <img src="https://i.ibb.co/4d7F9vK/real1.jpg">
          <img src="https://i.ibb.co/qYz1HqT/real2.jpg">
          <img src="https://i.ibb.co/X2T8RzK/real3.jpg">
          <img src="https://i.ibb.co/6rV9qV2/real4.jpg">
          <img src="https://i.ibb.co/2dM6jC5/real5.jpg">
          <!-- Anime Cute -->
          <img src="https://i.ibb.co/1qR3kR2/anime1.jpg">
          <img src="https://i.ibb.co/T1p2xF3/anime2.jpg">
          <img src="https://i.ibb.co/qYv6V9K/anime3.jpg">
          <img src="https://i.ibb.co/4jqFzT1/anime4.jpg">
          <img src="https://i.ibb.co/8Kf7vP2/anime5.jpg">
          <!-- Fantasy -->
          <img src="https://i.ibb.co/hc1kTtQ/hinata.jpg">
          <img src="https://i.ibb.co/vvB8GJq/tsunade.jpg">
          <img src="https://i.ibb.co/1M1gFjN/sakura.jpg">
          <img src="https://i.ibb.co/5F1jQvP/fantasy1.jpg">
          <img src="https://i.ibb.co/8F4xTtM/fantasy2.jpg">
          <img src="https://i.ibb.co/9YzPqV7/fantasy3.jpg">
          <img src="https://i.ibb.co/4V1kL9D/fantasy4.jpg">
          <img src="https://i.ibb.co/xY9FhR8/fantasy5.jpg">
          <img src="https://i.ibb.co/6V7tQ1K/fantasy6.jpg">
          <img src="https://i.ibb.co/7K9tR2L/fantasy7.jpg">
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

function generate(){
  const p=document.getElementById("prompt").value;
  const type=document.getElementById("type").value;
  const r=document.getElementById("result");
  r.innerHTML="";
  let imgs=[];
  if(type==="Real") imgs=[
    "https://i.ibb.co/4d7F9vK/real1.jpg",
    "https://i.ibb.co/qYz1HqT/real2.jpg",
    "https://i.ibb.co/X2T8RzK/real3.jpg",
    "https://i.ibb.co/6rV9qV2/real4.jpg",
    "https://i.ibb.co/2dM6jC5/real5.jpg"
  ];
  if(type==="Anime") imgs=[
    "https://i.ibb.co/1qR3kR2/anime1.jpg",
    "https://i.ibb.co/T1p2xF3/anime2.jpg",
    "https://i.ibb.co/qYv6V9K/anime3.jpg",
    "https://i.ibb.co/4jqFzT1/anime4.jpg",
    "https://i.ibb.co/8Kf7vP2/anime5.jpg"
  ];
  if(type==="Fantasy") imgs=[
    "https://i.ibb.co/hc1kTtQ/hinata.jpg",
    "https://i.ibb.co/vvB8GJq/tsunade.jpg",
    "https://i.ibb.co/1M1gFjN/sakura.jpg",
    "https://i.ibb.co/5F1jQvP/fantasy1.jpg",
    "https://i.ibb.co/8F4xTtM/fantasy2.jpg",
    "https://i.ibb.co/9YzPqV7/fantasy3.jpg",
    "https://i.ibb.co/4V1kL9D/fantasy4.jpg",
    "https://i.ibb.co/xY9FhR8/fantasy5.jpg",
    "https://i.ibb.co/6V7tQ1K/fantasy6.jpg",
    "https://i.ibb.co/7K9tR2L/fantasy7.jpg"
  ];
  imgs.forEach(src=>{
    let img=document.createElement("img");
    img.src=src;
    r.appendChild(img);
  });
}

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
</script>
</body>
</html>
