<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SAI Admin Panel</title>
<style>
body { font-family:'Segoe UI',sans-serif; background:#f4f7fb; color:#333; margin:0; }
header { background:#243b55; color:white; text-align:center; padding:20px; font-size:1.8em; }
.container { width:90%; max-width:900px; margin:30px auto; background:white; padding:25px; border-radius:10px; box-shadow:0 4px 12px rgba(0,0,0,0.1);}
h2{text-align:center;color:#243b55;}
table{width:100%; border-collapse:collapse; text-align:center; margin-top:20px;}
th,td{padding:12px; border-bottom:1px solid #ddd;}
th{background:#e9eef4;}
tr:hover{background:#f8faff;}
.paid{color:green;font-weight:bold;}
.not-paid{color:red;font-weight:bold;}
button{background:#007bff;color:white;border:none;padding:8px 16px;border-radius:8px;cursor:pointer;transition:0.3s;}
button:hover{background:#0056b3;}
input,select{padding:10px;margin:5px;border-radius:5px;border:1px solid #ccc;}
.form{display:flex;justify-content:center;gap:10px;flex-wrap:wrap;}
.meet-box {width:100%; margin-top:20px;}
iframe { width:100%; height:400px; border:none; border-radius:10px; }
</style>
</head>
<body>
<header>SAI Admin Panel</header>
<div class="container">
<h2>Manage Students</h2>
<div class="form">
<input type="text" id="studentName" placeholder="Student Name">
<select id="paymentStatus"><option value="Paid">Paid</option><option value="Not Paid">Not Paid</option></select>
<button onclick="updateStudent()">Update</button>
</div>

<table>
<thead><tr><th>Name</th><th>Login Code</th><th>Status</th><th>Demo Date</th></tr></thead>
<tbody id="studentTable"></tbody>
</table>

<div class="meet-box">
<h3>📹 SAI Online Meet</h3>
<iframe src="https://meet.jit.si/SAI2025MEET" allow="camera; microphone; fullscreen"></iframe>
<p>Phone Join: <a href="tel:+911234567896">+91 1234567896</a></p>
</div>
</div>

<script>
let students = JSON.parse(localStorage.getItem("sai_students")) || [];

function renderTable(){
  const tbody = document.getElementById("studentTable");
  tbody.innerHTML="";
  students.forEach(s=>{
    tbody.innerHTML+=`<tr>
      <td>${s.name}</td>
      <td>${s.code}</td>
      <td class="${s.status==='Paid'?'paid':'not-paid'}">${s.status}</td>
      <td>${s.date}</td>
    </tr>`;
  });
}

function updateStudent(){
  const name=document.getElementById("studentName").value.trim();
  const status=document.getElementById("paymentStatus").value;
  if(!name){alert("Enter name"); return;}
  let found=false;
  students.forEach(s=>{if(s.name===name){s.status=status; found=true;}});
  if(!found){alert("Student not found"); return;}
  localStorage.setItem("sai_students",JSON.stringify(students));
  renderTable();
}

renderTable();
</script>
</body>
</html>
