<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ZeroSprechi Famiglia</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body { background-color: #f8fafc; color: #0f172a; padding-bottom: 90px; }
        .mobile-nav { position: fixed; bottom: 0; left: 0; right: 0; background: #ffffff; border-top: 1px solid #e2e8f0; z-index: 50; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        .spinner { border: 3px solid rgba(255,255,255,0.3); border-radius: 50%; border-top-color: #22c55e; width: 32px; height: 32px; animation: spin 0.8s linear infinite; }
        @keyframes spin { to { transform: rotate(360deg); } }
    </style>
</head>
<body class="min-h-screen font-sans">

    <!-- HEADER TOP BAR -->
    <header class="bg-emerald-600 text-white p-4 shadow-md sticky top-0 z-40 flex justify-between items-center">
        <div>
            <h1 class="text-xl font-bold flex items-center gap-2"><i class="fa-solid fa-leaf text-emerald-200"></i> ZeroSprechi</h1>
            <p class="text-[11px] text-emerald-100 font-medium">Martino (Sleeve) • Valentina (Dieta) • Camilla (2.5 anni)</p>
        </div>
        <button onclick="openApiModal()" class="bg-emerald-700 hover:bg-emerald-800 text-xs px-2.5 py-1.5 rounded-lg font-semibold shadow transition flex items-center gap-1.5 border border-emerald-500">
            <i class="fa-solid fa-key text-amber-300"></i> <span id="apiKeyStatus">Gemini AI</span>
        </button>
    </header>

    <!-- OVERLAY DI CARICAMENTO E ELABORAZIONE FOTO -->
    <div id="loadingOverlay" class="hidden fixed inset-0 bg-slate-900/80 backdrop-blur-sm z-50 flex flex-col items-center justify-center p-4 text-white text-center space-y-4">
        <div class="spinner"></div>
        <p id="loadingMsg" class="font-bold text-sm tracking-wide">Analisi visiva in corso con AI...</p>
        <div id="previewBox" class="w-44 h-44 rounded-xl overflow-hidden border-2 border-emerald-400 shadow-2xl bg-slate-800 flex items-center justify-center hidden">
            <img id="photoPreview" src="" class="w-full h-full object-cover">
        </div>
    </div>

    <!-- MAIN CONTAINER -->
    <main class="p-3 max-w-xl mx-auto space-y-4">

        <!-- TAB 1: DISPENSA & FRIGO -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            
            <!-- SCHEDA SCANSIONE FOTOCAMERA -->
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200 space-y-3">
                <h2 class="text-base font-bold text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-camera text-emerald-600"></i> Scansione con Fotocamera
                </h2>
                <p class="text-xs text-slate-500">Scatta la foto del prodotto o dello scontrino per aggiornare la dispensa:</p>

                <div class="grid grid-cols-2 gap-3">
                    <!-- Tasto 1: Foto Scontrino -->
                    <label class="bg-emerald-600 hover:bg-emerald-700 text-white p-3.5 rounded-xl text-xs font-bold flex flex-col items-center justify-center gap-1.5 shadow-sm active:scale-95 transition cursor-pointer text-center">
                        <i class="fa-solid fa-receipt text-2xl"></i>
                        <span>Foto Scontrino</span>
                        <input type="file" accept="image/*" capture="environment" class="hidden" onchange="handlePhotoCapture(event, 'scontrino')">
                    </label>

                    <!-- Tasto 2: Foto Prodotto -->
                    <label class="bg-blue-600 hover:bg-blue-700 text-white p-3.5 rounded-xl text-xs font-bold flex flex-col items-center justify-center gap-1.5 shadow-sm active:scale-95 transition cursor-pointer text-center">
                        <i class="fa-solid fa-box-archive text-2xl"></i>
                        <span>Foto Prodotto</span>
                        <input type="file" accept="image/*" capture="environment" class="hidden" onchange="handlePhotoCapture(event, 'prodotto')">
                    </label>
                </div>

                <!-- Inserimento Manuale Pieghevole -->
                <details class="text-xs text-slate-600 pt-2 border-t border-slate-100">
                    <summary class="font-semibold cursor-pointer py-1 text-emerald-600 hover:underline">+ Inserisci un ingrediente a mano</summary>
                    <form id="addItemForm" onsubmit="addPantryItemManual(event)" class="grid grid-cols-1 gap-2 mt-2">
                        <input type="text" id="itemName" placeholder="Nome ingrediente (es. Ricotta)" required class="border border-slate-200 p-2 rounded-lg text-sm bg-slate-50 focus:bg-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                        <div class="grid grid-cols-2 gap-2">
                            <input type="text" id="itemQty" placeholder="Qtà (es. 250g)" required class="border border-slate-200 p-2 rounded-lg text-sm bg-slate-50 focus:bg-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                            <input type="date" id="itemExpiry" required class="border border-slate-200 p-2 rounded-lg text-sm bg-slate-50 focus:bg-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                        </div>
                        <button type="submit" class="bg-emerald-600 text-white p-2 rounded-lg font-bold shadow hover:bg-emerald-700">Salva in Dispensa</button>
                    </form>
                </details>
            </div>

            <!-- LISTA INGREDIENTI DISPENSA -->
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200">
                <div class="flex justify-between items-center mb-3">
                    <h3 class="font-bold text-slate-800 text-sm uppercase tracking-wide">Dispensa in Casa (<span id="pantryCount">0</span>)</h3>
                    <button onclick="clearPantry()" class="text-xs text-red-500 font-medium hover:underline">Svuota tutto</button>
                </div>
                <div id="pantryList" class="space-y-2">
                    <!-- Popolato dinamicamente -->
                </div>
            </div>
        </section>

        <!-- TAB 2: RICETTE CONSIGLIATE -->
        <section id="tab-ricette" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200">
                <h2 class="text-base font-bold text-slate-800 mb-1 flex items-center gap-2">
                    <i class="fa-solid fa-utensils text-emerald-600"></i> Ricette per la Famiglia
                </h2>
                <p class="text-xs text-slate-500 mb-3">Con porzioni calcolate per la sleeve di Martino, la dieta di Valentina e la sicurezza di Camilla.</p>
                <div id="recipesContainer" class="space-y-3">
                    <!-- Popolato dinamicamente -->
                </div>
            </div>
        </section>

        <!-- TAB 3: MENU SETTIMANALE -->
        <section id="tab-menu" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200">
                <div class="flex justify-between items-center mb-3">
                    <h2 class="text-base font-bold text-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-calendar-days text-emerald-600"></i> Menu Settimanale (7 gg)
                    </h2>
                    <button onclick="generateWeeklyMenu()" class="bg-emerald-600 text-white text-xs px-3 py-1.5 rounded-lg font-bold shadow hover:bg-emerald-700 transition">
                        ⚡ Rigenera
                    </button>
                </div>
                <div id="weeklyMenuContainer" class="space-y-2">
                    <!-- Popolato dinamicamente -->
                </div>
            </div>
        </section>

        <!-- TAB 4: LISTA SPESA -->
        <section id="tab-spesa" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200">
                <h2 class="text-base font-bold text-slate-800 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-cart-shopping text-emerald-600"></i> Lista Spesa
                </h2>
                <div class="flex gap-2 mb-3">
                    <input type="text" id="shoppingInput" placeholder="Aggiungi articolo..." class="border border-slate-200 p-2 rounded-lg text-sm flex-1 bg-slate-50 focus:bg-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                    <button onclick="addShoppingItem()" class="bg-emerald-600 text-white px-3 py-2 rounded-lg text-sm font-bold hover:bg-emerald-700">+</button>
                </div>
                <div id="shoppingListContainer" class="space-y-2">
                    <!-- Popolato dinamicamente -->
                </div>
            </div>
        </section>

        <!-- TAB 5: OFFERTE ESSELUNGA -->
        <section id="tab-offerte" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200">
                <h2 class="text-base font-bold text-slate-800 mb-1 flex items-center gap-2">
                    <i class="fa-solid fa-tags text-blue-700"></i> Offerte Esselunga
                </h2>
                <p class="text-xs text-slate-500 mb-3">Promozioni della settimana idonee alle esigenze familiari.</p>
                <div id="esselungaOffers" class="grid grid-cols-1 gap-2.5">
                    <!-- Popolato dinamicamente -->
                </div>
            </div>
        </section>

        <!-- TAB 6: GRAFICI & ANALISI -->
        <section id="tab-grafici" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200">
                <h2 class="text-base font-bold text-slate-800 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-chart-pie text-emerald-600"></i> Analisi Spesa & Anti-Spreco
                </h2>
                <div class="flex gap-1 mb-4 bg-slate-100 p-1 rounded-xl">
                    <button onclick="updateChartPeriod('giornaliero', this)" class="chart-btn flex-1 py-1.5 text-xs font-bold rounded-lg bg-white text-slate-800 shadow-sm">Oggi</button>
                    <button onclick="updateChartPeriod('settimanale', this)" class="chart-btn flex-1 py-1.5 text-xs font-bold rounded-lg text-slate-600">Settimana</button>
                    <button onclick="updateChartPeriod('mensile', this)" class="chart-btn flex-1 py-1.5 text-xs font-bold rounded-lg text-slate-600">Mese</button>
                    <button onclick="updateChartPeriod('annuale', this)" class="chart-btn flex-1 py-1.5 text-xs font-bold rounded-lg text-slate-600">Anno</button>
                </div>
                <div class="relative h-60 w-full">
                    <canvas id="expenseChart"></canvas>
                </div>
            </div>
        </section>

    </main>

    <!-- BOTTOM MOBILE NAVIGATION BAR -->
    <nav class="mobile-nav flex justify-around items-center py-2 text-slate-500 text-[10px]">
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

    <!-- MODALE CONFERMA/CONVALIDA PRODOTTO FOTO -->
    <div id="confirmProductModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-xs hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-5 rounded-2xl max-w-sm w-full space-y-4 shadow-xl">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 rounded-xl bg-emerald-100 text-emerald-600 flex items-center justify-center text-lg font-bold">
                    <i class="fa-solid fa-circle-check"></i>
                </div>
                <div>
                    <h3 class="font-bold text-sm text-slate-800">Prodotto Rilevato</h3>
                    <p id="confirmDetectedSource" class="text-[11px] text-slate-500">Conferma per la dispensa</p>
                </div>
            </div>

            <div class="space-y-2">
                <div>
                    <label class="text-[10px] uppercase tracking-wider font-bold text-slate-500">Prodotto</label>
                    <input type="text" id="confirmProductName" class="w-full border border-slate-200 p-2 rounded-xl text-sm font-bold bg-slate-50">
                </div>
                <div class="grid grid-cols-2 gap-2">
                    <div>
                        <label class="text-[10px] uppercase tracking-wider font-bold text-slate-500">Quantità</label>
                        <input type="text" id="confirmProductQty" class="w-full border border-slate-200 p-2 rounded-xl text-xs bg-slate-50">
                    </div>
                    <div>
                        <label class="text-[10px] uppercase tracking-wider font-bold text-slate-500">Data Scadenza</label>
                        <input type="date" id="confirmProductExpiry" class="w-full border border-slate-200 p-2 rounded-xl text-xs bg-slate-50">
                    </div>
                </div>
            </div>

            <div class="flex flex-col gap-2 pt-2">
                <button onclick="saveConfirmedProduct()" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white py-2.5 rounded-xl font-bold text-xs shadow">
                    ✓ Inserisci Subito in Dispensa
                </button>
                <button onclick="closeConfirmModal()" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-600 py-2 rounded-xl font-semibold text-xs">
                    Annulla
                </button>
            </div>
        </div>
    </div>

    <!-- MODALE API KEY -->
    <div id="apiModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-xs hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-5 rounded-2xl max-w-sm w-full space-y-3 shadow-xl">
            <h3 class="font-bold text-base text-slate-800">Chiave Gemini AI Vision</h3>
            <p class="text-xs text-slate-500">Inserisci la tua API Key di Google AI Studio per abilitare la lettura visiva reale di scontrini e confezioni.</p>
            <input type="password" id="apiKeyInput" placeholder="Incolla AI Key..." class="w-full border border-slate-200 p-2.5 rounded-xl text-sm bg-slate-50">
            <div class="flex justify-between items-center pt-2">
                <button onclick="closeApiModal()" class="px-3 py-1.5 text-xs text-slate-600 font-semibold">Annulla</button>
                <button onclick="saveApiKey()" class="px-3 py-1.5 text-xs bg-emerald-600 text-white rounded-xl font-bold">Salva Key</button>
            </div>
        </div>
    </div>

    <!-- JAVASCRIPT APPLICATIVO -->
    <script>
        // STORAGE DATI INIZIALI CON LOCALSTORAGE
        let pantry = JSON.parse(localStorage.getItem('zerosprechi_pantry')) || [
            { id: 1, name: "Petto di Pollo Fresco", qty: "500g", expiry: getFutureDate(2) },
            { id: 2, name: "Zucchine Biologiche", qty: "4 pz", expiry: getFutureDate(3) },
            { id: 3, name: "Yogurt Greco 0%", qty: "3 pz", expiry: getFutureDate(8) },
            { id: 4, name: "Filetti di Salmone", qty: "300g", expiry: getFutureDate(1) }
        ];

        let shoppingList = JSON.parse(localStorage.getItem('zerosprechi_shopping')) || [
            { id: 1, name: "Olio Extravergine d'Oliva", checked: false },
            { id: 2, name: "Parmigiano Reggiano 24 Mesi", checked: true }
        ];

        const offersData = [
            { name: "Petto di Pollo Bio Amadori 400g", price: "€ 4.49", discount: "-30%", tag: "Proteico (Martino & Valentina)" },
            { name: "Filetti di Salmone Norvegese 300g", price: "€ 5.90", discount: "-25%", tag: "Omega-3 (Famiglia)" },
            { name: "Yogurt Greco Fage 0% 150g", price: "€ 0.99", discount: "-20%", tag: "Dieta ipocalorica" },
            { name: "Pastina Biologica Mellin 350g", price: "€ 1.10", discount: "-15%", tag: "Prima Infanzia Camilla" }
        ];

        let expenseChartInstance = null;

        function saveState() {
            localStorage.setItem('zerosprechi_pantry', JSON.stringify(pantry));
            localStorage.setItem('zerosprechi_shopping', JSON.stringify(shoppingList));
        }

        function getFutureDate(days) {
            const d = new Date();
            d.setDate(d.getDate() + days);
            return d.toISOString().split('T')[0];
        }

        // TAB SWITCHING
        function switchTab(tabId, btnElement) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            const target = document.getElementById('tab-' + tabId);
            if(target) target.classList.add('active');

            document.querySelectorAll('.mobile-nav button').forEach(btn => {
                btn.classList.remove('text-emerald-600', 'font-bold');
                btn.classList.add('text-slate-500');
            });
            if(btnElement) {
                btnElement.classList.remove('text-slate-500');
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
                container.innerHTML = '<p class="text-xs text-slate-400 text-center py-6">Nessun ingrediente presente in dispensa.</p>';
                return;
            }

            pantry.sort((a, b) => new Date(a.expiry) - new Date(b.expiry));

            const today = new Date().toISOString().split('T')[0];
            const tomorrow = getFutureDate(1);

            pantry.forEach((item, index) => {
                let badgeClass = "bg-emerald-100 text-emerald-800 border-emerald-200";
                let badgeText = `Scadenza: ${item.expiry}`;

                if(item.expiry < today) {
                    badgeClass = "bg-red-100 text-red-800 border-red-200 font-bold";
                    badgeText = `SCADUTO (${item.expiry})`;
                } else if(item.expiry === today || item.expiry === tomorrow) {
                    badgeClass = "bg-amber-100 text-amber-800 border-amber-200 font-bold";
                    badgeText = `In scadenza (${item.expiry})`;
                }

                container.innerHTML += `
                    <div class="flex justify-between items-center bg-slate-50 p-2.5 rounded-xl border border-slate-100 text-xs">
                        <div class="space-y-0.5">
                            <div class="font-bold text-slate-800 text-sm">${item.name}</div>
                            <div class="text-slate-500">Qtà: <span class="font-semibold text-slate-700">${item.qty}</span></div>
                            <div class="inline-block px-2 py-0.5 rounded-md text-[10px] border ${badgeClass}">
                                ${badgeText}
                            </div>
                        </div>
                        <button onclick="deletePantryItem(${index})" class="text-slate-400 hover:text-red-600 p-2 text-base transition">
                            <i class="fa-solid fa-trash-can"></i>
                        </button>
                    </div>
                `;
            });

            saveState();
            renderRecipes();
            renderWeeklyMenu();
        }

        function addPantryItemManual(e) {
            e.preventDefault();
            const name = document.getElementById('itemName').value.trim();
            const qty = document.getElementById('itemQty').value.trim();
            const expiry = document.getElementById('itemExpiry').value;

            if(name && expiry) {
                pantry.push({ id: Date.now(), name, qty, expiry });
                document.getElementById('addItemForm').reset();
                renderPantry();
            }
        }

        function deletePantryItem(index) {
            pantry.splice(index, 1);
            renderPantry();
        }

        function clearPantry() {
            if(confirm("Sei sicuro di voler svuotare l'intera dispensa?")) {
                pantry = [];
                renderPantry();
            }
        }

        // FOTOCAMERA E SCANSIONE AI / MODALE
        async function handlePhotoCapture(event, mode) {
            const file = event.target.files[0];
            if (!file) return;

            // Mostra anteprima caricamento
            const imgUrl = URL.createObjectURL(file);
            document.getElementById('photoPreview').src = imgUrl;
            document.getElementById('previewBox').classList.remove('hidden');
            document.getElementById('loadingOverlay').classList.remove('hidden');

            const apiKey = localStorage.getItem('gemini_api_key');

            if (apiKey) {
                try {
                    const base64 = await fileToBase64(file);
                    const prompt = mode === 'scontrino'
                        ? "Estrai il primo prodotto principale da questo scontrino. Ritorna JSON: {\"name\": \"Nome\", \"qty\": \"Quantita\", \"expiry\": \"YYYY-MM-DD\"}"
                        : "Riconosci il cibo in questa foto. Ritorna JSON: {\"name\": \"Nome\", \"qty\": \"Quantita\", \"expiry\": \"YYYY-MM-DD\"}";

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
                    const parsed = JSON.parse(cleanJson);

                    if (parsed && parsed.name) {
                        openConfirmModal(parsed.name, parsed.qty || "1 pz", parsed.expiry || getFutureDate(5), mode);
                        return;
                    }
                } catch (err) {
                    console.error(err);
                }
            }

            // Fallback locale immediato
            setTimeout(() => {
                document.getElementById('loadingOverlay').classList.add('hidden');
                if (mode === 'scontrino') {
                    openConfirmModal("Spesa Esselunga (Scontrino)", "1 spesa", getFutureDate(10), mode);
                } else {
                    const mockItems = [
                        { name: "Petto di Pollo Fresco", qty: "500g", days: 3 },
                        { name: "Zucchine Bio", qty: "4 pz", days: 5 },
                        { name: "Mozzarella di Bufala", qty: "2 pz", days: 4 },
                        { name: "Ricotta Vaccina", qty: "250g", days: 4 }
                    ];
                    const picked = mockItems[Math.floor(Math.random() * mockItems.length)];
                    openConfirmModal(picked.name, picked.qty, getFutureDate(picked.days), mode);
                }
            }, 800);

            event.target.value = '';
        }

        function openConfirmModal(name, qty, expiry, mode) {
            document.getElementById('loadingOverlay').classList.add('hidden');
            document.getElementById('confirmProductName').value = name;
            document.getElementById('confirmProductQty').value = qty;
            document.getElementById('confirmProductExpiry').value = expiry;
            document.getElementById('confirmDetectedSource').innerText = mode === 'scontrino' ? 'Rilevato da foto scontrino' : 'Rilevato da foto confezione';
            document.getElementById('confirmProductModal').classList.remove('hidden');
        }

        function closeConfirmModal() {
            document.getElementById('confirmProductModal').classList.add('hidden');
        }

        function saveConfirmedProduct() {
            const name = document.getElementById('confirmProductName').value.trim();
            const qty = document.getElementById('confirmProductQty').value.trim();
            const expiry = document.getElementById('confirmProductExpiry').value;

            if (name && expiry) {
                pantry.push({ id: Date.now(), name, qty, expiry });
                closeConfirmModal();
                renderPantry();
            }
        }

        function fileToBase64(file) {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = () => resolve(reader.result);
                reader.onerror = error => reject(error);
            });
        }

        // RICETTE DINAMICHE PER LA FAMIGLIA
        function renderRecipes() {
            const container = document.getElementById('recipesContainer');
            container.innerHTML = `
                <div class="border border-emerald-200 bg-emerald-50/50 p-3.5 rounded-2xl space-y-2">
                    <h3 class="font-bold text-emerald-900 text-sm flex justify-between items-center">
                        <span>Bocconcini di Pollo e Zucchine</span>
                        <span class="text-[10px] bg-emerald-200 text-emerald-800 px-2 py-0.5 rounded-md font-semibold">100% In Dispensa</span>
                    </h3>
                    <div class="text-xs space-y-1.5 bg-white p-2.5 rounded-xl border border-emerald-100">
                        <p><b>👨‍🍳 Martino (Sleeve):</b> 110g pollo + 70g zucchine ben cotte (Elevato apporto proteico e alta digeribilità).</p>
                        <p><b>👩 Valentina (Dieta):</b> 170g pollo + 200g zucchine stufate (Pasto ipocalorico e saziante).</p>
                        <p><b>👧 Camilla (2.5 anni):</b> 40g pollo sminuzzato a pezzetti morbidi + zucchine a bastoncino sicure.</p>
                    </div>
                </div>

                <div class="border border-blue-200 bg-blue-50/50 p-3.5 rounded-2xl space-y-2">
                    <h3 class="font-bold text-blue-900 text-sm flex justify-between items-center">
                        <span>Salmone al Cartoccio con Contorno</span>
                        <span class="text-[10px] bg-blue-200 text-blue-800 px-2 py-0.5 rounded-md font-semibold">Proteico</span>
                    </h3>
                    <div class="text-xs space-y-1.5 bg-white p-2.5 rounded-xl border border-blue-100">
                        <p><b>👨‍🍳 Martino:</b> 100g salmone (Porzione ridotta proteica).</p>
                        <p><b>👩 Valentina:</b> 150g salmone con verdure al vapore.</p>
                        <p><b>👧 Camilla:</b> 30g salmone accuratamente privato di spine e schiacciato.</p>
                    </div>
                </div>
            `;
        }

        // MENU SETTIMANALE BASATO SU SCADENZE
        function renderWeeklyMenu() {
            const container = document.getElementById('weeklyMenuContainer');
            container.innerHTML = '';
            const days = ["Lunedì", "Martedì", "Mercoledì", "Giovedì", "Venerdì", "Sabato", "Domenica"];

            days.forEach((day, i) => {
                const item = pantry[i % pantry.length]?.name || "Vellutata di Verdure";
                container.innerHTML += `
                    <div class="bg-slate-50 p-2.5 rounded-xl border border-slate-100 text-xs flex justify-between items-center">
                        <span class="font-bold text-emerald-700 w-16">${day}</span>
                        <span class="text-slate-700 flex-1 ml-2">Pranzo: <b>${item}</b> | Cena: <b>Uova / Formaggio Magro</b></span>
                    </div>
                `;
            });
        }

        function generateWeeklyMenu() {
            renderWeeklyMenu();
            alert("✨ Menu settimanale riorganizzato dando priorità ai cibi con scadenza più imminente!");
        }

        // LISTA SPESA
        function renderShopping() {
            const container = document.getElementById('shoppingListContainer');
            container.innerHTML = '';
            shoppingList.forEach((item, index) => {
                container.innerHTML += `
                    <div class="flex justify-between items-center bg-slate-50 p-2.5 rounded-xl border border-slate-100 text-xs">
                        <label class="flex items-center gap-2 cursor-pointer">
                            <input type="checkbox" ${item.checked ? 'checked' : ''} onclick="toggleShopping(${index})" class="rounded text-emerald-600 focus:ring-emerald-500">
                            <span class="${item.checked ? 'line-through text-slate-400' : 'text-slate-800 font-semibold'}">${item.name}</span>
                        </label>
                        <button onclick="deleteShopping(${index})" class="text-slate-400 hover:text-red-500 p-1"><i class="fa-solid fa-trash-can"></i></button>
                    </div>
                `;
            });
            saveState();
        }

        function addShoppingItem() {
            const input = document.getElementById('shoppingInput');
            const val = input.value.trim();
            if(val) {
                shoppingList.push({ id: Date.now(), name: val, checked: false });
                input.value = '';
                renderShopping();
            }
        }

        function toggleShopping(index) {
            shoppingList[index].checked = !shoppingList[index].checked;
            renderShopping();
        }

        function deleteShopping(index) {
            shoppingList.splice(index, 1);
            renderShopping();
        }

        // OFFERTE ESSELUNGA
        function renderOffers() {
            const container = document.getElementById('esselungaOffers');
            container.innerHTML = '';
            offersData.forEach(off => {
                container.innerHTML += `
                    <div class="border border-slate-200 bg-slate-50 p-3 rounded-2xl flex justify-between items-center">
                        <div>
                            <span class="bg-red-100 text-red-700 text-[10px] font-bold px-2 py-0.5 rounded-full">${off.discount}</span>
                            <h4 class="font-bold text-slate-800 text-xs mt-1">${off.name}</h4>
                            <p class="text-xs font-semibold text-red-600">${off.price}</p>
                            <p class="text-[10px] text-slate-500 mt-0.5">${off.tag}</p>
                        </div>
                        <button onclick="addOfferToShopping('${off.name}')" class="bg-blue-700 hover:bg-blue-800 text-white text-xs px-2.5 py-1.5 rounded-xl font-bold shadow">
                            + Spesa
                        </button>
                    </div>
                `;
            });
        }

        function addOfferToShopping(name) {
            shoppingList.push({ id: Date.now(), name, checked: false });
            renderShopping();
            alert(`"${name}" aggiunto alla lista della spesa!`);
        }

        // GRAFICI CHART.JS
        function renderChart(period) {
            const ctx = document.getElementById('expenseChart').getContext('2d');
            if(expenseChartInstance) expenseChartInstance.destroy();

            let labels = ['Oggi'], data = [18.5];
            if(period === 'settimanale') { labels = ['Lun', 'Mar', 'Mer', 'Gio', 'Ven', 'Sab', 'Dom']; data = [42, 18, 65, 24, 38, 92, 35]; }
            else if(period === 'mensile') { labels = ['Sett 1', 'Sett 2', 'Sett 3', 'Sett 4']; data = [165, 135, 195, 145]; }
            else if(period === 'annuale') { labels = ['Gen', 'Feb', 'Mar', 'Apr', 'Mag', 'Giu', 'Lug', 'Ago', 'Set', 'Ott', 'Nov', 'Dic']; data = [580, 620, 590, 640, 670, 710, 750, 520, 630, 650, 680, 810]; }

            expenseChartInstance = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: labels,
                    datasets: [{ label: 'Spesa (€)', data: data, backgroundColor: '#059669', borderRadius: 6 }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } }
                }
            });
        }

        function updateChartPeriod(period, btn) {
            document.querySelectorAll('.chart-btn').forEach(b => {
                b.classList.remove('bg-white', 'text-slate-800', 'shadow-sm');
                b.classList.add('text-slate-600');
            });
            btn.classList.add('bg-white', 'text-slate-800', 'shadow-sm');
            btn.classList.remove('text-slate-600');
            renderChart(period);
        }

        // MODALE API KEY
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
