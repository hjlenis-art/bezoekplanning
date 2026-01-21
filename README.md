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
      border-bottom: 1px solid var(--border);
      min-height: 20px;
    }

    .weeknav {
      display: flex;
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
      cursor: pointer;
    }

    .content { padding:16px; padding-bottom:100px; }

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

    .slot {
      padding: 8px;
      border-radius: 8px;
      font-size: 0.9rem;
      font-weight: 500;
      min-height: 36px;
    }

    .free { background:rgba(52,199,89,0.15); color:var(--free); }
    .booked { background:rgba(255,149,0,0.15); color:var(--booked); }
    .full { background:rgba(255,59,48,0.15); color:var(--full); }

    .vader-badge {
      background:#0062cc;
      color:white;
      font-size:0.75rem;
      padding:2px 8px;
      border-radius:12px;
    }

    .entries-list {
      display:flex;
      flex-direction:column;
      gap:12px;
    }

    .entry-card {
      background:white;
      padding:16px;
      border-radius:12px;
      border:1px solid var(--border);
      box-shadow:0 1px 3px rgba(0,0,0,0.08);
    }

    .top {
      display:flex;
      justify-content:space-between;
      margin-bottom:6px;
    }

    /* 🗑️ Verwijderknop */
    .delete-btn {
      margin-top:12px;
      background:#ff3b30;
      color:white;
      border:none;
      padding:8px 12px;
      border-radius:8px;
      font-size:1rem;
      cursor:pointer;
      display:flex;
      align-items:center;
      gap:6px;
    }
    .delete-btn:active { transform:scale(0.95); }

    .day-button {
      background:none;
      border:none;
      color:var(--primary);
      font-size:1rem;
      font-weight:600;
      cursor:pointer;
    }

  </style>
</head>
<body>

<header></header>

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
  <div id="entriesList" class="entries-list"></div>
</div>

<button class="fab" id="fab">+</button>

<!-- Modal voor nieuwe afspraak -->
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
        <label for="vadermee">Vader komt mee</label>
      </div>

      <button type="submit" class="submit-btn">Opslaan</button>
    </form>
  </div>
</div>

<!-- Modal voor dag-inschrijvingen -->
<div class="modal" id="dayModal">
  <div class="modal-content">
    <div class="modal-header">
      <h2 id="dayModalTitle">Inschrijvingen</h2>
      <button class="close-btn" id="closeDayModal">×</button>
    </div>
    <div id="dayModalContent"></div>
  </div>
</div>

<script>

const SHEET_URL =
  "https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec";

let entries = [];
let currentWeekStart = getMondayOfWeek(new Date());

/* ---------------------- */
/*  VEILIGE DATUM PARSER  */
/* ---------------------- */
function normalizeDate(input) {
  if (!input) return "";
  // Als het al in YYYY-MM-DD is → direct gebruiken
  if (/^\d{4}-\d{2}-\d{2}$/.test(input)) return input;

  // Input uit datepicker
  const [y, m, d] = input.split("-");
  return `${y}-${m}-${d}`;
}

function getMondayOfWeek(date) {
  const d = new Date(date.getFullYear(), date.getMonth(), date.getDate());
  const day = d.getDay();
  const diff = day === 0 ? -6 : 1 - day;
  d.setDate(d.getDate() + diff);
  return d;
}

const timeKey = (t) =>
  t.includes("10")
    ? "T1"
    : t.includes("15")
    ? "T2"
    : t.includes("18")
    ? "T3"
    : "";

const timeLabel = (k) =>
  k === "T1"
    ? "10:00–12:00"
    : k === "T2"
    ? "15:00–17:30"
    : k === "T3"
    ? "18:30–20:00"
    : "";

function isRecentEnough(d) {
  const [y, m, da] = d.split("-");
  const g = new Date(y, m - 1, da);

  const now = new Date();
  now.setHours(0, 0, 0, 0);

  const min = new Date(now);
  min.setDate(min.getDate() - 7);

  return g >= min;
}

/* ---------------------- */
/*   LADEN VAN ENTRIES   */
/* ---------------------- */

async function loadEntries() {
  try {
    const res = await fetch(SHEET_URL);
    const data = await res.json();

    entries = data.slice(1).map((r) => {
      const datum = normalizeDate(r[0]);
      const tijd = r[1];
      const naam = r[2];
      const locatie = r[3];

      return {
        id: `${datum}_${tijd}_${naam}_${locatie}`.replace(/\s+/g, ""),
        datum,
        tijd,
        naam,
        locatie,
        opmerking: r[4] || "",
        vadermee: r[5] || "Nee",
      };
    });

    renderAll();
  } catch (e) {
    console.error("Fout bij laden:", e);
  }
}

/* ---------------------- */
/*     SLOT STATUS        */
/* ---------------------- */

function getSlotStatus(datum, tKey) {
  const matches = entries.filter((e) => e.datum === datum && e.tijd === tKey);

  if (matches.length === 0) {
    return { text: "Vrij", class: "free", vaderBadge: false };
  }
  if (matches.length >= 2) {
    return { text: "Vol", class: "full", vaderBadge: false };
  }

  const vaderMee = matches.some((e) => e.vadermee === "Ja");

  return { text: "Bezet", class: "booked", vaderBadge: vaderMee };
}

/* ---------------------- */
/*     WEEK OVERZICHT     */
/* ---------------------- */

function renderWeek() {
  const tbody = document.getElementById("weekBody");
  tbody.innerHTML = "";

  for (let i = 0; i < 7; i++) {
    const d = new Date(
      currentWeekStart.getFullYear(),
      currentWeekStart.getMonth(),
      currentWeekStart.getDate() + i
    );

    const ds =
      d.getFullYear() +
      "-" +
      String(d.getMonth() + 1).padStart(2, "0") +
      "-" +
      String(d.getDate()).padStart(2, "0");

    const dayShort = d
      .toLocaleDateString("nl-NL", { weekday: "short" })
      .replace(".", "");

    const tr = document.createElement("tr");

    tr.innerHTML = `
      <td class="day-cell" data-date="${ds}">
        <button class="day-button">${dayShort} ${d.getDate()} ${d.toLocaleDateString(
      "nl-NL",
      { month: "short" }
    )}</button>
      </td>
    `;

    ["T1", "T2", "T3"].forEach((k) => {
      const s = getSlotStatus(ds, k);
      tr.innerHTML += `
        <td>
          <div class="slot ${s.class}">
            ${s.text}
            ${s.vaderBadge ? '<span class="vader-badge">Vader mee</span>' : ""}
          </div>
        </td>
      `;
    });

    tbody.appendChild(tr);
  }
}

/* ---------------------- */
/*    INSCHRIJVINGEN      */
/* ---------------------- */

function renderEntries() {
  const container = document.getElementById("entriesList");
  container.innerHTML = "";

  const order = { T1: 1, T2: 2, T3: 3 };

  entries.sort(
    (a, b) => a.datum.localeCompare(b.datum) || order[a.tijd] - order[b.tijd]
  );

  entries.forEach((e) => {
    const card = document.createElement("div");
    card.className = "entry-card";
    card.innerHTML = `
      <div class="top">
        <div class="naam">${e.naam}</div>
        <div class="datum-tijd">${e.datum} • ${timeLabel(e.tijd)}</div>
      </div>

      <div class="locatie">${e.locatie}</div>
      ${e.opmerking ? `<div class="opmerking">${e.opmerking}</div>` : ""}
      ${e.vadermee === "Ja" ? '<span class="vader-mee">Vader mee</span>' : ''}

      <button class="delete-btn" onclick="deleteEntry('${e.id}')">🗑️ Intrekken</button>
    `;

    container.appendChild(card);
  });
}

/* ---------------------- */
/*    DAG KLIKBAAR MAKEN  */
/* ---------------------- */

function activateDayClicks() {
  document.querySelectorAll(".day-cell").forEach((cell) => {
    cell.onclick = () => {
      openDayModal(cell.dataset.date);
    };
  });
}

/* ---------------------- */
/*    DAG-POPUP WEERGAVE  */
/* ---------------------- */

function openDayModal(datum) {
  const modal = document.getElementById("dayModal");
  const title = document.getElementById("dayModalTitle");
  const content = document.getElementById("dayModalContent");

  const order = { T1: 1, T2: 2, T3: 3 };
  const list = entries
    .filter((e) => e.datum === datum)
    .sort((a, b) => order[a.tijd] - order[b.tijd]);

  const [y, m, d] = datum.split("-");
  const pretty = new Date(y, m - 1, d).toLocaleDateString("nl-NL", {
    weekday: "long",
    day: "numeric",
    month: "long",
    year: "numeric",
  });

  title.textContent = `Inschrijvingen voor ${pretty}`;

  if (list.length === 0) {
    content.innerHTML = `<p style="color:var(--text-secondary)">Geen inschrijvingen.</p>`;
  } else {
    content.innerHTML = list
      .map(
        (e) => `
      <div class="entry-card">
        <div class="top">
          <div class="naam">${e.naam}</div>
          <div class="datum-tijd">${timeLabel(e.tijd)}</div>
        </div>
        <div class="locatie">${e.locatie}</div>
        ${e.opmerking ? `<div class="opmerking">${e.opmerking}</div>` : ""}
        ${e.vadermee === "Ja" ? '<span class="vader-mee">Vader mee</span>' : ""}

        <button class="delete-btn" onclick="deleteEntry('${e.id}')">🗑️ Intrekken</button>
      </div>
    `
      )
      .join("");
  }

  modal.classList.add("active");
}

/* ---------------------- */
/*     VERWIJDEREN        */
/* ---------------------- */

async function deleteEntry(id) {
  if (!confirm("Weet je zeker dat je deze inschrijving wilt intrekken?")) return;

  entries = entries.filter((e) => e.id !== id);

  try {
    await fetch(SHEET_URL, {
      method: "POST",
      mode: "no-cors",
      body: JSON.stringify({ action: "delete", id }),
    });
  } catch (e) {
    console.error("Verwijderen mislukt:", e);
    alert("Kon niet verwijderen, probeer opnieuw.");
  }

  renderAll();
}

/* ---------------------- */
/*   ALLES RENDEREN       */
/* ---------------------- */

function renderAll() {
  const end = new Date(
    currentWeekStart.getFullYear(),
    currentWeekStart.getMonth(),
    currentWeekStart.getDate() + 6
  );

  const fmt = (d) =>
    d.toLocaleDateString("nl-NL", { day: "numeric", month: "long" });

  document.getElementById("weekLabel").textContent =
    `${fmt(currentWeekStart)} – ${fmt(end)}`;

  renderWeek();
  activateDayClicks();
  renderEntries();
}

/* ---------------------- */
/*     NAVIGATIE WEEK     */
/* ---------------------- */

document.getElementById("prevWeek").onclick = () => {
  currentWeekStart.setDate(currentWeekStart.getDate() - 7);
  renderAll();
};
document.getElementById("nextWeek").onclick = () => {
  currentWeekStart.setDate(currentWeekStart.getDate() + 7);
  renderAll();
};

/* ---------------------- */
/*     MODAL HANDLING     */
/* ---------------------- */

const modal = document.getElementById("modal");
const fab = document.getElementById("fab");
const closeModal = document.getElementById("closeModal");

fab.onclick = () => modal.classList.add("active");
closeModal.onclick = () => modal.classList.remove("active");
modal.onclick = (e) => {
  if (e.target === modal) modal.classList.remove("active");
};

const dayModal = document.getElementById("dayModal");
const closeDayModal = document.getElementById("closeDayModal");

closeDayModal.onclick = () => dayModal.classList.remove("active");
dayModal.onclick = (e) => {
  if (e.target === dayModal) dayModal.classList.remove("active");
};

/* ---------------------- */
/*   FORMULIER OPSLAAN    */
/* ---------------------- */

const form = document.getElementById("visitForm");

form.onsubmit = async (e) => {
  e.preventDefault();

  const datum = normalizeDate(form.datum.value);
  if (!isRecentEnough(datum))
    return alert("Maximaal 7 dagen terug boeken.");

  const tijd = timeKey(form.tijd.value);
  const locatie = form.locatie.value;

  if (!tijd) return alert("Kies een geldige tijd.");
  if (!locatie) return alert("Kies een locatie.");

  if (
    entries.some(
      (en) => en.datum === datum && en.tijd === tijd && en.locatie === locatie
    )
  ) {
    return alert("Dit tijdslot is al bezet op deze locatie.");
  }

  const id = `${datum}_${tijd}_${form.naam.value.trim()}_${locatie}`.replace(
    /\s+/g,
    ""
  );

  const entry = {
    id,
    datum,
    tijd,
    naam: form.naam.value.trim(),
    locatie,
    opmerking: form.opmerking.value.trim(),
    vadermee: form.vadermee.checked ? "Ja" : "Nee",
  };

  entries.push(entry);

  try {
    await fetch(SHEET_URL, {
      method: "POST",
      mode: "no-cors",
      body: JSON.stringify(entry),
    });
  } catch (e) {
    console.error(e);
    alert("Opslaan mislukt, probeer opnieuw.");
  }

  modal.classList.remove("active");
  form.reset();
  renderAll();
};

/* ---------------------- */
/*     START LADEN        */
/* ---------------------- */

loadEntries();

</script>
</body>
</html>

