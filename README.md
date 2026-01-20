<html lang="nl">
<head>
<meta charset="UTF-8">
<title>Bezoekplanning Revalidatie</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
body { font-family: Arial, sans-serif; background:#f4f6f8; padding:10px; }
.container { max-width:800px; margin:auto; background:#fff; padding:20px; border-radius:8px; box-shadow:0 4px 10px rgba(0,0,0,0.1); }

h1,h2 { text-align:center; margin-top:10px; }
button { padding:10px 14px; background:#0078d4; color:#fff; border:none; border-radius:5px; font-size:16px; cursor:pointer; }
button:hover { background:#005fa3; }

input,select { width:100%; padding:8px; margin-top:5px; font-size:14px; }
label { display:block; margin-top:10px; font-weight:bold; }

.hidden { display:none; }
.weeknav { text-align:center; margin-bottom:10px; }

.weekCards { display:flex; overflow-x:auto; gap:10px; padding:5px 0; }
.weekCard { min-width:120px; background:#eef4ff; border-radius:8px; padding:8px; text-align:center; flex-shrink:0; }
.slot { padding:3px; border-radius:4px; margin-top:2px; font-size:13px; }

@media (max-width:600px){
  .weekCard { min-width:100px; padding:6px; }
  .slot { font-size:12px; padding:2px; }
  input, select { font-size:13px; padding:6px; }
  button { font-size:14px; padding:8px 10px; }
}
table { width:100%; border-collapse:collapse; margin-top:10px; text-align:center; font-size:14px; }
th,td { border:1px solid #aaa; padding:6px; }
</style>
</head>
<body>
<div class="container">
<h1>Bezoekplanning</h1>

<!-- Weekoverzicht -->
<h2>Weekoverzicht</h2>
<div class="weeknav">
<button id="prevWeek">◀ Vorige</button>
<strong id="weekLabel"></strong>
<button id="nextWeek">Volgende ▶</button>
</div>
<div id="weekCards" class="weekCards"></div>

<!-- Inschrijvingen -->
<h2>Inschrijvingen</h2>
<table id="inschrijvingen">
<thead>
<tr>
<th>Naam</th><th>Datum</th><th>Tijd</th><th>Locatie</th><th>Opmerking</th><th>Vader mee</th>
</tr>
</thead>
<tbody></tbody>
</table>

<!-- Formulier -->
<div style="text-align:center; margin-top:15px;">
<button id="showForm">Nieuwe inschrijving</button>
</div>
<form id="visitForm" class="hidden">
<hr>
<label>Locatie</label>
<select id="locatie" required>
<option value="">Maak keuze</option>
<option>Moeder – Revalidatiecentrum</option>
<option>Vader – Thuis</option>
</select>

<label>Naam</label>
<input id="naam" required>

<label>Datum</label>
<input type="date" id="datum" required>

<label>Tijd</label>
<select id="tijd" required>
<option value="">Kies</option>
<option>10:00 – 12:00</option>
<option>15:00 – 17:30</option>
<option>18:30 – 20:00</option>
</select>

<label>Opmerking</label>
<input id="opmerking">

<label><input type="checkbox" id="vadermee"> Vader mee</label>

<button type="submit" style="margin-top:15px;">Inschrijven</button>
</form>
</div>

<script>
const SHEET_URL="https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec";
let entries=[],currentWeekStart=new Date(); currentWeekStart.setDate(currentWeekStart.getDate()-currentWeekStart.getDay()+1);

/* Datum & Tijd Helpers */
function normalizeDate(d){
  if(!d) return "";
  let dt;
  if(typeof d === "string"){ dt = new Date(d); if(isNaN(dt)){const p=d.split("-"); dt=new Date(p[0],p[1]-1,p[2]);}}
  else dt=new Date(d);
  const yyyy=dt.getFullYear(), mm=String(dt.getMonth()+1).padStart(2,"0"), dd=String(dt.getDate()).padStart(2,"0");
  return `${yyyy}-${mm}-${dd}`;
}
const timeKey = t => t.includes("10:00")?"T1":t.includes("15:00")?"T2":t.includes("18:30")?"T3":"";
const timeLabel = k => k==="T1"?"10:00 – 12:00":k==="T2"?"15:00 – 17:30":k==="T3"?"18:30 – 20:00":"";
function isDatumToegestaan(d){const g=new Date(d),v=new Date(); v.setHours(0,0,0,0); const grens=new Date(v); grens.setDate(grens.getDate()-7); return g>=grens;}

/* Laden */
async function loadEntries(){
  try{
    const r=await fetch(SHEET_URL);
    const d=await r.json();
    entries=d.slice(1).map(r=>({datum:normalizeDate(r[0]),tijd:timeKey(r[1]),naam:r[2],locatie:r[3],opmerking:r[4],vadermee:r[5]}));
    updateTables();
  }catch(e){ console.error(e); }
}

/* Weekoverzicht kaartjes */
function updateWeekCards(){
  const container=document.getElementById("weekCards"); container.innerHTML="";
  for(let i=0;i<7;i++){
    const d=new Date(currentWeekStart); d.setDate(d.getDate()+i);
    const ds=d.getFullYear()+"-"+String(d.getMonth()+1).padStart(2,"0")+"-"+String(d.getDate()).padStart(2,"0");
    let html=`<div class="weekCard"><div style="font-weight:bold">${ds}</div>`;
    ["T1","T2","T3"].forEach(k=>{
      const bezet=entries.some(e=>e.datum===ds && e.tijd===k);
      let bg=bezet?"#ffeb9c":"#c6efce";
      let txt=bezet?"Bezet":"Vrij";
      html+=`<div class="slot" style="background:${bg}">${timeLabel(k)} ${txt}</div>`;
    });
    html+="</div>";
    container.innerHTML+=html;
  }
}

/* Inschrijvingen tabel */
function updateInschrijvingen(){
  const tbody=document.querySelector("#inschrijvingen tbody"); tbody.innerHTML="";
  entries.forEach(e=>{
    tbody.innerHTML+=`<tr>
      <td>${e.naam}</td><td>${e.datum}</td><td>${timeLabel(e.tijd)}</td>
      <td>${e.locatie}</td><td>${e.opmerking}</td><td>${e.vadermee}</td>
    </tr>`;
  });
}

/* Totaal update */
function updateTables(){
  const end=new Date(currentWeekStart); end.setDate(end.getDate()+6);
  weekLabel.innerText=currentWeekStart.toLocaleDateString()+" – "+end.toLocaleDateString();
  updateWeekCards();
  updateInschrijvingen();
}

/* Navigatie */
prevWeek.onclick=()=>{currentWeekStart.setDate(currentWeekStart.getDate()-7);updateTables();}
nextWeek.onclick=()=>{currentWeekStart.setDate(currentWeekStart.getDate()+7);updateTables();}

/* Form toggle */
showForm.onclick=()=>visitForm.classList.toggle("hidden");

/* Opslaan */
visitForm.onsubmit=async e=>{
  e.preventDefault();
  const normalizedDatum=normalizeDate(datum.value);
  const key=timeKey(tijd.value);
  if(!isDatumToegestaan(normalizedDatum)) return alert("Maximaal 7 dagen terug boeken.");
  if(entries.some(e=>e.datum===normalizedDatum && e.tijd===key)) return alert("Dit tijdstip is al bezet.");
  const n={datum:normalizedDatum,tijd:key,naam:naam.value,locatie:locatie.value,opmerking:opmerking.value,vadermee:vadermee.checked?"Ja":"Nee"};
  entries.push(n);
  await fetch(SHEET_URL,{method:"POST",mode:"no-cors",headers:{"Content-Type":"application/json"},body:JSON.stringify(n)});
  updateTables();
  visitForm.reset(); visitForm.classList.add("hidden");
};

loadEntries();
</script>
</body>
</html>
