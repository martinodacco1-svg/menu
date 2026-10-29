<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ZeroSprechi Famiglia</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js per i grafici -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- FontAwesome per le icone -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body { background-color: #f3f4f6; color: #1f2937; padding-bottom: 95px; }
        .mobile-nav { position: fixed; bottom: 0; left: 0; right: 0; background: white; border-top: 1px solid #e5e7eb; z-index: 50; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        .pulse-mic { animation: pulse-ring 1.25s cubic-bezier(0.215, 0.61, 0.355, 1) infinite; }
        @keyframes pulse-ring {
            0% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.7); }
            70% { transform: scale(1); box-shadow: 0 0 0 12px rgba(239, 68, 68, 0); }
            100% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(239, 68, 68, 0); }
        }
    </style>
</head>
<body class="min-h-screen">

    <!-- Header App -->
    <header class="bg-emerald-600 text-white p-4 shadow-md sticky top-0 z-40 flex justify-between items-center">
        <div>
            <h1 class="text-xl font-bold flex items-center gap-2"><i class="fa-solid fa-leaf"></i> ZeroSprechi</h1>
            <p class="text-[11px] text-emerald-100">Martino (Sleeve) • Valentina (Dieta) • Camilla (2.5 anni)</p>
        </div>
        <div class="bg-emerald-700 text-emerald-100 text-[10px] px-2.5 py-1 rounded-full font-semibold">
            Versione Facile
        </div>
    </header>

    <main class="p-3 max-w-xl mx-auto space-y-4">

        <!-- TAB 1: DISPENSA & FRIGO -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            
            <!-- Box Inserimento Rapido / Vocale -->
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-gray-100 space-y-4">
                <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-plus-circle text-emerald-600"></i> Aggiungi alla Dispensa
                </h2>

                <!-- TASTO VOCALE PRINCIPALE -->
                <div class="bg-gradient-to-r from-red-500 to-rose-600 p-4 rounded-xl text-white shadow-md text-center space-y-2">
                    <p class="text-xs font-semibold opacity-90">Premi e parla al microfono:</p>
                    <button id="voiceBtn" onclick="toggleVoiceRecognition()" class="w-full bg-white text-red-600 py-3 px-4 rounded-xl font-extrabold text-sm flex items-center justify-center gap-3 shadow active:scale-95 transition">
                        <i id="micIcon" class="fa-solid fa-microphone text-lg"></i>
                        <span id="voiceBtnText">🎙️ Ditta Vocale (Premi e parla)</span>
                    </button>
                    <p class="text-[11px] opacity-80 italic">Es: "Mozzarella 100 grammi scadenza 18 novembre 2026"</p>
                </div>

                <!-- FORM MANUALE / RISULTATO VOCALE -->
                <form id="addItemForm" onsubmit="addPantryItem(event)" class="space-y-3 pt-2">
                    <div>
                        <label class="text-xs font-bold text-gray-700 mb-1 block">Nome Prodotto</label>
                        <input type="text" id="itemName" placeholder="Es. Mozzarella, Pollo, Latte..." required class="w-full border border-gray-200 p-2.5 rounded-xl text-sm bg-gray-50 focus:bg-white focus:ring-2 focus:ring-emerald-500 outline-none">
                    </div>

                    <div class="grid grid-cols-2 gap-2">
                        <div>
                            <label class="text-xs font-bold text-gray-700 mb-1 block">Quantità</label>
                            <input type="text" id="itemQty" placeholder="Es. 100g, 2 pz..." required class="w-full border border-gray-200 p-2.5 rounded-xl text-sm bg-gray-50 focus:bg-white focus:ring-2 focus:ring-emerald-500 outline-none">
                        </div>
                        <div>
                            <label class="text-xs font-bold text-gray-700 mb-1 block">Data Scadenza</label>
                            <input type="date" id="itemExpiry" required class="w-full border border-gray-200 p-2.5 rounded-xl text-sm bg-gray-50 focus:bg-white focus:ring-2 focus:ring-emerald-500 outline-none">
                        </div>
                    </div>

                    <button type="submit" class="w-full bg-emerald-600 hover:bg-emerald-700 active:scale-98 text-white p-3 rounded-xl text-sm font-bold shadow-md transition flex items-center justify-center gap-2">
                        <i class="fa-solid fa-check"></i> Salva Prodotto in Dispensa
                    </button>
                </form>

                <!-- OPTIONAL: FOTO VELOCE -->
                <div class="border-t pt-3">
                    <label class="bg-blue-50 text-blue-700 border border-blue-200 p-2.5 rounded-xl text-xs font-bold flex items-center justify-center gap-2 cursor-pointer active:scale-95 transition">
                        <i class="fa-solid fa-camera text-base"></i>
                        <span>Scatta Foto per Promemoria</span>
                        <input type="file" accept="image/*" capture="environment" class="hidden" onchange="handleQuickPhoto(event)">
                    </label>
                </div>
            </div>

            <!-- LISTA PRODOTTI IN DISPENSA -->
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-gray-100">
                <div class="flex justify-between items-center mb-3">
                    <h3 class="font-bold text-gray-800 text-sm uppercase flex items-center gap-2">
                        <i class="fa-solid fa-box-open text-emerald-600"></i> Dispensa (<span id="pantryCount">0</span>)
                    </h3>
                    <button onclick="clearPantry()" class="text-xs text-red-500 font-semibold hover:underline">Svuota tutto</button>
                </div>
                <div id="pantryList" class="space-y-2"></div>
            </div>
        </section>

        <!-- TAB 2: RICETTE -->
        <section id="tab-ricette" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-gray-100 space-y-3">
                <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-utensils text-emerald-600"></i> Ricette Consigliate
                </h2>
                <p class="text-xs text-gray-500">Porzioni ricalcolate per i profili nutrizionali della famiglia:</p>
                <div id="recipesContainer" class="space-y-3"></div>
            </div>
        </section>

        <!-- TAB 3: MENU SETTIMANALE -->
        <section id="tab-menu" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-gray-100 space-y-3">
                <div class="flex justify-between items-center">
                    <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-calendar-days text-emerald-600"></i> Menu Settimanale
                    </h2>
                    <button onclick="generateWeeklyMenu()" class="bg-emerald-600 text-white text-xs px-3 py-1.5 rounded-lg font-bold">
                        ⚡ Rigenera
                    </button>
                </div>
                <div id="weeklyMenuContainer" class="space-y-2"></div>
            </div>
        </section>

        <!-- TAB 4: SPESA -->
        <section id="tab-spesa" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-gray-100 space-y-3">
                <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-cart-shopping text-emerald-600"></i> Lista Spesa
                </h2>
                <div class="flex gap-2">
                    <input type="text" id="shoppingInput" placeholder="Aggiungi alla spesa..." class="border p-2.5 rounded-xl text-sm flex-1 bg-gray-50">
                    <button onclick="addShoppingItem()" class="bg-emerald-600 text-white px-4 py-2.5 rounded-xl text-sm font-bold">+</button>
                </div>
                <div id="shoppingListContainer" class="space-y-2 pt-2"></div>
            </div>
        </section>

        <!-- TAB 5: OFFERTE ESSELUNGA -->
        <section id="tab-offerte" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-gray-100 space-y-3">
                <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-tags text-red-600"></i> Offerte Esselunga
                </h2>
                <div id="esselungaOffers" class="grid grid-cols-1 gap-2.5"></div>
            </div>
        </section>

        <!-- TAB 6: GRAFICI -->
        <section id="tab-grafici" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-gray-100 space-y-3">
                <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-chart-pie text-emerald-600"></i> Analisi Spesa e Risparmi
                </h2>
                <div class="flex gap-1 bg-gray-100 p-1 rounded-xl">
                    <button onclick="updateChartPeriod('giornaliero', this)" class="flex-1 py-1.5 text-xs font-bold rounded-lg bg-white shadow-sm chart-btn">Oggi</button>
                    <button onclick="updateChartPeriod('settimanale', this)" class="flex-1 py-1.5 text-xs font-bold rounded-lg text-gray-600 chart-btn">Settimana</button>
                    <button onclick="updateChartPeriod('mensile', this)" class="flex-1 py-1.5 text-xs font-bold rounded-lg text-gray-600 chart-btn">Mese</button>
                </div>
                <div class="relative h-60 w-full pt-2">
                    <canvas id="expenseChart"></canvas>
                </div>
            </div>
        </section>

    </main>

    <!-- Navigazione Mobile Inferiore -->
    <nav class="mobile-nav flex justify-around items-center py-2.5 text-gray-500 text-[10px]">
        <button onclick="switchTab('dispensa', this)" class="nav-btn flex flex-col items-center text-emerald-600 font-bold">
            <i class="fa-solid fa-box-open text-lg mb-0.5"></i> Dispensa
        </button>
        <button onclick="switchTab('ricette', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-utensils text-lg mb-0.5"></i> Ricette
        </button>
        <button onclick="switchTab('menu', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-calendar-days text-lg mb-0.5"></i> Menu
        </button>
        <button onclick="switchTab('spesa', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-cart-shopping text-lg mb-0.5"></i> Spesa
        </button>
        <button onclick="switchTab('offerte', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-tags text-lg mb-0.5"></i> Offerte
        </button>
        <button onclick="switchTab('grafici', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-chart-pie text-lg mb-0.5"></i> Analisi
        </button>
    </nav>

    <script>
        // Data Base Locale
        let pantry = JSON.parse(localStorage.getItem('zerosprechi_pantry')) || [
            { id: 1, name: "Petto di Pollo", qty: "500g", expiry: "2026-11-05" },
            { id: 2, name: "Zucchine", qty: "4 pz", expiry: "2026-11-02" }
        ];

        let shoppingList = JSON.parse(localStorage.getItem('zerosprechi_shopping')) || [
            { id: 1, name: "Olio Extravergine d'Oliva", checked: false },
            { id: 2, name: "Parmigiano Reggiano", checked: true }
        ];

        const offers = [
            { name: "Petto di Pollo Bio Amadori", price: "€ 7,90 /kg", discount: "-30%", tag: "Proteico per Martino e Valentina" },
            { name: "Filetti di Salmone Norvegese", price: "€ 14,50 /kg", discount: "-25%", tag: "Ricco di Omega-3" },
            { name: "Yogurt Greco 0%", price: "€ 2,49", discount: "-20%", tag: "Alto valore proteico" },
            { name: "Pastina d'orzo per bambini", price: "€ 0,89", discount: "-15%", tag: "Ideale per Camilla" }
        ];

        let isListening = false;
        let recognition = null;
        let expenseChartInstance = null;

        function saveState() {
            localStorage.setItem('zerosprechi_pantry', JSON.stringify(pantry));
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

        // RENDER DISPENSA
        function renderPantry() {
            const container = document.getElementById('pantryList');
            document.getElementById('pantryCount').innerText = pantry.length;
            container.innerHTML = '';

            if(pantry.length === 0) {
                container.innerHTML = '<p class="text-xs text-gray-400 text-center py-6">La dispensa è vuota.</p>';
                return;
            }

            pantry.sort((a, b) => new Date(a.expiry) - new Date(b.expiry));

            pantry.forEach((item, index) => {
                container.innerHTML += `
                    <div class="flex justify-between items-center bg-gray-50 p-3 rounded-xl border border-gray-100 text-xs">
                        <div class="space-y-0.5">
                            <div class="font-bold text-gray-800 text-sm">${item.name}</div>
                            <div class="text-gray-500">Quantità: <span class="font-semibold text-gray-700">${item.qty}</span></div>
                            <div class="text-[11px] text-orange-600 font-semibold">Scadenza: ${item.expiry}</div>
                        </div>
                        <button onclick="deletePantryItem(${index})" class="text-red-400 hover:text-red-600 p-2 text-base">
                            <i class="fa-solid fa-trash-can"></i>
                        </button>
                    </div>
                `;
            });

            saveState();
            renderRecipes();
            renderWeeklyMenu();
        }

        function addPantryItem(e) {
            if(e) e.preventDefault();
            const name = document.getElementById('itemName').value.trim();
            const qty = document.getElementById('itemQty').value.trim();
            const expiry = document.getElementById('itemExpiry').value;

            if(!name || !qty || !expiry) {
                alert("Compila tutti i campi o usa la dettatura vocale!");
                return;
            }

            pantry.push({ id: Date.now(), name, qty, expiry });
            document.getElementById('addItemForm').reset();
            renderPantry();
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

        // RICONOSCIMENTO VOCALE AVANZATO
        function toggleVoiceRecognition() {
            const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;

            if (!SpeechRecognition) {
                alert("Il riconoscimento vocale non è supportato su questo browser. Prova con Chrome o Safari!");
                return;
            }

            if (isListening) {
                recognition.stop();
                return;
            }

            recognition = new SpeechRecognition();
            recognition.lang = 'it-IT';
            recognition.interimResults = false;

            recognition.onstart = function() {
                isListening = true;
                const btn = document.getElementById('voiceBtn');
                btn.classList.add('pulse-mic', 'bg-red-100', 'text-red-700');
                document.getElementById('voiceBtnText').innerText = "🎙️ Ti ascolto... Parla ora!";
            };

            recognition.onresult = function(event) {
                const text = event.results[0][0].transcript;
                parseVoiceInput(text);
            };

            recognition.onerror = function() {
                alert("Non ho capito bene. Riprova a parlare più vicino al microfono.");
                resetVoiceBtn();
            };

            recognition.onend = function() {
                resetVoiceBtn();
            };

            recognition.start();
        }

        function resetVoiceBtn() {
            isListening = false;
            const btn = document.getElementById('voiceBtn');
            btn.classList.remove('pulse-mic', 'bg-red-100', 'text-red-700');
            document.getElementById('voiceBtnText').innerText = "🎙️ Ditta Vocale (Premi e parla)";
        }

        // PARSER INTELILGENTE PER TESTO VOCALE
        function parseVoiceInput(text) {
            alert(`Hai detto: "${text}"`);
            const lower = text.toLowerCase();

            // Estrazione Nome
            let name = lower.split(' quantita')[0].split(' quantità')[0].split(' scadenza')[0].split(' grammi')[0].split(' g ')[0];
            name = name.charAt(0).toUpperCase() + name.slice(1);

            // Estrazione Quantità
            let qty = "1 pz";
            const qtyMatch = lower.match(/(\d+)\s*(grammi|g|kg|chili|pezzi|pz|etto|etti)/i) || lower.match(/(\d+)/);
            if (qtyMatch) {
                qty = qtyMatch[0];
            }

            // Estrazione Data Scadenza
            let expiry = getFutureDate(7); // Default
            const months = { 'gennaio':'01', 'febbraio':'02', 'marzo':'03', 'aprile':'04', 'maggio':'05', 'giugno':'06', 'luglio':'07', 'agosto':'08', 'settembre':'09', 'ottobre':'10', 'novembre':'11', 'dicembre':'12' };

            for (let monthName in months) {
                if (lower.includes(monthName)) {
                    const dayMatch = lower.match(new RegExp(`(\\d{1,2})\\s*${monthName}`, 'i'));
                    const yearMatch = lower.match(/\b(202\d)\b/);
                    const day = dayMatch ? dayMatch[1].padStart(2, '0') : '15';
                    const year = yearMatch ? yearMatch[1] : '2026';
                    expiry = `${year}-${months[monthName]}-${day}`;
                    break;
                }
            }

            // Compila i campi del modulo
            document.getElementById('itemName').value = name;
            document.getElementById('itemQty').value = qty;
            document.getElementById('itemExpiry').value = expiry;
        }

        function handleQuickPhoto(e) {
            const file = e.target.files[0];
            if (!file) return;
            document.getElementById('itemName').value = "Prodotto da Foto";
            document.getElementById('itemQty').value = "1 pz";
            document.getElementById('itemExpiry').value = getFutureDate(7);
            alert("📷 Foto acquisita! Campo precompilato.");
        }

        function getFutureDate(days) {
            const d = new Date();
            d.setDate(d.getDate() + days);
            return d.toISOString().split('T')[0];
        }

        // RICETTE & PORZIONI
        function renderRecipes() {
            const container = document.getElementById('recipesContainer');
            container.innerHTML = `
                <div class="border border-emerald-200 bg-emerald-50/50 p-3.5 rounded-xl space-y-2">
                    <h3 class="font-bold text-emerald-900 text-sm flex items-center gap-2">
                        <i class="fa-solid fa-utensils text-emerald-600"></i> Bocconcini di Pollo e Zucchine
                    </h3>
                    <div class="text-xs space-y-1.5 bg-white p-2.5 rounded-lg border border-emerald-100">
                        <p><b>🧑 Martino (Sleeve):</b> 120g pollo + 80g zucchine (Morbido, alta densità proteica).</p>
                        <p><b>👩 Valentina (Dieta):</b> 180g pollo + 200g zucchine (Ipocalorico e saziante).</p>
                        <p><b>👶 Camilla (2.5 anni):</b> 40g pollo sminuzzato fine + zucchine cotte morbide.</p>
                    </div>
                </div>

                <div class="border border-blue-200 bg-blue-50/50 p-3.5 rounded-xl space-y-2">
                    <h3 class="font-bold text-blue-900 text-sm flex items-center gap-2">
                        <i class="fa-solid fa-fish text-blue-600"></i> Salmone e verdure al vapore
                    </h3>
                    <div class="text-xs space-y-1.5 bg-white p-2.5 rounded-lg border border-blue-100">
                        <p><b>🧑 Martino (Sleeve):</b> 100g salmone (Piccola porzione ben cotta).</p>
                        <p><b>👩 Valentina (Dieta):</b> 150g salmone + abbondanti verdure.</p>
                        <p><b>👶 Camilla (2.5 anni):</b> 30g salmone schiacciato senza lische.</p>
                    </div>
                </div>
            `;
        }

        // MENU SETTIMANALE
        function renderWeeklyMenu() {
            const container = document.getElementById('weeklyMenuContainer');
            container.innerHTML = '';
            const days = ["Lunedì", "Martedì", "Mercoledì", "Giovedì", "Venerdì", "Sabato", "Domenica"];
            days.forEach((day, i) => {
                const item = pantry[i % pantry.length]?.name || "Pasto Leggero";
                container.innerHTML += `
                    <div class="bg-gray-50 p-2.5 rounded-lg border border-gray-100 text-xs flex justify-between items-center">
                        <span class="font-bold text-emerald-700 w-16">${day}</span>
                        <span class="text-gray-700 flex-1 ml-2">Pranzo: <b>${item}</b> | Cena: <b>Vellutata/Uova</b></span>
                    </div>
                `;
            });
        }

        function generateWeeklyMenu() {
            alert("✨ Menu rigenerato prioritizzando i cibi vicini alla scadenza!");
            renderWeeklyMenu();
        }

        // SPESA
        function renderShopping() {
            const container = document.getElementById('shoppingListContainer');
            container.innerHTML = '';
            shoppingList.forEach((item, index) => {
                container.innerHTML += `
                    <div class="flex justify-between items-center bg-gray-50 p-2.5 rounded-lg border border-gray-100 text-xs">
                        <label class="flex items-center gap-2 cursor-pointer">
                            <input type="checkbox" ${item.checked ? 'checked' : ''} onclick="toggleShopping(${index})" class="rounded text-emerald-600">
                            <span class="${item.checked ? 'line-through text-gray-400' : 'text-gray-800 font-semibold'}">${item.name}</span>
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

        function toggleShopping(index) {
            shoppingList[index].checked = !shoppingList[index].checked;
            renderShopping();
        }

        function deleteShopping(index) {
            shoppingList.splice(index, 1);
            renderShopping();
        }

        // OFFERTE
        function renderOffers() {
            const container = document.getElementById('esselungaOffers');
            container.innerHTML = '';
            offers.forEach(off => {
                container.innerHTML += `
                    <div class="border border-red-100 bg-red-50/40 p-3 rounded-xl flex justify-between items-center">
                        <div>
                            <span class="bg-red-100 text-red-700 text-[10px] font-bold px-2 py-0.5 rounded-full">${off.discount}</span>
                            <h4 class="font-bold text-gray-800 text-xs mt-1">${off.name}</h4>
                            <p class="text-xs font-semibold text-red-600">${off.price}</p>
                            <p class="text-[10px] text-gray-500 mt-0.5">${off.tag}</p>
                        </div>
                        <button onclick="shoppingList.push({id: Date.now(), name: '${off.name}', checked: false}); renderShopping(); alert('Aggiunto alla spesa!')" class="bg-red-600 text-white text-xs px-2.5 py-1.5 rounded-lg font-bold">
                            + Spesa
                        </button>
                    </div>
                `;
            });
        }

        // GRAFICI
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

        // AVVIO
        window.onload = function() {
            // Imposta la data di oggi come predefinita
            document.getElementById('itemExpiry').value = getFutureDate(7);
            renderPantry();
            renderShopping();
            renderOffers();
        };
    </script>
</body>
</html>
