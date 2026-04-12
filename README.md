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
padding:25px;
text-align:center;
font-size:28px;
font-weight:bold;
}

.sub{
font-size:14px;
color:#9ca3af;
}

nav{
background:#020617;
padding:15px;
text-align:center;
position:sticky;
top:0;
}

nav a{
color:#9ca3af;
margin:15px;
text-decoration:none;
}

nav a:hover{
color:white;
}

.container{
max-width:1200px;
margin:auto;
padding:25px;
}

.grid{
display:grid;
grid-template-columns:repeat(4,1fr);
gap:20px;
}

.card{
background:#111827;
padding:20px;
border-radius:10px;
border:1px solid #1f2937;
margin-bottom:20px;
}

.stat{
font-size:30px;
text-align:center;
font-weight:bold;
}

input,textarea{
width:100%;
padding:10px;
margin-top:10px;
background:#020617;
border:1px solid #1f2937;
color:white;
}

button{
margin-top:10px;
padding:10px;
background:#2563eb;
border:none;
color:white;
cursor:pointer;
width:100%;
}

small{color:#9ca3af}

.hidden{display:none}
</style>

</head>

<body>

<header>
US Department of Labor
<div class="sub">Secretary of Labor Portal</div>
</header>

<nav>
<a href="#home">Главная</a>
<a href="#reports">Отчеты</a>
<a href="#staff">Состав</a>
<a href="#admin">Админ</a>
</nav>

<div class="container" id="home">

<div class="grid">

<div class="card"><div class="stat">26</div>Сотрудников</div>
<div class="card"><div class="stat">315</div>Лицензий</div>
<div class="card"><div class="stat">40</div>Экзаменов</div>
<div class="card"><div class="stat">33</div>Лекций</div>

</div>

</div>

<!-- REPORTS -->
<div class="container" id="reports">

<div class="card">
<h2>Добавить отчет</h2>

<input id="title" placeholder="Название">
<textarea id="text" placeholder="Текст отчета"></textarea>

<button onclick="addReport()">Добавить отчет</button>

</div>

<div id="reportList"></div>

</div>

<!-- STAFF -->
<div class="container" id="staff">

<div class="card">
<h2>Состав Министерства</h2>

<p>Министр — Artur Drovic</p>
<p>Заместитель — Miy Li</p>
<p>Заместитель — Elmer Moroz</p>

</div>

</div>

<!-- ADMIN -->
<div class="container" id="admin">

<div class="card">
<h2>Админ вход</h2>

<input id="login" placeholder="логин">
<input id="pass" type="password" placeholder="пароль">

<button onclick="login()">Войти</button>

</div>

<div class="card hidden" id="panel">

<h2>Админ панель</h2>

<p>✔ Доступ открыт</p>

</div>

</div>

<script>

// LOGIN
function login(){

let l=document.getElementById("login").value;
let p=document.getElementById("pass").value;

if(
(l=="admin" && p=="labor2026") ||
(l=="minister" && p=="labor2026") ||
(l=="deputy" && p=="labor2026")
){
document.getElementById("panel").classList.remove("hidden");
alert("Доступ разрешен");
}else{
alert("Неверный логин");
}

}

// REPORT SYSTEM
function addReport(){

let title=document.getElementById("title").value;
let text=document.getElementById("text").value;

let data=JSON.parse(localStorage.getItem("reports")||"[]");

data.push({
title:title,
text:text,
date:new Date().toLocaleDateString()
});

localStorage.setItem("reports",JSON.stringify(data));

loadReports();

}

function loadReports(){

let list=document.getElementById("reportList");
if(!list) return;

let data=JSON.parse(localStorage.getItem("reports")||"[]");

list.innerHTML="";

data.reverse().forEach(r=>{
list.innerHTML+=`
<div class="card">
<h3>${r.title}</h3>
<p>${r.text}</p>
<small>${r.date}</small>
</div>
`;
});

}

loadReports();

</script>

</body>
</html>
