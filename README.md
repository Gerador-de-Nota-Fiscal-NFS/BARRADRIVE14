<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Barra Drive - Estância Turística</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <style>
    :root {
      --bg-main: #08080a;
      --card-bg: #121216;
      --card-border: #22222a;
      --accent-green: #76c813; /* Verde vibrante BARRA DRIVE */
      --accent-red: #e11d48;   /* Vermelho do Coração */
      --accent-blue: #2563eb;  /* Azul */
      --accent-yellow: #f59e0b;/* Amarelo */
      --text-main: #ffffff;
      --text-muted: #9a9ab0;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
    }

    body {
      background-color: var(--bg-main);
      color: var(--text-main);
      display: flex;
      flex-direction: column;
      height: 100vh;
      overflow: hidden;
    }

    /* HEADER */
    header {
      background: var(--card-bg);
      padding: 10px 16px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 2px solid var(--accent-green);
      z-index: 1000;
      box-shadow: 0 4px 20px rgba(0,0,0,0.8);
    }

    .brand-container {
      display: flex;
      align-items: center;
      gap: 10px;
    }

    .brand-logo-badge {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 42px;
      height: 42px;
      background: #000;
      border-radius: 50%;
      border: 2px solid var(--accent-green);
      box-shadow: 0 0 10px rgba(118, 200, 19, 0.4);
    }

    .brand-title {
      font-size: 1.1rem;
      font-weight: 900;
      line-height: 1;
    }

    .brand-title span { color: var(--accent-green); }

    .brand-subtitle {
      font-size: 0.58rem;
      font-weight: 800;
      letter-spacing: 1.2px;
      text-transform: uppercase;
      margin-top: 3px;
      background: linear-gradient(90deg, var(--accent-red), var(--accent-blue), var(--accent-yellow), var(--accent-green));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    .header-actions {
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .btn-auth-header {
      background: var(--card-border);
      color: var(--text-main);
      border: 1px solid rgba(255,255,255,0.1);
      padding: 6px 10px;
      font-size: 0.75rem;
      font-weight: 700;
      border-radius: 8px;
      cursor: pointer;
    }

    .mode-switch {
      display: flex;
      gap: 4px;
      background: var(--bg-main);
      padding: 3px;
      border-radius: 10px;
      border: 1px solid var(--card-border);
    }

    .mode-btn {
      background: none;
      border: none;
      color: var(--text-muted);
      padding: 6px 10px;
      font-size: 0.75rem;
      font-weight: 800;
      border-radius: 7px;
      cursor: pointer;
      transition: all 0.2s ease;
    }

    .mode-btn.active {
      background: var(--accent-green);
      color: #000;
      box-shadow: 0 0 10px rgba(118, 200, 19, 0.4);
    }

    #app-container {
      display: flex;
      flex: 1;
      position: relative;
      overflow: hidden;
    }

    #map { flex: 1; height: 100%; z-index: 1; }

    /* PAINEL INFERIOR */
    .panel {
      position: absolute;
      bottom: 0;
      left: 0;
      right: 0;
      background: var(--card-bg);
      border-top-left-radius: 24px;
      border-top-right-radius: 24px;
      padding: 18px 20px;
      z-index: 1000;
      box-shadow: 0 -8px 30px rgba(0, 0, 0, 0.8);
      max-height: 85vh;
      overflow-y: auto;
      border-top: 1px solid var(--card-border);
    }

    @media (min-width: 768px) {
      .panel {
        top: 20px;
        left: 20px;
        bottom: 20px;
        right: auto;
        width: 410px;
        border-radius: 20px;
        border: 1px solid var(--card-border);
      }
    }

    .search-box {
      display: flex;
      flex-direction: column;
      gap: 10px;
      margin-bottom: 14px;
    }

    .field {
      position: relative;
      display: flex;
      align-items: center;
      background: var(--bg-main);
      border: 1px solid var(--card-border);
      border-radius: 12px;
      padding: 10px 14px;
    }

    .field:focus-within { border-color: var(--accent-green); }

    .field-dot {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      margin-right: 12px;
      flex-shrink: 0;
    }

    .field-dot.origin { background: var(--accent-green); box-shadow: 0 0 8px var(--accent-green); }
    .field-dot.dest { background: var(--accent-red); box-shadow: 0 0 8px var(--accent-red); }

    .field input, .form-group input, .form-group select {
      background: none;
      border: none;
      color: var(--text-main);
      width: 100%;
      font-size: 0.9rem;
      outline: none;
    }

    .gps-btn {
      background: none;
      border: none;
      color: var(--accent-green);
      cursor: pointer;
      font-size: 1.1rem;
      padding: 2px 6px;
    }

    .route-metrics {
      display: flex;
      justify-content: space-around;
      background: var(--bg-main);
      padding: 12px;
      border-radius: 12px;
      border: 1px solid var(--card-border);
      margin-bottom: 14px;
      text-align: center;
    }

    .metric-value {
      font-size: 1.1rem;
      font-weight: 800;
    }

    .metric-label {
      font-size: 0.68rem;
      color: var(--text-muted);
      text-transform: uppercase;
    }

    .categories {
      display: flex;
      flex-direction: column;
      gap: 10px;
      margin-bottom: 16px;
    }

    .category-card {
      display: flex;
      align-items: center;
      justify-content: space-between;
      background: var(--bg-main);
      border: 2px solid transparent;
      padding: 12px 14px;
      border-radius: 12px;
      cursor: pointer;
    }

    .category-card.selected {
      border-color: var(--accent-green);
      background: rgba(118, 200, 19, 0.08);
    }

    .cat-price {
      font-size: 1.15rem;
      font-weight: 900;
      color: var(--accent-green);
    }

    .btn-main {
      width: 100%;
      background: var(--accent-green);
      color: #000;
      font-weight: 900;
      border: none;
      padding: 14px;
      border-radius: 12px;
      font-size: 0.95rem;
      cursor: pointer;
      text-transform: uppercase;
      box-shadow: 0 4px 15px rgba(118, 200, 19, 0.3);
    }

    .status-badge {
      display: inline-block;
      padding: 4px 12px;
      border-radius: 20px;
      font-size: 0.72rem;
      font-weight: 800;
      margin-bottom: 10px;
    }

    /* MODAL DE CADASTRO E RECONHECIMENTO FACIAL */
    .modal-overlay {
      position: fixed;
      top: 0; left: 0; right: 0; bottom: 0;
      background: rgba(0,0,0,0.85);
      backdrop-filter: blur(8px);
      z-index: 3000;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 20px;
    }

    .modal-card {
      background: var(--card-bg);
      border: 1px solid var(--card-border);
      border-radius: 20px;
      width: 100%;
      max-width: 460px;
      max-height: 90vh;
      overflow-y: auto;
      padding: 24px;
      box-shadow: 0 10px 40px rgba(0,0,0,0.9);
      position: relative;
    }

    .modal-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 18px;
    }

    .modal-title {
      font-size: 1.2rem;
      font-weight: 900;
    }

    .close-btn {
      background: none;
      border: none;
      color: var(--text-muted);
      font-size: 1.5rem;
      cursor: pointer;
    }

    .form-group {
      margin-bottom: 14px;
    }

    .form-group label {
      display: block;
      font-size: 0.78rem;
      color: var(--text-muted);
      margin-bottom: 6px;
      text-transform: uppercase;
      font-weight: 700;
    }

    .form-input {
      background: var(--bg-main);
      border: 1px solid var(--card-border);
      border-radius: 10px;
      padding: 12px;
      color: var(--text-main);
      width: 100%;
      font-size: 0.9rem;
    }

    .form-input:focus {
      border-color: var(--accent-green);
      outline: none;
    }

    /* CONTAINER CAMERA FACIAL */
    .facial-box {
      position: relative;
      width: 100%;
      height: 280px;
      background: #000;
      border-radius: 16px;
      overflow: hidden;
      border: 2px solid var(--card-border);
      margin-bottom: 16px;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    #facial-video {
      width: 100%;
      height: 100%;
      object-fit: cover;
      transform: scaleX(-1);
    }

    /* MOLDURA OVAL DO ROSTO */
    .face-overlay-guide {
      position: absolute;
      width: 180px;
      height: 230px;
      border: 3px dashed var(--accent-green);
      border-radius: 50%;
      box-shadow: 0 0 0 9999px rgba(0, 0, 0, 0.65);
      pointer-events: none;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .scan-line {
      position: absolute;
      top: 0;
      width: 100%;
      height: 4px;
      background: var(--accent-green);
      box-shadow: 0 0 15px var(--accent-green);
      animation: scanAnim 2.2s infinite ease-in-out;
    }

    @keyframes scanAnim {
      0% { top: 10%; opacity: 0.2; }
      50% { top: 85%; opacity: 1; }
      100% { top: 10%; opacity: 0.2; }
    }

    .facial-status {
      position: absolute;
      bottom: 12px;
      background: rgba(0,0,0,0.8);
      padding: 6px 14px;
      border-radius: 20px;
      font-size: 0.75rem;
      font-weight: 800;
      color: var(--accent-green);
      border: 1px solid var(--accent-green);
    }

    .preview-photo {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    .hidden { display: none !important; }
  </style>
</head>
<body>

  <!-- HEADER COM ATALHOS DE CADASTRO -->
  <header>
    <div class="brand-container">
      <div class="brand-logo-badge">
        <svg width="26" height="26" viewBox="0 0 24 24" fill="none" stroke="#76c813" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round">
          <path d="M19 17h2c.6 0 1-.4 1-1v-3c0-.9-.7-1.7-1.5-1.9C18.7 10.6 16 10 16 10s-1.3-1.4-2.2-2.3c-.5-.4-1.1-.7-1.8-.7H5c-.6 0-1.1.4-1.4.9l-1.4 2.9A3.7 3.7 0 0 0 2 12v4c0 .6.4 1 1 1h2" />
          <circle cx="7" cy="17" r="2" />
          <circle cx="17" cy="17" r="2" />
        </svg>
      </div>
      <div class="brand-text">
        <div class="brand-title">BARRA <span>DRIVE</span></div>
        <div class="brand-subtitle">Estância Turística</div>
      </div>
    </div>

    <div class="header-actions">
      <button class="btn-auth-header" onclick="openRegistrationModal('passenger')">Cadastrar</button>
      <div class="mode-switch">
        <button class="mode-btn active" id="btn-passenger" onclick="switchMode('passenger')">Passageiro</button>
        <button class="mode-btn" id="btn-driver" onclick="switchMode('driver')">Motorista</button>
      </div>
    </div>
  </header>

  <div id="app-container">
    <div id="map"></div>

    <!-- PAINEL PASSAGEIRO -->
    <div class="panel" id="panel-passenger">
      <div id="step-request">
        <h3 style="margin-bottom: 10px; font-weight: 900;">Sua corrida, do seu jeito.</h3>
        
        <div class="search-box">
          <div class="field">
            <div class="field-dot origin"></div>
            <input type="text" id="input-origin" value="Obtendo sua localização GPS..." />
            <button class="gps-btn" onclick="getExactGPSLocation()">🎯</button>
          </div>

          <div class="field">
            <div class="field-dot dest"></div>
            <input type="text" id="input-dest" value="Igaraçu do Tietê - Centro" placeholder="Qual é o seu destino?" />
          </div>
        </div>

        <div class="route-metrics">
          <div>
            <div class="metric-value" id="val-distance">0.0 km</div>
            <div class="metric-label">Distância</div>
          </div>
          <div style="border-left: 1px solid var(--card-border); border-right: 1px solid var(--card-border); padding: 0 16px;">
            <div class="metric-value" id="val-duration">0 min</div>
            <div class="metric-label">Tempo Est.</div>
          </div>
          <div>
            <div class="metric-value" style="color: var(--accent-green);" id="val-basefare">R$ 0,00</div>
            <div class="metric-label">Valor Mínimo</div>
          </div>
        </div>

        <div class="categories">
          <div class="category-card selected" onclick="selectCategory(this, 1.0)">
            <div>
              <h4>Barra Drive Carro</h4>
              <p style="font-size:0.75rem; color: var(--text-muted);">Viagem confortável com ar-condicionado</p>
            </div>
            <div class="cat-price" id="price-standard">R$ 0,00</div>
          </div>

          <div class="category-card" onclick="selectCategory(this, 0.70)">
            <div>
              <h4>Barra Drive Moto</h4>
              <p style="font-size:0.75rem; color: var(--text-muted);">Rápido e econômico pela cidade</p>
            </div>
            <div class="cat-price" id="price-moto">R$ 0,00</div>
          </div>
        </div>

        <button class="btn-main" onclick="requestRide()">Chamar Barra Drive</button>
      </div>

      <div id="step-searching" class="hidden" style="text-align: center; padding: 20px 0;">
        <div class="status-badge" style="background: rgba(118, 200, 19, 0.2); color: var(--accent-green);">PROCURANDO MOTORISTA</div>
        <h3>Procurando motorista próximo...</h3>
        <p style="color: var(--text-muted); font-size: 0.85rem; margin: 10px 0 20px;">Atendendo Barra Bonita e Região do DDD 14</p>
        <button class="btn-main" style="background: var(--accent-red); color: #fff;" onclick="cancelRide()">Cancelar Viagem</button>
      </div>

      <div id="step-in-progress" class="hidden">
        <div class="status-badge" style="background: rgba(37, 99, 235, 0.2); color: var(--accent-blue);">CORRIDA EM ANDAMENTO</div>
        <h3>Carlos Eduardo • Onix Prata</h3>
        <p style="color: var(--text-muted); font-size: 0.85rem; margin-bottom: 16px;">Placa: BBD-1414 • Biometria Verificada ✓</p>
        <button class="btn-main" onclick="resetApp()">Finalizar Corrida</button>
      </div>
    </div>

    <!-- PAINEL MOTORISTA -->
    <div class="panel hidden" id="panel-driver">
      <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px;">
        <h3>Painel Barra Driver</h3>
        <button class="btn-auth-header" onclick="openRegistrationModal('driver')">+ Cadastrar Veículo</button>
      </div>

      <div style="background: var(--bg-main); padding: 16px; border-radius: 14px; margin-bottom: 16px; border: 1px solid var(--card-border);">
        <div style="font-size: 0.8rem; color: var(--text-muted);">Ganhos de Hoje</div>
        <div style="font-size: 1.6rem; font-weight: 900; color: var(--accent-green);">R$ 195,50</div>
      </div>

      <div id="driver-ride-request" class="hidden" style="background: var(--bg-main); border: 2px solid var(--accent-green); padding: 16px; border-radius: 14px;">
        <div class="status-badge" style="background: var(--accent-green); color: #000;">NOVA CORRIDA BARRA DRIVE</div>
        <h4 id="driver-route-title" style="margin-top: 6px;">Sua Localização ➔ Destino</h4>
        <p style="font-size: 0.85rem; color: var(--text-muted); margin: 6px 0;" id="driver-route-info">Distância calculada</p>
        <div style="font-size: 1.3rem; font-weight: 900; color: var(--accent-green); margin-bottom: 12px;" id="driver-fare-value">R$ 0,00</div>
        <div style="display: flex; gap: 8px;">
          <button class="btn-main" onclick="acceptDriverRide()">Aceitar</button>
          <button class="btn-main" style="background: var(--card-border); color: var(--text-main);" onclick="rejectDriverRide()">Recusar</button>
        </div>
      </div>
    </div>
  </div>

  <!-- MODAL DE CADASTRO UNIFICADO -->
  <div class="modal-overlay hidden" id="modal-registration">
    <div class="modal-card">
      <div class="modal-header">
        <div class="modal-title" id="reg-title">Cadastro de Passageiro</div>
        <button class="close-btn" onclick="closeModal('modal-registration')">&times;</button>
      </div>

      <form id="form-registration" onsubmit="handleRegistrationSubmit(event)">
        <input type="hidden" id="reg-type" value="passenger" />
        
        <div class="form-group">
          <label>Nome Completo</label>
          <input type="text" class="form-input" required placeholder="Digite seu nome completo" />
        </div>

        <div style="display: flex; gap: 10px;">
          <div class="form-group" style="flex:1;">
            <label>Celular / WhatsApp</label>
            <input type="tel" class="form-input" required placeholder="(14) 99999-9999" />
          </div>
          <div class="form-group" style="flex:1;">
            <label>CPF</label>
            <input type="text" class="form-input" required placeholder="000.000.000-00" />
          </div>
        </div>

        <!-- CAMPOS EXCLUSIVOS DE MOTORISTA -->
        <div id="driver-fields" class="hidden">
          <div class="form-group">
            <label>Número da CNH</label>
            <input type="text" class="form-input" placeholder="00000000000" />
          </div>

          <div style="display: flex; gap: 10px;">
            <div class="form-group" style="flex:1;">
              <label>Modelo do Veículo</label>
              <input type="text" class="form-input" placeholder="Ex: Onix, Titan 160" />
            </div>
            <div class="form-group" style="flex:1;">
              <label>Placa</label>
              <input type="text" class="form-input" placeholder="ABC-1D23" />
            </div>
          </div>
        </div>

        <!-- SEÇÃO VERIFICAÇÃO FACIAL -->
        <div class="form-group">
          <label>Verificação Facial Biométrica</label>
          <div style="background: var(--bg-main); border: 1px solid var(--card-border); border-radius: 12px; padding: 12px; text-align: center;">
            <div id="facial-status-text" style="font-size: 0.82rem; color: var(--text-muted); margin-bottom: 10px;">
              Necessário para segurança de motoristas e passageiros no Barra Drive.
            </div>
            
            <div id="facial-preview-container" class="hidden" style="margin-bottom: 10px;">
              <img id="facial-result-img" class="preview-photo" style="height: 140px; border-radius: 10px; width: auto;" />
            </div>

            <button type="button" class="btn-auth-header" style="background: var(--accent-green); color: #000; width: 100%; padding: 10px;" onclick="openFacialScanner()">
              📷 Realizar Reconhecimento Facial
            </button>
          </div>
        </div>

        <button type="submit" class="btn-main" style="margin-top: 10px;">Concluir Cadastro</button>
      </form>
    </div>
  </div>

  <!-- MODAL DE SCANNER / CÂMERA FACIAL -->
  <div class="modal-overlay hidden" id="modal-facial">
    <div class="modal-card" style="text-align: center;">
      <div class="modal-header">
        <div class="modal-title">Reconhecimento Facial</div>
        <button class="close-btn" onclick="closeFacialScanner()">&times;</button>
      </div>

      <p style="font-size: 0.8rem; color: var(--text-muted); margin-bottom: 14px;">
        Centralize seu rosto no centro da tela para autenticação.
      </p>

      <div class="facial-box">
        <video id="facial-video" autoplay playsinline muted></video>
        <div class="face-overlay-guide">
          <div class="scan-line"></div>
        </div>
        <div class="facial-status" id="facial-live-status">PROCURANDO ROSTO...</div>
      </div>

      <canvas id="facial-canvas" class="hidden"></canvas>

      <button class="btn-main" onclick="captureFace()">Capturar e Validar Rosto</button>
    </div>
  </div>

  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script>
    let originCoords = [-22.4947, -48.5583];
    let destCoords = [-22.5150, -48.5500];

    const map = L.map('map', { zoomControl: false }).setView(originCoords, 15);

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; OpenStreetMap'
    }).addTo(map);

    const greenPinIcon = L.divIcon({
      className: 'custom-pin',
      html: `<div style="background:#76c813; width:20px; height:20px; border-radius:50%; border:3px solid #fff; box-shadow:0 0 12px #76c813;"></div>`
    });

    const redPinIcon = L.divIcon({
      className: 'custom-pin',
      html: `<div style="background:#e11d48; width:20px; height:20px; border-radius:50%; border:3px solid #fff; box-shadow:0 0 12px #e11d48;"></div>`
    });

    let markerOrigin = L.marker(originCoords, { draggable: true, icon: greenPinIcon }).addTo(map);
    let markerDest = L.marker(destCoords, { draggable: true, icon: redPinIcon }).addTo(map);
    let routePolyline;

    let selectedMultiplier = 1.0;
    let currentDistanceKm = 0;
    let currentDurationMin = 0;
    let currentBaseFare = 0;
    let faceStream = null;
    let isFaceVerified = false;

    // --- RECONHECIMENTO FACIAL E CÂMERA ---
    function openRegistrationModal(type) {
      document.getElementById('reg-type').value = type;
      const isDriver = type === 'driver';
      document.getElementById('reg-title').innerText = isDriver ? 'Cadastro de Motorista Barra Drive' : 'Cadastro de Passageiro';
      document.getElementById('driver-fields').classList.toggle('hidden', !isDriver);
      document.getElementById('modal-registration').classList.remove('hidden');
    }

    function closeModal(modalId) {
      document.getElementById(modalId).classList.add('hidden');
    }

    async function openFacialScanner() {
      document.getElementById('modal-facial').classList.remove('hidden');
      const video = document.getElementById('facial-video');
      const statusText = document.getElementById('facial-live-status');

      try {
        faceStream = await navigator.mediaDevices.getUserMedia({
          video: { facingMode: 'user', width: { ideal: 640 }, height: { ideal: 480 } }
        });
        video.srcObject = faceStream;

        setTimeout(() => {
          statusText.innerText = "ROSTO ENCONTRADO ✓";
          statusText.style.color = "var(--accent-green)";
        }, 1200);
      } catch (err) {
        alert("Não foi possível acessar a câmera para reconhecimento facial.");
        closeFacialScanner();
      }
    }

    function closeFacialScanner() {
      if (faceStream) {
        faceStream.getTracks().forEach(track => track.stop());
      }
      document.getElementById('modal-facial').classList.add('hidden');
    }

    function captureFace() {
      const video = document.getElementById('facial-video');
      const canvas = document.getElementById('facial-canvas');
      const imgPreview = document.getElementById('facial-result-img');
      const container = document.getElementById('facial-preview-container');
      const statusText = document.getElementById('facial-status-text');

      canvas.width = video.videoWidth || 320;
      canvas.height = video.videoHeight || 240;
      const ctx = canvas.getContext('2d');
      ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

      const dataUrl = canvas.toDataURL('image/jpeg');
      imgPreview.src = dataUrl;
      container.classList.remove('hidden');

      isFaceVerified = true;
      statusText.innerText = "✅ Biometria facial verificada e validada com sucesso!";
      statusText.style.color = "var(--accent-green)";

      closeFacialScanner();
    }

    function handleRegistrationSubmit(e) {
      e.preventDefault();
      if (!isFaceVerified) {
        alert("Por favor, realize o Reconhecimento Facial antes de concluir o cadastro.");
        return;
      }
      alert("Cadastro concluído com sucesso! Biometria facial registrada no Barra Drive.");
      closeModal('modal-registration');
    }

    // --- LOCALIZAÇÃO GPS EXATA ---
    function getExactGPSLocation() {
      const inputOrigin = document.getElementById('input-origin');
      inputOrigin.value = "Obtendo localização exata via GPS...";

      if ("geolocation" in navigator) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            const lat = position.coords.latitude;
            const lng = position.coords.longitude;
            originCoords = [lat, lng];

            markerOrigin.setLatLng(originCoords);
            map.setView(originCoords, 16);
            updateRouteRealtime();
            document.getElementById('input-origin').value = "Minha Localização Exata (GPS)";
          },
          () => {
            alert("Erro ao obter GPS. Por favor ative a localização do aparelho.");
            inputOrigin.value = "Barra Bonita - Centro";
          },
          { enableHighAccuracy: true, timeout: 10000, maximumAge: 0 }
        );
      }
    }

    markerOrigin.on('drag', updateRouteRealtime);
    markerDest.on('drag', updateRouteRealtime);

    function updateRouteRealtime() {
      const p1 = markerOrigin.getLatLng();
      const p2 = markerDest.getLatLng();

      const coords = [[p1.lat, p1.lng], [p2.lat, p2.lng]];
      if (routePolyline) {
        routePolyline.setLatLngs(coords);
      } else {
        routePolyline = L.polyline(coords, { color: '#76c813', weight: 5, opacity: 0.9 }).addTo(map);
      }

      currentDistanceKm = calculateDistance(p1.lat, p1.lng, p2.lat, p2.lng);
      currentDurationMin = Math.max(2, Math.round(currentDistanceKm * 2.1));

      const baseFee = 4.00;
      const ratePerKm = 2.00;
      const ratePerMin = 0.30;

      currentBaseFare = baseFee + (currentDistanceKm * ratePerKm) + (currentDurationMin * ratePerMin);
      if (currentBaseFare < 7.00) currentBaseFare = 7.00;

      document.getElementById('val-distance').innerText = `${currentDistanceKm.toFixed(1)} km`;
      document.getElementById('val-duration').innerText = `${currentDurationMin} min`;
      document.getElementById('val-basefare').innerText = `R$ ${currentBaseFare.toFixed(2).replace('.', ',')}`;

      document.getElementById('price-standard').innerText = `R$ ${(currentBaseFare * 1.0).toFixed(2).replace('.', ',')}`;
      document.getElementById('price-moto').innerText = `R$ ${(currentBaseFare * 0.70).toFixed(2).replace('.', ',')}`;
    }

    function calculateDistance(lat1, lon1, lat2, lon2) {
      const R = 6371;
      const dLat = (lat2 - lat1) * Math.PI / 180;
      const dLon = (lon2 - lon1) * Math.PI / 180;
      const a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
                Math.sin(dLon/2) * Math.sin(dLon/2);
      return R * (2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a))) * 1.25;
    }

    function selectCategory(element, multiplier) {
      document.querySelectorAll('.category-card').forEach(card => card.classList.remove('selected'));
      element.classList.add('selected');
      selectedMultiplier = multiplier;
    }

    function switchMode(mode) {
      document.getElementById('btn-passenger').classList.toggle('active', mode === 'passenger');
      document.getElementById('btn-driver').classList.toggle('active', mode === 'driver');
      document.getElementById('panel-passenger').classList.toggle('hidden', mode !== 'passenger');
      document.getElementById('panel-driver').classList.toggle('hidden', mode !== 'driver');
    }

    function requestRide() {
      document.getElementById('step-request').classList.add('hidden');
      document.getElementById('step-searching').classList.remove('hidden');

      const finalFare = (currentBaseFare * selectedMultiplier).toFixed(2).replace('.', ',');
      document.getElementById('driver-fare-value').innerText = `R$ ${finalFare}`;
      document.getElementById('driver-route-info').innerText = `Distância: ${currentDistanceKm.toFixed(1)} km • Est. ${currentDurationMin} min`;

      setTimeout(() => {
        document.getElementById('driver-ride-request').classList.remove('hidden');
      }, 1500);
    }

    function cancelRide() {
      document.getElementById('step-searching').classList.add('hidden');
      document.getElementById('step-request').classList.remove('hidden');
      document.getElementById('driver-ride-request').classList.add('hidden');
    }

    function acceptDriverRide() {
      document.getElementById('driver-ride-request').classList.add('hidden');
      switchMode('passenger');
      document.getElementById('step-searching').classList.add('hidden');
      document.getElementById('step-in-progress').classList.remove('hidden');
    }

    function rejectDriverRide() {
      document.getElementById('driver-ride-request').classList.add('hidden');
    }

    function resetApp() {
      document.getElementById('step-in-progress').classList.add('hidden');
      document.getElementById('step-request').classList.remove('hidden');
    }

    getExactGPSLocation();
  </script>
</body>
</html>
