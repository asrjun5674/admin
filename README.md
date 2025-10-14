<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SAI - Smart Academic Institutions (Student)</title>
<style>
body { font-family: Arial, sans-serif; margin:0; padding:0; background:#f5f5f5;}
header { background:#2E86C1; color:white; padding:15px; text-align:center; font-size:24px; }
nav { display:flex; justify-content:center; background:#2980B9;}
nav button { padding:10px 20px; margin:5px; border:none; color:white; background:#3498DB; cursor:pointer; border-radius:5px;}
nav button:hover { background:#1F618D;}
section { padding:20px; }
#sai-ai-btn { position:fixed; bottom:20px; right:20px; width:60px; height:60px; border-radius:50%; background:#E74C3C; color:white; font-weight:bold; border:none; cursor:pointer; font-size:14px; }
#ai-popup { display:none; position:fixed; bottom:90px; right:20px; width:300px; max-height:400px; overflow:auto; background:white; border:1px solid #ccc; border-radius:10px; padding:10px; box-shadow:0 0 10px rgba(0,0,0,0.3);}
#ai-popup h3 { margin-top:0; text-align:center; color:#E74C3C;}
#ai-messages { max-height:300px; overflow:auto; margin-bottom:10px;}
#ai-input { width:80%; padding:5px;}
#ai-send { padding:5px 10px; background:#E74C3C; color:white; border:none; cursor:pointer; }
.student-list, .worksheet { background:white; padding:15px; border-radius:10px; margin-bottom:20px; box-shadow:0 0 5px rgba(0,0,0,0.1);}
h2 { color:#2E86C1;}
.grade-select, .subject-select { margin-bottom:15px; padding:5px;}
</style>
</head>
<body>

<header>SAI - Smart Academic Institutions (Student)</header>
<nav>
  <button onclick="showSection('home')">Home</button>
  <button onclick="showSection('worksheets')">Worksheets</button>
  <button onclick="showSection('meet')">SAI Meet</button>
</nav>

<section id="home">
  <h2>Welcome Student!</h2>
  <p>Book your demo class first. After demo, login by your name to access worksheets and SAI AI.</p>
  <button onclick="bookDemo()">Book Demo Class</button>
</section>

<section id="worksheets" style="display:none;">
  <h2>Worksheets</h2>
  <label>Choose Grade: 
    <select id="gradeSelect" class="grade-select" onchange="loadSubjects()">
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
    <select id="subjectSelect" class="subject-select" onchange="loadWorksheet()">
      <option value="">--Select Subject--</option>
    </select>
  </label>

  <div id="worksheetContainer"></div>
  <p>Press <b>SAI AI</b> button for help with answers and step-by-step explanation.</p>
</section>

<section id="meet" style="display:none;">
  <h2>SAI Meet</h2>
  <iframe src="https://meet.jit.si/SAI2025MEET" width="100%" height="500px" allow="camera; microphone; fullscreen"></iframe>
  <p>Call Admin if needed:</p>
  <button onclick="window.location.href='tel:+911234567896'">Call Admin</button>
</section>

<button id="sai-ai-btn" onclick="toggleAI()">SAI AI</button>
<div id="ai-popup">
  <h3>SAI AI</h3>
  <div id="ai-messages"></div>
  <input type="text" id="ai-input" placeholder="Ask your question">
  <button id="ai-send" onclick="sendAI()">Send</button>
</div>

<script>
// --- DEMO BOOKING & LOGIN ---
let studentName = '';
let demoBooked = false;

function bookDemo(){
  studentName = prompt("Enter your Name to book Demo Class:");
  if(studentName){
    alert(`Demo class booked for ${studentName} at 4:30 PM - 6:30 PM`);
    demoBooked = true;
  }
}

function showSection(section){
  if(!demoBooked && section!=='home'){ alert("Please book demo first!"); return; }
  document.querySelectorAll('section').forEach(s=>s.style.display='none');
  document.getElementById(section).style.display='block';
}

// --- SAI AI ---
function toggleAI(){
  if(!studentName){ alert("Please login with your name first!"); return;}
  let popup = document.getElementById('ai-popup');
  popup.style.display = popup.style.display==='block'?'none':'block';
}

function sendAI(){
  let input = document.getElementById('ai-input').value.trim();
  if(!input) return;
  let messages = document.getElementById('ai-messages');
  let msgDiv = document.createElement('div');
  msgDiv.innerHTML = `<b>${studentName}:</b> ${input}`;
  messages.appendChild(msgDiv);

  let reply = '';
  if(!demoBooked){
    reply = "Please attend demo class first.";
  } else {
    let keyword = input.toLowerCase();
    if(keyword.includes("math") || keyword.includes("addition") || keyword.includes("division")) reply = "Math Answer: Follow steps carefully...";
    else if(keyword.includes("physics")) reply = "Physics Answer: Step by step explanation...";
    else if(keyword.includes("chemistry")) reply = "Chemistry Answer: Step by step explanation...";
    else if(keyword.includes("biology")) reply = "Biology Answer: Step by step explanation...";
    else if(keyword.includes("history") || keyword.includes("geography") || keyword.includes("civics")) reply = "Social Science Answer: Refer to concepts...";
    else reply = "I am SAI AI. Please ask your question properly.";
  }

  let aiDiv = document.createElement('div');
  aiDiv.innerHTML = `<b>SAI AI:</b> ${reply}`;
  messages.appendChild(aiDiv);
  document.getElementById('ai-input').value='';

  // notify admin (simulate)
  console.log(`${studentName} is using SAI AI: ${input}`);
}

// --- Worksheets ---
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
  document.getElementById('worksheetContainer').innerHTML = "";
}

// For demo, sample 5 questions per subject. Can expand to 20 easily
const sampleQuestions = {
  "Math":["1+1=?","2+3=?","5-2=?","3*2=?","10/2=?"],
  "Physics":["State Newton's first law","Define force","What is gravity?","Explain motion","What is speed?"],
  "Chemistry":["What is H2O?","Define acid","Define base","What is salt?","What is chemical reaction?"],
  "Biology":["What is a cell?","Define photosynthesis","Name parts of plant","What is respiration?","What is habitat?"],
  "English":["Define noun","Define verb","Make a sentence with 'happy'","What is adjective?","Write opposite of 'big'"],
  "History":["Who was Ashoka?","Define democracy","What is empire?","Name freedom fighter","What is constitution?"],
  "Civics":["Define citizen","What is government?","Explain rights","Explain duties","What is parliament?"],
  "Geography":["Name continents","Name oceans","What is river?","Define mountain","What is climate?"],
  "Science":["What is matter?","What is energy?","Define force","What is light?","What is magnet?"],
  "Social Studies":["Define society","Name 3 religions","What is economy?","Explain culture","Define government"],
  "GK":["Who is president?","Capital of India?","Flag colors?","National animal?","National bird?"]
};

function loadWorksheet(){
  const subject = document.getElementById('subjectSelect').value;
  const container = document.getElementById('worksheetContainer');
  container.innerHTML = "";
  if(sampleQuestions[subject]){
    let html = "<ol>";
    sampleQuestions[subject].forEach(q=>{ html += `<li>${q}</li>`; });
    html += "</ol>";
    container.innerHTML = html;
  }
}
</script>
</body>
</html>
