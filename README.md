<html lang="nl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Planner</title>
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
      --vader: #5ac8fa;
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

    /* ===== TOOLBAR HEADER ===== */
    .app-toolbar {
      background: white;
      border-bottom: 1px solid var(--border);
      padding: 12px 20px;
      position: sticky;
      top: 0;
      z-index: 1000;
      box-shadow: 0 2px 8px rgba(0,0,0,0.05);
      display: flex;
      align-items: center;
      justify-content: space-between;
      flex-wrap: wrap;
      gap: 15px;
      min-height: 70px;
    }

    /* Left side: Week Navigation */
    .week-nav-toolbar {
      display: flex;
      align-items: center;
      gap: 15px;
      flex: 1;
      min-width: 300px;
    }

    .week-info {
      display: flex;
      flex-direction: column;
      gap: 4px;
    }

    .week-title {
      font-size: 0.9rem;
      font-weight: 600;
      color: var(--text-secondary);
    }

    .week-range {
      font-size: 1.1rem;
      font-weight: 700;
      color: var(--text);
      white-space: nowrap;
    }

    .nav-buttons {
      display: flex;
      gap: 8px;
    }

    .nav-btn {
      width: 36px;
      height: 36px;
      border-radius: 8px;
      border: 1px solid var(--border);
      background: white;
      color: var(--primary);
      font-size: 1rem;
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

    /* Center: View Toggle */
    .view-toggle-toolbar {
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

    /* Right side: Page Tabs */
    .page-tabs {
      display: flex;
      gap: 2px;
      background: #f1f1f6;
      border-radius: 8px;
      padding: 4px;
    }

    .tab-button {
      padding: 8px 16px;
      border: none;
      background: transparent;
      border-radius: 6px;
      font-weight: 600;
      color: var(--text-secondary);
      cursor: pointer;
      transition: var(--transition);
      font-size: 0.9rem;
      white-space: nowrap;
      display: flex;
      align-items: center;
      gap: 6px;
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
    .week-content {
      margin-top: 20px;
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
      min-height: 250px;
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
      margin-top: 20px;
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

    /* ===== MODAL ===== */
    .modal-overlay {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: rgba(0,0,0,0.5);
      display: none;
      align-items: center;
      justify-content: center;
      z-index: 2000;
      padding: 20px;
    }

    .modal-overlay.active {
      display: flex;
      animation: fadeIn 0.3s ease;
    }

    .modal {
      background: white;
      border-radius: var(--radius);
      width: 100%;
      max-width: 500px;
      max-height: 90vh;
      overflow-y: auto;
      animation: slideUp 0.3s ease;
      box-shadow: 0 10px 40px rgba(0,0,0,0.2);
    }

    @keyframes slideUp {
      from { transform: translateY(50px); opacity: 0; }
      to { transform: translateY(0); opacity: 1; }
    }

    .modal-header {
      padding: 20px;
      border-bottom: 1px solid var(--border);
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .modal-header h2 {
      font-size: 1.3rem;
      font-weight: 600;
    }

    .modal-close {
      background: none;
      border: none;
      font-size: 1.5rem;
      color: var(--text-secondary);
      cursor: pointer;
      width: 30px;
      height: 30px;
      display: flex;
      align-items: center;
      justify-content: center;
      border-radius: 50%;
      transition: var(--transition);
    }

    .modal-close:hover {
      background: var(--bg);
      color: var(--text);
    }

    .modal-body {
      padding: 20px;
    }

    .form-group {
      margin-bottom: 20px;
    }

    .form-label {
      display: block;
      margin-bottom: 8px;
      font-weight: 600;
      color: var(--text);
      font-size: 0.95rem;
    }

    .form-input, .form-select {
      width: 100%;
      padding: 12px 15px;
      border: 1px solid var(--border);
      border-radius: 8px;
      font-size: 1rem;
      transition: var(--transition);
      background: white;
    }

    .form-input:focus, .form-select:focus {
      outline: none;
      border-color: var(--primary);
      box-shadow: 0 0 0 3px rgba(0, 122, 255, 0.1);
    }

    .form-checkbox {
      display: flex;
      align-items: center;
      gap: 10px;
    }

    .form-checkbox input {
      width: 18px;
      height: 18px;
    }

    .modal-footer {
      padding: 20px;
      border-top: 1px solid var(--border);
      display: flex;
      gap: 10px;
      justify-content: flex-end;
    }

    .btn {
      padding: 10px 24px;
      border-radius: 8px;
      font-weight: 600;
      cursor: pointer;
      transition: var(--transition);
      font-size: 0.95rem;
      border: none;
    }

    .btn-primary {
      background: var(--primary);
      color: white;
    }

    .btn-primary:hover {
      background: var(--primary-dark);
      transform: translateY(-1px);
    }

    .btn-secondary {
      background: var(--bg);
      color: var(--text);
      border: 1px solid var(--border);
    }

    .btn-secondary:hover {
      background: #e9ecef;
    }

    /* ===== FLOATING ACTION BUTTON ===== */
    .fab {
      position: fixed;
      bottom: 30px;
      right: 30px;
      width: 60px;
      height: 60px;
      border-radius: 50%;
      background: var(--primary);
      color: white;
      border: none;
      font-size: 1.8rem;
      cursor: pointer;
      box-shadow: 0 4px 20px rgba(0, 122, 255, 0.3);
      z-index: 100;
      transition: var(--transition);
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .fab:hover {
      transform: scale(1.1);
      box-shadow: 0 6px 25px rgba(0, 122, 255, 0.4);
    }

    /* ===== RESPONSIVE ADJUSTMENTS ===== */
    @media (max-width: 768px) {
      .app-toolbar {
        flex-direction: column;
        align-items: stretch;
        padding: 12px 15px;
        gap: 10px;
        min-height: auto;
      }
      
      .week-nav-toolbar {
        order: 1;
        min-width: auto;
        justify-content: space-between;
      }
      
      .view-toggle-toolbar {
        order: 2;
        width: 100%;
        justify-content: center;
      }
      
      .page-tabs {
        order: 3;
        width: 100%;
        justify-content: center;
      }
      
      .week-info {
        flex: 1;
      }
      
      .main-content {
        padding: 15px;
      }
      
      .week-content {
        margin-top: 10px;
      }
      
      .calendar-grid {
        grid-template-columns: repeat(2, 1fr);
      }
      
      .fab {
        bottom: 20px;
        right: 20px;
        width: 50px;
        height: 50px;
        font-size: 1.5rem;
      }
      
      .registrations-header {
        margin-top: 10px;
      }
    }

    @media (max-width: 480px) {
      .modal {
        width: 95%;
        margin: 0 auto;
      }
      
      .card-details {
        flex-direction: column;
        gap: 8px;
      }
      
      .detail-item i {
        min-width: 80px;
      }
      
      .filters {
        flex-direction: column;
      }
      
      .filter-select {
        width: 100%;
      }
      
      .view-toggle-btn span,
      .tab-button span {
        display: none;
      }
      
      .view-toggle-btn,
      .tab-button {
        padding: 8px 12px;
      }
      
      .calendar-grid {
        grid-template-columns: 1fr;
      }
      
      .week-range {
        font-size: 1rem;
      }
      
      .nav-btn {
        width: 32px;
        height: 32px;
      }
    }

    /* ===== UTILITY CLASSES ===== */
    .text-center { text-align: center; }
    .text-muted { color: var(--text-secondary); }
    .mb-20 { margin-bottom: 20px; }
    .mt-20 { margin-top: 20px; }
    .d-none { display: none !important; }
    .d-flex { display: flex; }
    .align-center { align-items: center; }
    .justify-between { justify-content: space-between; }
    .flex-wrap { flex-wrap: wrap; }
    .gap-10 { gap: 10px; }
  </style>
</head>
<body>
  <div class="app-container">
    <!-- TOOLBAR HEADER -->
    <header class="app-toolbar">
      <!-- Left: Week Navigation -->
      <div class="week-nav-toolbar">
        <div class="week-info">
          <div class="week-title">Huidige Week</div>
          <div class="week-range" id="weekRange"></div>
        </div>
        <div class="nav-buttons">
          <button class="nav-btn" id="prevWeek" title="Vorige week">←</button>
          <button class="nav-btn" id="nextWeek" title="Volgende week">→</button>
        </div>
      </div>

      <!-- Center: View Toggle (only on home page) -->
      <div class="view-toggle-toolbar" id="viewToggleContainer">
        <button class="view-toggle-btn active" data-view="grid">
          <span>🟪</span>
          <span>Kaartjes</span>
        </button>
        <button class="view-toggle-btn" data-view="table">
          <span>📊</span>
          <span>Tabel</span>
        </button>
      </div>

      <!-- Right: Page Tabs -->
      <div class="page-tabs">
        <button class="tab-button active" data-view="home">
          <span>📅</span>
          <span>Planning</span>
        </button>
        <button class="tab-button" data-view="registrations">
          <span>📋</span>
          <span>Inschrijvingen</span>
        </button>
      </div>
    </header>

    <!-- MAIN CONTENT AREA -->
    <main class="main-content">
      <!-- HOME VIEW - WEEK PLANNING -->
      <div id="homeView" class="view active">
        <div class="week-content">
          <!-- Grid View -->
          <div id="gridView" class="week-view-container active">
            <div class="calendar-grid" id="calendarGrid">
              <!-- Days will be generated here -->
            </div>
          </div>

          <!-- Table View -->
          <div id="tableView" class="week-view-container">
            <div class="calendar-table-container">
              <table class="calendar-table" id="calendarTable">
                <!-- Table will be generated here -->
              </table>
            </div>
          </div>
        </div>
      </div>

      <!-- REGISTRATIONS VIEW -->
      <div id="registrationsView" class="view">
        <div class="registrations-header">
          <h1>Alle Inschrijvingen</h1>
          <div class="filters">
            <select class="filter-select" id="filterLocation">
              <option value="">Alle locaties</option>
              <option>Moeder – Revalidatiecentrum</option>
              <option>Vader – Thuis</option>
            </select>
            <select class="filter-select" id="filterWeek">
              <option value="">Alle weken</option>
              <!-- Weeks will be populated dynamically -->
            </select>
            <select class="filter-select" id="filterStatus">
              <option value="">Alle status</option>
              <option value="upcoming">Aankomend</option>
              <option value="past">Vorige</option>
            </select>
          </div>
        </div>

        <div class="registrations-list" id="registrationsList">
          <!-- Registrations will be loaded here -->
        </div>
      </div>
    </main>

    <!-- FLOATING ACTION BUTTON -->
    <button class="fab" id="fab" title="Nieuwe afspraak">+</button>

    <!-- MODAL FOR NEW REGISTRATION -->
    <div class="modal-overlay" id="modalOverlay">
      <div class="modal">
        <div class="modal-header">
          <h2>Nieuwe Bezoekafspraak</h2>
          <button class="modal-close" id="closeModal">&times;</button>
        </div>
        
        <form id="visitForm">
          <div class="modal-body">
            <div class="form-group">
              <label class="form-label" for="modalLocation">Locatie</label>
              <select class="form-select" id="modalLocation" required>
                <option value="">Kies een locatie</option>
                <option>Moeder – Revalidatiecentrum</option>
                <option>Vader – Thuis</option>
              </select>
            </div>

            <div class="form-group">
              <label class="form-label" for="modalName">Naam</label>
              <input type="text" class="form-input" id="modalName" 
                     placeholder="Vul uw naam in" required>
            </div>

            <div class="form-group">
              <label class="form-label" for="modalDate">Datum</label>
              <input type="date" class="form-input" id="modalDate" required>
            </div>

            <div class="form-group">
              <label class="form-label" for="modalTime">Tijd</label>
              <select class="form-select" id="modalTime" required>
                <option value="">Kies een tijdslot</option>
                <option value="T4">09:00 – 10:00 (Bezoek vader)</option>
                <option value="T1">10:00 – 12:00 (Ochtend)</option>
                <option value="T2">15:00 – 17:30 (Middag)</option>
                <option value="T3">18:30 – 20:00 (Avond)</option>
              </select>
            </div>

            <div class="form-group">
              <label class="form-label" for="modalNote">Opmerking (optioneel)</label>
              <input type="text" class="form-input" id="modalNote" 
                     placeholder="Bijzonderheden of voorkeuren">
            </div>

            <div class="form-group">
              <div class="form-checkbox">
                <input type="checkbox" id="modalVaderMee">
                <label for="modalVaderMee">Vader komt mee</label>
              </div>
            </div>
          </div>

          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" id="cancelModal">
              Annuleren
            </button>
            <button type="submit" class="btn btn-primary">
              Afspraak Maken
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>

  <script>
    const SHEET_URL = "https://script.google.com/macros/s/AKfycbx_YzuFqcn8mZNeQLjlbp1vt8Ntd6S16RmawZSC4z_iql3pV2c-_dsXu1yBHNCKQmfa/exec";

    let entries = [];
    let currentWeekStart = getMondayOfWeek(new Date());
    let slotStatusCache = new Map();
    let selectedDayForModal = null;
    let selectedTimeForModal = null;
    let currentView = 'grid'; // 'grid' or 'table'
    let currentPage = 'home'; // 'home' or 'registrations'

    // ===== INITIALIZATION =====
    document.addEventListener('DOMContentLoaded', function() {
      setupNavigation();
      setupViewToggle();
      loadEntries();
      setupModal();
      setupWeekNavigation();
      setupFilters();
      updateToolbarVisibility();
    });

    // ===== TOOLBAR FUNCTIONS =====
    function updateToolbarVisibility() {
      const viewToggleContainer = document.getElementById('viewToggleContainer');
      if (currentPage === 'home') {
        viewToggleContainer.style.display = 'flex';
      } else {
        viewToggleContainer.style.display = 'none';
      }
    }

    function updateWeekRange() {
      const weekRange = document.getElementById('weekRange');
      const end = new Date(currentWeekStart);
      end.setDate(end.getDate() + 6);
      
      const startFormatted = formatDateShort(normalizeDate(currentWeekStart));
      const endFormatted = formatDateShort(normalizeDate(end));
      const weekNumber = getWeekNumber(currentWeekStart);
      
      weekRange.textContent = `Week ${weekNumber}: ${startFormatted} - ${endFormatted}`;
    }

    // ===== NAVIGATION =====
    function setupNavigation() {
      document.querySelectorAll('.tab-button').forEach(button => {
        button.addEventListener('click', function(e) {
          e.preventDefault();
          const viewId = this.dataset.view + 'View';
          
          // Update active tab
          document.querySelectorAll('.tab-button').forEach(btn => {
            btn.classList.remove('active');
          });
          this.classList.add('active');
          
          // Update current page
          currentPage = this.dataset.view;
          
          // Switch view
          document.querySelectorAll('.view').forEach(view => {
            view.classList.remove('active');
          });
          document.getElementById(viewId).classList.add('active');
          
          // Update toolbar visibility
          updateToolbarVisibility();
          
          // Refresh data for the view
          if (viewId === 'homeView') {
            renderCalendar();
          } else if (viewId === 'registrationsView') {
            renderRegistrationsList();
          }
        });
      });
    }

    // ===== VIEW TOGGLE (Grid/Table) =====
    function setupViewToggle() {
      document.querySelectorAll('.view-toggle-btn').forEach(button => {
        button.addEventListener('click', function() {
          const viewType = this.dataset.view;
          
          // Update active button
          document.querySelectorAll('.view-toggle-btn').forEach(btn => {
            btn.classList.remove('active');
          });
          this.classList.add('active');
          
          // Switch view
          currentView = viewType;
          document.querySelectorAll('.week-view-container').forEach(container => {
            container.classList.remove('active');
          });
          document.getElementById(viewType + 'View').classList.add('active');
          
          // Render the appropriate view
          if (viewType === 'grid') {
            renderCalendarGrid();
          } else {
            renderCalendarTable();
          }
        });
      });
    }

    // ===== DATE FUNCTIONS =====
    function getMondayOfWeek(date) {
      const d = new Date(date);
      d.setHours(0, 0, 0, 0);
      const day = d.getDay();
      const diff = day === 0 ? -6 : 1 - day;
      d.setDate(d.getDate() + diff);
      return d;
    }

    function normalizeDate(date) {
      if (!date) return "";
      
      const d = new Date(date);
      if (isNaN(d.getTime())) return "";
      
      const year = d.getFullYear();
      const month = String(d.getMonth() + 1).padStart(2, '0');
      const day = String(d.getDate()).padStart(2, '0');
      
      return `${year}-${month}-${day}`;
    }

    function formatDateForDisplay(dateString) {
      const [year, month, day] = dateString.split('-').map(Number);
      const date = new Date(year, month - 1, day);
      return date.toLocaleDateString('nl-NL', { 
        weekday: 'long',
        day: 'numeric',
        month: 'long'
      }).replace(/\./g, '');
    }

    function formatDateShort(dateString) {
      const [year, month, day] = dateString.split('-').map(Number);
      const date = new Date(year, month - 1, day);
      return date.toLocaleDateString('nl-NL', { 
        weekday: 'short',
        day: 'numeric',
        month: 'short'
      }).replace(/\./g, '');
    }

    function getWeekNumber(date) {
      const d = new Date(date);
      d.setHours(0, 0, 0, 0);
      d.setDate(d.getDate() + 3 - (d.getDay() + 6) % 7);
      const week1 = new Date(d.getFullYear(), 0, 4);
      return 1 + Math.round(((d.getTime() - week1.getTime()) / 86400000 - 3 + (week1.getDay() + 6) % 7) / 7);
    }

    // ===== DATA LOADING =====
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
          vadermee: r[5] || 'Nee',
          id: Math.random().toString(36).substr(2, 9)
        })).filter(e => e.datum);
        
        slotStatusCache.clear();
        renderCalendar();
        populateWeekFilter();
        renderRegistrationsList();
      } catch(e) {
        console.error("Fout bij laden:", e);
        showNotification("Kan gegevens niet laden", "error");
      }
    }

    // ===== CALENDAR RENDERING =====
    function renderCalendar() {
      if (currentView === 'grid') {
        renderCalendarGrid();
      } else {
        renderCalendarTable();
      }
      updateWeekRange();
    }

    function renderCalendarGrid() {
      const grid = document.getElementById('calendarGrid');
      
      // Clear grid
      grid.innerHTML = '';
      
      // Generate days
      for (let i = 0; i < 7; i++) {
        const day = new Date(currentWeekStart);
        day.setDate(day.getDate() + i);
        const dateString = normalizeDate(day);
        
        const dayCard = document.createElement('div');
        dayCard.className = 'day-card';
        dayCard.dataset.date = dateString;
        
        // Day header
        const dayHeader = `
          <div class="day-header">
            <div class="day-name">${day.toLocaleDateString('nl-NL', { weekday: 'long' })}</div>
            <div class="day-date">${day.getDate()} ${day.toLocaleDateString('nl-NL', { month: 'short' })}</div>
          </div>
        `;
        
        // Time slots - MET 4 SLOTS
        const timeSlots = [
          { key: 'T4', label: '09:00-10:00', type: 'vader-bezoek', name: 'Bezoek vader' },
          { key: 'T1', label: '10:00-12:00', type: 'free', name: 'Ochtend' },
          { key: 'T2', label: '15:00-17:30', type: 'free', name: 'Middag' },
          { key: 'T3', label: '18:30-20:00', type: 'free', name: 'Avond' }
        ].map(slot => {
          const status = getSlotStatus(dateString, slot.key);
          const extraClass = slot.key === 'T4' ? 'vader-bezoek' : status.class;
          
          return `
            <div class="time-slot ${extraClass}" 
                 data-date="${dateString}" 
                 data-time="${slot.key}"
                 onclick="openRegistrationModal('${dateString}', '${slot.key}')">
              <div class="slot-info">
                <span>${slot.label}</span>
                <span>${slot.key === 'T4' ? 'Vader' : status.text}</span>
                ${status.vaderBadge && slot.key !== 'T4' ? '<span class="badge vader">Vader</span>' : ''}
              </div>
            </div>
          `;
        }).join('');
        
        dayCard.innerHTML = dayHeader + `<div class="time-slots">${timeSlots}</div>`;
        grid.appendChild(dayCard);
      }
    }

    function renderCalendarTable() {
      const table = document.getElementById('calendarTable');
      
      // Clear table
      table.innerHTML = '';
      
      // Create header row
      const headerRow = document.createElement('tr');
      headerRow.innerHTML = '<th>Tijd</th>';
      
      for (let i = 0; i < 7; i++) {
        const day = new Date(currentWeekStart);
        day.setDate(day.getDate() + i);
        const dayName = day.toLocaleDateString('nl-NL', { weekday: 'short' });
        const dayNumber = day.getDate();
        const monthName = day.toLocaleDateString('nl-NL', { month: 'short' });
        
        headerRow.innerHTML += `
          <th>
            <div class="table-day-header">
              ${dayName} ${dayNumber} ${monthName}
            </div>
          </th>
        `;
      }
      table.appendChild(headerRow);
      
      // Create time slot rows - MET 4 RIJEN
      const timeSlots = [
        { key: 'T4', label: '09:00 - 10:00', period: 'Bezoek vader', isVader: true },
        { key: 'T1', label: '10:00 - 12:00', period: 'Ochtend', isVader: false },
        { key: 'T2', label: '15:00 - 17:30', period: 'Middag', isVader: false },
        { key: 'T3', label: '18:30 - 20:00', period: 'Avond', isVader: false }
      ];
      
      timeSlots.forEach((slot) => {
        const row = document.createElement('tr');
        const periodClass = slot.isVader ? 'vader-bezoek' : '';
        
        row.innerHTML = `
          <td>
            <div class="table-time-header ${periodClass}">
              <div>${slot.label}</div>
              <small>${slot.period}</small>
            </div>
          </td>
        `;
        
        for (let i = 0; i < 7; i++) {
          const day = new Date(currentWeekStart);
          day.setDate(day.getDate() + i);
          const dateString = normalizeDate(day);
          
          const status = getSlotStatus(dateString, slot.key);
          const cell = document.createElement('td');
          const slotClass = slot.isVader ? 'vader-bezoek' : status.class;
          
          cell.innerHTML = `
            <div class="table-slot ${slotClass}" 
                 data-date="${dateString}" 
                 data-time="${slot.key}"
                 onclick="openRegistrationModal('${dateString}', '${slot.key}')">
              <div class="slot-details">
                <div class="slot-status">${slot.isVader ? 'Vader' : status.text}</div>
                ${status.vaderBadge && !slot.isVader ? '<div class="slot-badges"><span class="badge vader">Vader mee</span></div>' : ''}
              </div>
            </div>
          `;
          
          row.appendChild(cell);
        }
        
        table.appendChild(row);
      });
    }

    function getSlotStatus(dateString, timeKey) {
      const cacheKey = `${dateString}-${timeKey}`;
      if (slotStatusCache.has(cacheKey)) {
        return slotStatusCache.get(cacheKey);
      }

      const matches = entries.filter(e => e.datum === dateString && e.tijd === timeKey);
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

    // ===== WEEK NAVIGATION =====
    function setupWeekNavigation() {
      document.getElementById('prevWeek').addEventListener('click', () => {
        currentWeekStart.setDate(currentWeekStart.getDate() - 7);
        renderCalendar();
      });
      
      document.getElementById('nextWeek').addEventListener('click', () => {
        currentWeekStart.setDate(currentWeekStart.getDate() + 7);
        renderCalendar();
      });
    }

    // ===== REGISTRATIONS LIST =====
    function renderRegistrationsList() {
      const container = document.getElementById('registrationsList');
      const locationFilter = document.getElementById('filterLocation').value;
      const weekFilter = document.getElementById('filterWeek').value;
      const statusFilter = document.getElementById('filterStatus').value;
      
      let filtered = [...entries];
      
      // Apply filters
      if (locationFilter) {
        filtered = filtered.filter(e => e.locatie === locationFilter);
      }
      
      if (weekFilter) {
        filtered = filtered.filter(e => getWeekNumber(e.datum) === parseInt(weekFilter));
      }
      
      if (statusFilter === 'upcoming') {
        const today = normalizeDate(new Date());
        filtered = filtered.filter(e => e.datum >= today);
      } else if (statusFilter === 'past') {
        const today = normalizeDate(new Date());
        filtered = filtered.filter(e => e.datum < today);
      }
      
      // Sort by date (newest first)
      filtered.sort((a, b) => new Date(b.datum) - new Date(a.datum));
      
      if (filtered.length === 0) {
        container.innerHTML = `
          <div class="empty-state">
            <div>📭</div>
            <h3>Geen inschrijvingen gevonden</h3>
            <p class="text-muted">${locationFilter || weekFilter || statusFilter ? 'Probeer andere filters' : 'Maak je eerste afspraak!'}</p>
          </div>
        `;
        return;
      }
      
      container.innerHTML = filtered.map(entry => {
        const timeLabel = entry.tijd === 'T1' ? '10:00-12:00' : 
                         entry.tijd === 'T2' ? '15:00-17:30' : 
                         entry.tijd === 'T3' ? '18:30-20:00' : '09:00-10:00 (Vader)';
        
        return `
          <div class="registration-card">
            <div class="card-header">
              <h3>${entry.naam}</h3>
              <div class="card-date">${formatDateForDisplay(entry.datum)}</div>
            </div>
            
            <div class="card-details">
              <div class="detail-item">
                <i>Tijd:</i> <span>${timeLabel}</span>
              </div>
              <div class="detail-item">
                <i>Locatie:</i> <span>${entry.locatie}</span>
              </div>
            </div>
            
            ${entry.opmerking ? `
              <div class="detail-item">
                <i>Opmerking:</i> <span>${entry.opmerking}</span>
              </div>
            ` : ''}
            
            <div class="card-footer">
              <div class="tags">
                ${entry.vadermee === 'Ja' ? '<span class="tag vader">Vader mee</span>' : ''}
                ${entry.opmerking ? '<span class="tag opmerking">Heeft opmerking</span>' : ''}
                ${entry.tijd === 'T4' ? '<span class="tag vader">Bezoek vader</span>' : ''}
              </div>
              <div class="text-muted">
                Gemaakt: ${new Date(entry.datum).toLocaleDateString('nl-NL')}
              </div>
            </div>
          </div>
        `;
      }).join('');
    }

    function populateWeekFilter() {
      const filter = document.getElementById('filterWeek');
      const weeks = new Set();
      
      entries.forEach(entry => {
        weeks.add(getWeekNumber(entry.datum));
      });
      
      // Clear existing options except first
      filter.innerHTML = '<option value="">Alle weken</option>';
      
      // Add week options
      Array.from(weeks).sort((a, b) => b - a).forEach(week => {
        const option = document.createElement('option');
        option.value = week;
        option.textContent = `Week ${week}`;
        filter.appendChild(option);
      });
    }

    function setupFilters() {
      ['filterLocation', 'filterWeek', 'filterStatus'].forEach(id => {
        document.getElementById(id).addEventListener('change', renderRegistrationsList);
      });
    }

    // ===== MODAL FUNCTIONS =====
    function setupModal() {
      const modal = document.getElementById('modalOverlay');
      const fab = document.getElementById('fab');
      const closeBtn = document.getElementById('closeModal');
      const cancelBtn = document.getElementById('cancelModal');
      const form = document.getElementById('visitForm');
      
      // Open modal
      fab.addEventListener('click', () => {
        selectedDayForModal = null;
        selectedTimeForModal = null;
        resetModalForm();
        modal.classList.add('active');
      });
      
      // Close modal
      closeBtn.addEventListener('click', () => modal.classList.remove('active'));
      cancelBtn.addEventListener('click', () => modal.classList.remove('active'));
      modal.addEventListener('click', (e) => {
        if (e.target === modal) modal.classList.remove('active');
      });
      
      // Form submission
      form.addEventListener('submit', async (e) => {
        e.preventDefault();
        
        const entry = {
          datum: normalizeDate(document.getElementById('modalDate').value),
          tijd: document.getElementById('modalTime').value,
          naam: document.getElementById('modalName').value.trim(),
          locatie: document.getElementById('modalLocation').value,
          opmerking: document.getElementById('modalNote').value.trim(),
          vadermee: document.getElementById('modalVaderMee').checked ? 'Ja' : 'Nee'
        };
        
        if (!validateEntry(entry)) return;
        
        try {
          // Simulate API call
          await fetch(SHEET_URL, {
            method: 'POST',
            mode: 'no-cors',
            body: JSON.stringify(entry)
          });
          
          // Add to local data
          entries.push(entry);
          slotStatusCache.clear();
          
          // Update UI
          renderCalendar();
          renderRegistrationsList();
          modal.classList.remove('active');
          showNotification("Afspraak succesvol gemaakt!", "success");
          
          // Reset form
          form.reset();
          
        } catch(err) {
          console.error(err);
          showNotification("Fout bij opslaan", "error");
        }
      });
    }

    function openRegistrationModal(dateString, timeKey) {
      const status = getSlotStatus(dateString, timeKey);
      if (status.class === 'full') {
        showNotification("Dit tijdslot is vol", "warning");
        return;
      }
      
      selectedDayForModal = dateString;
      selectedTimeForModal = timeKey;
      
      // Set modal values
      document.getElementById('modalDate').value = dateString;
      document.getElementById('modalTime').value = timeKey;
      
      // Open modal
      document.getElementById('modalOverlay').classList.add('active');
    }

    function resetModalForm() {
      document.getElementById('visitForm').reset();
      document.getElementById('modalDate').value = '';
      document.getElementById('modalTime').value = '';
    }

    function validateEntry(entry) {
      if (!entry.datum) {
        showNotification("Selecteer een datum", "error");
        return false;
      }
      
      if (!entry.tijd) {
        showNotification("Selecteer een tijd", "error");
        return false;
      }
      
      if (!entry.locatie) {
        showNotification("Selecteer een locatie", "error");
        return false;
      }
      
      if (!entry.naam) {
        showNotification("Vul een naam in", "error");
        return false;
      }
      
      // Check for duplicates
      const isDuplicate = entries.some(e => 
        e.datum === entry.datum && 
        e.tijd === entry.tijd && 
        e.locatie === entry.locatie
      );
      
      if (isDuplicate) {
        showNotification("Dit tijdslot is al bezet op deze locatie", "error");
        return false;
      }
      
      return true;
    }

    // ===== NOTIFICATION SYSTEM =====
    function showNotification(message, type = 'info') {
      // Remove existing notification
      const existing = document.querySelector('.notification');
      if (existing) existing.remove();
      
      const colors = {
        success: '#34c759',
        error: '#ff3b30',
        warning: '#ff9500',
        info: '#007aff'
      };
      
      const notification = document.createElement('div');
      notification.className = 'notification';
      notification.style.cssText = `
        position: fixed;
        top: 80px;
        right: 20px;
        background: ${colors[type]};
        color: white;
        padding: 12px 24px;
        border-radius: 8px;
        box-shadow: 0 4px 12px rgba(0,0,0,0.15);
        z-index: 3000;
        animation: slideInRight 0.3s ease;
        max-width: 300px;
      `;
      
      notification.textContent = message;
      document.body.appendChild(notification);
      
      setTimeout(() => {
        notification.style.animation = 'slideOutRight 0.3s ease';
        setTimeout(() => notification.remove(), 300);
      }, 3000);
    }

    // Add notification animations
    const style = document.createElement('style');
    style.textContent = `
      @keyframes slideInRight {
        from { transform: translateX(100%); opacity: 0; }
        to { transform: translateX(0); opacity: 1; }
      }
      @keyframes slideOutRight {
        from { transform: translateX(0); opacity: 1; }
        to { transform: translateX(100%); opacity: 0; }
      }
    `;
    document.head.appendChild(style);
  </script>
</body>
</html>