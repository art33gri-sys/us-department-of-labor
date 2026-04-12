<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>US Department of Labor</title>

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

input,textarea,button{
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
</style>

</head>

<body>

<header>US Department of Labor</header>

<div class="container">

<!-- PUBLIC -->
<div class="card">
<h3>📢 Публичные отчёты</h3>
<div id="public"></div>
</div>

<!-- LOGIN -->
<div class="card">
<h3>🔐 Админ вход</h3>

<input id="email" placeholder="email">
<input id="pass" type="password" placeholder="password">

<button onclick="login()">Войти</button>
</div>

<!-- ADMIN PANEL -->
<div class="card hidden" id="panel">

<h3>➕ Создать отчёт</h3>

<input id="title" placeholder="Название">
<textarea id="text" placeholder="Текст"></textarea>

<button onclick="addReport()">Добавить</button>

</div>

</div>

<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { 
getAuth,
signInWithEmailAndPassword 
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";

import {
getFirestore,
collection,
addDoc,
onSnapshot,
query,
orderBy
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

// 🔥 ВСТАВЬ СВОЙ CONFIG С FIREBASE
const firebaseConfig = {
apiKey: "YOUR_KEY",
authDomain: "YOUR_DOMAIN",
projectId: "YOUR_ID",
storageBucket: "YOUR_BUCKET",
messagingSenderId: "YOUR_ID",
appId: "YOUR_APP"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);

// LOGIN (ИСПРАВЛЕНО)
window.login = async function(){

let email = document.getElementById("email").value;
let pass = document.getElementById("pass").value;

try{
await signInWithEmailAndPassword(auth,email,pass);

document.getElementById("panel").classList.remove("hidden");

alert("Вход выполнен");
loadReports();

}catch(e){
alert("Ошибка входа");
}

}

// ADD REPORT
window.addReport = async function(){

await addDoc(collection(db,"reports"),{
title:document.getElementById("title").value,
text:document.getElementById("text").value,
time:new Date()
});

loadReports();

}

// LOAD REPORTS
function loadReports(){

const q = query(collection(db,"reports"));

onSnapshot(q,(snap)=>{

let html="";

snap.forEach(doc=>{

let d=doc.data();

html+=`
<div class="public">
<b>${d.title}</b><br>
${d.text}
</div>
`;

});

document.getElementById("public").innerHTML=html;

});

}

loadReports();

</script>

</body>
</html>
