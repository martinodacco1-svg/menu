<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>BUON APP-ETITO</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        html, body {
            font-size: 16px;
            background-color: #f3f4f6;
            color: #1f2937;
            padding-bottom: 90px;
            -webkit-text-size-adjust: 100%;
            touch-action: manipulation;
        }
        .mobile-nav {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background: white;
            border-top: 1px solid #e5e7eb;
            z-index: 50;
            padding-bottom: env(safe-area-inset-bottom);
        }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        .pulse-mic {
            animation: pulse-ring 1.2s cubic-bezier(0.215, 0.61, 0.355, 1) infinite;
        }
        @keyframes pulse-ring {
            0% { transform: scale(0.98); box-shadow: 0 0 0 0 rgba(220, 38, 38, 0.8); }
            70% { transform: scale(1.04); box-shadow: 0 0 0 20px rgba(220, 38, 38, 0); }
            100% { transform: scale(0.98); box-shadow: 0 0 0 0 rgba(220, 38, 38, 0); }
        }
    </style>
</head>
<body class="min-h-screen">

    <!-- Header App Nativa -->
    <header class="bg-emerald-600 text-white p-4 shadow-lg sticky top-0 z-40 pt-safe">
        <h1 class="text-2xl font-black text-center tracking-wide flex items-center justify-center gap-2">
            <i class="fa-solid fa-utensils"></i> BUON APP-ETITO
        </h1>
        <p class="text-xs text-center text-emerald-100 font-semibold mt-0.5">Martino • Valentina • Camilla</p>
    </header>

    <!-- NOTIFICA A SCOMPARSA -->
    <div id="successNotification" class="fixed top-16 left-1/2 -translate-x-1/2 bg-emerald-600 text-white px-5 py-3 rounded-2xl shadow-2xl font-black text-sm hidden z-50 flex items-center gap-2 w-11/12 max-w-sm justify-center">
        <i class="fa-solid fa-circle-check text-xl"></i>
        <span id="successText">Operazione completata!</span>
    </div>

    <!-- WIZARD VOCALE / INTERATTIVO INSERIMENTO DISPENSA -->
    <div id="voiceWizardModal" class="fixed inset-0 bg-black/80 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-5 rounded-3xl max-w-sm w-full space-y-4 text-center shadow-2xl border-4 border-emerald-500 relative">
            
            <div id="wizardStepHeader" class="space-y-1">
                <span id="stepBadge" class="bg-emerald-100 text-emerald-800 text-xs font-black px-3 py-1 rounded-full uppercase">PASSO 1 DI 4</span>
                <h3 id="stepTitle" class="text-xl font-black text-gray-800 pt-1">Detti il NOME del prodotto</h3>
                <p id="stepSubtitle" class="text-[11px] text-gray-500 font-semibold">Dì "SALTA" per saltare</p>
            </div>

            <!-- STEP 1 & 2: VOCALE -->
            <div id="wizardVoiceContainer" class="py-1">
                <button id="wizardMicBtn" onclick="listenCurrentStep()" class="w-24 h-24 bg-red-600 text-white rounded-full flex flex-col items-center justify-center mx-auto shadow-2xl active:scale-95 transition">
                    <i id="wizardMicIcon" class="fa-solid fa-microphone text-3xl"></i>
                    <span class="text-[9px] uppercase font-bold mt-1">Tocca & Parla</span>
                </button>
                <div class="bg-gray-50 p-3 rounded-2xl border-2 border-gray-200 min-h-[50px] flex items-center justify-center mt-3">
                    <p id="stepValue" class="text-sm font-black text-emerald-700">In attesa della voce...</p>
                </div>
            </div>

            <!-- STEP 3: CALENDARIO SCADENZA -->
            <div id="wizardCalendarContainer" class="hidden space-y-3 py-2">
                <input type="date" id="wizardCalendarInput" class="w-full border-2 border-emerald-500 p-3 rounded-2xl text-base font-bold bg-gray-50">
                <button onclick="confirmCalendarDate()" class="w-full bg-emerald-600 text-white py-3 rounded-2xl font-black text-sm">Conferma Scadenza</button>
            </div>

            <!-- STEP 4: SELETTORE NUMERI DA 1 A 10 (PEZZI) + FREEZER -->
            <div id="wizardNumbersContainer" class="hidden space-y-3 py-1">
                <p class="text-xs font-bold text-gray-600">Seleziona il numero di pezzi:</p>
                <div class="grid grid-cols-5 gap-2" id="numberGrid">
                    <!-- Generato via JS -->
                </div>
                <div class="flex items-center gap-2 pt-2 bg-blue-50 p-3 rounded-xl border border-blue-200 text-left">
                    <input type="checkbox" id="wizardFreezerCheck" class="w-5 h-5 text-blue-600 rounded">
                    <label for="wizardFreezerCheck" class="text-xs font-bold text-blue-900 cursor-pointer">❄️ Conservato in Freezer (Avvisa per scongelare)</label>
                </div>
            </div>

            <!-- Tasti di Controllo -->
            <div class="flex gap-2 pt-1" id="wizardFooterBtns">
                <button onclick="cancelVoiceWizard()" class="flex-1 bg-gray-200 text-gray-700 py-3 rounded-xl font-black text-xs">Chiudi</button>
                <button onclick="skipWizardStep()" id="skipStepBtn" class="flex-1 bg-amber-500 text-white py-3 rounded-xl font-black text-xs">⏭️ Salta</button>
            </div>
        </div>
    </div>

    <!-- MODALE MODIFICA SINGOLO PRODOTTO -->
    <div id="editPantryModal" class="fixed inset-0 bg-black/70 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-5 rounded-3xl max-w-sm w-full space-y-3 shadow-2xl">
            <h3 class="font-black text-lg text-gray-800 flex items-center gap-2">
                <i class="fa-solid fa-pen-to-square text-emerald-600"></i> Modifica Prodotto
            </h3>
            <input type="hidden" id="editPantryIndex">
            <div>
                <label class="text-xs font-bold text-gray-600">Nome Prodotto</label>
                <input type="text" id="editPantryName" class="w-full border p-2.5 rounded-xl text-sm bg-gray-50">
            </div>
            <div class="grid grid-cols-2 gap-2">
                <div>
                    <label class="text-xs font-bold text-gray-600">Quantità</label>
                    <input type="text" id="editPantryQty" class="w-full border p-2.5 rounded-xl text-sm bg-gray-50">
                </div>
                <div>
                    <label class="text-xs font-bold text-gray-600">N° Pezzi</label>
                    <input type="number" id="editPantryPezzi" min="1" class="w-full border p-2.5 rounded-xl text-sm bg-gray-50">
                </div>
            </div>
            <div>
                <label class="text-xs font-bold text-gray-600">Data Scadenza</label>
                <input type="date" id="editPantryExpiry" class="w-full border p-2.5 rounded-xl text-sm bg-gray-50">
            </div>
            <div class="flex items-center gap-2 pt-1">
                <input type="checkbox" id="editPantryFreezer" class="w-4 h-4 text-blue-600 rounded">
                <label for="editPantryFreezer" class="text-xs font-bold text-gray-700">Conservato in Freezer</label>
            </div>
            <div class="flex justify-end gap-2 pt-2">
                <button onclick="closeEditPantryModal()" class="px-4 py-2 text-xs font-bold text-gray-600">Annulla</button>
                <button onclick="saveEditPantry()" class="px-5 py-2 bg-emerald-600 text-white text-xs font-black rounded-xl">Salva</button>
            </div>
        </div>
    </div>

    <!-- MODALE CAMBIO PASTO -->
    <div id="customMealModal" class="fixed inset-0 bg-black/70 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-5 rounded-3xl max-w-sm w-full space-y-3 shadow-2xl">
            <h3 class="font-black text-lg text-gray-800 flex items-center gap-2">
                <i class="fa-solid fa-utensils text-blue-600"></i> Cosa hai cucinato?
            </h3>
            <p class="text-xs text-gray-500">Gli ingredienti indicati verranno scalati dalla dispensa.</p>
            <input type="hidden" id="customMealDayIndex">
            <input type="hidden" id="customMealType">
            <div>
                <input type="text" id="customMealDesc" placeholder="Es. Pasta con passata e mozzarella" class="w-full border p-3 rounded-xl text-sm bg-gray-50">
            </div>
            <div class="flex justify-end gap-2 pt-2">
                <button onclick="closeCustomMealModal()" class="px-4 py-2 text-xs font-bold text-gray-600">Annulla</button>
                <button onclick="confirmCustomMeal()" class="px-5 py-2 bg-blue-600 text-white text-xs font-black rounded-xl">Conferma</button>
            </div>
        </div>
    </div>

    <!-- CONTAINER PRINCIPALE APPLICATIVO -->
    <main class="p-4 max-w-md mx-auto space-y-4">

        <!-- TAB 1: DISPENSA & FRIGO (DIVISA PER CATEGORIE) -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            
            <div class="bg-white p-4 rounded-3xl shadow-sm border border-gray-200 space-y-3">
                <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-plus-circle text-emerald-600"></i> Aggiungi Prodotto
                </h2>

                <button onclick="startVoiceWizard()" class="w-full bg-gradient-to-r from-red-500 to-rose-600 text-white py-4 px-4 rounded-2xl font-black text-base flex items-center justify-center gap-3 shadow-lg active:scale-95 transition">
                    <i class="fa-solid fa-microphone text-2xl"></i>
                    <span>🎙️ Inserimento Vocale Rapido</span>
                </button>
            </div>

            <!-- PRODOTTI APERTI -->
            <div class="bg-amber-50 p-4 rounded-3xl border-2 border-amber-200 space-y-2">
                <div class="flex justify-between items-center">
                    <h3 class="font-black text-amber-900 text-base flex items-center gap-2">
                        <i class="fa-solid fa-box-open text-amber-600"></i> Prodotti Aperti
                    </h3>
                    <button onclick="openOpenProductModal()" class="bg-amber-600 text-white text-[11px] px-3 py-1.5 rounded-xl font-black">+ Segnala Aperto</button>
                </div>
                <div id="openedProductsList" class="space-y-2"></div>
            </div>

            <!-- DISPENSA DIVISA PER CATEGORIE -->
            <div class="space-y-4">
                <div class="flex justify-between items-center px-1">
                    <h3 class="font-black text-gray-800 text-base uppercase">Dispensa per Aree (<span id="pantryCount">0</span>)</h3>
                    <button onclick="clearPantry()" class="text-xs text-red-500 font-black hover:underline">Svuota tutto</button>
                </div>

                <!-- CATEGORIE CONTAINER -->
                <div id="categoriesContainer" class="space-y-3"></div>
            </div>
        </section>

        <!-- TAB 2: RICETTE -->
        <section id="tab-ricette" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-3xl shadow-sm border border-gray-200 space-y-3">
                <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-utensils text-emerald-600"></i> Ricette Consigliate
                </h2>
                <div id="recipesContainer" class="space-y-3"></div>
            </div>
        </section>

        <!-- TAB 3: MENU SETTIMANALE & SCONGELAMENTO -->
        <section id="tab-menu" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-3xl shadow-sm border border-gray-200 space-y-3">
                <div class="flex justify-between items-center">
                    <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-calendar-days text-emerald-600"></i> Menu Settimanale
                    </h2>
                    <button onclick="generateWeeklyMenu()" class="bg-emerald-600 text-white text-xs px-3 py-1.5 rounded-xl font-black">⚡ Rigenera</button>
                </div>

                <p class="text-xs text-gray-500 font-semibold">Metti la spunta quando consumi un pasto per scalare gli ingredienti:</p>

                <div id="weeklyMenuContainer" class="space-y-3 pt-1"></div>
            </div>
        </section>

        <!-- TAB 4: SPESA -->
        <section id="tab-spesa" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-3xl shadow-sm border border-gray-200 space-y-3">
                <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-cart-shopping text-emerald-600"></i> Lista Spesa
                </h2>
                <div class="flex gap-2">
                    <input type="text" id="shoppingInput" placeholder="Aggiungi alla spesa..." class="border p-3 rounded-2xl text-sm flex-1 bg-gray-50">
                    <button onclick="addShoppingItem()" class="bg-emerald-600 text-white px-4 py-3 rounded-2xl text-lg font-black">+</button>
                </div>
                <div id="shoppingListContainer" class="space-y-2 pt-2"></div>
            </div>
        </section>

        <!-- TAB 5: OFFERTE ESSELUNGA -->
        <section id="tab-offerte" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-3xl shadow-sm border border-gray-200 space-y-3">
                <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-tags text-red-600"></i> Offerte Esselunga
                </h2>
                <div id="esselungaOffers" class="grid grid-cols-1 gap-3"></div>
            </div>
        </section>

        <!-- TAB 6: GRAFICI -->
        <section id="tab-grafici" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-3xl shadow-sm border border-gray-200 space-y-3">
                <h2 class="text-lg font-black text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-chart-pie text-emerald-600"></i> Analisi Spesa
                </h2>
                <div class="flex gap-1 bg-gray-100 p-1.5 rounded-2xl">
                    <button onclick="updateChartPeriod('giornaliero', this)" class="flex-1 py-1.5 text-xs font-black rounded-xl bg-white shadow-sm chart-btn">Oggi</button>
                    <button onclick="updateChartPeriod('settimanale', this)" class="flex-1 py-1.5 text-xs font-black rounded-xl text-gray-600 chart-btn">Settimana</button>
                    <button onclick="updateChartPeriod('mensile', this)" class="flex-1 py-1.5 text-xs font-black rounded-xl text-gray-600 chart-btn">Mese</button>
                </div>
                <div class="relative h-60 w-full pt-1">
                    <canvas id="expenseChart"></canvas>
                </div>
            </div>
        </section>

    </main>

    <!-- MODALE PRODOTTO APERTO -->
    <div id="openProductModal" class="fixed inset-0 bg-black/60 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-5 rounded-3xl max-w-sm w-full space-y-3 shadow-2xl">
            <h3 class="font-black text-base text-gray-800">Segnala Prodotto Aperto</h3>
            <input type="text" id="openProdName" placeholder="Nome prodotto (es. Latte, Passata)" class="w-full border p-2.5 rounded-xl text-sm bg-gray-50">
            <div class="space-y-1">
                <label class="text-xs font-bold text-gray-600">Data di Apertura</label>
                <input type="date" id="openProdDate" class="w-full border p-2.5 rounded-xl text-sm bg-gray-50">
            </div>
            <div class="flex justify-end gap-2 pt-2">
                <button onclick="closeOpenProductModal()" class="px-3 py-2 text-xs font-black text-gray-600">Annulla</button>
                <button onclick="saveOpenProduct()" class="px-4 py-2 bg-amber-600 text-white text-xs font-black rounded-xl">Salva Aperto</button>
            </div>
        </div>
    </div>

    <!-- NAVIGAZIONE MOBILE INFERIORE -->
    <nav class="mobile-nav flex justify-around items-center py-2.5 text-gray-500 text-xs font-bold">
        <button onclick="switchTab('dispensa', this)" class="nav-btn flex flex-col items-center text-emerald-600">
            <i class="fa-solid fa-box-open text-xl mb-0.5"></i> Dispensa
        </button>
        <button onclick="switchTab('ricette', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-utensils text-xl mb-0.5"></i> Ricette
        </button>
        <button onclick="switchTab('menu', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-calendar-days text-xl mb-0.5"></i> Menu
        </button>
        <button onclick="switchTab('spesa', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-cart-shopping text-xl mb-0.5"></i> Spesa
        </button>
        <button onclick="switchTab('offerte', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-tags text-xl mb-0.5"></i> Offerte
        </button>
        <button onclick="switchTab('grafici', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-chart-pie text-xl mb-0.5"></i> Analisi
        </button>
    </nav>

    <script>
        let pantry = JSON.parse(localStorage.getItem('zerosprechi_pantry')) || [
            { id: 1, name: "Mozzarella", qty: "100g", pezzi: 2, expiry: "2026-11-14", freezer: false, category: "Frigorifero" },
            { id: 2, name: "Petto di Pollo", qty: "500g", pezzi: 1, expiry: "2026-11-05", freezer: true, category: "Freezer" },
            { id: 3, name: "Zucchine", qty: "300g", pezzi: 4, expiry: "2026-11-02", freezer: false, category: "Verdura" }
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
            { name: "Filetti di Salmone Norvegese", price: "€ 14,50 /kg", discount: "-25%", tag: "Ricco di Omega-3" },
            { name: "Pastina d'orzo per bambini", price: "€ 0,89", discount: "-15%", tag: "Ideale per Camilla" }
        ];

        let wizardCurrentStep = 1; // 1: Nome, 2: Qta, 3: Scadenza (Calendario), 4: Pezzi & Freezer
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

        // AUTO-SMISTAMENTO CATEGORIE DISPENSA
        function autoDetectCategory(name, isFreezer) {
            if (isFreezer) return "Freezer";
            const lower = name.toLowerCase();
            if (lower.includes('latte') || lower.includes('mozzarella') || lower.includes('yogurt') || lower.includes('affettato') || lower.includes('burro') || lower.includes('formaggio') || lower.includes('panna')) return "Frigorifero";
            if (lower.includes('biscotti') || lower.includes('merendine') || lower.includes('cereali') || lower.includes('brioche') || lower.includes('latte uht') || lower.includes('caffè')) return "Colazione / Merende";
            if (lower.includes('insalata') || lower.includes('zucchine') || lower.includes('pomodoro') || lower.includes('carote') || lower.includes('patate') || lower.includes('verdura') || lower.includes('spinaci')) return "Verdura";
            if (lower.includes('mela') || lower.includes('pera') || lower.includes('banana') || lower.includes('arancia') || lower.includes('frutta') || lower.includes('kiwi')) return "Frutta";
            if (lower.includes('farina') || lower.includes('lievito') || lower.includes('zucchero') || lower.includes('vanillina') || lower.includes('cacao')) return "Prodotti per Pane o Dolci";
            return "Pasta / Dispensa";
        }

        // RENDER DISPENSA DIVISA PER CATEGORIE
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
                                <div class="text-gray-600">Singolo: <b>${item.qty}</b> | Pezzi: <span class="bg-emerald-100 text-emerald-800 font-black px-2 py-0.5 rounded-md">${item.pezzi} pz</span></div>
                                <div class="text-[11px] text-orange-600 font-bold">Scadenza: ${formatItalianDate(item.expiry)}</div>
                            </div>
                            <div class="flex gap-1">
                                <button onclick="openEditPantryModal(${originalIndex})" class="text-blue-600 hover:text-blue-800 p-2 text-base"><i class="fa-solid fa-pen-to-square"></i></button>
                                <button onclick="deletePantryItem(${originalIndex})" class="text-red-500 hover:text-red-700 p-2 text-base"><i class="fa-solid fa-trash-can"></i></button>
                            </div>
                        </div>
                    `;
                });

                catHtml += `</div></div>`;
                container.innerHTML += catHtml;
            });

            saveState();
            renderOpenedProducts();
            renderRecipes();
            renderWeeklyMenu();
        }

        // --- WIZARD VOCALE CON CALENDARIO E NUMERI ---
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
            const subtitle = document.getElementById('stepSubtitle');
            
            const voiceBox = document.getElementById('wizardVoiceContainer');
            const calBox = document.getElementById('wizardCalendarContainer');
            const numBox = document.getElementById('wizardNumbersContainer');
            const skipBtn = document.getElementById('skipStepBtn');

            voiceBox.classList.add('hidden');
            calBox.classList.add('hidden');
            numBox.classList.add('hidden');
            skipBtn.classList.remove('hidden');

            if (wizardCurrentStep === 1) {
                badge.innerText = "PASSO 1 DI 4";
                title.innerText = "Detti il NOME del prodotto";
                subtitle.innerText = "Dì 'SALTA' se vuoi passare oltre";
                voiceBox.classList.remove('hidden');
                document.getElementById('stepValue').innerText = wizardTempProduct.name || "In attesa della voce...";
            } else if (wizardCurrentStep === 2) {
                badge.innerText = "PASSO 2 DI 4";
                title.innerText = "Detti la QUANTITÀ singola";
                subtitle.innerText = "Es. '100 grammi', '1 litro' (o 'SALTA')";
                voiceBox.classList.remove('hidden');
                document.getElementById('stepValue').innerText = wizardTempProduct.qty || "In attesa della voce...";
            } else if (wizardCurrentStep === 3) {
                badge.innerText = "PASSO 3 DI 4";
                title.innerText = "Seleziona Data Scadenza";
                subtitle.innerText = "Usa il calendario o premi Salta";
                calBox.classList.remove('hidden');
                document.getElementById('wizardCalendarInput').value = getFutureDate(7);
            } else if (wizardCurrentStep === 4) {
                badge.innerText = "PASSO 4 DI 4";
                title.innerText = "Seleziona N° Pezzi & Freezer";
                subtitle.innerText = "Clicca sul numero corrispondente";
                numBox.classList.remove('hidden');
                skipBtn.classList.add('hidden'); // Nessun salto necessario, basta cliccare un numero
                renderNumberGrid();
            }
        }

        function renderNumberGrid() {
            const grid = document.getElementById('numberGrid');
            grid.innerHTML = '';
            for(let i=1; i<=10; i++) {
                grid.innerHTML += `
                    <button onclick="selectNumberAndFinish(${i})" class="bg-emerald-50 hover:bg-emerald-600 hover:text-white border-2 border-emerald-500 text-emerald-800 py-2.5 rounded-xl font-black text-base shadow-sm transition active:scale-95">
                        ${i}
                    </button>
                `;
            }
        }

        function listenCurrentStep() {
            const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            if (!SpeechRecognition) {
                alert("Riconoscimento vocale non supportato.");
                return;
            }

            recognition = new SpeechRecognition();
            recognition.lang = 'it-IT';
            recognition.interimResults = false;

            recognition.onstart = function() {
                document.getElementById('wizardMicBtn').classList.add('pulse-mic');
                document.getElementById('stepValue').innerText = "🎙️ Ti sto ascoltando...";
            };

            recognition.onresult = function(event) {
                const text = event.results[0][0].transcript;
                processWizardStepInput(text);
            };

            recognition.onerror = function() {
                document.getElementById('stepValue').innerText = "Non ho capito. Riprova premi il microfono.";
                document.getElementById('wizardMicBtn').classList.remove('pulse-mic');
            };

            recognition.onend = function() {
                document.getElementById('wizardMicBtn').classList.remove('pulse-mic');
            };

            recognition.start();
        }

        function processWizardStepInput(text) {
            const lower = text.trim().toLowerCase();
            const isSkip = lower.includes('salta') || lower.includes('avanti') || lower.includes('niente');

            if (wizardCurrentStep === 1) {
                wizardTempProduct.name = isSkip ? "Alimento Generico" : (text.charAt(0).toUpperCase() + text.slice(1));
                nextWizardStep();
            } else if (wizardCurrentStep === 2) {
                wizardTempProduct.qty = isSkip ? "Quantità std" : text;
                nextWizardStep();
            }
        }

        function nextWizardStep() {
            wizardCurrentStep++;
            updateWizardStepUI();
            if (wizardCurrentStep === 1 || wizardCurrentStep === 2) {
                setTimeout(() => listenCurrentStep(), 400);
            }
        }

        function skipWizardStep() {
            if (wizardCurrentStep === 1) {
                wizardTempProduct.name = "Prodotto Generico";
                nextWizardStep();
            } else if (wizardCurrentStep === 2) {
                wizardTempProduct.qty = "Std";
                nextWizardStep();
            } else if (wizardCurrentStep === 3) {
                wizardTempProduct.expiry = getFutureDate(7); // Default se saltata
                wizardCurrentStep++;
                updateWizardStepUI();
            }
        }

        function confirmCalendarDate() {
            const val = document.getElementById('wizardCalendarInput').value;
            wizardTempProduct.expiry = val || getFutureDate(7);
            wizardCurrentStep++;
            updateWizardStepUI();
        }

        function selectNumberAndFinish(num) {
            wizardTempProduct.pezzi = num;
            wizardTempProduct.freezer = document.getElementById('wizardFreezerCheck').checked;
            finishVoiceWizard();
        }

        function cancelVoiceWizard() { document.getElementById('voiceWizardModal').classList.add('hidden'); }

        function finishVoiceWizard() {
            if(!wizardTempProduct.name) wizardTempProduct.name = "Prodotto";
            if(!wizardTempProduct.qty) wizardTempProduct.qty = "Std";
            if(!wizardTempProduct.expiry) wizardTempProduct.expiry = getFutureDate(7);

            const category = autoDetectCategory(wizardTempProduct.name, wizardTempProduct.freezer);

            pantry.push({
                id: Date.now(),
                name: wizardTempProduct.name,
                qty: wizardTempProduct.qty,
                pezzi: parseInt(wizardTempProduct.pezzi) || 1,
                expiry: wizardTempProduct.expiry,
                freezer: wizardTempProduct.freezer,
                category: category
            });

            cancelVoiceWizard();
            renderPantry();
            showNotification(`✅ Prodotto "${wizardTempProduct.name}" aggiunto in ${category}!`);
        }

        // MENU E AVVISO SCONGELAMENTO GIORNO PRIMA
        function renderWeeklyMenu() {
            const container = document.getElementById('weeklyMenuContainer');
            container.innerHTML = '';

            // Controlla se nel menu ci sono ingredienti in freezer da scongelare il giorno prima
            let defrostAlerts = "";
            weeklyMenuData.forEach((item, idx) => {
                const lowerLunch = item.lunch.toLowerCase();
                const freezerItem = pantry.find(p => p.freezer && lowerLunch.includes(p.name.toLowerCase()));
                if(freezerItem && idx > 0) {
                    // Avvisa nel giorno precedente
                    const prevDay = weeklyMenuData[idx - 1].day;
                    defrostAlerts += `<div class="bg-blue-100 text-blue-900 p-3 rounded-2xl text-xs font-black mb-2">❄️ Ricordati di scongelare **${freezerItem.name}** oggi per la ricetta di domani (${item.day})!</div>`;
                }
            });

            container.innerHTML = defrostAlerts;

            weeklyMenuData.forEach((item, index) => {
                container.innerHTML += `
                    <div class="bg-gray-50 p-3.5 rounded-2xl border border-gray-200 text-xs space-y-2">
                        <div class="font-black text-emerald-800 text-sm border-b pb-1">
                            <span>${item.day}</span>
                        </div>

                        <div class="flex items-center justify-between gap-2 pt-1">
                            <label class="flex items-center gap-2 cursor-pointer flex-1">
                                <input type="checkbox" ${item.lunchDone ? 'checked' : ''} onchange="toggleMealDone(${index}, 'lunch')" class="w-5 h-5 text-emerald-600 rounded">
                                <span class="${item.lunchDone ? 'line-through text-gray-400' : 'text-gray-800 font-bold'} text-xs">
                                    Pranzo: <b>${item.lunch}</b>
                                </span>
                            </label>
                            <button onclick="openCustomMealModal(${index}, 'lunch')" class="text-blue-600 font-bold px-2 py-1 bg-blue-50 rounded-lg text-[10px]">✏️ Cambia</button>
                        </div>

                        <div class="flex items-center justify-between gap-2 pt-1 border-t border-gray-100">
                            <label class="flex items-center gap-2 cursor-pointer flex-1">
                                <input type="checkbox" ${item.dinnerDone ? 'checked' : ''} onchange="toggleMealDone(${index}, 'dinner')" class="w-5 h-5 text-emerald-600 rounded">
                                <span class="${item.dinnerDone ? 'line-through text-gray-400' : 'text-gray-800 font-bold'} text-xs">
                                    Cena: <b>${item.dinner}</b>
                                </span>
                            </label>
                            <button onclick="openCustomMealModal(${index}, 'dinner')" class="text-blue-600 font-bold px-2 py-1 bg-blue-50 rounded-lg text-[10px]">✏️ Cambia</button>
                        </div>
                    </div>
                `;
            });
        }

        function toggleMealDone(dayIndex, mealType) {
            const isDone = mealType === 'lunch' ? !weeklyMenuData[dayIndex].lunchDone : !weeklyMenuData[dayIndex].dinnerDone;
            if (mealType === 'lunch') weeklyMenuData[dayIndex].lunchDone = isDone;
            else weeklyMenuData[dayIndex].dinnerDone = isDone;

            if (isDone) {
                const mealText = mealType === 'lunch' ? weeklyMenuData[dayIndex].lunch : weeklyMenuData[dayIndex].dinner;
                deductIngredientsFromPantry(mealText);
                showNotification(`Pasto confermato! Ingredienti scalati dalla dispensa.`);
            }
            renderWeeklyMenu();
        }

        function deductIngredientsFromPantry(mealText) {
            const lowerMeal = mealText.toLowerCase();
            pantry.forEach((item, idx) => {
                if (lowerMeal.includes(item.name.toLowerCase())) {
                    if (item.pezzi > 1) item.pezzi -= 1;
                    else pantry.splice(idx, 1);
                }
            });
            renderPantry();
        }

        // MODIFICA PRODOTTO IN DISPENSA
        function openEditPantryModal(index) {
            const item = pantry[index];
            document.getElementById('editPantryIndex').value = index;
            document.getElementById('editPantryName').value = item.name;
            document.getElementById('editPantryQty').value = item.qty;
            document.getElementById('editPantryPezzi').value = item.pezzi || 1;
            document.getElementById('editPantryExpiry').value = item.expiry;
            document.getElementById('editPantryFreezer').checked = item.freezer || false;
            document.getElementById('editPantryModal').classList.remove('hidden');
        }

        function closeEditPantryModal() { document.getElementById('editPantryModal').classList.add('hidden'); }

        function saveEditPantry() {
            const index = document.getElementById('editPantryIndex').value;
            const name = document.getElementById('editPantryName').value.trim();
            const freezer = document.getElementById('editPantryFreezer').checked;

            pantry[index].name = name;
            pantry[index].qty = document.getElementById('editPantryQty').value.trim();
            pantry[index].pezzi = parseInt(document.getElementById('editPantryPezzi').value) || 1;
            pantry[index].expiry = document.getElementById('editPantryExpiry').value;
            pantry[index].freezer = freezer;
            pantry[index].category = autoDetectCategory(name, freezer);

            closeEditPantryModal();
            renderPantry();
            showNotification("Modifiche salvate!");
        }

        // CAMBIO PASTO
        function openCustomMealModal(dayIndex, mealType) {
            document.getElementById('customMealDayIndex').value = dayIndex;
            document.getElementById('customMealType').value = mealType;
            document.getElementById('customMealDesc').value = '';
            document.getElementById('customMealModal').classList.remove('hidden');
        }
        function closeCustomMealModal() { document.getElementById('customMealModal').classList.add('hidden'); }
        function confirmCustomMeal() {
            const dayIdx = document.getElementById('customMealDayIndex').value;
            const mealType = document.getElementById('customMealType').value;
            const desc = document.getElementById('customMealDesc').value.trim();
            if (!desc) return;
            if (mealType === 'lunch') { weeklyMenuData[dayIdx].lunch = desc; weeklyMenuData[dayIdx].lunchDone = true; }
            else { weeklyMenuData[dayIdx].dinner = desc; weeklyMenuData[dayIdx].dinnerDone = true; }
            deductIngredientsFromPantry(desc);
            closeCustomMealModal();
            renderWeeklyMenu();
            showNotification("Menu aggiornato!");
        }

        function renderOpenedProducts() {
            const container = document.getElementById('openedProductsList');
            container.innerHTML = '';
            if(openedProducts.length === 0) {
                container.innerHTML = '<p class="text-xs text-amber-700 font-semibold italic">Nessun prodotto aperto.</p>';
                return;
            }
            openedProducts.forEach((item, index) => {
                container.innerHTML += `
                    <div class="flex justify-between items-center bg-white p-3 rounded-2xl border border-amber-200 text-xs shadow-sm">
                        <div>
                            <span class="font-black text-amber-900 text-sm">${item.name}</span>
                            <div class="text-gray-500 text-[11px]">Aperto il: ${formatItalianDate(item.openDate)} | Entro: ${formatItalianDate(item.consumeBy)}</div>
                        </div>
                        <button onclick="openedProducts.splice(${index},1); renderPantry();" class="text-amber-700 p-2 font-black text-base"><i class="fa-solid fa-check"></i></button>
                    </div>
                `;
            });
        }

        function openOpenProductModal() { document.getElementById('openProductModal').classList.remove('hidden'); document.getElementById('openProdDate').value = getTodayDate(); }
        function closeOpenProductModal() { document.getElementById('openProductModal').classList.add('hidden'); }
        function saveOpenProduct() {
            const name = document.getElementById('openProdName').value.trim();
            const openDate = document.getElementById('openProdDate').value;
            if(!name || !openDate) return;
            const consumeBy = addDaysToDate(openDate, 3);
            openedProducts.push({ id: Date.now(), name, openDate, consumeBy });
            closeOpenProductModal();
            renderPantry();
            showNotification("Prodotto aperto registrato!");
        }

        function showNotification(msg) {
            const el = document.getElementById('successNotification');
            document.getElementById('successText').innerText = msg;
            el.classList.remove('hidden');
            setTimeout(() => el.classList.add('hidden'), 3500);
        }

        function deletePantryItem(index) { pantry.splice(index, 1); renderPantry(); }
        function clearPantry() { if(confirm("Svuotare la dispensa?")) { pantry = []; renderPantry(); } }

        function getTodayDate() { return new Date().toISOString().split('T')[0]; }
        function getFutureDate(days) { const d = new Date(); d.setDate(d.getDate() + days); return d.toISOString().split('T')[0]; }
        function addDaysToDate(dateStr, days) { const d = new Date(dateStr); d.setDate(d.getDate() + days); return d.toISOString().split('T')[0]; }
        function formatItalianDate(dateStr) {
            if(!dateStr) return '';
            const parts = dateStr.split('-');
            if(parts.length < 3) return dateStr;
            return `${parts[2]}/${parts[1]}/${parts[0]}`;
        }

        function renderRecipes() {
            document.getElementById('recipesContainer').innerHTML = `
                <div class="border border-emerald-200 bg-emerald-50/50 p-4 rounded-2xl space-y-2">
                    <h3 class="font-bold text-emerald-900 text-base flex items-center gap-2">
                        <i class="fa-solid fa-utensils text-emerald-600"></i> Bocconcini di Pollo e Zucchine
                    </h3>
                    <div class="text-xs space-y-1.5 bg-white p-3 rounded-xl border border-emerald-100">
                        <p><b>🧑 Martino (Sleeve):</b> 120g pollo + 80g zucchine.</p>
                        <p><b>👩 Valentina (Dieta):</b> 180g pollo + 200g zucchine.</p>
                        <p><b>👶 Camilla (2.5 anni):</b> 40g pollo sminuzzato + zucchine morbide.</p>
                    </div>
                </div>
            `;
        }

        function generateWeeklyMenu() { renderWeeklyMenu(); showNotification("Menu aggiornato!"); }
        function renderShopping() {
            const container = document.getElementById('shoppingListContainer');
            container.innerHTML = '';
            shoppingList.forEach((item, index) => {
                container.innerHTML += `
                    <div class="flex justify-between items-center bg-gray-50 p-3.5 rounded-2xl border border-gray-200 text-sm">
                        <label class="flex items-center gap-2 cursor-pointer">
                            <input type="checkbox" ${item.checked ? 'checked' : ''} onclick="shoppingList[${index}].checked = !shoppingList[${index}].checked; saveState();" class="rounded text-emerald-600">
                            <span class="${item.checked ? 'line-through text-gray-400' : 'text-gray-800 font-bold'}">${item.name}</span>
                        </label>
                        <button onclick="shoppingList.splice(${index},1); renderShopping();" class="text-red-400 p-1 text-lg"><i class="fa-solid fa-trash-can"></i></button>
                    </div>
                `;
            });
            saveState();
        }
        function addShoppingItem() {
            const input = document.getElementById('shoppingInput');
            if(!input.value.trim()) return;
            shoppingList.push({ id: Date.now(), name: input.value, checked: false });
            input.value = '';
            renderShopping();
        }

        function renderOffers() {
            const container = document.getElementById('esselungaOffers');
            container.innerHTML = '';
            offers.forEach(off => {
                container.innerHTML += `
                    <div class="border border-red-100 bg-red-50/40 p-4 rounded-2xl flex justify-between items-center">
                        <div>
                            <span class="bg-red-100 text-red-700 text-xs font-bold px-2 py-0.5 rounded-full">${off.discount}</span>
                            <h4 class="font-bold text-gray-800 text-sm mt-1">${off.name}</h4>
                            <p class="text-xs font-semibold text-red-600">${off.price}</p>
                            <p class="text-xs text-gray-500 mt-0.5">${off.tag}</p>
                        </div>
                        <button onclick="shoppingList.push({id: Date.now(), name: '${off.name}', checked: false}); renderShopping(); showNotification('Aggiunto alla spesa!')" class="bg-red-600 text-white text-xs px-3.5 py-2.5 rounded-xl font-black">+ Spesa</button>
                    </div>
                `;
            });
        }

        function renderChart(period) {
            const ctx = document.getElementById('expenseChart').getContext('2d');
            if(expenseChartInstance) expenseChartInstance.destroy();
            let labels = ['Oggi'], data = [18];
            if(period === 'settimanale') { labels = ['Lun', 'Mar', 'Mer', 'Gio', 'Ven', 'Sab', 'Dom']; data = [40, 15, 60, 20, 35, 90, 30]; }
            else if(period === 'mensile') { labels = ['Sett 1', 'Sett 2', 'Sett 3', 'Sett 4']; data = [160, 130, 190, 140]; }
            expenseChartInstance = new Chart(ctx, {
                type: 'bar',
                data: { labels: labels, datasets: [{ label: 'Spesa (€)', data: data, backgroundColor: '#10b981', borderRadius: 6 }] },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } } }
            });
        }
        function updateChartPeriod(period, btn) {
            document.querySelectorAll('.chart-btn').forEach(b => { b.classList.remove('bg-white', 'shadow-sm'); b.classList.add('text-gray-600'); });
            btn.classList.add('bg-white', 'shadow-sm'); btn.classList.remove('text-gray-600');
            renderChart(period);
        }

        window.onload = function() {
            renderPantry();
            renderShopping();
            renderOffers();
        };
    </script>
</body>
</html>
