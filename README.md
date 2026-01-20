<html lang="nl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Bezoekplanner</title>

<style>
:root{
  --primary:#007aff;--bg:#f2f2f7;--card:#fff;--text:#1c1c1e;
  --muted:#8e8e93;--free:#34c759;--booked:#ff9500;--full:#ff3b30;
  --moeder:#007aff;--vader:#ff9500;--both:#af52de;--border:#c7c7cc;
}
*{box-sizing:border-box}
body{margin:0;font-family:-apple-system,BlinkMacSystemFont,Segoe UI,Roboto;background:var(--bg);color:var(--text)}
.weeknav{display:flex;justify-content:space-between;align-items:center;padding:10px;background:#fff;border-bottom:1px solid var(--border)}
.weeknav button{border:none;background:none;font-size:1.4rem;color:var(--primary);cursor:pointer}
.content{padding:12px}
.page{display:none}
.page.active{display:block}

.table-wrapper{overflow-x:auto}
.week-table{width:100%;min-width:380px;border-collapse:separate;border-spacing:0 6px}
.week-table th,.week-table td{background:#fff;border-radius:8px;padding:8px;text-align:center;font-size:.85rem}
.week-table th{background:#f0f0f5;color:var(--muted);font-size:.75rem}
.week-table td:first-child{text-align:left;font-weight:600;cursor:pointer;position:relative}

.slot{padding:6px;border-radius:6px;font-weight:600}
.free{background:rgba(52,199,89,.18);color:var(--free)}
.booked{background:rgba(255,149,0,.18);color:var(--booked)}
.full{background:rgba(255,59,48,.18);color:var(--full)}
.whole-day{background:rgba(255,149,0,.3);color:var(--booked)}

.indicator{position:absolute;top:4px;right:6px;font-size:1.1rem}
.moeder{color:var(--moeder)}
.vader{color:var(--vader)}
.both{color:var(--both)}

button.main{width:100%;padding:12px;margin-top:10px;border:none;
background:var(--primary);color:#fff;border-radius:10px;font-size:1rem}
</style>
</head>

<body>

<div class="weeknav">
  <button id="prevWeek">◀</button>
  <strong id="weekLabel"></strong>
  <button id="nextWeek">▶</button>
</div>

<div class="content">

<div id="mainPage" class="page active">
  <div class="table-wrapper">
    <table class="week-table">
      <thead>
        <tr>
          <th>Dag</th>
          <th>10-12</th>
          <th>15-17:30</th>
          <th>18:30-20</th>
        </tr>
      </thead>
      <tbody id="weekBody"></tbody>
    </table>
  </div>

  <button class="main" onclick="showPage('formPage')">Inschrijven</button>
</div>

<div id="formPage" class="page">
  <button class="main" onclick="showPage('mainPage')">Terug</button>
  <form id="visitForm">
    <select id="locatie" required>
      <option value="">Locatie</option>
      <option>Moeder – Revalidatiecentrum</option>
      <option>Vader – Thuis</option>
    </select>
    <input id="naam" placeholder="Naam" required>
    <input type="date" id="datum" required>
    <select id="tijd" required>
      <option value="">Tijd</option>
      <option>10:00 – 12:00</option>
      <option>15:00 – 17:30</option>
      <option>18:30 – 20:00</option>
      <option>Geen specifieke tijd (hele dag)</option>
    </select>
    <button class="main" type="submit">Opslaan</button>
  </form>
</div>

</div>

<script>
const SHEET_URL="https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec";
let entries=[];
let currentWeekStart=getMonday(new Date());

function getMonday(d){
  d=new Date(d);d.setHours(0,0,0,0);
  const day=d.getDay()||7;d.setDate(d.getDate()-day+1);
  return d;
}
function formatDate(d){
  return d.getFullYear()+"-"+String(d.getMonth()+1).padStart(2,"0")+"-"+String(d.getDate()).padStart(2,"0");
}
function timeKey(t){
  if(t.includes("Geen"))return"T0";
  if(t.includes("10"))return"T1";
  if(t.includes("15"))return"T2";
  if(t.includes("18"))return"T3";
}
function timeLabel(k){
  return{T1:"10-12",T2:"15-17:30",T3:"18:30-20",T0:"Hele dag"}[k];
}

async function loadEntries(){
  const r=await fetch(SHEET_URL);const d=await r.json();
  entries=d.slice(1).map(r=>({
    datum:r[0],tijd:timeKey(r[1]),naam:r[2],locatie:r[3]
  }));
  renderAll();
}

function slotStatus(datum,key){
  const day=entries.filter(e=>e.datum===datum);
  if(day.some(e=>e.tijd==="T0"))return{c:"whole-day",t:"Hele dag"};
  const c=day.filter(e=>e.tijd===key).length;
  if(c===0)return{c:"free",t:"Vrij"};
  if(c>=2)return{c:"full",t:"Vol"};
  return{c:"booked",t:"Bezet"};
}

function indicator(datum){
  const d=entries.filter(e=>e.datum===datum);
  const m=d.some(e=>e.locatie.includes("Moeder"));
  const v=d.some(e=>e.locatie.includes("Vader"));
  if(m&&v)return"both";
  if(m)return"moeder";
  if(v)return"vader";
}

function renderWeek(){
  const b=document.getElementById("weekBody");b.innerHTML="";
  for(let i=0;i<7;i++){
    const d=new Date(currentWeekStart);d.setDate(d.getDate()+i);
    const ds=formatDate(d);
    const tr=document.createElement("tr");
    const ind=indicator(ds);
    tr.innerHTML=`<td>${d.toLocaleDateString("nl",{weekday:"short"})} ${d.getDate()}
      ${ind?`<span class="indicator ${ind}">●</span>`:""}</td>`;
    ["T1","T2","T3"].forEach(k=>{
      const s=slotStatus(ds,k);
      tr.innerHTML+=`<td><div class="slot ${s.c}">${s.t}</div></td>`;
    });
    b.appendChild(tr);
  }
}

function renderAll(){
  const e=new Date(currentWeekStart);e.setDate(e.getDate()+6);
  weekLabel.textContent=
    currentWeekStart.toLocaleDateString("nl",{day:"numeric",month:"short"})+
    " – "+e.toLocaleDateString("nl",{day:"numeric",month:"short"});
  renderWeek();
}

function showPage(id){
  document.querySelectorAll(".page").forEach(p=>p.classList.remove("active"));
  document.getElementById(id).classList.add("active");
  if(id==="mainPage")renderAll();
}

prevWeek.onclick=()=>{currentWeekStart.setDate(currentWeekStart.getDate()-7);renderAll();}
nextWeek.onclick=()=>{currentWeekStart.setDate(currentWeekStart.getDate()+7);renderAll();}

visitForm.onsubmit=async e=>{
  e.preventDefault();
  const entry={
    datum:visitForm.datum.value,
    tijd:timeKey(visitForm.tijd.value),
    naam:visitForm.naam.value,
    locatie:visitForm.locatie.value
  };
  entries.push(entry);
  await fetch(SHEET_URL,{method:"POST",mode:"no-cors",body:JSON.stringify(entry)});
  showPage("mainPage");
};

loadEntries();
</script>
</body>
</html>
