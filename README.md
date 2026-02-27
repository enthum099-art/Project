<!DOCTYPE html>
<html>
<head>
<title>School Communication System</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
body{margin:0;font-family:Arial;background:#eef2f7;}
header{background:#1f3c88;color:white;padding:15px;text-align:center;}
nav{background:#162447;padding:10px;text-align:center;display:none;}
nav button{background:none;border:none;color:white;margin:0 8px;cursor:pointer;font-weight:bold;}
nav button:hover{color:#ffd369;}
.section{display:none;padding:30px;}
.active{display:block;}
.card{background:white;padding:20px;border-radius:8px;box-shadow:0 0 10px rgba(0,0,0,0.1);margin-bottom:20px;}
input,select{width:100%;padding:10px;margin:8px 0;}
button.main{background:#1f3c88;color:white;border:none;padding:10px;cursor:pointer;width:100%;}
button.main:hover{background:#162447;}
table{width:100%;border-collapse:collapse;}
table,th,td{border:1px solid #ccc;}
th,td{padding:10px;text-align:center;}
.video-box{background:black;color:white;height:250px;display:flex;align-items:center;justify-content:center;border-radius:8px;}
.video-controls{text-align:center;margin-top:10px;}
.video-controls button{margin:5px;padding:8px 12px;}
footer{background:#1f3c88;color:white;text-align:center;padding:10px;}
</style>
</head>

<body>

<header>
<h2>School Communication System</h2>
<p>2nd Year College Project</p>
</header>

<!-- LOGIN -->
<div id="login" class="section active">
<div class="card">
<h3>Login</h3>
<select id="role">
<option value="">Select Role</option>
<option>Teacher</option>
<option>Student</option>
<option>Parent</option>
<option>Admin</option>
</select>
<input type="text" id="username" placeholder="Enter Username">
<input type="password" id="password" placeholder="Password">
<button class="main" onclick="login()">Login</button>
<p id="error" style="color:red;"></p>
</div>
</div>

<!-- NAVIGATION -->
<nav id="navbar">
<button onclick="showSection('dashboard')">Dashboard</button>
<button onclick="showSection('attendance')">Attendance</button>
<button onclick="showSection('homework')">Homework</button>
<button onclick="showSection('chat')">Chat</button>
<button onclick="showSection('video')">Video Call</button>
<button onclick="showSection('manageStudents')" id="manageBtn">Manage Students</button>
<button onclick="logout()">Logout</button>
</nav>

<!-- DASHBOARD -->
<div id="dashboard" class="section">
<div class="card">
<h3 id="welcomeMsg"></h3>
<p>Welcome to the School Communication System.</p>
</div>
</div>

<!-- ATTENDANCE -->
<div id="attendance" class="section">
<div class="card">
<h3>Attendance</h3>

<label>Select Date:</label>
<input type="date" id="attendanceDate">
<p>Current Time: <span id="currentTime"></span></p>

<table id="attendanceTable"></table>

<br>
<button class="main" id="saveBtn" onclick="saveAttendance()">Save Attendance</button>
<p id="attendanceResult"></p>

</div>
</div>

<!-- HOMEWORK -->
<div id="homework" class="section">
<div class="card">
<h3>Homework</h3>
<ul>
<li>Math: Chapter 5 Exercise</li>
<li>Science: Photosynthesis Notes</li>
<li>English: Essay Writing</li>
</ul>
</div>
</div>

<!-- CHAT -->
<div id="chat" class="section">
<div class="card">
<h3>Chat</h3>
<p>Parent-Teacher Communication Module.</p>
</div>
</div>

<!-- VIDEO -->
<div id="video" class="section">
<div class="card">
<h3>Video Call</h3>
<div class="video-box" id="videoScreen">Live Video Session</div>
<div class="video-controls">
<button onclick="toggleCamera()">Camera</button>
<button onclick="toggleMute()">Mute</button>
<button onclick="endCall()" style="background:red;color:white;">End</button>
</div>
</div>
</div>

<!-- ADMIN SECTION -->
<div id="manageStudents" class="section">
<div class="card">
<h3>Manage Students (Admin Only)</h3>
<input type="text" id="newStudentName" placeholder="Enter Student Name">
<button class="main" onclick="addStudent()">Add Student</button>
<p id="studentMsg"></p>
</div>
</div>

<footer>
© 2026 School Communication System
</footer>

<script>
let currentRole="";
let currentUser="";
let students=["Rahul","Priya","Aman"];

function login(){
let role=document.getElementById("role").value;
let user=document.getElementById("username").value;
let pass=document.getElementById("password").value;

if(role==""||user==""||pass==""){
document.getElementById("error").innerText="Fill all fields!";
return;
}

currentRole=role;
currentUser=user;

document.getElementById("login").classList.remove("active");
document.getElementById("navbar").style.display="block";
document.getElementById("welcomeMsg").innerText="Welcome "+role+" : "+user;

if(currentRole!=="Admin"){
document.getElementById("manageBtn").style.display="none";
}else{
document.getElementById("manageBtn").style.display="inline-block";
}

renderStudents();
applyRolePermissions();
showSection("dashboard");
}

function renderStudents(){
let table=document.getElementById("attendanceTable");
table.innerHTML=`
<tr>
<th>Student Name</th>
<th>Present</th>
<th>Absent</th>
</tr>
`;

students.forEach(name=>{
let row=document.createElement("tr");
row.setAttribute("data-student",name);
row.innerHTML=`
<td>${name}</td>
<td><input type="radio" name="${name}" value="present"></td>
<td><input type="radio" name="${name}" value="absent"></td>
`;
table.appendChild(row);
});
}

function applyRolePermissions(){
let rows=document.querySelectorAll("#attendanceTable tr[data-student]");
let radios=document.querySelectorAll("#attendance input[type='radio']");
let saveBtn=document.getElementById("saveBtn");

if(currentRole==="Teacher"){
rows.forEach(r=>r.style.display="table-row");
radios.forEach(r=>r.disabled=false);
saveBtn.style.display="block";
}
else if(currentRole==="Student"){
rows.forEach(row=>{
row.style.display=(row.dataset.student===currentUser)?"table-row":"none";
});
radios.forEach(r=>r.disabled=true);
saveBtn.style.display="none";
}
else if(currentRole==="Parent"){
rows.forEach(row=>{
row.style.display=(row.dataset.student==="Rahul")?"table-row":"none";
});
radios.forEach(r=>r.disabled=true);
saveBtn.style.display="none";
}
else if(currentRole==="Admin"){
rows.forEach(r=>r.style.display="table-row");
radios.forEach(r=>r.disabled=true);
saveBtn.style.display="none";
}
}

function addStudent(){
if(currentRole!=="Admin"){
alert("Only Admin can add students!");
return;
}
let name=document.getElementById("newStudentName").value.trim();
if(name=="" || students.includes(name)){
alert("Invalid or duplicate name!");
return;
}
students.push(name);
renderStudents();
applyRolePermissions();
document.getElementById("studentMsg").innerText="Student added successfully!";
document.getElementById("newStudentName").value="";
}

function saveAttendance(){
let date=document.getElementById("attendanceDate").value;
if(date==""){alert("Select date!");return;}
document.getElementById("attendanceResult").innerText="Attendance saved for "+date;
}

function showSection(id){
document.querySelectorAll(".section").forEach(s=>s.classList.remove("active"));
document.getElementById(id).classList.add("active");
}

function logout(){location.reload();}

document.addEventListener("DOMContentLoaded",function(){
document.getElementById("attendanceDate").value=new Date().toISOString().split('T')[0];
});
setInterval(function(){
document.getElementById("currentTime").innerText=new Date().toLocaleTimeString();
},1000);

function toggleCamera(){
let screen=document.getElementById("videoScreen");
screen.innerText=(screen.innerText==="Live Video Session")?"Camera Off":"Live Video Session";
}
function toggleMute(){
alert("Microphone Toggled");
}
function endCall(){
alert("Call Ended");
showSection("dashboard");
}
</script>

</body>
</html>
