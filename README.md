<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>US Department of Labor - GOV v2</title>

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
font-size:26px;
font-weight:bold;
}

nav{
background:#020617;
padding:12px;
text-align:center;
position:sticky;
top:0;
}

nav a{
color:#9ca3af;
margin:12px;
text-decoration:none;
}

.container{
max-width:1100px;
margin:auto;
padding:25px;
}

.card{
background:#111827;
padding:18px;
border-radius:10px;
margin-bottom:15px;
border:1px solid #1f2937;
}

input,textarea,select{
width:100%;
padding:10px;
margin-top:8px;
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
border-radius:6px;
}

.report{
padding:10px;
border-left:3px solid #2563eb;
margin-top:10px;
background:#0a0f1a;
}

small{color:#9ca3af}

.hidden{display:none}

.grid{
display:grid;
grid-template-columns:repeat(3,1fr);
gap:15px;
}
</style>

</head>

<body>

<header>
US Department of Labor
<div style="font-size:12px;color:#9ca3af">REAL GOV SYSTEM v2</div>
</header>

<nav>
<a href="#home">Главная</a>
<a href="#reports">Отчёты</a>
<a href="#admin">Админ</a>
</nav>

<div class="container">

<!-- HOME -->
<div id="home" class="grid">

<div class="card"><b>26</b><br>Сотрудников</div>
<div class="card"><b>315</b><br>Лицензий</div>
<div class="card"><b>40</b><br>Экзаменов</div>

</div>

<!-- REPORTS -->
<div id="reports" class="card">

<h2>📊 Отчёты</h2>

<div id="list"></div>

</div>

<!-- ADMIN -->
<div id="admin" class="card">

<h2>🔐 Админ вход</h2>

<input id="login" placeholder="логин">
<input id="pass" type="password" placeholder="пароль">

<select id="role">
<option value="viewer">viewer</option>
<option value="admin">admin</option>
<option value="minister">minister</option>
<option value="deputy">deputy</option>
</select>

<button onclick="login()">Войти</button>

<div id="panel" class="hidden">

<h3>➕ Добавить отчёт</h3>

<input id="title" placeholder="Название">
<textarea id="text" placeholder="Текст"></textarea>

<button onclick="addReport()">Сохранить</button>

<button onclick="clearAll()" style="background:red">Очистить всё</button>

</div>

</div>

</div>

<script>

// LOGIN
function login(){
let l=document.getElementById("login").value;
let p=document.getElementById("pass").value;
let r=document.getElementById("role").value;

if(p==="labor2026" && (r==="admin"||r==="minister"||r==="deputy")){
document.getElementById("panel").classList.remove("hidden");
alert("Доступ открыт: "+r);
}else{
alert("Ошибка доступа");
}
}

// ADD REPORT
function addReport(){

let title=document.getElementById("title").value;
let text=document.getElementById("text").value;

let data=JSON.parse(localStorage.getItem("gov_reports")||"[]");

data.unshift({
title,
text,
date:new Date().toLocaleString()
});

localStorage.setItem("gov_reports",JSON.stringify(data));

loadReports();

}

// LOAD REPORTS
function loadReports(){

let list=document.getElementById("list");
let data=JSON.parse(localStorage.getItem("gov_reports")||"[]");

list.innerHTML="";

data.forEach((r,i)=>{

list.innerHTML+=`
<div class="report">
<b>${r.title}</b><br>
${r.text}<br>
<small>${r.date}</small>
<br>
<button onclick="del(${i})" style="background:red;margin-top:5px">Удалить</button>
</div>
`;

});

}

// DELETE
function del(i){

let data=JSON.parse(localStorage.getItem("gov_reports")||"[]");
data.splice(i,1);
localStorage.setItem("gov_reports",JSON.stringify(data));
loadReports();

}

// CLEAR ALL
function clearAll(){
localStorage.removeItem("gov_reports");
loadReports();
}

loadReports();

</script>

</body>
</html>
