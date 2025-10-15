
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
table{width:100%;border-collapse:collapse;margin-top:10px;}
th,td{border:1px solid #ccc;padding:8px;text-align:center;}
iframe{width:100%;height:400px;border:none;border-radius:10px;margin-top:10px;}
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
<h2>SAI Meet</h2>
<iframe src="https://meet.jit.si/SAI2025MEET" allow="camera; microphone; fullscreen"></iframe>
</section>

<section>
<h2>SAI AI Usage Logs</h2>
<div id="aiLogs"></div>
</section>

<script>
// --- Load Demo Requests ---
function loadDemos(){
  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  let html = "<table><tr><th>Name</th><th>Date</th><th>Code</th><th>Paid</th><th>Action</th></tr>";
  demoRequests.forEach((r,index)=>{
    if(r.paid===undefined) r.paid=false;
    html += `<tr>
      <td>${r.name}</td>
      <td>${r.date}</td>
      <td>${r.code || ''}</td>
      <td>${r.paid ? '✅' : '❌'}</td>
      <td>
        <button onclick="togglePaid(${index})">Toggle Paid</button>
        <button onclick="deleteStudent(${index})">Delete</button>
      </td>
    </tr>`;
  });
  html += "</table>";
  document.getElementById('demoList').innerHTML = html;
}

// --- Toggle Paid Status ---
function togglePaid(index){
  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  demoRequests[index].paid = !demoRequests[index].paid;
  localStorage.setItem('demoRequests', JSON.stringify(demoRequests));
  loadDemos();
}

// --- Delete Student ---
function deleteStudent(index){
  if(!confirm("Are you sure you want to remove this student?")) return;
  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  demoRequests.splice(index,1);
  localStorage.setItem('demoRequests', JSON.stringify(demoRequests));
  loadDemos();
}

// --- Assign Code ---
function assignCode(){
  const name = document.getElementById('assignName').value.trim();
  const code = document.getElementById('assignCode').value.trim();
  if(!name || !code){ alert("Enter both name and code"); return; }

  let studentCodes = JSON.parse(localStorage.getItem('studentCodes')||'{}');
  studentCodes[name] = code;

  // Also update demoRequests with code
  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  let student = demoRequests.find(s=>s.name===name);
  if(student) student.code = code;
  localStorage.setItem('demoRequests', JSON.stringify(demoRequests));

  localStorage.setItem('studentCodes', JSON.stringify(studentCodes));
  alert(`Assigned code ${code} to ${name}`);
  loadDemos();
}

// --- Load AI Logs ---
function loadAILogs(){
  let logs = JSON.parse(localStorage.getItem('aiLogs')||'[]');
  let html = "<ul>";
  logs.slice(-10).forEach(l=>{
    html += `<li><b>${l.name}</b>: ${l.question} <i>${l.time}</i></li>`;
  });
  html += "</ul>";
  document.getElementById('aiLogs').innerHTML = html;
}

// --- Initial Load ---
loadDemos();
loadAILogs();
setInterval(loadAILogs,3000);
</script>

</body>
</html>
