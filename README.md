
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SAI - Admin Panel</title>
<style>
body { font-family: Arial, sans-serif; margin:0; padding:0; background:#f5f5f5;}
header { background:#1ABC9C; color:white; padding:15px; text-align:center; font-size:24px; }
nav { display:flex; justify-content:center; background:#16A085;}
nav button { padding:10px 20px; margin:5px; border:none; color:white; background:#1ABC9C; cursor:pointer; border-radius:5px;}
nav button:hover { background:#138D75;}
section { padding:20px; }
h2 { color:#16A085;}
table { width:100%; border-collapse: collapse; margin-top:10px;}
table, th, td { border:1px solid #ccc; }
th, td { padding:8px; text-align:center;}
#aiLogs, #historyContainer { max-height:300px; overflow:auto; background:white; padding:10px; border-radius:10px; box-shadow:0 0 5px rgba(0,0,0,0.1);}
input[type=text], select { padding:5px; margin-bottom:10px;}
</style>
</head>
<body>

<header>SAI - Admin Panel</header>
<nav>
  <button onclick="showSection('students')">Students</button>
  <button onclick="showSection('ai-log')">SAI AI Log</button>
  <button onclick="showSection('worksheets')">Worksheets Answers</button>
  <button onclick="showSection('meet')">SAI Meet</button>
</nav>

<section id="students">
  <h2>Student List / Demo Bookings</h2>
  <input type="text" id="searchInput" placeholder="Search student by name" onkeyup="searchStudent()" style="width:50%;">
  <table id="studentTable">
    <tr>
      <th>Name</th>
      <th>Demo Time</th>
      <th>Paid</th>
      <th>SAI Student</th>
      <th>Action</th>
    </tr>
  </table>

  <h3>View AI History</h3>
  <label>Choose Student: 
    <select id="historySelect" onchange="showStudentHistory()">
      <option value="">--Select Student--</option>
    </select>
  </label>
  <div id="historyContainer"></div>
</section>

<section id="ai-log" style="display:none;">
  <h2>SAI AI Live Usage</h2>
  <div id="aiLogs"></div>
</section>

<section id="worksheets" style="display:none;">
  <h2>Worksheets Answers</h2>
  <label>Choose Grade: 
    <select id="gradeSelect" onchange="loadSubjects()">
      <option value="">--Select Grade--</option>
      <option value="1">Grade 1</option>
      <option value="2">Grade 2</option>
      <option value="3">Grade 3</option>
      <option value="4">Grade 4</option>
      <option value="5">Grade 5</option>
      <option value="6">Grade 6</option>
      <option value="7">Grade 7</option>
      <option value="8">Grade 8</option>
      <option value="9">Grade 9</option>
      <option value="10">Grade 10</option>
    </select>
  </label>

  <label>Choose Subject: 
    <select id="subjectSelect" onchange="loadAnswers()">
      <option value="">--Select Subject--</option>
    </select>
  </label>

  <div id="answersContainer"></div>
</section>

<section id="meet" style="display:none;">
  <h2>SAI Meet</h2>
  <iframe src="https://meet.jit.si/SAI2025MEET" width="100%" height="500px" allow="camera; microphone; fullscreen"></iframe>
</section>

<script>
function showSection(sec){
  document.querySelectorAll('section').forEach(s=>s.style.display='none');
  document.getElementById(sec).style.display='block';
}

// --- Load Students ---
let students = JSON.parse(localStorage.getItem('students')||'[]');
let studentData = [];

function updateStudentData(){
  let names = students.map(s=>s.name);
  studentData = students.map(s=>({name:s.name,demoTime:s.demoTime,paid:s.paid||false,saiStudent:s.saiStudent||'No'}));
  renderStudentTable();
}

function renderStudentTable(){
  const table = document.getElementById('studentTable');
  table.innerHTML = "<tr><th>Name</th><th>Demo Time</th><th>Paid</th><th>SAI Student</th><th>Action</th></tr>";
  studentData.forEach((s, idx)=>{
    table.innerHTML += `<tr>
      <td>${s.name}</td>
      <td>${s.demoTime}</td>
      <td><input type="checkbox" ${s.paid?'checked':''} onchange="studentData[${idx}].paid=this.checked; saveStudents()"></td>
      <td>
        <select onchange="studentData[${idx}].saiStudent=this.value; saveStudents()">
          <option value="No" ${s.saiStudent==='No'?'selected':''}>No</option>
          <option value="Yes" ${s.saiStudent==='Yes'?'selected':''}>Yes</option>
        </select>
      </td>
      <td><button onclick="deleteStudent(${idx})">Delete</button></td>
    </tr>`;
  });
  populateHistorySelect();
}

function deleteStudent(idx){
  if(confirm("Delete this student?")){
    studentData.splice(idx,1);
    saveStudents();
    renderStudentTable();
  }
}

function saveStudents(){
  localStorage.setItem('students', JSON.stringify(studentData));
}

// Search
function searchStudent(){
  const input = document.getElementById('searchInput').value.toLowerCase();
  const rows = document.getElementById('studentTable').getElementsByTagName('tr');
  for(let i=1;i<rows.length;i++){
    let td = rows[i].getElementsByTagName('td')[0];
    rows[i].style.display = td.innerText.toLowerCase().includes(input)?'':'none';
  }
}

// AI Logs
function updateAILogs(){
  let aiLogs = JSON.parse(localStorage.getItem('aiLogs')||'[]');
  const logDiv = document.getElementById('aiLogs');
  logDiv.innerHTML = "";
  aiLogs.slice().reverse().forEach(log=>{
    logDiv.innerHTML += `<p>${log.time} - ${log.name}: ${log.question}</p>`;
  });
}
setInterval(updateAILogs,2000);

// History dropdown
function populateHistorySelect(){
  const sel = document.getElementById('historySelect');
  sel.innerHTML = "<option value=''>--Select Student--</option>";
  studentData.forEach(s=>sel.innerHTML += `<option value="${s.name}">${s.name}</option>`);
}

function showStudentHistory(){
  const name = document.getElementById('historySelect').value;
  const container = document.getElementById('historyContainer');
  container.innerHTML = "";
  if(!name) return;
  let aiLogs = JSON.parse(localStorage.getItem('aiLogs')||'[]');
  const logs = aiLogs.filter(l=>l.name===name);
  if(logs.length===0){ container.innerHTML = "No AI activity for this student."; return; }
  logs.forEach(l=>{ container.innerHTML += `<p>${l.time}: ${l.question}</p>`; });
}

// Worksheets (same as Student)
const subjectsPerGrade = {
  1:["English","Math","Science","Social Studies","GK"],
  2:["English","Math","Science","Social Studies","GK"],
  3:["English","Math","Science","Social Studies","GK"],
  4:["English","Math","Science","Social Studies","GK"],
  5:["English","Math","Science","Social Studies","GK"],
  6:["English","Math","Physics","Chemistry","Biology","History","Civics","Geography"],
  7:["English","Math","Physics","Chemistry","Biology","History","Civics","Geography"],
  8:["English","Math","Physics","Chemistry","Biology","History","Civics","Geography"],
  9:["English","Math","Physics","Chemistry","Biology","History","Civics","Geography"],
  10:["English","Math","Physics","Chemistry","Biology","History","Civics","Geography"]
};

const sampleAnswers = {
  "Math":["1+1=2","2+3=5","5-2=3","3*2=6","10/2=5"],
  "Physics":["Newton's law","Force=mass*acceleration","Gravity pulls objects","Motion=change in position","Speed=distance/time"],
  "Chemistry":["H2O=water","Acid releases H+","Base releases OH-","Salt=NaCl","Chemical reaction"],
  "Biology":["Cell: basic unit","Photosynthesis: sunlight to energy","Plant parts","Respiration","Habitat"],
  "English":["Noun","Verb","Sentence","Adjective","Opposite of big: small"],
  "History":["Ashoka","Democracy","Empire","Freedom fighter","Constitution"],
  "Civics":["Citizen","Government","Rights","Duties","Parliament"],
  "Geography":["Continents","Oceans","River","Mountain","Climate"],
  "Science":["Matter","Energy","Force","Light","Magnet"],
  "Social Studies":["Society","Religions","Economy","Culture","Government"],
  "GK":["President","Capital of India","Flag colors","National animal","National bird"]
};

function loadSubjects(){
  const grade = document.getElementById('gradeSelect').value;
  const subjectSelect = document.getElementById('subjectSelect');
  subjectSelect.innerHTML = "<option value=''>--Select Subject--</option>";
  if(subjectsPerGrade[grade]) subjectsPerGrade[grade].forEach(sub => {
    subjectSelect.innerHTML += `<option value="${sub}">${sub}</option>`;
  });
  document.getElementById('answersContainer').innerHTML="";
}

function loadAnswers(){
  const subject = document.getElementById('subjectSelect').value;
  const container = document.getElementById('answersContainer');
  container.innerHTML="";
  if(sampleAnswers[subject]){
    let html = "<ol>";
    sampleAnswers[subject].forEach(ans => html += `<li>${ans}</li>`);
    html += "</ol>";
    container.innerHTML = html;
  }
}

updateStudentData();
</script>
</body>
</html>

<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SAI - Admin Panel</title>
<style>
body { font-family: Arial, sans-serif; margin:0; padding:0; background:#f5f5f5;}
header { background:#1ABC9C; color:white; padding:15px; text-align:center; font-size:24px; }
nav { display:flex; justify-content:center; background:#16A085;}
nav button { padding:10px 20px; margin:5px; border:none; color:white; background:#1ABC9C; cursor:pointer; border-radius:5px;}
nav button:hover { background:#138D75;}
section { padding:20px; }
h2 { color:#16A085;}
table { width:100%; border-collapse: collapse; margin-top:10px;}
table, th, td { border:1px solid #ccc; }
th, td { padding:8px; text-align:center;}
#aiLogs, #historyContainer { max-height:300px; overflow:auto; background:white; padding:10px; border-radius:10px; box-shadow:0 0 5px rgba(0,0,0,0.1);}
input[type=text], select { padding:5px; margin-bottom:10px;}
</style>
</head>
<body>

<header>SAI - Admin Panel</header>
<nav>
  <button onclick="showSection('students')">Students</button>
  <button onclick="showSection('ai-log')">SAI AI Log</button>
  <button onclick="showSection('worksheets')">Worksheets Answers</button>
  <button onclick="showSection('meet')">SAI Meet</button>
</nav>

<section id="students">
  <h2>Student List / Demo Bookings</h2>
  <input type="text" id="searchInput" placeholder="Search student by name" onkeyup="searchStudent()" style="width:50%;">
  <table id="studentTable">
    <tr>
      <th>Name</th>
      <th>Demo Time</th>
      <th>Paid</th>
      <th>SAI Student</th>
      <th>Action</th>
    </tr>
  </table>

  <h3>View AI History</h3>
  <label>Choose Student: 
    <select id="historySelect" onchange="showStudentHistory()">
      <option value="">--Select Student--</option>
    </select>
  </label>
  <div id="historyContainer"></div>
</section>

<section id="ai-log" style="display:none;">
  <h2>SAI AI Live Usage</h2>
  <div id="aiLogs"></div>
</section>

<section id="worksheets" style="display:none;">
  <h2>Worksheets Answers</h2>
  <label>Choose Grade: 
    <select id="gradeSelect" onchange="loadSubjects()">
      <option value="">--Select Grade--</option>
      <option value="1">Grade 1</option>
      <option value="2">Grade 2</option>
      <option value="3">Grade 3</option>
      <option value="4">Grade 4</option>
      <option value="5">Grade 5</option>
      <option value="6">Grade 6</option>
      <option value="7">Grade 7</option>
      <option value="8">Grade 8</option>
      <option value="9">Grade 9</option>
      <option value="10">Grade 10</option>
    </select>
  </label>
  <label>Choose Subject: 
    <select id="subjectSelect" onchange="loadAnswers()">
      <option value="">--Select Subject--</option>
    </select>
  </label>
  <div id="answersContainer"></div>
</section>

<section id="meet" style="display:none;">
  <h2>SAI Meet</h2>
  <iframe src="https://meet.jit.si/SAI2025MEET" width="100%" height="500px" allow="camera; microphone; fullscreen"></iframe>
</section>

<script>
// --- NAVIGATION ---
function showSection(sec){
  document.querySelectorAll('section').forEach(s=>s.style.display='none');
  document.getElementById(sec).style.display='block';
}

// --- STUDENT DATA ---
let studentData = [];
let aiLogs = [];

// Load from localStorage initially
function updateStudentData(){
  let students = JSON.parse(localStorage.getItem('students')||'[]');
  let prevData = {};
  studentData.forEach(s=>{ prevData[s.name] = s; });
  studentData = students.map(s=>{
    return {
      name: s,
      demoTime: '4:30-6:30 PM',
      paid: prevData[s]?.paid || false,
      saiStudent: prevData[s]?.saiStudent || 'No'
    };
  });
  renderStudentTable();
}

// Render student table
function renderStudentTable(){
  const table = document.getElementById('studentTable');
  table.innerHTML = "<tr><th>Name</th><th>Demo Time</th><th>Paid</th><th>SAI Student</th><th>Action</th></tr>";
  studentData.forEach((s, idx)=>{
    table.innerHTML += `<tr>
      <td>${s.name}</td>
      <td>${s.demoTime}</td>
      <td><input type="checkbox" ${s.paid?'checked':''} onchange="studentData[${idx}].paid=this.checked"></td>
      <td>
        <select onchange="studentData[${idx}].saiStudent=this.value">
          <option value="No" ${s.saiStudent==='No'?'selected':''}>No</option>
          <option value="Yes" ${s.saiStudent==='Yes'?'selected':''}>Yes</option>
        </select>
      </td>
      <td><button onclick="deleteStudent(${idx})">Delete</button></td>
    </tr>`;
  });
  populateHistorySelect();
}

function deleteStudent(idx){
  if(confirm("Delete this student?")){
    studentData.splice(idx,1);
    renderStudentTable();
  }
}

function searchStudent(){
  const input = document.getElementById('searchInput').value.toLowerCase();
  const table = document.getElementById('studentTable');
  const rows = table.getElementsByTagName('tr');
  for(let i=1;i<rows.length;i++){
    let td = rows[i].getElementsByTagName('td')[0];
    rows[i].style.display = td.innerText.toLowerCase().includes(input)?'':'none';
  }
}

// --- AI LOG ---
function updateAILogs(){
  aiLogs = JSON.parse(localStorage.getItem('aiLogs')||'[]');
  const logDiv = document.getElementById('aiLogs');
  logDiv.innerHTML = "";
  aiLogs.slice().reverse().forEach(log=>{
    logDiv.innerHTML += `<p>${log.time} - ${log.name}: ${log.question}</p>`;
  });
}

function populateHistorySelect(){
  const sel = document.getElementById('historySelect');
  sel.innerHTML = "<option value=''>--Select Student--</option>";
  studentData.forEach(s=>sel.innerHTML += `<option value="${s.name}">${s.name}</option>`);
}

function showStudentHistory(){
  const name = document.getElementById('historySelect').value;
  const container = document.getElementById('historyContainer');
  container.innerHTML = "";
  if(!name) return;
  const logs = aiLogs.filter(l=>l.name===name);
  if(logs.length===0){ container.innerHTML = "No AI activity for this student."; return; }
  logs.forEach(l=>{
    container.innerHTML += `<p>${l.time}: ${l.question}</p>`;
  });
}

// --- WORKSHEETS ---
const subjectsPerGrade = {
  1: ["English","Math","Science","Social Studies","GK"],
  2: ["English","Math","Science","Social Studies","GK"],
  3: ["English","Math","Science","Social Studies","GK"],
  4: ["English","Math","Science","Social Studies","GK"],
  5: ["English","Math","Science","Social Studies","GK"],
  6: ["English","Math","Physics","Chemistry","Biology","History","Civics","Geography"],
  7: ["English","Math","Physics","Chemistry","Biology","History","Civics","Geography"],
  8: ["English","Math","Physics","Chemistry","Biology","History","Civics","Geography"],
  9: ["English","Math","Physics","Chemistry","Biology","History","Civics","Geography"],
  10:["English","Math","Physics","Chemistry","Biology","History","Civics","Geography"]
};

const sampleAnswers = {
  "Math":["1+1=2","2+3=5","5-2=3","3*2=6","10/2=5"],
  "Physics":["Newton's first law: Inertia","Force=mass*acceleration","Gravity pulls objects","Motion=change in position","Speed=distance/time"],
  "Chemistry":["H2O=water","Acid releases H+","Base releases OH-","Salt=NaCl","Chemical reaction: substance changes"],
  "Biology":["Cell: basic unit","Photosynthesis: sunlight to energy","Plant parts: root, stem, leaf","Respiration: energy release","Habitat: living place"],
  "English":["Noun: person/place/thing","Verb: action","Sentence: I am happy","Adjective: describes noun","Opposite of big: small"],
  "History":["Ashoka: Emperor","Democracy: rule by people","Empire: large kingdom","Freedom fighter: fought for freedom","Constitution: rules of country"],
  "Civics":["Citizen: member of country","Government: authority","Rights: freedoms","Duties: responsibilities","Parliament: law body"],
  "Geography":["Continents","Oceans","River","Mountain","Climate"],
  "Science":["Matter occupies space","Energy: ability to work","Force: push/pull","Light: visible energy","Magnet attracts metals"],
  "Social Studies":["Society: group people","Religions: Hindu, Muslim...","Economy: money system","Culture: lifestyle","Government: rule"],
  "GK":["President: head of state","Capital of India: New Delhi","Flag colors","National animal","National bird"]
};

function loadSubjects(){
  const grade = document.getElementById('gradeSelect').value;
  const subjectSelect = document.getElementById('subjectSelect');
  subjectSelect.innerHTML = "<option value=''>--Select Subject--</option>";
  if(subjectsPerGrade[grade]) subjectsPerGrade[grade].forEach(sub=>subjectSelect.innerHTML+=`<option value="${sub}">${sub}</option>`);
  document.getElementById('answersContainer').innerHTML="";
}

function loadAnswers(){
  const subject = document.getElementById('subjectSelect').value;
  const container = document.getElementById('answersContainer');
  container.innerHTML="";
  if(sampleAnswers[subject]){
    let html="<ol>";
    sampleAnswers[subject].forEach(ans=>html+=`<li>${ans}</li>`);
    html+="</ol>";
    container.innerHTML=html;
  }
}

// --- REAL-TIME LISTENER ---
window.addEventListener('storage', (e)=>{
  if(e.key==='students'){ updateStudentData(); }
  if(e.key==='aiLogs'){ updateAILogs(); }
});

// --- INITIAL LOAD ---
updateStudentData();
updateAILogs();
</script>
</body>
</html>
