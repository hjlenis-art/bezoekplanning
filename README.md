<html lang="nl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Bezoekplanner</title>
  <style>
    :root {
      --primary: #007aff;
      --primary-dark: #0062cc;
      --bg: #f2f2f7;
      --card: #ffffff;
      --text: #1c1c1e;
      --text-secondary: #8e8e93;
      --free: #34c759;
      --booked: #ff9500;
      --full: #ff3b30;
      --border: #c7c7cc;
      --moeder: #007aff;
      --vader: #ff9500;
      --both: #af52de;
    }
    * { margin:0; padding:0; box-sizing:border-box; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background: var(--bg);
      color: var(--text);
      min-height: 100vh;
      line-height: 1.4;
      font-size: 16px;
    }
    .weeknav {
      display: flex; align-items: center; justify-content: space-between; padding: 10px 12px;
      background: white; border-bottom: 1px solid var(--border); position: sticky; top: 0; z-index: 10;
    }
    .weeknav button { background: transparent; border: none; font-size: 1.5rem; color: var(--primary); padding: 6px 12px; font-weight: 600; cursor: pointer; }
    .weeknav strong { font-size: 1rem; font-weight: 600; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; max-width: 60%; text-align: center; }
    .content { padding: 12px 10px; padding-bottom: 100px; }
    
    .table-wrapper { overflow-x: auto; -webkit-overflow-scrolling: touch; margin: 0 -10px; padding: 0 10px; }
    .week-table { width: 100%; min-width: 380px; border-collapse: separate; border-spacing: 0 6px; }
    .week-table th, .week-table td {
      padding: 8px 4px; text-align: center; background: white; border-radius: 8px; min-width: 80px; font-size: 0.85rem; position: relative;
    }
    .week-table th { background: #f0f0f5; font-size: 0.75rem; color: var(--text-secondary); font-weight: 500; white-space: nowrap; padding: 6px 4px; position: sticky; top: 0; z-index: 5; }
    .week-table td:first-child {
      position: sticky; left: 0; z-index: 4; background: white; min-width: 90px; font-weight: 500; font-size: 0.9rem;
      padding-left: 8px; text-align: left; cursor: pointer; user-select: none;
    }
    .vader-indicator {
      position: absolute; top: 4px; right: 4px; font-size: 1.2rem; font-weight: bold; line-height: 1;
    }
    .indicator-moeder { color: var(--moeder); }
    .indicator-vader  { color: var(--vader); }
    .indicator-both   { color: var(--both); }

    .slot { display: flex; align-items: center; justify-content: center; padding: 6px 4px; border-radius: 6px; font-size: 0.82rem; font-weight: 600; min-height: 32px; line-height: 1.2; }
    .free  { background: rgba(52, 199, 89, 0.18); color: var(--free); }
    .booked{ background: rgba(255, 149, 0, 0.18); color: var(--booked); }
    .full  { background: rgba(255, 59, 48, 0.18); color: var(--full); }
    .whole-day { background: rgba(255, 149, 0, 0.28); color: var(--booked); font-weight: 700; }

    h2 { font-size: 1.25rem; margin: 20px 0 8px; color: var(--text); }
    .legend { font-size: 0.85rem; color: var(--text-secondary); margin-bottom: 16px; padding: 8px; background: white; border-radius: 8px; border: 1px solid var(--border); }
    .legend strong { color: var(--text); }
    .legend span { margin-right: 12px; font-size: 1.1rem; }

    .entries-list { display: flex; flex-direction: column; gap: 10px; }
    .entry-card { background: white; border-radius: 10px; padding: 12px; box-shadow: 0 1px 3px rgba(0,0,0,0.06); border: 1px solid var(--border); position: relative; }
    .entry-card .top { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 6px; gap: 8px; }
    .entry-card .naam { font-weight: 600; font-size: 1.05rem; flex: 1; }
    .entry-card .datum-tijd { font-size: 0.82rem; color: var(--text-secondary); text-align: right; white-space: nowrap; }
    .entry-card .locatie { font-size: 0.9rem; margin: 3px 0; }
    .entry-card .opmerking { font-size: 0.85rem; color: var(--text-secondary); margin-top: 4px; }
    .vader-mee { background: #e8f0fe; color: #0062cc; padding: 2px 7px; border-radius: 10px; font-size: 0.75rem; margin-top: 6px; display: inline-block; }
    .delete-btn { position: absolute; top: 8px; right: 8px; background: var(--full); color: white; padding: 4px 8px; border-radius: 6px; font-size: 0.8rem; cursor: pointer; border: none; }

    .btn-group { display: flex; gap: 12px; margin: 20px 0; }
    .btn-group button { flex: 1; background: var(--primary); color: white; padding: 12px; border: none; border-radius: 12px; font-size: 1rem; cursor: pointer; }

    .free-slots { margin-top: 20px; }
    .free-slots h2 { font-size: 1.2rem; margin-bottom: 8px; }
    .free-slots ul { list-style: none; padding: 0; }
    .free-slots li { background: white; padding: 8px; margin-bottom: 6px; border-radius: 8px; border: 1px solid var(--border); font-size: 0.9rem; }

    @media (min-width: 500px) {
      .content { max-width: 460px; margin: 0 auto; padding: 16px; }
    }
  </style>
</head>
<body>

<div class="weeknav">
  <button id="prevWeek">◀</button>
  <strong id="weekLabel"></strong>
  <button id="nextWeek">▶</button>
</div>

<div class="content">

  <div class="legend">
    <strong>Legenda stipjes:</strong><br>
    <span class="indicator-moeder">●</span> Iemand bij Moeder<br>
    <span class="indicator-vader">●</span> Iemand bij Vader<br>
    <span class="indicator-both">●</span> Beide locaties bezoek
  </div>

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

  <div class="free-slots">
    <h2>Vrije slots vandaag + komende 2 dagen</h2>
    <div id="freeSlotsList"></div>
  </div>

  <div class="btn-group">
    <button onclick="showPage('entriesPage')">Inschrijvingen bekijken</button>
    <button onclick="showPage('formPage')">Nieuwe afspraak maken</button>
  </div>

  <!-- Subpagina's -->
  <div id="entriesPage" class="page" style="display:none;">
    <button onclick="showPage('mainPage')" style="width:100%; padding:12px; background:var(--primary); color:white; border:none; border-radius:12px; margin-bottom:16px; font-size:1rem;">Terug naar hoofd</button>
    <h2>Alle inschrijvingen</h2>
    <div class="entries-list" id="entriesList"></div>
  </div>

  <div id="formPage" class="page" style="display:none;">
    <button onclick="showPage('mainPage')" style="width:100%; padding:12px; background:var(--primary); color:white; border:none; border-radius:12px; margin-bottom:16px; font-size:1rem;">Terug naar hoofd</button>
    <h2>Nieuwe afspraak</h2>
    <form id="visitForm">
      <label>Locatie</label>
      <select id="locatie" required>
        <option value="">Kies locatie</option>
        <option>Moeder – Revalidatiecentrum</option>
        <option>Vader – Thuis</option>
      </select>
      <label>Naam</label>
      <input id="naam" required placeholder="Bijv. Henk">
      <label>Datum</label>
      <input type="date" id="datum" required>
      <label>Tijd</label>
      <select id="tijd" required>
        <option value="">Kies tijd</option>
        <option>10:00 – 12:00</option>
        <option>15:00 – 17:30</option>
        <option>18:30 – 20:00</option>
        <option value="Geen specifieke tijd (hele dag)">Geen specifieke tijd (hele dag)</option>
      </select>
      <label>Opmerking</label>
      <input id="opmerking" placeholder="Bijv. hele middag langs">
      <div class="checkbox-wrapper">
        <input type="checkbox" id="vadermee">
        <label for="vadermee" style="margin:0; font-weight:normal;">Vader komt mee</label>
      </div>
      <button type="submit" class="submit-btn">Opslaan</button>
    </form>
  </div>

</div>

<script>
const SHEET_URL = "https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec";
let entries = [];
let currentWeekStart = getMondayOfWeek(new Date());

function getMondayOfWeek(date) {
  const d = new Date(date); d.setHours(0,0,0,0);
  const day = d.getDay();
  const diff = day === 0 ? -6 : 1 - day;
  d.setDate(d.getDate() + diff);
  return d;
}

function normalizeDate(input) {
  if (!input) return "";
  if (input.match(/^\d{4}-\d{2}-\d{2}$/)) return input;
  const parts = input.match(/(\d{1,2})-(\d{1,2})-(\d{4})/);
  if (parts) {
    const dt = new Date(parts[3], parts[2] - 1, parts[1]);
    if (!isNaN(dt.getTime())) {
      return dt.getFullYear() + '-' + String(dt.getMonth() + 1).padStart(2, '0') + '-' + String(dt.getDate()).padStart(2, '0');
    }
  }
  const dt = new Date(input);
  if (isNaN(dt.getTime())) return "";
  return dt.getFullYear() + '-' + String(dt.getMonth() + 1).padStart(2, '0') + '-' + String(dt.getDate()).padStart(2, '0');
}

function timeKey(t) {
  if (t.includes('Geen specifieke tijd') || t === 'Geen specifieke tijd (hele dag)') return 'T0';
  if (t.includes('10')) return 'T1';
  if (t.includes('15')) return 'T2';
  if (t.includes('18')) return 'T3';
  return '';
}

function timeLabel(k) {
  if (k === 'T0') return 'Hele dag';
  if (k === 'T1') return '10-12';
  if (k === 'T2') return '15-17:30';
  if (k === 'T3') return '18:30-20';
  return '';
}

function isRecentEnough(d) {
  const g = new Date(d);
  const v = new Date(); v.setHours(0,0,0,0);
  const min = new Date(v); min.setDate(min.getDate() - 7);
  return g >= min;
}

async function loadEntries() {
  try {
    const res = await fetch(SHEET_URL);
    if (!res.ok) throw new Error("Netwerkfout: " + res.status);
    const data = await res.json();
    entries = data.slice(1).map(r => ({
      datum: normalizeDate(r[0]),
      tijd: r[1],
      naam: r[2],
      locatie: r[3],
      opmerking: r[4] || '',
      vadermee: r[5] || 'Nee'
    }));
    renderAll();
  } catch(e) {
    console.error("Fout bij laden inschrijvingen:", e);
    alert("Kon de inschrijvingen niet laden. Controleer je internet of de Google Script URL.");
  }
}

function getSlotStatus(datum, tKey) {
  const dayEntries = entries.filter(e => e.datum === datum);
  const hasWholeDay = dayEntries.some(e => e.tijd === 'T0');
  const matches = dayEntries.filter(e => e.tijd === tKey);

  if (hasWholeDay) return { text: 'Hele dag bezet', class: 'whole-day' };
  if (matches.length === 0) return { text: 'Vrij', class: 'free' };
  if (matches.length >= 2) return { text: 'Vol', class: 'full' };
  return { text: 'Bezet', class: 'booked' };
}

function getDayIndicator(datum) {
  const dayEntries = entries.filter(e => e.datum === datum);
  const heeftVader = dayEntries.some(e => e.locatie.includes('Vader'));
  const heeftMoeder = dayEntries.some(e => e.locatie.includes('Moeder'));
  if (heeftVader && heeftMoeder) return 'both';
  if (heeftVader) return 'vader';
  if (heeftMoeder) return 'moeder';
  return null;
}

function showDayDetails(datum) {
  const dayEntries = entries.filter(e => e.datum === datum);
  if (dayEntries.length === 0) return alert(`Op ${datum} komt niemand.`);
  let message = `Afspraken op ${datum}:\n\n`;
  dayEntries.forEach(e => {
    const mee = e.vadermee === 'Ja' ? ' (Vader mee)' : '';
    const opm = e.opmerking ? ` - ${e.opmerking}` : '';
    message += `${e.naam} • ${timeLabel(e.tijd)}${mee} • ${e.locatie}${opm}\n`;
  });
  alert(message);
}

function renderWeek() {
  const tbody = document.getElementById('weekBody');
  tbody.innerHTML = '';
  for (let i = 0; i < 7; i++) {
    const d = new Date(currentWeekStart);
    d.setDate(d.getDate() + i);
    const ds = normalizeDate(d);
    const tr = document.createElement('tr');
    
    const dayShort = d.toLocaleDateString('nl-NL', {weekday: 'short'}).replace('.', '');
    const monthShort = d.toLocaleDateString('nl-NL', {month: 'short'});
    const indicator = getDayIndicator(ds);
    let indicatorHtml = '';
    if (indicator) {
      const className = indicator === 'moeder' ? 'indicator-moeder' : indicator === 'vader' ? 'indicator-vader' : 'indicator-both';
      indicatorHtml = `<span class="vader-indicator ${className}">●</span>`;
    }
    
    tr.innerHTML = `<td onclick="showDayDetails('${ds}')">${dayShort} ${d.getDate()} ${monthShort}${indicatorHtml}</td>`;
    
    ['T1','T2','T3'].forEach(k => {
      const s = getSlotStatus(ds, k);
      tr.innerHTML += `<td><div class="slot ${s.class}">${s.text}</div></td>`;
    });
    tbody.appendChild(tr);
  }
}

function renderFreeSlots() {
  const container = document.getElementById('freeSlotsList');
  container.innerHTML = '';
  const today = new Date();
  for (let i = 0; i < 3; i++) {
    const d = new Date(today);
    d.setDate(d.getDate() + i);
    const ds = normalizeDate(d);
    const dayLabel = i === 0 ? 'Vandaag' : i === 1 ? 'Morgen' : 'Overmorgen';
    const freeList = [];
    
    ['Moeder – Revalidatiecentrum', 'Vader – Thuis'].forEach(loc => {
      ['T1', 'T2', 'T3'].forEach(k => {
        const matches = entries.filter(e => e.datum === ds && e.tijd === k && e.locatie === loc);
        if (matches.length === 0) freeList.push(`${loc} - ${timeLabel(k)}`);
      });
      if (!entries.some(e => e.datum === ds && e.tijd === 'T0' && e.locatie === loc)) {
        freeList.push(`${loc} - Hele dag`);
      }
    });

    if (freeList.length > 0) {
      const h3 = document.createElement('h3');
      h3.textContent = `${dayLabel} (${ds})`;
      container.appendChild(h3);
      const ul = document.createElement('ul');
      freeList.forEach(slot => {
        const li = document.createElement('li');
        li.textContent = slot;
        ul.appendChild(li);
      });
      container.appendChild(ul);
    }
  }
}

function renderAll() {
  const end = new Date(currentWeekStart);
  end.setDate(end.getDate() + 6);
  const fmt = d => d.toLocaleDateString('nl-NL', {day:'numeric', month:'short'});
  document.getElementById('weekLabel').textContent = `${fmt(currentWeekStart)} – ${fmt(end)}`;
  renderWeek();
  renderFreeSlots();
}

function showPage(pageId) {
  document.querySelectorAll('.page').forEach(p => p.style.display = 'none');
  document.getElementById(pageId).style.display = 'block';
  if (pageId === 'entriesPage') {
    const container = document.getElementById('entriesList');
    container.innerHTML = '';
    entries.sort((a,b) => new Date(a.datum) - new Date(b.datum) || a.tijd.localeCompare(b.tijd));
    entries.forEach(e => {
      const card = document.createElement('div');
      card.className = 'entry-card';
      card.innerHTML = `
        <div class="top">
          <div class="naam">${e.naam}</div>
          <div class="datum-tijd">${e.datum} • ${timeLabel(e.tijd)}</div>
        </div>
        <div class="locatie">${e.locatie}</div>
        ${e.opmerking ? `<div class="opmerking">${e.opmerking}</div>` : ''}
        ${e.vadermee === 'Ja' ? '<span class="vader-mee">Vader mee</span>' : ''}
      `;
      container.appendChild(card);
    });
  }
}

document.getElementById('prevWeek').onclick = () => { currentWeekStart.setDate(currentWeekStart.getDate() - 7); renderAll(); };
document.getElementById('nextWeek').onclick = () => { currentWeekStart.setDate(currentWeekStart.getDate() + 7); renderAll(); };

const form = document.getElementById('visitForm');
form.onsubmit = async e => {
  e.preventDefault();
  const datum = normalizeDate(form.datum.value);
  if (!datum || !isRecentEnough(datum)) return alert("Ongeldige of te oude datum.");
  const key = timeKey(form.tijd.value);
  const locatie = form.locatie.value;

  if (!key || !locatie) return alert("Vul alle velden in.");

  const dayEntries = entries.filter(en => en.datum === datum && en.locatie === locatie);
  const hasWholeDay = dayEntries.some(en => en.tijd === 'T0');
  const isWholeDay = key === 'T0';

  if (hasWholeDay || (isWholeDay && dayEntries.length > 0) || 
      dayEntries.some(en => en.tijd === key && key !== 'T0')) {
    return alert("Dit tijdslot of de hele dag is al bezet op deze locatie.");
  }

  const entry = {
    datum,
    tijd: key,
    naam: form.naam.value.trim(),
    locatie,
    opmerking: form.opmerking.value.trim(),
    vadermee: form.vadermee.checked ? 'Ja' : 'Nee'
  };

  entries.push(entry);
  try {
    await fetch(SHEET_URL, {
      method: 'POST',
      mode: 'no-cors',
      headers: { 'Content-Type': 'text/plain;charset=utf-8' },
      body: JSON.stringify(entry)
    });
    renderAll();
    form.reset();
    showPage('mainPage');
  } catch(err) {
    console.error(err);
    alert("Opslaan mislukt – controleer verbinding of script.");
    entries.pop();
  }
};

loadEntries();
renderAll(); // Zorg dat week direct getoond wordt
</script>
</body>
</html>
