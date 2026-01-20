<html lang="nl">
<head>
<meta charset="UTF-8">
<title>Bezoekplanning Revalidatie</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
  body { font-family: Arial, sans-serif; background: #f4f6f8; padding: 20px; }
  .container { max-width:700px; margin:auto; background:#fff; padding:25px; border-radius:8px; box-shadow:0 4px 10px rgba(0,0,0,0.1);}
  h1 { text-align:center; }
  label { display:block; margin-top:15px; font-weight:bold; }
  input, select { width:100%; padding:10px; margin-top:5px; }
  .checkbox { margin-top:10px; }
  button { margin-top:25px; width:100%; padding:12px; background:#0078d4; color:#fff; border:none; font-size:16px; border-radius:5px; cursor:pointer; }
  button:hover { background:#005fa3; }
  .info { background:#eef4ff; padding:15px; border-radius:5px; margin-bottom:20px; font-size:14px; }
  table { border-collapse:collapse; text-align:center; width:100%; margin-top:10px; }
  th, td { border:1px solid #aaa; padding:8px; }
</style>
</head>
<body>
<div class="container">
<h1>Bezoekplanning</h1>

<div class="info">
  <strong>Bezoektijden revalidatiecentrum:</strong><br>
  Doordeweeks: 15:00 – 17:30 en 18:30 – 20:00<br>
  Weekend: 10:00 – 12:00<br>
  Startdatum planning: <strong>17 januari 2026</strong>
</div>

<form id="visitForm">
  <label>Waar gaat het bezoek naartoe?</label>
  <select id="locatie" required>
    <option value="">Maak een keuze</option>
    <option>Moeder – Revalidatiecentrum</option>
    <option>Vader – Thuis</option>
  </select>

  <label>Naam bezoeker(s)</label>
  <input type="text" id="naam" placeholder="Bijv. Henk - Diana - Vasco" required>

  <label>Datum bezoek</label>
  <input type="date" id="datum" min="2026-01-17" required>

  <label>Tijdstip</label>
  <select id="tijd" required>
    <option value="">Kies tijdstip</option>
    <option>15:00 – 17:30 (doordeweeks)</option>
    <option>18:30 – 20:00 (doordeweeks)</option>
    <option>10:00 – 12:00 (weekend)</option>
    <option>Ochtend (Vader)</option>
    <option>Middag (Vader)</option>
    <option>Avond (Vader)</option>
  </select>

  <div class="checkbox">
    <label><input type="checkbox" id="vadermee"> Vader wordt meegenomen naar moeder</label>
  </div>

  <label>Opmerking (optioneel)</label>
  <input type="text" id="opmerking" placeholder="Bijv. wij halen vader thuis op">

  <button type="submit">Inschrijven</button>
</form>

<hr>

<h2>Weekoverzicht beschikbaarheid – Moeder (Revalidatie)</h2>
<div style="margin-bottom:10px;">
  <button id="prevWeek" type="button">◀ Vorige week</button>
  <strong id="weekLabel" style="margin:0 10px;">Week 3 (12–18 jan 2026)</strong>
  <button id="nextWeek" type="button">Volgende week ▶</button>
</div>
<p>Groen = 0 boekingen | Oranje = 1 boeking | Rood = 2 boekingen (vol)</p>
<table id="planning">
  <thead>
    <tr><th>Datum</th><th>10:00 – 12:00</th><th>15:00 – 17:30</th><th>18:30 – 20:00</th></tr>
  </thead>
  <tbody></tbody>
</table>

<h2 style="margin-top:30px;">Inschrijvingen</h2>
<table id="inschrijvingen">
  <thead>
    <tr><th>Naam</th><th>Locatie</th><th>Datum</th><th>Tijd</th><th>Opmerking</th><th>Vader mee?</th></tr>
  </thead>
  <tbody></tbody>
</table>

</div>

<script>
// === CONFIG: URL van je Google WebApp ===
const SHEET_URL = "[PLAATS_HIER_JE_WEBAPP_URL](https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec)";

// Data ophalen uit Google Sheet
let entries = [];

async function loadEntries(){
  const res = await fetch(SHEET_URL);
  const data = await res.json();
  // eerste regel headers weghalen
  entries = data.slice(1).map(r => ({
    datum: r[0],
    tijd: r[1],
    naam: r[2],
    locatie: r[3],
    opmerking: r[4],
    vadermee: r[5]
  }));
  updateTables();
}

// Form submit
document.getElementById("visitForm").addEventListener("submit", e=>{
  e.preventDefault();
  const entry = {
    datum: document.getElementById("datum").value,
    tijd: document.getElementById("tijd").value,
    naam: document.getElementById("naam").value,
    locatie: document.getElementById("locatie").value,
    opmerking: document.getElementById("opmerking").value,
    vadermee: document.getElementById("vadermee").checked ? "Ja" : "Nee"
  };
  // In Google Form invullen via POST kan, hier doen we demo via alert
  alert("Je inschrijving is geregistreerd! Voor live versie wordt dit opgeslagen in Google Sheet.");
  // Voeg toe aan lokale entries (direct zichtbaar)
  entries.push(entry);
  updateTables();
  document.getElementById("visitForm").reset();
});

// Update weekplanning en inschrijvingen
function updateTables(){
  const tbody = document.querySelector("#planning tbody");
  tbody.innerHTML = "";
  // voorbeeld: 7 dagen vanaf 17 jan
  const start = new Date("2026-01-17");
  for(let i=0;i<7;i++){
    const d = new Date(start);
    d.setDate(start.getDate()+i);
    const datumStr = d.toISOString().split("T")[0];
    const row = document.createElement("tr");
    row.innerHTML = `<td>${datumStr}</td>
      <td style="background:${colorFor(datumStr,"10:00 – 12:00")}">${labelFor(datumStr,"10:00 – 12:00")}</td>
      <td style="background:${colorFor(datumStr,"15:00 – 17:30")}">${labelFor(datumStr,"15:00 – 17:30")}</td>
      <td style="background:${colorFor(datumStr,"18:30 – 20:00")}">${labelFor(datumStr,"18:30 – 20:00")}</td>`;
    tbody.appendChild(row);
  }

  // Inschrijvingen table
  const tb2 = document.querySelector("#inschrijvingen tbody");
  tb2.innerHTML = "";
  entries.forEach(e=>{
    const tr = document.createElement("tr");
    tr.innerHTML = `<td>${e.naam}</td><td>${e.locatie}</td><td>${e.datum}</td><td>${e.tijd}</td><td>${e.opmerking}</td><td>${e.vadermee}</td>`;
    tb2.appendChild(tr);
  });
}

// Bepaal kleur op basis van aantal inschrijvingen
function colorFor(datum,tijd){
  const count = entries.filter(e=>e.datum===datum && e.tijd===tijd).length;
  if(count==0) return "#c6efce";
  if(count==1) return "#ffc7ce";
  return "#ff0000";
}

function labelFor(datum,tijd){
  const count = entries.filter(e=>e.datum===datum && e.tijd===tijd).length;
  if(count==0) return "Vrij";
  if(count==1) return "Bezet";
  return "Vol";
}

loadEntries();
</script>
</body>
</html>
