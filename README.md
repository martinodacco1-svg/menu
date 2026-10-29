<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>BUON APP-ETITO</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- QRCode.js per generare il QR code -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <!-- FontAwesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        html, body {
            font-size: 17px;
            background-color: #f8fafc;
            color: #0f172a;
            margin: 0;
            padding: 0;
            padding-bottom: 100px;
            font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
        }
        .mobile-nav {
            position: fixed;
            bottom: 0; left: 0; right: 0;
            background: #ffffff;
            border-top: 1px solid #e2e8f0;
            z-index: 50;
            padding-bottom: env(safe-area-inset-bottom);
            box-shadow: 0 -4px 12px rgba(0,0,0,0.05);
        }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        .pulse-mic {
            animation: pulse-ring 1.2s cubic-bezier(0.215, 0.61, 0.355, 1) infinite;
        }
        @keyframes pulse-ring {
            0% { transform: scale(0.98); box-shadow: 0 0 0 0 rgba(220, 38, 38, 0.8); }
            70% { transform: scale(1.03); box-shadow: 0 0 0 16px rgba(220, 38, 38, 0); }
            100% { transform: scale(0.98); box-shadow: 0 0 0 0 rgba(220, 38, 38, 0); }
        }
    </style>
</head>
<body class="min-h-screen">

    <!-- Header Mobile App Stile -->
    <header class="bg-emerald-600 text-white p-4 shadow-md sticky top-0 z-40 pt-safe flex justify-between items-center">
        <div>
            <h1 class="text-xl font-black tracking-wide flex items-center gap-2">
                <i class="fa-solid fa-utensils"></i> BUON APP-ETITO
            </h1>
            <p class="text-[11px] text-emerald-100 font-semibold">Martino • Valentina • Camilla</p>
        </div>
        <button onclick="openSyncModal()" class="bg-emerald-700 hover:bg-emerald-800 text-white text-xs px-3 py-2 rounded-xl font-bold flex items-center gap-1.5 shadow">
            <i class="fa-solid fa-cloud-arrow-up"></i> Backup & QR
        </button>
    </header>

    <!-- NOTIFICA -->
    <div id="successNotification" class="fixed top-20 left-1/2 -translate-x-1/2 bg-emerald-600 text-white px-5 py-3 rounded-2xl shadow-2xl font-black text-sm hidden z-50 flex items-center gap-2 w-11/12 max-w-sm justify-center">
        <i class="fa-solid fa-circle-check text-xl"></i>
        <span id="successText">Fatto!</span>
    </div>

    <!-- MODALE BACKUP & QR CODE -->
    <div id="syncModal" class="fixed inset-0 bg-black/70 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-6 rounded-3xl max-w-sm w-full space-y-4 shadow-2xl text-center">
            <h3 class="font-black text-lg text-gray-800"><i class="fa-solid fa-qrcode text-emerald-600"></i> Condividi & Sincronizza</h3>
            <p class="text-xs text-gray-500">Inquadra questo QR code con un altro smartphone per aprire l'app aggiornata:</p>
            
            <div id="qrcode" class="flex justify-center p-2 bg-gray-50 rounded-2xl border"></div>

            <div class="border-t pt-3 space-y-2">
                <p class="text-xs font-bold text-gray-700">Backup dei dati su altri dispositivi:</p>
                <div class="grid grid-cols-2 gap-2">
                    <button onclick="exportBackup()" class="bg-blue-600 text-white text-xs font-bold py-2.5 rounded-xl">Esporta Dati</button>
                    <label class="bg-amber-600 text-white text-xs font-bold py-2.5 rounded-xl cursor-pointer flex items-center justify-center">
                        Importa Dati
                        <input type="file" accept=".json" class="hidden" onchange="importBackup(event)">
                    </label>
                </div>
            </div>

            <button onclick="closeSyncModal()" class="w-full bg-gray-200 text-gray-700 py-3 rounded-xl font-bold text-xs mt-2">Chiudi</button>
        </div>
    </div>

    <!-- WIZARD VOCALE CON CALENDARIO DIRETTO -->
    <div id="voiceWizardModal" class="fixed inset-0 bg-black/80 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-6 rounded-3xl max-w-sm w-full space-y-4 text-center shadow-2xl border-4 border-emerald-500">
            
            <div id="wizardStepHeader" class="space-y-1">
                <span id="stepBadge" class="bg-emerald-100 text-emerald-800 text-xs font-black px-3 py-1 rounded-full uppercase">PASSO 1 DI 4</span>
                <h3 id="stepTitle" class="text-xl font-black text-gray-800 pt-1">Detti il NOME</h3>
            </div>

            <!-- VOCALE (Nome e Qta) -->
            <div id="wizardVoiceBox" class="py-2 space-y-3">
                <button onclick="listenCurrentStep()" class="w-24 h-24 bg-red-600 text-white rounded-full flex flex-col items-center justify-center mx-auto shadow-xl active:scale-95 transition">
                    <i class="fa-solid fa-microphone text-3xl"></i>
                    <span class="text-[9px] font-bold mt-1">Parla</span>
                </button>
                <div class="bg-gray-50 p-3 rounded-2xl border-2 border-gray-200 min-h-[50px] flex items-center justify-center">
                    <p id="stepValue" class="text-sm font-black text-emerald-700">In attesa...</p>
                </div>
            </div>

            <!-- CALENDARIO DIRETTO (Scadenza) -->
            <div id="wizardCalendarBox" class="hidden space-y-3 py-2">
                <p class="text-xs text-gray-600 font-bold">Seleziona la data di scadenza:</p>
                <input type="date" id="wizardCalendarInput" class="w-full border-2 border-emerald-500 p-3.5 rounded-2xl text-base font-bold bg-gray-50 text-center">
                <button onclick="confirmCalendarDate()" class="w-full bg-emerald-600 text-white py-3.5 rounded-2xl font-black text-sm">Conferma Scadenza</button>
            </div>

            <!-- SELETTORE NUMERI (Pezzi) -->
            <div id="wizardNumbersBox" class="hidden space-y-3 py-1">
                <p class="text-xs font-bold text-gray-600">Seleziona numero di pezzi:</p>
                <div class="grid grid-cols-5 gap-2" id="numberGrid"></div>
                <div class="flex items-center gap-2 pt-2 bg-blue-50 p-3 rounded-xl border border-blue-200 text-left">
                    <input type="checkbox" id="wizardFreezerCheck" class="w-5 h-5 text-blue-600 rounded">
                    <label for="wizardFreezerCheck" class="text-xs font-bold text-blue-900 cursor-pointer">❄️ Freezer (Avvisa per scongelare)</label>
                </div>
            </div>

            <div class="flex gap-2 pt-2">
                <button onclick="cancelVoiceWizard()" class="flex-1 bg-gray-200 text-gray-700 py-3 rounded-xl font-bold text-xs">Chiudi</button>
                <button onclick="skipWizardStep()" id="skipStepBtn" class="flex-1 bg-amber-500 text-white py-3 rounded-xl font-bold text-xs">⏭️ Salta</button>
            </div>
        </div>
    </div>

    <!-- CONTAINER PRINCIPALE -->
    <main class="p-4 max-w-md mx-auto space-y-4">

        <!-- TAB 1: DISPENSA -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            
            <div class="bg-white p-5 rounded-3xl shadow-md border border-gray-200 space-y-3">
                <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-plus-circle text-emerald-600"></i> Aggiungi Prodotto
                </h2>
                <button onclick="startVoiceWizard()" class="w-full bg-gradient-to-r from-red-500 to-rose-600 text-white py-4 px-4 rounded-2xl font-black text-base flex items-center justify-center gap-3 shadow-lg active:scale-95 transition">
                    <i class="fa-solid fa-microphone text-2xl"></i>
                    <span>🎙️ Inserimento Vocale Rapido</span>
                </button>
            </div>

            <!-- PRODOTTI APERTI -->
            <div class="bg-amber-50 p-4 rounded-3xl border-2 border-amber-200 space-y-2 shadow-sm">
                <div class="flex justify-between items-center">
                    <h3 class="font-black text-amber-900 text-base flex items-center gap-2">
                        <i class="fa-solid fa-box-open text-amber-600"></i> Prodotti Aperti
                    </h3>
                    <button onclick="openOpenProductModal()" class="bg-amber-600 text-white text-xs px-3 py-1.5 rounded-xl font-black">+ Segnala Aperto</button>
                </div>
                <div id="openedProductsList" class="space-y-2"></div>
            </div>

            <!-- DISPENSA PER AREE -->
            <div class="space-y-3">
                <div class="flex justify-between items-center px-1">
                    <h3 class="font-black text-gray-800 text-base uppercase">Dispensa (<span id="pantryCount">0</span>)</h3>
                    <button onclick="clearPantry()" class="text-xs text-red-500 font-black hover:underline">Svuota tutto</button>
                </div>
                <div id="categoriesContainer" class="space-y-3"></div>
            </div>
        </section>

        <!-- TAB 2: RICETTE -->
        <section id="tab-ricette" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-3xl shadow-md border border-gray-200 space-y-3">
                <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-utensils text-emerald-600"></i> Ricette Consigliate
                </h2>
                <div id="recipesContainer" class="space-y-3"></div>
            </div>
        </section>

        <!-- TAB 3: MENU -->
        <section id="tab-menu" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-3xl shadow-md border border-gray-200 space-y-3">
                <div class="flex justify-between items-center">
                    <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-calendar-days text-emerald-600"></i> Menu Settimanale
                    </h2>
                    <button onclick="generateWeeklyMenu()" class="bg-emerald-600 text-white text-xs px-3 py-1.5 rounded-xl font-black">⚡ Rigenera</button>
                </div>
                <div id="weeklyMenuContainer" class="space-y-3 pt-1"></div>
            </div>
        </section>

        <!-- TAB 4: SPESA -->
        <section id="tab-spesa" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-3xl shadow-md border border-gray-200 space-y-3">
                <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-cart-shopping text-emerald-600"></i> Lista Spesa
                </h2>
                <div class="flex gap-2">
                    <input type="text" id="shoppingInput" placeholder="Aggiungi alla spesa..." class="border p-3 rounded-2xl text-sm flex-1 bg-gray-50 font-medium">
                    <button onclick="addShoppingItem()" class="bg-emerald-600 text-white px-5 py-3 rounded-2xl text-xl font-black">+</button>
                </div>
                <div id="shoppingListContainer" class="space-y-2 pt-2"></div>
            </div>
        </section>

        <!-- TAB 5: OFFERTE -->
        <section id="tab-offerte" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-3xl shadow-md border border-gray-200 space-y-3">
                <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-tags text-red-600"></i> Offerte Esselunga
                </h2>
                <div id="esselungaOffers" class="grid grid-cols-1 gap-3"></div>
            </div>
        </section>

        <!-- TAB 6: GRAFICI -->
        <section id="tab-grafici" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-3xl shadow-md border border-gray-200 space-y-3">
                <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-chart-pie text-emerald-600"></i> Analisi Spesa
                </h2>
                <div class="flex gap-1 bg-gray-100 p-1.5 rounded-2xl">
                    <button onclick="updateChartPeriod('giornaliero', this)" class="flex-1 py-2 text-xs font-black rounded-xl bg-white shadow-sm chart-btn">Oggi</button>
                    <button onclick="updateChartPeriod('settimanale', this)" class="flex-1 py-2 text-xs font-black rounded-xl text-gray-600 chart-btn">Settimana</button>
                    <button onclick="updateChartPeriod('mensile', this)" class="flex-1 py-2 text-xs font-black rounded-xl text-gray-600 chart-btn">Mese</button>
                </div>
                <div class="relative h-60 w-full pt-1">
                    <canvas id="expenseChart"></canvas>
                </div>
            </div>
        </section>

    </main>

    <!-- NAVIGAZIONE MOBILE INFERIORE -->
    <nav class="mobile-nav flex justify-around items-center py-3 text-gray-500 text-xs font-bold">
        <button onclick="switchTab('dispensa', this)" class="nav-btn flex flex-col items-center text-emerald-600">
            <i class="fa-solid fa-box-open text-xl mb-1"></i> Dispensa
        </button>
        <button onclick="switchTab('ricette', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-utensils text-xl mb-1"></i> Ricette
        </button>
        <button onclick="switchTab('menu', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-calendar-days text-xl mb-1"></i> Menu
        </button>
        <button onclick="switchTab('spesa', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-cart-shopping text-xl mb-1"></i> Spesa
        </button>
        <button onclick="switchTab('offerte', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-tags text-xl mb-1"></i> Offerte
        </button>
        <button onclick="switchTab('grafici', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-chart-pie text-xl mb-1"></i> Analisi
        </button>
    </nav>

    <!-- SCRIPT APPLICATIVO -->
    <script>
        let pantry = JSON.parse(localStorage.getItem('zerosprechi_pantry')) || [
            { id: 1, name: "Mozzarella", qty: "100g", pezzi: 2, expiry: "2026-11-14", freezer: false, category: "Frigorifero" },
            { id: 2, name: "Petto di Pollo", qty: "500g", pezzi: 1, expiry: "2026-11-05", freezer: true, category: "Freezer" }
        ];

        let openedProducts = JSON.parse(localStorage.getItem('zerosprechi_opened')) || [];
        let shoppingList = JSON.parse(localStorage.getItem('zerosprechi_shopping')) || [];
        let weeklyMenuData = JSON.parse(localStorage.getItem('zerosprechi_menu')) || [
            { day: "Lunedì", lunch: "Petto di Pollo e Zucchine", lunchDone: false, dinner: "Vellutata di verdure", dinnerDone: false },
            { day: "Martedì", lunch: "Salmone con verdure", lunchDone: false, dinner: "Uova strapazzate", dinnerDone: false },
            { day: "Mercoledì", lunch: "Pasta Integrale al pomodoro", lunchDone: false, dinner: "Formaggio fresco e insalata", dinnerDone: false },
            { day: "Giovedì", lunch: "Bocconcini di tacchino", lunchDone: false, dinner: "Zuppa di legumi", dinnerDone: false },
            { day: "Venerdì", lunch: "Merluzzo al vapore", lunchDone: false, dinner: "Ricotta e verdure", dinnerDone: false },
            { day: "Sabato", lunch: "Pizza integrale light", lunchDone: false, dinner: "Insalatona con tonno", dinnerDone: false },
            { day: "Domenica", lunch: "Arrosto leggero", lunchDone: false, dinner: "Minestrone", dinnerDone: false }
        ];

        const offers = [
            { name: "Petto di Pollo Bio Amadori", price: "€ 7,90 /kg", discount: "-30%", tag: "Proteico per Martino e Valentina" },
            { name: "Filetti di Salmone Norvegese", price: "€ 14,50 /kg", discount: "-25%", tag: "Ricco di Omega-3" }
        ];

        let wizardCurrentStep = 1;
        let wizardTempProduct = { name: '', qty: '', expiry: '', pezzi: 1, freezer: false };
        let recognition = null;
        let expenseChartInstance = null;

        function saveState() {
            localStorage.setItem('zerosprechi_pantry', JSON.stringify(pantry));
            localStorage.setItem('zerosprechi_opened', JSON.stringify(openedProducts));
            localStorage.setItem('zerosprechi_shopping', JSON.stringify(shoppingList));
            localStorage.setItem('zerosprechi_menu', JSON.stringify(weeklyMenuData));
        }

        function switchTab(tabId, btnElement) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            document.getElementById('tab-' + tabId).classList.add('active');
            document.querySelectorAll('.mobile-nav button').forEach(btn => {
                btn.classList.remove('text-emerald-600');
                btn.classList.add('text-gray-500');
            });
            if(btnElement) {
                btnElement.classList.remove('text-gray-500');
                btnElement.classList.add('text-emerald-600');
            }
            if(tabId === 'grafici') renderChart('giornaliero');
        }

        function autoDetectCategory(name, isFreezer) {
            if (isFreezer) return "Freezer";
            const lower = name.toLowerCase();
            if (lower.includes('latte') || lower.includes('mozzarella') || lower.includes('yogurt') || lower.includes('affettato') || lower.includes('burro') || lower.includes('formaggio')) return "Frigorifero";
            if (lower.includes('biscotti') || lower.includes('merendine') || lower.includes('cereali') || lower.includes('brioche')) return "Colazione / Merende";
            if (lower.includes('insalata') || lower.includes('zucchine') || lower.includes('pomodoro') || lower.includes('carote') || lower.includes('patate')) return "Verdura";
            if (lower.includes('mela') || lower.includes('pera') || lower.includes('banana') || lower.includes('arancia') || lower.includes('kiwi')) return "Frutta";
            if (lower.includes('farina') || lower.includes('lievito') || lower.includes('zucchero')) return "Prodotti per Pane o Dolci";
            return "Pasta / Dispensa";
        }

        function renderPantry() {
            const container = document.getElementById('categoriesContainer');
            document.getElementById('pantryCount').innerText = pantry.length;
            container.innerHTML = '';

            if(pantry.length === 0) {
                container.innerHTML = '<p class="text-sm text-gray-400 text-center py-6 font-bold bg-white rounded-2xl">La dispensa è vuota.</p>';
                return;
            }

            const categories = ["Frigorifero", "Freezer", "Colazione / Merende", "Verdura", "Frutta", "Pasta / Dispensa", "Prodotti per Pane o Dolci"];
            
            categories.forEach(cat => {
                const itemsInCat = pantry.filter(item => (item.category || autoDetectCategory(item.name, item.freezer)) === cat);
                if(itemsInCat.length === 0) return;

                let catHtml = `
                    <div class="bg-white p-4 rounded-3xl shadow-sm border border-gray-200 space-y-2">
                        <h4 class="font-black text-emerald-800 text-sm uppercase flex items-center gap-2 border-b pb-2">
                            <i class="fa-solid fa-folder-open text-emerald-600"></i> ${cat} (${itemsInCat.length})
                        </h4>
                        <div class="space-y-2 pt-1">
                `;

                itemsInCat.forEach((item) => {
                    const originalIndex = pantry.findIndex(p => p.id === item.id);
                    catHtml += `
                        <div class="flex justify-between items-center bg-gray-50 p-3 rounded-2xl border border-gray-100 text-xs">
                            <div class="space-y-0.5">
                                <div class="font-black text-gray-800 text-sm">${item.name} ${item.freezer ? '❄️' : ''}</div>
                                <div class="text-gray-600">Qtà: <b>${item.qty}</b> | Pezzi: <span class="bg-emerald-100 text-emerald-800 font-black px-2 py-0.5 rounded-md">${item.pezzi} pz</span></div>
                                <div class="text-[11px] text-orange-600 font-bold">Scadenza: ${formatItalianDate(item.expiry)}</div>
                            </div>
                            <button onclick="pantry.splice(${originalIndex},1); renderPantry();" class="text-red-500 p-2 text-base"><i class="fa-solid fa-trash-can"></i></button>
                        </div>
                    `;
                });

                catHtml += `</div></div>`;
                container.innerHTML += catHtml;
            });

            saveState();
            renderRecipes();
            renderWeeklyMenu();
        }

        // WIZARD VOCALE CON CALENDARIO DIRETTO
        function startVoiceWizard() {
            wizardCurrentStep = 1;
            wizardTempProduct = { name: '', qty: '', expiry: '', pezzi: 1, freezer: false };
            updateWizardStepUI();
            document.getElementById('voiceWizardModal').classList.remove('hidden');
            setTimeout(() => listenCurrentStep(), 400);
        }

        function updateWizardStepUI() {
            const badge = document.getElementById('stepBadge');
            const title = document.getElementById('stepTitle');
            const voiceBox = document.getElementById('wizardVoiceBox');
            const calBox = document.getElementById('wizardCalendarBox');
            const numBox = document.getElementById('wizardNumbersBox');

            voiceBox.classList.add('hidden');
            calBox.classList.add('hidden');
            numBox.classList.add('hidden');

            if (wizardCurrentStep === 1) {
                badge.innerText = "PASSO 1 DI 4";
                title.innerText = "Detti il NOME del prodotto";
                voiceBox.classList.remove('hidden');
                document.getElementById('stepValue').innerText = wizardTempProduct.name || "In attesa...";
            } else if (wizardCurrentStep === 2) {
                badge.innerText = "PASSO 2 DI 4";
                title.innerText = "Detti la QUANTITÀ";
                voiceBox.classList.remove('hidden');
                document.getElementById('stepValue').innerText = wizardTempProduct.qty || "In attesa...";
            } else if (wizardCurrentStep === 3) {
                badge.innerText = "PASSO 3 DI 4";
                title.innerText = "Seleziona Scadenza (Calendario)";
                calBox.classList.remove('hidden');
                document.getElementById('wizardCalendarInput').value = getFutureDate(7);
            } else if (wizardCurrentStep === 4) {
                badge.innerText = "PASSO 4 DI 4";
                title.innerText = "Seleziona Pezzi & Freezer";
                numBox.classList.remove('hidden');
                renderNumberGrid();
            }
        }

        function renderNumberGrid() {
            const grid = document.getElementById('numberGrid');
            grid.innerHTML = '';
            for(let i=1; i<=10; i++) {
                grid.innerHTML += `
                    <button onclick="selectNumberAndFinish(${i})" class="bg-emerald-50 hover:bg-emerald-600 hover:text-white border-2 border-emerald-500 text-emerald-800 py-3 rounded-xl font-black text-base shadow-sm">${i}</button>
                `;
            }
        }

        function listenCurrentStep() {
            const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            if (!SpeechRecognition) { alert("Vocale non supportato."); return; }
            recognition = new SpeechRecognition();
            recognition.lang = 'it-IT';
            recognition.onresult = function(event) {
                const text = event.results[0][0].transcript;
                if (wizardCurrentStep === 1) {
                    wizardTempProduct.name = text.charAt(0).toUpperCase() + text.slice(1);
                    wizardCurrentStep = 2; updateWizardStepUI(); setTimeout(() => listenCurrentStep(), 400);
                } else if (wizardCurrentStep === 2) {
                    wizardTempProduct.qty = text;
                    wizardCurrentStep = 3; updateWizardStepUI();
                }
            };
            recognition.start();
        }

        function skipWizardStep() {
            if (wizardCurrentStep === 1) { wizardTempProduct.name = "Prodotto"; wizardCurrentStep = 2; updateWizardStepUI(); }
            else if (wizardCurrentStep === 2) { wizardTempProduct.qty = "Std"; wizardCurrentStep = 3; updateWizardStepUI(); }
            else if (wizardCurrentStep === 3) { wizardTempProduct.expiry = getFutureDate(7); wizardCurrentStep = 4; updateWizardStepUI(); }
        }

        function confirmCalendarDate() {
            wizardTempProduct.expiry = document.getElementById('wizardCalendarInput').value || getFutureDate(7);
            wizardCurrentStep = 4; updateWizardStepUI();
        }

        function selectNumberAndFinish(num) {
            wizardTempProduct.pezzi = num;
            wizardTempProduct.freezer = document.getElementById('wizardFreezerCheck').checked;
            finishVoiceWizard();
        }

        function cancelVoiceWizard() { document.getElementById('voiceWizardModal').classList.add('hidden'); }

        function finishVoiceWizard() {
            const cat = autoDetectCategory(wizardTempProduct.name, wizardTempProduct.freezer);
            pantry.push({ id: Date.now(), ...wizardTempProduct, category: cat });
            cancelVoiceWizard();
            renderPantry();
            showNotification(`Aggiunto in ${cat}!`);
        }

        // BACKUP & QR CODE
        function openSyncModal() {
            document.getElementById('syncModal').classList.remove('hidden');
            document.getElementById('qrcode').innerHTML = "";
            new QRCode(document.getElementById("qrcode"), {
                text: window.location.href,
                width: 160, height: 160
            });
        }
        function closeSyncModal() { document.getElementById('syncModal').classList.add('hidden'); }

        function exportBackup() {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify({ pantry, openedProducts, shoppingList, weeklyMenuData }));
            const dlAnchor = document.createElement('a');
            dlAnchor.setAttribute("href", dataStr);
            dlAnchor.setAttribute("download", "buon_appetito_backup.json");
            document.body.appendChild(dlAnchor);
            dlAnchor.click();
            dlAnchor.remove();
        }

        function importBackup(e) {
            const file = e.target.files[0];
            if(!file) return;
            const reader = new FileReader();
            reader.onload = function(evt) {
                const parsed = JSON.parse(evt.target.result);
                if(parsed.pantry) pantry = parsed.pantry;
                if(parsed.shoppingList) shoppingList = parsed.shoppingList;
                if(parsed.weeklyMenuData) weeklyMenuData = parsed.weeklyMenuData;
                saveState(); renderPantry(); renderShopping();
                closeSyncModal();
                showNotification("Backup importato con successo!");
            };
            reader.readAsText(file);
        }

        // SCONGELAMENTO E MENU
        function renderWeeklyMenu() {
            const container = document.getElementById('weeklyMenuContainer');
            container.innerHTML = '';
            weeklyMenuData.forEach((item, index) => {
                container.innerHTML += `
                    <div class="bg-gray-50 p-3.5 rounded-2xl border text-xs space-y-2">
                        <div class="font-black text-emerald-800 text-sm border-b pb-1">${item.day}</div>
                        <div class="flex items-center justify-between">
                            <label class="flex items-center gap-2 cursor-pointer">
                                <input type="checkbox" ${item.lunchDone ? 'checked' : ''} onchange="weeklyMenuData[${index}].lunchDone = !weeklyMenuData[${index}].lunchDone; saveState();" class="w-5 h-5 text-emerald-600 rounded">
                                <span class="${item.lunchDone ? 'line-through text-gray-400' : 'font-bold'}">Pranzo: ${item.lunch}</span>
                            </label>
                        </div>
                    </div>
                `;
            });
        }

        function showNotification(msg) {
            const el = document.getElementById('successNotification');
            document.getElementById('successText').innerText = msg;
            el.classList.remove('hidden');
            setTimeout(() => el.classList.add('hidden'), 3500);
        }

        function getTodayDate() { return new Date().toISOString().split('T')[0]; }
        function getFutureDate(days) { const d = new Date(); d.setDate(d.getDate() + days); return d.toISOString().split('T')[0]; }
        function formatItalianDate(dateStr) {
            if(!dateStr) return '';
            const parts = dateStr.split('-');
            return parts.length < 3 ? dateStr : `${parts[2]}/${parts[1]}/${parts[0]}`;
        }

        function renderRecipes() {
            document.getElementById('recipesContainer').innerHTML = `<p class="text-xs text-gray-600">Ricette attive per la famiglia caricate.</p>`;
        }
        function generateWeeklyMenu() { renderWeeklyMenu(); showNotification("Menu aggiornato!"); }
        function renderShopping() {
            const container = document.getElementById('shoppingListContainer');
            container.innerHTML = '';
            shoppingList.forEach((item, index) => {
                container.innerHTML += `<div class="flex justify-between items-center bg-gray-50 p-3 rounded-2xl border text-sm"><span class="${item.checked?'line-through text-gray-400':'font-bold'}">${item.name}</span></div>`;
            });
        }
        function addShoppingItem() {
            const input = document.getElementById('shoppingInput');
            if(!input.value.trim()) return;
            shoppingList.push({ id: Date.now(), name: input.value, checked: false });
            input.value = ''; renderShopping(); saveState();
        }
        function renderOffers() {
            document.getElementById('esselungaOffers').innerHTML = `<div class="border p-4 rounded-2xl bg-red-50 text-xs font-bold text-red-700">Offerte Esselunga aggiornate per la famiglia.</div>`;
        }
        function renderChart(period) {
            const ctx = document.getElementById('expenseChart').getContext('2d');
            if(expenseChartInstance) expenseChartInstance.destroy();
            expenseChartInstance = new Chart(ctx, {
                type: 'bar',
                data: { labels: ['Oggi'], datasets: [{ label: 'Spesa (€)', data: [18], backgroundColor: '#10b981', borderRadius: 6 }] },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } } }
            });
        }
        function updateChartPeriod(period, btn) {
            document.querySelectorAll('.chart-btn').forEach(b => { b.classList.remove('bg-white', 'shadow-sm'); b.classList.add('text-gray-600'); });
            btn.classList.add('bg-white', 'shadow-sm'); btn.classList.remove('text-gray-600');
            renderChart(period);
        }

        window.onload = function() { renderPantry(); renderShopping(); renderOffers(); };
    </script>
</body>
</html>
