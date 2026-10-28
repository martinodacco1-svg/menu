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
        .loading-spinner { border: 3px solid #f3f3f3; border-top: 3px solid #10b981; border-radius: 50%; width: 24px; height: 24px; animation: spin 1s linear infinite; }
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

        <!-- INPUT MULTIMEDIALI SEPARATI (C Capturing nativo per Fotocamera e Videocamera) -->
        <input type="file" id="cameraReceiptInput" accept="image/*" capture="environment" class="hidden" onchange="processMedia(event, 'scontrino')">
        <input type="file" id="cameraPhotoInput" accept="image/*" capture="environment" class="hidden" onchange="processMedia(event, 'dispensa_foto')">
        <input type="file" id="cameraVideoInput" accept="video/*" capture="environment" class="hidden" onchange="processMedia(event, 'dispensa_video')">
        <input type="file" id="galleryInput" accept="image/*,video/*" class="hidden" onchange="processMedia(event, 'galleria')">

        <!-- Loading Overlay -->
        <div id="loadingOverlay" class="hidden fixed inset-0 bg-black/60 z-50 flex flex-col items-center justify-center text-white space-y-3">
            <div class="loading-spinner"></div>
            <p id="loadingText" class="text-sm font-semibold">Analisi con Intelligenza Artificiale in corso...</p>
        </div>

        <!-- TAB 1: DISPENSA & FRIGO -->
        <section id="tab-dispensa" class="tab-content active space-y-4">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                <h2 class="text-base font-bold text-gray-800 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-camera text-emerald-600"></i> Scansione Automatica (Fotocamera / Video)
                </h2>
                
                <!-- Pulsanti separati per Fotocamera, Video e Galleria -->
                <div class="grid grid-cols-2 gap-2 mb-3">
                    <button onclick="document.getElementById('cameraReceiptInput').click()" class="bg-emerald-50 text-emerald-800 border border-emerald-200 p-3 rounded-xl text-xs font-bold flex flex-col items-center gap-1.5 hover:bg-emerald-100 active:scale-95 transition">
                        <i class="fa-solid fa-receipt text-lg text-emerald-600"></i>
                        <span>Fotocamera Scontrino</span>
                    </button>
                    <button onclick="document.getElementById('cameraPhotoInput').click()" class="bg-blue-50 text-blue-800 border border-blue-200 p-3 rounded-xl text-xs font-bold flex flex-col items-center gap-1.5 hover:bg-blue-100 active:scale-95 transition">
                        <i class="fa-solid fa-camera-retro text-lg text-blue-600"></i>
                        <span>Fotocamera Dispensa/Frigo</span>
                    </button>
                </div>

                <div class="grid grid-cols-2 gap-2 mb-3">
                    <button onclick="document.getElementById('cameraVideoInput').click()" class="bg-purple-50 text-purple-800 border border-purple-200 p-3 rounded-xl text-xs font-bold flex flex-col items-center gap-1.5 hover:bg-purple-100 active:scale-95 transition">
                        <i class="fa-solid fa-video text-lg text-purple-600"></i>
                        <span>Registra Video Dispensa</span>
                    </button>
                    <button onclick="document.getElementById('galleryInput').click()" class="bg-gray-100 text-gray-700 border border-gray-200 p-3 rounded-xl text-xs font-bold flex flex-col items-center gap-1.5 hover:bg-gray-200 active:scale-95 transition">
                        <i class="fa-solid fa-images text-lg text-gray-600"></i>
                        <span>Carica da Galleria</span>
                    </button>
                </div>

                <!-- Inserimento Manuale -->
                <details class="text-xs text-gray-600 pt-2 border-t">
                    <summary class="font-semibold cursor-pointer py-1 text-emerald-600">+ Inserisci manualmente</summary>
                    <form
