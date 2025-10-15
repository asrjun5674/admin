
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SAI Admin Panel</title>
<style>
body{font-family:Arial,sans-serif;margin:0;background:#eef2f3;}
header{background:#1ABC9C;color:white;padding:15px;text-align:center;font-size:24px;}
section{padding:20px;}
button{cursor:pointer;background:#16A085;color:white;border:none;padding:8px 15px;border-radius:5px;margin:2px;}
input{padding:5px;margin:5px;}
</style>
</head>
<body>

<header>SAI Admin Panel</header>

<section>
<h2>Demo Class Requests</h2>
<div id="demoList"></div>
</section>

<section>
<h2>Assign Student Code</h2>
<input type="text" id="assignName" placeholder="Enter student name">
<input type="text" id="assignCode" placeholder="Enter unique code">
<button onclick="assignCode()">Assign Code</button>
</section>

<section>
<h2>All Students (Manage)</h2>
<div id="studentList"></div>
</section>

<section>
<h2>SAI AI Usage Logs</h2>
<div id="aiLogs"></div>
</section>

<section>
<h2>SAI Meet</h2>
<iframe src="https://meet.jit.si/SAI2025MEET" width="100%" height="400px" allow="camera; microphone; fullscreen"></iframe>
</section>

<script>
function loadDemos(){
  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  let html = "<ul>";
  demoRequests.forEach((r,i)=>{
    html += `<li>${r.name} - ${r.date} - ${r.approved?"✅ Approved":"⏳ Pending"} 
    <button onclick="approveDemo(${i})">Approve</button>
    <button onclick="rejectDemo(${i})">Reject</button></li>`;
  });
  html += "</ul>";
  document.getElementById('demoList').innerHTML = html;
}

function approveDemo(i){
  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  demoRequests[i].approved = true;
  localStorage.setItem('demoRequests', JSON.stringify(demoRequests));
  alert(demoRequests[i].name + " approved for login.");
  loadDemos();
  loadStudents();
}

function rejectDemo(i){
  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  demoRequests.splice(i,1);
  localStorage.setItem('demoRequests', JSON.stringify(demoRequests));
  loadDemos();
}

function assignCode(){
  const name = document.getElementById('assignName').value.trim();
  const code = document.getElementById('assignCode').value.trim();
  if(!name || !code){ alert("Enter both name and code"); return; }

  let studentCodes = JSON.parse(localStorage.getItem('studentCodes')||'{}');
  studentCodes[name] = code;
  localStorage.setItem('studentCodes', JSON.stringify(studentCodes));

  alert(`Assigned code ${code} to ${name}`);
  loadStudents();
}

function loadStudents(){
  let studentCodes = JSON.parse(localStorage.getItem('studentCodes')||'{}');
  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  let html = "<ul>";
  Object.entries(studentCodes).forEach(([name,code])=>{
    const student = demoRequests.find(d=>d.name===name);
    const paid = student?.paid?"💰 Paid":"❌ Not Paid";
    html += `<li><b>${name}</b> (${code}) - ${paid}
    <button onclick="togglePaid('${name}')">${student?.paid?"Mark Unpaid":"Mark Paid"}</button>
    <button onclick="deleteStudent('${name}')">Delete</button></li>`;
  });
  html += "</ul>";
  document.getElementById('studentList').innerHTML = html;
}

function togglePaid(name){
  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  const student = demoRequests.find(d=>d.name===name);
  if(student){
    student.paid = !student.paid;
    localStorage.setItem('demoRequests', JSON.stringify(demoRequests));
    loadStudents();
  }
}

function deleteStudent(name){
  let studentCodes = JSON.parse(localStorage.getItem('studentCodes')||'{}');
  delete studentCodes[name];
  localStorage.setItem('studentCodes', JSON.stringify(studentCodes));

  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  demoRequests = demoRequests.filter(d=>d.name!==name);
  localStorage.setItem('demoRequests', JSON.stringify(demoRequests));

  loadStudents();
}

function loadAILogs(){
  let logs = JSON.parse(localStorage.getItem('aiLogs')||'[]');
  let html = "<ul>";
  logs.slice(-10).forEach(l=>{
    html += `<li><b>${l.name}</b>: ${l.question} <i>${l.time}</i></li>`;
  });
  html += "</ul>";
  document.getElementById('aiLogs').innerHTML = html;
}

loadDemos();
loadStudents();
loadAILogs();
setInterval(loadAILogs,3000);
</script>
</body>
</html>
