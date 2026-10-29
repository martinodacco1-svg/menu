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

    <!-- Header Mobile -->
    <header class="bg-emerald-600 text-white p-4 shadow-md sticky top-0 z-40 pt-safe flex justify-between items-center">
        <div>
            <h1 class="text-xl font-black tracking-wide flex items-center gap-2">
                <i class="fa-solid fa-utensils"></i> BUON APP-ETITO
            </h1>
            <p class="text-[11px] text-emerald-100 font-semibold">Martino • Valentina • Camilla</p>
        </div>
        <button onclick="openSyncModal()" class="bg-emerald-700 hover:bg-emerald-800 text-white text-xs px-3 py-2 rounded-xl font-bold flex items-center gap-1.5 shadow">
            <i class="fa-solid fa-cloud-arrow-up"></i> Backup Dati
        </button>
    </header>

    <!-- NOTIFICA -->
    <div id="successNotification" class="fixed top-20 left-1/2 -translate-x-1/2 bg-emerald-600 text-white px-5 py-3 rounded-2xl shadow-2xl font-black text-sm hidden z-50 flex items-center gap-2 w-11/12 max-w-sm justify-center">
        <i class="fa-solid fa-circle-check text-xl"></i>
        <span id="successText">Fatto!</span>
    </div>

    <!-- MODALE BACKUP -->
    <div id="syncModal" class="fixed inset-0 bg-black/70 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-6 rounded-3xl max-w-sm w-full space-y-4 shadow-2xl text-center">
            <h3 class="font-black text-lg text-gray-800"><i class="fa-solid fa-cloud text-emerald-600"></i> Backup & Sincronizzazione</h3>
            <p class="text-xs text-gray-500">Esporta i dati e importali sull'altro dispositivo:</p>
            <div class="border-t pt-3 space-y-3">
                <button onclick="exportBackup()" class="w-full bg-blue-600 text-white text-sm font-bold py-3 rounded-xl shadow">Esporta Backup Dati (.json)</button>
                <label class="w-full bg-amber-600 text-white text-sm font-bold py-3 rounded-xl cursor-pointer flex items-center justify-center shadow">
                    Importa Backup Dati
                    <input type="file" accept=".json" class="hidden" onchange="importBackup(event)">
                </label>
            </div>
            <button onclick="closeSyncModal()" class="w-full bg-gray-200 text-gray-700 py-3 rounded-xl font-bold text-xs mt-2">Chiudi</button>
        </div>
    </div>

    <!-- MODALE PREFERITI FAMIGLIA -->
    <div id="favoritesModal" class="fixed inset-0 bg-black/70 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-6 rounded-3xl max-w-sm w-full space-y-4 shadow-2xl">
            <h3 class="font-black text-lg text-gray-800 flex items-center gap-2">
                <i class="fa-solid fa-heart text-red-500"></i> Cibi Preferiti della Famiglia
            </h3>
            <p class="text-xs text-gray-500">Inserisci i piatti e gli ingredienti che mangiate di solito:</p>
            <textarea id="familyFavoritesInput" rows="5" class="w-full border p-3 rounded-2xl text-sm bg-gray-50 font-medium" placeholder="Es. Petto di pollo, Zucchine, Salmone..."></textarea>
            <div class="flex gap-2">
                <button onclick="closeFavoritesModal()" class="flex-1 bg-gray-200 text-gray-700 py-3 rounded-xl font-bold text-xs">Annulla</button>
                <button onclick="saveFamilyFavorites()" class="flex-1 bg-emerald-600 text-white py-3 rounded-xl font-bold text-xs">Salva Preferiti</button>
            </div>
        </div>
    </div>

    <!-- WIZARD INSERIMENTO PRODOTTO -->
    <div id="voiceWizardModal" class="fixed inset-0 bg-black/80 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-6 rounded-3xl max-w-sm w-full space-y-4 text-center shadow-2xl border-4 border-emerald-500">
            <div id="wizardStepHeader" class="space-y-1">
                <span id="stepBadge" class="bg-emerald-100 text-emerald-800 text-xs font-black px-3 py-1 rounded-full uppercase">PASSO 1 DI 5</span>
                <h3 id="stepTitle" class="text-xl font-black text-gray-800 pt-1">Detti il NOME</h3>
            </div>
            <div id="wizardVoiceBox" class="py-2 space-y-3">
                <button onclick="listenCurrentStep()" class="w-24 h-24 bg-red-600 text-white rounded-full flex flex-col items-center justify-center mx-auto shadow-xl active:scale-95 transition">
                    <i class="fa-solid fa-microphone text-3xl"></i>
                    <span class="text-[9px] font-bold mt-1">Parla</span>
                </button>
                <div class="bg-gray-50 p-3 rounded-2xl border-2 border-gray-200 min-h-[50px] flex items-center justify-center">
                    <p id="stepValue" class="text-sm font-black text-emerald-700">In attesa...</p>
                </div>
            </div>
            <div id="wizardCalendarBox" class="hidden space-y-3 py-2">
                <p class="text-xs text-gray-600 font-bold">Seleziona la data di scadenza:</p>
                <input type="date" id="wizardCalendarInput" class="w-full border-2 border-emerald-500 p-3.5 rounded-2xl text-base font-bold bg-gray-50 text-center">
                <button onclick="confirmCalendarDate()" class="w-full bg-emerald-600 text-white py-3.5 rounded-2xl font-black text-sm">Conferma Scadenza</button>
            </div>
            <div id="wizardNumbersBox" class="hidden space-y-3 py-1">
                <p class="text-xs font-bold text-gray-600">Seleziona numero di pezzi:</p>
                <div class="grid grid-cols-5 gap-2" id="numberGrid"></div>
            </div>
            <div id="wizardDestinationBox" class="hidden space-y-3 py-1 text-left">
                <p class="text-xs font-bold text-gray-700 text-center">Dove è destinato questo prodotto?</p>
                <div class="space-y-2">
                    <label class="flex items-center gap-3 p-3 bg-gray-50 rounded-2xl border cursor-pointer hover:bg-emerald-50">
                        <input type="radio" name="destChoice" value="Frigorifero" checked class="w-5 h-5 text-emerald-600">
                        <span class="font-bold text-sm text-gray-800">🧊 Frigorifero</span>
                    </label>
                    <label class="flex items-center gap-3 p-3 bg-gray-50 rounded-2xl border cursor-pointer hover:bg-emerald-50">
                        <input type="radio" name="destChoice" value="Freezer" class="w-5 h-5 text-emerald-600">
                        <span class="font-bold text-sm text-gray-800">❄️ Freezer (Avvisa per scongelare)</span>
                    </label>
                    <label class="flex items-center gap-3 p-3 bg-gray-50 rounded-2xl border cursor-pointer hover:bg-emerald-50">
                        <input type="radio" name="destChoice" value="Pasta" class="w-5 h-5 text-emerald-600">
                        <span class="font-bold text-sm text-gray-800">🍝 Pasta / Dispensa</span>
                    </label>
                    <label class="flex items-center gap-3 p-3 bg-gray-50 rounded-2xl border cursor-pointer hover:bg-emerald-50">
                        <input type="radio" name="destChoice" value="Prodotti per pane e dolci" class="w-5 h-5 text-emerald-600">
                        <span class="font-bold text-sm text-gray-800">🥐 Prodotti per pane e dolci</span>
                    </label>
                </div>
                <button onclick="confirmDestinationAndFinish()" class="w-full bg-emerald-600 text-white py-3 rounded-xl font-black text-sm mt-2">Salva in Dispensa</button>
            </div>
            <div class="flex gap-2 pt-2" id="wizardFooterNav">
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

            <!-- CARTELLE DISPENSA -->
            <div class="space-y-3">
                <div class="flex justify-between items-center px-1">
                    <h3 class="font-black text-gray-800 text-base uppercase">Cartelle Dispensa (<span id="pantryCount">0</span>)</h3>
                    <button onclick="clearPantry()" class="text-xs text-red-500 font-black hover:underline">Svuota tutto</button>
                </div>
                <div id="foldersContainer" class="space-y-3"></div>
            </div>
        </section>

        <!-- TAB 2: RICETTE & PREFERITI -->
        <section id="tab-ricette" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-3xl shadow-md border border-gray-200 space-y-3">
                <div class="flex justify-between items-center">
                    <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-utensils text-emerald-600"></i> Ricette & Preferiti
                    </h2>
                    <button onclick="openFavoritesModal()" class="bg-red-500 text-white text-xs px-3 py-2 rounded-xl font-bold flex items-center gap-1 shadow">
                        <i class="fa-solid fa-heart"></i> Cibi Famiglia
                    </button>
                </div>

                <!-- FORM INSERIMENTO NUOVA RICETTA -->
                <div class="bg-emerald-50 p-4 rounded-2xl border border-emerald-200 space-y-3">
                    <h3 class="font-bold text-emerald-900 text-sm flex items-center gap-2">
                        <i class="fa-solid fa-plus-circle text-emerald-600"></i> Inserisci Nuova Ricetta
                    </h3>
                    <input type="text" id="newRecipeName" placeholder="Nome Ricetta (es. Pollo al limone)" class="w-full border p-2.5 rounded-xl text-sm bg-white font-medium">
                    <textarea id="newRecipeDesc" rows="2" placeholder="Dettagli e dosi per Martino, Valentina e Camilla..." class="w-full border p-2.5 rounded-xl text-sm bg-white font-medium"></textarea>
                    <button onclick="addNewCustomRecipe()" class="w-full bg-emerald-600 text-white py-2.5 rounded-xl font-bold text-xs shadow">Salva Nuova Ricetta</button>
                </div>

                <!-- LE TUE RICETTE PREFERITE SALVATE -->
                <div class="space-y-2 pt-2">
                    <h3 class="font-black text-gray-800 text-sm uppercase">⭐ Le Tue Ricette Preferite</h3>
                    <div id="savedFavoritesContainer" class="space-y-2"></div>
                </div>

                <!-- RICETTE PROPOSTE DALL'APP -->
                <div class="space-y-2 pt-2">
                    <h3 class="font-black text-gray-800 text-sm uppercase">💡 Ricette Consigliate dall'App</h3>
                    <div id="recipesContainer" class="space-y-3"></div>
                </div>
            </div>
        </section>

        <!-- TAB 3: MENU -->
        <section id="tab-menu" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-3xl shadow-md border border-gray-200 space-y-3">
                <div class="flex justify-between items-center">
                    <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-calendar-days text-emerald-600"></i> Menu Settimanale
                    </h2>
                    <button onclick="generateWeeklyMenu()" class="bg-emerald-600 text-white text-xs px-3 py-1.5 rounded-xl font-black">⚡ Rigenera Menu</button>
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
            { id: 1, name: "Mozzarella", qty: "100g", pezzi: 2, expiry: "2026-11-14", destination: "Frigorifero" },
            { id: 2, name: "Petto di Pollo", qty: "500g", pezzi: 1, expiry: "2026-11-05", destination: "Freezer" }
        ];

        let openedProducts = JSON.parse(localStorage.getItem('zerosprechi_opened')) || [];
        let shoppingList = JSON.parse(localStorage.getItem('zerosprechi_shopping')) || [];
        let familyFavorites = JSON.parse(localStorage.getItem('zerosprechi_favorites')) || [
            "Petto di pollo", "Zucchine", "Salmone", "Pasta al pomodoro", "Vellutata di verdure", "Uova strapazzate", "Formaggio fresco"
        ];

        let customRecipes = JSON.parse(localStorage.getItem('zerosprechi_custom_recipes')) || [];
        let favoriteRecipes = JSON.parse(localStorage.getItem('zerosprechi_fav_recipes')) || [
            { id: 101, name: "Bocconcini di Pollo e Zucchine", desc: "Martino: 120g pollo + 80g zucchine | Valentina: 180g pollo + 200g zucchine | Camilla: 40g sminuzzato" }
        ];

        let weeklyMenuData = JSON.parse(localStorage.getItem('zerosprechi_menu')) || [
            { day: "Lunedì", lunch: "Petto di Pollo e Zucchine", lunchDone: false, dinner: "Vellutata di verdure", dinnerDone: false },
            { day: "Martedì", lunch: "Salmone con verdure", lunchDone: false, dinner: "Uova strapazzate", dinnerDone: false },
            { day: "Mercoledì", lunch: "Pasta Integrale al pomodoro", lunchDone: false, dinner: "Formaggio fresco e insalata", dinnerDone: false }
        ];

        const offers = [
            { name: "Petto di Pollo Bio Amadori", price: "€ 7,90 /kg", discount: "-30%", tag: "Proteico per Martino e Valentina" },
            { name: "Filetti di Salmone Norvegese", price: "€ 14,50 /kg", discount: "-25%", tag: "Ricco di Omega-3" }
        ];

        let wizardCurrentStep = 1;
        let wizardTempProduct = { name: '', qty: '', expiry: '', pezzi: 1, destination: 'Frigorifero' };
        let recognition = null;
        let expenseChartInstance = null;

        function saveState() {
            localStorage.setItem('zerosprechi_pantry', JSON.stringify(pantry));
            localStorage.setItem('zerosprechi_opened', JSON.stringify(openedProducts));
            localStorage.setItem('zerosprechi_shopping', JSON.stringify(shoppingList));
            localStorage.setItem('zerosprechi_menu', JSON.stringify(weeklyMenuData));
            localStorage.setItem('zerosprechi_favorites', JSON.stringify(familyFavorites));
            localStorage.setItem('zerosprechi_custom_recipes', JSON.stringify(customRecipes));
            localStorage.setItem('zerosprechi_fav_recipes', JSON.stringify(favoriteRecipes));
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

        function getSubCategory(name) {
            const lower = name.toLowerCase();
            if (lower.includes('zucchine') || lower.includes('insalata') || lower.includes('pomodoro') || lower.includes('carote') || lower.includes('patate') || lower.includes('verdura') || lower.includes('spinaci')) return "🥗 Verdure";
            if (lower.includes('pollo') || lower.includes('tacchino') || lower.includes('carne') || lower.includes('arrosto') || lower.includes('bistecca') || lower.includes('macinato')) return "🥩 Carne";
            if (lower.includes('salmone') || lower.includes('merluzzo') || lower.includes('tonno') || lower.includes('pesce') || lower.includes('orata')) return "🐟 Pesce";
            if (lower.includes('mela') || lower.includes('pera') || lower.includes('banana') || lower.includes('arancia') || lower.includes('kiwi') || lower.includes('frutta')) return "🍎 Frutta";
            if (lower.includes('latte') || lower.includes('mozzarella') || lower.includes('formaggio') || lower.includes('yogurt') || lower.includes('ricotta') || lower.includes('burro')) return "🧀 Latticini & Frigo";
            if (lower.includes('pasta') || lower.includes('riso') || lower.includes('farina') || lower.includes('pane') || lower.includes('biscotti')) return "🍝 Pasta & Dispensa";
            return "📦 Altri Prodotti";
        }

        function renderPantry() {
            const container = document.getElementById('foldersContainer');
            document.getElementById('pantryCount').innerText = pantry.length;
            container.innerHTML = '';

            if(pantry.length === 0) {
                container.innerHTML = '<p class="text-sm text-gray-400 text-center py-6 font-bold bg-white rounded-2xl">La dispensa è vuota.</p>';
                return;
            }

            let grouped = {};
            pantry.forEach(item => {
                let subCat = getSubCategory(item.name);
                if(!grouped[subCat]) grouped[subCat] = [];
                grouped[subCat].push(item);
            });

            for(let cat in grouped) {
                let items = grouped[cat];
                let folderId = 'folder_' + cat.replace(/[^a-zA-Z0-9]/g, '');
                
                let html = `
                    <div class="bg-white rounded-3xl shadow-sm border border-gray-200 overflow-hidden">
                        <button onclick="toggleFolder('${folderId}')" class="w-full p-4 flex justify-between items-center bg-gray-50/80 hover:bg-emerald-50/50 transition">
                            <span class="font-black text-gray-800 text-sm flex items-center gap-2">
                                <i class="fa-solid fa-folder-open text-emerald-600"></i> ${cat} (${items.length})
                            </span>
                            <i id="${folderId}_icon" class="fa-solid fa-chevron-down text-gray-400 text-xs transition"></i>
                        </button>
                        <div id="${folderId}" class="hidden p-3 space-y-2 border-t border-gray-100">
                `;

                items.forEach((item) => {
                    let originalIndex = pantry.findIndex(p => p.id === item.id);
                    html += `
                        <div class="flex justify-between items-center bg-gray-50 p-3 rounded-2xl border border-gray-100 text-xs">
                            <div class="space-y-0.5">
                                <div class="font-black text-gray-800 text-sm">${item.name} <span class="text-[10px] text-blue-600 font-bold bg-blue-50 px-2 py-0.5 rounded-full">${item.destination}</span></div>
                                <div class="text-gray-600">Qtà: <b>${item.qty}</b> | Pezzi: <span class="bg-emerald-100 text-emerald-800 font-black px-2 py-0.5 rounded-md">${item.pezzi} pz</span></div>
                                <div class="text-[11px] text-orange-600 font-bold">Scadenza: ${formatItalianDate(item.expiry)}</div>
                            </div>
                            <button onclick="pantry.splice(${originalIndex},1); renderPantry();" class="text-red-500 p-2 text-base"><i class="fa-solid fa-trash-can"></i></button>
                        </div>
                    `;
                });

                html += `</div></div>`;
                container.innerHTML += html;
            }

            saveState();
            renderRecipes();
            renderWeeklyMenu();
        }

        function toggleFolder(folderId) {
            let el = document.getElementById(folderId);
            let icon = document.getElementById(folderId + '_icon');
            if(el.classList.contains('hidden')) {
                el.classList.remove('hidden');
                icon.classList.add('rotate-180');
            } else {
                el.classList.add('hidden');
                icon.classList.remove('rotate-180');
            }
        }

        // GESTIONE AGGIUNTA NUOVA RICETTA E PREFERITI
        function addNewCustomRecipe() {
            let name = document.getElementById('newRecipeName').value.trim();
            let desc = document.getElementById('newRecipeDesc').value.trim();
            if(!name) return;

            let newRec = { id: Date.now(), name, desc };
            customRecipes.push(newRec);
            favoriteRecipes.push(newRec); // Salva direttamente tra i preferiti
            
            document.getElementById('newRecipeName').value = '';
            document.getElementById('newRecipeDesc').value = '';
            saveState();
            renderRecipes();
            showNotification("Nuova ricetta creata e salvata nei preferiti!");
        }

        function toggleFavoriteRecipe(name, desc) {
            let existsIndex = favoriteRecipes.findIndex(r => r.name === name);
            if(existsIndex >= 0) {
                favoriteRecipes.splice(existsIndex, 1);
                showNotification("Rimossa dai preferiti");
            } else {
                favoriteRecipes.push({ id: Date.now(), name, desc });
                showNotification("⭐ Salvata tra le ricette preferite!");
            }
            saveState();
            renderRecipes();
        }

        function renderRecipes() {
            // Renderizza Preferiti
            let favContainer = document.getElementById('savedFavoritesContainer');
            favContainer.innerHTML = '';
            if(favoriteRecipes.length === 0) {
                favContainer.innerHTML = '<p class="text-xs text-gray-400 italic">Nessuna ricetta preferita salvata.</p>';
            } else {
                favoriteRecipes.forEach(rec => {
                    favContainer.innerHTML += `
                        <div class="bg-amber-50/70 p-3 rounded-2xl border border-amber-200 text-xs space-y-1">
                            <div class="flex justify-between items-center">
                                <span class="font-black text-amber-900 text-sm">⭐ ${rec.name}</span>
                                <button onclick="toggleFavoriteRecipe('${rec.name}', '')" class="text-red-500 font-bold px-2 py-1"><i class="fa-solid fa-trash-can"></i></button>
                            </div>
                            <p class="text-gray-700 font-medium">${rec.desc || 'Nessun dettaglio aggiuntivo.'}</p>
                        </div>
                    `;
                });
            }

            // Renderizza Proposte App
            let container = document.getElementById('recipesContainer');
            let appRecipes = [
                { name: "Bocconcini di Pollo e Zucchine", desc: "Martino: 120g pollo + 80g zucchine | Valentina: 180g pollo + 200g zucchine | Camilla: 40g sminuzzato" },
                { name: "Salmone con Verdure al Vapore", desc: "Martino: 100g salmone morbido | Valentina: 150g salmone | Camilla: sminuzzato con carote" },
                { name: "Pasta Integrale al Pomodoro Fresco", desc: "Dosi bilanciate per tutta la famiglia con basilico e olio evo." }
            ];

            container.innerHTML = '';
            appRecipes.forEach(rec => {
                let isFav = favoriteRecipes.some(r => r.name === rec.name);
                container.innerHTML += `
                    <div class="border border-emerald-200 bg-emerald-50/50 p-4 rounded-2xl space-y-2">
                        <div class="flex justify-between items-center">
                            <h4 class="font-bold text-emerald-900 text-sm flex items-center gap-2">
                                <i class="fa-solid fa-utensils text-emerald-600"></i> ${rec.name}
                            </h4>
                            <button onclick="toggleFavoriteRecipe('${rec.name}', '${rec.desc}')" class="bg-${isFav?'red':'emerald'}-600 text-white text-[11px] px-3 py-1.5 rounded-xl font-bold flex items-center gap-1 shadow">
                                <i class="fa-solid fa-star"></i> ${isFav ? 'Preferito' : 'Salva Preferito'}
                            </button>
                        </div>
                        <p class="text-xs text-gray-700 font-medium">${rec.desc}</p>
                    </div>
                `;
            });
        }

        // WIZARD VOCALE
        function startVoiceWizard() {
            wizardCurrentStep = 1;
            wizardTempProduct = { name: '', qty: '', expiry: '', pezzi: 1, destination: 'Frigorifero' };
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
            const destBox = document.getElementById('wizardDestinationBox');
            const skipBtn = document.getElementById('skipStepBtn');

            voiceBox.classList.add('hidden');
            calBox.classList.add('hidden');
            numBox.classList.add('hidden');
            destBox.classList.add('hidden');
            skipBtn.classList.remove('hidden');

            if (wizardCurrentStep === 1) {
                badge.innerText = "PASSO 1 DI 5";
                title.innerText = "Detti il NOME del prodotto";
                voiceBox.classList.remove('hidden');
                document.getElementById('stepValue').innerText = wizardTempProduct.name || "In attesa...";
            } else if (wizardCurrentStep === 2) {
                badge.innerText = "PASSO 2 DI 5";
                title.innerText = "Detti la QUANTITÀ";
                voiceBox.classList.remove('hidden');
                document.getElementById('stepValue').innerText = wizardTempProduct.qty || "In attesa...";
            } else if (wizardCurrentStep === 3) {
                badge.innerText = "PASSO 3 DI 5";
                title.innerText = "Seleziona Scadenza (Calendario)";
                calBox.classList.remove('hidden');
                document.getElementById('wizardCalendarInput').value = getFutureDate(7);
            } else if (wizardCurrentStep === 4) {
                badge.innerText = "PASSO 4 DI 5";
                title.innerText = "Seleziona N° Pezzi";
                numBox.classList.remove('hidden');
                skipBtn.classList.add('hidden');
                renderNumberGrid();
            } else if (wizardCurrentStep === 5) {
                badge.innerText = "PASSO 5 DI 5";
                title.innerText = "Dove è destinato?";
                destBox.classList.remove('hidden');
                skipBtn.classList.add('hidden');
            }
        }

        function renderNumberGrid() {
            const grid = document.getElementById('numberGrid');
            grid.innerHTML = '';
            for(let i=1; i<=10; i++) {
                grid.innerHTML += `
                    <button onclick="selectNumber(${i})" class="bg-emerald-50 hover:bg-emerald-600 hover:text-white border-2 border-emerald-500 text-emerald-800 py-3 rounded-xl font-black text-base shadow-sm">${i}</button>
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

        function selectNumber(num) {
            wizardTempProduct.pezzi = num;
            wizardCurrentStep = 5; updateWizardStepUI();
        }

        function confirmDestinationAndFinish() {
            let radios = document.getElementsByName('destChoice');
            for(let r of radios) {
                if(r.checked) { wizardTempProduct.destination = r.value; break; }
            }
            pantry.push({ id: Date.now(), ...wizardTempProduct });
            cancelVoiceWizard();
            renderPantry();
            showNotification(`Prodotto aggiunto con successo!`);
        }

        function cancelVoiceWizard() { document.getElementById('voiceWizardModal').classList.add('hidden'); }

        // PREFERITI FAMIGLIA & BACKUP
        function openFavoritesModal() {
            document.getElementById('familyFavoritesInput').value = familyFavorites.join(', ');
            document.getElementById('favoritesModal').classList.remove('hidden');
        }
        function closeFavoritesModal() { document.getElementById('favoritesModal').classList.add('hidden'); }
        function saveFamilyFavorites() {
            let val = document.getElementById('familyFavoritesInput').value;
            familyFavorites = val.split(',').map(s => s.trim()).filter(s => s.length > 0);
            saveState();
            closeFavoritesModal();
            showNotification("Preferiti famiglia aggiornati!");
            generateWeeklyMenu();
        }

        function openSyncModal() { document.getElementById('syncModal').classList.remove('hidden'); }
        function closeSyncModal() { document.getElementById('syncModal').classList.add('hidden'); }

        function exportBackup() {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify({ pantry, openedProducts, shoppingList, weeklyMenuData, familyFavorites, customRecipes, favoriteRecipes }));
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
                if(parsed.familyFavorites) familyFavorites = parsed.familyFavorites;
                if(parsed.customRecipes) customRecipes = parsed.customRecipes;
                if(parsed.favoriteRecipes) favoriteRecipes = parsed.favoriteRecipes;
                saveState(); renderPantry(); renderShopping(); renderRecipes();
                closeSyncModal();
                showNotification("Backup importato con successo!");
            };
            reader.readAsText(file);
        }

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

        function generateWeeklyMenu() {
            if(familyFavorites.length > 0) {
                weeklyMenuData.forEach(item => {
                    let fav1 = familyFavorites[Math.floor(Math.random() * familyFavorites.length)];
                    let fav2 = familyFavorites[Math.floor(Math.random() * familyFavorites.length)];
                    item.lunch = fav1 + " con verdure";
                    item.dinner = fav2 + " leggero";
                });
            }
            renderWeeklyMenu();
            showNotification("Menu rigenerato con i cibi preferiti!");
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

        window.onload = function() { renderPantry(); renderShopping(); renderOffers(); renderRecipes(); };
    </script>
</body>
</html>
