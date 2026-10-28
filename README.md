<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ZeroSprechi Famiglia</title>
    <!-- Tailwind CSS per un design moderno e responsive -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js per i grafici -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- FontAwesome per le icone -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body { background-color: #f3f4f6; color: #1f2937; padding-bottom: 90px; }
        .mobile-nav { position: fixed; bottom: 0; left: 0; right: 0; background: white; border-top: 1px solid #e5e7eb; z-index: 50; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
    </style>
</head>
<body class="min-h-screen">

    <!-- Header -->
    <header class="bg-emerald-600 text-white p-4 shadow-md sticky top-0 z-40 flex justify-between items-center">
        <div>
            <h1 class="text-xl font-bold flex items-center gap-2"><i class="fa-solid fa-seedling"></i> ZeroSprechi Famiglia</h1>
            <p class="text-xs text-emerald-100">Martino (Sleeve) • Valentina (Dieta) • Camilla (2.5 anni)</p>
        </div>
        <button onclick="openApiModal()" class="bg-emerald-700 hover:bg-emerald-800 text-xs px-3 py-2 rounded-lg font-medium shadow transition flex items-center gap-1">
            <i class="fa-solid fa-key"></i> API Key
        </button>
    </header>

    <!-- Container Principale -->
    <main class="p-3 max-w-4xl mx-auto">

        <!-- 1. DISPENSA & FRIGO -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h2 class="text-lg font-bold text-gray-800 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-box-open text-emerald-600"></i> Gestione Dispensa & Frigo
                </h2>
                
                <!-- Pulsanti Azione Rapida / AI -->
                <div class="grid grid-cols-2 gap-2 mb-4">
                    <button onclick="simulateReceiptScan()" class="bg-emerald-50 text-emerald-700 border border-emerald-200 p-2.5 rounded-lg text-xs font-semibold flex items-center justify-center gap-2 hover:bg-emerald-100 transition">
                        <receipt class="fa-solid fa-receipt"></receipt> Scansiona Scontrino (AI)
                    </button>
                    <button onclick="simulateVideoScan()" class="bg-blue-50 text-blue-700 border border-blue-200 p-2.5 rounded-lg text-xs font-semibold flex items-center justify-center gap-2 hover:bg-blue-100 transition">
                        <i class="fa-solid fa-video"></i> Video Frigo/Dispensa
                    </button>
                </div>

                <!-- Form Aggiunta Manuale -->
                <form id="addItemForm" onsubmit="addPantryItem(event)" class="grid grid-cols-1 sm:grid-cols-4 gap-2">
                    <input type="text" id="itemName" placeholder="Nome ingrediente (es. Pollo)" required class="border p-2 rounded-lg text-sm bg-gray-50 focus:bg-white">
                    <input type="number" id="itemQty" placeholder="Qtà (es. 500g / 3 pz)" required class="border p-2 rounded-lg text-sm bg-gray-50 focus:bg-white">
                    <input type="date" id="itemExpiry" required class="border p-2 rounded-lg text-sm bg-gray-50 focus:bg-white">
                    <button type="submit" class="bg-emerald-600 hover:bg-emerald-700 text-white p-2 rounded-lg text-sm font-semibold transition">
                        <i class="fa-solid fa-plus"></i> Aggiungi
                    </button>
                </form>
            </div>

            <!-- Lista Prodotti -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h3 class="font-bold text-gray-700 mb-3 text-sm uppercase tracking-wider">Prodotti Disponibili</h3>
                <div id="pantryList" class="space-y-2">
                    <!-- Javascript popolerà qui -->
                </div>
            </div>
        </section>

        <!-- 2. RICETTE CONSIGLIATE -->
        <section id="tab-ricette" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h2 class="text-lg font-bold text-gray-800 mb-2 flex items-center gap-2">
                    <i class="fa-solid fa-utensili text-emerald-600"></i> Ricette per la Famiglia
                </h2>
                <p class="text-xs text-gray-500 mb-4">Porzioni ricalcolate specificamente per Martino (Sleeve), Valentina (Ipocalorica) e Camilla (Bambina).</p>
                
                <div id="recipesContainer" class="space-y-4">
                    <!-- Dinamico -->
                </div>
            </div>
        </section>

        <!-- 3. MENU SETTIMANALE -->
        <section id="tab-menu" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-calendar-days text-emerald-600"></i> Menu della Settimana
                    </h2>
                    <button onclick="generateWeeklyMenu()" class="bg-emerald-600 text-white text-xs px-3 py-2 rounded-lg font-semibold hover:bg-emerald-700 transition">
                        <i class="fa-solid fa-wand-magic-sparkles"></i> Genera Menu Intelligente
                    </button>
                </div>
                <div id="weeklyMenuContainer" class="space-y-3">
                    <!-- Dinamico -->
                </div>
            </div>
        </section>

        <!-- 4. LISTA DELLA SPESA -->
        <section id="tab-spesa" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h2 class="text-lg font-bold text-gray-800 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-cart-shopping text-emerald-600"></i> Lista della Spesa Intelligente
                </h2>
                <div class="flex gap-2 mb-4">
                    <input type="text" id="shoppingInput" placeholder="Aggiungi prodotto alla spesa..." class="border p-2 rounded-lg text-sm flex-1 bg-gray-50">
                    <button onclick="addShoppingItem()" class="bg-emerald-600 hover:bg-emerald-700 text-white px-4 py-2 rounded-lg text-sm font-semibold">Aggiungi</button>
                </div>
                <div id="shoppingListContainer" class="space-y-2">
                    <!-- Dinamico -->
                </div>
            </div>
        </section>

        <!-- 5. OFFERTE ESSELUNGA -->
        <section id="tab-offerte" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h2 class="text-lg font-bold text-gray-800 mb-1 flex items-center gap-2">
                    <i class="fa-solid fa-tags text-red-600"></i> Offerte Esselunga Consigliate
                </h2>
                <p class="text-xs text-gray-500 mb-4">Selezionate in base ai gusti e alle necessità nutrizionali della famiglia.</p>
                <div id="esselungaOffers" class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                    <!-- Dinamico -->
                </div>
            </div>
        </section>

        <!-- 6. GRAFICI SPESA & SPRECHI -->
        <section id="tab-grafici" class="tab-content space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h2 class="text-lg font-bold text-gray-800 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-chart-pie text-emerald-600"></i> Analisi Spese e Risparmio
                </h2>
                <div class="flex gap-1 mb-4 bg-gray-100 p-1 rounded-lg">
                    <button onclick="updateChartPeriod('giornaliero')" class="flex-1 py-1 text-xs font-semibold rounded bg-white shadow-sm chart-btn">Oggi</button>
                    <button onclick="updateChartPeriod('settimanale')" class="flex-1 py-1 text-xs font-semibold rounded text-gray-600 chart-btn">Settimana</button>
                    <button onclick="updateChartPeriod('mensile')" class="flex-1 py-1 text-xs font-semibold rounded text-gray-600 chart-btn">Mese</button>
                    <button onclick="updateChartPeriod('annuale')" class="flex-1 py-1 text-xs font-semibold rounded text-gray-600 chart-btn">Anno</button>
                </div>
                <div class="relative h-64 w-full">
                    <canvas id="expenseChart"></canvas>
                </div>
            </div>
        </section>

    </main>

    <!-- Navigazione Mobile Inferiore -->
    <nav class="mobile-nav flex justify-around items-center py-2 text-gray-500 text-xs">
        <button onclick="switchTab('dispensa', this)" class="nav-btn flex flex-col items-center text-emerald-600 font-bold">
            <i class="fa-solid fa-box-open text-lg"></i> Dispensa
        </button>
        <button onclick="switchTab('ricette', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-utensils text-lg"></i> Ricette
        </button>
        <button onclick="switchTab('menu', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-calendar-days text-lg"></i> Menu
        </button>
        <button onclick="switchTab('spesa', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-cart-shopping text-lg"></i> Spesa
        </button>
        <button onclick="switchTab('offerte', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-tags text-lg"></i> Offerte
        </button>
        <button onclick="switchTab('grafici', this)" class="nav-btn flex flex-col items-center hover:text-emerald-600">
            <i class="fa-solid fa-chart-pie text-lg"></i> Analisi
        </button>
    </nav>

    <!-- Modal API Key -->
    <div id="apiModal" class="fixed inset-0 bg-black/50 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-5 rounded-xl max-w-sm w-full space-y-3">
            <h3 class="font-bold text-lg text-gray-800">Imposta Google Gemini API Key</h3>
            <p class="text-xs text-gray-500">Inserisci la tua chiave per abilitare il riconoscimento reale di scontrini e video della dispensa.</p>
            <input type="password" id="apiKeyInput" placeholder="Inserisci API Key..." class="w-full border p-2 rounded-lg text-sm">
            <div class="flex justify-end gap-2 pt-2">
                <button onclick="closeApiModal()" class="px-3 py-1.5 text-xs text-gray-600 font-semibold">Annulla</button>
                <button onclick="saveApiKey()" class="px-3 py-1.5 text-xs bg-emerald-600 text-white rounded-lg font-semibold">Salva</button>
            </div>
        </div>
    </div>

    <!-- JavaScript Applicativo -->
    <script>
        // Dati Iniziali
        let pantry = [
            { id: 1, name: "Petto di Pollo", qty: "600g", expiry: "2026-03-05" },
            { id: 2, name: "Zucchine", qty: "1 kg", expiry: "2026-03-03" },
            { id: 3, name: "Pasta Integrale", qty: "500g", expiry: "2027-01-10" },
            { id: 4, name: "Salmone Fresco", qty: "400g", expiry: "2026-03-02" },
            { id: 5, name: "Yogurt Greco", qty: "4 pz", expiry: "2026-03-08" }
        ];

        let shoppingList = [
            { id: 1, name: "Olio Extravergine d'Oliva", checked: false },
            { id: 2, name: "Parmigiano Reggiano", checked: true }
        ];

        let offers = [
            { name: "Petto di Pollo Bio Amadori", price: "€ 7,90 /kg", discount: "-30%", tag: "Ottimo per Martino & Valentina" },
            { name: "Filetti di Salmone Norvegese", price: "€ 14,50 /kg", discount: "-25%", tag: "Ricco di Omega 3" },
            { name: "Yogurt Greco Fage 0%", price: "€ 2,49", discount: "-20%", tag: "Proteico per la dieta" },
            { name: "Pastina d'orzo per bambini", price: "€ 0,89", discount: "-15%", tag: "Ideale per Camilla" }
        ];

        let recipes = [
            {
                title: "Bocconcini di Pollo e Zucchine in umido",
                ingredients: ["Petto di Pollo", "Zucchine"],
                portions: {
                    martino: "120g pollo + 80g zucchine (Alta densità proteica, morbido post-sleeve)",
                    valentina: "180g pollo + 150g zucchine (Ipocalorico, ricco di fibre e saziante)",
                    camilla: "40g pollo sminuzzato + 50g zucchine ben cotte e morbide"
                }
            },
            {
                title: "Salmone al cartoccio con verdure delicate",
                ingredients: ["Salmone Fresco", "Zucchine"],
                portions: {
                    martino: "100g salmone (Porzione ridotta ben cotta e digeribile)",
                    valentina: "150g salmone con contorno leggero",
                    camilla: "30g salmone senza lische schiacciato con verdure"
                }
            }
        ];

        let weeklyMenuData = [
            { day: "Lunedì", lunch: "Bocconcini di Pollo e Zucchine", dinner: "Yogurt Greco e Frutta" },
            { day: "Martedì", lunch: "Salmone al cartoccio", dinner: "Vellutata di verdure" },
            { day: "Mercoledì", lunch: "Pasta Integrale con ricotta", dinner: "Petto di Pollo ai ferri" },
            { day: "Giovedì", lunch: "Pollo e Zucchine avanzate", dinner: "Uova strapazzate e insalata" },
            { day: "Venerdì", lunch: "Salmone con riso basmati", dinner: "Formaggio magro e verdure" },
            { day: "Sabato", lunch: "Pizza integrale light", dinner: "Merluzzo al vapore" },
            { day: "Domenica", lunch: "Arrosto leggero di tacchino", dinner: "Vellutata leggera" }
        ];

        let expenseChartInstance = null;

        // Gestione Tab / Schermate
        function switchTab(tabId, btnElement) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            document.getElementById('tab-' + tabId).classList.add('active');

            document.querySelectorAll('.mobile-nav button').forEach(btn
