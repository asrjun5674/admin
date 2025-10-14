
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SAI Admin Panel</title>
<style>
body{font-family:Arial,sans-serif;background:#f7f9f9;margin:0;}
header{background:#1ABC9C;color:white;text-align:center;padding:15px;font-size:24px;}
table{width:100%;border-collapse:collapse;margin-top:10px;}
table,th,td{border:1px solid #ccc;}
th,td{padding:8px;text-align:center;}
section{padding:20px;}
button{cursor:pointer;padding:5px 10px;border:none;border-radius:5px;background:#1ABC9C;color:white;}
button:hover{background:#16A085;}
</style>
</head>
<body>

<header>SAI - Admin Panel</header>

<section>
<h2>Student Management</h2>
<table id="studentTable">
<tr><th>Name</th><th>Code</th><th>Paid</th><th>Action</th></tr>
</table>
</section>

<section>
<h2>SAI AI Logs</h2>
<div id="aiLogs" style="background:white;padding:10px;border-radius:10px;max-height:300px;overflow:auto;"></div>
</section>

<section>
<h2>SAI Meet</h2>
<iframe src="https://meet.jit.si/SAI2025MEET" width="100%" height="400px" allow="camera; microphone; fullscreen"></iframe>
</section>

<script>
function renderStudents(){
  const students = JSON.parse(localStorage.getItem("students")||"[]");
  const table = document.getElementById("studentTable");
  table.innerHTML = "<tr><th>Name</th><th>Code</th><th>Paid</th><th>Action</th></tr>";
  students.forEach((s,i)=>{
    table.innerHTML += `
      <tr>
        <td>${s.name}</td>
        <td>${s.code}</td>
        <td><input type='checkbox' ${s.paid?'checked':''} onchange='togglePaid(${i},this.checked)'></td>
        <td><button onclick='deleteStudent(${i})'>Delete</button></td>
      </tr>`;
  });
}

function togglePaid(index, paid){
  let students = JSON.parse(localStorage.getItem("students")||"[]");
  students[index].paid = paid;
  localStorage.setItem("students", JSON.stringify(students));
}

function deleteStudent(index){
  if(confirm("Delete student?")){
    let students = JSON.parse(localStorage.getItem("students")||"[]");
    students.splice(index,1);
    localStorage.setItem("students", JSON.stringify(students));
    renderStudents();
  }
}

function renderLogs(){
  const logs = JSON.parse(localStorage.getItem("aiLogs")||"[]").slice().reverse();
  const logDiv = document.getElementById("aiLogs");
  logDiv.innerHTML = "";
  logs.forEach(l=>{
    logDiv.innerHTML += `<p>${l.time} - <b>${l.name}</b>: ${l.question}</p>`;
  });
}
setInterval(renderLogs,2000);

renderStudents();
</script>
</body>
</html>
