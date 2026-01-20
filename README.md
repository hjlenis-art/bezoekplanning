
<!DOCTYPE html>
<html lang="nl">
<head>
<meta charset="UTF-8">
<title>Bezoekplanning Revalidatie</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
body { font-family: Arial, sans-serif; background:#f4f6f8; padding:20px; }
.container { max-width:750px; margin:auto; background:#fff; padding:25px; border-radius:8px; box-shadow:0 4px 10px rgba(0,0,0,0.1); }
h1,h2 { text-align:center; }
label { display:block; margin-top:15px; font-weight:bold; }
input,select { width:100%; padding:10px; margin-top:5px; font-size:16px; }
button { margin-top:25px; width:100%; padding:12px; background:#0078d4; color:#fff; border:none; border-radius:5px; font-size:18px; cursor:pointer; }
button:hover { background:#005fa3; }

table { width:100%; border-collapse:collapse; margin-top:10px; text-align:center; font-size:15px; }
th,td { border:1px solid #aaa; padding:8px; }

.info { background:#eef4ff; padding:15px; border-radius:5px; margin-bottom:20px; }
.weeknav { text-align:center; margin-bottom:10px; }

.weeknav button { width:auto; padding:8px 14px; }

/* MOBIEL WEERGAVE */
@media (max-width: 600px){
  body { padding:10px; }
  .container { padding:15px; }
  table { font-size:13px; display:block; overflow-x:auto; white-space:nowrap; }
  button { font-size:16px; }
  input, select { font-size:14px; }
  .weeknav { display:flex; flex-direction:column; gap:8px; }
}
</style>
</head>

<body>
<div class="container">
<h1>Bezoekplanning</h1>

<div class="info">
<strong>Bezoektijden revalidatiecentrum:</strong><br>
Doordeweeks: 15:00 – 17:30 en 18:30 – 20:00<br>
Weekend: 10:00 – 12:00<br>
Start planning: <strong>17 januari 2026</strong>
</div>

<form id="visitForm">
<label>Locatie</label>
<select id="locatie" required>
<option value="">Maak keuze</option>
<option>Moeder – Revalidatiecentrum</option>
<option>Vader – Thuis</option>
</select>

<label>Naam</label>
<input id="naam" required>

<label>Datum</label>
<input type="date" id="datum" min="2026-01-17" required>

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

<button type="submit">Inschrijven</button>
</form>

<hr>

<h2>Weekoverzicht</h2>
<div class="weeknav">
<button id="prevWeek">◀ Vorige</button>
<strong id="weekLabel"></strong>
<button id="nextWeek">Volgende ▶</button>
</div>

<p>Groen = vrij | Oranje = 1 boeking | Rood = vol</p>

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

<h2>Inschrijvingen</h2>
<table id="inschrijvingen">
<thead>
<tr>
<th>Naam</th><th>Datum</th><th>Tijd</th><th>Locatie</th><th>Opmerking</th><th>Vader mee</th>
</tr>
</thead>
<tbody></tbody>
</table>
</div>

<script>
const SHEET_URL = "https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec";

let entries = [];
let currentWeekStart = new Date("2026-01-17");

function normalize(t){
    return t.replace(/\s/g,"").replace("–","-");
}

async function loadEntries(){
    try{
        const r = await fetch(SHEET_URL);
        const d = await r.json();

        entries = d.slice(1).map(r => ({
            datum: r[0], tijd: r[1], naam: r[2],
            locatie: r[3], opmerking: r[4], vadermee: r[5]
        }));

        updateTables();
    }catch(e){ console.error(e); }
}

function updateTables(){
    const tbody = document.querySelector("#planning tbody");
    tbody.innerHTML = "";
    const weekEnd = new Date(currentWeekStart);
    weekEnd.setDate(weekEnd.getDate()+6);

    document.getElementById("weekLabel").innerText =
        currentWeekStart.toLocaleDateString()+" – "+weekEnd.toLocaleDateString();

    for(let i=0;i<7;i++){
        const d = new Date(currentWeekStart);
        d.setDate(d.getDate()+i);
        const ds = d.toISOString().split("T")[0];

        const row = document.createElement("tr");
        row.innerHTML = `
        <td>${ds}</td>
        ${slotCell(ds,"10:00 – 12:00")}
        ${slotCell(ds,"15:00 – 17:30")}
        ${slotCell(ds,"18:30 – 20:00")}
        `;
        tbody.appendChild(row);
    }

    const tb2 = document.querySelector("#inschrijvingen tbody");
    tb2.innerHTML="";
    entries.forEach(e=>{
        tb2.innerHTML += `<tr>
        <td>${e.naam}</td><td>${e.datum}</td><td>${e.tijd}</td>
        <td>${e.locatie}</td><td>${e.opmerking}</td><td>${e.vadermee}</td>
        </tr>`;
    });
}

function slotCell(datum,tijd){
    const c = entries.filter(e =>
        e.datum===datum && normalize(e.tijd)===normalize(tijd)
    ).length;

    let bg="#c6efce", txt="Vrij";
    if(c===1){ bg="#ffeb9c"; txt="1 boeking"; }
    if(c>=2){ bg="#f4cccc"; txt="Vol"; }

    return `<td style="background:${bg}">${txt}</td>`;
}

document.getElementById("prevWeek").onclick=()=>{
    currentWeekStart.setDate(currentWeekStart.getDate()-7);
    updateTables();
};
document.getElementById("nextWeek").onclick=()=>{
    currentWeekStart.setDate(currentWeekStart.getDate()+7);
    updateTables();
};

document.getElementById("visitForm").onsubmit = async e => {
    e.preventDefault();

    const newEntry = {
        datum: datum.value,
        tijd: tijd.value,
        naam: naam.value,
        locatie: locatie.value,
        opmerking: opmerking.value,
        vadermee: vadermee.checked ? "Ja" : "Nee"
    };

    entries.push(newEntry);

    await fetch(SHEET_URL, {
        method: "POST",
        mode: "no-cors",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(newEntry)
    });

    updateTables();
    e.target.reset();
    alert("Inschrijving opgeslagen!");
};

loadEntries();
</script>
</body>
</html>
