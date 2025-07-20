<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <meta name="theme-color" content="#4CAF50">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>Meu Planejador</title>
    
    <!-- Manifest for PWA -->
    <link rel="manifest" href="data:application/json,{&quot;name&quot;:&quot;Meu Planejador&quot;,&quot;short_name&quot;:&quot;Planejador&quot;,&quot;start_url&quot;:&quot;.&quot;,&quot;display&quot;:&quot;standalone&quot;,&quot;background_color&quot;:&quot;#ffffff&quot;,&quot;theme_color&quot;:&quot;#4CAF50&quot;,&quot;orientation&quot;:&quot;portrait&quot;,&quot;icons&quot;:[{&quot;src&quot;:&quot;https://placehold.co/192x192/4CAF50/FFFFFF?text=MP&quot;,&quot;sizes&quot;:&quot;192x192&quot;,&quot;type&quot;:&quot;image/png&quot;},{&quot;src&quot;:&quot;https://placehold.co/512x512/4CAF50/FFFFFF?text=MP&quot;,&quot;sizes&quot;:&quot;512x512&quot;,&quot;type&quot;:&quot;image/png&quot;}]}">
    
    <style>
        /* --- Global Styles & Resets --- */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
            background: #f0f2f5;
            color: #333;
            height: 100vh;
            overflow: hidden;
            user-select: none;
        }
        
        .container {
            max-width: 100%;
            height: 100vh;
            display: flex;
            flex-direction: column;
            background: white;
        }
        
        /* --- Header & Navigation --- */
        header {
            background: linear-gradient(135deg, #4CAF50, #45a049);
            color: white;
            padding: 1rem;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            position: relative;
            z-index: 100;
            text-align: center;
        }
        
        h1 {
            font-size: 1.5rem;
            font-weight: 600;
        }
        
        .nav-tabs {
            display: flex;
            background: white;
            box-shadow: 0 2px 5px rgba(0,0,0,0.08);
            overflow-x: auto;
            scrollbar-width: none;
            -webkit-overflow-scrolling: touch;
            border-bottom: 1px solid #e0e0e0;
        }
        
        .nav-tabs::-webkit-scrollbar { display: none; }
        
        .nav-tab {
            flex: 1;
            min-width: max-content;
            padding: 0.75rem 1rem;
            border: none;
            background: none;
            cursor: pointer;
            font-size: 0.9rem;
            color: #666;
            border-bottom: 3px solid transparent;
            transition: all 0.3s ease;
            white-space: nowrap;
        }
        
        .nav-tab.active {
            color: #4CAF50;
            border-bottom-color: #4CAF50;
            font-weight: 600;
        }
        
        .nav-tab:active { background: #f5f5f5; }
        
        /* --- Content Area --- */
        .content {
            flex: 1;
            overflow-y: auto;
            -webkit-overflow-scrolling: touch;
            padding: 1rem;
            padding-bottom: 80px;
        }
        
        .tab-content {
            display: none;
            animation: fadeIn 0.4s ease;
        }
        
        .tab-content.active { display: block; }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        /* --- Full-screen Event Alert --- */
        .event-alert-overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(255, 0, 0, 0.95);
            z-index: 9999;
            display: none;
            align-items: center; justify-content: center; flex-direction: column;
            animation: pulseAlert 1s ease-in-out infinite;
        }
        
        .event-alert-overlay.show { display: flex; }
        
        @keyframes pulseAlert {
            0%, 100% { background: rgba(255, 0, 0, 0.95); }
            50% { background: rgba(255, 50, 50, 0.98); }
        }
        
        .event-alert-content {
            text-align: center; color: white; padding: 2rem;
            animation: scaleIn 0.5s ease;
        }
        
        @keyframes scaleIn {
            from { transform: scale(0.5); opacity: 0; }
            to { transform: scale(1); opacity: 1; }
        }
        
        .event-alert-icon {
            font-size: 6rem; margin-bottom: 1rem;
            animation: bounce 1s ease-in-out infinite;
        }
        
        @keyframes bounce {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-20px); }
        }
        
        .event-alert-title {
            font-size: 3rem; font-weight: bold; margin-bottom: 1rem;
            text-transform: uppercase; letter-spacing: 2px;
        }
        
        .event-alert-event {
            font-size: 2rem; margin-bottom: 2rem; padding: 1rem;
            background: rgba(255, 255, 255, 0.2); border-radius: 10px;
        }
        
        .event-alert-dismiss {
            background: white; color: #333; font-size: 1.5rem;
            padding: 1rem 3rem; border: none; border-radius: 50px;
            cursor: pointer; font-weight: bold;
            box-shadow: 0 4px 20px rgba(0,0,0,0.3);
            transition: all 0.3s ease;
        }
        
        .event-alert-dismiss:hover {
            transform: scale(1.05);
            box-shadow: 0 6px 25px rgba(0,0,0,0.4);
        }
        
        .event-alert-dismiss:active { transform: scale(0.95); }
        
        /* --- Common Components (Inputs, Buttons) --- */
        .input-group {
            display: flex;
            gap: 0.5rem;
            margin-bottom: 1rem;
        }
        
        input[type="text"], input[type="datetime-local"], textarea {
            flex: 1;
            padding: 0.75rem;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 1rem;
            outline: none;
            transition: border-color 0.3s ease;
        }
        
        input:focus, textarea:focus { border-color: #4CAF50; }
        
        button {
            background: #4CAF50;
            color: white;
            border: none;
            padding: 0.75rem 1.5rem;
            border-radius: 8px;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.3s ease;
            font-weight: 500;
        }
        
        button:disabled { background: #aaa; cursor: not-allowed; }
        button:active { transform: scale(0.95); }
        button:hover:not(:disabled) { background: #45a049; }
        
        button.gemini-btn {
            background: linear-gradient(135deg, #4285F4, #9B72F9);
        }
        button.gemini-btn:hover:not(:disabled) {
            background: linear-gradient(135deg, #3a75d8, #8a63e0);
        }
        
        .btn-danger { background: #dc3545; }
        .btn-danger:hover:not(:disabled) { background: #c82333; }
        
        /* --- Tasks --- */
        .task-item {
            background: #f8f9fa; padding: 1rem; border-radius: 8px;
            margin-bottom: 0.5rem; display: flex; align-items: center;
            gap: 0.75rem; transition: all 0.3s ease; cursor: pointer;
        }
        
        .task-item:active { transform: scale(0.98); }
        .task-item.completed { opacity: 0.6; }
        .task-item.completed .task-text {
            text-decoration: line-through;
            color: #6c757d;
        }
        
        .task-checkbox {
            width: 24px; height: 24px; border: 2px solid #4CAF50;
            border-radius: 4px; cursor: pointer; display: flex;
            align-items: center; justify-content: center;
            transition: all 0.3s ease; flex-shrink: 0;
        }
        
        .task-checkbox.checked { background: #4CAF50; }
        .task-checkbox.checked::after {
            content: '✓'; color: white; font-weight: bold;
        }
        
        .task-text { flex: 1; font-size: 1rem; }
        .task-delete {
            background: #dc3545; color: white; border: none;
            padding: 0.4rem 0.8rem; border-radius: 4px; font-size: 0.85rem;
        }
        
        .task-stats {
            background: #e9ecef; padding: 0.75rem; border-radius: 8px;
            margin-bottom: 1rem; text-align: center; font-size: 0.9rem; color: #666;
        }
        
        /* --- Agenda --- */
        .event-item {
            background: #f8f9fa; padding: 1rem; border-radius: 8px;
            margin-bottom: 0.75rem; border-left: 4px solid #4CAF50;
            transition: all 0.3s ease; position: relative;
        }
        .event-item:active { transform: scale(0.98); }
        .event-time { font-weight: 600; color: #4CAF50; font-size: 0.9rem; }
        .event-title { font-size: 1.1rem; margin: 0.25rem 0; }
        .event-date { font-size: 0.85rem; color: #6c757d; }
        .event-alerts { margin-top: 0.5rem; display: flex; gap: 0.5rem; align-items: center; font-size: 0.85rem; }
        .event-delete-btn {
            position: absolute; top: 0.5rem; right: 0.5rem;
        }

        /* --- Calculator --- */
        .calculator { max-width: 350px; margin: 0 auto; background: #f8f9fa; padding: 1rem; border-radius: 12px; }
        .calc-display {
            background: #333; color: white; padding: 1rem; text-align: right;
            font-size: 1.8rem; border-radius: 8px; margin-bottom: 1rem;
            min-height: 60px; word-wrap: break-word; overflow-wrap: break-word;
        }
        .calc-buttons { display: grid; grid-template-columns: repeat(4, 1fr); gap: 0.5rem; }
        .calc-btn {
            padding: 1.2rem; font-size: 1.2rem; border-radius: 8px;
            background: white; border: 1px solid #ddd; color: #333;
        }
        .calc-btn:active { transform: scale(0.95); background: #e9ecef; }
        .calc-btn.operator { background: #4CAF50; color: white; border: none; }
        .calc-btn.equals { background: #2196F3; color: white; grid-column: span 2; border: none; }
        .calc-btn.clear { background: #dc3545; color: white; border: none; }
        
        /* --- Weather --- */
        .weather-container { text-align: center; padding: 1rem 0; }
        .weather-info { background: #f8f9fa; padding: 2rem; border-radius: 12px; margin-top: 1rem; }
        .weather-temp { font-size: 3rem; font-weight: 300; color: #4CAF50; margin: 1rem 0; }
        .weather-desc { font-size: 1.2rem; color: #666; text-transform: capitalize; }
        .weather-details { display: grid; grid-template-columns: repeat(2, 1fr); gap: 1rem; margin-top: 1.5rem; }
        .weather-detail { background: white; padding: 1rem; border-radius: 8px; }
        .weather-detail-label { font-size: 0.85rem; color: #6c757d; }
        .weather-detail-value { font-size: 1.1rem; font-weight: 600; color: #333; }
        
        /* --- Notes --- */
        .note-item {
            background: #f8f9fa; padding: 1rem; border-radius: 8px;
            margin-bottom: 0.75rem; cursor: pointer; transition: all 0.3s ease;
            position: relative;
        }
        .note-item:active { transform: scale(0.98); }
        .note-title { font-weight: 600; font-size: 1.1rem; margin-bottom: 0.25rem; }
        .note-content { color: #666; font-size: 0.9rem; margin-bottom: 0.25rem; white-space: pre-wrap; word-break: break-word; }
        .note-date { font-size: 0.8rem; color: #6c757d; }
        .note-delete-btn { position: absolute; top: 0.5rem; right: 0.5rem; }
        .note-form { background: #f8f9fa; padding: 1rem; border-radius: 8px; margin-bottom: 1rem; }
        .note-form textarea { min-height: 120px; resize: vertical; }
        
        /* --- Timer --- */
        .timer-display { text-align: center; font-size: 4rem; font-weight: 300; color: #4CAF50; margin: 2rem 0; font-family: 'Courier New', monospace; }
        .timer-controls { display: flex; justify-content: center; gap: 1rem; margin-bottom: 2rem; flex-wrap: wrap; }
        .timer-presets { display: grid; grid-template-columns: repeat(2, 1fr); gap: 0.5rem; max-width: 300px; margin: 0 auto; }
        .preset-btn { background: #e9ecef; color: #333; padding: 0.75rem; font-size: 0.9rem; }
        .preset-btn:hover { background: #dee2e6; }
        
        /* --- Backup --- */
        .backup-section { background: #f8f9fa; padding: 1.5rem; border-radius: 8px; margin-bottom: 1rem; }
        .backup-buttons { display: flex; gap: 1rem; justify-content: center; flex-wrap: wrap; }
        
        /* --- Modal --- */
        .modal {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.5); z-index: 1000;
            align-items: center; justify-content: center;
        }
        .modal.active { display: flex; }
        .modal-content {
            background: white; padding: 1.5rem; border-radius: 12px; width: 90%;
            max-width: 500px; max-height: 80vh; display: flex; flex-direction: column;
            animation: modalIn 0.3s ease;
        }
        @keyframes modalIn {
            from { transform: scale(0.8); opacity: 0; }
            to { transform: scale(1); opacity: 1; }
        }
        .modal-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 1rem; }
        .modal-close { background: none; color: #666; font-size: 1.5rem; padding: 0.5rem; width: auto; }
        .modal-body { overflow-y: auto; margin-bottom: 1rem; white-space: pre-wrap; }
        .modal-footer { display: flex; gap: 0.5rem; justify-content: flex-end; margin-top: auto; }
        
        /* --- PWA Install Prompt --- */
        .install-prompt {
            position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%);
            background: #333; color: white; padding: 1rem 1.5rem; border-radius: 50px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.3); display: none; align-items: center;
            gap: 1rem; z-index: 1000; animation: slideUp 0.5s ease;
        }
        @keyframes slideUp {
            from { transform: translate(-50%, 100px); }
            to { transform: translate(-50%, 0); }
        }
        .install-prompt.show { display: flex; }
        
        /* --- Loading & Status Indicators --- */
        .loading, .app-loader { text-align: center; padding: 2rem; color: #666; }
        .spinner {
            border: 4px solid rgba(0, 0, 0, 0.1); width: 36px; height: 36px;
            border-radius: 50%; border-left-color: #4CAF50;
            animation: spin 1s ease infinite; margin: 20px auto;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .error { background: #f8d7da; color: #721c24; padding: 1rem; border-radius: 8px; margin-bottom: 1rem; }

        /* --- Responsiveness --- */
        @media (max-width: 768px) {
            .timer-display { font-size: 3rem; }
            .calculator { max-width: 100%; }
            .event-alert-title { font-size: 2.5rem; }
            .event-alert-event { font-size: 1.5rem; }
        }

    </style>
</head>
<body>
    <div class="container">
        <div id="appLoader" class="app-loader">
            <div class="spinner"></div>
            <p>Carregando seus dados...</p>
        </div>
        
        <div id="appContent" style="display: none; height: 100%; flex-direction: column; display: flex;">
            <header>
                <h1>📋 Meu Planejador</h1>
            </header>
            
            <nav class="nav-tabs">
                <button class="nav-tab active" data-tab="tasks">📝 Tarefas</button>
                <button class="nav-tab" data-tab="calendar">📅 Agenda</button>
                <button class="nav-tab" data-tab="calculator">🧮 Calculadora</button>
                <button class="nav-tab" data-tab="weather">🌤️ Clima</button>
                <button class="nav-tab" data-tab="notes">📋 Notas</button>
                <button class="nav-tab" data-tab="timer">⏱️ Timer</button>
                <button class="nav-tab" data-tab="backup">💾 Backup</button>
            </nav>
            
            <div class="content">
                <!-- Tasks -->
                <div id="tasks" class="tab-content active">
                    <div class="task-stats" id="taskStats">Pendentes: 0 | Concluídas: 0</div>
                    <div class="input-group">
                        <input type="text" id="taskInput" placeholder="Nova tarefa..." />
                        <button id="addTaskBtn">Adicionar</button>
                    </div>
                     <div class="input-group">
                        <button class="gemini-btn" id="planTasksBtn" style="width: 100%;">✨ Planejar Tarefas com IA</button>
                    </div>
                    <div id="taskList"></div>
                </div>
                
                <!-- Calendar -->
                <div id="calendar" class="tab-content">
                    <div class="input-group">
                        <input type="text" id="eventTitle" placeholder="Título do evento" />
                    </div>
                    <div class="input-group">
                        <input type="datetime-local" id="eventDateTime" />
                        <button id="addEventBtn">Adicionar</button>
                    </div>
                    <h3 style="margin-top: 1.5rem; margin-bottom: 1rem;">Opções de Alerta:</h3>
                    <div style="background: #f8f9fa; padding: 1rem; border-radius: 8px; margin-bottom: 1rem;">
                        <label style="display: flex; align-items: center; margin-bottom: 0.5rem;"><input type="checkbox" id="alertOnTime" checked style="margin-right: 0.5rem;">Alertar na hora exata</label>
                        <label style="display: flex; align-items: center; margin-bottom: 0.5rem;"><input type="checkbox" id="alert3Min" checked style="margin-right: 0.5rem;">Alertar 3 minutos antes</label>
                        <label style="display: flex; align-items: center;"><input type="checkbox" id="alert15Min" style="margin-right: 0.5rem;">Alertar 15 minutos antes</label>
                    </div>
                    <div id="eventList"></div>
                </div>
                
                <!-- Calculator -->
                <div id="calculator" class="tab-content">
                    <div class="calculator">
                        <div class="calc-display" id="calcDisplay">0</div>
                        <div class="calc-buttons" id="calcButtons">
                            <button class="calc-btn clear">C</button>
                            <button class="calc-btn operator">÷</button>
                            <button class="calc-btn operator">×</button>
                            <button class="calc-btn">←</button>
                            <button class="calc-btn">7</button>
                            <button class="calc-btn">8</button>
                            <button class="calc-btn">9</button>
                            <button class="calc-btn operator">-</button>
                            <button class="calc-btn">4</button>
                            <button class="calc-btn">5</button>
                            <button class="calc-btn">6</button>
                            <button class="calc-btn operator">+</button>
                            <button class="calc-btn">1</button>
                            <button class="calc-btn">2</button>
                            <button class="calc-btn">3</button>
                            <button class="calc-btn">.</button>
                            <button class="calc-btn">0</button>
                            <button class="calc-btn">00</button>
                            <button class="calc-btn equals">=</button>
                        </div>
                    </div>
                </div>
                
                <!-- Weather -->
                <div id="weather" class="tab-content">
                    <div class="weather-container">
                        <button id="getWeatherBtn">🔄 Atualizar Clima</button>
                        <div id="weatherInfo" class="weather-info">
                            <div class="loading">Clique em "Atualizar Clima" para começar.</div>
                        </div>
                    </div>
                </div>
                
                <!-- Notes -->
                <div id="notes" class="tab-content">
                    <div class="note-form">
                        <input type="text" id="noteTitle" placeholder="Título da nota" />
                        <textarea id="noteContent" placeholder="Conteúdo da nota..."></textarea>
                        <button id="addNoteBtn">Salvar Nota</button>
                    </div>
                    <div id="notesList"></div>
                </div>
                
                <!-- Timer -->
                <div id="timer" class="tab-content">
                    <div class="timer-display" id="timerDisplay">00:00:00</div>
                    <div class="timer-controls">
                        <button id="startTimer">▶️ Iniciar</button>
                        <button id="pauseTimer" style="display:none">⏸️ Pausar</button>
                        <button id="resetTimer">🔄 Resetar</button>
                    </div>
                    <div class="timer-presets" id="timerPresets">
                         <button class="preset-btn" data-minutes="1">1 min</button><button class="preset-btn" data-minutes="5">5 min</button>
                         <button class="preset-btn" data-minutes="10">10 min</button><button class="preset-btn" data-minutes="15">15 min</button>
                    </div>
                </div>
                
                <!-- Backup -->
                <div id="backup" class="tab-content">
                    <div class="backup-section">
                        <h3>📤 Exportar Dados</h3>
                        <p>Salve todos os seus dados em um arquivo para mantê-los seguros.</p>
                        <button id="exportDataBtn">Exportar Dados</button>
                    </div>
                    <div class="backup-section">
                        <h3>📥 Importar Dados</h3>
                        <p>Restaure seus dados de um arquivo de backup. (Isso substituirá os dados atuais)</p>
                        <input type="file" id="importFile" accept=".json" style="display:none" />
                        <button id="importDataBtn">Importar Dados</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Generic Modal -->
    <div class="modal" id="modal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 id="modalTitle"></h3>
                <button class="modal-close" id="modalCloseBtn">×</button>
            </div>
            <div class="modal-body" id="modalBody"></div>
            <div class="modal-footer" id="modalFooter"></div>
        </div>
    </div>
    
    <!-- Full-screen Event Alert -->
    <div class="event-alert-overlay" id="eventAlertOverlay">
        <div class="event-alert-content">
            <div class="event-alert-icon">⏰</div>
            <div class="event-alert-title">HORA DO COMPROMISSO!</div>
            <div class="event-alert-event" id="alertEventTitle"></div>
            <button class="event-alert-dismiss" id="dismissEventAlertBtn">OK, ENTENDI!</button>
        </div>
    </div>
    
    <!-- PWA Install Prompt -->
    <div class="install-prompt" id="installPrompt">
        <span>Instalar o aplicativo?</span>
        <button id="installPwaBtn">Instalar</button>
    </div>

    <!-- Firebase SDK -->
    <script type="module">
        // Import Firebase modules
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, setDoc, onSnapshot, updateDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // --- GLOBAL STATE & VARIABLES ---
        const dom = { // Cache DOM elements
            appLoader: document.getElementById('appLoader'),
            appContent: document.getElementById('appContent'),
            // Add other frequently accessed elements here
        };

        let appData = {
            tasks: [],
            events: [],
            notes: [],
            weather: null,
            lastWeatherUpdate: null
        };
        
        // Firebase state
        let db, auth, userId, docRef;
        let unsubscribe = null; // To detach Firestore listener

        let timerInterval = null, timerSeconds = 0, timerMode = 'stopwatch', countdownTime = 0;
        let calcDisplay = '0';
        let deferredPrompt;
        let alertSound = null, eventCheckInterval = null;

        // --- INITIALIZATION ---
        document.addEventListener('DOMContentLoaded', async () => {
            try {
                // IMPORTANT: These are placeholders. In a real environment, these would be provided securely.
                const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
                
                if (!firebaseConfig.apiKey) {
                    showError("Configuração do Firebase não encontrada. O aplicativo funcionará no modo offline.");
                    loadDataOffline(); // Fallback to localStorage
                    initializeAppUI();
                    return;
                }

                const app = initializeApp(firebaseConfig);
                auth = getAuth(app);
                db = getFirestore(app);
                
                onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
                        userId = user.uid;
                        // FIX: Correct the Firestore document path to comply with security rules.
                        // All data for this user's planner will be stored in a single document.
                        docRef = doc(db, `artifacts/${appId}/users/${userId}/planner/data`);
                        
                        await loadDataOnline();
                        listenForChanges();
                    } else {
                        await signInAnonymously(auth);
                    }
                    initializeAppUI();
                });

            } catch (error) {
                console.error("Firebase initialization failed:", error);
                showError("Falha ao conectar com a nuvem. Usando modo offline.");
                loadDataOffline();
                initializeAppUI();
            }
        });

        function initializeAppUI() {
            dom.appLoader.style.display = 'none';
            dom.appContent.style.display = 'flex';
            
            updateAllViews();
            setupEventListeners();
            setupEventChecking();
            requestNotificationPermission();
            initializeAlertSound();
            
            window.addEventListener('beforeinstallprompt', (e) => {
                e.preventDefault();
                deferredPrompt = e;
                document.getElementById('installPrompt').classList.add('show');
            });
        }

        function showError(message) {
            dom.appLoader.innerHTML = `<div class="error">${message}</div>`;
        }
        
        // --- DATA PERSISTENCE (ONLINE & OFFLINE) ---

        async function loadDataOnline() {
            try {
                const docSnap = await getDoc(docRef);
                if (docSnap.exists()) {
                    const data = docSnap.data();
                    // Basic data validation and migration
                    appData.tasks = data.tasks || [];
                    appData.events = (data.events || []).map(e => ({...e, notified: e.notified || {}})); // Ensure notified object exists
                    appData.notes = data.notes || [];
                    appData.weather = data.weather || null;
                    appData.lastWeatherUpdate = data.lastWeatherUpdate || null;
                } else {
                    // Create the document for a new user
                    await setDoc(docRef, appData);
                }
            } catch (error) {
                console.error("Error loading data from Firestore:", error);
                showError("Não foi possível carregar dados da nuvem. Usando dados locais.");
                loadDataOffline();
            }
        }

        function listenForChanges() {
            if (unsubscribe) unsubscribe(); // Detach previous listener
            unsubscribe = onSnapshot(docRef, (doc) => {
                if (doc.exists()) {
                    const remoteData = doc.data();
                    // A simple check to avoid overwriting local state if it's the source of the change
                    // A more robust solution would use versioning or timestamps
                    if (JSON.stringify(remoteData) !== JSON.stringify(appData)) {
                         appData = remoteData;
                         updateAllViews();
                         console.log("Data updated from Firestore.");
                    }
                }
            }, (error) => {
                console.error("Firestore snapshot listener error:", error);
                showError("A ligação com a base de dados em tempo real falhou.");
            });
        }

        async function saveData() {
            if (docRef) { // Online
                try {
                    // Use setDoc with merge: true or updateDoc to avoid overwriting
                    await setDoc(docRef, appData, { merge: true });
                } catch (error) {
                    console.error("Error saving to Firestore:", error);
                    saveDataOffline(); // Fallback to local
                }
            } else { // Offline
                saveDataOffline();
            }
        }

        function loadDataOffline() {
            const saved = localStorage.getItem('planejadorData');
            if (saved) appData = JSON.parse(saved);
        }

        function saveDataOffline() {
            localStorage.setItem('planejadorData', JSON.stringify(appData));
        }

        // --- EVENT LISTENERS ---
        function setupEventListeners() {
            document.querySelectorAll('.nav-tab').forEach(tab => tab.addEventListener('click', () => switchTab(tab.dataset.tab)));
            document.getElementById('taskInput').addEventListener('keypress', e => { if (e.key === 'Enter') addTask(); });
            document.getElementById('addTaskBtn').addEventListener('click', addTask);
            document.getElementById('planTasksBtn').addEventListener('click', planTasksWithGemini);
            document.getElementById('addEventBtn').addEventListener('click', addEvent);
            document.getElementById('getWeatherBtn').addEventListener('click', getWeather);
            document.getElementById('addNoteBtn').addEventListener('click', addNote);
            document.getElementById('startTimer').addEventListener('click', startTimer);
            document.getElementById('pauseTimer').addEventListener('click', pauseTimer);
            document.getElementById('resetTimer').addEventListener('click', resetTimer);
            document.getElementById('exportDataBtn').addEventListener('click', exportData);
            document.getElementById('importDataBtn').addEventListener('click', () => document.getElementById('importFile').click());
            document.getElementById('importFile').addEventListener('change', importData);
            document.getElementById('installPwaBtn').addEventListener('click', installPWA);
            document.getElementById('modal').addEventListener('click', e => { if (e.target === e.currentTarget) closeModal(); });
            document.getElementById('modalCloseBtn').addEventListener('click', closeModal);
            document.getElementById('dismissEventAlertBtn').addEventListener('click', dismissEventAlert);

            // Delegated event listeners for dynamic content
            document.getElementById('taskList').addEventListener('click', handleTaskListClick);
            document.getElementById('eventList').addEventListener('click', handleEventListClick);
            document.getElementById('notesList').addEventListener('click', handleNotesListClick);
            document.getElementById('calcButtons').addEventListener('click', handleCalcClick);
            document.getElementById('timerPresets').addEventListener('click', handleTimerPresetClick);
        }

        function handleTaskListClick(e) {
            const target = e.target;
            const taskItem = target.closest('.task-item');
            if (!taskItem) return;
            
            const id = Number(taskItem.dataset.id);
            if (target.classList.contains('task-delete')) {
                e.stopPropagation();
                deleteTask(id);
            } else {
                toggleTask(id);
            }
        }

        function handleEventListClick(e) {
            const deleteBtn = e.target.closest('.event-delete-btn');
            if(deleteBtn) {
                const id = Number(deleteBtn.dataset.id);
                deleteEvent(id);
            }
        }

        function handleNotesListClick(e) {
             const noteItem = e.target.closest('.note-item');
             if(!noteItem) return;
             const id = Number(noteItem.dataset.id);

             if(e.target.closest('.note-delete-btn')){
                e.stopPropagation();
                deleteNote(id);
             } else {
                editNote(id);
             }
        }

        function handleCalcClick(e) {
            if (e.target.tagName !== 'BUTTON') return;
            const value = e.target.textContent;
            if (value === 'C') clearCalc();
            else if (value === '←') deleteCalc();
            else if (value === '=') calculate();
            else appendCalc(value);
        }

        function handleTimerPresetClick(e) {
            if (e.target.classList.contains('preset-btn')) {
                setTimer(Number(e.target.dataset.minutes));
            }
        }
        
        // --- NAVIGATION ---
        function switchTab(tabName) {
            document.querySelectorAll('.nav-tab').forEach(tab => tab.classList.remove('active'));
            document.querySelectorAll('.tab-content').forEach(content => content.classList.remove('active'));
            document.querySelector(`[data-tab="${tabName}"]`).classList.add('active');
            document.getElementById(tabName).classList.add('active');
            
            if (tabName === 'weather' && (!appData.lastWeatherUpdate || Date.now() - appData.lastWeatherUpdate > 600000)) {
                getWeather();
            }
        }

        // --- TASKS ---
        function addTask() {
            const input = document.getElementById('taskInput');
            const text = input.value.trim();
            if (!text) return;
            
            appData.tasks.push({
                id: Date.now(),
                text: text,
                completed: false,
                createdAt: new Date().toISOString()
            });
            
            input.value = '';
            saveData();
            renderTasks();
        }

        function toggleTask(id) {
            const task = appData.tasks.find(t => t.id === id);
            if (task) {
                task.completed = !task.completed;
                saveData();
                renderTasks();
            }
        }

        function deleteTask(id) {
            appData.tasks = appData.tasks.filter(t => t.id !== id);
            saveData();
            renderTasks();
        }

        function renderTasks() {
            const container = document.getElementById('taskList');
            const stats = document.getElementById('taskStats');
            
            const pending = appData.tasks.filter(t => !t.completed).length;
            const completed = appData.tasks.length - pending;
            stats.textContent = `Pendentes: ${pending} | Concluídas: ${completed}`;
            
            container.innerHTML = appData.tasks
                .sort((a, b) => a.completed - b.completed || b.id - a.id)
                .map(task => `
                    <div class="task-item ${task.completed ? 'completed' : ''}" data-id="${task.id}">
                        <div class="task-checkbox ${task.completed ? 'checked' : ''}"></div>
                        <span class="task-text">${escapeHtml(task.text)}</span>
                        <button class="task-delete">×</button>
                    </div>
                `).join('');
        }

        // --- CALENDAR & ALERTS ---
        function addEvent() {
            const title = document.getElementById('eventTitle').value.trim();
            const dateTime = document.getElementById('eventDateTime').value;
            if (!title || !dateTime) return;
            
            appData.events.push({
                id: Date.now(),
                title: title,
                dateTime: new Date(dateTime).toISOString(),
                alerts: {
                    onTime: document.getElementById('alertOnTime').checked,
                    before3Min: document.getElementById('alert3Min').checked,
                    before15Min: document.getElementById('alert15Min').checked
                },
                notified: { onTime: false, before3Min: false, before15Min: false }
            });
            
            document.getElementById('eventTitle').value = '';
            document.getElementById('eventDateTime').value = '';
            saveData();
            renderEvents();
        }

        function deleteEvent(id) {
            appData.events = appData.events.filter(e => e.id !== id);
            saveData();
            renderEvents();
        }

        function renderEvents() {
            const container = document.getElementById('eventList');
            const now = new Date();
            
            container.innerHTML = appData.events
                .filter(event => new Date(event.dateTime) > now)
                .sort((a, b) => new Date(a.dateTime) - new Date(b.dateTime))
                .map(event => {
                    const eventDate = new Date(event.dateTime);
                    const alertsText = [];
                    if (event.alerts?.onTime) alertsText.push('Na hora');
                    if (event.alerts?.before3Min) alertsText.push('3 min antes');
                    if (event.alerts?.before15Min) alertsText.push('15 min antes');
                    
                    return `
                        <div class="event-item">
                            <div class="event-time">${eventDate.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' })}</div>
                            <div class="event-title">${escapeHtml(event.title)}</div>
                            <div class="event-date">${eventDate.toLocaleDateString('pt-BR', {weekday: 'long', day: '2-digit', month: 'long'})}</div>
                            ${alertsText.length > 0 ? `<div class="event-alerts">🔔 ${alertsText.join(', ')}</div>` : ''}
                            <button class="task-delete event-delete-btn" data-id="${event.id}">Excluir</button>
                        </div>
                    `;
                }).join('');
        }

        function setupEventChecking() {
            if (eventCheckInterval) clearInterval(eventCheckInterval);
            eventCheckInterval = setInterval(checkEventAlerts, 10000);
            checkEventAlerts();
        }

        function checkEventAlerts() {
            const now = Date.now();
            let dataChanged = false;
            appData.events.forEach(event => {
                if (!event.alerts) return;
                const eventTime = new Date(event.dateTime).getTime();
                const timeDiff = (eventTime - now) / 1000;

                const check = (type, seconds) => {
                    if (event.alerts[type] && !event.notified[type] && timeDiff > 0 && timeDiff <= seconds) {
                        event.notified[type] = true;
                        showNotification(event.title, `em ${seconds / 60} minutos`);
                        dataChanged = true;
                    }
                };
                
                check('before15Min', 900);
                check('before3Min', 180);

                if (event.alerts.onTime && !event.notified.onTime && Math.abs(timeDiff) <= 30) {
                    event.notified.onTime = true;
                    showEventAlert(event.title);
                    dataChanged = true;
                }
            });
            if (dataChanged) saveData();
        }
        
        // --- WEATHER (with Live API) ---
        function getWeather() {
            const weatherInfo = document.getElementById('weatherInfo');
            weatherInfo.innerHTML = '<div class="spinner"></div><p>Obtendo localização...</p>';
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    position => fetchWeatherData(position.coords),
                    () => { weatherInfo.innerHTML = '<div class="error">Erro ao obter localização. Verifique as permissões.</div>'; }
                );
            } else {
                weatherInfo.innerHTML = '<div class="error">Geolocalização não suportada.</div>';
            }
        }

        async function fetchWeatherData(coords) {
            const weatherInfo = document.getElementById('weatherInfo');
            weatherInfo.innerHTML = '<div class="spinner"></div><p>Carregando dados do clima...</p>';
            const { latitude, longitude } = coords;
            const apiUrl = `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true&hourly=relativehumidity_2m&daily=weathercode,temperature_2m_max,temperature_2m_min&timezone=auto`;

            try {
                const response = await fetch(apiUrl);
                if (!response.ok) throw new Error('Weather API request failed');
                const data = await response.json();
                
                const weatherData = {
                    temp: Math.round(data.current_weather.temperature),
                    description: getWeatherDescription(data.current_weather.weathercode),
                    humidity: data.hourly.relativehumidity_2m[new Date().getHours()], // Approx humidity for current hour
                    windSpeed: Math.round(data.current_weather.windspeed),
                    location: 'Sua Localização'
                };

                appData.weather = weatherData;
                appData.lastWeatherUpdate = Date.now();
                saveData();
                renderWeather();
            } catch (error) {
                console.error("Weather API fetch error:", error);
                weatherInfo.innerHTML = '<div class="error">Não foi possível buscar os dados do clima.</div>';
            }
        }
        
        function getWeatherDescription(code) {
            const codes = {0: "Céu limpo", 1: "Principalmente limpo", 2: "Parcialmente nublado", 3: "Nublado", 45: "Nevoeiro", 48: "Nevoeiro com geada", 51: "Garoa leve", 53: "Garoa moderada", 55: "Garoa densa", 61: "Chuva leve", 63: "Chuva moderada", 65: "Chuva forte", 80: "Pancadas de chuva leves", 81: "Pancadas de chuva moderadas", 82: "Pancadas de chuva violentas", 95: "Trovoada"};
            return codes[code] || "Condição desconhecida";
        }

        function renderWeather() {
            const weatherInfo = document.getElementById('weatherInfo');
            const data = appData.weather;
            if (!data) return;
            weatherInfo.innerHTML = `
                <h3>${data.location}</h3>
                <div class="weather-temp">${data.temp}°C</div>
                <div class="weather-desc">${data.description}</div>
                <div class="weather-details">
                    <div class="weather-detail"><div class="weather-detail-label">Umidade</div><div class="weather-detail-value">${data.humidity}%</div></div>
                    <div class="weather-detail"><div class="weather-detail-label">Vento</div><div class="weather-detail-value">${data.windSpeed} km/h</div></div>
                </div>
            `;
        }

        // --- NOTES ---
        function addNote() {
            const title = document.getElementById('noteTitle').value.trim();
            const content = document.getElementById('noteContent').value.trim();
            if (!title && !content) return;
            
            appData.notes.push({
                id: Date.now(),
                title: title || 'Sem título',
                content: content,
                createdAt: new Date().toISOString()
            });
            
            document.getElementById('noteTitle').value = '';
            document.getElementById('noteContent').value = '';
            saveData();
            renderNotes();
        }

        function deleteNote(id) {
            appData.notes = appData.notes.filter(n => n.id !== id);
            saveData();
            renderNotes();
        }

        function editNote(id) {
            const note = appData.notes.find(n => n.id === id);
            if (!note) return;
            
            const body = `
                <input type="text" id="editNoteTitle" value="${escapeHtml(note.title)}" placeholder="Título da nota" style="width: 100%; margin-bottom: 0.5rem;" />
                <textarea id="editNoteContent" placeholder="Conteúdo da nota..." style="width: 100%; min-height: 150px;">${escapeHtml(note.content)}</textarea>
            `;
            const footer = `
                <button class="gemini-btn" onclick="summarizeNoteWithGemini(${id})">✨ Resumir</button>
                <button class="gemini-btn" onclick="continueNoteWithGemini(event, ${id})">✨ Continuar</button>
                <button onclick="saveEditedNote(${id})">Salvar</button>
                <button class="btn-secondary" onclick="closeModal()">Cancelar</button>
            `;
            openModal('Editar Nota', body, footer);
        }

        function saveEditedNote(id) {
            const note = appData.notes.find(n => n.id === id);
            if (!note) return;
            
            note.title = document.getElementById('editNoteTitle').value.trim() || 'Sem título';
            note.content = document.getElementById('editNoteContent').value.trim();
            saveData();
            renderNotes();
            closeModal();
        }

        function renderNotes() {
            const container = document.getElementById('notesList');
            container.innerHTML = appData.notes
                .sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt))
                .map(note => `
                    <div class="note-item" data-id="${note.id}">
                        <div class="note-title">${escapeHtml(note.title)}</div>
                        <div class="note-content">${escapeHtml(note.content.substring(0, 100))}${note.content.length > 100 ? '...' : ''}</div>
                        <div class="note-date">${new Date(note.createdAt).toLocaleString('pt-BR')}</div>
                        <button class="btn-danger note-delete-btn">Excluir</button>
                    </div>
                `).join('');
        }
        
        // --- OTHER FEATURES (Timer, Calc, Backup, PWA, Gemini, etc.) ---
        
        // --- CALCULATOR ---
        function appendCalc(value) {
            if (calcDisplay === '0' && value !== '.') calcDisplay = '';
            if (calcDisplay === 'Erro') calcDisplay = '';
            calcDisplay += value;
            updateCalcDisplay();
        }
        function clearCalc() { calcDisplay = '0'; updateCalcDisplay(); }
        function deleteCalc() { calcDisplay = calcDisplay.length > 1 ? calcDisplay.slice(0, -1) : '0'; updateCalcDisplay(); }
        function calculate() {
            try {
                // First, replace display operators with functional ones
                const expression = calcDisplay.replace(/×/g, '*').replace(/÷/g, '/');
                // Sanitize to prevent code injection, then evaluate
                const sanitized = expression.replace(/[^-()\d/*+.]/g, '');
                if (sanitized !== expression) {
                    throw new Error("Invalid characters in expression");
                }
                calcDisplay = new Function('return ' + sanitized)().toString();
            } catch (e) { 
                calcDisplay = 'Erro'; 
            }
            updateCalcDisplay();
        }
        function updateCalcDisplay() { document.getElementById('calcDisplay').textContent = calcDisplay; }

        // --- TIMER ---
        function startTimer() {
            if (timerInterval) return;
            document.getElementById('startTimer').style.display = 'none';
            document.getElementById('pauseTimer').style.display = 'inline-block';
            timerInterval = setInterval(updateTimer, 1000);
        }
        function pauseTimer() {
            clearInterval(timerInterval);
            timerInterval = null;
            document.getElementById('startTimer').style.display = 'inline-block';
            document.getElementById('pauseTimer').style.display = 'none';
        }
        function resetTimer() {
            pauseTimer();
            timerSeconds = timerMode === 'countdown' ? countdownTime : 0;
            updateTimerDisplay();
        }
        function setTimer(minutes) {
            pauseTimer();
            timerMode = 'countdown';
            countdownTime = minutes * 60;
            timerSeconds = countdownTime;
            updateTimerDisplay();
        }
        function updateTimer() {
            if (timerMode === 'countdown') {
                if (timerSeconds > 0) timerSeconds--; else { pauseTimer(); playTimerAlert(); }
            } else { timerSeconds++; }
            updateTimerDisplay();
        }
        function updateTimerDisplay() {
            const h = Math.floor(timerSeconds / 3600).toString().padStart(2, '0');
            const m = Math.floor((timerSeconds % 3600) / 60).toString().padStart(2, '0');
            const s = (timerSeconds % 60).toString().padStart(2, '0');
            document.getElementById('timerDisplay').textContent = `${h}:${m}:${s}`;
        }
        
        // --- NOTIFICATIONS & SOUNDS ---
        function initializeAlertSound() {
            if (window.AudioContext || window.webkitAudioContext) {
                const AudioContext = window.AudioContext || window.webkitAudioContext;
                alertSound = new AudioContext();
            }
        }
        function requestNotificationPermission() {
            if ('Notification' in window && Notification.permission === 'default') {
                Notification.requestPermission();
            }
        }
        function showNotification(eventTitle, timeText) {
            playAlertSound();
            if ('Notification' in window && Notification.permission === 'granted') {
                new Notification('📅 Lembrete de Evento', {
                    body: `${eventTitle} ${timeText}!`,
                    icon: 'https://placehold.co/192x192/4CAF50/FFFFFF?text=MP'
                });
            }
        }
        function showEventAlert(eventTitle) {
            playAlertSoundContinuous();
            document.getElementById('alertEventTitle').textContent = eventTitle;
            document.getElementById('eventAlertOverlay').classList.add('show');
            if ('vibrate' in navigator) navigator.vibrate([500, 250, 500]);
        }
        function dismissEventAlert() {
            document.getElementById('eventAlertOverlay').classList.remove('show');
            stopAlertSound();
        }
        let continuousAlertInterval = null;
        function playAlertSound() {
            if (!alertSound) return;
            const oscillator = alertSound.createOscillator();
            oscillator.type = 'sine';
            oscillator.frequency.setValueAtTime(800, alertSound.currentTime);
            oscillator.connect(alertSound.destination);
            oscillator.start();
            oscillator.stop(alertSound.currentTime + 0.3);
        }
        function playAlertSoundContinuous() { stopAlertSound(); continuousAlertInterval = setInterval(playAlertSound, 500); }
        function stopAlertSound() { if (continuousAlertInterval) clearInterval(continuousAlertInterval); }
        function playTimerAlert() { playAlertSound(); if ('Notification' in window && Notification.permission === 'granted') { new Notification('Timer Finalizado!', { body: 'O tempo acabou!' }); } }
        
        // --- BACKUP ---
        function exportData() {
            const dataStr = JSON.stringify(appData, null, 2);
            const dataBlob = new Blob([dataStr], { type: 'application/json' });
            const a = document.createElement('a');
            a.href = URL.createObjectURL(dataBlob);
            a.download = `meu-planejador-backup-${new Date().toISOString().split('T')[0]}.json`;
            a.click();
            URL.revokeObjectURL(a.href);
        }
        function importData(event) {
            const file = event.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = (e) => {
                try {
                    const importedData = JSON.parse(e.target.result);
                    const body = '<p>Isso substituirá todos os dados atuais. Deseja continuar?</p>';
                    const footer = `<button id="confirmImportBtn">Confirmar</button><button class="btn-secondary" onclick="closeModal()">Cancelar</button>`;
                    openModal('Confirmar Importação', body, footer);
                    document.getElementById('confirmImportBtn').onclick = () => performImport(importedData);
                } catch (error) {
                    openModal('Erro', '<p>Arquivo de backup inválido.</p>', '<button onclick="closeModal()">OK</button>');
                }
            };
            reader.readAsText(file);
            event.target.value = '';
        }
        function performImport(importedData) {
            appData = { ...appData, ...importedData };
            saveData();
            updateAllViews();
            closeModal();
        }

        // --- GEMINI API FEATURES ---
        async function callGemini(prompt) {
            const apiKey = ""; // Leave empty
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;
            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ contents: [{ role: "user", parts: [{ text: prompt }] }] })
                });
                if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
                const result = await response.json();
                if (result.candidates?.[0]?.content?.parts?.[0]?.text) {
                    return result.candidates[0].content.parts[0].text;
                } else {
                    return "Não foi possível obter uma resposta da IA.";
                }
            } catch (error) {
                console.error("Error calling Gemini API:", error);
                return "Erro ao conectar com a IA.";
            }
        }
        async function planTasksWithGemini() {
            const pendingTasks = appData.tasks.filter(t => !t.completed);
            if (pendingTasks.length === 0) {
                openModal('Nenhuma Tarefa', '<p>Adicione tarefas pendentes antes de usar a IA.</p>', '<button onclick="closeModal()">OK</button>');
                return;
            }
            const taskList = pendingTasks.map(t => `- ${t.text}`).join('\n');
            const prompt = `Como um assistente de produtividade, crie um plano de ação para as seguintes tarefas:\n\n${taskList}\n\nDivida o plano em etapas claras.`;
            openModal('✨ Planejando com IA...', '<div class="spinner"></div>', '');
            const plan = await callGemini(prompt);
            const body = `<pre style="white-space: pre-wrap; word-wrap: break-word; background: #f8f9fa; padding: 1rem; border-radius: 8px;">${escapeHtml(plan)}</pre>`;
            openModal('✨ Plano de Ação da IA', body, '<button onclick="closeModal()">Fechar</button>');
        }
        async function summarizeNoteWithGemini(noteId) {
            const note = appData.notes.find(n => n.id === noteId);
            if (!note?.content) return;
            const prompt = `Resuma o seguinte texto:\n\n---\n${note.content}\n---`;
            openModal('✨ Resumindo com IA...', '<div class="spinner"></div>', '');
            const summary = await callGemini(prompt);
            openModal('✨ Resumo da IA', `<p>${escapeHtml(summary)}</p>`, '<button onclick="closeModal()">Fechar</button>');
        }
        async function continueNoteWithGemini(event, noteId) {
            const noteContentElement = document.getElementById('editNoteContent');
            if (!noteContentElement?.value) return;
            const prompt = `Continue escrevendo o texto a seguir:\n\n---\n${noteContentElement.value}\n---`;
            const button = event.target;
            button.disabled = true;
            button.innerHTML = '✨ Escrevendo...';
            const continuation = await callGemini(prompt);
            noteContentElement.value += `\n\n${continuation}`;
            button.disabled = false;
            button.innerHTML = '✨ Continuar';
        }

        // --- UTILITIES & MODAL ---
        function escapeHtml(text) {
            if (typeof text !== 'string') return '';
            const div = document.createElement('div');
            div.textContent = text;
            return div.innerHTML;
        }
        function openModal(title, bodyHtml, footerHtml = '') {
            document.getElementById('modalTitle').textContent = title;
            document.getElementById('modalBody').innerHTML = bodyHtml;
            document.getElementById('modalFooter').innerHTML = footerHtml;
            document.getElementById('modal').classList.add('active');
        }
        function closeModal() {
            document.getElementById('modal').classList.remove('active');
        }
        function updateAllViews() {
            renderTasks();
            renderEvents();
            renderNotes();
            updateTimerDisplay();
            updateCalcDisplay();
            if (appData.weather) renderWeather();
        }
        
        // --- PWA ---
        function installPWA() {
            if (deferredPrompt) {
                deferredPrompt.prompt();
                deferredPrompt.userChoice.then(() => {
                    deferredPrompt = null;
                    document.getElementById('installPrompt').classList.remove('show');
                });
            }
        }

        // Expose functions to global scope for inline event handlers
        window.summarizeNoteWithGemini = summarizeNoteWithGemini;
        window.continueNoteWithGemini = continueNoteWithGemini;
        window.saveEditedNote = saveEditedNote;
        window.closeModal = closeModal;

    </script>
</body>
</html>
