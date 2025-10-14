
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SAI Admin Panel</title>
<style>
body{font-family:Arial,sans-serif;margin:0;background:#eef2f3;}
header{background:#1ABC9C;color:white;padding:15px;text-align:center;font-size:24px;}
section{padding:20px;}
button{cursor:pointer;background:#16A085;color:white;border:none;padding:8px 15px;border-radius:5px;}
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
<h2>SAI AI Usage Logs</h2>
<div id="aiLogs"></div>
</section>

<script>
// --- Load demo requests ---
function loadDemos(){
  let demoRequests = JSON.parse(localStorage.getItem('demoRequests')||'[]');
  let html = "<ul>";
  demoRequests.forEach(r=>{ html += `<li>${r.name} - ${r.date}</li>`; });
  html += "</ul>";
  document.getElementById('demoList').innerHTML = html;
}

// --- Assign code ---
function assignCode(){
  const name = document.getElementById('assignName').value.trim();
  const code = document.getElementById('assignCode').value.trim();
  if(!name || !code){ alert("Enter both name and code"); return; }

  let studentCodes = JSON.parse(localStorage.getItem('studentCodes')||'{}');
  studentCodes[name] = code;
  localStorage.setItem('studentCodes', JSON.stringify(studentCodes));
  alert(`Assigned code ${code} to ${name}`);
}

// --- Load AI logs ---
function loadAILogs(){
  let logs = JSON.parse(localStorage.getItem('aiLogs')||'[]');
  let html = "<ul>";
  logs.slice(-20).forEach(l=>{ html += `<li><b>${l.name}</b>: ${l.question} <i>${l.time}</i></li>`; });
  html += "</ul>";
  document.getElementById('aiLogs').innerHTML = html;
}

loadDemos();
loadAILogs();
setInterval(loadAILogs,3000);
</script>
</body>
</html>
