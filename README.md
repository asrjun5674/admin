
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
.student-list, .worksheet { background:white; padding:15px; border-radius:10px; margin-bottom:20px; box-shadow:0 0 5px rgba(0,0,0,0.1);}
h2 { color:#16A085;}
table { width:100%; border-collapse: collapse;}
table, th, td { border:1px solid #ccc; }
th, td { padding:8px; text-align:center;}
#aiLogs { max-height:300px; overflow:auto; background:white; padding:10px; border-radius:10px; box-shadow:0 0 5px rgba(0,0,0,0.1);}
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
  <table id="studentTable">
    <tr><th>Name</th><th>Demo Time</th><th>Paid</th><th>SAI Student</th><th>Action</th></tr>
    <tr><td>Ganesh</td><td>4:30-6:30 PM</td><td><input type="checkbox" checked></td><td>Yes</td><td><button onclick="deleteStudent(this)">Delete</button></td></tr>
    <tr><td>Ravi</td><td>4:30-6:30 PM</td><td><input type="checkbox"></td><td>No</td><td><button onclick="deleteStudent(this)">Delete</button></td></tr>
  </table>
</section>

<section id="ai-log" style="display:none;">
  <h2>SAI AI Live Usage</h2>
  <div id="aiLogs">
    <!-- Live AI usage logs -->
  </div>
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
let currentSection='students';
function showSection(sec){
  document.querySelectorAll('section').forEach(s=>s.style.display='none');
  document.getElementById(sec).style.display='block';
}

// Delete student
function deleteStudent(btn){
  if(confirm("Delete this student?")){
    let row = btn.parentElement.parentElement;
    row.parentElement.removeChild(row);
  }
}

// --- SAI AI Log Simulation ---
function receiveAILog(name, question){
  let div = document.createElement('div');
  let time = new Date().toLocaleTimeString();
  div.textContent = `${name} is using SAI AI at ${time}: ${question}`;
  document.getElementById('aiLogs').prepend(div);
}

// --- Worksheets Answers ---
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

function loadSubjects(){
  const grade = document.getElementById('gradeSelect').value;
  const subjectSelect = document.getElementById('subjectSelect');
  subjectSelect.innerHTML = "<option value=''>--Select Subject--</option>";
  if(subjectsPerGrade[grade]){
    subjectsPerGrade[grade].forEach(sub=>{ subjectSelect.innerHTML += `<option value='${sub}'>${sub}</option>`; });
  }
  document.getElementById('answersContainer').innerHTML = "";
}

// Sample answers (expandable to 20 per subject)
const sampleAnswers = {
  "Math":["1+1=2","2+3=5","5-2=3","3*2=6","10/2=5"],
  "Physics":["Newton's first law: Inertia","Force is mass x acceleration","Gravity pulls objects","Motion is change in position","Speed=distance/time"],
  "Chemistry":["H2O is water","Acid: releases H+","Base: releases OH-","Salt: NaCl","Chemical reaction: substance changes"],
  "Biology":["Cell is basic unit of life","Photosynthesis: sunlight to energy","Plant parts: root, stem, leaf","Respiration: energy release","Habitat: living place"],
  "English":["Noun: person/place/thing","Verb: action word","Sentence: I am happy","Adjective: describes noun","Opposite of big: small"],
  "History":["Ashoka: Emperor","Democracy: rule by people","Empire: large kingdom","Freedom fighter: fought for freedom","Constitution: rules of country"],
  "Civics":["Citizen: member of country","Government: authority","Rights: freedoms","Duties: responsibilities","Parliament: law body"],
  "Geography":["Continents: Asia, Africa...","Oceans: Pacific, Atlantic","River: flowing water","Mountain: high land","Climate: weather pattern"],
  "Science":["Matter: occupies space","Energy: ability to work","Force: push/pull","Light: visible energy","Magnet: attracts metals"],
  "Social Studies":["Society: group people","Religions: Hindu, Muslim...","Economy: money system","Culture: lifestyle","Government: rule"],
  "GK":["President: head of state","Capital of India: New Delhi","Flag: saffron, white, green","National animal: Tiger","National bird: Peacock"]
};

function loadAnswers(){
  const subject = document.getElementById('subjectSelect').value;
  const container = document.getElementById('answersContainer');
  container.innerHTML = "";
  if(sampleAnswers[subject]){
    let html = "<ol>";
    sampleAnswers[subject].forEach(ans=>{ html += `<li>${ans}</li>`; });
    html += "</ol>";
    container.innerHTML = html;
  }
}

// --- Simulate AI log (for demo) ---
setInterval(()=>{ receiveAILog('Ganesh','Asked a math question'); }, 30000);
setInterval(()=>{ receiveAILog('Ravi','Asked a biology question'); }, 45000);

</script>

</body>
</html>
