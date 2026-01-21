<html lang="nl">
<head>
  <meta charset="UTF-8" />
  <meta
    name="viewport"
    content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"
  />
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
      min-height: 20px;
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

    .day-button {
      background: none;
      border: none;
      color: var(--primary);
      font-size: 1rem;
      font-weight: 600;
      cursor: pointer;
      padding: 4px 8px;
    }

    @media (min-width: 500px) {
      .content { max-width: 480px; margin: 0 auto; }
      .fab { left: calc(50% + 220px); transform: none; }
    }
  </style>
</head>
<body>

  <header></header>

  <div class="weeknav">
    <button id="prevWeek" aria-label="Vorige week">◀</button>
    <strong id="weekLabel"></strong>
    <button id="nextWeek" aria-label="Volgende week">▶</button>
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

  <button class="fab" id="fab" aria-label="Nieuwe afspraak toevoegen">+</button>

  <div class="modal" id="modal" aria-hidden="true">
    <div class="modal-content" role="dialog" aria-modal="true" aria-labelledby="newVisitTitle">
      <div class="modal-header">
        <h2 id="newVisitTitle">Nieuwe afspraak</h2>
        <button class="close-btn" id="closeModal" aria-label="Sluiten">×</button>
      </div>

      <form id="visitForm">
        <label>Locatie</label>
        <select id="locatie" required>
          <option value="">Kies locatie</option>
          <option>Moeder – Revalidatiecentrum</option>
          <option>Vader – Thuis</option>
        </select>

        <label>Naam</label>
        <input id="naam" required placeholder="Bijv. Henk" />

        <label>Datum</label>
        <input type="date" id="datum" required />

        <label>Tijd</label>
        <select id="tijd" required>
          <option value="">Kies tijd</option>
          <option>10:00 – 12:00</option>
          <option>15:00 – 17:30</option>
          <option>18:30 – 20:00</option>
        </select>

        <label>Opmerking</label>
        <input id="opmerking" placeholder="Bijv. alleen even koffiedrinken" />

        <div class="checkbox-wrapper">
          <input type="checkbox" id="vadermee" />
          <label for="vadermee" style="margin:0; font-weight:normal;">Vader komt mee</label>
        </div>

        <button type="submit" class="submit-btn">Opslaan</button>
      </form>
    </div>
  </div>

  <div class="modal" id="dayModal" aria-hidden="true">
    <div class="modal-content" role="dialog" aria-modal="true" aria-labelledby="dayModalTitle">
      <div class="modal-header">
        <h2 id="dayModalTitle">Inschrijvingen</h2>
        <button class="close-btn" id="closeDayModal" aria-label="Sluiten">×</button>
      </div>
      <div id="dayModalContent"></div>
    </div>
  </div>

  <script>
    const SHEET_URL = "https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec";

    let entries = [];
    let currentWeekStart = getMondayOfWeek(new Date());
    let slotStatusCache = new Map();

    function getMondayOfWeek(date) {
      const d = new Date(date);
      d.setHours(0, 0, 0, 0);
      const day = d.getDay();
      const diff = day === 0 ? -6 : 1 - day;
      d.setDate(d.getDate() + diff);
      return d;
    }

    // Verbeterde datumverwerking - voorkomt timezone issues
    function normalizeDate(input) {
      if (!input) return "";
      
      // Als het al in YYYY-MM-DD formaat is
      if (typeof input === 'string' && input.match(/^\d{4}-\d{2}-\d{2}$/)) {
        return input;
      }
      
      // Parse datum zonder timezone issues
      let dt;
      if (typeof input === 'string') {
        // Voor strings zoals "1/17/2024" of andere formaten
        const parts = input.split('/');
        if (parts.length === 3) {
          // Assumeer MM/DD/YYYY formaat
          dt = new Date(parts[2], parts[0] - 1, parts[1]);
        } else {
          dt = new Date(input);
        }
      } else {
        dt = new Date(input);
      }
      
      if (isNaN(dt.getTime())) return "";
      
      // Gebruik lokale datum zonder timezone conversie
      const year = dt.getFullYear();
      const month = String(dt.getMonth() + 1).padStart(2, '0');
      const day = String(dt.getDate()).padStart(2, '0');
      
      return `${year}-${month}-${day}`;
    }

    // Verbeterde datum parsing voor display
    function parseDisplayDate(dateString) {
      const [year, month, day] = dateString.split('-').map(Number);
      return new Date(year, month - 1, day);
    }

    function formatDateForDisplay(dateString) {
      const date = parseDisplayDate(dateString);
      return date.toLocaleDateString('nl-NL', { 
        weekday: 'short', 
        day: 'numeric', 
        month: 'short' 
      }).replace('.', '');
    }

    function formatDateLong(dateString) {
      const date = parseDisplayDate(dateString);
      const formatted = date.toLocaleDateString('nl-NL', { 
        weekday: 'long', 
        day: 'numeric', 
        month: 'long', 
        year: 'numeric' 
      });
      return formatted.charAt(0).toUpperCase() + formatted.slice(1);
    }

    const timeKey = t => t.includes('10') ? 'T1' : t.includes('15') ? 'T2' : t.includes('18') ? 'T3' : '';
    const timeLabel = k => k==='T1'?'10:00–12:00':k==='T2'?'15:00–17:30':k==='T3'?'18:30–20:00':'';

    function isRecentEnough(dateString) {
      const targetDate = parseDisplayDate(dateString);
      const today = new Date();
      today.setHours(0,0,0,0);
      const minDate = new Date(today);
      minDate.setDate(minDate.getDate() - 7);
      return targetDate >= minDate;
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
        })).filter(e => e.datum); // Filter out entries with invalid dates
        
        // Clear cache when new data loads
        slotStatusCache.clear();
        renderAll();
      } catch(e) { 
        console.error("Fout bij laden:", e); 
      }
    }

    function getSlotStatus(datum, tKey) {
      const cacheKey = `${datum}-${tKey}`;
      if (slotStatusCache.has(cacheKey)) {
        return slotStatusCache.get(cacheKey);
      }

      const matches = entries.filter(e => e.datum === datum && e.tijd === tKey);
      let result;
      
      if (matches.length === 0) {
        result = { text: 'Vrij', class: 'free', vaderBadge: false };
      } else if (matches.length >= 2) {
        result = { text: 'Vol', class: 'full', vaderBadge: false };
      } else {
        const heeftVaderMee = matches.some(e => e.vadermee === 'Ja');
        result = { text: 'Bezet', class: 'booked', vaderBadge: heeftVaderMee };
      }

      slotStatusCache.set(cacheKey, result);
      return result;
    }

    function renderWeek() {
      const tbody = document.getElementById('weekBody');
      tbody.innerHTML = '';

      const fragment = document.createDocumentFragment();

      for (let i = 0; i < 7; i++) {
        const d = new Date(currentWeekStart);
        d.setDate(d.getDate() + i);
        const ds = normalizeDate(d);

        const tr = document.createElement('tr');
        
        // Format dag kolom
        const dayShort = d.toLocaleDateString('nl-NL', {weekday: 'short'}).replace('.', '');
        const dayNum = d.getDate();
        const monthShort = d.toLocaleDateString('nl-NL', {month: 'short'});
        
        tr.innerHTML = `
          <td class="day-cell" data-date="${ds}">
            <button class="day-button" aria-label="Toon inschrijvingen voor ${ds}">
              ${dayShort} ${dayNum} ${monthShort}
            </button>
          </td>`;

        ['T1','T2','T3'].forEach(k => {
          const s = getSlotStatus(ds, k);
          let badgeHtml = s.vaderBadge ? '<span class="vader-badge">Vader mee</span>' : '';
          const td = document.createElement('td');
          td.innerHTML = `
            <div class="slot ${s.class}">
              ${s.text}
              ${badgeHtml}
            </div>`;
          tr.appendChild(td);
        });

        fragment.appendChild(tr);
      }

      tbody.appendChild(fragment);
      activateDayClicks();
    }

    function renderEntries() {
      const container = document.getElementById('entriesList');
      container.innerHTML = '';

      // Sorteer op datum en vervolgens tijdvolgorde
      const order = { T1: 1, T2: 2, T3: 3 };
      const sortedEntries = [...entries].sort((a,b) => {
        const dateA = parseDisplayDate(a.datum);
        const dateB = parseDisplayDate(b.datum);
        return dateA - dateB || (order[a.tijd] - order[b.tijd]);
      });

      const fragment = document.createDocumentFragment();

      sortedEntries.forEach(e => {
        const card = document.createElement('div');
        card.className = 'entry-card';
        
        // Format datum voor display
        const formattedDate = formatDateForDisplay(e.datum);
        
        card.innerHTML = `
          <div class="top">
            <div class="naam">${e.naam}</div>
            <div class="datum-tijd">${formattedDate} • ${timeLabel(e.tijd)}</div>
          </div>
          <div class="locatie">${e.locatie}</div>
          ${e.opmerking ? `<div class="opmerking">${e.opmerking}</div>` : ''}
          ${e.vadermee === 'Ja' ? '<span class="vader-mee">Vader mee</span>' : ''}
        `;
        fragment.appendChild(card);
      });

      container.appendChild(fragment);
    }

    function activateDayClicks() {
      document.querySelectorAll(".day-cell").forEach(cell => {
        cell.onclick = () => {
          const datum = cell.dataset.date;
          openDayModal(datum);
        };
      });
    }

    function openDayModal(datum) {
      const modal = document.getElementById("dayModal");
      const title = document.getElementById("dayModalTitle");
      const content = document.getElementById("dayModalContent");

      // Filter en sorteer op tijd
      const order = { T1: 1, T2: 2, T3: 3 };
      const list = entries
        .filter(e => e.datum === datum)
        .sort((a,b) => order[a.tijd] - order[b.tijd]);

      const pretty = formatDateLong(datum);
      title.textContent = `Inschrijvingen voor ${pretty}`;

      if (list.length === 0) {
        content.innerHTML = `<p style="color: var(--text-secondary);">Geen inschrijvingen.</p>`;
      } else {
        content.innerHTML = list.map(e => `
          <div class="entry-card">
            <div class="top">
              <div class="naam">${e.naam}</div>
              <div class="datum-tijd">${timeLabel(e.tijd)}</div>
            </div>
            <div class="locatie">${e.locatie}</div>
            ${e.opmerking ? `<div class="opmerking">${e.opmerking}</div>` : ''}
            ${e.vadermee === 'Ja' ? '<span class="vader-mee">Vader mee</span>' : ''}
          </div>
        `).join('');
      }

      modal.classList.add("active");
      modal.setAttribute('aria-hidden', 'false');
    }

    function renderAll() {
      // Clear cache when re-rendering
      slotStatusCache.clear();
      
      const end = new Date(currentWeekStart);
      end.setDate(end.getDate() + 6);
      const fmt = d => d.toLocaleDateString('nl-NL', {day:'numeric', month:'long'});
      document.getElementById('weekLabel').textContent = `${fmt(currentWeekStart)} – ${fmt(end)}`;

      renderWeek();
      renderEntries();
    }

    // Navigatie
    document.getElementById('prevWeek').onclick = () => {
      currentWeekStart.setDate(currentWeekStart.getDate() - 7);
      renderAll();
    };
    document.getElementById('nextWeek').onclick = () => {
      currentWeekStart.setDate(currentWeekStart.getDate() + 7);
      renderAll();
    };

    // Modal handling
    const modal = document.getElementById('modal');
    const fab = document.getElementById('fab');
    const closeModal = document.getElementById('closeModal');
    const form = document.getElementById('visitForm');

    fab.onclick = () => { 
      modal.classList.add('active'); 
      modal.setAttribute('aria-hidden', 'false'); 
    };
    
    closeModal.onclick = () => { 
      modal.classList.remove('active'); 
      modal.setAttribute('aria-hidden', 'true'); 
    };
    
    modal.onclick = e => { 
      if (e.target === modal) { 
        modal.classList.remove('active'); 
        modal.setAttribute('aria-hidden', 'true'); 
      } 
    };

    // Day modal handling
    const dayModal = document.getElementById("dayModal");
    const closeDayModal = document.getElementById("closeDayModal");
    
    closeDayModal.onclick = () => { 
      dayModal.classList.remove('active'); 
      dayModal.setAttribute('aria-hidden', 'true'); 
    };
    
    dayModal.onclick = e => { 
      if (e.target === dayModal) { 
        dayModal.classList.remove('active'); 
        dayModal.setAttribute('aria-hidden', 'true'); 
      } 
    };

    // Form submit
    form.onsubmit = async e => {
      e.preventDefault();

      const datum = normalizeDate(form.datum.value);
      if (!datum) return alert("Ongeldige datum.");
      if (!isRecentEnough(datum)) return alert("Maximaal 7 dagen terug boeken.");

      const key = timeKey(form.tijd.value);
      const locatie = form.locatie.value;

      if (!key) return alert("Kies een geldige tijd.");
      if (!locatie) return alert("Kies een locatie.");

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
        modal.setAttribute('aria-hidden', 'true');
      } catch(err) {
        console.error(err);
        alert("Opslaan mislukt – probeer opnieuw.");
        entries.pop();
      }
    };

    // Initialize
    loadEntries();
  </script>
</body>
</html>
