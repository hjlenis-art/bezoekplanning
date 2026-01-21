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
    }

    * { margin:0; padding:0; box-sizing:border-box; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background: var(--bg);
      color: var(--text);
      min-height: 100vh;
      line-height: 1.4;
      font-size: 17px;
    }

    header {
      background: white;
      padding: 16px;
      text-align: center;
      border-bottom: 1px solid var(--border);
      position: sticky;
      top: 0;
      z-index: 10;
    }

    h1 {
      font-size: 1.5rem;
      font-weight: 600;
      margin: 0;
    }

    .weeknav {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 12px 16px;
      background: white;
      border-bottom: 1px solid var(--border);
    }

    .weeknav button {
      background: transparent;
      border: none;
      font-size: 1.4rem;
      color: var(--primary);
      padding: 8px 16px;
      font-weight: 600;
      cursor: pointer;
    }

    .weeknav strong {
      font-size: 1.1rem;
      font-weight: 600;
    }

    .content {
      padding: 16px;
      padding-bottom: 100px;
    }

    .week-table {
      width: 100%;
      border-collapse: separate;
      border-spacing: 0 8px;
    }

    .week-table th, .week-table td {
      padding: 12px 8px;
      text-align: center;
      background: white;
      border-radius: 10px;
    }

    .week-table th {
      background: #f0f0f5;
      font-size: 0.82rem;
      color: var(--text-secondary);
      font-weight: 500;
      white-space: nowrap;
      line-height: 1.2;
      padding: 10px 6px;
    }

    .slot {
      display: flex;
      align-items: center;
      justify-content: center;
      flex-wrap: wrap;
      gap: 6px;
      padding: 8px;
      border-radius: 8px;
      font-size: 0.9rem;
      font-weight: 500;
      min-height: 36px;
    }

    .free  { background: rgba(52, 199, 89, 0.15); color: var(--free); }
    .booked{ background: rgba(255, 149, 0, 0.15); color: var(--booked); }
    .full  { background: rgba(255, 59, 48, 0.15); color: var(--full); }

    .vader-badge {
      background: #0062cc;
      color: white;
      font-size: 0.75rem;
      padding: 2px 8px;
      border-radius: 12px;
      white-space: nowrap;
    }

    h2 {
      font-size: 1.3rem;
      margin: 24px 0 12px;
      color: var(--text);
    }

    .entries-list {
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    .entry-card {
      background: white;
      border-radius: 12px;
      padding: 16px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.08);
      border: 1px solid var(--border);
    }

    .entry-card .top {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 8px;
    }

    .entry-card .naam {
      font-weight: 600;
      font-size: 1.1rem;
    }

    .entry-card .datum-tijd {
      font-size: 0.9rem;
      color: var(--text-secondary);
    }

    .entry-card .locatie {
      font-size: 0.95rem;
      margin: 4px 0;
    }

    .entry-card .opmerking {
      font-size: 0.9rem;
      color: var(--text-secondary);
      margin-top: 6px;
    }

    .vader-mee {
      background: #e8f0fe;
      color: #0062cc;
      padding: 2px 8px;
      border-radius: 12px;
      font-size: 0.8rem;
      align-self: flex-start;
      margin-top: 8px;
      display: inline-block;
    }

    .fab {
      position: fixed;
      bottom: 24px;
      left: 50%;
      transform: translateX(-50%);
      background: var(--primary);
      color: white;
      width: 60px;
      height: 60px;
      border-radius: 50%;
      font-size: 2.2rem;
      display: flex;
      align-items: center;
      justify-content: center;
      box-shadow: 0 4px 12px rgba(0,122,255,0.3);
      border: none;
      cursor: pointer;
      z-index: 100;
      transition: transform 0.2s;
    }

    .fab:active { transform: translateX(-50%) scale(0.92); }

    .modal {
      position: fixed;
      inset: 0;
      background: rgba(0,0,0,0.4);
      display: flex;
      align-items: flex-end;
      z-index: 200;
      opacity: 0;
      visibility: hidden;
      transition: all 0.3s ease;
    }

    .modal.active {
      opacity: 1;
      visibility: visible;
    }

    .modal-content {
      background: white;
      width: 100%;
      max-height: 90vh;
      border-radius: 20px 20px 0 0;
      padding: 24px 20px;
      overflow-y: auto;
      transform: translateY(100%);
      transition: transform 0.3s ease;
    }

    .modal.active .modal-content { transform: translateY(0); }

    .modal-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 20px;
    }

    .close-btn {
      background: none;
      border: none;
      font-size: 1.8rem;
      color: var(--text-secondary);
      cursor: pointer;
    }

    form label {
      display: block;
      margin: 16px 0 6px;
      font-weight: 600;
      font-size: 0.95rem;
      color: var(--text);
    }

    input, select {
      width: 100%;
      padding: 14px;
      border: 1px solid var(--border);
      border-radius: 10px;
      font-size: 1rem;
      background: #f9f9f9;
    }

    .checkbox-wrapper {
      display: flex;
      align-items: center;
      gap: 10px;
      margin: 20px 0;
    }

    .submit-btn {
      width: 100%;
      padding: 16px;
      background: var(--primary);
      color: white;
      border: none;
      border-radius: 12px;
      font-size: 1.1rem;
      font-weight: 600;
      margin-top: 20px;
      cursor: pointer;
    }

    @media (min-width: 500px) {
      .content { max-width: 480px; margin: 0 auto; }
      .fab { left: calc(50% + 220px); transform: none; }
    }
  </style>
</head>
<body>

<header>
  <h1>Bezoekplanner</h1>
</header>

<div class="weeknav">
  <button id="prevWeek">◀</button>
  <strong id="weekLabel"></strong>
  <button id="nextWeek">▶</button>
</div>

<div class="content">
  <h2>Deze week</h2>
  <table class="week-table">
    <thead>
      <tr>
        <th>Dag</th>
        <th>10:00 – 12:00</th>
        <th>15:00 – 17:30</th>
        <th>18:30 – 20:00</th>
      </tr>
    </thead>
    <tbody id="weekBody"></tbody>
  </table>

  <h2>Inschrijvingen</h2>
  <div class="entries-list" id="entriesList"></div>
</div>

<button class="fab" id="fab">+</button>

<div class="modal" id="modal">
  <div class="modal-content">
    <div class="modal-header">
      <h2>Nieuwe afspraak</h2>
      <button class="close-btn" id="closeModal">×</button>
    </div>

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
      </select>

      <label>Opmerking</label>
      <input id="opmerking" placeholder="Bijv. alleen even koffiedrinken">

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
  const d = new Date(date);
  d.setHours(0, 0, 0, 0);
  const day = d.getDay();
  const diff = day === 0 ? -6 : 1 - day;
  d.setDate(d.getDate() + diff);
  return d;
}

function normalizeDate(input) {
  if (!input) return "";
  const dt = new Date(input);
  if (isNaN(dt.getTime())) return "";
  return dt.toISOString().split('T')[0];
}

const timeKey = t => t.includes('10') ? 'T1' : t.includes('15') ? 'T2' : t.includes('18') ? 'T3' : '';
const timeLabel = k => k==='T1'?'10:00–12:00':k==='T2'?'15:00–17:30':k==='T3'?'18:30–20:00':'';

function isRecentEnough(d) {
  const g = new Date(d);
  const v = new Date(); v.setHours(0,0,0,0);
  const min = new Date(v); min.setDate(min.getDate() - 7);
  return g >= min;
}

async function loadEntries() {
  try {
    const res = await fetch(SHEET_URL);
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
  } catch(e) { console.error("Fout bij laden:", e); }
}

function getSlotStatus(datum, tKey) {
  const matches = entries.filter(e => e.datum === datum && e.tijd === tKey);
  
  if (matches.length === 0) {
    return { text: 'Vrij', class: 'free', vaderBadge: false };
  }
  
  if (matches.length >= 2) {
    return { text: 'Vol', class: 'full', vaderBadge: false };
  }
  
  const heeftVaderMee = matches.some(e => e.vadermee === 'Ja');
  return {
    text: 'Bezet',
    class: 'booked',
    vaderBadge: heeftVaderMee
  };
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
    tr.innerHTML = `<td>${dayShort} ${d.getDate()} ${d.toLocaleDateString('nl-NL', {month: 'short'})}</td>`;

    ['T1','T2','T3'].forEach(k => {
      const s = getSlotStatus(ds, k);
      
      let badgeHtml = '';
      if (s.vaderBadge) {
        badgeHtml = '<span class="vader-badge">Vader mee</span>';
      }
      
      tr.innerHTML += `
        <td>
          <div class="slot ${s.class}">
            ${s.text}
            ${badgeHtml}
          </div>
        </td>`;
    });

    tbody.appendChild(tr);
  }
}

function renderEntries() {
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

function renderAll() {
  const end = new Date(currentWeekStart);
  end.setDate(end.getDate() + 6);
  const fmt = d => d.toLocaleDateString('nl-NL', {day:'numeric', month:'long'});
  document.getElementById('weekLabel').textContent = `${fmt(currentWeekStart)} – ${fmt(end)}`;

  renderWeek();
  renderEntries();
}

// Events
document.getElementById('prevWeek').onclick = () => {
  currentWeekStart.setDate(currentWeekStart.getDate() - 7);
  renderAll();
};
document.getElementById('nextWeek').onclick = () => {
  currentWeekStart.setDate(currentWeekStart.getDate() + 7);
  renderAll();
};

const modal = document.getElementById('modal');
const fab = document.getElementById('fab');
const closeModal = document.getElementById('closeModal');
const form = document.getElementById('visitForm');

fab.onclick = () => modal.classList.add('active');
closeModal.onclick = () => modal.classList.remove('active');
modal.onclick = e => { if (e.target === modal) modal.classList.remove('active'); };

form.onsubmit = async e => {
  e.preventDefault();

  const datum = normalizeDate(form.datum.value);
  if (!isRecentEnough(datum)) return alert("Maximaal 7 dagen terug boeken.");

  const key = timeKey(form.tijd.value);
  const locatie = form.locatie.value;

  if (entries.some(en => en.datum === datum && en.tijd === key && en.locatie === locatie)) {
    return alert("Dit tijdslot is al bezet op deze locatie.");
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
      body: JSON.stringify(entry)
    });

    renderAll();
    form.reset();
    modal.classList.remove('active');
  } catch(err) {
    console.error(err);
    alert("Opslaan mislukt – probeer opnieuw.");
    entries.pop();
  }
};

loadEntries();
</script>
</body>
</html>
