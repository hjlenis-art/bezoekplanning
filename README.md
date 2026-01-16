# bezoekplanning
<!DOCTYPE html>
<html lang="nl">
<head>
  <meta charset="UTF-8" />
  <title>Bezoekplanning Revalidatie</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f4f6f8;
      padding: 20px;
    }
    .container {
      max-width: 700px;
      margin: auto;
      background: #ffffff;
      padding: 25px;
      border-radius: 8px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    }
    h1 {
      text-align: center;
    }
    label {
      display: block;
      margin-top: 15px;
      font-weight: bold;
    }
    input, select {
      width: 100%;
      padding: 10px;
      margin-top: 5px;
    }
    .checkbox {
      margin-top: 10px;
    }
    button {
      margin-top: 25px;
      width: 100%;
      padding: 12px;
      background: #0078d4;
      color: #fff;
      border: none;
      font-size: 16px;
      border-radius: 5px;
      cursor: pointer;
    }
    button:hover {
      background: #005fa3;
    }
    .info {
      background: #eef4ff;
      padding: 15px;
      border-radius: 5px;
      margin-bottom: 20px;
      font-size: 14px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Bezoekplanning</h1>

    <div class="info">
      <strong>Bezoektijden revalidatiecentrum:</strong><br />
      Doordeweeks: 15:00 – 17:30 en 18:30 – 20:00<br />
      Weekend: extra tijdstip 10:00 – 12:00<br />
      <br />
      Startdatum planning: <strong>17 januari 2026</strong>
    </div>

    <form>
      <label>Waar gaat het bezoek naartoe?</label>
      <select required>
        <option value="">Maak een keuze</option>
        <option>Moeder – Revalidatiecentrum</option>
        <option>Vader – Thuis</option>
      </select>

      <label>Naam bezoeker(s)</label>
      <input type="text" placeholder="Bijv. Jan & Petra + kinderen" required />

      

      <label>Datum bezoek</label>
      <input type="date" min="2026-01-17" required />

      <label>Tijdstip</label>
      <select required>
        <option value="">Kies tijdstip</option>
        <option>15:00 – 17:30 (doordeweeks)</option>
        <option>18:30 – 20:00 (doordeweeks)</option>
        <option>10:00 – 12:00 (weekend)</option>
      </select>

      <div class="checkbox">
        <label>
          <input type="checkbox" /> Vader wordt meegenomen naar moeder
        </label>
      </div>

      <label>Opmerking (optioneel)</label>
      <input type="text" placeholder="Bijv. wij halen vader thuis op" />

      <button type="submit">Inschrijven</button>
    </form>

    <hr />
    <h2>Weekoverzicht beschikbaarheid – Moeder (Revalidatie)</h2>
    <p>Selecteer een week om meerdere dagen tegelijk te bekijken.</p>
    <div style="margin-bottom:10px;">
      <button type="button">◀ Vorige week</button>
      <strong style="margin:0 10px;">Week 3 (12–18 jan 2026)</strong>
      <button type="button">Volgende week ▶</button>
    </div>
    <p>Groen = 0 boekingen (vrij) &nbsp; | &nbsp; Oranje = 1 boeking &nbsp; | &nbsp; Rood = 2 boekingen (vol)</p>
    <p style="font-size:13px;">Bij 2 boekingen is het tijdslot vol en kan er geen 3e inschrijving meer plaatsvinden.</p>

    <table id="planning" border="1" width="100%" cellpadding="8" style="border-collapse:collapse; text-align:center;">
      <thead>
        <tr>
          <th>Datum</th>
          <th>10:00 – 12:00</th>
          <th>15:00 – 17:30</th>
          <th>18:30 – 20:00</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>17-01-2026</td>
          <td style="background:#c6efce;">Vrij</td>
          <td style="background:#c6efce;">Vrij</td>
          <td style="background:#c6efce;">Vrij</td>
        </tr>
        <tr>
          <td>18-01-2026</td>
          <td style="background:#ffc7ce;">Bezet</td>
          <td style="background:#c6efce;">Vrij</td>
          <td style="background:#c6efce;">Vrij</td>
        </tr>
      </tbody>
    </table>

    <h2 style="margin-top:30px;">Overzicht beschikbaarheid – Vader (thuis)</h2>
    <p>Bezoekmomenten bij vader zijn flexibel en bedoeld om te kijken hoe het gaat.</p>

    <table border="1" width="100%" cellpadding="8" style="border-collapse:collapse; text-align:center;">
      <thead>
        <tr>
          <th>Datum</th>
          <th>Ochtend</th>
          <th>Middag</th>
          <th>Avond</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>17-01-2026</td>
          <td style="background:#c6efce;">Vrij</td>
          <td style="background:#ffc7ce;">Bezet</td>
          <td style="background:#c6efce;">Vrij</td>
        </tr>
      </tbody>
    </table>

    <p style="font-size:13px; margin-top:10px;">Dit overzicht kan automatisch worden bijgewerkt bij nieuwe inschrijvingen.</p>
  </div>
</body>
</html>
