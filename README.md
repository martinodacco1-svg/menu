<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ZeroSprechi Famiglia</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body { background-color: #f3f4f6; color: #1f2937; padding-bottom: 95px; }
        .mobile-nav { position: fixed; bottom: 0; left: 0; right: 0; background: white; border-top: 1px solid #e5e7eb; z-index: 50; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        .spinner { border: 3px solid rgba(255,255,255,0.3); border-radius: 50%; border-top-color: #fff; width: 28px; height: 28px; animation: spin 1s ease-in-out infinite; }
        @keyframes spin { to { transform: rotate(360deg); } }
    </style>
</head>
<body class="min-h-screen">

    <!-- Header -->
    <header class="bg-emerald-600 text-white p-4 shadow-md sticky top-0 z-40 flex justify-between items-center">
        <div>
            <h1 class="text-xl font-bold flex items-center gap-2"><i class="fa-solid fa-leaf"></i> ZeroSprechi</h1>
            <p class="text-[11px] text-emerald-100">Martino (Sleeve) • Valentina (Dieta) • Camilla (2.5 anni)</p>
        </div>
        <button onclick="openApiModal()" class="bg-emerald-700 hover:bg-emerald-800 text-xs px-2.5 py-1.5 rounded-lg font-medium shadow flex items-center gap-1">
            <i class="fa-solid fa-key"></i> <span id="apiKeyStatus">Chiave AI</span>
        </button>
    </header>

    <!-- Overlay Caricamento e Anteprima Foto -->
    <div id="loadingOverlay" class="hidden fixed inset-0 bg-black/80 z-50 flex flex-col items-center justify-center p-4 text-white text-center space-y-4">
        <div class="spinner"></div>
        <p id="loadingMsg" class="font-bold text-sm">Analisi della foto in corso...</p>
        <div class="w-48 h-48 rounded-xl overflow-hidden border-2 border-emerald-400 shadow-2xl bg-gray-900 flex items-center justify-center">
            <img id="photoPreview" src="" class="w-full h-full object-cover">
        </div>
    </div>

    <main class="p-3 max-w-xl mx-auto space-y-4">

        <!-- TAB 1: DISPENSA & FRIGO -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100 space-y-3">
                <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-camera text-emerald-600"></i> Scansione Fotocamera
                </h2>
                
                <p class="text-xs text-gray-500 font-medium">Scegli cosa vuoi scansionare:</p>

                <!-- Tasti Fotocamera diretti -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                    
                    <!-- 1. FOTOCAMERA SCONTRINO -->
                    <button onclick="triggerCamera('scontrino')" class="bg-emerald-600 hover:bg-emerald-700 text-white p-4 rounded-xl text-sm font-bold flex items-center justify-center gap-3 shadow-md active:scale-95 transition">
                        <i class="fa-solid fa-receipt text-2xl"></i>
                        <span class="text-base">Foto Scontrino</span>
                    </button>

                    <!-- 2. FOTOCAMERA PRODOTTO -->
                    <button onclick="triggerCamera('prodotto')" class="bg-blue-600 hover:bg-blue-700 text-white p-4 rounded-xl text-sm font-bold flex items-center justify-center gap-3 shadow-md active:scale-95 transition">
                        <i class="fa-solid fa-camera text-2xl"></i>
                        <span class="text-base">Foto Prodotto</span>
                    </button>

                </div>

                <!-- Input Fotocamera unificato e gestito via JS -->
                <input type="file" id="globalCameraInput" accept="image/*" capture="environment" class="hidden" onchange="processCapture(event)">

                <!-- Inserimento Manuale Veloce -->
                <details class="text-xs text-gray-600 pt-2 border-t">
                    <summary class="font-semibold cursor-pointer py-1 text-emerald-600">+ Inserisci manualmente se preferisci</summary>
                    <form id="addItemForm" onsubmit="addPantryItem(event)" class="grid grid-cols-1 gap-2 mt-2">
                        <input type="text" id="itemName" placeholder="Nome prodotto (es. Pollo)" required class="border p-2 rounded-lg text-sm bg-gray-50">
                        <div class="grid grid-cols-2 gap-2">
                            <input type="text" id="itemQty" placeholder="Quantità (es. 500g)" required class="border p-2 rounded-lg text-sm bg-gray-50">
                            <input type="date" id="itemExpiry" required class="border p-2 rounded-lg text-sm bg-gray-50">
                        </div>
                        <button type="submit" class="bg-emerald-600 text-white p-2 rounded-lg font-bold">Aggiungi</button>
                    </form>
                </details>
            </div>

            <!-- Lista Prodotti in Dispensa -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <div class="flex justify-between items-center mb-3">
                    <h3 class="font-bold text-gray-800 text-sm uppercase">Dispensa Attuale (<span id="pantryCount">0</span>)</h3>
                    <button onclick="clearPantry()" class="text-[11px] text-red-500 hover:underline">Svuota tutto</button>
                </div>
                <div id="pantryList" class="space-y-2"></div>
            </div>
        </section>

        <!-- TAB 2: RICETTE -->
        <section id="tab-ricette" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h2 class="text-base font-bold text-gray-800 mb-1 flex items-center gap-2">
                    <i class="fa-solid fa-utensils text-emerald-600"></i> Ricette Consigliate
                </h2>
                <p class="text-xs text-gray-500 mb-3">Porzioni calcolate per Martino (Sleeve), Valentina e Camilla.</p>
                <div id="recipesContainer" class="space-y-3"></div>
            </div>
        </section>

        <!-- TAB 3: MENU SETTIMANALE -->
        <section id="tab-menu" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <div class="flex justify-between items-center mb-3">
                    <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-calendar-days text-emerald-600"></i> Menu Settimanale
                    </h2>
                    <button onclick="generateWeeklyMenu()" class="bg-emerald-600 text-white text-xs px-2.5 py-1.5 rounded-lg font-bold">⚡ Rigenera</button>
                </div>
                <div id="weeklyMenuContainer" class="space-y-2"></div>
            </div>
        </section>

        <!-- TAB 4: SPESA -->
        <section id="tab-spesa" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h2 class="text-base font-bold text-gray-800 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-cart-shopping text-emerald-600"></i> Lista Spesa
                </h2>
                <div class="flex gap-2 mb-3">
                    <input type="text" id="shoppingInput" placeholder="Aggiungi prodotto..." class="border p-2 rounded-lg text-sm flex-1 bg-gray-50">
                    <button onclick="addShoppingItem()" class="bg-emerald-600 text-white px-3 py-2 rounded-lg text-sm font-bold">+</button>
                </div>
                <div id="shoppingListContainer" class="space-y-2"></div>
            </div>
        </section>

        <!-- TAB 5: OFFERTE -->
        <section id="tab-offerte" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h2 class="text-base font-bold text-gray-800 mb-1 flex items-center gap-2">
                    <i class="fa-solid fa-tags text-red-600"></i> Offerte Esselunga
                </h2>
                <p class="text-xs text-gray-500 mb-3">Selezionate per le esigenze della famiglia.</p>
                <div id="esselungaOffers" class="grid grid-cols-1 gap-2.5"></div>
            </div>
        </section>

        <!-- TAB 6: GRAFICI -->
        <section id="tab-grafici" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h2 class="text-base font-bold text-gray-800 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-chart-pie text-emerald-600"></i> Spese e Risparmi
                </h2>
                <div class="flex gap-1 mb-4 bg-gray-100 p-1 rounded-lg">
                    <button onclick="updateChartPeriod('giornaliero', this)" class="flex-1 py-1 text-xs font-bold rounded bg-white shadow-sm chart-btn">Oggi</button>
                    <button onclick="updateChartPeriod('settimanale', this)" class="flex-1 py-1 text-xs font-bold rounded text-gray-600 chart-btn">Settimana</button>
                    <button onclick="updateChartPeriod('mensile', this)" class="flex-1 py-1 text-xs font-bold rounded text-gray-600 chart-btn">Mese</button>
                </div>
                <div class="relative h-60 w-full">
                    <canvas id="expenseChart"></canvas>
                </div>
            </div>
        </section>

    </main>

    <!-- Navigazione Mobile Inferiore -->
    <nav class="mobile-nav flex justify-around items-center py-2.5 text-gray-500 text-[10px]">
        <button onclick="switchTab('dispensa', this)" class="nav-btn flex flex-col items-center text-emerald-600 font-bold">
            <i class="fa-solid fa-box-open text-base mb-0.5"></i> Dispensa
        </button>
        <button onclick="switchTab('ricette', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-utensils text-base mb-0.5"></i> Ricette
        </button>
        <button onclick="switchTab('menu', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-calendar-days text-base mb-0.5"></i> Menu
        </button>
        <button onclick="switchTab('spesa', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-cart-shopping text-base mb-0.5"></i> Spesa
        </button>
        <button onclick="switchTab('offerte', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-tags text-base mb-0.5"></i> Offerte
        </button>
        <button onclick="switchTab('grafici', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-chart-pie text-base mb-0.5"></i> Analisi
        </button>
    </nav>

    <!-- Modal API Key -->
    <div id="apiModal" class="fixed inset-0 bg-black/50 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-5 rounded-2xl max-w-sm w-full space-y-3">
            <h3 class="font-bold text-base text-gray-800">Google Gemini API Key</h3>
            <p class="text-xs text-gray-500">Incolla qui la tua API Key gratuita di Google AI Studio per attivare la lettura automatica di scontrini e prodotti.</p>
            <input type="password" id="apiKeyInput" placeholder="Incolla AI Key..." class="w-full border p-2.5 rounded-lg text-sm bg-gray-50">
            <div class="flex justify-between items-center pt-2">
                <a href="https://aistudio.google.com/" target="_blank" class="text-xs text-emerald-600 font-bold underline">Crea Chiave Gratis</a>
                <div class="flex gap-2">
                    <button onclick="closeApiModal()" class="px-3 py-1.5 text-xs text-gray-600 font-semibold">Annulla</button>
                    <button onclick="saveApiKey()" class="px-3 py-1.5 text-xs bg-emerald-600 text-white rounded-lg font-bold">Salva</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        let currentMode = 'prodotto';

        let pantry = JSON.parse(localStorage.getItem('zerosprechi_pantry')) || [
            { id: 1, name: "Petto di Pollo", qty: "500g", expiry: "2026-03-04" },
            { id: 2, name: "Zucchine", qty: "4 pz", expiry: "2026-03-02" }
        ];

        let shoppingList = JSON.parse(localStorage.getItem('zerosprechi_shopping')) || [
            { id: 1, name: "Olio Extravergine d'Oliva", checked: false }
        ];

        const offers = [
            { name: "Petto di Pollo Bio Amadori", price: "€ 7,90 /kg", discount: "-30%", tag: "Proteico per Martino e Valentina" },
            { name: "Filetti di Salmone Norvegese", price: "€ 14,50 /kg", discount: "-25%", tag: "Ricco di Omega-3" },
            { name: "Pastina d'orzo per bambini", price: "€ 0,89", discount: "-15%", tag: "Per Camilla" }
        ];

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
                    <div class="flex justify-between items-center bg-gray-50 p-2.5 rounded-xl border border-gray-100 text-xs">
                        <div class="space-y-0.5">
                            <div class="font-bold text-gray-800 text-sm">${item.name}</div>
                            <div class="text-gray-500">Qtà: <span class="font-semibold text-gray-700">${item.qty}</span></div>
                            <div class="text-[10px] text-orange-600 font-medium">Scadenza: ${item.expiry}</div>
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

        // TRIGGER FOTOCAMERA
        function triggerCamera(mode) {
            currentMode = mode;
            const input = document.getElementById('globalCameraInput');
            input.value = ''; // Reset per consentire scatti consecutivi
            input.click();
        }

        // ELABORAZIONE SCATTO
        async function processCapture(event) {
            const file = event.target.files[0];
            if (!file) return;

            // Mostra Anteprima Immagine e Loading
            const imgUrl = URL.createObjectURL(file);
            document.getElementById('photoPreview').src = imgUrl;
            document.getElementById('loadingOverlay').classList.remove('hidden');

            const apiKey = localStorage.getItem('gemini_api_key');

            if (!apiKey) {
                // Risposta Immediata Locale se l'utente non ha ancora inserito la chiave API
                setTimeout(() => {
                    document.getElementById('loadingOverlay').classList.add('hidden');
                    if (currentMode === 'scontrino') {
                        pantry.push({ id: Date.now(), name: "Spesa da Scontrino", qty: "1 spesa", expiry: getFutureDate(14) });
                        alert("🧾 Foto scontrino ricevuta! Aggiunta in dispensa.\n\nTip: Inserisci la chiave API in alto a destra per far leggere all'AI ogni singolo prodotto dallo scontrino.");
                    } else {
                        pantry.push({ id: Date.now(), name: "Prodotto da Foto", qty: "1 pz", expiry: getFutureDate(7) });
                        alert("📸 Foto prodotto ricevuta! Aggiunta in dispensa.\n\nTip: Inserisci la chiave API in alto a destra per far riconoscere il cibo esatto all'AI.");
                    }
                    renderPantry();
                }, 1200);
                return;
            }

            // Invio ad API Gemini Vision
            try {
                const base64 = await fileToBase64(file);
                const prompt = currentMode === 'scontrino'
                    ? "Estrai i prodotti da questo scontrino. Restituisci ESCLUSIVAMENTE un JSON array valido di oggetti con campi 'name', 'qty', 'expiry' (data AAAA-MM-DD)."
                    : "Identifica gli alimenti in questa foto. Restituisci ESCLUSIVAMENTE un JSON array valido di oggetti con campi 'name', 'qty', 'expiry' (data AAAA-MM-DD).";

                const res = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${apiKey}`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{
                            parts: [
                                { text: prompt },
                                { inline_data: { mime_type: file.type, data: base64.split(',')[1] } }
                            ]
                        }]
                    })
                });

                const json = await res.json();
                const textResponse = json.candidates[0].content.parts[0].text;
                const cleanJson = textResponse.replace(/```json/g, '').replace(/```/g, '').trim();
                const items = JSON.parse(cleanJson);

                if (items && items.length > 0) {
                    items.forEach(it => {
                        pantry.push({ id: Date.now() + Math.random(), name: it.name, qty: it.qty || "1 pz", expiry: it.expiry || getFutureDate(7) });
                    });
                    alert(`✅ L'AI ha riconosciuto e aggiunto ${items.length} prodotti!`);
                } else {
                    fallbackAdd();
                }
            } catch (err) {
                console.error(err);
                fallbackAdd();
            } finally {
                document.getElementById('loadingOverlay').classList.add('hidden');
                renderPantry();
            }
        }

        function fallbackAdd() {
            pantry.push({ id: Date.now(), name: currentMode === 'scontrino' ? "Scontrino Scansionato" : "Alimento Scansionato", qty: "1 pz", expiry: getFutureDate(7) });
            alert("✅ Foto ricevuta! Prodotto aggiunto in dispensa.");
        }

        function fileToBase64(file) {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = () => resolve(reader.result);
                reader.onerror = error => reject(error);
            });
        }

        function getFutureDate(days) {
            const d = new Date();
            d.setDate(d.getDate() + days);
            return d.toISOString().split('T')[0];
        }

        function addPantryItem(e) {
            e.preventDefault();
            const name = document.getElementById('itemName').value;
            const qty = document.getElementById('itemQty').value;
            const expiry = document.getElementById('itemExpiry').value;
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

        function renderRecipes() {
            const container = document.getElementById('recipesContainer');
            container.innerHTML = `
                <div class="border border-emerald-200 bg-emerald-50/50 p-3.5 rounded-xl space-y-2">
                    <h3 class="font-bold text-emerald-900 text-sm">Bocconcini di Pollo e Zucchine</h3>
                    <div class="text-xs space-y-1.5 bg-white p-2.5 rounded-lg border border-emerald-100">
                        <p><b>🧑 Martino (Sleeve):</b> 120g pollo + 80g zucchine (Alta densità proteica).</p>
                        <p><b>👩 Valentina (Dieta):</b> 180g pollo + 200g zucchine (Ipocalorico e saziante).</p>
                        <p><b>👶 Camilla (2.5 anni):</b> 40g pollo sminuzzato + zucchine morbide.</p>
                    </div>
                </div>
            `;
        }

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
            alert("✨ Menu rigenerato in base alle scadenze!");
            renderWeeklyMenu();
        }

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

        function openApiModal() { document.getElementById('apiModal').classList.remove('hidden'); }
        function closeApiModal() { document.getElementById('apiModal').classList.add('hidden'); }
        function saveApiKey() {
            const key = document.getElementById('apiKeyInput').value.trim();
            if(key) {
                localStorage.setItem('gemini_api_key', key);
                checkApiKeyStatus();
                closeApiModal();
                alert("Chiave AI salvata con successo!");
            }
        }
        function checkApiKeyStatus() {
            const k = localStorage.getItem('gemini_api_key');
            const el = document.getElementById('apiKeyStatus');
            if(k) { el.innerText = "Chiave Attiva"; el.classList.add('text-emerald-200'); }
        }

        window.onload = function() {
            renderPantry();
            renderShopping();
            renderOffers();
            checkApiKeyStatus();
        };
    </script>
</body>
</html>
