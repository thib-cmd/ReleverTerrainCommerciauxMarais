[app.js](https://github.com/user-attachments/files/24573084/app.js)

// Application de Relevés de Chantier
// ===============================================[index.html](https://github.com/user-attachments/files/24573087/index.html)<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Relevés de Chantier - MARAIS</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Leaflet CSS -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    
    <!-- Font Awesome -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.4.0/css/all.min.css">
    
    <!-- jsPDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    
    <!-- Signature Pad -->
    <script src="https://cdn.jsdelivr.net/npm/signature_pad@4.1.7/dist/signature_pad.umd.min.js"></script>
    
    <style>
        .map-container {
            height: 300px;
            width: 100%;
            border-radius: 0.5rem;
        }
        
        #signature-canvas {
            border: 2px dashed #cbd5e1;
            border-radius: 0.5rem;
            touch-action: none;
        }
        
        .nav-btn.active {
            background-color: #3b82f6;
            color: white;
        }
        
        .photo-preview {
            position: relative;
            display: inline-block;
        }
        
        .photo-preview img {
            max-width: 100px;
            max-height: 100px;
            object-fit: cover;
            border-radius: 0.5rem;
        }
        
        .photo-preview .remove-photo {
            position: absolute;
            top: -8px;
            right: -8px;
            background-color: #ef4444;
            color: white;
            border-radius: 50%;
            width: 24px;
            height: 24px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            font-size: 12px;
        }
        
        /* PWA-like styling */
        body {
            max-width: 768px;
            margin: 0 auto;
            background-color: #f8fafc;
        }
    </style>
</head>
<body class="bg-gray-50">
    <!-- Header -->
    <header class="bg-blue-600 text-white p-4 sticky top-0 z-50 shadow-lg">
        <div class="flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <img src="images/logo-marais.png" alt="MARAIS - TESMEC GROUP COMPANY" class="h-10">
            </div>
            <button id="menu-btn" class="text-2xl">
                <i class="fas fa-bars"></i>
            </button>
        </div>
    </header>

    <!-- Navigation -->
    <nav class="bg-white shadow-md sticky top-16 z-40">
        <div class="flex justify-around p-2">
            <button class="nav-btn active flex-1 py-3 px-4 text-center transition-all duration-200" data-page="form">
                <i class="fas fa-plus-circle mb-1"></i>
                <div class="text-xs">Nouveau</div>
            </button>
            <button class="nav-btn flex-1 py-3 px-4 text-center transition-all duration-200" data-page="list">
                <i class="fas fa-list mb-1"></i>
                <div class="text-xs">Liste</div>
            </button>
            <button class="nav-btn flex-1 py-3 px-4 text-center transition-all duration-200" data-page="search">
                <i class="fas fa-search mb-1"></i>
                <div class="text-xs">Recherche</div>
            </button>
        </div>
    </nav>

    <!-- Main Content -->
    <main class="p-4 pb-20">
        <!-- Formulaire de relevé -->
        <div id="form-page" class="page-content">
            <div class="bg-white rounded-lg shadow-md p-6 mb-4">
                <h2 class="text-2xl font-bold text-gray-800 mb-6 flex items-center">
                    <i class="fas fa-clipboard-list mr-3 text-blue-600"></i>
                    Nouveau Relevé
                </h2>
                
                <form id="releve-form" class="space-y-6">
                    <!-- Informations générales -->
                    <div class="space-y-4">
                        <h3 class="text-lg font-semibold text-gray-700 border-b pb-2">
                            <i class="fas fa-info-circle mr-2 text-blue-500"></i>
                            Informations Générales
                        </h3>
                        
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-user mr-2"></i>Nom du Client *
                            </label>
                            <input type="text" id="client-name" required
                                   class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                   placeholder="Ex: Entreprise Martin">
                        </div>

                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-map-marker-alt mr-2"></i>Lieu du Chantier *
                            </label>
                            <input type="text" id="location" required
                                   class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                   placeholder="Ex: 25 Rue de Paris, 75001 Paris">
                            <button type="button" id="get-location-btn" 
                                    class="mt-2 w-full bg-green-500 text-white py-2 px-4 rounded-lg hover:bg-green-600 transition-colors">
                                <i class="fas fa-crosshairs mr-2"></i>Utiliser ma position
                            </button>
                        </div>

                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-cogs mr-2"></i>Type de Machine *
                            </label>
                            <select id="machine-type" required
                                    class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                                <option value="">Sélectionner...</option>
                                <option value="Roue déportée">Roue déportée</option>
                                <option value="Roue axiale">Roue axiale</option>
                                <option value="Chaîne">Chaîne</option>
                                <option value="Extra déport">Extra déport</option>
                                <option value="City clean">City clean</option>
                                <option value="Fast clean">Fast clean</option>
                                <option value="Fast green">Fast green</option>
                                <option value="Pose">Pose</option>
                                <option value="Multicut">Multicut</option>
                                <option value="Multicut gaz">Multicut gaz</option>
                                <option value="Roue axiale extra déport">Roue axiale extra déport</option>
                            </select>
                        </div>

                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-network-wired mr-2"></i>Type de Réseau *
                            </label>
                            <select id="network-type" required
                                    class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                                <option value="">Sélectionner...</option>
                                <option value="Électrique">Électrique</option>
                                <option value="Gaz">Gaz</option>
                                <option value="Eau">Eau</option>
                                <option value="Télécom">Télécom</option>
                                <option value="Assainissement">Assainissement</option>
                                <option value="Fibre optique">Fibre optique</option>
                                <option value="Mixte">Mixte</option>
                                <option value="Autre">Autre</option>
                            </select>
                        </div>

                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-circle mr-2"></i>Diamètre du Réseau
                            </label>
                            <input type="text" id="network-diameter"
                                   class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                   placeholder="Ex: 100mm, 200mm, etc.">
                        </div>

                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-calendar-alt mr-2"></i>Date et Heure
                            </label>
                            <input type="datetime-local" id="date-time" 
                                   class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                        </div>
                    </div>

                    <!-- Dimensions de tranchée -->
                    <div class="space-y-4">
                        <h3 class="text-lg font-semibold text-gray-700 border-b pb-2">
                            <i class="fas fa-ruler mr-2 text-blue-500"></i>
                            Dimensions de Tranchée
                        </h3>
                        
                        <div class="grid grid-cols-3 gap-4">
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-2">
                                    <i class="fas fa-arrows-alt-h mr-2"></i>Longueur (m)
                                </label>
                                <input type="number" id="length" step="0.01" min="0"
                                       class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                       placeholder="Ex: 50">
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-2">
                                    <i class="fas fa-arrows-alt-v mr-2"></i>Profondeur (m)
                                </label>
                                <input type="number" id="depth" step="0.01" min="0"
                                       class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                       placeholder="Ex: 1.5">
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-2">
                                    <i class="fas fa-arrows-alt mr-2"></i>Largeur (m)
                                </label>
                                <input type="number" id="width" step="0.01" min="0"
                                       class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                       placeholder="Ex: 0.8">
                            </div>
                        </div>
                    </div>

                    <!-- Type de terrain -->
                    <div class="space-y-4">
                        <h3 class="text-lg font-semibold text-gray-700 border-b pb-2">
                            <i class="fas fa-mountain mr-2 text-blue-500"></i>
                            Type de Terrain
                        </h3>
                        
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-layer-group mr-2"></i>Nature du Terrain
                            </label>
                            <input type="text" id="terrain-type"
                                   class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                   placeholder="Ex: Argile, Sable, Roche, etc.">
                        </div>

                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-hammer mr-2"></i>Dureté du Terrain *
                            </label>
                            <select id="terrain-hardness" required
                                    class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                                <option value="">Sélectionner...</option>
                                <option value="Tendre">Tendre</option>
                                <option value="Moyen">Moyen</option>
                                <option value="Dur">Dur</option>
                            </select>
                        </div>
                    </div>

                    <!-- Cadence et linéage -->
                    <div class="space-y-4">
                        <h3 class="text-lg font-semibold text-gray-700 border-b pb-2">
                            <i class="fas fa-tachometer-alt mr-2 text-blue-500"></i>
                            Performance Estimée
                        </h3>
                        
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-clock mr-2"></i>Cadence Estimée
                            </label>
                            <input type="text" id="estimated-cadence"
                                   class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                   placeholder="Ex: 50m/jour, 10m/h, etc.">
                        </div>

                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-exchange-alt mr-2"></i>Linéage Transfert
                            </label>
                            <input type="text" id="lineage-transfer"
                                   class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                   placeholder="Ex: 200m, 500m, etc.">
                        </div>
                    </div>

                    <!-- Description du chantier -->
                    <div class="space-y-4">
                        <h3 class="text-lg font-semibold text-gray-700 border-b pb-2">
                            <i class="fas fa-file-alt mr-2 text-blue-500"></i>
                            Description du Chantier
                        </h3>
                        
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                Descriptif Détaillé *
                            </label>
                            <textarea id="description" required rows="5"
                                      class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                      placeholder="Décrivez en détail le chantier, les travaux à effectuer, les contraintes particulières..."></textarea>
                        </div>
                    </div>

                    <!-- Localisation GPS -->
                    <div class="space-y-4">
                        <h3 class="text-lg font-semibold text-gray-700 border-b pb-2">
                            <i class="fas fa-map mr-2 text-blue-500"></i>
                            Localisation GPS
                        </h3>
                        
                        <div>
                            <button type="button" id="get-location-btn-2" 
                                    class="w-full bg-green-500 text-white py-2 px-4 rounded-lg hover:bg-green-600 transition-colors mb-3">
                                <i class="fas fa-crosshairs mr-2"></i>Localiser ma position
                            </button>
                            
                            <label class="block text-sm font-medium text-gray-700 mb-2">
                                <i class="fas fa-search mr-2"></i>Rechercher une adresse
                            </label>
                            <div class="relative">
                                <input type="text" id="address-search" 
                                       class="w-full px-4 py-2 pr-12 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                       placeholder="Entrez une adresse...">
                                <button type="button" id="search-address-btn"
                                        class="absolute right-2 top-2 bg-blue-500 text-white px-3 py-1 rounded hover:bg-blue-600">
                                    <i class="fas fa-search"></i>
                                </button>
                            </div>
                        </div>
                        
                        <div id="map" class="map-container shadow-lg"></div>
                        
                        <div class="grid grid-cols-2 gap-4">
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-2">Latitude</label>
                                <input type="text" id="latitude" readonly
                                       class="w-full px-4 py-2 border border-gray-300 rounded-lg bg-gray-50">
                            </div>
                            <div>
                                <label class="block text-sm font-medium text-gray-700 mb-2">Longitude</label>
                                <input type="text" id="longitude" readonly
                                       class="w-full px-4 py-2 border border-gray-300 rounded-lg bg-gray-50">
                            </div>
                        </div>
                    </div>

                    <!-- Photos -->
                    <div class="space-y-4">
                        <h3 class="text-lg font-semibold text-gray-700 border-b pb-2">
                            <i class="fas fa-camera mr-2 text-blue-500"></i>
                            Photos du Chantier
                        </h3>
                        
                        <div>
                            <label class="block mb-2">
                                <span class="w-full inline-block bg-blue-500 text-white py-3 px-4 rounded-lg hover:bg-blue-600 transition-colors text-center cursor-pointer">
                                    <i class="fas fa-upload mr-2"></i>Ajouter des Photos
                                </span>
                                <input type="file" id="photos" multiple accept="image/*" class="hidden">
                            </label>
                            <div id="photo-preview" class="flex flex-wrap gap-3 mt-3"></div>
                        </div>
                    </div>

                    <!-- Signature -->
                    <div class="space-y-4">
                        <h3 class="text-lg font-semibold text-gray-700 border-b pb-2">
                            <i class="fas fa-signature mr-2 text-blue-500"></i>
                            Signature
                        </h3>
                        
                        <div class="bg-gray-50 p-4 rounded-lg">
                            <canvas id="signature-canvas" width="300" height="150" class="w-full"></canvas>
                            <button type="button" id="clear-signature-btn"
                                    class="mt-2 w-full bg-gray-500 text-white py-2 px-4 rounded-lg hover:bg-gray-600 transition-colors">
                                <i class="fas fa-eraser mr-2"></i>Effacer la Signature
                            </button>
                        </div>
                    </div>

                    <!-- Boutons d'action -->
                    <div class="flex space-x-3 pt-4">
                        <button type="submit"
                                class="flex-1 bg-blue-600 text-white py-3 px-6 rounded-lg hover:bg-blue-700 transition-colors font-semibold shadow-lg">
                            <i class="fas fa-save mr-2"></i>Enregistrer
                        </button>
                        <button type="button" id="reset-form-btn"
                                class="flex-1 bg-gray-500 text-white py-3 px-6 rounded-lg hover:bg-gray-600 transition-colors font-semibold">
                            <i class="fas fa-redo mr-2"></i>Réinitialiser
                        </button>
                    </div>
                </form>
            </div>
        </div>

        <!-- Liste des relevés -->
        <div id="list-page" class="page-content hidden">
            <div class="bg-white rounded-lg shadow-md p-6 mb-4">
                <h2 class="text-2xl font-bold text-gray-800 mb-6 flex items-center justify-between">
                    <span>
                        <i class="fas fa-list mr-3 text-blue-600"></i>
                        Mes Relevés
                    </span>
                    <span id="releve-count" class="text-sm bg-blue-100 text-blue-800 px-3 py-1 rounded-full">0</span>
                </h2>
                
                <div id="releve-list" class="space-y-3">
                    <!-- Les relevés seront ajoutés dynamiquement ici -->
                </div>
            </div>
        </div>

        <!-- Recherche -->
        <div id="search-page" class="page-content hidden">
            <div class="bg-white rounded-lg shadow-md p-6 mb-4">
                <h2 class="text-2xl font-bold text-gray-800 mb-6 flex items-center">
                    <i class="fas fa-search mr-3 text-blue-600"></i>
                    Rechercher
                </h2>
                
                <div class="space-y-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">Recherche par mot-clé</label>
                        <input type="text" id="search-input"
                               class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                               placeholder="Client, lieu, machine, réseau...">
                    </div>
                    
                    <div class="grid grid-cols-2 gap-4">
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">Type de Machine</label>
                            <select id="search-machine"
                                    class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                                <option value="">Tous</option>
                                <option value="Roue déportée">Roue déportée</option>
                                <option value="Roue axiale">Roue axiale</option>
                                <option value="Chaîne">Chaîne</option>
                                <option value="Extra déport">Extra déport</option>
                                <option value="City clean">City clean</option>
                                <option value="Fast clean">Fast clean</option>
                                <option value="Fast green">Fast green</option>
                                <option value="Pose">Pose</option>
                                <option value="Multicut">Multicut</option>
                                <option value="Multicut gaz">Multicut gaz</option>
                                <option value="Roue axiale extra déport">Roue axiale extra déport</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-2">Type de Réseau</label>
                            <select id="search-network"
                                    class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                                <option value="">Tous</option>
                                <option value="Électrique">Électrique</option>
                                <option value="Gaz">Gaz</option>
                                <option value="Eau">Eau</option>
                                <option value="Télécom">Télécom</option>
                                <option value="Assainissement">Assainissement</option>
                                <option value="Fibre optique">Fibre optique</option>
                                <option value="Mixte">Mixte</option>
                                <option value="Autre">Autre</option>
                            </select>
                        </div>
                    </div>
                    
                    <button id="search-btn"
                            class="w-full bg-blue-600 text-white py-3 px-6 rounded-lg hover:bg-blue-700 transition-colors font-semibold">
                        <i class="fas fa-search mr-2"></i>Rechercher
                    </button>
                </div>
                
                <div id="search-results" class="mt-6 space-y-3">
                    <!-- Les résultats seront ajoutés ici -->
                </div>
            </div>
        </div>
    </main>

    <!-- Modal de détails -->
    <div id="detail-modal" class="hidden fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center p-4">
        <div class="bg-white rounded-lg max-w-2xl w-full max-h-[90vh] overflow-y-auto">
            <div class="p-6">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-2xl font-bold text-gray-800">Détails du Relevé</h2>
                    <button id="close-modal-btn" class="text-gray-500 hover:text-gray-700">
                        <i class="fas fa-times text-2xl"></i>
                    </button>
                </div>
                <div id="modal-content"></div>
            </div>
        </div>
    </div>

    <!-- Toast notification -->
    <div id="toast" class="hidden fixed bottom-4 left-4 right-4 bg-green-500 text-white px-6 py-4 rounded-lg shadow-lg z-50 max-w-md mx-auto">
        <div class="flex items-center">
            <i class="fas fa-check-circle text-2xl mr-3"></i>
            <span id="toast-message"></span>
        </div>
    </div>

    <!-- Leaflet JS -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    
    <!-- Main App JS -->
    <script src="js/app.js"></script>
</body>
</html>



// Variables globales
let map;
let marker;
let signaturePad;
let currentPhotos = [];

// Initialisation de l'application
document.addEventListener('DOMContentLoaded', function() {
    initApp();
});

function initApp() {
    // Initialiser la carte
    initMap();
    
    // Initialiser le signature pad
    initSignaturePad();
    
    // Initialiser la date/heure actuelle
    setCurrentDateTime();
    
    // Initialiser les événements
    initEventListeners();
    
    // Charger la liste des relevés
    loadReleves();
}

// ===============================================
// CARTE INTERACTIVE
// ===============================================

function initMap() {
    // Coordonnées par défaut (Paris)
    const defaultLat = 48.8566;
    const defaultLng = 2.3522;
    
    // Créer la carte
    map = L.map('map').setView([defaultLat, defaultLng], 13);
    
    // Ajouter le layer OpenStreetMap
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
        maxZoom: 19
    }).addTo(map);
    
    // Créer le marqueur draggable
    marker = L.marker([defaultLat, defaultLng], {
        draggable: true
    }).addTo(map);
    
    // Mettre à jour les coordonnées quand on déplace le marqueur
    marker.on('dragend', function(e) {
        const position = marker.getLatLng();
        updateCoordinates(position.lat, position.lng);
    });
    
    // Permettre de placer le marqueur en cliquant sur la carte
    map.on('click', function(e) {
        marker.setLatLng(e.latlng);
        updateCoordinates(e.latlng.lat, e.latlng.lng);
    });
    
    // Mettre à jour les coordonnées initiales
    updateCoordinates(defaultLat, defaultLng);
}

function updateCoordinates(lat, lng) {
    document.getElementById('latitude').value = lat.toFixed(6);
    document.getElementById('longitude').value = lng.toFixed(6);
}

// ===============================================
// GÉOLOCALISATION
// ===============================================

function getUserLocation() {
    if ('geolocation' in navigator) {
        const btn = document.getElementById('get-location-btn');
        btn.innerHTML = '<i class="fas fa-spinner fa-spin mr-2"></i>Localisation...';
        btn.disabled = true;
        
        navigator.geolocation.getCurrentPosition(
            function(position) {
                const lat = position.coords.latitude;
                const lng = position.coords.longitude;
                
                // Centrer la carte et déplacer le marqueur
                map.setView([lat, lng], 15);
                marker.setLatLng([lat, lng]);
                updateCoordinates(lat, lng);
                
                // Géocodage inverse pour obtenir l'adresse
                reverseGeocode(lat, lng);
                
                btn.innerHTML = '<i class="fas fa-check mr-2"></i>Position récupérée';
                setTimeout(() => {
                    btn.innerHTML = '<i class="fas fa-crosshairs mr-2"></i>Utiliser ma position';
                    btn.disabled = false;
                }, 2000);
            },
            function(error) {
                console.error('Erreur de géolocalisation:', error);
                showToast('Impossible d\'obtenir votre position', 'error');
                btn.innerHTML = '<i class="fas fa-crosshairs mr-2"></i>Utiliser ma position';
                btn.disabled = false;
            }
        );
    } else {
        showToast('Géolocalisation non disponible', 'error');
    }
}

// Géocodage inverse avec Nominatim (OpenStreetMap)
function reverseGeocode(lat, lng) {
    fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lng}`)
        .then(response => response.json())
        .then(data => {
            if (data.display_name) {
                document.getElementById('location').value = data.display_name;
            }
        })
        .catch(error => console.error('Erreur de géocodage:', error));
}

// Rechercher une adresse et la localiser sur la carte
function searchAddress() {
    const address = document.getElementById('address-search').value;
    
    if (!address) {
        showToast('Veuillez entrer une adresse', 'error');
        return;
    }
    
    const searchBtn = document.getElementById('search-address-btn');
    searchBtn.innerHTML = '<i class="fas fa-spinner fa-spin"></i>';
    searchBtn.disabled = true;
    
    fetch(`https://nominatim.openstreetmap.org/search?format=json&q=${encodeURIComponent(address)}`)
        .then(response => response.json())
        .then(data => {
            if (data && data.length > 0) {
                const result = data[0];
                const lat = parseFloat(result.lat);
                const lng = parseFloat(result.lon);
                
                // Centrer la carte et déplacer le marqueur
                map.setView([lat, lng], 15);
                marker.setLatLng([lat, lng]);
                updateCoordinates(lat, lng);
                
                // Mettre à jour le champ location
                document.getElementById('location').value = result.display_name;
                
                showToast('Adresse localisée avec succès', 'success');
            } else {
                showToast('Adresse non trouvée', 'error');
            }
        })
        .catch(error => {
            console.error('Erreur de recherche:', error);
            showToast('Erreur lors de la recherche', 'error');
        })
        .finally(() => {
            searchBtn.innerHTML = '<i class="fas fa-search"></i>';
            searchBtn.disabled = false;
        });
}

// ===============================================
// SIGNATURE ÉLECTRONIQUE
// ===============================================

function initSignaturePad() {
    const canvas = document.getElementById('signature-canvas');
    
    // Ajuster la taille du canvas
    function resizeCanvas() {
        const ratio = Math.max(window.devicePixelRatio || 1, 1);
        canvas.width = canvas.offsetWidth * ratio;
        canvas.height = canvas.offsetHeight * ratio;
        canvas.getContext('2d').scale(ratio, ratio);
    }
    
    window.addEventListener('resize', resizeCanvas);
    resizeCanvas();
    
    signaturePad = new SignaturePad(canvas, {
        backgroundColor: 'rgb(255, 255, 255)',
        penColor: 'rgb(0, 0, 0)'
    });
}

// ===============================================
// GESTION DES PHOTOS
// ===============================================

function handlePhotoUpload(event) {
    const files = event.target.files;
    
    for (let i = 0; i < files.length; i++) {
        const file = files[i];
        
        if (file.type.startsWith('image/')) {
            const reader = new FileReader();
            
            reader.onload = function(e) {
                currentPhotos.push(e.target.result);
                displayPhotoPreview(e.target.result, currentPhotos.length - 1);
            };
            
            reader.readAsDataURL(file);
        }
    }
    
    // Réinitialiser l'input pour permettre de sélectionner les mêmes fichiers
    event.target.value = '';
}

function displayPhotoPreview(photoData, index) {
    const previewContainer = document.getElementById('photo-preview');
    
    const photoDiv = document.createElement('div');
    photoDiv.className = 'photo-preview';
    photoDiv.innerHTML = `
        <img src="${photoData}" alt="Photo ${index + 1}">
        <div class="remove-photo" onclick="removePhoto(${index})">
            <i class="fas fa-times"></i>
        </div>
    `;
    
    previewContainer.appendChild(photoDiv);
}

function removePhoto(index) {
    currentPhotos.splice(index, 1);
    redrawPhotoPreview();
}

function redrawPhotoPreview() {
    const previewContainer = document.getElementById('photo-preview');
    previewContainer.innerHTML = '';
    
    currentPhotos.forEach((photo, index) => {
        displayPhotoPreview(photo, index);
    });
}

// ===============================================
// GESTION DU FORMULAIRE
// ===============================================

function setCurrentDateTime() {
    const now = new Date();
    const datetime = now.toISOString().slice(0, 16);
    document.getElementById('date-time').value = datetime;
}

async function handleFormSubmit(event) {
    event.preventDefault();
    
    // Validation
    if (signaturePad.isEmpty()) {
        showToast('Veuillez signer le relevé', 'error');
        return;
    }
    
    // Récupérer les données du formulaire
    const formData = {
        clientName: document.getElementById('client-name').value,
        location: document.getElementById('location').value,
        machineType: document.getElementById('machine-type').value,
        networkType: document.getElementById('network-type').value,
        networkDiameter: document.getElementById('network-diameter').value,
        length: parseFloat(document.getElementById('length').value) || 0,
        depth: parseFloat(document.getElementById('depth').value) || 0,
        width: parseFloat(document.getElementById('width').value) || 0,
        terrainType: document.getElementById('terrain-type').value,
        terrainHardness: document.getElementById('terrain-hardness').value,
        estimatedCadence: document.getElementById('estimated-cadence').value,
        lineageTransfer: document.getElementById('lineage-transfer').value,
        description: document.getElementById('description').value,
        dateTime: new Date(document.getElementById('date-time').value).getTime(),
        latitude: parseFloat(document.getElementById('latitude').value),
        longitude: parseFloat(document.getElementById('longitude').value),
        photos: currentPhotos,
        signature: signaturePad.toDataURL()
    };
    
    try {
        // Enregistrer dans la base de données via l'API
        const response = await fetch('tables/releves', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(formData)
        });
        
        if (response.ok) {
            const data = await response.json();
            showToast('Relevé enregistré avec succès !', 'success');
            
            // Réinitialiser le formulaire
            resetForm();
            
            // Recharger la liste
            loadReleves();
            
            // Passer à l'onglet liste
            setTimeout(() => {
                showPage('list');
            }, 1500);
        } else {
            throw new Error('Erreur lors de l\'enregistrement');
        }
    } catch (error) {
        console.error('Erreur:', error);
        showToast('Erreur lors de l\'enregistrement', 'error');
    }
}

function resetForm() {
    document.getElementById('releve-form').reset();
    signaturePad.clear();
    currentPhotos = [];
    document.getElementById('photo-preview').innerHTML = '';
    setCurrentDateTime();
    
    // Réinitialiser la carte à Paris
    const defaultLat = 48.8566;
    const defaultLng = 2.3522;
    map.setView([defaultLat, defaultLng], 13);
    marker.setLatLng([defaultLat, defaultLng]);
    updateCoordinates(defaultLat, defaultLng);
}

// ===============================================
// GESTION DES RELEVÉS
// ===============================================

async function loadReleves() {
    try {
        const response = await fetch('tables/releves?limit=100&sort=-created_at');
        if (response.ok) {
            const result = await response.json();
            displayReleves(result.data);
            document.getElementById('releve-count').textContent = result.total;
        }
    } catch (error) {
        console.error('Erreur lors du chargement des relevés:', error);
    }
}

function displayReleves(releves) {
    const listContainer = document.getElementById('releve-list');
    
    if (releves.length === 0) {
        listContainer.innerHTML = `
            <div class="text-center py-12 text-gray-500">
                <i class="fas fa-inbox text-6xl mb-4"></i>
                <p class="text-lg">Aucun relevé enregistré</p>
                <p class="text-sm mt-2">Commencez par créer un nouveau relevé</p>
            </div>
        `;
        return;
    }
    
    listContainer.innerHTML = releves.map(releve => `
        <div class="bg-gray-50 p-4 rounded-lg border border-gray-200 hover:shadow-md transition-shadow cursor-pointer" 
             onclick="viewReleveDetails('${releve.id}')">
            <div class="flex justify-between items-start mb-2">
                <div class="flex-1">
                    <h3 class="font-bold text-lg text-gray-800">
                        <i class="fas fa-user mr-2 text-blue-500"></i>${releve.clientName}
                    </h3>
                    <p class="text-sm text-gray-600 mt-1">
                        <i class="fas fa-map-marker-alt mr-2"></i>${releve.location}
                    </p>
                </div>
                <div class="flex space-x-2">
                    <button onclick="event.stopPropagation(); generatePDF('${releve.id}')" 
                            class="bg-red-500 text-white px-3 py-1 rounded hover:bg-red-600 text-sm">
                        <i class="fas fa-file-pdf"></i>
                    </button>
                    <button onclick="event.stopPropagation(); deleteReleve('${releve.id}')" 
                            class="bg-gray-500 text-white px-3 py-1 rounded hover:bg-gray-600 text-sm">
                        <i class="fas fa-trash"></i>
                    </button>
                </div>
            </div>
            
            <div class="grid grid-cols-2 gap-2 text-sm mt-3">
                <div class="text-gray-600">
                    <i class="fas fa-cogs mr-1"></i>${releve.machineType}
                </div>
                <div class="text-gray-600">
                    <i class="fas fa-network-wired mr-1"></i>${releve.networkType}
                </div>
            </div>
            
            <div class="text-xs text-gray-500 mt-3 flex justify-between items-center">
                <span>
                    <i class="fas fa-calendar mr-1"></i>${formatDateTime(releve.dateTime)}
                </span>
                ${releve.photos && releve.photos.length > 0 ? `
                    <span><i class="fas fa-camera mr-1"></i>${releve.photos.length} photo(s)</span>
                ` : ''}
            </div>
        </div>
    `).join('');
}

async function viewReleveDetails(id) {
    try {
        const response = await fetch(`tables/releves/${id}`);
        if (response.ok) {
            const releve = await response.json();
            showReleveModal(releve);
        }
    } catch (error) {
        console.error('Erreur:', error);
        showToast('Erreur lors du chargement des détails', 'error');
    }
}

function showReleveModal(releve) {
    const modalContent = document.getElementById('modal-content');
    
    modalContent.innerHTML = `
        <div class="space-y-6">
            <!-- Informations générales -->
            <div>
                <h3 class="text-lg font-semibold text-gray-700 border-b pb-2 mb-3">
                    <i class="fas fa-info-circle mr-2 text-blue-500"></i>Informations Générales
                </h3>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="text-sm font-medium text-gray-600">Client</label>
                        <p class="text-gray-800">${releve.clientName}</p>
                    </div>
                    <div>
                        <label class="text-sm font-medium text-gray-600">Date</label>
                        <p class="text-gray-800">${formatDateTime(releve.dateTime)}</p>
                    </div>
                    <div>
                        <label class="text-sm font-medium text-gray-600">Machine</label>
                        <p class="text-gray-800">${releve.machineType}</p>
                    </div>
                    <div>
                        <label class="text-sm font-medium text-gray-600">Réseau</label>
                        <p class="text-gray-800">${releve.networkType}</p>
                    </div>
                    ${releve.networkDiameter ? `
                    <div>
                        <label class="text-sm font-medium text-gray-600">Diamètre Réseau</label>
                        <p class="text-gray-800">${releve.networkDiameter}</p>
                    </div>
                    ` : ''}
                </div>
            </div>
            
            <!-- Dimensions de tranchée -->
            ${releve.length || releve.depth || releve.width ? `
            <div>
                <h3 class="text-lg font-semibold text-gray-700 border-b pb-2 mb-3">
                    <i class="fas fa-ruler mr-2 text-blue-500"></i>Dimensions de Tranchée
                </h3>
                <div class="grid grid-cols-3 gap-4">
                    ${releve.length ? `
                    <div>
                        <label class="text-sm font-medium text-gray-600">Longueur</label>
                        <p class="text-gray-800">${releve.length} m</p>
                    </div>
                    ` : ''}
                    ${releve.depth ? `
                    <div>
                        <label class="text-sm font-medium text-gray-600">Profondeur</label>
                        <p class="text-gray-800">${releve.depth} m</p>
                    </div>
                    ` : ''}
                    ${releve.width ? `
                    <div>
                        <label class="text-sm font-medium text-gray-600">Largeur</label>
                        <p class="text-gray-800">${releve.width} m</p>
                    </div>
                    ` : ''}
                </div>
            </div>
            ` : ''}
            
            <!-- Type de terrain -->
            ${releve.terrainType || releve.terrainHardness ? `
            <div>
                <h3 class="text-lg font-semibold text-gray-700 border-b pb-2 mb-3">
                    <i class="fas fa-mountain mr-2 text-blue-500"></i>Type de Terrain
                </h3>
                <div class="grid grid-cols-2 gap-4">
                    ${releve.terrainType ? `
                    <div>
                        <label class="text-sm font-medium text-gray-600">Nature du Terrain</label>
                        <p class="text-gray-800">${releve.terrainType}</p>
                    </div>
                    ` : ''}
                    ${releve.terrainHardness ? `
                    <div>
                        <label class="text-sm font-medium text-gray-600">Dureté</label>
                        <p class="text-gray-800">${releve.terrainHardness}</p>
                    </div>
                    ` : ''}
                </div>
            </div>
            ` : ''}
            
            <!-- Performance estimée -->
            ${releve.estimatedCadence || releve.lineageTransfer ? `
            <div>
                <h3 class="text-lg font-semibold text-gray-700 border-b pb-2 mb-3">
                    <i class="fas fa-tachometer-alt mr-2 text-blue-500"></i>Performance Estimée
                </h3>
                <div class="grid grid-cols-2 gap-4">
                    ${releve.estimatedCadence ? `
                    <div>
                        <label class="text-sm font-medium text-gray-600">Cadence Estimée</label>
                        <p class="text-gray-800">${releve.estimatedCadence}</p>
                    </div>
                    ` : ''}
                    ${releve.lineageTransfer ? `
                    <div>
                        <label class="text-sm font-medium text-gray-600">Linéage Transfert</label>
                        <p class="text-gray-800">${releve.lineageTransfer}</p>
                    </div>
                    ` : ''}
                </div>
            </div>
            ` : ''}
            
            <!-- Localisation -->
            <div>
                <h3 class="text-lg font-semibold text-gray-700 border-b pb-2 mb-3">
                    <i class="fas fa-map-marker-alt mr-2 text-blue-500"></i>Localisation
                </h3>
                <p class="text-gray-800 mb-2">${releve.location}</p>
                <div class="grid grid-cols-2 gap-4 text-sm">
                    <div>
                        <label class="text-sm font-medium text-gray-600">Latitude</label>
                        <p class="text-gray-800">${releve.latitude.toFixed(6)}</p>
                    </div>
                    <div>
                        <label class="text-sm font-medium text-gray-600">Longitude</label>
                        <p class="text-gray-800">${releve.longitude.toFixed(6)}</p>
                    </div>
                </div>
                <div id="detail-map" class="map-container mt-3"></div>
            </div>
            
            <!-- Description -->
            <div>
                <h3 class="text-lg font-semibold text-gray-700 border-b pb-2 mb-3">
                    <i class="fas fa-file-alt mr-2 text-blue-500"></i>Description
                </h3>
                <p class="text-gray-800 whitespace-pre-wrap">${releve.description}</p>
            </div>
            
            <!-- Photos -->
            ${releve.photos && releve.photos.length > 0 ? `
                <div>
                    <h3 class="text-lg font-semibold text-gray-700 border-b pb-2 mb-3">
                        <i class="fas fa-camera mr-2 text-blue-500"></i>Photos (${releve.photos.length})
                    </h3>
                    <div class="grid grid-cols-2 gap-3">
                        ${releve.photos.map((photo, index) => `
                            <img src="${photo}" alt="Photo ${index + 1}" 
                                 class="w-full h-48 object-cover rounded-lg cursor-pointer hover:opacity-80"
                                 onclick="window.open('${photo}', '_blank')">
                        `).join('')}
                    </div>
                </div>
            ` : ''}
            
            <!-- Signature -->
            <div>
                <h3 class="text-lg font-semibold text-gray-700 border-b pb-2 mb-3">
                    <i class="fas fa-signature mr-2 text-blue-500"></i>Signature
                </h3>
                <img src="${releve.signature}" alt="Signature" class="border rounded-lg max-w-xs">
            </div>
            
            <!-- Boutons d'action -->
            <div class="flex space-x-3 pt-4">
                <button onclick="generatePDF('${releve.id}')" 
                        class="flex-1 bg-red-600 text-white py-3 px-6 rounded-lg hover:bg-red-700 transition-colors font-semibold">
                    <i class="fas fa-file-pdf mr-2"></i>Générer PDF
                </button>
                <button onclick="closeModal()" 
                        class="flex-1 bg-gray-500 text-white py-3 px-6 rounded-lg hover:bg-gray-600 transition-colors font-semibold">
                    <i class="fas fa-times mr-2"></i>Fermer
                </button>
            </div>
        </div>
    `;
    
    // Afficher le modal
    document.getElementById('detail-modal').classList.remove('hidden');
    
    // Initialiser la mini carte après un court délai
    setTimeout(() => {
        const detailMap = L.map('detail-map').setView([releve.latitude, releve.longitude], 15);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; OpenStreetMap contributors'
        }).addTo(detailMap);
        L.marker([releve.latitude, releve.longitude]).addTo(detailMap);
    }, 100);
}

function closeModal() {
    document.getElementById('detail-modal').classList.add('hidden');
}

async function deleteReleve(id) {
    if (!confirm('Êtes-vous sûr de vouloir supprimer ce relevé ?')) {
        return;
    }
    
    try {
        const response = await fetch(`tables/releves/${id}`, {
            method: 'DELETE'
        });
        
        if (response.ok) {
            showToast('Relevé supprimé avec succès', 'success');
            loadReleves();
        }
    } catch (error) {
        console.error('Erreur:', error);
        showToast('Erreur lors de la suppression', 'error');
    }
}

// ===============================================
// GÉNÉRATION PDF
// ===============================================

async function generatePDF(id) {
    try {
        const response = await fetch(`tables/releves/${id}`);
        if (!response.ok) {
            throw new Error('Erreur lors du chargement des données');
        }
        
        const releve = await response.json();
        
        showToast('Génération du PDF en cours...', 'info');
        
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF();
        
        let yPos = 20;
        
        // Titre
        doc.setFontSize(20);
        doc.setTextColor(59, 130, 246);
        doc.text('FICHE DE PRÉPARATION', 105, yPos, { align: 'center' });
        yPos += 10;
        
        doc.setFontSize(16);
        doc.text('RELEVÉ DE CHANTIER', 105, yPos, { align: 'center' });
        yPos += 15;
        
        // Ligne de séparation
        doc.setDrawColor(59, 130, 246);
        doc.setLineWidth(0.5);
        doc.line(20, yPos, 190, yPos);
        yPos += 10;
        
        // Informations générales
        doc.setFontSize(14);
        doc.setTextColor(0, 0, 0);
        doc.setFont(undefined, 'bold');
        doc.text('INFORMATIONS GÉNÉRALES', 20, yPos);
        yPos += 8;
        
        doc.setFontSize(11);
        doc.setFont(undefined, 'normal');
        
        doc.setFont(undefined, 'bold');
        doc.text('Client:', 20, yPos);
        doc.setFont(undefined, 'normal');
        doc.text(releve.clientName, 50, yPos);
        yPos += 7;
        
        doc.setFont(undefined, 'bold');
        doc.text('Date:', 20, yPos);
        doc.setFont(undefined, 'normal');
        doc.text(formatDateTime(releve.dateTime), 50, yPos);
        yPos += 7;
        
        doc.setFont(undefined, 'bold');
        doc.text('Type de machine:', 20, yPos);
        doc.setFont(undefined, 'normal');
        doc.text(releve.machineType, 50, yPos);
        yPos += 7;
        
        doc.setFont(undefined, 'bold');
        doc.text('Type de réseau:', 20, yPos);
        doc.setFont(undefined, 'normal');
        doc.text(releve.networkType, 50, yPos);
        yPos += 7;
        
        if (releve.networkDiameter) {
            doc.setFont(undefined, 'bold');
            doc.text('Diamètre réseau:', 20, yPos);
            doc.setFont(undefined, 'normal');
            doc.text(releve.networkDiameter, 50, yPos);
            yPos += 7;
        }
        yPos += 5;
        
        // Dimensions de tranchée
        if (releve.length || releve.depth || releve.width) {
            doc.setFontSize(14);
            doc.setFont(undefined, 'bold');
            doc.text('DIMENSIONS DE TRANCHÉE', 20, yPos);
            yPos += 8;
            
            doc.setFontSize(11);
            doc.setFont(undefined, 'normal');
            
            if (releve.length) {
                doc.setFont(undefined, 'bold');
                doc.text('Longueur:', 20, yPos);
                doc.setFont(undefined, 'normal');
                doc.text(`${releve.length} m`, 50, yPos);
                yPos += 7;
            }
            
            if (releve.depth) {
                doc.setFont(undefined, 'bold');
                doc.text('Profondeur:', 20, yPos);
                doc.setFont(undefined, 'normal');
                doc.text(`${releve.depth} m`, 50, yPos);
                yPos += 7;
            }
            
            if (releve.width) {
                doc.setFont(undefined, 'bold');
                doc.text('Largeur:', 20, yPos);
                doc.setFont(undefined, 'normal');
                doc.text(`${releve.width} m`, 50, yPos);
                yPos += 7;
            }
            yPos += 5;
        }
        
        // Type de terrain
        if (releve.terrainType || releve.terrainHardness) {
            doc.setFontSize(14);
            doc.setFont(undefined, 'bold');
            doc.text('TYPE DE TERRAIN', 20, yPos);
            yPos += 8;
            
            doc.setFontSize(11);
            doc.setFont(undefined, 'normal');
            
            if (releve.terrainType) {
                doc.setFont(undefined, 'bold');
                doc.text('Nature du terrain:', 20, yPos);
                doc.setFont(undefined, 'normal');
                doc.text(releve.terrainType, 60, yPos);
                yPos += 7;
            }
            
            if (releve.terrainHardness) {
                doc.setFont(undefined, 'bold');
                doc.text('Dureté:', 20, yPos);
                doc.setFont(undefined, 'normal');
                doc.text(releve.terrainHardness, 60, yPos);
                yPos += 7;
            }
            yPos += 5;
        }
        
        // Performance estimée
        if (releve.estimatedCadence || releve.lineageTransfer) {
            doc.setFontSize(14);
            doc.setFont(undefined, 'bold');
            doc.text('PERFORMANCE ESTIMÉE', 20, yPos);
            yPos += 8;
            
            doc.setFontSize(11);
            doc.setFont(undefined, 'normal');
            
            if (releve.estimatedCadence) {
                doc.setFont(undefined, 'bold');
                doc.text('Cadence estimée:', 20, yPos);
                doc.setFont(undefined, 'normal');
                doc.text(releve.estimatedCadence, 60, yPos);
                yPos += 7;
            }
            
            if (releve.lineageTransfer) {
                doc.setFont(undefined, 'bold');
                doc.text('Linéage transfert:', 20, yPos);
                doc.setFont(undefined, 'normal');
                doc.text(releve.lineageTransfer, 60, yPos);
                yPos += 7;
            }
            yPos += 5;
        }
        
        // Vérifier s'il faut une nouvelle page
        if (yPos > 220) {
            doc.addPage();
            yPos = 20;
        }
        
        
        // Localisation
        doc.setFontSize(14);
        doc.setFont(undefined, 'bold');
        doc.text('LOCALISATION', 20, yPos);
        yPos += 8;
        
        doc.setFontSize(11);
        doc.setFont(undefined, 'normal');
        const locationLines = doc.splitTextToSize(releve.location, 170);
        doc.text(locationLines, 20, yPos);
        yPos += locationLines.length * 7 + 5;
        
        doc.setFont(undefined, 'bold');
        doc.text('Coordonnées GPS:', 20, yPos);
        doc.setFont(undefined, 'normal');
        doc.text(`${releve.latitude.toFixed(6)}, ${releve.longitude.toFixed(6)}`, 60, yPos);
        yPos += 12;
        
        // Vérifier s'il faut une nouvelle page
        if (yPos > 250) {
            doc.addPage();
            yPos = 20;
        }
        
        // Description
        doc.setFontSize(14);
        doc.setFont(undefined, 'bold');
        doc.text('DESCRIPTION DU CHANTIER', 20, yPos);
        yPos += 8;
        
        doc.setFontSize(11);
        doc.setFont(undefined, 'normal');
        const descLines = doc.splitTextToSize(releve.description, 170);
        doc.text(descLines, 20, yPos);
        yPos += descLines.length * 7 + 12;
        
        // Vérifier s'il faut une nouvelle page
        if (yPos > 250) {
            doc.addPage();
            yPos = 20;
        }
        
        // Photos
        if (releve.photos && releve.photos.length > 0) {
            doc.setFontSize(14);
            doc.setFont(undefined, 'bold');
            doc.text(`PHOTOS (${releve.photos.length})`, 20, yPos);
            yPos += 10;
            
            // Ajouter les photos (max 2 par page)
            for (let i = 0; i < Math.min(releve.photos.length, 4); i++) {
                if (yPos > 220) {
                    doc.addPage();
                    yPos = 20;
                }
                
                try {
                    doc.addImage(releve.photos[i], 'JPEG', 20, yPos, 80, 60);
                    yPos += 70;
                } catch (error) {
                    console.error('Erreur lors de l\'ajout de la photo:', error);
                }
            }
        }
        
        // Signature
        if (yPos > 220) {
            doc.addPage();
            yPos = 20;
        }
        
        doc.setFontSize(14);
        doc.setFont(undefined, 'bold');
        doc.text('SIGNATURE', 20, yPos);
        yPos += 10;
        
        try {
            doc.addImage(releve.signature, 'PNG', 20, yPos, 60, 30);
        } catch (error) {
            console.error('Erreur lors de l\'ajout de la signature:', error);
        }
        
        // Pied de page sur chaque page
        const pageCount = doc.internal.getNumberOfPages();
        for (let i = 1; i <= pageCount; i++) {
            doc.setPage(i);
            doc.setFontSize(8);
            doc.setTextColor(128, 128, 128);
            doc.text(`Page ${i} sur ${pageCount}`, 105, 285, { align: 'center' });
            doc.text(`Généré le ${new Date().toLocaleDateString('fr-FR')}`, 105, 290, { align: 'center' });
        }
        
        // Télécharger le PDF
        const filename = `Releve_${releve.clientName.replace(/\s+/g, '_')}_${new Date().getTime()}.pdf`;
        doc.save(filename);
        
        showToast('PDF généré avec succès !', 'success');
        
    } catch (error) {
        console.error('Erreur lors de la génération du PDF:', error);
        showToast('Erreur lors de la génération du PDF', 'error');
    }
}

// ===============================================
// RECHERCHE
// ===============================================

async function performSearch() {
    const keyword = document.getElementById('search-input').value.toLowerCase();
    const machineFilter = document.getElementById('search-machine').value;
    const networkFilter = document.getElementById('search-network').value;
    
    try {
        let url = 'tables/releves?limit=100&sort=-created_at';
        
        if (keyword) {
            url += `&search=${encodeURIComponent(keyword)}`;
        }
        
        const response = await fetch(url);
        if (response.ok) {
            const result = await response.json();
            
            // Filtrer localement par machine et réseau si nécessaire
            let filtered = result.data;
            
            if (machineFilter) {
                filtered = filtered.filter(r => r.machineType === machineFilter);
            }
            
            if (networkFilter) {
                filtered = filtered.filter(r => r.networkType === networkFilter);
            }
            
            displaySearchResults(filtered);
        }
    } catch (error) {
        console.error('Erreur lors de la recherche:', error);
        showToast('Erreur lors de la recherche', 'error');
    }
}

function displaySearchResults(releves) {
    const resultsContainer = document.getElementById('search-results');
    
    if (releves.length === 0) {
        resultsContainer.innerHTML = `
            <div class="text-center py-12 text-gray-500">
                <i class="fas fa-search text-6xl mb-4"></i>
                <p class="text-lg">Aucun résultat trouvé</p>
                <p class="text-sm mt-2">Essayez avec d'autres critères de recherche</p>
            </div>
        `;
        return;
    }
    
    resultsContainer.innerHTML = `
        <h3 class="text-lg font-semibold text-gray-700 mb-3">
            ${releves.length} résultat(s) trouvé(s)
        </h3>
        ${releves.map(releve => `
            <div class="bg-gray-50 p-4 rounded-lg border border-gray-200 hover:shadow-md transition-shadow cursor-pointer mb-3" 
                 onclick="viewReleveDetails('${releve.id}')">
                <div class="flex justify-between items-start mb-2">
                    <div class="flex-1">
                        <h3 class="font-bold text-lg text-gray-800">
                            <i class="fas fa-user mr-2 text-blue-500"></i>${releve.clientName}
                        </h3>
                        <p class="text-sm text-gray-600 mt-1">
                            <i class="fas fa-map-marker-alt mr-2"></i>${releve.location}
                        </p>
                    </div>
                    <button onclick="event.stopPropagation(); generatePDF('${releve.id}')" 
                            class="bg-red-500 text-white px-3 py-1 rounded hover:bg-red-600 text-sm">
                        <i class="fas fa-file-pdf"></i>
                    </button>
                </div>
                
                <div class="grid grid-cols-2 gap-2 text-sm mt-3">
                    <div class="text-gray-600">
                        <i class="fas fa-cogs mr-1"></i>${releve.machineType}
                    </div>
                    <div class="text-gray-600">
                        <i class="fas fa-network-wired mr-1"></i>${releve.networkType}
                    </div>
                </div>
                
                <div class="text-xs text-gray-500 mt-3">
                    <i class="fas fa-calendar mr-1"></i>${formatDateTime(releve.dateTime)}
                </div>
            </div>
        `).join('')}
    `;
}

// ===============================================
// NAVIGATION
// ===============================================

function showPage(pageName) {
    // Masquer toutes les pages
    document.querySelectorAll('.page-content').forEach(page => {
        page.classList.add('hidden');
    });
    
    // Afficher la page demandée
    document.getElementById(`${pageName}-page`).classList.remove('hidden');
    
    // Mettre à jour les boutons de navigation
    document.querySelectorAll('.nav-btn').forEach(btn => {
        btn.classList.remove('active');
    });
    
    document.querySelector(`[data-page="${pageName}"]`).classList.add('active');
}

// ===============================================
// ÉVÉNEMENTS
// ===============================================

function initEventListeners() {
    // Navigation
    document.querySelectorAll('.nav-btn').forEach(btn => {
        btn.addEventListener('click', function() {
            const page = this.getAttribute('data-page');
            showPage(page);
        });
    });
    
    // Formulaire
    document.getElementById('releve-form').addEventListener('submit', handleFormSubmit);
    document.getElementById('reset-form-btn').addEventListener('click', resetForm);
    
    // Géolocalisation
    document.getElementById('get-location-btn').addEventListener('click', getUserLocation);
    document.getElementById('get-location-btn-2').addEventListener('click', getUserLocation);
    
    // Recherche d'adresse
    document.getElementById('search-address-btn').addEventListener('click', searchAddress);
    document.getElementById('address-search').addEventListener('keypress', function(e) {
        if (e.key === 'Enter') {
            e.preventDefault();
            searchAddress();
        }
    });
    
    // Photos
    document.getElementById('photos').addEventListener('change', handlePhotoUpload);
    
    // Signature
    document.getElementById('clear-signature-btn').addEventListener('click', function() {
        signaturePad.clear();
    });
    
    // Modal
    document.getElementById('close-modal-btn').addEventListener('click', closeModal);
    document.getElementById('detail-modal').addEventListener('click', function(e) {
        if (e.target === this) {
            closeModal();
        }
    });
    
    // Recherche
    document.getElementById('search-btn').addEventListener('click', performSearch);
    document.getElementById('search-input').addEventListener('keypress', function(e) {
        if (e.key === 'Enter') {
            performSearch();
        }
    });
}

// ===============================================
// UTILITAIRES
// ===============================================

function formatDateTime(timestamp) {
    const date = new Date(timestamp);
    return date.toLocaleDateString('fr-FR', {
        day: '2-digit',
        month: '2-digit',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit'
    });
}

function showToast(message, type = 'success') {
    const toast = document.getElementById('toast');
    const toastMessage = document.getElementById('toast-message');
    
    toastMessage.textContent = message;
    
    // Changer la couleur selon le type
    toast.classList.remove('bg-green-500', 'bg-red-500', 'bg-blue-500');
    if (type === 'error') {
        toast.classList.add('bg-red-500');
    } else if (type === 'info') {
        toast.classList.add('bg-blue-500');
    } else {
        toast.classList.add('bg-green-500');
    }
    
    toast.classList.remove('hidden');
    
    setTimeout(() => {
        toast.classList.add('hidden');
    }, 3000);
}
