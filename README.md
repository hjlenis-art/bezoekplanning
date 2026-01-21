<html lang="nl">
<head>
<meta charset="UTF-8">
<title>Bezoekplanning Revalidatie</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
body {
  font-family: Arial, sans-serif;
  background:#f4f6f8;
  padding:15px;
}

.container {
  max-width:700px;
  margin:auto;
  background:#fff;
  padding:20px;
  border-radius:10px;
}

.page { display:none; }
.page.active { display:block; }

button.main {
  width:100%;
  padding:12px;
  margin-top:10px;
  border:none;
  border-radius:8px;
  background:#2563eb;
  color:#fff;
  font-size:16px;
}

.table-wrapper {
  overflow-x:auto;
}

.week-table {
  width:100%;
  border-collapse:collapse;
  min-width:420px;
}

.week-table th,
.week-table td {
  border:1px solid #ddd;
  padding:8px;
  text-align:center;
}

.free { background:#dcfce7; }
.full { background:#fee2e2; }
</style>
</head>

<body>
<div class="container">

<!-- HOOFDPAGINA -->
<div id="mainPage" class="page active">
  <h2>Weekoverzicht</h2>

  <div class="table-wrapper">
    <table class="week-table">
      <thead>
        <tr>
          <th>Datum</th>
          <th>10:00</th>
          <th>15:00</th>
          <th>18:00</th>
        </tr>
      </thead>
      <tbody id="weekBody"></tbody>
    </table>
  </div>

  <button class="main" onclick="showPage('entriesPage')">
    Inschrijvingen bekijken
  </button>
</div>

<!-- INSCHRIJVINGEN -->
<div id="entriesPage" class="page">
  <button class="main" onclick="showPage('mainPage')">Terug</button>
  <h2>Inschrijvingen</h2>
  <div id="entriesList"></div>
</div>

</div>

<script>
/* ===== DATA ===== */
let entries = [
  { naam:"Jan", datum:"2026-01-20", tijd:"10:00", locatie:"Kamer 1" },
  { naam:"Piet", datum:"2026-01-21", tijd:"15:00", locatie:"Kamer 2" }
];

const slots = ["10:00","15:00","18:00"];

/* ===== HELPERS ===== */
function formatDate(d){
  return d.toISOString().split("T")[0];
}

function timeKey(t){
  if(t==="10:00") return "T1";
  if(t==="15:00") return "T2";
  if(t==="18:00") return "T3";
  return "";
}

function getSlotStatus(date,time){
  return entries.filter(e=>e.datum===date && e.tijd===time).length > 0
    ? "full"
    : "free";
}

/* ===== RENDER WEEK ===== */
function renderWeek(){
  const body=document.getElementById("weekBody");
  body.innerHTML="";

  for(let i=0;i<7;i++){
    const d=new Date();
    d.setDate(d.getDate()+i);
    const ds=formatDate(d);

    const tr=document.createElement("tr");
    tr.innerHTML=`<td>${ds}</td>`;

    slots.forEach(t=>{
      const td=document.createElement("td");
      td.className=getSlotStatus(ds,t);
      td.textContent=t;
      tr.appendChild(td);
    });

    body.appendChild(tr);
  }
}

/* ===== RENDER INSCHRIJVINGEN ===== */
function renderEntries(){
  const c=document.getElementById("entriesList");
  c.innerHTML="";

  entries.forEach(e=>{
    const div=document.createElement("div");
    div.style.padding="10px";
    div.style.marginBottom="8px";
    div.style.borderRadius="8px";
    div.style.background="#f9fafb";
    div.innerHTML=`
      <strong>${e.naam}</strong><br>
      ${e.datum} • ${e.tijd}<br>
      <small>${e.locatie}</small>
    `;
    c.appendChild(div);
  });
}

/* ===== PAGINA WISSEL ===== */
function showPage(id){
  document.querySelectorAll(".page")
    .forEach(p=>p.classList.remove("active"));

  document.getElementById(id).classList.add("active");

  if(id==="mainPage") renderWeek();
  if(id==="entriesPage") renderEntries();
}

/* ===== INIT ===== */
document.addEventListener("DOMContentLoaded",()=>{
  renderWeek(); // 🔴 ESSENTIEEL → dit was de oorzaak
});
</script>

</body>
</html>
