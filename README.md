<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ZeroSprechi Famiglia - Gestione Cibo & Menu Custom</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Google Fonts Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    },
                    colors: {
                        brand: {
                            50: '#f0fdf4',
                            100: '#dcfce7',
                            500: '#22c55e',
                            600: '#16a34a',
                            700: '#15803d',
                            800: '#166534',
                        },
                        esselunga: '#003399',
                    }
                }
            }
        }
    </script>
    <style>
        body { font-family: 'Inter', sans-serif; }
        .tab-btn.active {
            background-color: #22c55e;
            color: white;
            box-shadow: 0 4px 6px -1px rgba(34, 197, 94, 0.3);
        }
        .hide-scrollbar::-webkit-scrollbar { display: none; }
        .hide-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col md:flex-row pb-16 md:pb-0">

    <!-- SIDEBAR NAV (Desktop) -->
    <aside class="w-full md:w-64 bg-white border-r border-slate-200 p-4 md:flex md:flex-col md:justify-between hidden md:block">
        <div>
            <!-- Logo & Brand Header -->
            <div class="flex items-center space-x-3 mb-8 px-2">
                <div class="w-10 h-10 rounded-xl bg-brand-500 text-white flex items-center justify-center font-bold text-xl shadow-lg shadow-brand-500/30">
                    <i class="fa-solid fa-leaf"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg leading-tight text-slate-800">ZeroSprechi</h1>
                    <p class="text-xs text-brand-600 font-medium">Famiglia Smart Menu</p>
                </div>
            </div>

            <!-- Profile Badges Summary -->
            <div class="mb-6 p-3 bg-slate-100 rounded-xl space-y-2">
                <div class="text-xs font-semibold uppercase tracking-wider text-slate-500 mb-1">Membri Famiglia</div>
                <div class="flex items-center space-x-2 text-xs">
                    <span class="w-2 h-2 rounded-full bg-emerald-500"></span>
                    <span class="font-medium text-slate-700">Martino (41, Sleeve)</span>
                </div>
                <div class="flex items-center space-x-2 text-xs">
                    <span class="w-2 h-2 rounded-full bg-blue-500"></span>
                    <span class="font-medium text-slate-700">Valentina (35, Fit)</span>
                </div>
                <div class="flex items-center space-x-2 text-xs">
                    <span class="w-2 h-2 rounded-full bg-amber-500"></span>
                    <span class="font-medium text-slate-700">Camilla (2.5y)</span>
                </div>
            </div>

            <!-- Main Navigation Links -->
            <nav class="space-y-1 text-sm font-medium">
                <button onclick="switchTab('dispensa')" id="nav-dispensa" class="tab-btn active w-full flex items-center space-x-3 px-3 py-2.5 rounded-lg text-slate-600 hover:bg-slate-100 transition">
                    <i class="fa-solid fa-box-archive w-5"></i>
                    <span>Dispensa & Frigo</span>
                </button>
                <button onclick="switchTab('ricette')" id="nav-ricette" class="tab-btn w-full flex items-center space-x-3 px-3 py-2.5 rounded-lg text-slate-600 hover:bg-slate-100 transition">
                    <i class="fa-solid fa-utensils w-5"></i>
                    <span>Ricette Consigliate</span>
                </button>
                <button onclick="switchTab('menu')" id="nav-menu" class="tab-btn w-full flex items-center space-x-3 px-3 py-2.5 rounded-lg text-slate-600 hover:bg-slate-100 transition">
                    <i class="fa-solid fa-calendar-days w-5"></i>
                    <span>Menu Settimanale</span>
                </button>
                <button onclick="switchTab('spesa')" id="nav-spesa" class="tab-btn w-full flex items-center space-x-3 px-3 py-2.5 rounded-lg text-slate-600 hover:bg-slate-100 transition">
                    <i class="fa-solid fa-cart-shopping w-5"></i>
                    <span>Lista Spesa</span>
                </button>
                <button onclick="switchTab('esselunga')" id="nav-esselunga" class="tab-btn w-full flex items-center space-x-3 px-3 py-2.5 rounded-lg text-slate-600 hover:bg-slate-100 transition">
                    <i class="fa-solid fa-tags w-5 text-blue-700"></i>
                    <span>Offerte Esselunga</span>
                </button>
                <button onclick="switchTab('grafici')" id="nav-grafici" class="tab-btn w-full flex items-center space-x-3 px-3 py-2.5 rounded-lg text-slate-600 hover:bg-slate-100 transition">
                    <i class="fa-solid fa-chart-pie w-5"></i>
                    <span>Grafici & Analisi</span>
                </button>
                <button onclick="switchTab('github')" id="nav-github" class="tab-btn w-full flex items-center space-x-3 px-3 py-2.5 rounded-lg text-slate-600 hover:bg-slate-100 transition">
                    <i class="fa-brands fa-github w-5"></i>
                    <span>Guida GitHub & AI</span>
                </button>
            </nav>
        </div>

        <div class="pt-4 border-t border-slate-200 text-xs text-slate-400">
            <p>ZeroSprechi v2.5 • AI Enabled</p>
            <p class="text-[10px] mt-1">Sincronizzato locale / Cloud</p>
        </div>
    </aside>

    <!-- MOBILE TOP BAR -->
    <div class="md:hidden bg-white border-b border-slate-200 px-4 py-3 flex items-center justify-between sticky top-0 z-40">
        <div class="flex items-center space-x-2">
            <div class="w-8 h-8 rounded-lg bg-brand-500 text-white flex items-center justify-center font-bold">
                <i class="fa-solid fa-leaf text-sm"></i>
            </div>
            <span class="font-bold text-slate-800">ZeroSprechi</span>
        </div>
        <div class="flex items-center space-x-1 text-xs bg-slate-100 px-2 py-1 rounded-full">
            <i class="fa-solid fa-users text-slate-500"></i>
            <span class="font-semibold text-slate-700">M, V, C</span>
        </div>
    </div>

    <!-- MAIN CONTENT AREA -->
    <main class="flex-1 p-4 md:p-8 max-w-7xl mx-auto w-full overflow-y-auto">

        <!-- TAB 1: DISPENSA & FRIGO -->
        <section id="tab-dispensa" class="space-y-6">
            <div class="flex flex-col md:flex-row md:items-center justify-between gap-4">
                <div>
                    <h2 class="text-2xl font-bold text-slate-800">Dispensa & Frigo</h2>
                    <p class="text-slate-500 text-sm">Gestisci gli ingredienti e scopri le scadenze vicine per evitare sprechi.</p>
                </div>

                <div class="flex flex-wrap items-center gap-2">
                    <button onclick="openScanModal('receipt')" class="bg-indigo-600 hover:bg-indigo-700 text-white text-sm font-medium px-4 py-2.5 rounded-xl shadow-sm transition flex items-center space-x-2">
                        <i class="fa-solid fa-receipt"></i>
                        <span>Scansiona Scontrino</span>
                    </button>
                    <button onclick="openScanModal('video')" class="bg-purple-600 hover:bg-purple-700 text-white text-sm font-medium px-4 py-2.5 rounded-xl shadow-sm transition flex items-center space-x-2">
                        <i class="fa-solid fa-video"></i>
                        <span>Scan Video Dispensa</span>
                    </button>
                    <button onclick="openAddPantryModal()" class="bg-brand-600 hover:bg-brand-700 text-white text-sm font-medium px-4 py-2.5 rounded-xl shadow-sm transition flex items-center space-x-2">
                        <i class="fa-solid fa-plus"></i>
                        <span>Aggiungi Manuale</span>
                    </button>
                </div>
            </div>

            <!-- STATS SUMMARY CARDS -->
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                    <div class="text-slate-400 text-xs font-semibold">TOTALE PRODOTTI</div>
                    <div class="text-2xl font-bold text-slate-800 mt-1" id="stat-pantry-total">0</div>
                    <div class="text-xs text-slate-500 mt-1"><i class="fa-solid fa-boxes-stacked"></i> In casa</div>
                </div>
                <div class="bg-red-50 p-4 rounded-2xl border border-red-100 shadow-sm">
                    <div class="text-red-500 text-xs font-semibold">IN SCADENZA (1-2 GG)</div>
                    <div class="text-2xl font-bold text-red-600 mt-1" id="stat-pantry-critical">0</div>
                    <div class="text-xs text-red-500 mt-1"><i class="fa-solid fa-triangle-exclamation"></i> Da consumare subito</div>
                </div>
                <div class="bg-amber-50 p-4 rounded-2xl border border-amber-100 shadow-sm">
                    <div class="text-amber-600 text-xs font-semibold">PROSSIMA SCADENZA (3-5 GG)</div>
                    <div class="text-2xl font-bold text-amber-700 mt-1" id="stat-pantry-warning">0</div>
                    <div class="text-xs text-amber-600 mt-1"><i class="fa-solid fa-clock"></i> Pianifica nei prossimi giorni</div>
                </div>
                <div class="bg-emerald-50 p-4 rounded-2xl border border-emerald-100 shadow-sm">
                    <div class="text-emerald-600 text-xs font-semibold">RISPARMIO STIMATO MESE</div>
                    <div class="text-2xl font-bold text-emerald-700 mt-1">€ 142.50</div>
                    <div class="text-xs text-emerald-600 mt-1"><i class="fa-solid fa-arrow-trend-up"></i> Cibo salvato dallo spreco</div>
                </div>
            </div>

            <!-- FILTER BAR & SEARCH -->
            <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm flex flex-col md:flex-row gap-3 justify-between items-center">
                <div class="relative w-full md:w-80">
                    <i class="fa-solid fa-magnifying-glass absolute left-3 top-1/2 -translate-y-1/2 text-slate-400"></i>
                    <input type="text" id="pantry-search" oninput="renderPantry()" placeholder="Cerca ingrediente..." class="w-full pl-9 pr-4 py-2 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-brand-500">
                </div>
                <div class="flex items-center space-x-2 w-full md:w-auto overflow-x-auto pb-1 md:pb-0">
                    <button onclick="setFilterCategory('all')" class="pantry-cat-btn bg-brand-500 text-white text-xs font-medium px-3 py-1.5 rounded-lg whitespace-nowrap">Tutti</button>
                    <button onclick="setFilterCategory('Proteine')" class="pantry-cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-1.5 rounded-lg whitespace-nowrap">Proteine</button>
                    <button onclick="setFilterCategory('Verdura')" class="pantry-cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-1.5 rounded-lg whitespace-nowrap">Verdura/Frutta</button>
                    <button onclick="setFilterCategory('Latticini')" class="pantry-cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-1.5 rounded-lg whitespace-nowrap">Latticini</button>
                    <button onclick="setFilterCategory('Cereali')" class="pantry-cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-1.5 rounded-lg whitespace-nowrap">Cereali/Pasta</button>
                    <button onclick="setFilterCategory('Prima Infanzia')" class="pantry-cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-1.5 rounded-lg whitespace-nowrap">Bambini</button>
                </div>
            </div>

            <!-- INVENTORY TABLE / GRID -->
            <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
                <div class="overflow-x-auto">
                    <table class="w-full text-left text-sm text-slate-600">
                        <thead class="bg-slate-50 text-slate-500 text-xs uppercase border-b border-slate-200">
                            <tr>
                                <th class="py-3 px-4">Ingrediente</th>
                                <th class="py-3 px-4">Categoria</th>
                                <th class="py-3 px-4">Quantità</th>
                                <th class="py-3 px-4">Scadenza</th>
                                <th class="py-3 px-4">Stato</th>
                                <th class="py-3 px-4 text-right">Azioni</th>
                            </tr>
                        </thead>
                        <tbody id="pantry-table-body" class="divide-y divide-slate-100">
                            <!-- Rendered dynamically -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- TAB 2: RICETTE CONSIGLIATE -->
        <section id="tab-ricette" class="space-y-6 hidden">
            <div>
                <h2 class="text-2xl font-bold text-slate-800">Ricette Consigliate</h2>
                <p class="text-slate-500 text-sm">Create su misura per la disponibilità in dispensa con porzioni calibrate per Martino, Valentina e Camilla.</p>
            </div>

            <!-- RECIPE CARDS GRID -->
            <div id="recipes-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- Rendered dynamically -->
            </div>
        </section>

        <!-- TAB 3: MENU SETTIMANALE -->
        <section id="tab-menu" class="space-y-6 hidden">
            <div class="flex flex-col md:flex-row md:items-center justify-between gap-4">
                <div>
                    <h2 class="text-2xl font-bold text-slate-800">Menu Settimanale Intelligente</h2>
                    <p class="text-slate-500 text-sm">Pianificazione automatica per smaltire le scadenze e bilanciare la dieta familiare.</p>
                </div>
                <button onclick="generateWeeklyMenu()" class="bg-brand-600 hover:bg-brand-700 text-white text-sm font-medium px-4 py-2.5 rounded-xl shadow-sm transition flex items-center space-x-2">
                    <i class="fa-solid fa-arrows-rotate"></i>
                    <span>Rigenera Menu Settimanale</span>
                </button>
            </div>

            <!-- FAMILY PROFILE RECAP BANNER -->
            <div class="bg-gradient-to-r from-emerald-500 via-teal-600 to-indigo-600 rounded-2xl p-4 text-white shadow-md flex flex-wrap justify-between items-center gap-4">
                <div class="flex items-center space-x-3">
                    <div class="p-3 bg-white/20 backdrop-blur-md rounded-xl text-xl">
                        <i class="fa-solid fa-sliders"></i>
                    </div>
                    <div>
                        <h3 class="font-semibold text-base">Regole di Porzionamento Attive</h3>
                        <p class="text-xs text-white/80">I piatti calcolano automaticamente i grammi corretti per ciascun componente.</p>
                    </div>
                </div>
                <div class="flex items-center space-x-4 text-xs font-medium">
                    <div class="bg-white/20 px-3 py-1.5 rounded-lg border border-white/20">
                        👨‍🍳 <b>Martino:</b> Proteine corte, 100-150g tot
                    </div>
                    <div class="bg-white/20 px-3 py-1.5 rounded-lg border border-white/20">
                        👩 <b>Valentina:</b> Ipocalorico high-fiber
                    </div>
                    <div class="bg-white/20 px-3 py-1.5 rounded-lg border border-white/20">
                        👧 <b>Camilla:</b> Porzioni 2.5y, soft/safe
                    </div>
                </div>
            </div>

            <!-- DAYS CONTAINER -->
            <div id="menu-days-container" class="space-y-6">
                <!-- Rendered dynamically -->
            </div>
        </section>

        <!-- TAB 4: LISTA DELLA SPESA -->
        <section id="tab-spesa" class="space-y-6 hidden">
            <div class="flex flex-col md:flex-row md:items-center justify-between gap-4">
                <div>
                    <h2 class="text-2xl font-bold text-slate-800">Lista della Spesa</h2>
                    <p class="text-slate-500 text-sm">Generata automaticamente in base agli ingredienti mancanti per il menu settimanale.</p>
                </div>
                <div class="flex items-center space-x-2">
                    <button onclick="addCustomShoppingItem()" class="bg-slate-800 hover:bg-slate-900 text-white text-sm font-medium px-4 py-2.5 rounded-xl shadow-sm transition flex items-center space-x-2">
                        <i class="fa-solid fa-plus"></i>
                        <span>Aggiungi Articolo</span>
                    </button>
                    <button onclick="transferBoughtToPantry()" class="bg-brand-600 hover:bg-brand-700 text-white text-sm font-medium px-4 py-2.5 rounded-xl shadow-sm transition flex items-center space-x-2">
                        <i class="fa-solid fa-file-import"></i>
                        <span>Sposta Acquistati in Dispensa</span>
                    </button>
                </div>
            </div>

            <div class="bg-white rounded-2xl border border-slate-200 shadow-sm p-6 space-y-4">
                <div class="flex items-center justify-between border-b pb-3">
                    <span class="font-semibold text-slate-700">Articoli da Comprare (<span id="shopping-count">0</span>)</span>
                    <button onclick="clearCompletedShopping()" class="text-xs text-red-500 hover:underline">Rimuovi selezionati</button>
                </div>

                <div id="shopping-list-container" class="space-y-2">
                    <!-- Rendered dynamically -->
                </div>
            </div>
        </section>

        <!-- TAB 5: OFFERTE ESSELUNGA -->
        <section id="tab-esselunga" class="space-y-6 hidden">
            <div class="bg-esselunga text-white p-6 rounded-2xl shadow-md flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
                <div>
                    <div class="flex items-center space-x-2 text-yellow-300 text-xs font-bold uppercase tracking-wider mb-1">
                        <i class="fa-solid fa-bolt"></i> Volantino Smart & Fidaty
                    </div>
                    <h2 class="text-2xl font-bold">Offerte Esselunga Famiglia</h2>
                    <p class="text-blue-100 text-sm mt-1">Selezionate per le esigenze alimentari di Martino, Valentina e Camilla.</p>
                </div>
                <div class="bg-white/10 backdrop-blur-md px-4 py-2 rounded-xl text-xs border border-white/20">
                    📍 Volantino Settimanale Attivo
                </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6" id="esselunga-grid">
                <!-- Rendered dynamically -->
            </div>
        </section>

        <!-- TAB 6: GRAFICI & ANALISI SPESA -->
        <section id="tab-grafici" class="space-y-6 hidden">
            <div>
                <h2 class="text-2xl font-bold text-slate-800">Analisi Spesa & Anti-Spreco</h2>
                <p class="text-slate-500 text-sm">Monitora quanto spendi e quanto cibo stai salvando mese per mese.</p>
            </div>

            <!-- ANALYTICS CARDS -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="font-bold text-slate-700 text-sm">Andamento Spesa (€)</h3>
                        <span class="text-xs bg-slate-100 text-slate-600 px-2 py-1 rounded-md">2026</span>
                    </div>
                    <div class="h-64">
                        <canvas id="chartSpesa"></canvas>
                    </div>
                </div>

                <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="font-bold text-slate-700 text-sm">Ripartizione Categorie Cibo</h3>
                        <span class="text-xs bg-emerald-100 text-emerald-700 px-2 py-1 rounded-md">Bilanciato</span>
                    </div>
                    <div class="h-64 flex items-center justify-center">
                        <canvas id="chartCategorie"></canvas>
                    </div>
                </div>

                <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="font-bold text-slate-700 text-sm">Cibo Salvato dallo Spreco (Kg)</h3>
                        <span class="text-xs bg-emerald-100 text-emerald-700 px-2 py-1 rounded-md">+24% vs Mese Scorso</span>
                    </div>
                    <div class="h-64">
                        <canvas id="chartSpreci"></canvas>
                    </div>
                </div>
            </div>

            <!-- EXPENSE TIME PERIOD BREAKDOWN -->
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
                <h3 class="font-bold text-slate-800 mb-4">Riepilogo Budget Spesa Famiglia</h3>
                <div class="grid grid-cols-2 md:grid-cols-4 gap-4 text-center divide-x divide-slate-100">
                    <div class="p-2">
                        <p class="text-xs text-slate-400 font-medium">GIORNALIERA (MEDIA)</p>
                        <p class="text-xl font-bold text-slate-800 mt-1">€ 18.50</p>
                    </div>
                    <div class="p-2">
                        <p class="text-xs text-slate-400 font-medium">SETTIMANALE</p>
                        <p class="text-xl font-bold text-slate-800 mt-1">€ 129.50</p>
                    </div>
                    <div class="p-2">
                        <p class="text-xs text-slate-400 font-medium">MENSILE</p>
                        <p class="text-xl font-bold text-brand-600 mt-1">€ 518.00</p>
                    </div>
                    <div class="p-2">
                        <p class="text-xs text-slate-400 font-medium">ANNUALE (STIMA)</p>
                        <p class="text-xl font-bold text-indigo-600 mt-1">€ 6.216.00</p>
                    </div>
                </div>
            </div>
        </section>

        <!-- TAB 7: GUIDA GITHUB & AI Gemini -->
        <section id="tab-github" class="space-y-6 hidden">
            <div>
                <h2 class="text-2xl font-bold text-slate-800">Guida Hosting GitHub & Integrazione AI</h2>
                <p class="text-slate-500 text-sm">Istruzioni dettagliate per pubblicare questa app gratis su GitHub Pages e collegare l'AI per lo scanner di scontrini e video.</p>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <!-- STEP 1: GITHUB PAGES -->
                <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm space-y-4">
                    <div class="flex items-center space-x-3 text-brand-600">
                        <i class="fa-brands fa-github text-2xl"></i>
                        <h3 class="font-bold text-lg text-slate-800">1. Come Pubblicare Gratis su GitHub</h3>
                    </div>
                    <ol class="list-decimal list-inside text-sm text-slate-600 space-y-2">
                        <li>Crea un account gratuito su <b>GitHub.com</b> se non ne hai già uno.</li>
                        <li>Crea un nuovo repository pubblico denominato <code>zerosprechi-famiglia</code>.</li>
                        <li>Carica questo file salvandolo con il nome <code>index.html</code> nel repository.</li>
                        <li>Vai nelle impostazioni del repository (<b>Settings</b>) &rarr; <b>Pages</b>.</li>
                        <li>Alla voce <i>Build and deployment / Branch</i>, seleziona <code>main</code> o <code>master</code> e clicca <b>Save</b>.</li>
                        <li>Dopo circa 1 minuto, la tua app sarà attiva online all'indirizzo: <br><code class="bg-slate-100 px-2 py-0.5 rounded text-indigo-600 font-mono text-xs">https://tuousername.github.io/zerosprechi-famiglia</code></li>
                    </ol>
                </div>

                <!-- STEP 2: REAL AI GEMINI INTEGRATION -->
                <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm space-y-4">
                    <div class="flex items-center space-x-3 text-indigo-600">
                        <i class="fa-solid fa-brain text-2xl"></i>
                        <h3 class="font-bold text-lg text-slate-800">2. Integrazione Riconoscimento Video/Scontrini</h3>
                    </div>
                    <p class="text-sm text-slate-600">
                        Questa web app include già i simulatori di visione artificiale. Se vuoi collegare le API di riconoscimento reali (Google Gemini 3.1 Flash Vision), puoi inserire la API Key e il codice invierà la foto dello scontrino o i frame del video direttamente al modello per estrarre la tabella ingredienti in JSON.
                    </p>
                    <div class="p-3 bg-slate-900 text-slate-200 rounded-xl font-mono text-xs overflow-x-auto">
                        <pre>// Esempio Call API Gemini Vision
const response = await fetch('https://generativelanguage.googleapis.com/v1beta/models/gemini-3.1-flash-preview:generateContent?key=' + API_KEY, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    contents: [{
      parts: [
        { text: "Estrai lista ingredienti, quantita e scadenza presunta in formato JSON" },
        { inlineData: { mimeType: "image/jpeg", data: base64Image } }
      ]
    }]
  })
});</pre>
                    </div>
                </div>
            </div>
        </section>
    </main>

    <!-- BOTTOM MOBILE NAVIGATION BAR -->
    <nav class="md:hidden fixed bottom-0 left-0 right-0 bg-white border-t border-slate-200 px-2 py-2 flex justify-around items-center z-40 text-slate-500 text-[10px]">
        <button onclick="switchTab('dispensa')" id="mob-dispensa" class="flex flex-col items-center space-y-0.5 text-brand-600">
            <i class="fa-solid fa-box-archive text-base"></i>
            <span>Dispensa</span>
        </button>
        <button onclick="switchTab('ricette')" id="mob-ricette" class="flex flex-col items-center space-y-0.5">
            <i class="fa-solid fa-utensils text-base"></i>
            <span>Ricette</span>
        </button>
        <button onclick="switchTab('menu')" id="mob-menu" class="flex flex-col items-center space-y-0.5">
            <i class="fa-solid fa-calendar-days text-base"></i>
            <span>Menu</span>
        </button>
        <button onclick="switchTab('spesa')" id="mob-spesa" class="flex flex-col items-center space-y-0.5">
            <i class="fa-solid fa-cart-shopping text-base"></i>
            <span>Spesa</span>
        </button>
        <button onclick="switchTab('esselunga')" id="mob-esselunga" class="flex flex-col items-center space-y-0.5">
            <i class="fa-solid fa-tags text-base"></i>
            <span>Esselunga</span>
        </button>
        <button onclick="switchTab('grafici')" id="mob-grafici" class="flex flex-col items-center space-y-0.5">
            <i class="fa-solid fa-chart-pie text-base"></i>
            <span>Grafici</span>
        </button>
    </nav>

    <!-- MODAL 1: ADD MANUAL PANTRY ITEM -->
    <div id="add-modal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm hidden items-center justify-center p-4 z-50">
        <div class="bg-white rounded-2xl max-w-md w-full p-6 space-y-4 shadow-xl">
            <div class="flex items-center justify-between border-b pb-3">
                <h3 class="font-bold text-slate-800 text-lg">Aggiungi Ingrediente in Dispensa</h3>
                <button onclick="closeAddPantryModal()" class="text-slate-400 hover:text-slate-600"><i class="fa-solid fa-xmark text-lg"></i></button>
            </div>
            <form id="pantry-form" onsubmit="handleAddPantrySubmit(event)" class="space-y-3 text-sm">
                <div>
                    <label class="block text-xs font-semibold text-slate-600 mb-1">Nome Prodotto</label>
                    <input type="text" id="input-name" required placeholder="es. Petto di Pollo" class="w-full px-3 py-2 border border-slate-200 rounded-xl focus:ring-2 focus:ring-brand-500 focus:outline-none">
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">Quantità</label>
                        <input type="number" step="0.1" id="input-qty" required placeholder="es. 500" class="w-full px-3 py-2 border border-slate-200 rounded-xl focus:ring-2 focus:ring-brand-500 focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">Unità</label>
                        <select id="input-unit" class="w-full px-3 py-2 border border-slate-200 rounded-xl focus:ring-2 focus:ring-brand-500 focus:outline-none bg-white">
                            <option value="g">Grammi (g)</option>
                            <option value="kg">Chilogrammi (kg)</option>
                            <option value="ml">Millilitri (ml)</option>
                            <option value="l">Litri (l)</option>
                            <option value="pz">Pezzi / Unità</option>
                        </select>
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">Categoria</label>
                        <select id="input-category" class="w-full px-3 py-2 border border-slate-200 rounded-xl focus:ring-2 focus:ring-brand-500 focus:outline-none bg-white">
                            <option value="Proteine">Proteine</option>
                            <option value="Verdura">Verdura/Frutta</option>
                            <option value="Latticini">Latticini</option>
                            <option value="Cereali">Cereali/Pasta</option>
                            <option value="Prima Infanzia">Prima Infanzia</option>
                            <option value="Altro">Altro</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">Data Scadenza</label>
                        <input type="date" id="input-expiry" required class="w-full px-3 py-2 border border-slate-200 rounded-xl focus:ring-2 focus:ring-brand-500 focus:outline-none">
                    </div>
                </div>
                <div class="pt-3 flex justify-end space-x-2">
                    <button type="button" onclick="closeAddPantryModal()" class="px-4 py-2 text-slate-600 hover:bg-slate-100 rounded-xl">Annulla</button>
                    <button type="submit" class="px-4 py-2 bg-brand-600 text-white font-medium rounded-xl hover:bg-brand-700 shadow-sm">Salva in Dispensa</button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL 2: SCAN RECEIPT & VIDEO SIMULATION -->
    <div id="scan-modal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm hidden items-center justify-center p-4 z-50">
        <div class="bg-white rounded-2xl max-w-lg w-full p-6 space-y-4 shadow-xl">
            <div class="flex items-center justify-between border-b pb-3">
                <h3 class="font-bold text-slate-800 text-lg" id="scan-modal-title">AI Vision Scanner</h3>
                <button onclick="closeScanModal()" class="text-slate-400 hover:text-slate-600"><i class="fa-solid fa-xmark text-lg"></i></button>
            </div>

            <div class="space-y-4">
                <div id="scan-dropzone" class="border-2 border-dashed border-slate-300 rounded-2xl p-6 text-center hover:border-brand-500 transition cursor-pointer bg-slate-50 flex flex-col items-center justify-center space-y-2" onclick="simulateScanProcess()">
                    <div class="w-12 h-12 rounded-full bg-brand-100 text-brand-600 flex items-center justify-center text-xl">
                        <i id="scan-icon" class="fa-solid fa-camera"></i>
                    </div>
                    <div>
                        <p class="font-semibold text-sm text-slate-700" id="scan-instruction">Clicca per simulare lo scanner AI dello scontrino o del video dispensa</p>
                        <p class="text-xs text-slate-400 mt-0.5">Riconoscimento automatico prodotti e scadenze stimate</p>
                    </div>
                </div>

                <!-- PROGRESS BAR SIMULATION -->
                <div id="scan-progress" class="hidden space-y-2">
                    <div class="flex justify-between text-xs font-semibold text-slate-600">
                        <span id="scan-status-text">Analisi visiva in corso...</span>
                        <span id="scan-percent">0%</span>
                    </div>
                    <div class="w-full bg-slate-100 h-2 rounded-full overflow-hidden">
                        <div id="scan-bar" class="bg-brand-500 h-full w-0 transition-all duration-300"></div>
                    </div>
                </div>

                <!-- DETECTED ITEMS PREVIEW -->
                <div id="scan-results" class="hidden space-y-3">
                    <div class="text-xs font-bold text-slate-700 uppercase tracking-wider flex items-center justify-between">
                        <span>Prodotti Rilevati (<span id="detected-count">0</span>)</span>
                        <span class="text-brand-600"><i class="fa-solid fa-circle-check"></i> Pronto all'importazione</span>
                    </div>
                    <div id="detected-list" class="max-h-48 overflow-y-auto space-y-2 pr-1">
                        <!-- Detected list items -->
                    </div>
                    <button onclick="applyDetectedToPantry()" class="w-full py-2.5 bg-brand-600 hover:bg-brand-700 text-white text-sm font-semibold rounded-xl shadow-sm transition">
                        Aggiungi Tutti alla Dispensa
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- JAVASCRIPT LOGIC -->
    <script>
        // ==========================================
        // INITIAL STATE & DUMMY DATA
        // ==========================================
        let pantryItems = [
            { id: 1, name: 'Petto di Tacchino Fresco', qty: 400, unit: 'g', category: 'Proteine', expiry: '2026-10-30' },
            { id: 2, name: 'Zucchine Bio', qty: 3, unit: 'pz', category: 'Verdura', expiry: '2026-10-31' },
            { id: 3, name: 'Yogurt Greco 0%', qty: 4, unit: 'pz', category: 'Latticini', expiry: '2026-11-02' },
            { id: 4, name: 'Ricotta Fresca Vaccina', qty: 250, unit: 'g', category: 'Latticini', expiry: '2026-10-29' },
            { id: 5, name: 'Passata di Pomodoro', qty: 2, unit: 'pz', category: 'Altro', expiry: '2026-12-15' },
            { id: 6, name: 'Omogeneizzato Tacchino Camilla', qty: 3, unit: 'pz', category: 'Prima Infanzia', expiry: '2026-11-20' },
            { id: 7, name: 'Pasta Integrale Penne', qty: 500, unit: 'g', category: 'Cereali', expiry: '2027-02-10' },
            { id: 8, name: 'Filetti di Orata', qty: 300, unit: 'g', category: 'Proteine', expiry: '2026-10-30' },
            { id: 9, name: 'Carote Bio', qty: 500, unit: 'g', category: 'Verdura', expiry: '2026-11-04' },
            { id: 10, name: 'Pastina Risoni Baby', qty: 1, unit: 'pz', category: 'Prima Infanzia', expiry: '2026-12-01' }
        ];

        let shoppingList = [
            { id: 1, name: 'Parmigiano Reggiano 24 mesi', qty: '200g', category: 'Latticini', checked: false },
            { id: 2, name: 'Olio Extravergine d\'Oliva', qty: '1 L', category: 'Altro', checked: false },
            { id: 3, name: 'Biscotti Infanzia Camilla', qty: '1 conf', category: 'Prima Infanzia', checked: true }
        ];

        const recipesData = [
            {
                id: 101,
                title: 'Tacchino alle Erbe con Zucchine Trifolate',
                time: '20 min',
                category: 'Proteine & Verdura',
                match: '100% disponibile in dispensa',
                ingredients: ['Petto di Tacchino Fresco', 'Zucchine Bio', 'Olio Extravergine'],
                portions: {
                    martino: '100g Tacchino + 50g Zucchine ben cotte (alta digeribilità)',
                    valentina: '150g Tacchino + 150g Zucchine + 1 cucchiaino d\'olio',
                    camilla: '60g Tacchino a pezzettini morbidi + 40g Zucchine senza buccia'
                }
            },
            {
                id: 102,
                title: 'Crema di Ricotta con Carote e Pastina per Camilla & Parents',
                time: '15 min',
                category: 'Latticini & Leggerezza',
                match: '90% disponibile',
                ingredients: ['Ricotta Fresca Vaccina', 'Carote Bio', 'Pastina Risoni Baby'],
                portions: {
                    martino: '80g Ricotta + purea di carote (molto idonea per sleeve)',
                    valentina: '100g Ricotta + 200g Carote a vapore + 30g Pastina',
                    camilla: '40g Ricotta + 30g Pastina risoni + purea carota'
                }
            },
            {
                id: 103,
                title: 'Filetto di Orata al Cartoccio con Pomodorini',
                time: '25 min',
                category: 'Pesce Magro',
                match: '100% disponibile',
                ingredients: ['Filetti di Orata', 'Passata di Pomodoro'],
                portions: {
                    martino: '110g Filetto di Orata (ottima fonte proteica magra)',
                    valentina: '180g Filetto di Orata + insalata fresca di contorno',
                    camilla: '70g Filetto spinato minuziosamente e sfilacciato'
                }
            }
        ];

        const esselungaOffersData = [
            {
                id: 201,
                name: 'Petto di Pollo Biologico Naturama 400g',
                oldPrice: '6.90',
                newPrice: '4.49',
                discount: '-35%',
                tag: 'Proteina Magra Martino & Vale',
                suitable: 'Martino & Valentina'
            },
            {
                id: 202,
                name: 'Yogurt Greco High Protein 0% Scaglione 150g',
                oldPrice: '1.49',
                newPrice: '0.99',
                discount: '-33%',
                tag: 'Proteine & Dimagrimento',
                suitable: 'Martino & Valentina'
            },
            {
                id: 203,
                name: 'Pappa Biologica Omogeneizzati Frutta Mellin 4x100g',
                oldPrice: '3.20',
                newPrice: '2.10',
                discount: '-34%',
                tag: 'Prima Infanzia Camilla',
                suitable: 'Camilla'
            },
            {
                id: 204,
                name: 'Zucchine e Carote Bio Busta 500g Esselunga Bio',
                oldPrice: '2.30',
                newPrice: '1.50',
                discount: '-35%',
                tag: 'Verdure di stagione',
                suitable: 'Tutta la famiglia'
            }
        ];

        let activeCategoryFilter = 'all';

        // ==========================================
        // APP INITIALIZATION & NAVIGATION
        // ==========================================
        window.onload = function() {
            renderPantry();
            renderRecipes();
            renderWeeklyMenu();
            renderShoppingList();
            renderEsselungaOffers();
            initCharts();
        };

        function switchTab(tabId) {
            const tabs = ['dispensa', 'ricette', 'menu', 'spesa', 'esselunga', 'grafici', 'github'];
            tabs.forEach(t => {
                const el = document.getElementById(`tab-${t}`);
                const navBtn = document.getElementById(`nav-${t}`);
                const mobBtn = document.getElementById(`mob-${t}`);

                if (t === tabId) {
                    el.classList.remove('hidden');
                    if (navBtn) navBtn.classList.add('active');
                    if (mobBtn) mobBtn.classList.add('text-brand-600');
                } else {
                    el.classList.add('hidden');
                    if (navBtn) navBtn.classList.remove('active');
                    if (mobBtn) mobBtn.classList.remove('text-brand-600');
                }
            });
        }

        // ==========================================
        // DISPENSA & FRIGO MANAGEMENT
        // ==========================================
        function getExpiryBadge(dateStr) {
            const today = new Date('2026-10-28');
            const exp = new Date(dateStr);
            const diffDays = Math.ceil((exp - today) / (1000 * 60 * 60 * 24));

            if (diffDays <= 2) {
                return { badgeClass: 'bg-red-100 text-red-700 border-red-200', text: `In scadenza (${diffDays <= 0 ? 'Oggi' : diffDays + ' gg'})`, type: 'critical' };
            } else if (diffDays <= 5) {
                return { badgeClass: 'bg-amber-100 text-amber-800 border-amber-200', text: `Scade tra ${diffDays} gg`, type: 'warning' };
            } else {
                return { badgeClass: 'bg-emerald-100 text-emerald-800 border-emerald-200', text: `Fresco (${diffDays} gg)`, type: 'ok' };
            }
        }

        function setFilterCategory(cat) {
            activeCategoryFilter = cat;
            document.querySelectorAll('.pantry-cat-btn').forEach(btn => {
                if (btn.innerText.includes(cat) || (cat === 'all' && btn.innerText.includes('Tutti'))) {
                    btn.className = 'pantry-cat-btn bg-brand-500 text-white text-xs font-medium px-3 py-1.5 rounded-lg whitespace-nowrap';
                } else {
                    btn.className = 'pantry-cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-1.5 rounded-lg whitespace-nowrap';
                }
            });
            renderPantry();
        }

        function renderPantry() {
            const tbody = document.getElementById('pantry-table-body');
            const searchVal = document.getElementById('pantry-search').value.toLowerCase();
            tbody.innerHTML = '';

            let totalCount = 0;
            let criticalCount = 0;
            let warningCount = 0;

            const filtered = pantryItems.filter(item => {
                const matchesSearch = item.name.toLowerCase().includes(searchVal);
                const matchesCat = activeCategoryFilter === 'all' || item.category === activeCategoryFilter;
                return matchesSearch && matchesCat;
            });

            pantryItems.forEach(item => {
                totalCount++;
                const status = getExpiryBadge(item.expiry);
                if (status.type === 'critical') criticalCount++;
                if (status.type === 'warning') warningCount++;
            });

            document.getElementById('stat-pantry-total').innerText = totalCount;
            document.getElementById('stat-pantry-critical').innerText = criticalCount;
            document.getElementById('stat-pantry-warning').innerText = warningCount;

            if (filtered.length === 0) {
                tbody.innerHTML = `<tr><td colspan="6" class="text-center py-8 text-slate-400">Nessun ingrediente trovato in dispensa.</td></tr>`;
                return;
            }

            filtered.forEach(item => {
                const status = getExpiryBadge(item.expiry);
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-50 transition border-b border-slate-100';
                tr.innerHTML = `
                    <td class="py-3 px-4 font-semibold text-slate-800">${item.name}</td>
                    <td class="py-3 px-4"><span class="bg-slate-100 text-slate-600 px-2.5 py-1 rounded-md text-xs font-medium">${item.category}</span></td>
                    <td class="py-3 px-4 font-medium">${item.qty} ${item.unit}</td>
                    <td class="py-3 px-4 text-xs font-mono">${item.expiry}</td>
                    <td class="py-3 px-4">
                        <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-semibold border ${status.badgeClass}">
                            ${status.text}
                        </span>
                    </td>
                    <td class="py-3 px-4 text-right space-x-1">
                        <button onclick="consumeItem(${item.id})" title="Consuma / Usato" class="p-1.5 text-slate-400 hover:text-brand-600 rounded-lg hover:bg-slate-100"><i class="fa-solid fa-check"></i></button>
                        <button onclick="deletePantryItem(${item.id})" title="Elimina" class="p-1.5 text-slate-400 hover:text-red-600 rounded-lg hover:bg-slate-100"><i class="fa-solid fa-trash-can"></i></button>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function consumeItem(id) {
            pantryItems = pantryItems.filter(i => i.id !== id);
            renderPantry();
        }

        function deletePantryItem(id) {
            pantryItems = pantryItems.filter(i => i.id !== id);
            renderPantry();
        }

        function openAddPantryModal() {
            document.getElementById('add-modal').classList.remove('hidden');
            document.getElementById('add-modal').classList.add('flex');
        }

        function closeAddPantryModal() {
            document.getElementById('add-modal').classList.add('hidden');
            document.getElementById('add-modal').classList.remove('flex');
        }

        function handleAddPantrySubmit(e) {
            e.preventDefault();
            const newItem = {
                id: Date.now(),
                name: document.getElementById('input-name').value,
                qty: parseFloat(document.getElementById('input-qty').value),
                unit: document.getElementById('input-unit').value,
                category: document.getElementById('input-category').value,
                expiry: document.getElementById('input-expiry').value
            };
            pantryItems.push(newItem);
            closeAddPantryModal();
            renderPantry();
            document.getElementById('pantry-form').reset();
        }

        // ==========================================
        // SCANNER SIMULATION (RECEIPT & VIDEO)
        // ==========================================
        let currentScanMode = 'receipt';
        let detectedTempItems = [];

        function openScanModal(mode) {
            currentScanMode = mode;
            const modal = document.getElementById('scan-modal');
            const title = document.getElementById('scan-modal-title');
            const icon = document.getElementById('scan-icon');
            const instruction = document.getElementById('scan-instruction');

            document.getElementById('scan-progress').classList.add('hidden');
            document.getElementById('scan-results').classList.add('hidden');
            document.getElementById('scan-dropzone').classList.remove('hidden');

            if (mode === 'receipt') {
                title.innerText = 'AI Receipt OCR Scanner';
                icon.className = 'fa-solid fa-receipt';
                instruction.innerText = 'Carica o fotografa lo scontrino della spesa Esselunga/Supermercato';
            } else {
                title.innerText = 'AI Video Computer Vision Dispensa';
                icon.className = 'fa-solid fa-video';
                instruction.innerText = 'Avvia video inquadrando gli scaffali della dispensa e il frigo';
            }

            modal.classList.remove('hidden');
            modal.classList.add('flex');
        }

        function closeScanModal() {
            document.getElementById('scan-modal').classList.add('hidden');
            document.getElementById('scan-modal').classList.remove('flex');
        }

        function simulateScanProcess() {
            document.getElementById('scan-dropzone').classList.add('hidden');
            document.getElementById('scan-progress').classList.remove('hidden');

            let progress = 0;
            const bar = document.getElementById('scan-bar');
            const percent = document.getElementById('scan-percent');
            const statusText = document.getElementById('scan-status-text');

            const interval = setInterval(() => {
                progress += 20;
                bar.style.width = `${progress}%`;
                percent.innerText = `${progress}%`;

                if (progress === 40) statusText.innerText = currentScanMode === 'receipt' ? 'Estrazione testo e importi OCR...' : 'Analisi frame e segmentazione cibo...';
                if (progress === 80) statusText.innerText = 'Stima date di scadenza e categoria...';

                if (progress >= 100) {
                    clearInterval(interval);
                    showScanResults();
                }
            }, 300);
        }

        function showScanResults() {
            document.getElementById('scan-progress').classList.add('hidden');
            document.getElementById('scan-results').classList.remove('hidden');

            if (currentScanMode === 'receipt') {
                detectedTempItems = [
                    { id: Date.now() + 1, name: 'Bresaola della Valtellina 100g', qty: 1, unit: 'pz', category: 'Proteine', expiry: '2026-11-10' },
                    { id: Date.now() + 2, name: 'Mozzarella Light 3x125g', qty: 1, unit: 'pz', category: 'Latticini', expiry: '2026-11-05' },
                    { id: Date.now() + 3, name: 'Finocchi Freschi Bio 1kg', qty: 1, unit: 'kg', category: 'Verdura', expiry: '2026-11-03' }
                ];
            } else {
                detectedTempItems = [
                    { id: Date.now() + 1, name: 'Uova Fresche Biologiche', qty: 6, unit: 'pz', category: 'Proteine', expiry: '2026-11-12' },
                    { id: Date.now() + 2, name: 'Latte Intero Alta Qualità', qty: 1, unit: 'l', category: 'Latticini', expiry: '2026-11-01' }
                ];
            }

            document.getElementById('detected-count').innerText = detectedTempItems.length;
            const listEl = document.getElementById('detected-list');
            listEl.innerHTML = '';

            detectedTempItems.forEach(item => {
                const div = document.createElement('div');
                div.className = 'flex items-center justify-between p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs';
                div.innerHTML = `
                    <div>
                        <span class="font-bold text-slate-800">${item.name}</span>
                        <div class="text-slate-400">${item.category} • Exp: ${item.expiry}</div>
                    </div>
                    <span class="font-medium bg-brand-100 text-brand-700 px-2 py-1 rounded-md">+${item.qty} ${item.unit}</span>
                `;
                listEl.appendChild(div);
            });
        }

        function applyDetectedToPantry() {
            pantryItems.push(...detectedTempItems);
            closeScanModal();
            renderPantry();
        }

        // ==========================================
        // RICETTE CONSIGLIATE
        // ==========================================
        function renderRecipes() {
            const grid = document.getElementById('recipes-grid');
            grid.innerHTML = '';

            recipesData.forEach(recipe => {
                const card = document.createElement('div');
                card.className = 'bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden flex flex-col justify-between hover:shadow-md transition';
                card.innerHTML = `
                    <div class="p-5 space-y-4">
                        <div class="flex items-center justify-between">
                            <span class="text-xs font-semibold px-2.5 py-1 rounded-md bg-indigo-50 text-indigo-700">${recipe.category}</span>
                            <span class="text-xs text-slate-400 font-medium"><i class="fa-regular fa-clock"></i> ${recipe.time}</span>
                        </div>
                        <h3 class="font-bold text-slate-800 text-lg leading-snug">${recipe.title}</h3>
                        <div class="text-xs bg-emerald-50 text-emerald-700 font-semibold px-2.5 py-1 rounded-lg inline-block">
                            <i class="fa-solid fa-circle-check"></i> ${recipe.match}
                        </div>

                        <div class="border-t border-slate-100 pt-3 space-y-2">
                            <div class="text-xs font-bold text-slate-700 uppercase tracking-wider">Porzioni Calibrate Famiglia:</div>

                            <!-- MARTINO PORTION -->
                            <div class="p-2.5 bg-emerald-50/60 rounded-xl text-xs border border-emerald-100">
                                <span class="font-bold text-emerald-800">👨‍🍳 Martino (Sleeve 100-150g):</span>
                                <p class="text-slate-600 mt-0.5">${recipe.portions.martino}</p>
                            </div>

                            <!-- VALENTINA PORTION -->
                            <div class="p-2.5 bg-blue-50/60 rounded-xl text-xs border border-blue-100">
                                <span class="font-bold text-blue-800">👩 Valentina (Dimagrimento):</span>
                                <p class="text-slate-600 mt-0.5">${recipe.portions.valentina}</p>
                            </div>

                            <!-- CAMILLA PORTION -->
                            <div class="p-2.5 bg-amber-50/60 rounded-xl text-xs border border-amber-100">
                                <span class="font-bold text-amber-800">👧 Camilla (2.5 Anni):</span>
                                <p class="text-slate-600 mt-0.5">${recipe.portions.camilla}</p>
                            </div>
                        </div>
                    </div>
                    <div class="bg-slate-50 p-4 border-t border-slate-100">
                        <button onclick="cookRecipe('${recipe.title}')" class="w-full py-2 bg-brand-600 hover:bg-brand-700 text-white font-medium text-xs rounded-xl transition shadow-sm">
                            <i class="fa-solid fa-fire-burner"></i> Cucina e Scala Ingrediente
                        </button>
                    </div>
                `;
                grid.appendChild(card);
            });
        }

        function cookRecipe(title) {
            alert(`Piatto "${title}" cucinato con successo! Ingredienti aggiornati in dispensa.`);
        }

        // ==========================================
        // MENU SETTIMANALE
        // ==========================================
        const days = ['Lunedì', 'Martedì', 'Mercoledì', 'Giovedì', 'Venerdì', 'Sabato', 'Domenica'];

        function generateWeeklyMenu() {
            renderWeeklyMenu();
        }

        function renderWeeklyMenu() {
            const container = document.getElementById('menu-days-container');
            container.innerHTML = '';

            days.forEach((day, idx) => {
                const dayCard = document.createElement('div');
                dayCard.className = 'bg-white rounded-2xl border border-slate-200 shadow-sm p-5 space-y-4';
                dayCard.innerHTML = `
                    <div class="flex items-center justify-between border-b pb-2">
                        <h3 class="font-bold text-slate-800 text-base">${day}</h3>
                        <span class="text-xs bg-brand-50 text-brand-700 px-2 py-0.5 rounded font-semibold">Priority Scadenze Brevi</span>
                    </div>

                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-xs">
                        <!-- PRANZO -->
                        <div class="p-3 bg-slate-50 rounded-xl border border-slate-200/80 space-y-2">
                            <div class="flex justify-between items-center text-slate-500 font-bold uppercase tracking-wider text-[10px]">
                                <span><i class="fa-solid fa-sun text-amber-500"></i> Pranzo</span>
                                <button class="text-slate-400 hover:text-brand-600"><i class="fa-solid fa-rotate text-xs"></i></button>
                            </div>
                            <div class="font-semibold text-slate-800 text-sm">
                                ${idx % 2 === 0 ? 'Tacchino alle Erbe con Zucchine' : 'Filetto di Orata al Cartoccio'}
                            </div>
                            <div class="text-slate-500 space-y-1 text-[11px]">
                                <div>• <b>Martino:</b> 100g proteina + verdura cotta</div>
                                <div>• <b>Valentina:</b> 150g proteina + abbondante contorno</div>
                                <div>• <b>Camilla:</b> Bocconcini morbidi senza spine/buccia</div>
                            </div>
                        </div>

                        <!-- CENA -->
                        <div class="p-3 bg-slate-50 rounded-xl border border-slate-200/80 space-y-2">
                            <div class="flex justify-between items-center text-slate-500 font-bold uppercase tracking-wider text-[10px]">
                                <span><i class="fa-solid fa-moon text-indigo-500"></i> Cena</span>
                                <button class="text-slate-400 hover:text-brand-600"><i class="fa-solid fa-rotate text-xs"></i></button>
                            </div>
                            <div class="font-semibold text-slate-800 text-sm">
                                ${idx % 2 === 0 ? 'Crema di Ricotta con Carote e Pastina' : 'Vellutata di Verdure e Fiocchi di Latte'}
                            </div>
                            <div class="text-slate-500 space-y-1 text-[11px]">
                                <div>• <b>Martino:</b> Ricotta/Passato leggero idoneo sleeve</div>
                                <div>• <b>Valentina:</b> Vellutata high-fiber + proteina magra</div>
                                <div>• <b>Camilla:</b> Pastina risoni ben cotta con crema carota</div>
                            </div>
                        </div>
                    </div>
                `;
                container.appendChild(dayCard);
            });
        }

        // ==========================================
        // LISTA DELLA SPESA
        // ==========================================
        function renderShoppingList() {
            const container = document.getElementById('shopping-list-container');
            container.innerHTML = '';

            document.getElementById('shopping-count').innerText = shoppingList.length;

            if (shoppingList.length === 0) {
                container.innerHTML = `<p class="text-slate-400 text-sm text-center py-4">Nessun elemento presente nella lista della spesa.</p>`;
                return;
            }

            shoppingList.forEach(item => {
                const div = document.createElement('div');
                div.className = `flex items-center justify-between p-3 rounded-xl border transition ${item.checked ? 'bg-slate-50 border-slate-200 text-slate-400 line-through' : 'bg-white border-slate-200 text-slate-800'}`;
                div.innerHTML = `
                    <div class="flex items-center space-x-3">
                        <input type="checkbox" ${item.checked ? 'checked' : ''} onchange="toggleShoppingItem(${item.id})" class="w-4 h-4 text-brand-600 rounded focus:ring-brand-500">
                        <span class="font-medium text-sm">${item.name} (${item.qty})</span>
                        <span class="text-[10px] bg-slate-100 text-slate-500 px-2 py-0.5 rounded">${item.category}</span>
                    </div>
                    <button onclick="removeShoppingItem(${item.id})" class="text-slate-300 hover:text-red-500"><i class="fa-solid fa-xmark"></i></button>
                `;
                container.appendChild(div);
            });
        }

        function toggleShoppingItem(id) {
            shoppingList = shoppingList.map(i => i.id === id ? { ...i, checked: !i.checked } : i);
            renderShoppingList();
        }

        function removeShoppingItem(id) {
            shoppingList = shoppingList.filter(i => i.id !== id);
            renderShoppingList();
        }

        function addCustomShoppingItem() {
            const name = prompt('Inserisci il nome del prodotto da aggiungere alla lista spesa:');
            if (name) {
                shoppingList.push({ id: Date.now(), name, qty: '1 conf', category: 'Altro', checked: false });
                renderShoppingList();
            }
        }

        function clearCompletedShopping() {
            shoppingList = shoppingList.filter(i => !i.checked);
            renderShoppingList();
        }

        function transferBoughtToPantry() {
            const bought = shoppingList.filter(i => i.checked);
            if (bought.length === 0) {
                alert('Seleziona almeno un articolo acquistato (spuntato) per trasferirlo in dispensa.');
                return;
            }

            bought.forEach(item => {
                pantryItems.push({
                    id: Date.now() + Math.random(),
                    name: item.name,
                    qty: 1,
                    unit: 'pz',
                    category: item.category,
                    expiry: '2026-11-15'
                });
            });

            shoppingList = shoppingList.filter(i => !i.checked);
            renderShoppingList();
            renderPantry();
            alert(`${bought.length} articoli aggiunti automaticamente alla dispensa!`);
        }

        // ==========================================
        // OFFERTE ESSELUNGA
        // ==========================================
        function renderEsselungaOffers() {
            const grid = document.getElementById('esselunga-grid');
            grid.innerHTML = '';

            esselungaOffersData.forEach(offer => {
                const card = document.createElement('div');
                card.className = 'bg-white rounded-2xl border border-slate-200 shadow-sm p-5 space-y-3 flex flex-col justify-between hover:border-blue-300 transition';
                card.innerHTML = `
                    <div class="space-y-2">
                        <div class="flex justify-between items-start">
                            <span class="text-xs font-bold px-2 py-0.5 rounded bg-blue-100 text-esselunga">${offer.tag}</span>
                            <span class="text-xs font-bold text-red-600 bg-red-50 px-2 py-0.5 rounded">${offer.discount}</span>
                        </div>
                        <h4 class="font-bold text-slate-800 text-base leading-snug">${offer.name}</h4>
                        <div class="text-xs text-slate-500">Consigliato per: <b>${offer.suitable}</b></div>
                        <div class="flex items-baseline space-x-2 pt-2">
                            <span class="text-xl font-extrabold text-slate-900">€ ${offer.newPrice}</span>
                            <span class="text-xs text-slate-400 line-through">€ ${offer.oldPrice}</span>
                        </div>
                    </div>
                    <button onclick="addOfferToShopping('${offer.name}')" class="w-full py-2 bg-esselunga hover:bg-blue-900 text-white text-xs font-medium rounded-xl transition shadow-sm flex items-center justify-center space-x-2">
                        <i class="fa-solid fa-cart-plus"></i>
                        <span>Aggiungi alla Lista Spesa</span>
                    </button>
                `;
                grid.appendChild(card);
            });
        }

        function addOfferToShopping(name) {
            shoppingList.push({ id: Date.now(), name, qty: '1 conf', category: 'Altro', checked: false });
            alert(`"${name}" aggiunto alla tua lista della spesa!`);
            renderShoppingList();
        }

        // ==========================================
        // CHARTS & ANALYTICS
        // ==========================================
        function initCharts() {
            // Chart 1: Expense
            const ctxSpesa = document.getElementById('chartSpesa').getContext('2d');
            new Chart(ctxSpesa, {
                type: 'line',
                data: {
                    labels: ['Lug', 'Ago', 'Set', 'Ott'],
                    datasets: [{
                        label: 'Spesa Mensile (€)',
                        data: [580, 520, 490, 518],
                        borderColor: '#22c55e',
                        backgroundColor: 'rgba(34, 197, 94, 0.1)',
                        fill: true,
                        tension: 0.3
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false }
            });

            // Chart 2: Category Pie
            const ctxCat = document.getElementById('chartCategorie').getContext('2d');
            new Chart(ctxCat, {
                type: 'doughnut',
                data: {
                    labels: ['Proteine', 'Verdura/Frutta', 'Latticini', 'Infanzia'],
                    datasets: [{
                        data: [40, 30, 15, 15],
                        backgroundColor: ['#22c55e', '#3b82f6', '#f59e0b', '#ec4899']
                    }]
