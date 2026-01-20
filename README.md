<!DOCTYPE html>
<html lang="nl">
<head>
<meta charset="UTF-8">
<title>Bezoekplanning Revalidatie</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
body { font-family: Arial, sans-serif; background:#f4f6f8; padding:20px; }
.container { max-width:800px; margin:auto; background:#fff; padding:25px; border-radius:8px; box-shadow:0 4px 10px rgba(0,0,0,0.1); }

h1,h2 { text-align:center; margin-top:10px; }

table { width:100%; border-collapse:collapse; margin-top:10px; text-align:center; font-size:15px; }
th,td { border:1px solid #aaa; padding:8px; }

button { padding:12px 16px; background:#0078d4; color:#fff; border:none; border-radius:5px; font-size:16px; cursor:pointer; }
button:hover { background:#005fa3; }

label { display:block; margin-top:15px; font-weight:bold; }
input,select { width:100%; padding:10px; margin-top:5px; font-size:16px; }

.info { background:#eef4ff; padding:15px; border-radius:5px; margin-bottom:20px; }
.weeknav { text-align:center; margin-bottom:10px; }

.hidden { display:none; }

@media (max-width:600px){
  table { font-size:13px; display:block; overflow-x:auto; white-space:nowrap; }
}
</style>
</head>

<body>
<div class="container">
<h1>Bezoekplanning</h1>

<div class="info">
<strong>Regels:</strong><br>
• Geen dubbele boeking op hetzelfde tijdstip<br>
• Meerdere boekingen per persoon toegestaan<br>
• Boeken tot maximaal <strong>7 dagen terug</strong>
</div>

<!-- ================= WEEKOVERZICHT ================= -->
<h2>Weekoverzicht</h2>
<div class="weeknav">
<button id="prevWeek">◀ Vorige</button>
<strong id="weekLabel"></strong>
<button id="nextWeek">Volgende ▶</button>
</div>

<table id="planning">
<thead>
<tr>
<th>Datum</th>
<th>10:00–12:00</th>
<th>15:00–17:30</th>
<th>18:30–20:00</th>
</tr>
</thead>
<tbody></tbody>
</table>

<!-- ================= INSCHRIJVINGEN ================= -->
<h2>Inschrijvingen</h2>
<table id="inschrijvingen">
<thead>
<tr>
<th>Naam</th><th>Datum</th><th>Tijd</th><th>Locatie</th><th>Opmerking</th><th>Vader mee</th>
</tr>
</thead>
<tbody></tbody>
</table>

<!-- ================= NIEUWE INSCHRIJVING ================= -->
<div style="text-align:center; margin-top:25px;">
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

<button type="submit" style="margin-top:20px;">Inschrijven</button>
</form>

</div>

<script>
const SHEET_URL = "https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec";

let entries = [];
let currentWeekStart = new Date();
currentWeekStart.setDate(currentWeekStart.getDate() - currentWeekStart.getDay() + 1);

/* Helpers */
const timeKey = t => t.includes("10:00")?"T1":t.includes("15:00")?"T2":"T3";
const timeLabel = k => k==="T1"?"10:00 – 12:00":k==="T2"?"15:00 – 17:30":"18:30 – 20:00";

function isDatumToegestaan(d){
  const g=new Date(d),v=new Date();v.setHours(0,0,0,0);
  const grens=new Date(v);grens.setDate(grens.getDate()-7);
  return g>=grens;
}

/* Laden */
async function loadEntries(){
  const r=await fetch(SHEET_URL);
  const d=await r.json();
  entries=d.slice(1).map(r=>({datum:r[0],tijd:r[1],naam:r[2],locatie:r[3],opmerking:r[4],vadermee:r[5]}));
  updateTables();
}

/* UI */
function updateTables(){
  const tbody=document.querySelector("#planning tbody");
  tbody.innerHTML="";
  const end=new Date(currentWeekStart);end.setDate(end.getDate()+6);
  weekLabel.innerText=currentWeekStart.toLocaleDateString()+" – "+end.toLocaleDateString();

  for(let i=0;i<7;i++){
    const d=new Date(currentWeekStart);d.setDate(d.getDate()+i);
    const ds=d.toISOString().split("T")[0];
    tbody.innerHTML+=`<tr>
      <td>${ds}</td>
      ${slot(ds,"T1")}${slot(ds,"T2")}${slot(ds,"T3")}
    </tr>`;
  }

  const t2=document.querySelector("#inschrijvingen tbody");
  t2.innerHTML="";
  entries.forEach(e=>{
    t2.innerHTML+=`<tr>
      <td>${e.naam}</td><td>${e.datum}</td><td>${timeLabel(e.tijd)}</td>
      <td>${e.locatie}</td><td>${e.opmerking}</td><td>${e.vadermee}</td>
    </tr>`;
  });
}

function slot(d,k){
  const bezet=entries.some(e=>e.datum===d && e.tijd===k);
  return `<td style="background:${bezet?"#ffeb9c":"#c6efce"};font-weight:bold">${bezet?"Bezet":"Vrij"}</td>`;
}

/* Navigatie */
prevWeek.onclick=()=>{currentWeekStart.setDate(currentWeekStart.getDate()-7);updateTables();};
nextWeek.onclick=()=>{currentWeekStart.setDate(currentWeekStart.getDate()+7);updateTables();};

/* Form toggle */
showForm.onclick=()=>visitForm.classList.toggle("hidden");

/* Opslaan */
visitForm.onsubmit=async e=>{
  e.preventDefault();
  if(!isDatumToegestaan(datum.value)) return alert("Maximaal 7 dagen terug boeken.");
  const key=timeKey(tijd.value);
  if(entries.some(e=>e.datum===datum.value && e.tijd===key))
    return alert("Dit tijdstip is al bezet.");

  const n={datum:datum.value,tijd:key,naam:naam.value,locatie:locatie.value,opmerking:opmerking.value,vadermee:vadermee.checked?"Ja":"Nee"};
  entries.push(n);

  await fetch(SHEET_URL,{method:"POST",mode:"no-cors",headers:{"Content-Type":"application/json"},body:JSON.stringify(n)});
  updateTables();
  visitForm.reset();
  visitForm.classList.add("hidden");
};

loadEntries();
</script>
</body>
</html>
