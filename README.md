<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, shrink-to-fit=no">
    <title>ZeroSprechi Famiglia</title>
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
        }
        .mobile-nav {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background: white;
            border-top: 1px solid #e5e7eb;
            z-index: 50;
        }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        .pulse-mic {
            animation: pulse-ring 1.2s cubic-bezier(0.215, 0.61, 0.355, 1) infinite;
        }
        @keyframes pulse-ring {
            0% { transform: scale(0.98); box-shadow: 0 0 0 0 rgba(220, 38, 38, 0.7); }
            70% { transform: scale(1.02); box-shadow: 0 0 0 16px rgba(220, 38, 38, 0); }
            100% { transform: scale(0.98); box-shadow: 0 0 0 0 rgba(220, 38, 38, 0); }
        }
    </style>
</head>
<body class="min-h-screen">

    <!-- Header -->
    <header class="bg-emerald-600 text-white p-4 shadow-md sticky top-0 z-40 flex justify-between items-center">
        <div>
            <h1 class="text-2xl font-black flex items-center gap-2"><i class="fa-solid fa-leaf"></i> ZeroSprechi</h1>
            <p class="text-xs text-emerald-100 font-medium">Martino (Sleeve) • Valentina (Dieta) • Camilla (2.5 anni)</p>
        </div>
    </header>

    <!-- WIZARD POP-UP VOCALE SEQUENZIALE -->
    <div id="voiceWizardModal" class="fixed inset-0 bg-black/80 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-6 rounded-3xl max-w-sm w-full space-y-5 text-center shadow-2xl relative">
            
            <div id="wizardStepHeader" class="space-y-1">
                <span id="stepBadge" class="bg-emerald-100 text-emerald-800 text-xs font-black px-3 py-1 rounded-full uppercase">PASSO 1 DI 3</span>
                <h3 id="stepTitle" class="text-xl font-extrabold text-gray-800 pt-2">Pronuncia il NOME del prodotto</h3>
                <p id="stepSubtitle" class="text-xs text-gray-500">Parla dopo aver premuto il microfono</p>
            </div>

            <!-- Pulsante Microfono del Wizard -->
            <div class="py-2">
                <button id="wizardMicBtn" onclick="listenCurrentStep()" class="w-24 h-24 bg-red-600 text-white rounded-full flex items-center justify-center mx-auto shadow-xl hover:bg-red-700 active:scale-95 transition">
                    <i id="wizardMicIcon" class="fa-solid fa-microphone text-3xl"></i>
                </button>
            </div>

            <!-- Valore Riconosciuto per lo Step Attuale -->
            <div class="bg-gray-50 p-3 rounded-2xl border border-gray-200 min-h-[50px] flex items-center justify-center">
                <p id="stepValue" class="text-base font-bold text-emerald-700">In attesa della voce...</p>
            </div>

            <!-- Tasti di Navigazione Manuale/Annulla -->
            <div class="flex gap-2 pt-2">
                <button onclick="cancelVoiceWizard()" class="flex-1 bg-gray-200 text-gray-700 py-3 rounded-xl font-bold text-xs">Annulla</button>
                <button onclick="skipWizardStep()" class="flex-1 bg-emerald-600 text-white py-3 rounded-xl font-bold text-xs">Prossimo Passo ➔</button>
            </div>
        </div>
    </div>

    <!-- POP-UP CONFERMA PRODOTTO INSERITO -->
    <div id="successNotification" class="fixed top-5 left-1/2 -translate-x-1/2 bg-emerald-600 text-white px-6 py-3 rounded-2xl shadow-2xl font-bold text-sm hidden z-50 flex items-center gap-2">
        <i class="fa-solid fa-circle-check text-xl"></i>
        <span id="successText">Prodotto inserito con successo!</span>
    </div>

    <main class="p-4 max-w-lg mx-auto space-y-5">

        <!-- TAB 1: DISPENSA & FRIGO -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            
            <!-- Inserimento Vocale Guidato Sequenziale -->
            <div class="bg-white p-5 rounded-2xl shadow-sm border border-gray-200 space-y-4">
                <h2 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-plus-circle text-emerald-600"></i> Aggiungi alla Dispensa
                </h2>

                <button onclick="startVoiceWizard('dispensa')" class="w-full bg-gradient-to-r from-red-500 to-rose-600 text-white py-4 px-4 rounded-2xl font-black text-base flex items-center justify-center gap-3 shadow-lg active:scale-95 transition">
                    <i class="fa-solid fa-microphone text-2xl"></i>
                    <span>🎙️ Inizia Inserimento Vocale</span>
                </button>

                <!-- Form Manuale di Riserva -->
                <details class="text-xs text-gray-500">
                    <summary class="font-bold cursor-pointer py-1 text-emerald-600">+ Inserisci manualmente</summary>
                    <form id="addItemForm" onsubmit="addPantryItemManual(event)" class="space-y-3 pt-3">
                        <input type="text" id="itemName" placeholder="Nome Prodotto (es. Mozzarella)" required class="w-full border p-3 rounded-xl text-sm bg-gray-50">
                        <div class="grid grid-cols-2 gap-2">
                            <input type="text" id="itemQty" placeholder="Quantità (es. 200g)" required class="w-full border p-3 rounded-xl text-sm bg-gray-50">
                            <input type="date" id="itemExpiry" required class="w-full border p-3 rounded-xl text-sm bg-gray-50">
                        </div>
                        <button type="submit" class="w-full bg-emerald-600 text-white p-3 rounded-xl font-bold text-sm">Salva in Dispensa</button>
                    </form>
                </details>
            </div>

            <!-- PRODOTTI APERTI (SEZIONE DEDICATA) -->
            <div class="bg-amber-50/60 p-5 rounded-2xl border border-amber-200 space-y-3">
                <div class="flex justify-between items-center">
                    <h3 class="font-bold text-amber-900 text-base flex items-center gap-2">
                        <i class="fa-solid fa-box-open text-amber-600"></i> Prodotti Aperti
                    </h3>
                    <button onclick="openOpenProductModal()" class="bg-amber-600 text-white text-xs px-3 py-1.5 rounded-xl font-bold">+ Segnala Aperto</button>
                </div>
                <p class="text-xs text-amber-800">Calcolo automatico durata da aperto per evitare sprechi:</p>
                <div id="openedProductsList" class="space-y-2"></div>
            </div>

            <!-- LISTA DISPENSA COMPLETA -->
            <div class="bg-white p-5 rounded-2xl shadow-sm border border-gray-200">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="font-bold text-gray-800 text-base uppercase flex items-center gap-2">
                        <i class="fa-solid fa-boxes-packing text-emerald-600"></i> Dispensa (<span id="pantryCount">0</span>)
                    </h3>
                    <button onclick="clearPantry()" class="text-xs text-red-500 font-bold hover:underline">Svuota tutto</button>
                </div>
                <div id="pantryList" class="space-y-3"></div>
            </div>
        </section>

        <!-- TAB 2: RICETTE -->
        <section id="tab-ricette" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-2xl shadow-sm border border-gray-200 space-y-3">
                <h2 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-utensils text-emerald-600"></i> Ricette Consigliate
                </h2>
                <p class="text-xs text-gray-500">Porzioni calcolate specificamente per la tua famiglia:</p>
                <div id="recipesContainer" class="space-y-3"></div>
            </div>
        </section>

        <!-- TAB 3: MENU SETTIMANALE -->
        <section id="tab-menu" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-2xl shadow-sm border border-gray-200 space-y-3">
                <div class="flex justify-between items-center">
                    <h2 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-calendar-days text-emerald-600"></i> Menu Settimanale
                    </h2>
                    <button onclick="generateWeeklyMenu()" class="bg-emerald-600 text-white text-xs px-3 py-2 rounded-xl font-bold">⚡ Rigenera</button>
                </div>

                <!-- TASTO VOCALE CAMBIO PROGRAMMA PASTO -->
                <button onclick="startMealSwapVoice()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 px-4 rounded-xl font-bold text-sm flex items-center justify-center gap-2 shadow transition">
                    <i class="fa-solid fa-microphone"></i>
                    <span>🎙️ Cambia programma (Ditta cosa cucini)</span>
                </button>

                <div id="weeklyMenuContainer" class="space-y-2.5 pt-2"></div>
            </div>
        </section>

        <!-- TAB 4: SPESA -->
        <section id="tab-spesa" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-2xl shadow-sm border border-gray-200 space-y-3">
                <h2 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-cart-shopping text-emerald-600"></i> Lista Spesa
                </h2>
                <div class="flex gap-2">
                    <input type="text" id="shoppingInput" placeholder="Aggiungi alla spesa..." class="border p-3 rounded-xl text-base flex-1 bg-gray-50">
                    <button onclick="addShoppingItem()" class="bg-emerald-600 text-white px-5 py-3 rounded-xl text-lg font-bold">+</button>
                </div>
                <div id="shoppingListContainer" class="space-y-2 pt-2"></div>
            </div>
        </section>

        <!-- TAB 5: OFFERTE ESSELUNGA -->
        <section id="tab-offerte" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-2xl shadow-sm border border-gray-200 space-y-3">
                <h2 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-tags text-red-600"></i> Offerte Esselunga
                </h2>
                <div id="esselungaOffers" class="grid grid-cols-1 gap-3"></div>
            </div>
        </section>

        <!-- TAB 6: GRAFICI -->
        <section id="tab-grafici" class="tab-content space-y-4">
            <div class="bg-white p-5 rounded-2xl shadow-sm border border-gray-200 space-y-3">
                <h2 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-chart-pie text-emerald-600"></i> Analisi Spesa
                </h2>
                <div class="flex gap-1 bg-gray-100 p-1 rounded-xl">
                    <button onclick="updateChartPeriod('giornaliero', this)" class="flex-1 py-2 text-xs font-bold rounded-lg bg-white shadow-sm chart-btn">Oggi</button>
                    <button onclick="updateChartPeriod('settimanale', this)" class="flex-1 py-1.5 text-xs font-bold rounded-lg text-gray-600 chart-btn">Settimana</button>
                    <button onclick="updateChartPeriod('mensile', this)" class="flex-1 py-1.5 text-xs font-bold rounded-lg text-gray-600 chart-btn">Mese</button>
                </div>
                <div class="relative h-64 w-full pt-2">
                    <canvas id="expenseChart"></canvas>
                </div>
            </div>
        </section>

    </main>

    <!-- Modale Segnala Prodotto Aperto -->
    <div id="openProductModal" class="fixed inset-0 bg-black/60 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-5 rounded-2xl max-w-sm w-full space-y-3">
            <h3 class="font-bold text-base text-gray-800">Segnala Prodotto Aperto</h3>
            <p class="text-xs text-gray-500">L'app calcolerà quanti giorni ti restano per consumarlo prima che vada a male.</p>
            <input type="text" id="openProdName" placeholder="Nome prodotto (es. Latte, Passata)" class="w-full border p-2.5 rounded-xl text-sm bg-gray-50">
            <div class="space-y-1">
                <label class="text-xs font-bold text-gray-600">Data di Apertura</label>
                <input type="date" id="openProdDate" class="w-full border p-2.5 rounded-xl text-sm bg-gray-50">
            </div>
            <div class="flex justify-end gap-2 pt-2">
                <button onclick="closeOpenProductModal()" class="px-3 py-2 text-xs font-bold text-gray-600">Annulla</button>
                <button onclick="saveOpenProduct()" class="px-4 py-2 bg-amber-600 text-white text-xs font-bold rounded-xl">Salva Aperto</button>
            </div>
        </div>
    </div>

    <!-- Navigazione Mobile Inferiore -->
    <nav class="mobile-nav flex justify-around items-center py-3 text-gray-500 text-xs">
        <button onclick="switchTab('dispensa', this)" class="nav-btn flex flex-col items-center text-emerald-600 font-bold">
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

    <script>
        // Data Base Locale
        let pantry = JSON.parse(localStorage.getItem('zerosprechi_pantry')) || [
            { id: 1, name: "Petto di Pollo", qty: "500g", expiry: "2026-11-05" },
            { id: 2, name: "Zucchine", qty: "4 pz", expiry: "2026-11-02" }
        ];

        let openedProducts = JSON.parse(localStorage.getItem('zerosprechi_opened')) || [
            { id: 101, name: "Latte Intero", openDate: getTodayDate(), consumeBy: getFutureDate(3) }
        ];

        let shoppingList = JSON.parse(localStorage.getItem('zerosprechi_shopping')) || [
            { id: 1, name: "Olio Extravergine d'Oliva", checked: false }
        ];

        let weeklyMenuData = [
            { day: "Lunedì", lunch: "Petto di Pollo e Zucchine", dinner: "Vellutata di verdure" },
            { day: "Martedì", lunch: "Salmone con verdure", dinner: "Uova strapazzate" },
            { day: "Mercoledì", lunch: "Pasta Integrale al pomodoro", dinner: "Formaggio fresco e insalata" },
            { day: "Giovedì", lunch: "Bocconcini di tacchino", dinner: "Zuppa di legumi" },
            { day: "Venerdì", lunch: "Merluzzo al vapore", dinner: "Ricotta e verdure" },
            { day: "Sabato", lunch: "Pizza integrale light", dinner: "Insalatona con tonno" },
            { day: "Domenica", lunch: "Arrosto leggero", dinner: "Minestrone" }
        ];

        const offers = [
            { name: "Petto di Pollo Bio Amadori", price: "€ 7,90 /kg", discount: "-30%", tag: "Proteico per Martino e Valentina" },
            { name: "Filetti di Salmone Norvegese", price: "€ 14,50 /kg", discount: "-25%", tag: "Ricco di Omega-3" },
            { name: "Pastina d'orzo per bambini", price: "€ 0,89", discount: "-15%", tag: "Ideale per Camilla" }
        ];

        // STATO DEL WIZARD VOCALE SEQUENZIALE
        let wizardCurrentStep = 1; // 1: Nome, 2: Quantità, 3: Scadenza
        let wizardTempProduct = { name: '', qty: '', expiry: '' };
        let isListening = false;
        let recognition = null;
        let expenseChartInstance = null;

        function saveState() {
            localStorage.setItem('zerosprechi_pantry', JSON.stringify(pantry));
            localStorage.setItem('zerosprechi_opened', JSON.stringify(openedProducts));
            localStorage.setItem('zerosprechi_shopping', JSON.stringify(shoppingList));
        }

        function switchTab(tabId, btnElement) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            document.getElementById('tab-' + tabId).classList.add('active');

            document.querySelectorAll('.mobile-nav button').forEach(btn => {
                btn.classList.remove('text-emerald-600', 'font-bold');
                btn.classList.add('text-gray-500');
            });
            if(btnElement) {
                btnElement.classList.remove('text-gray-500');
                btnElement.classList.add('text-emerald-600', 'font-bold');
            }

            if(tabId === 'grafici') renderChart('giornaliero');
        }

        function renderPantry() {
            const container = document.getElementById('pantryList');
            document.getElementById('pantryCount').innerText = pantry.length;
            container.innerHTML = '';

            if(pantry.length === 0) {
                container.innerHTML = '<p class="text-sm text-gray-400 text-center py-6">La dispensa è vuota.</p>';
                return;
            }

            pantry.sort((a, b) => new Date(a.expiry) - new Date(b.expiry));

            pantry.forEach((item, index) => {
                container.innerHTML += `
                    <div class="flex justify-between items-center bg-gray-50 p-3.5 rounded-xl border border-gray-200 text-sm">
                        <div class="space-y-1">
                            <div class="font-bold text-gray-800 text-base">${item.name}</div>
                            <div class="text-gray-600">Quantità: <span class="font-semibold text-gray-800">${item.qty}</span></div>
                            <div class="text-xs text-orange-600 font-bold">Scadenza: ${formatItalianDate(item.expiry)}</div>
                        </div>
                        <button onclick="deletePantryItem(${index})" class="text-red-500 hover:text-red-700 p-2 text-lg">
                            <i class="fa-solid fa-trash-can"></i>
                        </button>
                    </div>
                `;
            });

            saveState();
            renderOpenedProducts();
            renderRecipes();
            renderWeeklyMenu();
        }

        // RENDER PRODOTTI APERTI CON DURATA CALCOLATA
        function renderOpenedProducts() {
            const container = document.getElementById('openedProductsList');
            container.innerHTML = '';

            if(openedProducts.length === 0) {
                container.innerHTML = '<p class="text-xs text-amber-700 italic">Nessun prodotto aperto segnalato.</p>';
                return;
            }

            openedProducts.forEach((item, index) => {
                container.innerHTML += `
                    <div class="flex justify-between items-center bg-white p-3 rounded-xl border border-amber-200 text-xs shadow-sm">
                        <div>
                            <span class="font-bold text-amber-900 text-sm">${item.name}</span>
                            <div class="text-gray-500">Aperto il: ${formatItalianDate(item.openDate)}</div>
                            <div class="text-red-600 font-bold mt-0.5">⚠️ Da consumare entro: ${formatItalianDate(item.consumeBy)}</div>
                        </div>
                        <button onclick="deleteOpenedProduct(${index})" class="text-amber-700 hover:text-red-600 p-2 font-bold text-base">
                            <i class="fa-solid fa-check"></i>
                        </button>
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

            // Calcolo automatico durata da aperto (3 giorni standard per freschi)
            let daysToConsume = 3;
            const lower = name.toLowerCase();
            if(lower.includes('latte')) daysToConsume = 4;
            if(lower.includes('passata') || lower.includes('pomodoro')) daysToConsume = 3;
            if(lower.includes('pesto')) daysToConsume = 5;
            if(lower.includes('yogurt')) daysToConsume = 6;

            const consumeBy = addDaysToDate(openDate, daysToConsume);
            openedProducts.push({ id: Date.now(), name, openDate, consumeBy });

            closeOpenProductModal();
            renderPantry();
            showNotification(`Prodotto aperto registrato! Consumalo entro ${daysToConsume} giorni.`);
        }

        function deleteOpenedProduct(index) {
            openedProducts.splice(index, 1);
            renderPantry();
        }

        // --- WIZARD VOCALE SEQUENZIALE (NOME -> QUANTITÀ -> SCADENZA) ---
        function startVoiceWizard() {
            wizardCurrentStep = 1;
            wizardTempProduct = { name: '', qty: '', expiry: '' };
            updateWizardStepUI();
            document.getElementById('voiceWizardModal').classList.remove('hidden');
            setTimeout(() => listenCurrentStep(), 400);
        }

        function updateWizardStepUI() {
            const badge = document.getElementById('stepBadge');
            const title = document.getElementById('stepTitle');
            const subtitle = document.getElementById('stepSubtitle');
            const stepVal = document.getElementById('stepValue');

            if (wizardCurrentStep === 1) {
                badge.innerText = "PASSO 1 DI 3";
                title.innerText = "Detti il NOME del prodotto";
                subtitle.innerText = "Es. 'Mozzarella', 'Pollo', 'Latte'...";
                stepVal.innerText = wizardTempProduct.name || "In attesa della voce...";
            } else if (wizardCurrentStep === 2) {
                badge.innerText = "PASSO 2 DI 3";
                title.innerText = "Detti la QUANTITÀ";
                subtitle.innerText = "Es. '200 grammi', '2 pezzi', '1 litro'...";
                stepVal.innerText = wizardTempProduct.qty || "In attesa della voce...";
            } else if (wizardCurrentStep === 3) {
                badge.innerText = "PASSO 3 DI 3";
                title.innerText = "Detti la DATA DI SCADENZA";
                subtitle.innerText = "Es. '14 novembre', '20 dicembre'...";
                stepVal.innerText = wizardTempProduct.expiry ? formatItalianDate(wizardTempProduct.expiry) : "In attesa della voce...";
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
            if (wizardCurrentStep === 1) {
                // Passo 1: Nome
                let cleanName = text.trim();
                cleanName = cleanName.charAt(0).toUpperCase() + cleanName.slice(1);
                wizardTempProduct.name = cleanName;
                document.getElementById('stepValue').innerText = cleanName;

                // Passa subito al passo 2!
                setTimeout(() => {
                    wizardCurrentStep = 2;
                    updateWizardStepUI();
                    listenCurrentStep();
                }, 600);

            } else if (wizardCurrentStep === 2) {
                // Passo 2: Quantità
                wizardTempProduct.qty = text.trim();
                document.getElementById('stepValue').innerText = text.trim();

                // Passa subito al passo 3!
                setTimeout(() => {
                    wizardCurrentStep = 3;
                    updateWizardStepUI();
                    listenCurrentStep();
                }, 600);

            } else if (wizardCurrentStep === 3) {
                // Passo 3: Scadenza con Parse Preciso del Giorno Pronunciato
                const parsedDate = parseExactItalianDate(text);
                wizardTempProduct.expiry = parsedDate;
                document.getElementById('stepValue').innerText = formatItalianDate(parsedDate);

                // Completato! Salva direttamente
                setTimeout(() => {
                    finishVoiceWizard();
                }, 600);
            }
        }

        // PARSER DATA ITALIANA CORRETTA (Es. "14 novembre" -> YYYY-11-14)
        function parseExactItalianDate(text) {
            const lower = text.toLowerCase();
            const months = { 'gennaio':'01', 'febbraio':'02', 'marzo':'03', 'aprile':'04', 'maggio':'05', 'giugno':'06', 'luglio':'07', 'agosto':'08', 'settembre':'09', 'ottobre':'10', 'novembre':'11', 'dicembre':'12' };

            // Trova numero del giorno esatto
            const dayMatch = lower.match(/(\d{1,2})/);
            let day = dayMatch ? dayMatch[1].padStart(2, '0') : '15';

            let month = '11'; // Default
            for (let mName in months) {
                if (lower.includes(mName)) {
                    month = months[mName];
                    break;
                }
            }

            // Anno esplicito o 2026
            const yearMatch = lower.match(/\b(202\d)\b/);
            const year = yearMatch ? yearMatch[1] : '2026';

            return `${year}-${month}-${day}`;
        }

        function skipWizardStep() {
            if (wizardCurrentStep < 3) {
                wizardCurrentStep++;
                updateWizardStepUI();
            } else {
                finishVoiceWizard();
            }
        }

        function cancelVoiceWizard() {
            document.getElementById('voiceWizardModal').classList.add('hidden');
        }

        function finishVoiceWizard() {
            if(!wizardTempProduct.name) wizardTempProduct.name = "Alimento Senza Nome";
            if(!wizardTempProduct.qty) wizardTempProduct.qty = "1 pz";
            if(!wizardTempProduct.expiry) wizardTempProduct.expiry = getFutureDate(7);

            pantry.push({
                id: Date.now(),
                name: wizardTempProduct.name,
                qty: wizardTempProduct.qty,
                expiry: wizardTempProduct.expiry
            });

            cancelVoiceWizard();
            renderPantry();
            showNotification(`✅ Prodotto "${wizardTempProduct.name}" inserito correttamente in dispensa!`);
        }

        // SOSTITUZIONE VOCALE PROGRAMMA CUCINA
        function startMealSwapVoice() {
            const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            if(!SpeechRecognition) return;

            const rec = new SpeechRecognition();
            rec.lang = 'it-IT';
            rec.start();

            rec.onstart = function() {
                showNotification("🎙️ Detti cosa stai cucinando oggi...");
            };

            rec.onresult = function(e) {
                const text = e.results[0][0].transcript;
                // Sostituisce il pranzo di oggi nel menu
                weeklyMenuData[0].lunch = text.charAt(0).toUpperCase() + text.slice(1);
                renderWeeklyMenu();
                showNotification(`✨ Programma aggiornato! Oggi cucini: ${text}`);
            };
        }

        function showNotification(msg) {
            const el = document.getElementById('successNotification');
            document.getElementById('successText').innerText = msg;
            el.classList.remove('hidden');
            setTimeout(() => el.classList.add('hidden'), 3500);
        }

        function addPantryItemManual(e) {
            e.preventDefault();
            const name = document.getElementById('itemName').value;
            const qty = document.getElementById('itemQty').value;
            const expiry = document.getElementById('itemExpiry').value;
            pantry.push({ id: Date.now(), name, qty, expiry });
            document.getElementById('addItemForm').reset();
            renderPantry();
            showNotification("Prodotto inserito correttamente!");
        }

        function deletePantryItem(index) {
            pantry.splice(index, 1);
            renderPantry();
        }

        function clearPantry() {
            if(confirm("Vuoi svuotare la dispensa?")) {
                pantry = [];
                renderPantry();
            }
        }

        // UTILITIES DATE
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
            const container = document.getElementById('recipesContainer');
            container.innerHTML = `
                <div class="border border-emerald-200 bg-emerald-50/50 p-4 rounded-xl space-y-2">
                    <h3 class="font-bold text-emerald-900 text-base flex items-center gap-2">
                        <i class="fa-solid fa-utensils text-emerald-600"></i> Bocconcini di Pollo e Zucchine
                    </h3>
                    <div class="text-xs space-y-1.5 bg-white p-3 rounded-lg border border-emerald-100">
                        <p><b>🧑 Martino (Sleeve):</b> 120g pollo + 80g zucchine (Morbido, alta densità proteica).</p>
                        <p><b>👩 Valentina (Dieta):</b> 180g pollo + 200g zucchine (Ipocalorico e saziante).</p>
                        <p><b>👶 Camilla (2.5 anni):</b> 40g pollo sminuzzato fine + zucchine cotte morbide.</p>
                    </div>
                </div>
            `;
        }

        function renderWeeklyMenu() {
            const container = document.getElementById('weeklyMenuContainer');
            container.innerHTML = '';

            // Se ci sono prodotti aperti, prioritizzali in cima
            let openedNote = "";
            if(openedProducts.length > 0) {
                openedNote = `<div class="bg-amber-100 text-amber-900 p-2.5 rounded-xl text-xs font-bold mb-2">⚡ Priorità consumare prodotti aperti: ${openedProducts.map(p=>p.name).join(', ')}</div>`;
            }

            container.innerHTML = openedNote;

            weeklyMenuData.forEach((item) => {
                container.innerHTML += `
                    <div class="bg-gray-50 p-3 rounded-xl border border-gray-200 text-xs flex justify-between items-center">
                        <span class="font-bold text-emerald-700 w-16">${item.day}</span>
                        <span class="text-gray-800 flex-1 ml-2">Pranzo: <b>${item.lunch}</b> | Cena: <b>${item.dinner}</b></span>
                    </div>
                `;
            });
        }

        function generateWeeklyMenu() {
            renderWeeklyMenu();
            showNotification("Menu settimanale rigenerato su misura!");
        }

        function renderShopping() {
            const container = document.getElementById('shoppingListContainer');
            container.innerHTML = '';
            shoppingList.forEach((item, index) => {
                container.innerHTML += `
                    <div class="flex justify-between items-center bg-gray-50 p-3 rounded-xl border border-gray-200 text-sm">
                        <label class="flex items-center gap-2 cursor-pointer">
                            <input type="checkbox" ${item.checked ? 'checked' : ''} onclick="toggleShopping(${index})" class="rounded text-emerald-600">
                            <span class="${item.checked ? 'line-through text-gray-400' : 'text-gray-800 font-bold'}">${item.name}</span>
                        </label>
                        <button onclick="deleteShopping(${index})" class="text-red-400 p-1"><i class="fa-solid fa-trash-can"></i></button>
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

        function toggleShopping(index) { shoppingList[index].checked = !shoppingList[index].checked; renderShopping(); }
        function deleteShopping(index) { shoppingList.splice(index, 1); renderShopping(); }

        function renderOffers() {
            const container = document.getElementById('esselungaOffers');
            container.innerHTML = '';
            offers.forEach(off => {
                container.innerHTML += `
                    <div class="border border-red-100 bg-red-50/40 p-3.5 rounded-xl flex justify-between items-center">
                        <div>
                            <span class="bg-red-100 text-red-700 text-xs font-bold px-2 py-0.5 rounded-full">${off.discount}</span>
                            <h4 class="font-bold text-gray-800 text-sm mt-1">${off.name}</h4>
                            <p class="text-xs font-semibold text-red-600">${off.price}</p>
                            <p class="text-xs text-gray-500 mt-0.5">${off.tag}</p>
                        </div>
                        <button onclick="shoppingList.push({id: Date.now(), name: '${off.name}', checked: false}); renderShopping(); showNotification('Aggiunto alla lista spesa!')" class="bg-red-600 text-white text-xs px-3 py-2 rounded-xl font-bold">
                            + Spesa
                        </button>
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
                data: {
                    labels: labels,
                    datasets: [{ label: 'Spesa (€)', data: data, backgroundColor: '#10b981', borderRadius: 6 }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } } }
            });
        }

        function updateChartPeriod(period, btn) {
            document.querySelectorAll('.chart-btn').forEach(b => { b.classList.remove('bg-white', 'shadow-sm'); b.classList.add('text-gray-600'); });
            btn.classList.add('bg-white', 'shadow-sm');
            btn.classList.remove('text-gray-600');
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
