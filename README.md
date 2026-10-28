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
            <i class="fa-solid fa-key"></i> <span id="apiKeyStatus">AI opzionale</span>
        </button>
    </header>

    <!-- Modale Automatico Post-Scatto: Riconosce e chiede solo la scadenza -->
    <div id="autoRecognizeModal" class="fixed inset-0 bg-black/60 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white p-5 rounded-2xl max-w-sm w-full space-y-4 shadow-2xl">
            <div class="flex items-center gap-3">
                <div class="w-12 h-12 rounded-xl bg-emerald-100 flex items-center justify-center text-emerald-600 text-xl font-bold">
                    <i id="modalIcon" class="fa-solid fa-basket-shopping"></i>
                </div>
                <div>
                    <h3 class="font-bold text-base text-gray-800">Prodotto Riconosciuto!</h3>
                    <p id="detectedName" class="text-sm font-bold text-emerald-600">Pollo / Carne Fresca</p>
                </div>
            </div>

            <div class="space-y-1">
                <label class="text-xs font-bold text-gray-600">Conferma o modifica data di scadenza:</label>
                <input type="date" id="autoExpiry" class="w-full border p-2.5 rounded-xl text-sm bg-gray-50 font-semibold">
                <input type="hidden" id="autoDetectedNameFinal">
                <input type="hidden" id="autoDetectedQtyFinal">
            </div>

            <div class="flex flex-col gap-2 pt-1">
                <button onclick="confirmAutoAdd()" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white py-3 rounded-xl font-bold text-sm shadow">
                    ✓ Inserisci in Dispensa
                </button>
                <button onclick="closeAutoModal()" class="w-full bg-gray-100 hover:bg-gray-200 text-gray-600 py-2 rounded-xl font-semibold text-xs">
                    Annulla
                </button>
            </div>
        </div>
    </div>

    <main class="p-3 max-w-xl mx-auto space-y-4">

        <!-- TAB 1: DISPENSA & FRIGO -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100 space-y-3">
                <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-camera text-emerald-600"></i> Scansione Automatica Fotocamera
                </h2>
                
                <p class="text-xs text-gray-500 font-medium">Scatta la foto: l'app riconoscerà il prodotto al posto tuo!</p>

                <!-- Tasti Fotocamera diretti -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                    <button onclick="triggerCamera('scontrino')" class="bg-emerald-600 hover:bg-emerald-700 text-white p-4 rounded-xl text-sm font-bold flex items-center justify-center gap-3 shadow-md active:scale-95 transition">
                        <i class="fa-solid fa-receipt text-2xl"></i>
                        <span class="text-base">Foto Scontrino</span>
                    </button>
                    <button onclick="triggerCamera('prodotto')" class="bg-blue-600 hover:bg-blue-700 text-white p-4 rounded-xl text-sm font-bold flex items-center justify-center gap-3 shadow-md active:scale-95 transition">
                        <i class="fa-solid fa-camera text-2xl"></i>
                        <span class="text-base">Foto Prodotto</span>
                    </button>
                </div>

                <input type="file" id="globalCameraInput" accept="image/*" capture="environment" class="hidden" onchange="processAutoCapture(event)">

                <!-- Inserimento Manuale Veloce -->
                <details class="text-xs text-gray-600 pt-2 border-t">
                    <summary class="font-semibold cursor-pointer py-1 text-emerald-600">+ Inserisci manualmente</summary>
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
                <div class="flex justify-between items
