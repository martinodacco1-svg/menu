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
            <h1 class="text-xl font-bold flex items-center gap-2"><i class="fa-solid fa-leaf"></i> ZeroSprechi</h1>
            <p class="text-[11px] text-emerald-100">Martino (Sleeve) • Valentina (Dieta) • Camilla (2.5 anni)</p>
        </div>
        <button onclick="openApiModal()" class="bg-emerald-700 hover:bg-emerald-800 text-xs px-2.5 py-1.5 rounded-lg font-medium shadow flex items-center gap-1">
            <i class="fa-solid fa-key"></i> <span id="apiKeyStatus">API Key</span>
        </button>
    </header>

    <main class="p-3 max-w-xl mx-auto space-y-4">

        <!-- TAB 1: DISPENSA & FRIGO -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100 space-y-3">
                <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-camera text-emerald-600"></i> Scansiona con Fotocamera
                </h2>
                
                <p class="text-xs text-gray-500 font-medium">Tocca uno dei due tasti per scattare una foto:</p>

                <!-- I 2 PULSANTI FOTOCAMERA DIRETTI E NATIVI -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                    
                    <!-- 1. FOTOCAMERA SCONTRINO -->
                    <label class="bg-emerald-600 text-white p-4 rounded-xl text-sm font-bold flex items-center justify-center gap-3 cursor-pointer shadow-md active:scale-95 transition">
                        <i class="fa-solid fa-receipt text-2xl"></i>
                        <span class="text-base">Scatta Foto Scontrino</span>
                        <input type="file" accept="image/*" capture="environment" class="hidden" onchange="processPhoto(event, 'scontrino')">
                    </label>

                    <!-- 2. FOTOCAMERA PRODOTTO/DISPENSA -->
                    <label class="bg-blue-600 text-white p-4 rounded-xl text-sm font-bold flex items-center justify-center gap-3 cursor-pointer shadow-md active:scale-95 transition">
                        <i class="fa-solid fa-camera text-2xl"></i>
                        <span class="text-base">Scatta Foto Prodotto</span>
                        <input type="file" accept="image/*" capture="environment" class="hidden" onchange="processPhoto(event, 'prodotto')">
                    </label>

                </div>

                <!-- Modale Inserimento Manuale Veloce (Opzionale) -->
                <details class="text-xs text-gray-600 pt-2 border-t">
                    <summary class="font-semibold cursor-pointer py-1 text-emerald-600">+ Oppure inserisci a mano</summary>
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
                    <h3 class="font-bold text-gray-800 text-sm uppercase">Dispensa (<span id="pantryCount">0</span>)</h3>
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
                <p class="text-xs text-gray-5
