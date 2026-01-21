<html lang="nl">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Planner API Test</title>
<style>
body{font-family:sans-serif;background:#f7f7f9;color:#222;margin:2em}
h1{color:#007aff}
label{display:block;margin-top:1em}
input,select{padding:.5em;width:100%;max-width:280px}
button{margin-top:1em;padding:.6em 1em;background:#007aff;color:white;
border:none;border-radius:6px;cursor:pointer}
pre{background:#fff;border:1px solid #ccc;padding:.5em;margin-top:1em;white-space:pre-wrap}
.result.ok{color:green}
.result.err{color:red}
</style>
</head>
<body>
<h1>🧩 Google Sheet API Test</h1>

<p>Voer hier testgegevens in om je doPost/doGet te controleren.</p>

<label>Naam <input id="naam" value="Testpersoon"/></label>
<label>Datum <input type="date" id="datum"/></label>
<label>Tijdvak
  <select id="tijd">
    <option value="T1">10:00–12:00</option>
    <option value="T2">15:00–17:30</option>
    <option value="T3">18:30–20:00</option>
  </select>
</label>
<label>Locatie
  <select id="locatie">
    <option>Moeder – Revalidatiecentrum</option>
    <option>Vader – Thuis</option>
  </select>
</label>

<button id="btnAdd">➕ Toevoegen</button>
<button id="btnDelete" style="background:#ff3b30">🗑️ Verwijderen</button>
<button id="btnLoad" style="background:#34c759">🔄 Datalist</button>

<pre id="output">Resultaat verschijnt hier...</pre>

<script>
const SHEET_URL = "🟢 VUL HIER JOUW /exec URL IN 🟢";

function show(msg, ok=true){
  const out=document.getElementById("output");
  out.textContent=msg;
  out.className="result "+(ok?"ok":"err");
}

document.getElementById("btnAdd").onclick = async()=>{
  const body={
    datum:document.getElementById("datum").value,
    tijd:document.getElementById("tijd").value,
    naam:document.getElementById("naam").value,
    locatie:document.getElementById("locatie").value,
    vadermee:"Nee"
  };
  try{
    const r=await fetch(SHEET_URL,{
      method:"POST",
      headers:{"Content-Type":"application/json"},
      body:JSON.stringify(body)
    });
    const j=await r.json();
    show("✅ Toegevoegd: "+JSON.stringify(j), j.status==="ok");
  }catch(e){show("❌ Fout: "+e,false);}
};

document.getElementById("btnDelete").onclick = async()=>{
  const body={
    action:"delete",
    datum:document.getElementById("datum").value,
    tijd:document.getElementById("tijd").value,
    naam:document.getElementById("naam").value,
    locatie:document.getElementById("locatie").value
  };
  try{
    const r=await fetch(SHEET_URL,{
      method:"POST",
      headers:{"Content-Type":"application/json"},
      body:JSON.stringify(body)
    });
    const j=await r.json();
    show("🗑️ Delete response: "+JSON.stringify(j), j.status==="deleted");
  }catch(e){show("❌ Fout: "+e,false);}
};

document.getElementById("btnLoad").onclick = async()=>{
  try{
    const r=await fetch(SHEET_URL);
    const j=await r.json();
    show("📄 Sheet data:\n"+JSON.stringify(j,null,2));
  }catch(e){show("❌ Fout: "+e,false);}
};
</script>
</body>
</html>
