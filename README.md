<html lang="nl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Bezoekplanner</title>
  <style>
    /* ===== VARIABLES & RESET ===== */
    :root {
      --primary: #007aff;
      --primary-dark: #0062cc;
      --secondary: #5856d6;
      --bg: #f8f9fa;
      --card: #ffffff;
      --text: #1c1c1e;
      --text-secondary: #8e8e93;
      --free: #34c759;
      --booked: #ff9500;
      --full: #ff3b30;
      --vader: #5ac8fa; /* Nieuwe kleur voor bezoek vader */
      --border: #e5e5ea;
      --success: #34c759;
      --warning: #ff9500;
      --error: #ff3b30;
      --shadow: 0 2px 10px rgba(0,0,0,0.08);
      --radius: 12px;
      --transition: all 0.3s ease;
    }

    * { 
      margin: 0; 
      padding: 0; 
      box-sizing: border-box; 
    }

    html, body {
      height: 100%;
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
      background: var(--bg);
      color: var(--text);
      line-height: 1.5;
      overflow-x: hidden;
    }

    /* ===== LAYOUT CONTAINER ===== */
    .app-container {
      display: flex;
      flex-direction: column;
      min-height: 100vh;
      max-width: 100vw;
      overflow: hidden;
    }

    /* ===== HEADER & NAVIGATION ===== */
    .app-header {
      background: white;
      border-bottom: 1px solid var(--border);
      padding: 0;
      position: sticky;
      top: 0;
      z-index: 1000;
      box-shadow: 0 2px 8px rgba(0,0,0,0.05);
    }

    .header-content {
      max-width: 1200px;
      margin: 0 auto;
      padding: 0 20px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      height: 60px;
    }

    .logo {
      font-size: 1.4rem;
      font-weight: 700;
      color: var(--primary);
      text-decoration: none;
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .logo-icon {
      font-size: 1.8rem;
    }

    .nav-tabs {
      display: flex;
      gap: 2px;
      background: #f1f1f6;
      border-radius: 10px;
      padding: 4px;
    }

    .tab-button {
      padding: 8px 20px;
      border: none;
      background: transparent;
      border-radius: 8px;
      font-weight: 600;
      color: var(--text-secondary);
      cursor: pointer;
      transition: var(--transition);
      font-size: 0.95rem;
      white-space: nowrap;
    }

    .tab-button.active {
      background: white;
      color: var(--primary);
      box-shadow: var(--shadow);
    }

    /* ===== MAIN CONTENT AREA ===== */
    .main-content {
      flex: 1;
      max-width: 1200px;
      margin: 0 auto;
      padding: 20px;
      width: 100%;
      overflow: hidden;
    }

    /* ===== VIEWS ===== */
    .view {
      display: none;
      animation: fadeIn 0.3s ease;
      height: 100%;
      overflow-y: auto;
    }

    .view.active {
      display: block;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }

    /* ===== HOME VIEW - WEEK PLANNING ===== */
    .week-header {
      background: white;
      border-radius: var(--radius);
      padding: 20px;
      margin-bottom: 20px;
      box-shadow: var(--shadow);
    }

    .week-nav {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 15px;
      flex-wrap: wrap;
      gap: 15px;
    }

    .week-nav h1 {
      font-size: 1.5rem;
      font-weight: 700;
      color: var(--text);
    }

    .week-controls {
      display: flex;
      gap: 10px;
      align-items: center;
      flex-wrap: wrap;
    }

    .view-toggle {
      display: flex;
      background: #f1f1f6;
      border-radius: 8px;
      padding: 4px;
      gap: 2px;
    }

    .view-toggle-btn {
      padding: 8px 16px;
      border: none;
      background: transparent;
      border-radius: 6px;
      font-weight: 600;
      font-size: 0.9rem;
      color: var(--text-secondary);
      cursor: pointer;
      transition: var(--transition);
      display: flex;
      align-items: center;
      gap: 6px;
      white-space: nowrap;
    }

    .view-toggle-btn.active {
      background: white;
      color: var(--primary);
      box-shadow: var(--shadow);
    }

    .nav-btn {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      border: 1px solid var(--border);
      background: white;
      color: var(--primary);
      font-size: 1.2rem;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: var(--transition);
    }

    .nav-btn:hover {
      background: var(--primary);
      color: white;
      border-color: var(--primary);
    }

    .current-week {
      font-size: 1.1rem;
      font-weight: 600;
      color: var(--text);
      text-align: center;
      min-width: 200px;
    }

    /* ===== WEEK VIEW CONTAINERS ===== */
    .week-view-container {
      display: none;
    }

    .week-view-container.active {
      display: block;
      animation: fadeIn 0.3s ease;
    }

    /* ===== CALENDAR GRID VIEW ===== */
    .calendar-grid {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      gap: 10px;
      margin-bottom: 30px;
    }

    @media (max-width: 1024px) {
      .calendar-grid {
        grid-template-columns: repeat(4, 1fr);
      }
    }

    @media (max-width: 768px) {
      .calendar-grid {
        grid-template-columns: repeat(3, 1fr);
      }
    }

    @media (max-width: 480px) {
      .calendar-grid {
        grid-template-columns: repeat(2, 1fr);
      }
    }

    .day-card {
      background: white;
      border-radius: var(--radius);
      padding: 15px;
      box-shadow: var(--shadow);
      transition: var(--transition);
      border: 1px solid transparent;
      min-height: 250px; /* Hoger vanwege extra tijdslot */
    }

    .day-card:hover {
      transform: translateY(-2px);
      border-color: var(--primary);
    }

    .day-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 15px;
      padding-bottom: 10px;
      border-bottom: 1px solid var(--border);
    }

    .day-name {
      font-weight: 600;
      font-size: 0.9rem;
      color: var(--text-secondary);
    }

    .day-date {
      font-weight: 700;
      font-size: 1.2rem;
      color: var(--text);
    }

    .time-slots {
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    .time-slot {
      padding: 8px;
      border-radius: 8px;
      font-size: 0.85rem;
      font-weight: 500;
      text-align: center;
      cursor: pointer;
      transition: var(--transition);
      border: 1px solid transparent;
    }

    .time-slot:hover:not(.full) {
      transform: scale(1.02);
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }

    .time-slot.free {
      background: rgba(52, 199, 89, 0.1);
      color: var(--free);
      border-color: rgba(52, 199, 89, 0.3);
    }

    .time-slot.booked {
      background: rgba(255, 149, 0, 0.1);
      color: var(--booked);
      border-color: rgba(255, 149, 0, 0.3);
    }

    .time-slot.full {
      background: rgba(255, 59, 48, 0.1);
      color: var(--full);
      border-color: rgba(255, 59, 48, 0.3);
      cursor: not-allowed;
      opacity: 0.7;
    }

    .time-slot.vader-bezoek {
      background: rgba(90, 200, 250, 0.1);
      color: var(--vader);
      border-color: rgba(90, 200, 250, 0.3);
    }

    .slot-info {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 4px;
      flex-wrap: wrap;
    }

    .badge {
      font-size: 0.7rem;
      padding: 2px 6px;
      border-radius: 10px;
      white-space: nowrap;
    }

    .badge.vader {
      background: var(--vader);
      color: white;
    }

    /* ===== TABLE VIEW ===== */
    .calendar-table-container {
      overflow-x: auto;
      margin-bottom: 30px;
      background: white;
      border-radius: var(--radius);
      box-shadow: var(--shadow);
    }

    .calendar-table {
      width: 100%;
      border-collapse: collapse;
      min-width: 800px;
    }

    .calendar-table th {
      background: #f8f9fa;
      color: var(--text-secondary);
      font-weight: 600;
      font-size: 0.9rem;
      padding: 16px 12px;
      text-align: center;
      border-bottom: 1px solid var(--border);
      position: sticky;
      top: 0;
    }

    .calendar-table td {
      padding: 12px;
      text-align: center;
      border-bottom: 1px solid var(--border);
      vertical-align: top;
      min-width: 120px;
    }

    .calendar-table tr:hover {
      background: #fafafa;
    }

    .table-day-header {
      background: var(--primary);
      color: white;
      padding: 12px;
      border-radius: 6px;
      font-weight: 600;
      margin-bottom: 8px;
      display: block;
    }

    .table-time-header {
      background: #f1f1f6;
      color: var(--text-secondary);
      padding: 8px;
      border-radius: 6px;
      font-weight: 600;
      margin-bottom: 8px;
      font-size: 0.85rem;
      display: block;
    }

    .table-slot {
      padding: 10px;
      border-radius: 8px;
      margin: 4px 0;
      cursor: pointer;
      transition: var(--transition);
      border: 1px solid transparent;
    }

    .table-slot:hover:not(.full) {
      transform: translateY(-1px);
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }

    .table-slot.free {
      background: rgba(52, 199, 89, 0.1);
      color: var(--free);
      border-color: rgba(52, 199, 89, 0.2);
    }

    .table-slot.booked {
      background: rgba(255, 149, 0, 0.1);
      color: var(--booked);
      border-color: rgba(255, 149, 0, 0.2);
    }

    .table-slot.full {
      background: rgba(255, 59, 48, 0.1);
      color: var(--full);
      border-color: rgba(255, 59, 48, 0.2);
      cursor: not-allowed;
      opacity: 0.7;
    }

    .table-slot.vader-bezoek {
      background: rgba(90, 200, 250, 0.1);
      color: var(--vader);
      border-color: rgba(90, 200, 250, 0.2);
    }

    .slot-details {
      display: flex;
      flex-direction: column;
      gap: 4px;
      align-items: center;
    }

    .slot-status {
      font-weight: 600;
      font-size: 0.85rem;
    }

    .slot-badges {
      display: flex;
      gap: 4px;
      flex-wrap: wrap;
      justify-content: center;
    }

    /* ===== REGISTRATIONS VIEW ===== */
    .registrations-header {
      background: white;
      border-radius: var(--radius);
      padding: 20px;
      margin-bottom: 20px;
      box-shadow: var(--shadow);
    }

    .registrations-header h1 {
      font-size: 1.5rem;
      font-weight: 700;
      margin-bottom: 15px;
    }

    .filters {
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
    }

    .filter-select {
      padding: 8px 15px;
      border: 1px solid var(--border);
      border-radius: 8px;
      background: white;
      font-size: 0.9rem;
      min-width: 150px;
    }

    .registrations-list {
      display: flex;
      flex-direction: column;
      gap: 15px;
    }

    .registration-card {
      background: white;
      border-radius: var(--radius);
      padding: 20px;
      box-shadow: var(--shadow);
      transition: var(--transition);
    }

    .registration-card:hover {
      transform: translateX(5px);
      box-shadow: 0 4px 15px rgba(0,0,0,0.1);
    }

    .card-header {
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
      margin-bottom: 10px;
      flex-wrap: wrap;
      gap: 10px;
    }

    .card-header h3 {
      font-size: 1.2rem;
      color: var(--text);
    }

    .card-date {
      background: var(--primary);
      color: white;
      padding: 4px 12px;
      border-radius: 20px;
      font-size: 0.85rem;
      font-weight: 600;
    }

    .card-details {
      display: flex;
      flex-wrap: wrap;
      gap: 15px;
      margin: 15px 0;
    }

    .detail-item {
      display: flex;
      align-items: center;
      gap: 8px;
      font-size: 0.95rem;
    }

    .detail-item i {
      color: var(--primary);
      font-style: normal;
      font-weight: 600;
      min-width: 100px;
    }

    .card-footer {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-top: 15px;
      padding-top: 15px;
      border-top: 1px solid var(--border);
      flex-wrap: wrap;
      gap: 10px;
    }

    .tags {
      display: flex;
      gap: 8px;
      flex-wrap: wrap;
    }

    .tag {
      padding: 4px 10px;
      border-radius: 6px;
      font-size: 0.8rem;
      font-weight: 500;
    }

    .tag.vader {
      background: rgba(90, 200, 250, 0.1);
      color: var(--vader);
    }

    .tag.opmerking {
      background: rgba(142, 142, 147, 0.1);
      color: var(--text-secondary);
    }

    .empty-state {
      text-align: center;
      padding: 40px 20px;
      color: var(--text-secondary);
    }

    .empty-state i {
      font-size: 3rem;
      margin-bottom: 15px;
      opacity: 0.5;
      font-style: normal;
    }
