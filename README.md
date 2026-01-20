<html lang="nl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bezoekplanning Revalidatie</title>
  <style>
    :root {
      --primary: #0066cc;
      --primary-dark: #004c99;
      --light: #f8f9fa;
      --gray: #6c757d;
      --success: #28a745;
      --warning: #ffc107;
      --danger: #dc3545;
      --bg-free: #d4edda;
      --bg-booked: #fff3cd;
      --bg-full: #f8d7da;
    }

    * { box-sizing: border-box; margin:0; padding:0; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background: var(--light);
      color: #333;
      line-height: 1.5;
      min-height: 100vh;
    }

    .container {
      max-width: 900px;
      margin: 0 auto;
      padding: 16px;
      background: white;
      min-height: 100vh;
    }

    h1 {
      font-size: 1.8rem;
      text-align: center;
      margin: 16px 0 8px;
      color: var(--primary);
    }

    h2 {
      font-size: 1.4rem;
      margin: 24px 0 12px;
      text-align: center;
      color: #444;
    }

    .weeknav {
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 16px;
      margin: 16px 0;
      flex-wrap: wrap;
    }

    button {
      padding: 12px 20px;
      font-size: 1rem;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      transition: all 0.2s;
      min-width: 100px;
      touch-action: manipulation;
    }

    .nav-btn { background: #e9ecef; color: #333; }
    .nav-btn:hover { background: #dee2e6; }

    .add-btn {
      background: var(--primary);
      color: white;
      font-weight: bold;
      width: 100%;
      max-width: 360px;
      margin: 20px auto;
      display: block;
    }
    .add-btn:hover { background: var(--primary-dark); }

    table {
      width: 100%;
      border-collapse: collapse;
      margin: 12px 0;
      font-size: 0.95rem;
    }

    th, td {
      padding: 10px;
      text-align: center;
      border: 1px solid #dee2e6;
    }

    th {
      background: #e9ecef;
      font-weight: 600;
    }

    /* Weekoverzicht kleuren */
    .free  { background: var(--bg-free); }
    .booked { background: var(--bg-booked); }
    .full  { background: var(--bg-full); font-weight: bold; }

    /* Formulier */
    form {
      background: #f8f9fa;
      padding: 20px;
      border-radius: 12px;
      margin: 20px 0;
      border: 1px solid #dee2e6;
    }

    label {
      display: block;
      margin: 16px 0 6px;
      font-weight: 600;
      font-size: 0.95rem;
    }

    input, select {
      width: 100%;
      padding: 12px;
      border: 1px solid #ced4da;
      border-radius: 6px;
      font-size: 1rem;
    }

    .checkbox-label {
      display: flex;
      align-items: center;
      gap: 8px;
      margin: 16px 0;
      font-weight: normal;
    }

    .hidden { display: none; }

    /* Mobiel: stacked tabel voor inschrijvingen */
    @media (max-width: 640px) {
      #weekOverview th, #weekOverview td { padding: 8px 4px; font-size: 0.9rem; }
      #weekOverview th:first-child { width: 30%; }

      #inschrijvingen thead { display: none; }

      #inschrijvingen tbody tr {
        display: block;
        margin: 16px 0;
        border: 1px solid #dee2e6;
        border-radius: 10px;
        overflow: hidden;
        background: white;
        box-shadow: 0 2px 6px rgba(0,0,0,0.08);
      }

      #inschrijvingen tbody td {
        display: flex;
        justify-content: space-between;
        border: none;
        padding: 12px 16px;
        border-bottom: 1px solid #eee;
        text-align: right;
      }

      #inschrijvingen tbody td:before {
        content: attr(data-label);
        font-weight: bold;
        text-align: left;
        flex: 1;
      }

      #inschrijvingen tbody td:last-child { border-bottom: none; }
    }

    @media (max-width: 480px) {
      h1 { font-size: 1.5rem; }
      button { padding: 12px 16px; font-size: 0.95rem; }
    }
  </style>
</head>
<body>

<div class="container">
  <h1>Bezoekplanning</h1>

  <h2>Weekoverzicht</h2>
  <div class="weeknav">
    <button class="nav-btn" id="prevWeek">◀ Vorige</button>
    <strong id="weekLabel"></strong>
    <button class="nav-btn" id="nextWeek">Volgende ▶</button>
  </div>

  <table id="weekOverview">
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
        <th>Naam</th>
        <th>Datum</th>
        <th>Tijd</th>
        <th>Locatie</th>
        <th>Opmerking</th>
        <th>Vader mee</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>

  <button class="add-btn" id="showForm">+ Nieuwe inschrijving</button>

  <form id="visitForm" class="hidden">
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
      <option value="">Kies tijd</option>
      <option>10:00 – 12:00</option>
      <option>15:00 – 17:30</option>
      <option>18:30 – 20:00</option>
    </select>

    <label>Opmerking</label>
    <input id="opmerking">

    <label class="checkbox-label">
      <input type="checkbox" id="vadermee"> Vader komt mee
    </label>

    <button type="submit" class="add-btn" style="margin-top:20px;">Inschrijven</button>
  </form>
</div>

<script>
// ────────────────────────────────────────────────
const SHEET_URL = "https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec";

let entries = [];
let currentWeekStart = new Date();
currentWeekStart.setHours(0,0,0,0);
currentWeekStart.setDate(currentWeekStart.getDate() - currentWeekStart.getDay() + (currentWeekStart.getDay() === 0 ? -6 : 1)); // maandag

function normalizeDate(d) {
  if (!d) return "";
  const dt = new Date(d);
  return isNaN(dt) ? "" : dt.toISOString().split('T')[0];
}

const timeKey = t => t.includes("10") ? "T1" : t.includes("15") ? "T2" : t.includes("18") ? "T3" : "";
const timeLabel = k => k==="T1"?"10:00–12:00":k==="T2"?"15:00–17:30":k==="T3"?"18:30–20:00":"";

function isDatumToegestaan(d) {
  const g = new Date(d);
  const v = new Date(); v.setHours(0,0,0,0);
  const grens = new Date(v); grens.setDate(grens.getDate() - 7);
  return g >= grens;
}

async function loadEntries() {
  try {
    const r = await fetch(SHEET_URL);
    const d = await r.json();
    entries = d.slice(1).map(row => ({
      datum: normalizeDate(row[0]),
      tijd: row[1],
      naam: row[2],
      locatie: row[3],
      opmerking: row[4] || "",
      vadermee: row[5] || "Nee"
    }));
    updateTables();
  } catch(e) { console.error("Load error", e); }
}

function slotStatus(datum, tijdKey) {
  const rel = entries.filter(e => e.datum === datum && e.tijd === tijdKey);
  if (rel.length === 0) return { txt: "Vrij", cls: "free" };
  if (rel.length >= 2) return { txt: "Vol", cls: "full" };
  const vader = rel[0].vadermee === "Ja" ? " (vader mee)" : "";
  return { txt: "Bezet" + vader, cls: "booked" };
}

function updateWeekOverview() {
  const tbody = document.querySelector("#weekOverview tbody");
  tbody.innerHTML = "";

  for (let i = 0; i < 7; i++) {
    const d = new Date(currentWeekStart);
    d.setDate(d.getDate() + i);
    const ds = normalizeDate(d);

    const tr = document.createElement("tr");
    tr.innerHTML = `<td>${d.toLocaleDateString('nl-NL', {weekday:'short', day:'numeric', month:'short'})}</td>`;

    ["T1","T2","T3"].forEach(k => {
      const s = slotStatus(ds, k);
      tr.innerHTML += `<td class="${s.cls}">${s.txt}</td>`;
    });

    tbody.appendChild(tr);
  }
}

function updateInschrijvingen() {
  const tbody = document.querySelector("#inschrijvingen tbody");
  tbody.innerHTML = "";

  entries.forEach(e => {
    const tr = document.createElement("tr");
    tr.innerHTML = `
      <td data-label="Naam">${e.naam}</td>
      <td data-label="Datum">${e.datum}</td>
      <td data-label="Tijd">${timeLabel(e.tijd)}</td>
      <td data-label="Locatie">${e.locatie}</td>
      <td data-label="Opmerking">${e.opmerking || '–'}</td>
      <td data-label="Vader mee">${e.vadermee}</td>
    `;
    tbody.appendChild(tr);
  });
}

function updateTables() {
  const end = new Date(currentWeekStart);
  end.setDate(end.getDate() + 6);

  const fmt = d => d.toLocaleDateString('nl-NL', {day:'numeric', month:'long', year:'numeric'});
  weekLabel.textContent = fmt(currentWeekStart) + " – " + fmt(end);

  updateWeekOverview();
  updateInschrijvingen();
}

document.getElementById("prevWeek").onclick = () => {
  currentWeekStart.setDate(currentWeekStart.getDate() - 7);
  updateTables();
};

document.getElementById("nextWeek").onclick = () => {
  currentWeekStart.setDate(currentWeekStart.getDate() + 7);
  updateTables();
};

document.getElementById("showForm").onclick = () =>
  document.getElementById("visitForm").classList.toggle("hidden");

document.getElementById("visitForm").onsubmit = async e => {
  e.preventDefault();
  const form = e.target;

  const datum = normalizeDate(form.datum.value);
  if (!isDatumToegestaan(datum)) return alert("Max 7 dagen terug boeken.");

  const key = timeKey(form.tijd.value);
  const locatie = form.locatie.value;

  if (entries.some(en => en.datum === datum && en.tijd === key && en.locatie === locatie)) {
    return alert("Dit tijdslot op deze locatie is al bezet.");
  }

  const newEntry = {
    datum,
    tijd: key,
    naam: form.naam.value.trim(),
    locatie,
    opmerking: form.opmerking.value.trim(),
    vadermee: form.vadermee.checked ? "Ja" : "Nee"
  };

  entries.push(newEntry);

  try {
    await fetch(SHEET_URL, {
      method: "POST",
      mode: "no-cors",
      body: JSON.stringify(newEntry)
    });
    updateTables();
    form.reset();
    form.classList.add("hidden");
  } catch(err) {
    console.error(err);
    alert("Kon niet opslaan – probeer opnieuw.");
    entries.pop(); // rollback
  }
};

loadEntries();
</script>
</body>
</html>
