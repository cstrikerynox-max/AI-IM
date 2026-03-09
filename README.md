<!DOCTYPE html>
<html lang="az">
<head>
<meta charset="UTF-8">
<title>Cute AI Generator</title>

<style>

body{
margin:0;
font-family:Arial;
background:linear-gradient(120deg,#4b0082,#8a2be2);
color:white;
overflow:hidden;
}

#app{
display:flex;
height:100vh;
}

.sidebar{
width:230px;
background:rgba(0,0,0,0.35);
backdrop-filter:blur(10px);
padding:20px;
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
padding:12px;
color:white;
border-radius:10px;
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
}

.title{
font-size:28px;
margin-bottom:20px;
}

.panel{
background:rgba(255,255,255,0.1);
border-radius:15px;
padding:20px;
margin-bottom:20px;
animation:fade .7s;
}

@keyframes fade{
from{opacity:0;transform:translateY(20px)}
to{opacity:1;transform:translateY(0)}
}

input,textarea{
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
border-radius:10px;
transition:.4s;
}

.images img:hover{
transform:scale(1.05);
}

.chatbox{
height:300px;
overflow:auto;
background:rgba(0,0,0,0.3);
padding:10px;
border-radius:10px;
margin-bottom:10px;
}

.msg{
margin:6px 0;
}

</style>
</head>

<body>

<div id="app">

<div class="sidebar">

<h2>Cute AI</h2>

<button onclick="show('profile')">Profile</button>
<button onclick="show('create')">Create</button>
<button onclick="show('gallery')">Gallery</button>
<button onclick="show('chat')">Chat</button>

</div>

<div class="main" id="content">

<div class="title">Welcome</div>

<div class="panel">
Cute Anime AI Generator
</div>

</div>

</div>

<script>

function show(page){

if(page==="profile"){
content.innerHTML=`
<div class="title">Profile</div>

<div class="panel">

Login / Sign Up

<input placeholder="Username">
<input placeholder="Password" type="password">

<button class="generate">Login</button>
<button class="generate">Sign Up</button>

</div>
`
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

</div>
`
}

if(page==="gallery"){
content.innerHTML=`
<div class="title">Gallery</div>

<div class="panel">
<div class="images">

<img src="https://picsum.photos/200?1">
<img src="https://picsum.photos/200?2">
<img src="https://picsum.photos/200?3">
<img src="https://picsum.photos/200?4">
<img src="https://picsum.photos/200?5">
<img src="https://picsum.photos/200?6">
<img src="https://picsum.photos/200?7">
<img src="https://picsum.photos/200?8">
<img src="https://picsum.photos/200?9">
<img src="https://picsum.photos/200?10">

</div>
</div>
`
}

if(page==="chat"){
content.innerHTML=`
<div class="title">AI Chat</div>

<div class="panel">

<div class="chatbox" id="chatbox"></div>

<input id="msg">
<button class="generate" onclick="send()">Send</button>

</div>
`
}

}

function generate(){

let p=document.getElementById("prompt").value
let r=document.getElementById("result")

r.innerHTML=""

for(let i=0;i<10;i++){

let img=document.createElement("img")
img.src="https://picsum.photos/300?random="+Math.random()

r.appendChild(img)

}

}

function send(){

let m=document.getElementById("msg").value
let box=document.getElementById("chatbox")

box.innerHTML+=`<div class='msg'><b>Sen:</b> ${m}</div>`

let reply="Bunu maraqlı sual hesab edirəm."

if(m.includes("salam")) reply="Salam! Necə kömək edə bilərəm?"
if(m.includes("anime")) reply="Anime şəkilləri create bölməsindən yarada bilərsən."
if(m.includes("AI")) reply="AI şəkil generatoru prompt ilə işləyir."

box.innerHTML+=`<div class='msg'><b>AI:</b> ${reply}</div>`

box.scrollTop=box.scrollHeight

}

</script>

</body>
</html>
