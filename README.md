<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>GOV SYSTEM v5 FINAL</title>

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
font-weight:bold;
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

.report{
padding:10px;
border-left:3px solid #2563eb;
margin-top:10px;
background:#0a0f1a;
}

small{color:#9ca3af}
</style>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js"></script>

</head>

<body>

<header>GOV SYSTEM v5 FINAL</header>

<div class="container">

<!-- LOGIN -->
<div class="card" id="loginBox">

<h3>Вход</h3>

<input id="email" placeholder="Email">
<input id="password" type="password" placeholder="Пароль">

<select id="role">
<option value="viewer">Viewer</option>
<option value="deputy">Deputy</option>
<option value="minister">Minister</option>
<option value="admin">Admin</option>
</select>

<button onclick="login()">Войти</button>

</div>

<!-- PANEL -->
<div class="card hidden" id="panel">

<h3>➕ Создать отчет</h3>

<input id="title" placeholder="Название">
<textarea id="text" placeholder="Текст"></textarea>

<button onclick="addReport()">Добавить</button>

</div>

<div id="reports"></div>

</div>

<script>

// 🔥 FIREBASE CONFIG (ВСТАВЬ СВОЙ ИЗ FIREBASE)
const firebaseConfig = {
  apiKey: "YOUR_KEY",
  authDomain: "YOUR_DOMAIN",
  projectId: "YOUR_ID",
  storageBucket: "YOUR_BUCKET",
  messagingSenderId: "YOUR_SENDER",
  appId: "YOUR_APP"
};

firebase.initializeApp(firebaseConfig);

const auth = firebase.auth();
const db = firebase.firestore();

// LOGIN
function login(){

let email=document.getElementById("email").value;
let pass=document.getElementById("password").value;
let role=document.getElementById("role").value;

auth.signInWithEmailAndPassword(email,pass)
.then(()=>{

document.getElementById("panel").classList.remove("hidden");

log("login: "+role);

loadReports();

})
.catch(()=>alert("Ошибка входа"));

}

// ADD REPORT
function addReport(){

db.collection("reports").add({
title:document.getElementById("title").value,
text:document.getElementById("text").value,
time:new Date().toISOString()
});

log("add_report");

}

// LOAD REPORTS
function loadReports(){

db.collection("reports").onSnapshot(snap=>{

let html="";

snap.forEach(doc=>{

let d=doc.data();

html+=`
<div class="report">
<b>${d.title}</b><br>
${d.text}<br>
<small>${d.time}</small>
</div>
`;

});

document.getElementById("reports").innerHTML=html;

});

}

// LOG SYSTEM
function log(action){

db.collection("logs").add({
action:action,
time:new Date().toISOString()
});

}

</script>

</body>
</html>
