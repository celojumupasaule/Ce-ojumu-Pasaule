<!DOCTYPE html>
<html lang="lv">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Ceļojumu Pasaule</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(to right, #e0f7fa, #f1f8e9);
      color: #333;
      scroll-behavior: smooth;
      transition: background 0.5s, color 0.5s;
    }

    body.dark {
      background: linear-gradient(to right, #2c3e50, #34495e);
      color: #ecf0f1;
    }

    header {
      background: rgba(0, 123, 255, 0.95);
      padding: 1em;
      position: sticky;
      top: 0;
      z-index: 1000;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    }

    nav {
      display: flex;
      justify-content: space-between;
      align-items: center;
      max-width: 1200px;
      margin: auto;
    }

    .logo {
      font-size: 1.8em;
      font-weight: bold;
      color: #fff;
    }

    .nav-links {
      list-style: none;
      display: flex;
      gap: 25px;
    }

    .nav-links li a {
      color: #fff;
      text-decoration: none;
      font-size: 1.1em;
      transition: color 0.3s;
    }

    .nav-links li a:hover {
      color: #ffd700;
    }

    .burger {
      display: none;
      font-size: 1.5em;
      color: #fff;
      cursor: pointer;
    }

    .hero {
      background: linear-gradient(rgba(0,0,0,0.4), rgba(0,0,0,0.4)), url('https://images.unsplash.com/photo-1506748686214-e9df14d4d9d0') center/cover no-repeat;
      color: white;
      padding: 120px 20px;
      text-align: center;
      animation: fadeIn 2s ease-in-out;
    }

    .btn {
      background-color: #ff9800;
      color: white;
      padding: 12px 25px;
      text-decoration: none;
      border-radius: 5px;
      transition: background 0.3s ease;
    }

    .btn:hover {
      background-color: #e65100;
    }

    .destinations {
      padding: 60px 20px;
      text-align: center;
    }

    .filter-buttons {
      margin: 20px 0;
    }

    .filter-btn {
      background: #4caf50;
      color: white;
      border: none;
      padding: 10px 20px;
      margin: 5px;
      border-radius: 5px;
      cursor: pointer;
      transition: background 0.3s;
    }

    .filter-btn:hover, .filter-btn.active {
      background: #388e3c;
    }

    .cards {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 20px;
    }

    .card {
      background: #fff;
      border-radius: 10px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
      overflow: hidden;
      width: 250px;
      cursor: pointer;
      transition: transform 0.3s ease, box-shadow 0.3s ease;
    }

    .card img {
      width: 100%;
      height: 120px;
      object-fit: cover;
    }

    .card p {
      padding: 0 10px;
      font-size: 0.9em;
      line-height: 1.4;
    }

    body.dark .card {
      background: #34495e;
      color: #ecf0f1;
    }

    .card.hidden {
      display: none;
    }

    .modal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.7);
      z-index: 2000;
      justify-content: center;
      align-items: center;
    }

    .modal-content {
      background: #fff;
      padding: 20px;
      border-radius: 10px;
      max-width: 600px;
      width: 90%;
      max-height: 80vh;
      overflow-y: auto;
      position: relative;
    }

    body.dark .modal-content {
      background: #34495e;
      color: #ecf0f1;
    }

    .close {
      position: absolute;
      top: 10px;
      right: 15px;
      font-size: 1.5em;
      cursor: pointer;
    }

    .gallery {
      display: flex;
      gap: 10px;
      margin-top: 15px;
    }

    .gallery img {
      width: 180px;
      height: 120px;
      object-fit: cover;
      border-radius: 5px;
    }

    .reviews {
      background: linear-gradient(to right, #fff1f2, #f8bbd0);
      padding: 60px 20px;
      text-align: center;
      font-style: italic;
    }

    body.dark .reviews {
      background: linear-gradient(to right, #2c3e50, #34495e);
    }

    .review-slider blockquote {
      max-width: 600px;
      margin: auto;
      opacity: 0;
      transition: opacity 0.5s ease;
    }

    .review-slider blockquote.active {
      opacity: 1;
    }

    .calculator, .car-calculator {
      padding: 60px 20px;
      text-align: center;
      background: #e8f5e9;
    }

    body.dark .calculator, body.dark .car-calculator {
      background: #2c3e50;
    }

    .calculator form, .car-calculator form {
      max-width: 500px;
      margin: auto;
      display: flex;
      flex-direction: column;
      gap: 15px;
    }

    .calculator select, .calculator input, .car-calculator select, .car-calculator input {
      padding: 12px;
      border: 1px solid #ccc;
      border-radius: 5px;
      font-size: 1em;
    }

    #distance[readonly] {
      background-color: #f0f0f0;
      cursor: not-allowed;
    }

    #calc-result, #car-calc-result {
      margin-top: 20px;
      font-size: 1.2em;
      color: #4caf50;
    }

    .contact {
      padding: 60px 20px;
      text-align: center;
      background: #fffde7;
    }

    body.dark .contact {
      background: #34495e;
    }

    .map {
      padding: 60px 20px;
      text-align: center;
    }

    #interactive-map {
      width: 90%;
      max-width: 800px;
      height: 400px;
      margin: auto;
      border-radius: 10px;
    }

    footer {
      background: #333;
      color: #fff;
      padding: 25px;
      text-align: center;
    }

    body.dark footer {
      background: #1c2526;
    }

    .scroll-top {
      position: fixed;
      bottom: 20px;
      right: 20px;
      background: #ff9800;
      color: white;
      border: none;
      padding: 10px 15px;
      border-radius: 50%;
      cursor: pointer;
      display: none;
    }

    .theme-toggle {
      position: absolute;
      top: 10px;
      right: 60px;
      padding: 5px 10px;
      background: #fff;
      border-radius: 5px;
      cursor: pointer;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(20px); }
      to { opacity: 1; transform: translateY(0); }
    }

    @media (max-width: 768px) {
      .nav-links {
        display: none;
        flex-direction: column;
        position: absolute;
        top: 60px;
        right: 0;
        background: rgba(0, 123, 255, 0.95);
        width: 200px;
        padding: 20px;
      }

      .nav-links.active {
        display: flex;
      }

      .burger {
        display: block;
      }

      .gallery {
        flex-direction: column;
        align-items: center;
      }

      .gallery img {
        width: 100%;
        max-width: 300px;
      }
    }
  </style>
</head>
<body>
  <div class="lang-select">
    <label for="language">Valoda:</label>
    <select id="language" onchange="changeLanguage(this.value)">
      <option value="lv">Latviešu</option>
      <option value="en">English</option>
    </select>
  </div>
  <button class="theme-toggle" onclick="toggleTheme()">🌙</button>

  <header>
    <nav>
      <div class="logo">Ceļojumu Pasaule</div>
      <div class="burger" onclick="toggleMenu()">☰</div>
      <ul class="nav-links">
        <li><a href="#galamērķi">Galamērķi</a></li>
        <li><a href="#atsauksmes">Atsauksmes</a></li>
        <li><a href="#kalkulators">Kalkulators</a></li>
        <li><a href="#auto-kalkulators">Auto kalkulators</a></li>
        <li><a href="#kontakti">Kontakti</a></li>
      </ul>
    </nav>
  </header>

  <section class="hero">
    <h1 data-lv="Atklāj pasauli ar mums" data-en="Discover the world with us">Atklāj pasauli ar mums</h1>
    <p data-lv="Tavs sapņu ceļojums sākas šeit!" data-en="Your dream journey starts here!">Tavs sapņu ceļojums sākas šeit!</p>
    <a href="#galamērķi" class="btn">Skatīt galamērķus</a>
  </section>

  <section class="destinations" id="galamērķi">
    <h2>Populārie galamērķi</h2>
    <div class="filter-buttons">
      <button class="filter-btn active" onclick="filterDestinations('all')">Visi</button>
      <button class="filter-btn" onclick="filterDestinations('europe')">Eiropa</button>
    </div>
    <div class="cards">
      <!-- Austrija -->
      <div class="card" data-category="europe" onclick="showModal('vienna')">
        <img src="https://images.unsplash.com/photo-1516550893923-42d28e5677af" alt="Vīne">
        <h3>Vīne</h3>
        <p>Austrijas galvaspilsēta, slavena ar klasisko mūziku, pilīm un kūkām.</p>
      </div>
      <!-- Beļģija -->
      <div class="card" data-category="europe" onclick="showModal('brussels')">
        <img src="https://images.unsplash.com/photo-1559113202-c916b8e44373" alt="Brisele">
        <h3>Brisele</h3>
        <p>Beļģijas galvaspilsēta, ES centrs, slavena ar šokolādi un Grand Place laukumu.</p>
      </div>
      <!-- Bulgārija -->
      <div class="card" data-category="europe" onclick="showModal('sofia')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Sofija">
        <h3>Sofija</h3>
        <p>Bulgārijas galvaspilsēta ar Aleksandra Ņevska katedrāli un kalnu ainavām.</p>
      </div>
      <!-- Horvātija -->
      <div class="card" data-category="europe" onclick="showModal('zagreb')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Zagreba">
        <h3>Zagreba</h3>
        <p>Horvātijas galvaspilsēta ar vēsturisku centru un Adrijas jūras tuvumu.</p>
      </div>
      <!-- Kipra -->
      <div class="card" data-category="europe" onclick="showModal('nicosia')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Nikozija">
        <h3>Nikozija</h3>
        <p>Kipras galvaspilsēta ar dalītu pilsētu un Vidusjūras klimatu.</p>
      </div>
      <!-- Čehija -->
      <div class="card" data-category="europe" onclick="showModal('prague')">
        <img src="https://images.unsplash.com/photo-1541845157-2a856e47840c" alt="Prāga">
        <h3>Prāga</h3>
        <p>Čehijas galvaspilsēta ar viduslaiku arhitektūru un Kārļa tiltu.</p>
      </div>
      <!-- Dānija -->
      <div class="card" data-category="europe" onclick="showModal('copenhagen')">
        <img src="https://images.unsplash.com/photo-1513622471931-43e7276b7626" alt="Kopenhāgena">
        <h3>Kopenhāgena</h3>
        <p>Dānijas galvaspilsēta ar krāsainām mājām, velosipēdiem un mājīgu atmosfēru.</p>
      </div>
      <!-- Igaunija -->
      <div class="card" data-category="europe" onclick="showModal('tallinn')">
        <img src="https://images.unsplash.com/photo-1560170485-15e847b6e130" alt="Tallina">
        <h3>Tallina</h3>
        <p>Igaunijas galvaspilsēta ar viduslaiku vecpilsētu un Baltijas jūras krastu.</p>
      </div>
      <!-- Somija -->
      <div class="card" data-category="europe" onclick="showModal('helsinki')">
        <img src="https://images.unsplash.com/photo-1517271935-7b7b2e5e5e5e" alt="Helsinki">
        <h3>Helsinki</h3>
        <p>Somijas galvaspilsēta ar Baltijas jūru, salām un skandināvu dizainu.</p>
      </div>
      <!-- Francija -->
      <div class="card" data-category="europe" onclick="showModal('paris')">
        <img src="https://images.unsplash.com/photo-1502602898657-3e91760cbb34" alt="Parīze">
        <h3>Parīze</h3>
        <p>Francijas galvaspilsēta, slavena ar Eifeļa torni, Luvru un romantiskām ieliņām.</p>
      </div>
      <!-- Vācija -->
      <div class="card" data-category="europe" onclick="showModal('berlin')">
        <img src="https://images.unsplash.com/photo-1554774853-719586a18d74" alt="Berlīne">
        <h3>Berlīne</h3>
        <p>Vācijas galvaspilsēta ar Brandenburgas vārtiem un bagātu vēsturi.</p>
      </div>
      <!-- Grieķija -->
      <div class="card" data-category="europe" onclick="showModal('athens')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Atēnas">
        <h3>Atēnas</h3>
        <p>Grieķijas galvaspilsēta ar Akropoli un seno civilizāciju mantojumu.</p>
      </div>
      <!-- Ungārija -->
      <div class="card" data-category="europe" onclick="showModal('budapest')">
        <img src="https://images.unsplash.com/photo-1549894238-05e677006b4e" alt="Budapešta">
        <h3>Budapešta</h3>
        <p>Ungārijas galvaspilsēta ar Donavas tiltiem un termālajām pirtīm.</p>
      </div>
      <!-- Īrija -->
      <div class="card" data-category="europe" onclick="showModal('dublin')">
        <img src="https://images.unsplash.com/photo-1505761671935-60b3a7427bad" alt="Dublina">
        <h3>Dublina</h3>
        <p>Īrijas galvaspilsēta ar krogiem, vēsturi un zaļiem laukiem apkārt.</p>
      </div>
      <!-- Islande -->
      <div class="card" data-category="europe" onclick="showModal('reykjavik')">
        <img src="https://images.unsplash.com/photo-1504893524553-b855bce32c67" alt="Reikjavīka">
        <h3>Reikjavīka</h3>
        <p>Islandes galvaspilsēta ar elpu aizraujošiem dabas skatiem un ģeotermālām lagūnām.</p>
      </div>
      <!-- Itālija -->
      <div class="card" data-category="europe" onclick="showModal('rome')">
        <img src="https://images.unsplash.com/photo-1552832230-c0197dd311b5" alt="Roma">
        <h3>Roma</h3>
        <p>Itālijas galvaspilsēta ar Kolizeju, Vatikānu un bagātu vēsturi.</p>
      </div>
      <!-- Latvija -->
      <div class="card" data-category="europe" onclick="showModal('riga')">
        <img src="https://images.unsplash.com/photo-1567337710280-7f1d9d4f900e" alt="Rīga">
        <h3>Rīga</h3>
        <p>Latvijas galvaspilsēta ar jūgendstila arhitektūru un Baltijas jūras tuvumu.</p>
      </div>
      <!-- Lietuva -->
      <div class="card" data-category="europe" onclick="showModal('vilnius')">
        <img src="https://images.unsplash.com/photo-1567337710280-7f1d9d4f900e" alt="Viļņa">
        <h3>Viļņa</h3>
        <p>Lietuvas galvaspilsēta ar baroka arhitektūru un bagātu vēsturi.</p>
      </div>
      <!-- Lihtenšteina -->
      <div class="card" data-category="europe" onclick="showModal('vaduz')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Vaduca">
        <h3>Vaduca</h3>
        <p>Lihtenšteinas galvaspilsēta ar Alpu ainavām un pili.</p>
      </div>
      <!-- Luksemburga -->
      <div class="card" data-category="europe" onclick="showModal('luxembourg')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Luksemburga">
        <h3>Luksemburga</h3>
        <p>Luksemburgas galvaspilsēta ar viduslaiku cietoksni un ES institūcijām.</p>
      </div>
      <!-- Malta -->
      <div class="card" data-category="europe" onclick="showModal('valletta')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Valleta">
        <h3>Valleta</h3>
        <p>Maltas galvaspilsēta ar baroka arhitektūru un Vidusjūras ostu.</p>
      </div>
      <!-- Nīderlande -->
      <div class="card" data-category="europe" onclick="showModal('amsterdam')">
        <img src="https://images.unsplash.com/photo-1502631868851-1717c13a91b6" alt="Amsterdama">
        <h3>Amsterdama</h3>
        <p>Nīderlandes galvaspilsēta ar kanāliem, velosipēdiem un mākslas muzejiem.</p>
      </div>
      <!-- Norvēģija -->
      <div class="card" data-category="europe" onclick="showModal('oslo')">
        <img src="https://images.unsplash.com/photo-1517497777308-7e22bc4a827e" alt="Oslo">
        <h3>Oslo</h3>
        <p>Norvēģijas galvaspilsēta ar fjordiem, modernu arhitektūru un zaļu dabu.</p>
      </div>
      <!-- Polija -->
      <div class="card" data-category="europe" onclick="showModal('warsaw')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Varšava">
        <h3>Varšava</h3>
        <p>Polijas galvaspilsēta ar atjaunotu vecpilsētu un dinamisku kultūru.</p>
      </div>
      <!-- Portugāle -->
      <div class="card" data-category="europe" onclick="showModal('lisbon')">
        <img src="https://images.unsplash.com/photo-1513622471931-43e7276b7626" alt="Lisabona">
        <h3>Lisabona</h3>
        <p>Portugāles galvaspilsēta ar kalniem, tramvajiem un okeāna vēju.</p>
      </div>
      <!-- Rumānija -->
      <div class="card" data-category="europe" onclick="showModal('bucharest')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Bukareste">
        <h3>Bukareste</h3>
        <p>Rumānijas galvaspilsēta ar grandiozām ēkām un Karpatiem apkārt.</p>
      </div>
      <!-- Slovākija -->
      <div class="card" data-category="europe" onclick="showModal('bratislava')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Bratislava">
        <h3>Bratislava</h3>
        <p>Slovākijas galvaspilsēta ar pili un Donavas krastiem.</p>
      </div>
      <!-- Slovēnija -->
      <div class="card" data-category="europe" onclick="showModal('ljubljana')">
        <img src="https://images.unsplash.com/photo-1600585154340-be6161a56a0c" alt="Ļubļana">
        <h3>Ļubļana</h3>
        <p>Slovēnijas galvaspilsēta ar zaļu upi un Alpu tuvumu.</p>
      </div>
      <!-- Spānija -->
      <div class="card" data-category="europe" onclick="showModal('madrid')">
        <img src="https://images.unsplash.com/photo-1543783207-ec64e4d95325" alt="Madride">
        <h3>Madride</h3>
        <p>Spānijas galvaspilsēta ar karalisko pili un flamenko.</p>
      </div>
      <!-- Šveice -->
      <div class="card" data-category="europe" onclick="showModal('zurich')">
        <img src="https://images.unsplash.com/photo-1562779448-868a61310b75" alt="Cīrihe">
        <h3>Cīrihe</h3>
        <p>Šveices finanšu centrs ar gleznainu vecpilsētu un kristāldzidru ezeru.</p>
      </div>
      <!-- Zviedrija -->
      <div class="card" data-category="europe" onclick="showModal('stockholm')">
        <img src="https://images.unsplash.com/photo-1509356843363-0a6e5b8a9838" alt="Stokholma">
        <h3>Stokholma</h3>
        <p>Zviedrijas galvaspilsēta ar arhipelāgu un skandināvu šarmu.</p>
      </div>
    </div>
  </section>

  <div id="modal" class="modal">
    <div class="modal-content">
      <span class="close" onclick="closeModal()">×</span>
      <h2 id="modal-title"></h2>
      <p id="modal-description"></p>
      <div class="gallery" id="modal-gallery"></div>
    </div>
  </div>

  <section class="reviews" id="atsauksmes">
    <h2>Atsauksmes</h2>
    <div class="review-slider">
      <blockquote>"Ceļojums uz Parīzi bija neaizmirstams!" – Laura</blockquote>
      <blockquote>"Rīga mani pārsteidza ar savu šarmu." – Jānis</blockquote>
      <blockquote>"Amsterdama ir ideāla pilsēta!" – Anna</blockquote>
    </div>
  </section>

  <section class="map" id="karte">
    <h2>Mūsu galamērķi kartē</h2>
    <div id="interactive-map"></div>
  </section>

  <section class="calculator" id="kalkulators">
    <h2>Ceļojuma izdevumu kalkulators</h2>
    <form id="calc-form" onsubmit="calculateCost(event)">
      <select id="destination" required>
        <option value="" disabled selected>Izvēlies galamērķi</option>
        <option value="vienna">Vīne (€)</option>
        <option value="brussels">Brisele (€)</option>
        <option value="sofia">Sofija (BGN)</option>
        <option value="zagreb">Zagreba (HRK)</option>
        <option value="nicosia">Nikozija (€)</option>
        <option value="prague">Prāga (CZK)</option>
        <option value="copenhagen">Kopenhāgena (DKK)</option>
        <option value="tallinn">Tallina (€)</option>
        <option value="helsinki">Helsinki (€)</option>
        <option value="paris">Parīze (€)</option>
        <option value="berlin">Berlīne (€)</option>
        <option value="athens">Atēnas (€)</option>
        <option value="budapest">Budapešta (HUF)</option>
        <option value="dublin">Dublina (€)</option>
        <option value="reykjavik">Reikjavīka (ISK)</option>
        <option value="rome">Roma (€)</option>
        <option value="riga">Rīga (€)</option>
        <option value="vilnius">Viļņa (€)</option>
        <option value="vaduz">Vaduca (CHF)</option>
        <option value="luxembourg">Luksemburga (€)</option>
        <option value="valletta">Valleta (€)</option>
        <option value="amsterdam">Amsterdama (€)</option>
        <option value="oslo">Oslo (NOK)</option>
        <option value="warsaw">Varšava (PLN)</option>
        <option value="lisbon">Lisabona (€)</option>
        <option value="bucharest">Bukareste (RON)</option>
        <option value="bratislava">Bratislava (€)</option>
        <option value="ljubljana">Ļubļana (€)</option>
        <option value="madrid">Madride (€)</option>
        <option value="zurich">Cīrihe (CHF)</option>
        <option value="stockholm">Stokholma (SEK)</option>
      </select>
      <input type="number" id="days" placeholder="Dienu skaits" min="1" required>
      <input type="number" id="people" placeholder="Personu skaits" min="1" required>
      <select id="budget" required>
        <option value="" disabled selected>Budžeta līmenis</option>
        <option value="low">Ekonomisks</option>
        <option value="medium">Vidējs</option>
        <option value="high">Luksuss</option>
      </select>
      <button type="submit" class="btn">Aprēķināt</button>
    </form>
    <div id="calc-result"></div>
  </section>

  <section class="car-calculator" id="auto-kalkulators">
    <h2>Auto ceļojuma izdevumu kalkulators</h2>
    <form id="car-calc-form" onsubmit="calculateCarCost(event)">
      <select id="car-destination" onchange="updateDistance(this.value)" required>
        <option value="" disabled selected>Izvēlies galamērķi</option>
        <option value="vienna">Vīne (€)</option>
        <option value="brussels">Brisele (€)</option>
        <option value="sofia">Sofija (BGN)</option>
        <option value="zagreb">Zagreba (HRK)</option>
        <option value="nicosia">Nikozija (€)</option>
        <option value="prague">Prāga (CZK)</option>
        <option value="copenhagen">Kopenhāgena (DKK)</option>
        <option value="tallinn">Tallina (€)</option>
        <option value="helsinki">Helsinki (€)</option>
        <option value="paris">Parīze (€)</option>
        <option value="berlin">Berlīne (€)</option>
        <option value="athens">Atēnas (€)</option>
        <option value="budapest">Budapešta (HUF)</option>
        <option value="dublin">Dublina (€)</option>
        <option value="reykjavik">Reikjavīka (ISK)</option>
        <option value="rome">Roma (€)</option>
        <option value="riga">Rīga (€)</option>
        <option value="vilnius">Viļņa (€)</option>
        <option value="vaduz">Vaduca (CHF)</option>
        <option value="luxembourg">Luksemburga (€)</option>
        <option value="valletta">Valleta (€)</option>
        <option value="amsterdam">Amsterdama (€)</option>
        <option value="oslo">Oslo (NOK)</option>
        <option value="warsaw">Varšava (PLN)</option>
        <option value="lisbon">Lisabona (€)</option>
        <option value="bucharest">Bukareste (RON)</option>
        <option value="bratislava">Bratislava (€)</option>
        <option value="ljubljana">Ļubļana (€)</option>
        <option value="madrid">Madride (€)</option>
        <option value="zurich">Cīrihe (CHF)</option>
        <option value="stockholm">Stokholma (SEK)</option>
      </select>
      <input type="number" id="distance" placeholder="Attālums (km)" min="1" required readonly>
      <input type="number" id="fuel-efficiency" placeholder="Degvielas patēriņš (l/100km)" min="1" step="0.1" required>
      <input type="number" id="fuel-price" placeholder="Degvielas cena (vietējā valūtā/l)" min="0" step="0.01" required>
      <input type="number" id="extra-costs" placeholder="Papildu izdevumi (vietējā valūtā)" min="0" step="0.01" value="0">
      <button type="submit" class="btn">Aprēķināt</button>
    </form>
    <div id="car-calc-result"></div>
  </section>

  <section class="contact" id="kontakti">
    <h2>Sazinies ar mums</h2>
    <form id="contact-form" onsubmit="submitForm(event)">
      <input type="text" id="name" placeholder="Vārds" required>
      <input type="email" id="email" placeholder="E-pasts" required>
      <textarea rows="5" id="message" placeholder="Jautājums vai ziņa..." required></textarea>
      <button type="submit">Sūtīt</button>
    </form>
    <p id="form-message" style="display: none; color: #4caf50; margin-top: 10px;"></p>
  </section>

  <footer>
    <p>© 2025 Ceļojumu Pasaule. Visas tiesības aizsargātas.</p>
  </footer>

  <button class="scroll-top" onclick="scrollToTop()">↑</button>

  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script>
    // Valodas maiņa
    function changeLanguage(lang) {
      document.querySelectorAll('[data-lv]').forEach(el => {
        el.innerText = el.getAttribute('data-' + lang);
      });
    }

    // Burger menu
    function toggleMenu() {
      document.querySelector('.nav-links').classList.toggle('active');
    }

    // Gaismas/tumšais režīms
    function toggleTheme() {
      document.body.classList.toggle('dark');
      const button = document.querySelector('.theme-toggle');
      button.innerText = document.body.classList.contains('dark') ? '☀️' : '🌙';
    }

    // Automātisks atsauksmju slīdnis
    const reviews = document.querySelectorAll('.review-slider blockquote');
    let currentReview = 0;
    function showNextReview() {
      reviews[currentReview].classList.remove('active');
      currentReview = (currentReview + 1) % reviews.length;
      reviews[currentReview].classList.add('active');
    }
    reviews[0].classList.add('active');
    setInterval(showNextReview, 5000);

    // Galamērķu filtrēšana
    function filterDestinations(category) {
      const cards = document.querySelectorAll('.card');
      const buttons = document.querySelectorAll('.filter-btn');
      cards.forEach(card => {
        card.classList.remove('hidden');
        if (category !== 'all' && card.dataset.category !== category) {
          card.classList.add('hidden');
        }
      });
      buttons.forEach(btn => btn.classList.remove('active'));
      event.target.classList.add('active');
    }

    // Modal loga funkcionalitāte
    const destinationDetails = {
      vienna: { title: 'Vīne', description: 'Austrijas galvaspilsēta ar klasisko mūziku.', gallery: ['https://images.unsplash.com/photo-1516550893923-42d28e5677af'] },
      brussels: { title: 'Brisele', description: 'Beļģijas galvaspilsēta ar šokolādi.', gallery: ['https://images.unsplash.com/photo-1559113202-c916b8e44373'] },
      sofia: { title: 'Sofija', description: 'Bulgārijas galvaspilsēta ar katedrāli.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      zagreb: { title: 'Zagreba', description: 'Horvātijas galvaspilsēta ar vēsturisku centru.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      nicosia: { title: 'Nikozija', description: 'Kipras galvaspilsēta ar dalītu pilsētu.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      prague: { title: 'Prāga', description: 'Čehijas galvaspilsēta ar viduslaiku arhitektūru.', gallery: ['https://images.unsplash.com/photo-1541845157-2a856e47840c'] },
      copenhagen: { title: 'Kopenhāgena', description: 'Dānijas galvaspilsēta ar krāsainām mājām.', gallery: ['https://images.unsplash.com/photo-1513622471931-43e7276b7626'] },
      tallinn: { title: 'Tallina', description: 'Igaunijas galvaspilsēta ar viduslaiku vecpilsētu.', gallery: ['https://images.unsplash.com/photo-1560170485-15e847b6e130'] },
      helsinki: { title: 'Helsinki', description: 'Somijas galvaspilsēta ar Baltijas jūru.', gallery: ['https://images.unsplash.com/photo-1517271935-7b7b2e5e5e5e'] },
      paris: { title: 'Parīze', description: 'Francijas galvaspilsēta ar Eifeļa torni.', gallery: ['https://images.unsplash.com/photo-1502602898657-3e91760cbb34'] },
      berlin: { title: 'Berlīne', description: 'Vācijas galvaspilsēta ar Brandenburgas vārtiem.', gallery: ['https://images.unsplash.com/photo-1554774853-719586a18d74'] },
      athens: { title: 'Atēnas', description: 'Grieķijas galvaspilsēta ar Akropoli.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      budapest: { title: 'Budapešta', description: 'Ungārijas galvaspilsēta ar termālajām pirtīm.', gallery: ['https://images.unsplash.com/photo-1549894238-05e677006b4e'] },
      dublin: { title: 'Dublina', description: 'Īrijas galvaspilsēta ar krogiem.', gallery: ['https://images.unsplash.com/photo-1505761671935-60b3a7427bad'] },
      reykjavik: { title: 'Reikjavīka', description: 'Islandes galvaspilsēta ar dabas skatiem.', gallery: ['https://images.unsplash.com/photo-1504893524553-b855bce32c67'] },
      rome: { title: 'Roma', description: 'Itālijas galvaspilsēta ar Kolizeju.', gallery: ['https://images.unsplash.com/photo-1552832230-c0197dd311b5'] },
      riga: { title: 'Rīga', description: 'Latvijas galvaspilsēta ar jūgendstilu.', gallery: ['https://images.unsplash.com/photo-1567337710280-7f1d9d4f900e'] },
      vilnius: { title: 'Viļņa', description: 'Lietuvas galvaspilsēta ar baroka arhitektūru.', gallery: ['https://images.unsplash.com/photo-1567337710280-7f1d9d4f900e'] },
      vaduz: { title: 'Vaduca', description: 'Lihtenšteinas galvaspilsēta ar Alpu ainavām.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      luxembourg: { title: 'Luksemburga', description: 'Luksemburgas galvaspilsēta ar cietoksni.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      valletta: { title: 'Valleta', description: 'Maltas galvaspilsēta ar baroka arhitektūru.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      amsterdam: { title: 'Amsterdama', description: 'Nīderlandes galvaspilsēta ar kanāliem.', gallery: ['https://images.unsplash.com/photo-1502631868851-1717c13a91b6'] },
      oslo: { title: 'Oslo', description: 'Norvēģijas galvaspilsēta ar fjordiem.', gallery: ['https://images.unsplash.com/photo-1517497777308-7e22bc4a827e'] },
      warsaw: { title: 'Varšava', description: 'Polijas galvaspilsēta ar atjaunotu vecpilsētu.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      lisbon: { title: 'Lisabona', description: 'Portugāles galvaspilsēta ar tramvajiem.', gallery: ['https://images.unsplash.com/photo-1513622471931-43e7276b7626'] },
      bucharest: { title: 'Bukareste', description: 'Rumānijas galvaspilsēta ar grandiozām ēkām.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      bratislava: { title: 'Bratislava', description: 'Slovākijas galvaspilsēta ar pili.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      ljubljana: { title: 'Ļubļana', description: 'Slovēnijas galvaspilsēta ar zaļu upi.', gallery: ['https://images.unsplash.com/photo-1600585154340-be6161a56a0c'] },
      madrid: { title: 'Madride', description: 'Spānijas galvaspilsēta ar karalisko pili.', gallery: ['https://images.unsplash.com/photo-1543783207-ec64e4d95325'] },
      zurich: { title: 'Cīrihe', description: 'Šveices finanšu centrs ar ezeru.', gallery: ['https://images.unsplash.com/photo-1562779448-868a61310b75'] },
      stockholm: { title: 'Stokholma', description: 'Zviedrijas galvaspilsēta ar arhipelāgu.', gallery: ['https://images.unsplash.com/photo-1509356843363-0a6e5b8a9838'] }
    };

    function showModal(destination) {
      const modal = document.getElementById('modal');
      const title = document.getElementById('modal-title');
      const description = document.getElementById('modal-description');
      const gallery = document.getElementById('modal-gallery');
      
      const details = destinationDetails[destination];
      title.innerText = details.title;
      description.innerText = details.description;
      gallery.innerHTML = '';
      details.gallery.forEach(src => {
        const img = document.createElement('img');
        img.src = src;
        gallery.appendChild(img);
      });

      modal.style.display = 'flex';
    }

    function closeModal() {
      document.getElementById('modal').style.display = 'none';
    }

    // Formas apstrāde
    function submitForm(event) {
      event.preventDefault();
      const name = document.getElementById('name').value;
      const email = document.getElementById('email').value;
      const message = document.getElementById('message').value;
      if (name && email && message) {
        document.getElementById('form-message').innerText = 'Paldies, jūsu ziņa ir nosūtīta!';
        document.getElementById('form-message').style.display = 'block';
        document.getElementById('contact-form').reset();
        setTimeout(() => {
          document.getElementById('form-message').style.display = 'none';
        }, 3000);
      }
    }

    // Scroll-to-top poga
    window.onscroll = function() {
      const btn = document.querySelector('.scroll-top');
      btn.style.display = window.scrollY > 300 ? 'block' : 'none';
    };
    function scrollToTop() {
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    // Interaktīvā karte
    const map = L.map('interactive-map').setView([50, 10], 3);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
    }).addTo(map);

    const destinations = [
      { name: 'Vīne', coords: [48.2082, 16.3738], desc: 'Austrijas galvaspilsēta' },
      { name: 'Brisele', coords: [50.8503, 4.3517], desc: 'Beļģijas galvaspilsēta' },
      { name: 'Sofija', coords: [42.6977, 23.3219], desc: 'Bulgārijas galvaspilsēta' },
      { name: 'Zagreba', coords: [45.8150, 15.9819], desc: 'Horvātijas galvaspilsēta' },
      { name: 'Nikozija', coords: [35.1856, 33.3823], desc: 'Kipras galvaspilsēta' },
      { name: 'Prāga', coords: [50.0755, 14.4378], desc: 'Čehijas galvaspilsēta' },
      { name: 'Kopenhāgena', coords: [55.6761, 12.5683], desc: 'Dānijas galvaspilsēta' },
      { name: 'Tallina', coords: [59.4370, 24.7535], desc: 'Igaunijas galvaspilsēta' },
      { name: 'Helsinki', coords: [60.1699, 24.9384], desc: 'Somijas galvaspilsēta' },
      { name: 'Parīze', coords: [48.8566, 2.3522], desc: 'Francijas galvaspilsēta' },
      { name: 'Berlīne', coords: [52.5200, 13.4050], desc: 'Vācijas galvaspilsēta' },
      { name: 'Atēnas', coords: [37.9838, 23.7275], desc: 'Grieķijas galvaspilsēta' },
      { name: 'Budapešta', coords: [47.4979, 19.0402], desc: 'Ungārijas galvaspilsēta' },
      { name: 'Dublina', coords: [53.3498, -6.2603], desc: 'Īrijas galvaspilsēta' },
      { name: 'Reikjavīka', coords: [64.1355, -21.8954], desc: 'Islandes galvaspilsēta' },
      { name: 'Roma', coords: [41.9028, 12.4964], desc: 'Itālijas galvaspilsēta' },
      { name: 'Rīga', coords: [56.9496, 24.1052], desc: 'Latvijas galvaspilsēta' },
      { name: 'Viļņa', coords: [54.6872, 25.2797], desc: 'Lietuvas galvaspilsēta' },
      { name: 'Vaduca', coords: [47.1410, 9.5209], desc: 'Lihtenšteinas galvaspilsēta' },
      { name: 'Luksemburga', coords: [49.6116, 6.1319], desc: 'Luksemburgas galvaspilsēta' },
      { name: 'Valleta', coords: [35.8989, 14.5146], desc: 'Maltas galvaspilsēta' },
      { name: 'Amsterdama', coords: [52.3676, 4.9041], desc: 'Nīderlandes galvaspilsēta' },
      { name: 'Oslo', coords: [59.9139, 10.7522], desc: 'Norvēģijas galvaspilsēta' },
      { name: 'Varšava', coords: [52.2297, 21.0122], desc: 'Polijas galvaspilsēta' },
      { name: 'Lisabona', coords: [38.7223, -9.1393], desc: 'Portugāles galvaspilsēta' },
      { name: 'Bukareste', coords: [44.4268, 26.1025], desc: 'Rumānijas galvaspilsēta' },
      { name: 'Bratislava', coords: [48.1486, 17.1077], desc: 'Slovākijas galvaspilsēta' },
      { name: 'Ļubļana', coords: [46.0569, 14.5058], desc: 'Slovēnijas galvaspilsēta' },
      { name: 'Madride', coords: [40.4168, -3.7038], desc: 'Spānijas galvaspilsēta' },
      { name: 'Cīrihe', coords: [47.3769, 8.5417], desc: 'Šveices finanšu centrs' },
      { name: 'Stokholma', coords: [59.3293, 18.0686], desc: 'Zviedrijas galvaspilsēta' }
    ];

    destinations.forEach(dest => {
      L.marker(dest.coords).addTo(map)
        .bindPopup(`<b>${dest.name}</b><br>${dest.desc}`);
    });

    // Ceļojuma izdevumu kalkulators
    function calculateCost(event) {
      event.preventDefault();
      const destination = document.getElementById('destination').value;
      const days = parseInt(document.getElementById('days').value);
      const people = parseInt(document.getElementById('people').value);
      const budget = document.getElementById('budget').value;

      const costs = {
        vienna: { low: 50, medium: 100, high: 200, currency: '€', rate: 1 },
        brussels: { low: 60, medium: 120, high: 240, currency: '€', rate: 1 },
        sofia: { low: 50, medium: 100, high: 200, currency: 'BGN', rate: 0.51 },
        zagreb: { low: 300, medium: 600, high: 1200, currency: 'HRK', rate: 0.13 },
        nicosia: { low: 40, medium: 80, high: 160, currency: '€', rate: 1 },
        prague: { low: 1000, medium: 2000, high: 4000, currency: 'CZK', rate: 0.043 },
        copenhagen: { low: 400, medium: 800, high: 1600, currency: 'DKK', rate: 0.13 },
        tallinn: { low: 40, medium: 80, high: 160, currency: '€', rate: 1 },
        helsinki: { low: 50, medium: 100, high: 200, currency: '€', rate: 1 },
        paris: { low: 50, medium: 100, high: 200, currency: '€', rate: 1 },
        berlin: { low: 50, medium: 100, high: 200, currency: '€', rate: 1 },
        athens: { low: 40, medium: 80, high: 160, currency: '€', rate: 1 },
        budapest: { low: 10000, medium: 20000, high: 40000, currency: 'HUF', rate: 0.0028 },
        dublin: { low: 50, medium: 100, high: 200, currency: '€', rate: 1 },
        reykjavik: { low: 5000, medium: 10000, high: 20000, currency: 'ISK', rate: 0.0073 },
        rome: { low: 50, medium: 100, high: 200, currency: '€', rate: 1 },
        riga: { low: 30, medium: 60, high: 120, currency: '€', rate: 1 },
        vilnius: { low: 30, medium: 60, high: 120, currency: '€', rate: 1 },
        vaduz: { low: 80, medium: 160, high: 320, currency: 'CHF', rate: 1.05 },
        luxembourg: { low: 60, medium: 120, high: 240, currency: '€', rate: 1 },
        valletta: { low: 40, medium: 80, high: 160, currency: '€', rate: 1 },
        amsterdam: { low: 50, medium: 100, high: 200, currency: '€', rate: 1 },
        oslo: { low: 500, medium: 1000, high: 2000, currency: 'NOK', rate: 0.092 },
        warsaw: { low: 100, medium: 200, high: 400, currency: 'PLN', rate: 0.23 },
        lisbon: { low: 40, medium: 80, high: 160, currency: '€', rate: 1 },
        bucharest: { low: 100, medium: 200, high: 400, currency: 'RON', rate: 0.20 },
        bratislava: { low: 40, medium: 80, high: 160, currency: '€', rate: 1 },
        ljubljana: { low: 40, medium: 80, high: 160, currency: '€', rate: 1 },
        madrid: { low: 50, medium: 100, high: 200, currency: '€', rate: 1 },
        zurich: { low: 80, medium: 160, high: 320, currency: 'CHF', rate: 1.05 },
        stockholm: { low: 500, medium: 1000, high: 2000, currency: 'SEK', rate: 0.094 }
      };

      const baseCost = costs[destination][budget];
      const totalCostLocal = baseCost * days * people;
      const totalCostEuro = totalCostLocal * costs[destination].rate;
      const currency = costs[destination].currency;

      document.getElementById('calc-result').innerText = 
        `Aptuvenās izmaksas: ${totalCostLocal.toLocaleString()} ${currency} (${people} personām uz ${days} dienām)\n` +
        `Ekvivalents eiro: ${totalCostEuro.toLocaleString('lv-LV', { style: 'currency', currency: 'EUR' })}`;
    }

    // Attālumu dati no Rīgas (aptuveni, km)
    const distancesFromRiga = {
      vienna: 1300,
      brussels: 1500,
      sofia: 1700,
      zagreb: 1500,
      nicosia: 2800,
      prague: 1000,
      copenhagen: 700,
      tallinn: 300,
      helsinki: 400,
      paris: 1700,
      berlin: 1000,
      athens: 2200,
      budapest: 1200,
      dublin: 1900,
      reykjavik: 2800,
      rome: 2000,
      riga: 0,
      vilnius: 300,
      vaduz: 1600,
      luxembourg: 1500,
      valletta: 2500,
      amsterdam: 1300,
      oslo: 850,
      warsaw: 550,
      lisbon: 3100,
      bucharest: 1400,
      bratislava: 1200,
      ljubljana: 1500,
      madrid: 2800,
      zurich: 1500,
      stockholm: 500
    };

    // Funkcija, kas atjauno attālumu, izvēloties galamērķi
    function updateDistance(destination) {
      const distanceInput = document.getElementById('distance');
      if (destination && distancesFromRiga[destination]) {
        distanceInput.value = distancesFromRiga[destination];
        distanceInput.setAttribute('readonly', 'readonly');
      } else {
        distanceInput.value = '';
        distanceInput.removeAttribute('readonly');
      }
    }

    // Auto ceļojuma izdevumu kalkulators
    function calculateCarCost(event) {
      event.preventDefault();
      const destination = document.getElementById('car-destination').value;
      const distance = parseFloat(document.getElementById('distance').value); // Kopējais attālums (km)
      const fuelEfficiency = parseFloat(document.getElementById('fuel-efficiency').value); // Patēriņš (litri/100km)
      const fuelPrice = parseFloat(document.getElementById('fuel-price').value); // Cena par litru
      const extraCosts = parseFloat(document.getElementById('extra-costs').value) || 0;

      const costs = {
        vienna: { currency: '€', rate: 1 },
        brussels: { currency: '€', rate: 1 },
        sofia: { currency: 'BGN', rate: 0.51 },
        zagreb: { currency: 'HRK', rate: 0.13 },
        nicosia: { currency: '€', rate: 1 },
        prague: { currency: 'CZK', rate: 0.043 },
        copenhagen: { currency: 'DKK', rate: 0.13 },
        tallinn: { currency: '€', rate: 1 },
        helsinki: { currency: '€', rate: 1 },
        paris: { currency: '€', rate: 1 },
        berlin: { currency: '€', rate: 1 },
        athens: { currency: '€', rate: 1 },
        budapest: { currency: 'HUF', rate: 0.0028 },
        dublin: { currency: '€', rate: 1 },
        reykjavik: { currency: 'ISK', rate: 0.0073 },
        rome: { currency: '€', rate: 1 },
        riga: { currency: '€', rate: 1 },
        vilnius: { currency: '€', rate: 1 },
        vaduz: { currency: 'CHF', rate: 1.05 },
        luxembourg: { currency: '€', rate: 1 },
        valletta: { currency: '€', rate: 1 },
        amsterdam: { currency: '€', rate: 1 },
        oslo: { currency: 'NOK', rate: 0.092 },
        warsaw: { currency: 'PLN', rate: 0.23 },
        lisbon: { currency: '€', rate: 1 },
        bucharest: { currency: 'RON', rate: 0.20 },
        bratislava: { currency: '€', rate: 1 },
        ljubljana: { currency: '€', rate: 1 },
        madrid: { currency: '€', rate: 1 },
        zurich: { currency: 'CHF', rate: 1.05 },
        stockholm: { currency: 'SEK', rate: 0.094 }
      };

      // Degvielas izmaksas: (attālums / 100) * patēriņš uz 100km * cena par litru
      const fuelCost = (distance / 100) * fuelEfficiency * fuelPrice;
      const totalCostLocal = fuelCost + extraCosts;
      const totalCostEuro = totalCostLocal * costs[destination].rate;
      const currency = costs[destination].currency;

      document.getElementById('car-calc-result').innerText = 
        `Kopējās izmaksas: ${totalCostLocal.toLocaleString()} ${currency} (degviela: ${fuelCost.toLocaleString()} ${currency}, papildu: ${extraCosts.toLocaleString()} ${currency})\n` +
        `Ekvivalents eiro: ${totalCostEuro.toLocaleString('lv-LV', { style: 'currency', currency: 'EUR' })}`;
    }
  </script>
</body>
</html>
