<html lang="nl">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Bezoekplanner v4</title>
<style>
:root{
 --primary:#007aff;--accent:#34c759;--danger:#ff3b30;
 --bg:#f6f7fb;--card:#fff;--text:#1c1c1e;--gray:#8e8e93;--border:#d2d2d7
}
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,sans-serif;background:var(--bg);color:var(--text);overflow-x:hidden}
.fadeIn{animation:fadeIn .4s ease}
@keyframes fadeIn{from{opacity:0;transform:translateY(10px)}to{opacity:1;transform:none}}

header{text-align:center;padding:1rem;background:linear-gradient(90deg,var(--primary),#409cff);color:#fff;border-radius:0 0 10px 10px;box-shadow:0 2px 8px rgba(0,0,0,.1)}
header h1{font-size:1.5rem;font-weight:700;margin-bottom:.2rem}
header p{font-size:.9rem;opacity:.9}

.weeknav{display:flex;justify-content:space-between;align-items:center;background:var(--card);
margin:.7rem auto;padding:.5rem 1rem;border-radius:12px;max-width:560px;
box-shadow:0 1px 4px rgba(0,0,0,.05)}
.weeknav button{border:none;background:none;color:var(--primary);font-size:1.4rem;font-weight:600}
.weeknav strong{font-size:.95rem;color:var(--text)}

main{max-width:560px;margin:auto;padding:0 1rem 5rem}
h2{margin:1.4rem 0 .6rem;font-size:1.2rem}
table{width:100%;border-collapse:separate;border-spacing:0 .5rem}
th{font-size:.8rem;color:var(--gray);padding:.4rem;background:#f0f0f5;border-radius:6px 6px 0 0}
td{background:var(--card);border-radius:10px;text-align:center;padding:.5rem}
.slot{padding:.3rem .4rem;border-radius:8px;font-size:.85rem;font-weight:500;transition:all .2s}
.free{background:rgba(52,199,89,.12);color:var(--accent)}
.booked{background:rgba(255,149,0,.15);color:#ff9500}
.full{background:rgba(255,59,48,.15);color:var(--danger)}
.slot:hover{transform:scale(1.02)}
.vader-badge{background:var(--primary);color:#fff;font-size:.7rem;padding:0 .5rem;border-radius:10px;margin-left:.3rem}
.day-button{background:none;border:none;color:var(--primary);font-weight:600;cursor:pointer}

.entry-card{background:var(--card);border-radius:10px;padding:1rem;margin-bottom:.6rem;
box-shadow:0 1px 4px rgba(0,0,0,.05);animation:fadeIn .4s ease}
.entry-card .top{display:flex;justify-content:space-between;align-items:center;margin-bottom:.3rem}
.entry-card .naam{font-weight:600}
.entry-card .datum-tijd{font-size:.85rem;color:var(--gray)}
.vader-mee{display:inline-block;margin-top:.4rem;background:#e8f0fe;color:var(--primary);padding:.15rem .5rem;border-radius:10px;font-size:.75rem}
.bezoek-heit{display:inline-block;margin-top:.4rem;background:#fff3cd;color:#b07d00;padding:.15rem .5rem;border-radius:10px;font-size:.75rem}

.fab{position:fixed;bottom:1.5rem;right:1.5rem;width:54px;height:54px;display:flex;align-items:center;justify-content:center;
border:none;border-radius:50%;background:var(--primary);color:#fff;font-size:2rem;
box-shadow:0 4px 12px rgba(0,122,255,.3);cursor:pointer;transition:transform .2s}
.fab:active{transform:scale(.9)}

.modal{position:fixed;inset:0;background:rgba(0,0,0,.4);display:flex;justify-content:center;align-items:flex-end;
opacity:0;visibility:hidden;transition:opacity .25s}
.modal.active{opacity:1;visibility:visible}
.modal-content{background:var(--card);width:100%;max-width:560px;border-radius:16px 16px 0 0;
padding:1.4rem;transform:translateY(100%);transition:transform .3s;max-height:90vh;overflow:auto}
.modal.active .modal-content{transform:translateY(0)}
.modal-header{display:flex;justify-content:space-between;align-items:center;margin-bottom:1rem}
.close-btn{border:none;background:none;font-size:1.6rem;color:var(--gray)}
.submit-btn{width:100%;padding:.9rem;background:var(--primary);color:#fff;border:none;border-radius:10px;font-size:1rem;font-weight:600;margin-top:1rem}
input,select{width:100%;padding:.8rem;border:1px solid var(--border);border-radius:8px;margin-top:.3rem;background:#fafafa}
label{font-weight:600;font-size:.9rem;margin-top:.8rem;display:block}
.delete-btn{border:none;background:none;color:var(--danger);font-size:1.2rem;cursor:pointer}
</style>
</head>
<body>
<header>
  <h1>👋 Bezoekplanner</h1>
  <p>Plan en beheer je bezoekmomenten</p>
</header>

<div class="weeknav fadeIn">
  <button id="prevWeek">◀</button>
  <strong id="weekLabel"></strong>
  <button id="nextWeek">▶</button>
</div>

<main>
  <h2>Deze week</h2>
  <table>
    <thead><tr><th>Dag</th><th>10–12</th><th>15–17:30</th><th>18:30–20</th></tr></thead>
    <tbody id="weekBody"></tbody>
  </table>

  <h2>Inschrijvingen</h2>
  <div id="entriesList"></div>
</main>

<button class="fab" id="fab">+</button>

<!-- Nieuwe afspraak -->
<div class="modal" id="modal">
  <div class="modal-content fadeIn">
    <div class="modal-header">
      <h2>Nieuwe afspraak</h2>
      <button class="close-btn" id="closeModal">×</button>
    </div>
    <form id="visitForm">
      <label>Locatie</label>
      <select id="locatie" required>
        <option value="">Kies locatie</option>
        <option>Moeder – Revalidatiecentrum</option>
        <option>Vader – Thuis</option>
      </select>
      <label>Naam</label><input id="naam" required placeholder="Bijv. Henk"/>
      <label>Datum</label><input type="date" id="datum" required/>
      <label>Tijd</label>
      <select id="tijd" required>
        <option value="">Kies tijd</option>
        <option>10:00 – 12:00</option>
        <option>15:00 – 17:30</option>
        <option>18:30 – 20:00</option>
        <option>Bezoek Heit (geen specifieke tijd)</option>
      </select>
      <label>Opmerking</label><input id="opmerking" placeholder="Bijv. even koffiedrinken"/>
      <label><input type="checkbox" id="vadermee"> Vader komt mee</label>
      <button class="submit-btn">Opslaan</button>
    </form>
  </div>
</div>

<!-- Dag‑modal -->
<div class="modal" id="dayModal">
  <div class="modal-content fadeIn">
    <div class="modal-header">
      <h2 id="dayModalTitle">Inschrijvingen</h2>
      <button class="close-btn" id="closeDayModal">×</button>
    </div>
    <div id="dayModalContent"></div>
  </div>
</div>

<script>
const SHEET_URL="https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec";
let entries=[],currentWeekStart=getMondayOfWeek(new Date());

function getMondayOfWeek(d){const day=d.getDay();const diff=d.getDate()-day+(day===0?-6:1);return new Date(d.setDate(diff))}
function normalizeDate(i){if(!i)return"";const d=new Date(i);d.setMinutes(d.getMinutes()-d.getTimezoneOffset());return d.toISOString().split("T")[0]}
function timeKey(t){if(t.includes("10"))return"T1";if(t.includes("15"))return"T2";if(t.includes("18"))return"T3";if(t.includes("Bezoek"))return"T_H"}
function timeLabel(k){return k==="T1"?"10:00–12:00":k==="T2"?"15:00–17:30":k==="T3"?"18:30–20:00":"Bezoek Heit"}

async function loadEntries(){
  try{
    const res=await fetch(SHEET_URL);
    const data=await res.json();
    entries=data.slice(1).map(r=>({datum:normalizeDate(r[0]),tijd:r[1],naam:r[2],locatie:r[3],opmerking:r[4]||"",vadermee:r[5]||"Nee"}));
    renderAll();
  }catch(err){console.error(err);}
}

function getSlotStatus(date,key){
  const matches=entries.filter(e=>e.datum===date&&e.tijd===key);
  if(matches.length===0)return{text:"Vrij",class:"free"};
  if(matches.length>=2)return{text:"Vol",class:"full"};
  const vader=matches.some(e=>e.vadermee==="Ja");
  return{text:"Bezet",class:"booked",vader}
}

function renderWeek(){
  const tb=document.getElementById("weekBody");tb.innerHTML="";
  for(let i=0;i<7;i++){
    const d=new Date(currentWeekStart);d.setDate(d.getDate()+i);
    const ds=normalizeDate(d);
    const day=d.toLocaleDateString("nl-NL",{weekday:"short"}).replace(".","");
    const month=d.toLocaleDateString("nl-NL",{month:"short"});
    let tr=`<tr><td><button class="day-button" data-date="${ds}">${day} ${d.getDate()} ${month}</button></td>`;
    ["T1","T2","T3"].forEach(k=>{
      const s=getSlotStatus(ds,k);const b=s.vader?'<span class="vader-badge">Vader</span>':"";
      tr+=`<td><div class="slot ${s.class}">${s.text}${b}</div></td>`});
    tr+="</tr>";tb.insertAdjacentHTML("beforeend",tr);
  }
  document.querySelectorAll(".day-button").forEach(b=>b.onclick=()=>openDayModal(b.dataset.date))
}

function renderEntries(){
  const list=document.getElementById("entriesList");list.innerHTML="";
  entries.slice().sort((a,b)=>new Date(a.datum)-new Date(b.datum))
  .forEach(e=>{
    const extra=e.tijd==="T_H"?"<span class='bezoek-heit'>Bezoek Heit</span>":(e.vadermee==="Ja"?'<span class="vader-mee">Vader mee</span>':"");
    list.insertAdjacentHTML("beforeend",`
      <div class="entry-card fadeIn">
        <div class="top"><div class="naam">${e.naam}</div>
        <div class="datum-tijd">${e.datum} • ${timeLabel(e.tijd)}</div></div>
        <div>${e.locatie}</div>${e.opmerking?`<div>${e.opmerking}</div>`:""}${extra}
      </div>`);
  });
}

async function deleteEntry(entry){
  if(!confirm("Weet je zeker dat je deze inschrijving wilt intrekken?"))return;
  try{
    const res=await fetch(SHEET_URL,{method:"POST",headers:{"Content-Type":"application/json"},body:JSON.stringify({...entry,action:"delete"})});
    const result=await res.json();
    if(result.status==="deleted"){
      await loadEntries();
    }else{
      alert("Verwijderen mislukt of niet gevonden.");
    }
  }catch(err){alert("Verwijderen mislukt.");console.error(err);}
}

function openDayModal(date){
  const modal=document.getElementById("dayModal"),title=document.getElementById("dayModalTitle"),content=document.getElementById("dayModalContent");
  const list=entries.filter(e=>e.datum===date);title.textContent=`Inschrijvingen • ${date}`;
  if(list.length===0){content.innerHTML="<p style='color:var(--gray)'>Geen inschrijvingen.</p>";}
  else{
    content.innerHTML=list.map(e=>`
      <div class="entry-card fadeIn">
        <div class="top"><div class="naam">${e.naam}</div>
          <button class="delete-btn">🗑️</button>
        </div>
        <div class="datum-tijd">${timeLabel(e.tijd)}</div>
        <div>${e.locatie}</div>
        ${e.opmerking?`<div>${e.opmerking}</div>`:""}
        ${e.tijd==="T_H"?'<span class="bezoek-heit">Bezoek Heit</span>':(e.vadermee==="Ja"?'<span class="vader-mee">Vader mee</span>':"")}
      </div>`).join("");
    content.querySelectorAll(".delete-btn").forEach((btn,i)=>btn.onclick=()=>deleteEntry(list[i]));
  }
  modal.classList.add("active");
}

function renderAll(){
  const end=new Date(currentWeekStart);end.setDate(end.getDate()+6);
  const fmt=d=>d.toLocaleDateString("nl-NL",{day:"numeric",month:"long"});
  document.getElementById("weekLabel").textContent=`${fmt(currentWeekStart)} – ${fmt(end)}`;
  renderWeek();renderEntries();
}

// Navigatie
document.getElementById("prevWeek").onclick=()=>{currentWeekStart.setDate(currentWeekStart.getDate()-7);renderAll()}
document.getElementById("nextWeek").onclick=()=>{currentWeekStart.setDate(currentWeekStart.getDate()+7);renderAll()}

// Modals
const modal=document.getElementById("modal");
document.getElementById("fab").onclick=()=>modal.classList.add("active");
document.getElementById("closeModal").onclick=()=>modal.classList.remove("active");
modal.onclick=e=>{if(e.target===modal)modal.classList.remove("active")}
const dayModal=document.getElementById("dayModal");
document.getElementById("closeDayModal").onclick=()=>dayModal.classList.remove("active");
dayModal.onclick=e=>{if(e.target===dayModal)dayModal.classList.remove("active")}

// Form submission
document.getElementById("visitForm").onsubmit=async e=>{
  e.preventDefault();
  const entry={
    datum:normalizeDate(datum.value),
    tijd:timeKey(tijd.value),
    naam:naam.value.trim(),
    locatie:locatie.value,
    opmerking:opmerking.value.trim(),
    vadermee:vadermee.checked?"Ja":"Nee"
  };
  try{
    const res=await fetch(SHEET_URL,{method:"POST",headers:{"Content-Type":"application/json"},body:JSON.stringify(entry)});
    const result=await res.json();
    if(result.status==="ok"){
      await loadEntries(); // sync opnieuw
      modal.classList.remove("active");
      e.target.reset();
    }else{
      alert("Opslaan mislukt – probeer opnieuw.");
    }
  }catch(err){alert("Opslaan mislukt – controleer verbinding.");console.error(err);}
};

loadEntries();
</script>
</body>
</html>
