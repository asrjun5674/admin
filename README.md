
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>SAI Admin Panel — Smart Academic Institutions</title>
<style>
:root{--bg:#f4f7fb;--card:#fff;--brand:#0b63c4;--danger:#e04f5f}
body{margin:0;font-family:Inter,Segoe UI,Arial;background:var(--bg);color:#102233}
header{background:linear-gradient(90deg,#0b63c4,#1976d2);color:white;padding:16px 12px;text-align:center}
.wrap{max-width:1200px;margin:16px auto;padding:12px}
.grid{display:grid;grid-template-columns:1fr 420px;gap:12px}
.card{background:var(--card);border-radius:10px;padding:12px;box-shadow:0 8px 30px rgba(3,13,26,0.06)}
table{width:100%;border-collapse:collapse}
th,td{padding:8px;border-bottom:1px solid #eef4fb;text-align:left}
th{background:#f2f8ff}
.btn{background:linear-gradient(90deg,#00c4a7,#00a3d1);color:#00121a;padding:8px 10px;border-radius:8px;border:none;cursor:pointer}
.danger{background:var(--danger);color:white}
.small{font-size:0.9rem;color:#4f6b7a}
.chat-window{height:340px;overflow:auto;border-radius:8px;padding:8px;background:#fbfeff;border:1px solid #e7f6fb}
.help-list{max-height:300px;overflow:auto;border:1px solid #eef6fb;padding:8px;border-radius:8px;background:#fff}
.help-item{padding:8px;border-bottom:1px solid #eef6fb}
.meet-frame{width:100%;height:420px;border-radius:8px;border:1px solid #e6f3f8}
</style>
</head>
<body>
<header>SAI Admin Panel — Smart Academic Institutions</header>
<div class="wrap">
  <div class="grid">
    <div class="card">
      <h3>Students & Bookings</h3>
      <div style="display:flex;gap:8px;align-items:center;margin-bottom:8px">
        <input id="filterName" placeholder="Filter by name..." style="flex:1;padding:8px;border-radius:6px;border:1px solid #dbeaf7">
        <button class="btn" onclick="renderStudents()">Refresh</button>
      </div>
      <table>
        <thead><tr><th>Name</th><th>Code</th><th>Grade</th><th>Demo</th><th>Status</th><th>SAI</th><th>Actions</th></tr></thead>
        <tbody id="studentTbody"></tbody>
      </table>

      <hr>
      <h4>Help Requests</h4>
      <div id="helpList" class="help-list"></div>
    </div>

    <div>
      <div class="card">
        <h3>Chat Preview & Reply</h3>
        <div style="display:flex;gap:8px;margin-bottom:8px">
          <select id="selectStudent" style="flex:1;padding:8px;border-radius:6px;border:1px solid #dbeaf7" onchange="onSelectChange()"></select>
          <button class="btn" onclick="copyCode()">Copy</button>
        </div>
        <div id="adminChat" class="chat-window"></div>
        <div style="display:flex;gap:8px;margin-top:8px">
          <input id="adminReply" placeholder="Write reply / steps..." style="flex:1;padding:8px;border-radius:6px;border:1px solid #dbeaf7">
          <select id="replyAs"><option value="admin">Admin</option><option value="sai">SAI</option></select>
          <button class="btn" onclick="adminSend()">Send</button>
        </div>
      </div>

      <div class="card" style="margin-top:12px">
        <h3>SAI Meet</h3>
        <iframe class="meet-frame" src="https://meet.jit.si/SAI2025MEET" allow="camera; microphone; fullscreen"></iframe>
        <div class="small" style="margin-top:8px">Phone join: <a href="tel:+911234567896">+91 1234567896</a></div>
      </div>

      <div class="card" style="margin-top:12px">
        <h3>Worksheet Answers</h3>
        <div style="display:flex;gap:8px;margin-bottom:8px">
          <select id="adminGrade" style="flex:1;padding:8px;border-radius:6px;border:1px solid #dbeaf7"></select>
          <select id="adminSubject" style="padding:8px;border-radius:6px;border:1px solid #dbeaf7"></select>
        </div>
        <div id="answerArea" class="small"></div>
      </div>
    </div>
  </div>
</div>

<script>
/* Admin uses same localStorage keys used by student */
const LS = {
  students(){return JSON.parse(localStorage.getItem('sai_students')||'[]')},
  setStudents(v){localStorage.setItem('sai_students',JSON.stringify(v))},
  chats(){return JSON.parse(localStorage.getItem('sai_chats')||'{}')},
  setChats(v){localStorage.setItem('sai_chats',JSON.stringify(v))},
  help(){return JSON.parse(localStorage.getItem('sai_help_requests')||'[]')},
  setHelp(v){localStorage.setItem('sai_help_requests',JSON.stringify(v))},
  meets(){return JSON.parse(localStorage.getItem('sai_meet_joins')||'[]')},
  setMeets(v){localStorage.setItem('sai_meet_joins',JSON.stringify(v))}
};

// same subject lists as student
const SUBJECTS = {
  1:['English','Mathematics','Science','Social Studies','General Knowledge'],
  2:['English','Mathematics','Science','Social Studies','General Knowledge'],
  3:['English','Mathematics','Science','Social Studies','General Knowledge'],
  4:['English','Mathematics','Science','Social Studies','General Knowledge'],
  5:['English','Mathematics','Science','Social Studies','General Knowledge'],
  6:['English','Mathematics','Physics','Chemistry','Biology','History & Civics','Geography'],
  7:['English','Mathematics','Physics','Chemistry','Biology','History & Civics','Geography'],
  8:['English','Mathematics','Physics','Chemistry','Biology','History & Civics','Geography'],
  9:['English','Mathematics','Physics','Chemistry','Biology','History & Civics','Geography'],
 10:['English','Mathematics','Physics','Chemistry','Biology','History & Civics','Geography']
};

// Reconstruction of generateWorksheet used by student (same deterministic logic)
function generateWorksheet(grade,subject){
  const qs=[]; let i=1;
  while(qs.length<20){
    const id = `${grade}_${subject.replace(/\s+/g,'')}_q${i}`;
    let q={}, n=i;
    if(subject==='Mathematics'){
      if(grade<=3){
        q.type='short'; q.text=`What is ${n+1} + ${n+2}?`; q.answer=(n+1)+(n+2); q.steps=`Add ${n+1} and ${n+2}: ${q.answer}.`;
      } else if(grade<=5){
        q.type = (i%3===0)?'word':'short';
        q.text = (i%3===0)?`If you have ${n*2} apples and give away ${n}, how many left?`:`Calculate ${n*3} - ${n+1}.`;
        q.answer = (i%3===0)? (n*2 - n) : (n*3 - (n+1));
        q.steps = `Compute: ${q.answer}.`;
      } else {
        if(i%4===0){
          q.type='algebra'; q.text=`Solve for x: ${n}x + ${n} = ${n*3}`; q.answer=2; q.steps=`${n}x + ${n} = ${n*3}. Subtract ${n}: ${n}x = ${n*2}. So x = 2.`;
        } else {
          q.type='short'; q.text=`Calculate ${n*6} ÷ ${n+1}`; q.answer=(n*6)/(n+1); q.steps=`Divide ${n*6} by ${n+1}: result ${q.answer}.`;
        }
      }
    } else if(subject==='English'){
      if(grade<=3){
        q.type='short'; q.text=`Fill in the blank: "The cat ___ on the mat." (sit/sits)`; q.answer='sits'; q.steps=`'cat' is singular so 'sits'.`;
      } else if(grade<=5){
        const words = ['box','lady','toy']; const w=words[i%3];
        q.type='short'; q.text=`Make plural: ${w}`; q.answer = (w==='lady')?'ladies':w+'s'; q.steps=`Apply plural rule.`;
      } else {
        q.type='short'; q.text=`Identify tense: "She had finished her homework before dinner."`; q.answer='past perfect'; q.steps=`'had finished' => past perfect tense.`;
      }
    } else if(['Science','Physics','Chemistry','Biology'].includes(subject)){
      if(grade<=5){
        q.type='short'; q.text=`What does a plant need to make food?`; q.answer='sunlight, water, air'; q.steps=`Photosynthesis requires sunlight, water and CO2.`;
      } else {
        if(subject==='Physics'){ q.type='short'; q.text=`State the unit of force.`; q.answer='Newton (N)'; q.steps=`Force in SI units is Newton (kg·m/s²).`; }
        else if(subject==='Chemistry'){ q.type='short'; q.text=`What is the symbol for Sodium?`; q.answer='Na'; q.steps=`Sodium symbol is Na.`; }
        else if(subject==='Biology'){ q.type='short'; q.text=`What is the basic unit of life?`; q.answer='Cell'; q.steps=`Cell is the basic unit of life.`; }
        else { q.type='short'; q.text=`Name one source of energy.`; q.answer='Sun'; q.steps=`Sun supplies most energy.`; }
      }
    } else if(subject==='Social Studies' || subject==='History & Civics' || subject==='Geography'){
      if(grade<=5){ q.type='short'; q.text=`Which is the capital city of India?`; q.answer='New Delhi'; q.steps=`New Delhi is the capital.`; }
      else {
        if(subject==='Geography'){ q.type='short'; q.text=`Define latitude.`; q.answer='distance north/south of Equator'; q.steps=`Latitude measures degrees north/south of Equator.`; }
        else { q.type='short'; q.text=`What is democracy? (short)`; q.answer='government by the people'; q.steps=`Democracy = rule by the people.`; }
      }
    } else if(subject==='General Knowledge'){
      q.type='short'; q.text=`Name a major river in India.`; q.answer='Ganges'; q.steps=`Ganges is a major river in India.`; 
    } else { q.type='short'; q.text=`Sample`; q.answer='Answer'; q.steps='Steps'; }
    qs.push({id,qtype:q.type,text:q.text,answer:String(q.answer),steps:q.steps});
    i++;
  }
  return qs;
}

// ===== Admin functionality =====
let selectedCode = null;

function renderStudents(){
  const students = LS.students();
  const filter = (document.getElementById('filterName').value||'').toLowerCase();
  const tbody = document.getElementById('studentTbody'); tbody.innerHTML='';
  students.filter(s=>!filter || s.name.toLowerCase().includes(filter)).forEach(s=>{
    const tr=document.createElement('tr');
    tr.innerHTML = `<td>${escapeHtml(s.name)}</td><td>${s.code}</td><td>${s.grade}</td><td>${s.date||'—'}</td>
      <td>${s.status}</td><td>${s.isSAIStudent? 'Yes':'No'}</td>
      <td>
        <button onclick="markPaid('${s.code}')">Paid</button>
        <button onclick="markNotPaid('${s.code}')">Not Paid</button>
        <button onclick="issueNewCode('${s.code}')">Gen Code</button>
        <button onclick="toggleSAI('${s.code}')">Toggle SAI</button>
        <button class="danger" onclick="deleteStudent('${s.code}')">Delete</button>
      </td>`;
    tbody.appendChild(tr);
  });
  populateSelect();
  renderHelpList();
}

function populateSelect(){
  const sel = document.getElementById('selectStudent'); sel.innerHTML='<option value="">-- select student --</option>';
  LS.students().forEach(s=> sel.innerHTML += `<option value="${s.code}">${escapeHtml(s.name)} (${s.code})</option>`);
}

function markPaid(code){ updateStatus(code,'Paid'); pushSystemMessage(code,`Admin marked Paid`); }
function markNotPaid(code){ updateStatus(code,'Not Paid'); pushSystemMessage(code,`Admin marked Not Paid`); }
function updateStatus(code,status){ const st=LS.students(); const s=st.find(x=>x.code===code); if(!s) return; s.status=status; LS.setStudents(st); localStorage.setItem('sai_event','status_'+Date.now()); renderStudents(); }
function issueNewCode(code){ const st=LS.students(); const s=st.find(x=>x.code===code); if(!s) return; s.code = 'DA'+Math.floor(Math.random()*90000+10000); s.status='Paid'; LS.setStudents(st); pushSystemMessage(s.code,`Admin issued new code ${s.code}`); localStorage.setItem('sai_event','issue_'+Date.now()); renderStudents(); }
function toggleSAI(code){ const st=LS.students(); const s=st.find(x=>x.code===code); if(!s) return; s.isSAIStudent = !s.isSAIStudent; LS.setStudents(st); renderStudents(); }
function deleteStudent(code){ if(!confirm('Delete student and data?')) return; let st=LS.students(); st=st.filter(x=>x.code!==code); LS.setStudents(st); const chats=LS.chats(); delete chats[code]; LS.setChats(chats); let help=LS.help(); help=help.filter(h=>h.code!==code); LS.setHelp(help); localStorage.setItem('sai_event','del_'+Date.now()); renderStudents(); }

function pushSystemMessage(code,text){ const chats=LS.chats(); chats[code]=chats[code]||[]; chats[code].push({from:'admin', text, ts:Date.now()}); LS.setChats(chats); }

// Help requests
function renderHelpList(){
  const list = LS.help(); const el=document.getElementById('helpList'); el.innerHTML='';
  if(!list.length) {el.innerHTML='<div class="small">No help requests</div>'; return;}
  list.slice().reverse().forEach(h=>{
    const div=document.createElement('div'); div.className='help-item';
    div.innerHTML = `<div><b>${escapeHtml(h.name)}</b> (Grade ${h.grade}) • ${h.subject} • ${h.status}</div>
      <div style="margin-top:6px">${escapeHtml(h.qtext)}</div>
      <div style="margin-top:8px;display:flex;gap:8px">
        <button class="btn" onclick='openHelp("${h.id}")'>Open</button>
        <button onclick='answerHelp("${h.id}")'>Answer</button>
      </div>`;
    el.appendChild(div);
  });
}

function openHelp(id){
  const h = LS.help().find(x=>x.id===id); if(!h) return;
  document.getElementById('selectStudent').value = h.code; onSelectChange();
  // scroll admin chat to bottom to view the request
}
function answerHelp(id){
  const h = LS.help().find(x=>x.id===id); if(!h) return;
  const ans = prompt('Enter step-by-step answer for this request: (will be sent to student)');
  if(!ans) return;
  h.status='answered'; h.adminReply = ans; LS.setHelp(LS.help());
  // push to chats so student sees admin answer
  const chats = LS.chats(); chats[h.code]=chats[h.code]||[]; chats[h.code].push({from:'ai', text:'Admin answer: '+ans, ts:Date.now()}); LS.setChats(chats);
  localStorage.setItem('sai_event','help_ans_'+Date.now());
  renderHelpList();
  populateSelect();
}

// Chat preview
function onSelectChange(){
  const code = document.getElementById('selectStudent').value; selectedCode = code; populateChat(code);
}
function populateChat(code){
  const win = document.getElementById('adminChat'); win.innerHTML='';
  if(!code) return;
  const chats = LS.chats()[code] || [];
  chats.forEach(m=>{ const d=document.createElement('div'); d.className='msg '+(m.from==='user'?'user':'ai'); d.innerText = `${m.from.toUpperCase()}: ${m.text}`; win.appendChild(d); });
  win.scrollTop = win.scrollHeight;
}
function adminSend(){
  const code = selectedCode || document.getElementById('selectStudent').value; if(!code) return alert('Select student');
  const text = document.getElementById('adminReply').value.trim(); if(!text) return;
  const as = document.getElementById('replyAs').value;
  const chats = LS.chats(); chats[code]=chats[code]||[]; chats[code].push({from:(as==='admin'?'admin':'ai'), text, ts:Date.now()}); LS.setChats(chats);
  document.getElementById('adminReply').value=''; localStorage.setItem('sai_event','admin_msg_'+Date.now()); populateChat(code);
}

// Answers viewer for admin
function populateGradeSubject(){
  const gsel = document.getElementById('adminGrade'); gsel.innerHTML='';
  for(let g=1; g<=10; g++) gsel.innerHTML += `<option value="${g}">Grade ${g}</option>`;
  document.getElementById('adminGrade').value='6'; renderSubjectsAdmin();
}
function renderSubjectsAdmin(){
  const g = document.getElementById('adminGrade').value; const subs = SUBJECTS[g];
  const sel = document.getElementById('adminSubject'); sel.innerHTML='';
  subs.forEach(s=> sel.innerHTML += `<option value="${s}">${s}</option>`);
  renderAnswers();
}
function renderAnswers(){
  const g = document.getElementById('adminGrade').value; const s = document.getElementById('adminSubject').value;
  const area = document.getElementById('answerArea'); area.innerHTML='';
  if(!g || !s) return;
  const list = generateWorksheet(g,s);
  list.forEach((q,idx)=> area.innerHTML += `<div style="padding:8px;border-bottom:1px solid #eef6fb"><b>Q${idx+1}:</b> ${q.text}<br><b>Answer:</b> ${q.answer}<br><b>Steps:</b> ${q.steps}</div>`);
}

// other helpers
function escapeHtml(s){return String(s).replace(/[&<>"']/g,c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]));}
function copyCode(){ const val = document.getElementById('selectStudent').value; if(!val) return alert('Select'); navigator.clipboard.writeText(val).then(()=>alert('Copied '+val)); }

// init
(function init(){ populateGradeSubject(); renderStudents(); window.addEventListener('storage', ()=>{ renderStudents(); if(selectedCode) populateChat(selectedCode); renderHelpList(); }); })();
</script>
</body>
</html>
