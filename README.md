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
        .loading-spinner { border: 3px solid #f3f3f3; border-top: 3px solid #10b981; border-radius: 50%; width: 28px; height: 28px; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
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

        <!-- Loading Overlay -->
        <div id="loadingOverlay" class="hidden fixed inset-0 bg-black/70 z-50 flex flex-col items-center justify-center text-white space-y-3 p-4 text-center">
            <div class="loading-spinner"></div>
            <p id="loadingText" class="text-sm font-semibold">Analisi con Intelligenza Artificiale in corso...</p>
            <div id="previewContainer" class="mt-2 max-w-[200px] max-h-[200px] overflow-hidden rounded-lg border-2 border-white shadow-lg hidden">
                <img id="imagePreview" src="" class="w-full h-full object-cover">
            </div>
        </div>

        <!-- TAB 1: DISPENSA & FRIGO -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100 space-y-3">
                <h2 class="text-base font-bold text-gray-800 flex items-center gap-2">
                    <i class="fa-solid fa-camera text-emerald-600"></i> Scansione Fotocamera / Video
                </h2>
                
                <p class="text-xs text-gray-500">Tocca un pulsante per aprire direttamente la fotocamera o la videocamera del tuo telefono:</p>

                <!-- Form con Input Fotocamera nativi e visibili (Compatibili iOS/Android) -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-2.5">
                    
                    <!-- Fotocamera Scontrino -->
                    <label class="bg-emerald-50 text-emerald-800 border border-emerald-200 p-3 rounded-xl text-xs font-bold flex items-center justify-between cursor-pointer active:scale-95 transition">
                        <div class="flex items-center gap-2.5">
                            <i class="fa-solid fa-receipt text-xl text-emerald-600"></i>
                            <div class="text-left">
                                <div class="font-bold">Fotocamera Scontrino</div>
                                <div class="text-[10px] text-emerald-600 font-normal">Scatta foto scontrino</div>
                            </div>
                        </div>
                        <input type="file" accept="image/*" capture="environment" class="opacity-0 w-0 h-0" onchange="handleFileSelect(event, 'scontrino')">
                    </label>

                    <!-- Fotocamera Dispensa/Frigo -->
                    <label class="bg-blue-50 text-blue-800 border border-blue-200 p-3 rounded-xl text-xs font-bold flex items-center justify-between cursor-pointer active:scale-95 transition">
                        <div class="flex items-center gap-2.5">
                            <i class="fa-solid fa-camera text-xl text-blue-600"></i>
