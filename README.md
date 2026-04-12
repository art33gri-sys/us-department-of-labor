<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>GOV SYSTEM v6 ULTIMATE</title>

<style>
body{
margin:0;
background:#0b0e14;
color:white;
font-family:Segoe UI;
}

header{
background:#111827;
padding:20px;
text-align:center;
font-size:24px;
}

.container{
max-width:1000px;
margin:auto;
padding:20px;
}

.card{
background:#111827;
padding:15px;
margin-bottom:10px;
border-radius:10px;
border:1px solid #1f2937;
}

input,textarea,select,button{
width:100%;
padding:10px;
margin-top:8px;
background:#020617;
border:1px solid #333;
color:white;
}

button{
background:#2563eb;
cursor:pointer;
}

.hidden{display:none}

.public{
border-left:3px solid #22c55e;
background:#0a1a0f;
padding:10px;
margin-top:10px;
}

small{color:#9ca3af}

.tabs{
display:flex;
gap:10px;
margin-bottom:15px;
}

.tab{
flex:1;
padding:10px;
text-align:center;
background:#111827;
cursor:pointer;
}

.active{background:#2563eb}
</style>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js"></script>

</head>

<body>

<header>GOV SYSTEM v6 ULTIMATE</header>

<div class="container">

<!-- TABS -->
<div class="tabs">
<div class="tab active" onclick="showTab('public')">🌐 Публичные отчёты</div>
<div class="tab" onclick="showTab('admin')">🔐 Админ</div>
</div>

<!-- PUBLIC -->
<div id="public">

<h3>📢 Открытые отчёты</h3>
<div id="publicReports"></div>

</div>

<!-- ADMIN -->
<div id="admin" class="hidden">

<div class="card">

<h3>Вход</h3>

<input id="email" placeholder="Email">
<input id="password" type="password" placeholder="Пароль">

<select id="role">
<option>viewer</option>
<option>deputy</option>
<option>minister</option>
<option>admin</option>
</select>

<button onclick="login()">Войти</button>

</div>

<div class="card hidden" id="panel">

<h3>➕ Создать отчет (ПУБЛИЧНЫЙ)</h3>

<input id="title" placeholder="Название">
<textarea id="text" placeholder="Текст"></textarea>

<button onclick="addReport()">Опубликовать</button>

</div>

<h3>📊 Все отчёты</h3>
<div id="allReports"></div>

</div>

</div>

<script>

// FIREBASE CONFIG (ВСТАВЬ СВОЙ)
const firebaseConfig = {
 apiKey:"YOUR_KEY",
 authDomain:"YOUR_DOMAIN",
 projectId:"YOUR_ID",
 storageBucket:"YOUR_BUCKET",
 messagingSenderId:"YOUR_ID",
 appId:"YOUR_APP"
};

firebase.initializeApp(firebaseConfig);

const auth=firebase.auth();
const db=firebase.firestore();

// TABS
function showTab(tab){

document.getElementById("public").classList.add("hidden");
document.getElementById("admin").classList.add("hidden");

document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));

if(tab==="public"){
document.getElementById("public").classList.remove("hidden");
document.querySelectorAll(".tab")[0].classList.add("active");
loadPublic();
}

if(tab==="admin"){
document.getElementById("admin").classList.remove("hidden");
document.querySelectorAll(".tab")[1].classList.add("active");
}

}

// LOGIN
function login(){

let e=document.getElementById("email").value;
let p=document.getElementById("password").value;
let r=document.getElementById("role").value;

auth.signInWithEmailAndPassword(e,p)
.then(()=>{
document.getElementById("panel").classList.remove("hidden");
loadAll();
log("login:"+r);
})
.catch(()=>alert("Ошибка входа"));

}

// ADD REPORT (PUBLIC)
function addReport(){

db.collection("reports").add({
title:document.getElementById("title").value,
text:document.getElementById("text").value,
time:new Date().toISOString(),
public:true
});

log("add_public_report");

}

// LOAD PUBLIC
function loadPublic(){

db.collection("reports").onSnapshot(snap=>{

let html="";

snap.forEach(doc=>{
let d=doc.data();

if(d.public){

html+=`
<div class="public">
<b>${d.title}</b><br>
${d.text}<br>
<small>${d.time}</small>
</div>
`;

}

});

document.getElementById("publicReports").innerHTML=html;

});

}

// LOAD ALL (ADMIN)
function loadAll(){

db.collection("reports").onSnapshot(snap=>{

let html="";

snap.forEach(doc=>{
let d=doc.data();

html+=`
<div class="card">
<b>${d.title}</b><br>
${d.text}<br>
<small>${d.time}</small>
</div>
`;

});

document.getElementById("allReports").innerHTML=html;

});

}

// LOG
function log(a){

db.collection("logs").add({
action:a,
time:new Date().toISOString()
});

}

// AUTO LOAD PUBLIC
loadPublic();

</script>

</body>
</html>
