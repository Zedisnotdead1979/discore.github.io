<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Catch Ratio ToolBox</title>
    <!-- Import de la nouvelle police : Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <!-- Lien vers la police d'icônes de Google -->
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <!-- Librairie pour la génération d'image -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

    <style>
        /* Réinitialisation de base et configuration de la boîte de modèle */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        /* Variables CSS pour la thématisation */
        :root {
            /* Typographie */
            --font-family-base: 'Inter', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            --font-weight-regular: 400;
            --font-weight-semibold: 600;
            --font-weight-bold: 700;

            /* Couleurs de base (Thème Clair) */
            --bg-color: #f5f5f5;
            --app-bg: #ffffff;
            --border-color: #e0e0e0;
            --text-primary: #1a1a1a;
            --text-secondary: #555555;
            --header-text: #ffffff;
            --button-bg: #f0f0f0;
            --button-text: #333;
            --input-bg: #ffffff;
            --input-border: #ddd;
            --history-action-color: #666;
            
            --header-bg: #2196F3; /* Thème bleu par défaut */
        }

        /* Thème sombre */
        [data-theme="dark"] {
            --bg-color: #121212;
            --app-bg: #1e1e1e;
            --border-color: #333;
            --text-primary: #f0f0f0;
            --text-secondary: #aaaaaa;
            --header-text: #ffffff;
            --button-bg: #333;
            --button-text: #f0f0f0;
            --input-bg: #2a2a2a;
            --input-border: #555;
            --history-action-color: #ccc;
        }

        /* Styles de base du corps */
        body {
            font-family: var(--font-family-base);
            background-color: var(--bg-color);
            color: var(--text-primary);
            transition: all 0.3s ease;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
        }


        /* --- Thèmes de couleur --- */
        [data-color-theme="blue"] { --header-bg: #2196F3; }
        [data-color-theme="green"] { --header-bg: #4CAF50; }
        [data-color-theme="marron-cuir"] { --header-bg: #8D6E63; }
        [data-color-theme="violet"] { --header-bg: #673AB7; }
        [data-color-theme="rose"] { --header-bg: #E91E63; }
        [data-color-theme="turquoise"] { --header-bg: #009688; }
        [data-color-theme="ardoise"] { --header-bg: #607D8B; }
        [data-color-theme="rouge"] { --header-bg: #d32f2f; }
        [data-color-theme="jaune"] { --header-bg: #FBC02D; }
        [data-color-theme="cyan"] { --header-bg: #00BCD4; }
        [data-color-theme="citron-vert"] { --header-bg: #CDDC39; }
        [data-color-theme="acier"] { --header-bg: #546E7A; }
        [data-color-theme="orange"] { --header-bg: #FF9800; }
        [data-color-theme="sarcelle"] { --header-bg: #008080; }
        [data-color-theme="indigo"] { --header-bg: #3F51B5; }
        [data-color-theme="ambre"] { --header-bg: #FFC107; }
        [data-color-theme="magenta"] { --header-bg: #C2185B; }
        [data-color-theme="bleu-ciel"] { --header-bg: #03A9F4; }
        [data-color-theme="corail"] { --header-bg: #FF7F50; }
        [data-color-theme="lavande"] { --header-bg: #BDB2FF; }


        /* --- Disposition principale --- */
        .app-container {
            max-width: 400px;
            margin: 20px auto;
            border: 1px solid var(--border-color);
            border-radius: 15px;
            background-color: var(--app-bg);
            overflow: hidden;
            box-shadow: 0 4px 20px rgba(0,0,0,0.1);
            min-height: 700px;
            display: flex;
            flex-direction: column;
        }

        .header {
            background-color: var(--header-bg);
            color: var(--header-text);
            padding: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            transition: background-color 0.3s ease;
        }

        .nav-icon {
            cursor: pointer;
            padding: 8px;
            border-radius: 50%;
            transition: background-color 0.3s ease, transform 0.2s ease;
            user-select: none;
        }

        .nav-icon:hover {
            background-color: rgba(255,255,255,0.2);
        }

        .nav-icon:active {
            transform: scale(0.9);
        }

        .nav-icon.active {
            background-color: rgba(255,255,255,0.3);
        }

        .content {
            flex: 1;
            position: relative;
            overflow: hidden;
        }

        /* Gestion de l'affichage des vues et transitions */
        .view {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            padding: 20px;
            background-color: var(--app-bg);
            transition: transform 0.35s cubic-bezier(0.4, 0, 0.2, 1);
            display: flex;
            flex-direction: column;
            overflow-y: auto;
        }

        /* Désactive les transitions au chargement initial */
        .content.no-transition .view {
            transition: none;
        }

        /* Transition Horizontale */
        .content.transition-horizontal .view {
            transform: translateX(100%);
        }
        .content.transition-horizontal .view.view-exit {
            transform: translateX(-100%);
        }
        .content.transition-horizontal .view.active {
            transform: translateX(0);
            z-index: 1;
        }

        /* Transition Verticale */
        .content.transition-vertical .view {
            transform: translateY(100%);
        }
        .content.transition-vertical .view.view-exit {
            transform: translateY(-100%);
        }
        .content.transition-vertical .view.active {
            transform: translateY(0);
            z-index: 1;
        }


        /* --- Vue de Mesure --- */
        #measureView .action-buttons {
            margin-top: auto;
            padding-top: 20px;
            border-top: 1px solid var(--border-color);
        }

        .timer-container {
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 10px;
            position: relative;
        }

        .timer {
            text-align: center;
            font-size: 3.2em;
            font-weight: var(--font-weight-bold);
            padding: 10px;
            border-radius: 10px;
            background-color: var(--button-bg);
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
            color: var(--text-primary);
        }

        .timer.running { animation: blink-border 1s ease-in-out infinite; }
        @keyframes blink-border {
            0%, 100% { box-shadow: 0 0 0 3px var(--header-bg); }
            50% { box-shadow: 0 0 0 3px transparent; }
        }

        .timer.green { color: #4CAF50; }
        .timer.yellow { color: #FF9800; }
        .timer.red { color: #F44336; }
        .timer.critical { color: #fff; background-color: #F44336; }

        .counting-buttons { display: flex; flex-direction: column; gap: 20px; margin-bottom: 30px; }
        .main-count-row { display: flex; gap: 15px; }

        .main-count-button {
            flex: 1;
            padding: 45px;
            font-size: 1.6em;
            font-weight: var(--font-weight-bold);
            border: none;
            border-radius: 15px;
            cursor: pointer;
            transition: all 0.2s ease, transform 0.1s ease;
            color: white;
        }
        .main-count-button:active {
            transform: scale(0.97);
        }

        .main-count-button:disabled { opacity: 0.5; cursor: not-allowed; }

        .secondary-count-row {
            display: flex;
            gap: 15px;
            align-items: stretch;
        }

        .take-button {
            flex: 1;
            padding: 25px;
            font-size: 1.4em;
            font-weight: var(--font-weight-bold);
            border: none;
            border-radius: 12px;
            cursor: pointer;
            transition: all 0.2s ease, transform 0.1s ease;
            color: white;
            background-color: #FF9800;
        }
        .take-button:active {
            transform: scale(0.98);
        }

        .take-button:disabled { opacity: 0.5; cursor: not-allowed; }

        .fault-button {
            flex-shrink: 0;
            width: auto;
            padding: 0 25px;
            font-size: 1.4em;
            font-weight: var(--font-weight-bold);
            border: 2px solid #F44336;
            border-radius: 12px;
            cursor: pointer;
            transition: all 0.2s ease, transform 0.1s ease;
            color: #F44336;
            background-color: transparent;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .fault-button:hover:not(:disabled) {
            background-color: rgba(244, 67, 54, 0.1);
        }

        .fault-button:active:not(:disabled) {
            transform: scale(0.97);
        }

        .fault-button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }


        .catch-btn { background-color: #4CAF50; }
        .catch-btn:hover:not(:disabled) { background-color: #45a049; }
        .drop-btn { background-color: #F44336; }
        .drop-btn:hover:not(:disabled) { background-color: #da190b; }
        .take-button:hover:not(:disabled) { background-color: #e68900; }

        .action-buttons { display: flex; gap: 10px; }
        .action-button { flex: 1; padding: 15px; font-size: 1em; font-weight: var(--font-weight-semibold); border: none; border-radius: 10px; cursor: pointer; transition: all 0.2s ease, transform 0.1s ease; background-color: var(--button-bg); color: var(--button-text); }
        .action-button:hover { opacity: 0.8; }
        .action-button:active {
            transform: scale(0.97);
        }
        #undoBtn { background-color: var(--header-bg); color: var(--header-text); border: none; }
        #endRunBtn { border: 2px solid #F44336; background-color: var(--button-bg); color: var(--text-primary); font-weight: var(--font-weight-bold); }
        #razBtn { border: 2px solid #F44336; }
        #saveBtn { border: 2px solid #4CAF50; }

        .stats-display { margin-bottom: 10px; }
        .main-stat-block { background-color: var(--button-bg); border-radius: 10px; padding: 10px; text-align: center; }
        .main-stat-line { display: flex; justify-content: center; align-items: baseline; gap: 10px; margin-bottom: 15px; }
        .total-throws { font-size: 2em; font-weight: var(--font-weight-bold); color: var(--header-bg); margin-bottom: 0; }
        .total-label { font-size: 1.2em; margin-bottom: 0; color: var(--text-secondary); font-weight: var(--font-weight-semibold); }
        .live-timeline { background-color: var(--input-bg); border-radius: 8px; padding: 10px; min-height: 50px; overflow-x: auto; white-space: nowrap; border: 1px solid var(--input-border); }
        .live-timeline-item { display: inline-block; width: 25px; height: 25px; line-height: 25px; text-align: center; margin: 1px; border-radius: 50%; font-weight: bold; color: white; font-size: 0.8em; }
        .live-timeline-catch { background-color: #4CAF50; }
        .live-timeline-drop { background-color: #F44336; }
        .live-timeline-take { background-color: #FF9800; }
        .live-timeline-fault {
            background-color: transparent;
            border: 2px solid #F44336;
            color: #F44336;
            line-height: 21px; /* Adjust for border */
        }

        /* --- Vue des Résultats --- */
        .results-section { margin-bottom: 25px; }
        .results-title { font-size: 1.4em; font-weight: var(--font-weight-bold); margin-bottom: 15px; color: var(--header-bg); }
        .score-summary { text-align: center; font-size: 1.4em; font-weight: var(--font-weight-semibold); color: var(--text-primary); margin: 20px 0; }
        .timeline {
            background-color: var(--button-bg);
            border-radius: 10px;
            padding: 15px;
            margin-bottom: 25px;
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 4px;
        }
        .timeline-item { display: inline-block; width: 30px; height: 30px; line-height: 30px; text-align: center; margin: 2px; border-radius: 50%; font-weight: bold; color: white; }
        .timeline-catch { background-color: #4CAF50; }
        .timeline-drop { background-color: #F44336; }
        .timeline-take { background-color: #FF9800; }
        .timeline-fault {
            background-color: transparent;
            border: 2px solid #F44336;
            color: #F44336;
            line-height: 26px; /* Adjust for border */
        }

        /* RÉSULTATS COMPACTS */
        .compact-results-container {
            background-color: var(--button-bg);
            border-radius: 12px;
            padding: 15px;
            margin-top: 20px;
        }

        .compact-stats-row {
            display: flex;
            justify-content: center;
            align-items: baseline;
            gap: 12px;
            font-size: 1.1em;
            margin-bottom: 15px;
            padding-bottom: 15px;
            border-bottom: 1px solid var(--border-color);
            flex-wrap: wrap;
        }

        .compact-stat {
            font-weight: var(--font-weight-semibold);
            color: var(--text-primary);
            white-space: nowrap;
        }

        .compact-stat .stat-letter-c,
        .compact-stat .stat-letter-t,
        .compact-stat .stat-letter-d {
            display: inline-block;
            width: 22px;
            text-align: center;
            font-weight: var(--font-weight-bold);
            padding: 2px 0px;
            border-radius: 5px;
            color: #fff;
            margin-right: 6px;
        }

        .compact-stat .stat-letter-c { background-color: #4CAF50; }
        .compact-stat .stat-letter-t { background-color: #FF9800; }
        .compact-stat .stat-letter-d { background-color: #F44336; }

        .separator {
            color: var(--border-color);
            font-weight: var(--font-weight-regular);
        }

        .compact-time-row {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 8px;
            font-size: 1.1em;
            color: var(--text-secondary);
        }

        .compact-time-icon {
            font-size: 1.5em !important;
            color: var(--header-bg);
        }

        .compact-time-value {
            font-weight: var(--font-weight-bold);
            font-size: 1.2em;
            color: var(--text-primary);
        }

        .compact-time-label {
             font-weight: var(--font-weight-semibold);
        }


        /* --- Vue Historique --- */
        .history-controls { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; flex-wrap: wrap; }
        .search-container { width: 100%; margin-bottom: 15px; }
        .search-input {
            width: 100%;
            padding: 12px;
            border: none;
            border-bottom: 2px solid var(--header-bg);
            background-color: transparent;
            font-size: 1em;
            color: var(--text-primary);
            transition: all 0.3s ease;
        }
        .search-input:focus {
            outline: none;
        }
        .search-input::placeholder {
            color: var(--text-secondary);
            opacity: 0.7;
        }
        .sort-button, .export-button, .import-button { padding: 8px 12px; border: 1px solid var(--border-color); border-radius: 5px; background-color: var(--button-bg); color: var(--button-text); cursor: pointer; font-size: 0.9em; transition: all 0.2s ease, transform 0.1s ease; font-weight: var(--font-weight-semibold); }
        .sort-button:active, .export-button:active, .import-button:active { transform: scale(0.97); }
        .export-button { background-color: var(--header-bg); color: var(--header-text); border-color: var(--header-bg); }
        .import-button { background-color: transparent; color: var(--header-bg); border: 2px solid var(--header-bg); }
        .sort-button.active { background-color: var(--header-bg); color: var(--header-text); }
        .import-status { text-align: center; margin-bottom: 15px; font-style: italic; color: #999; }
        .history-list { }
        .history-item { 
            background-color: var(--button-bg); 
            padding: 15px; 
            margin-bottom: 10px; 
            border-radius: 10px; 
            cursor: default;
            border: 1px solid var(--border-color);
        }
        .history-item:hover {
            background-color: color-mix(in srgb, var(--button-bg) 90%, var(--text-primary));
        }
        .history-header { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 10px; }
        .history-header-info { flex: 1; }
        .dog-name { font-weight: var(--font-weight-bold); font-size: 1.1em; color: var(--text-primary); }
        .run-tag { background-color: var(--header-bg); color: var(--header-text); padding: 2px 8px; border-radius: 12px; font-size: 0.8em; font-weight: var(--font-weight-semibold); }
        .history-item-actions { display: flex; gap: 10px; }
        .history-action-btn { background: none; border: none; cursor: pointer; padding: 5px; color: var(--history-action-color); border-radius: 50%; z-index: 2; transition: all 0.2s ease; }
        .history-action-btn:hover { background-color: rgba(0,0,0,0.1); }
        .history-action-btn:active { transform: scale(0.9); }
        [data-theme="dark"] .history-action-btn:hover { background-color: rgba(255,255,255,0.1); }
        .history-item-actions .delete-btn:hover {
            color: #F44336;
            background-color: rgba(244, 67, 54, 0.1);
        }
        .history-details { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; font-size: 0.9em; color: var(--text-secondary); }
        .history-details strong { color: var(--text-primary); font-weight: var(--font-weight-semibold); }
        .history-score { background-color: var(--header-bg); color: var(--header-text); padding: 2px 8px; border-radius: 12px; font-size: 0.9em; font-weight: bold; display: inline-block; }

        .advanced-stats-container {
            margin-top: 15px;
            padding-top: 15px;
            border-top: 1px dashed var(--border-color);
            text-align: center; /* Pour centrer le titre */
        }
        .advanced-stats-title {
            font-weight: var(--font-weight-bold);
            color: var(--header-bg);
            font-size: 0.7em;
            text-transform: uppercase;
            border: 1.5px solid var(--header-bg);
            border-radius: 15px;
            padding: 4px 12px;
            display: inline-block;
            margin-bottom: 10px;
        }
        .advanced-stat {
            display: flex;
            justify-content: space-between;
            font-size: 0.9em;
            padding: 4px 0;
            text-align: left; /* Réaligner le texte à gauche pour les stats */
        }
        .advanced-stat .stat-label { color: var(--text-secondary); }
        .advanced-stat .stat-value { font-weight: var(--font-weight-bold); color: var(--text-primary); }
        .history-notes {
            margin-top: 15px;
            padding-top: 10px;
            font-style: italic;
            white-space: pre-wrap;
            font-size: 0.9em;
            color: var(--text-secondary);
            border-top: 1px dashed var(--border-color);
        }

        [data-theme="dark"] .advanced-stats-title {
            background-color: rgba(255, 255, 255, 0.1);
        }


        /* --- Vue Options --- */
        .option-group { background-color: var(--button-bg); padding: 20px; border-radius: 10px; margin-bottom: 20px; }
        .option-title { font-weight: var(--font-weight-bold); margin-bottom: 15px; color: var(--header-bg); font-size: 1.1em; }
        .sub-option {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 0;
            color: var(--text-primary);
        }
        .sub-option:not(:last-child) {
            border-bottom: 1px solid var(--border-color);
        }
        .toggle-switch { position: relative; display: inline-block; width: 60px; height: 34px; flex-shrink: 0; }
        .toggle-switch input { opacity: 0; width: 0; height: 0; }
        .slider { position: absolute; cursor: pointer; top: 0; left: 0; right: 0; bottom: 0; background-color: #ccc; transition: .4s; border-radius: 34px; }
        .slider:before { position: absolute; content: ""; height: 26px; width: 26px; left: 4px; bottom: 4px; background-color: white; transition: .4s; border-radius: 50%; }
        input:checked + .slider { background-color: var(--header-bg); }
        input:checked + .slider:before { transform: translateX(26px); }
        .danger-button { background-color: #F44336; color: white; padding: 12px 20px; border: none; border-radius: 5px; cursor: pointer; font-weight: var(--font-weight-bold); width: 100%; transition: all 0.2s ease, transform 0.1s ease; }
        .danger-button:hover { background-color: #da190b; }
        .danger-button:active { transform: scale(0.97); }
        .version-container { margin-top: auto; padding-top: 20px; text-align: center; color: var(--text-secondary); opacity: 0.8; font-size: 0.9em; }

        .dog-management-form { display: flex; gap: 10px; margin-bottom: 15px; }
        .dog-management-form .form-input { flex-grow: 1; }
        .dog-management-form .action-button { flex-grow: 0; flex-shrink: 0; padding: 10px; line-height: 1; background-color: var(--header-bg); color: var(--header-text); }
        .dog-profiles-list { max-height: 200px; overflow-y: auto; }
        .dog-profile-item { display: flex; align-items: center; justify-content: space-between; padding: 10px; border-bottom: 1px solid var(--border-color); }
        .dog-profile-item:last-child { border-bottom: none; }
        .dog-profile-name { font-weight: var(--font-weight-semibold); color: var(--text-primary); }
        .dog-profile-actions { display: flex; gap: 8px; }
        .dog-profile-actions .history-action-btn { font-size: 1.2em; }

        .color-theme-selector { display: grid; grid-template-columns: repeat(auto-fill, minmax(40px, 1fr)); gap: 15px; padding-top: 10px; }
        .color-swatch { width: 40px; height: 40px; border-radius: 50%; cursor: pointer; border: 3px solid transparent; transition: transform 0.2s ease, border-color 0.2s ease; display: flex; align-items: center; justify-content: center; }
        .color-swatch:hover { transform: scale(1.1); }
        .color-swatch.active { border-color: var(--header-bg); transform: scale(1.15); }
        .color-swatch.active::after { content: 'check'; font-family: 'Material Icons'; color: var(--header-text); font-size: 20px; }

        .data-buttons {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        .action-button.outline {
            background-color: transparent;
            border: 2px solid var(--header-bg);
            color: var(--header-bg);
            font-weight: var(--font-weight-bold);
            text-align: center;
        }

        .action-button.outline:hover {
            background-color: var(--header-bg);
            color: var(--header-text);
        }

        /* --- Modales --- */
        .modal {
            display: none;
            position: fixed;
            z-index: 1000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.5);
            opacity: 0;
            transition: opacity 0.3s ease-in-out;
        }
        .modal.visible {
            opacity: 1;
        }

        .modal-content {
            background-color: var(--app-bg);
            margin: 5vh auto;
            padding: 25px;
            border-radius: 12px;
            width: 90%;
            max-width: 400px;
            text-align: center;
            position: relative;
            transform: scale(0.95);
            transition: transform 0.3s ease-in-out;
            border: 1px solid var(--border-color);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1), 0 2px 5px rgba(0,0,0,0.05);
            max-height: 90vh;
            overflow-y: auto;
        }
        .modal.visible .modal-content {
            transform: scale(1);
        }

        .modal-title { 
            color: var(--header-bg); 
            margin-bottom: 15px;
            font-size: 1.4em;
            font-weight: var(--font-weight-bold);
        }
        #modalMessage {
            margin: 20px 0;
            line-height: 1.6;
            font-size: 1.1em;
            color: var(--text-secondary);
        }

        .modal-buttons { 
            display: flex;
            gap: 15px;
            justify-content: center;
            margin-top: 25px;
        }
        .modal-button { 
            padding: 12px 20px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: var(--font-weight-semibold);
            flex: 1;
            text-decoration: none;
            transition: all 0.2s ease, transform 0.1s ease;
        }
        .modal-button:active { transform: scale(0.97); }
        .modal-confirm { background-color: var(--header-bg); color: white; }
        .modal-cancel { background-color: var(--button-bg); color: var(--button-text); border: 1px solid var(--border-color); }

        .modal-button.disabled {
            background-color: #ccc;
            color: #888;
            cursor: not-allowed;
            opacity: 0.6;
        }
        [data-theme="dark"] .modal-button.disabled {
            background-color: #444;
            color: #888;
        }

        .save-form { padding: 0; margin: 0; }
        .form-group { margin-bottom: 20px; text-align: left; }
        .form-label { display: block; margin-bottom: 8px; font-weight: var(--font-weight-semibold); font-size: 0.9em; opacity: 0.8; color: var(--text-secondary); }
        .form-input { width: 100%; padding: 12px; border: 1px solid var(--input-border); border-radius: 8px; background-color: var(--input-bg); color: var(--text-primary); font-family: var(--font-family-base); }
        .suggestions { background-color: var(--input-bg); border: 1px solid var(--input-border); border-top: none; max-height: 150px; overflow-y: auto; position: absolute; width: calc(100% - 40px); z-index: 1001; }
        .suggestion-item { padding: 10px; cursor: pointer; border-bottom: 1px solid var(--input-border); }
        .suggestion-item:hover { background-color: var(--button-bg); }
        .tag-preview { font-style: italic; color: var(--header-bg); margin-top: 5px; text-align: left; }

        /* --- Partage --- */
        .share-card { display: block; position: absolute; left: -9999px; top: -9999px; width: 350px; background-color: var(--app-bg); color: var(--text-primary); border: 2px solid var(--border-color); border-radius: 15px; font-family: var(--font-family-base); overflow: hidden; }
        .share-header { padding: 10px; background-color: var(--header-bg); color: var(--header-text); text-align: center; }
        .share-header h3 { font-weight: var(--font-weight-bold); }
        .share-body { padding: 20px; }
        #shareDogName { text-align: center; font-size: 2em; margin-bottom: 5px; color: var(--header-bg); font-weight: var(--font-weight-bold); }
        #shareDate {
            text-align: center;
            margin-bottom: 5px;
            font-size: 0.9em;
            color: var(--text-secondary);
        }
        #shareRunTag {
            text-align: center;
            margin-bottom: 20px;
            font-size: 1em;
            font-style: italic;
            color: var(--text-secondary);
        }
        .share-score-container { text-align: center; margin-bottom: 20px; }
        .share-score { font-size: 4em; font-weight: var(--font-weight-bold); color: var(--header-bg); line-height: 1; }
        .share-score-label { font-size: 1.2em; color: var(--text-primary); }
        .share-stats-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; text-align: center; margin-bottom: 20px; }
        .share-stat span:first-child { font-size: 1.8em; font-weight: var(--font-weight-bold); display: block; color: var(--text-primary); }
        .share-stat span:last-child { font-size: 0.8em; text-transform: uppercase; color: var(--text-secondary); }
        .share-timeline { display: flex; gap: 4px; justify-content: center; flex-wrap: wrap; margin-bottom: 15px; }
        .share-timeline .timeline-item { width: 20px; height: 20px; line-height: 20px; font-size: 0.7em;}
        .share-timeline .timeline-fault {
            background-color: transparent !important; /* Override for sharing */
            border: 2px solid #F44336;
            color: #F44336;
            line-height: 16px; /* Adjust for border */
        }

        .share-advanced-stats {
            margin-top: 15px;
            padding-top: 10px;
            border-top: 1px dashed var(--border-color);
            text-align: left;
            font-size: 0.9em;
        }
        .share-advanced-stat {
            margin-bottom: 8px;
            display: flex;
            align-items: flex-start;
        }
        .share-advanced-stat strong {
            color: var(--header-bg);
            min-width: 130px;
            flex-shrink: 0;
            padding-top: 1px;
            font-weight: var(--font-weight-semibold);
        }
        .share-streak-timeline {
            display: flex;
            flex-wrap: wrap;
            gap: 3px;
            margin-left: 5px;
            max-width: 150px;
        }
        .share-streak-timeline .timeline-item {
            width: 18px;
            height: 18px;
            line-height: 18px;
            font-size: 0.7em;
        }

        #shareImageContainer img { max-width: 100%; border-radius: 10px; }
        .loader { border: 4px solid #f3f3f3; border-radius: 50%; border-top: 4px solid var(--header-bg); width: 40px; height: 40px; animation: spin 2s linear infinite; margin: 20px auto; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        /* --- Style de la barre de défilement --- */
        .view::-webkit-scrollbar,
        .modal-content::-webkit-scrollbar {
            width: 6px;
        }
        .view::-webkit-scrollbar-track,
        .modal-content::-webkit-scrollbar-track {
            background: transparent;
        }
        .view::-webkit-scrollbar-thumb,
        .modal-content::-webkit-scrollbar-thumb {
            background-color: var(--header-bg);
            border-radius: 10px;
        }
        .view, 
        .modal-content {
            scrollbar-width: thin;
            scrollbar-color: var(--header-bg) transparent;
        }

        /* --- Media Queries pour la responsivité --- */
        @media (max-width: 480px) {
            .app-container { margin: 10px; border-radius: 10px; }
            .timer { font-size: 2.5em; }
            .count-button { padding: 15px; font-size: 1.1em; }
            .history-controls { flex-direction: column; }
            .sort-button, .export-button, .import-button { width: 100%; }
        }
    </style>
</head>
<body>
    <!-- Conteneur principal de l'application -->
    <div class="app-container">
        <!-- En-tête de l'application avec la navigation -->
        <div class="header">
            <div class="nav-icon" id="optionsNav"><span class="material-icons">menu</span></div>
            <div class="nav-icon active" id="homeNav"><span class="material-icons">home</span></div>
            <div class="nav-icon" id="historyNav"><span class="material-icons">history</span></div>
        </div>

        <!-- Contenu principal qui change en fonction de la vue -->
        <div class="content">
            <!-- Vue de mesure (active par défaut) -->
            <div class="view active" id="measureView">
                <div class="timer-container"><div class="timer" id="timer">00:00</div></div>
                <div class="stats-display"><div class="main-stat-block"><div class="main-stat-line"><div class="total-label">Total Lancés :</div><div class="total-throws" id="totalThrows">0</div></div><div class="live-timeline" id="liveTimeline"><span style="color: #999; font-style: italic;">Aucun lancer pour le moment</span></div></div></div>
                <div class="counting-buttons">
                    <div class="main-count-row">
                        <button class="main-count-button catch-btn" id="catchBtn">CATCH</button>
                        <button class="main-count-button drop-btn" id="dropBtn">DROP</button>
                    </div>
                    <div class="secondary-count-row">
                        <button class="take-button" id="takeBtn">TAKE</button>
                        <button class="fault-button" id="faultBtn">Faute</button>
                    </div>
                </div>
                <div class="action-buttons"><button class="action-button" id="undoBtn">UNDO</button><button class="action-button" id="endRunBtn">Fin du Run</button></div>
            </div>

            <!-- Vue des résultats -->
            <div class="view" id="resultsView">
                <div class="results-section">
                    <div class="results-title">Résultats du Run</div>
                    <p class="score-summary" id="scoreSummary"></p>
                    <div class="timeline" id="timeline"></div>
                    <!-- NOUVELLE DISPOSITION COMPACTE DES RÉSULTATS -->
                    <div class="compact-results-container">
                        <div class="compact-stats-row">
                            <span class="compact-stat" id="compactCatches"></span>
                            <span class="separator">|</span>
                            <span class="compact-stat" id="compactTakes"></span>
                            <span class="separator">|</span>
                            <span class="compact-stat" id="compactDrops"></span>
                        </div>
                        <div class="compact-time-row">
                            <span class="material-icons compact-time-icon">timer</span>
                            <span class="compact-time-value" id="compactTime">00:00</span>
                            <span class="compact-time-label">Temps Final</span>
                        </div>
                    </div>
                </div>
                <div class="action-buttons">
                    <button class="action-button" id="razBtn">RAZ</button>
                    <button class="action-button" id="saveBtn">SAVE</button>
                </div>
            </div>

            <!-- Vue de l'historique -->
            <div class="view" id="historyView">
                <div class="history-controls">
                    <div class="search-container">
                        <input type="text" id="searchInput" class="search-input" placeholder="Rechercher par chien ou note...">
                    </div>
                    <button class="sort-button active" data-sort="date">Date/Heure</button>
                    <button class="sort-button" data-sort="name">Nom (A-Z)</button>
                    <button class="sort-button" data-sort="score">Score</button>
                    <label for="importCsvInput" class="import-button">Importer Runs CSV</label>
                    <input type="file" id="importCsvInput" accept=".csv" style="display: none;">
                    <button class="export-button" id="exportBtn">Exporter Runs CSV</button>
                </div>
                <div id="importStatus" class="import-status"></div>
                <div class="history-list" id="historyList"></div>
            </div>

            <!-- Vue des options -->
            <div class="view" id="optionsView">
                <div class="option-group">
                    <div class="option-title">Gérer les chiens</div>
                    <div class="dog-management-form"><input type="text" id="newDogNameInput" class="form-input" placeholder="Ajouter un nouveau chien..."><button id="addDogBtn" class="action-button material-icons">add_circle</button></div>
                    <div id="dogProfilesList" class="dog-profiles-list"></div>
                </div>
                <div class="option-group">
                    <div class="option-title">Seuil Minimum de Lancés</div>
                    <input type="number" class="form-input" id="seuilInput" min="0" value="0">
                </div>
                <div class="option-group">
                    <div class="option-title">Bouton Faute</div>
                    <label class="toggle-switch">
                        <input type="checkbox" id="faultButtonToggle">
                        <span class="slider"></span>
                    </label>
                    <span id="faultButtonLabel">Désactivé</span>
                </div>
                <div class="option-group">
                    <div class="option-title">Thème Sombre/Clair</div>
                    <label class="toggle-switch"><input type="checkbox" id="themeToggle"><span class="slider"></span></label>
                    <span id="themeLabel">Clair</span>
                </div>
                <div class="option-group">
                    <div class="option-title">Thème de Couleur</div>
                    <div class="color-theme-selector" id="colorThemeSelector"></div>
                </div>
                <div class="option-group">
                    <div class="option-title">Retour Haptique (Vibration)</div>
                    <label class="toggle-switch"><input type="checkbox" id="hapticToggle"><span class="slider"></span></label>
                    <span id="hapticLabel">Activé</span>
                </div>
                <div class="option-group">
                    <div class="option-title">Type de Transition</div>
                    <label class="toggle-switch"><input type="checkbox" id="transitionToggle"><span class="slider"></span></label>
                    <span id="transitionLabel">Horizontale</span>
                </div>
                <div class="option-group">
                    <div class="option-title">Partage</div>
                    <div class="sub-option">
                        <span>Activer le bouton de partage</span>
                        <label class="toggle-switch">
                            <input type="checkbox" id="shareToggle">
                            <span class="slider"></span>
                        </label>
                    </div>
                    <div class="sub-option">
                        <span>Afficher les stats avancées</span>
                        <label class="toggle-switch">
                            <input type="checkbox" id="advancedStatsToggle">
                            <span class="slider"></span>
                        </label>
                    </div>
                </div>
                 <div class="option-group">
                    <div class="option-title">Boutons d'import/export Runs CSV</div>
                    <label class="toggle-switch"><input type="checkbox" id="csvToggle"><span class="slider"></span></label>
                    <span id="csvLabel">Activé</span>
                </div>
                <div class="option-group">
                    <div class="option-title">Données</div>
                    <div class="data-buttons">
                        <button id="backupBtn" class="action-button outline">Backup</button>
                        <label for="restoreInput" class="action-button outline">Restore</label>
                        <input type="file" id="restoreInput" accept=".json" style="display: none;">
                    </div>
                    <button class="danger-button" id="purgeBtn">Purger l'historique</button>
                </div>
                <div class="version-container"><p id="versionDisplay"></p></div>
            </div>
        </div>
    </div>

    <!-- Modales -->
    <div class="modal" id="confirmModal"><div class="modal-content"><p id="modalMessage"></p><div class="modal-buttons"><button class="modal-button modal-cancel" id="modalCancel">Annuler</button><button class="modal-button modal-confirm" id="modalConfirm">Confirmer</button></div></div></div>
    <div class="modal" id="saveModal"><div class="modal-content"><h3 class="modal-title">Sauvegarder le Run</h3><div class="save-form" id="saveForm"><div class="form-group"><label class="form-label">Nom du chien</label><input type="text" class="form-input" id="dogNameInput" placeholder="Entrez le nom du chien"><div class="suggestions" id="suggestions" style="display: none;"></div><div class="tag-preview" id="tagPreview"></div></div><div class="form-group"><label class="form-label">Notes (facultatif)</label><textarea class="form-input" id="notesInput" rows="3" placeholder="Ajoutez des remarques..."></textarea></div><div class="modal-buttons"><button class="modal-button modal-cancel" id="cancelSaveBtn">Annuler</button><button class="modal-button modal-confirm" id="confirmSaveBtn">Enregistrer</button></div></div></div></div>
    <div class="modal" id="editModal"><div class="modal-content"><h3 class="modal-title">Modifier le Run</h3><div class="form-group"><label class="form-label">Nom du chien</label><input type="text" class="form-input" id="editDogNameInput"></div><div class="form-group"><label class="form-label">Notes</label><textarea class="form-input" id="editNotesInput" rows="3"></textarea></div><div class="modal-buttons"><button class="modal-button modal-cancel" id="cancelUpdateBtn">Annuler</button><button class="modal-button modal-confirm" id="confirmUpdateBtn">Mettre à jour</button></div></div></div>
    <div class="modal" id="shareModal"><div class="modal-content"><h3 class="modal-title">Partager le Résultat</h3><div id="shareImageContainer"><div class="loader"></div></div><div class="modal-buttons"><button class="modal-button modal-cancel" id="closeShareBtn">Fermer</button><a href="#" class="modal-button modal-confirm" id="downloadShareBtn" download="disc-dog-result.png">Télécharger</a></div></div></div>

    <!-- Template pour l'image de partage (caché) -->
    <div id="shareCard" class="share-card">
        <div class="share-header"><h3>Catch Ratio</h3></div>
        <div class="share-body">
            <h2 id="shareDogName"></h2>
            <p id="shareDate"></p>
            <p id="shareRunTag"></p>
            <div class="share-score-container"><div id="shareScore" class="share-score"></div><div class="share-score-label">Catch Ratio</div></div>
            <div class="share-stats-grid">
                <div class="share-stat"><span id="shareCatches"></span><span>Catches</span></div>
                <div class="share-stat"><span id="shareDrops"></span><span>Drops</span></div>
                <div class="share-stat"><span id="shareTakes"></span><span>Takes</span></div>
                <div class="share-stat"><span id="shareTotal"></span><span>Total</span></div>
            </div>
            <div class="share-timeline" id="shareTimeline"></div>
            <div class="share-advanced-stats" id="shareAdvancedStats">
                <!-- Les statistiques avancées seront injectées ici par JavaScript -->
            </div>
        </div>
        <div class="share-footer"><p>Made with Catch Ratio ToolBox</p></div>
    </div>

    <script>
        // Attend que le contenu du DOM soit entièrement chargé avant d'exécuter le script
        document.addEventListener('DOMContentLoaded', () => {
            
            /**
             * Classe principale pour gérer l'application Disc Dog Scorer.
             */
            class DiscDogScorer {
                constructor() {
                    // --- Propriétés de l'application ---
                    this.version = '1.3.0'; // Version mise à jour
                    this.currentView = 'measure';
                    this.editingRunId = null;
                    this.dogProfiles = [];
                    this.lastRunData = null;
                    this.modalConfirmCallback = null;

                    // --- Thèmes et couleurs ---
                    this.colorThemes = [
                        { name: 'rouge', color: '#d32f2f' }, { name: 'rose', color: '#E91E63' },
                        { name: 'magenta', color: '#C2185B' }, { name: 'orange', color: '#FF9800' },
                        { name: 'corail', color: '#FF7F50' },
                        { name: 'ambre', color: '#FFC107' }, { name: 'jaune', color: '#FBC02D' },
                        { name: 'citron-vert', color: '#CDDC39' }, { name: 'marron-cuir', color: '#8D6E63' },
                        { name: 'green', color: '#4CAF50' }, { name: 'turquoise', color: '#009688' },
                        { name: 'sarcelle', color: '#008080' }, { name: 'cyan', color: '#00BCD4' },
                        { name: 'bleu-ciel', color: '#03A9F4' }, { name: 'blue', color: '#2196F3' },
                        { name: 'indigo', color: '#3F51B5' }, { name: 'lavande', color: '#BDB2FF' },
                        { name: 'violet', color: '#673AB7' }, { name: 'ardoise', color: '#607D8B' },
                        { name: 'acier', color: '#546E7A' },
                    ];
                    
                    // --- État du run ---
                    this.isRunning = false;
                    this.isRunCompleted = false;
                    this.isRunSaved = false;
                    this.startTime = null;
                    this.elapsedTime = 0;
                    this.timerInterval = null;
                    
                    // --- Statistiques du run ---
                    this.actionHistory = []; // Historique détaillé des actions {type, time}

                    // --- Options chargées depuis le localStorage ---
                    this.theme = localStorage.getItem('theme') || 'light';
                    this.colorTheme = localStorage.getItem('colorTheme') || 'blue';
                    this.transitionStyle = localStorage.getItem('transitionStyle') || 'horizontal';
                    this.seuilMinimum = parseInt(localStorage.getItem('seuilMinimum')) || 0;
                    this.hapticEnabled = (localStorage.getItem('hapticEnabled') || 'true') === 'true';
                    this.shareEnabled = (localStorage.getItem('shareEnabled') || 'true') === 'true';
                    this.csvButtonsEnabled = (localStorage.getItem('csvButtonsEnabled') || 'true') === 'true';
                    this.advancedStatsVisible = (localStorage.getItem('advancedStatsVisible') || 'false') === 'true';
                    this.faultButtonEnabled = (localStorage.getItem('faultButtonEnabled') || 'false') === 'true';


                    // --- Initialisation ---
                    this.initializeApp();
                    this.bindEvents();
                    this.loadOptions();
                }

                /**
                 * Initialise l'application.
                 */
                initializeApp() {
                    document.body.setAttribute('data-theme', this.theme);
                    document.body.setAttribute('data-color-theme', this.colorTheme);
                    
                    const contentEl = document.querySelector('.content');
                    contentEl.classList.add('no-transition'); // Empêche l'animation au chargement
                    contentEl.className = `content transition-${this.transitionStyle}`;
                    
                    document.getElementById('versionDisplay').textContent = `Version ${this.version}`;
                    this.displayColorThemes();
                    this.loadDogProfiles();
                    this.updateCsvButtonsVisibility();
                    this.updateFaultButtonVisibility();

                    // Réactive les transitions après le premier rendu
                    setTimeout(() => {
                        contentEl.classList.remove('no-transition');
                    }, 100);
                }

                /**
                 * Lie tous les événements des éléments du DOM.
                 */
                bindEvents() {
                    // Navigation
                    document.getElementById('homeNav').addEventListener('click', () => this.navigateTo('measure'));
                    document.getElementById('historyNav').addEventListener('click', () => this.navigateTo('history'));
                    document.getElementById('optionsNav').addEventListener('click', () => this.navigateTo('options'));
                    
                    // Actions de mesure
                    document.getElementById('catchBtn').addEventListener('click', () => this.addAction('C'));
                    document.getElementById('dropBtn').addEventListener('click', () => this.addAction('D'));
                    document.getElementById('takeBtn').addEventListener('click', () => this.addAction('T'));
                    document.getElementById('faultBtn').addEventListener('click', () => this.addAction('F'));
                    document.getElementById('undoBtn').addEventListener('click', () => this.undoAction());
                    document.getElementById('endRunBtn').addEventListener('click', () => this.endRun());
                    
                    // Actions sur les résultats
                    document.getElementById('razBtn').addEventListener('click', () => this.confirmResetRun());
                    document.getElementById('saveBtn').addEventListener('click', () => this.showSaveModal());

                    // Actions des modales
                    document.getElementById('closeShareBtn').addEventListener('click', () => this.hideShareModal());
                    document.getElementById('cancelSaveBtn').addEventListener('click', () => this.hideSaveModal());
                    document.getElementById('confirmSaveBtn').addEventListener('click', () => this.saveRun());
                    document.getElementById('cancelUpdateBtn').addEventListener('click', () => this.hideEditModal());
                    document.getElementById('confirmUpdateBtn').addEventListener('click', () => this.updateRun());
                    document.getElementById('modalCancel').addEventListener('click', () => this.hideModal());
                    document.getElementById('modalConfirm').addEventListener('click', () => this.confirmModal());

                    // Formulaires et entrées
                    document.getElementById('dogNameInput').addEventListener('input', (e) => this.handleDogNameInput(e));
                    
                    // Options
                    document.getElementById('themeToggle').addEventListener('change', () => this.toggleTheme());
                    document.getElementById('transitionToggle').addEventListener('change', () => this.toggleTransition());
                    document.getElementById('hapticToggle').addEventListener('change', () => this.toggleHaptic());
                    document.getElementById('shareToggle').addEventListener('change', () => this.toggleShare());
                    document.getElementById('advancedStatsToggle').addEventListener('change', () => this.toggleAdvancedStats());
                    document.getElementById('csvToggle').addEventListener('change', () => this.toggleCsvButtons());
                    document.getElementById('faultButtonToggle').addEventListener('change', () => this.toggleFaultButton());
                    document.getElementById('seuilInput').addEventListener('change', (e) => this.updateSeuil(e));
                    document.getElementById('purgeBtn').addEventListener('click', () => this.purgeHistory());
                    document.getElementById('addDogBtn').addEventListener('click', () => this.addDogProfile());
                    document.getElementById('backupBtn').addEventListener('click', () => this.exportData());
                    document.getElementById('restoreInput').addEventListener('change', (e) => this.importData(e));
                    
                    // Historique
                    document.getElementById('searchInput').addEventListener('input', () => this.filterAndSortHistory());
                    document.getElementById('exportBtn').addEventListener('click', () => this.exportCSV());
                    document.getElementById('importCsvInput').addEventListener('change', (e) => this.handleCsvImport(e));
                    document.querySelectorAll('.sort-button').forEach(btn => btn.addEventListener('click', (e) => this.sortHistory(e.target.dataset.sort)));
                }

                vibrate(pattern = 50) {
                    if (this.hapticEnabled && window.navigator.vibrate) {
                        window.navigator.vibrate(pattern);
                    }
                }

                displayColorThemes() {
                    const container = document.getElementById('colorThemeSelector');
                    container.innerHTML = '';
                    this.colorThemes.forEach(theme => {
                        const swatch = document.createElement('div');
                        swatch.className = 'color-swatch';
                        swatch.style.backgroundColor = theme.color;
                        swatch.dataset.themeName = theme.name;
                        swatch.title = theme.name.charAt(0).toUpperCase() + theme.name.slice(1);
                        if (theme.name === this.colorTheme) {
                            swatch.classList.add('active');
                        }
                        swatch.addEventListener('click', () => this.updateColorTheme(theme.name));
                        container.appendChild(swatch);
                    });
                }
                
                updateColorTheme(themeName) {
                    this.colorTheme = themeName;
                    document.body.setAttribute('data-color-theme', this.colorTheme);
                    localStorage.setItem('colorTheme', this.colorTheme);
                    document.querySelectorAll('.color-swatch').forEach(swatch => {
                        swatch.classList.toggle('active', swatch.dataset.themeName === themeName);
                    });
                }

                loadDogProfiles() {
                    const profiles = localStorage.getItem('discDogProfiles');
                    if (profiles) {
                        this.dogProfiles = JSON.parse(profiles);
                    } else {
                        const history = JSON.parse(localStorage.getItem('discDogHistory') || '[]');
                        const uniqueNames = [...new Set(history.map(run => run.dogName))];
                        this.dogProfiles = uniqueNames.map(name => ({ id: Date.now() + Math.random(), name }));
                        this.saveDogProfiles();
                    }
                }

                saveDogProfiles() {
                    localStorage.setItem('discDogProfiles', JSON.stringify(this.dogProfiles));
                }

                displayDogProfiles() {
                    const list = document.getElementById('dogProfilesList');
                    list.innerHTML = '';
                    if (this.dogProfiles.length === 0) {
                        list.innerHTML = '<p style="text-align:center;color:#999;padding:10px 0;">Aucun profil.</p>';
                        return;
                    }
                    this.dogProfiles.sort((a, b) => a.name.localeCompare(b.name)).forEach(dog => {
                        const item = document.createElement('div');
                        item.className = 'dog-profile-item';
                        item.innerHTML = `
                            <span class="dog-profile-name">${dog.name}</span>
                            <div class="dog-profile-actions">
                                <button class="history-action-btn rename-dog-btn" title="Renommer"><span class="material-icons">edit</span></button>
                                <button class="history-action-btn delete-dog-btn" title="Supprimer"><span class="material-icons">delete</span></button>
                            </div>`;
                        item.querySelector('.rename-dog-btn').addEventListener('click', () => this.renameDogProfile(dog.id, dog.name));
                        item.querySelector('.delete-dog-btn').addEventListener('click', () => this.confirmDeleteDogProfile(dog.id, dog.name));
                        list.appendChild(item);
                    });
                }

                addDogProfile(name = null) {
                    const input = document.getElementById('newDogNameInput');
                    const newName = (name || input.value).trim();
                    if (newName && !this.dogProfiles.some(dog => dog.name.toLowerCase() === newName.toLowerCase())) {
                        this.dogProfiles.push({ id: Date.now(), name: newName });
                        this.saveDogProfiles();
                        this.displayDogProfiles();
                        if (input) input.value = '';
                    }
                }

                confirmDeleteDogProfile(dogId, dogName) {
                    const runsCount = JSON.parse(localStorage.getItem('discDogHistory') || '[]').filter(run => run.dogName.toLowerCase() === dogName.toLowerCase()).length;
                    let message = `Vraiment supprimer "${dogName}" ?`;
                    if (runsCount > 0) {
                        message += ` ${runsCount} run(s) associé(s) ne seront PAS supprimés.`;
                    }
                    this.showModal('Vraiment supprimer ce profil ?', () => {
                        this.dogProfiles = this.dogProfiles.filter(dog => dog.id !== dogId);
                        this.saveDogProfiles();
                        this.displayDogProfiles();
                    });
                }

                renameDogProfile(dogId, oldName) {
                    const newNamePrompt = prompt(`Renommer "${oldName}" :`, oldName);
                    if (newNamePrompt && newNamePrompt.trim() && newNamePrompt.trim() !== oldName) {
                        const newName = newNamePrompt.trim();
                        const dog = this.dogProfiles.find(d => d.id === dogId);
                        if (dog) dog.name = newName;
                        const history = JSON.parse(localStorage.getItem('discDogHistory') || '[]');
                        history.forEach(run => { if (run.dogName === oldName) run.dogName = newName; });
                        localStorage.setItem('discDogHistory', JSON.stringify(history));
                        this.saveDogProfiles();
                        this.displayDogProfiles();
                        this.loadHistory();
                    }
                }
                
                navigateTo(view) {
                    if (this.needsConfirmation() && view !== this.currentView) {
                        let message = this.isRunning ? 
                            'Un run est en cours. Si vous quittez, il sera annulé.' : 
                            'Le run n\'a pas été sauvegardé. Voulez-vous quitter sans sauvegarder ?';
                        this.showModal(message, () => {
                            this.resetRun(false);
                            this.showView(view);
                        });
                        return;
                    }
                    this.showView(view);
                }

                needsConfirmation() {
                    return this.isRunning || (this.isRunCompleted && !this.isRunSaved);
                }

                showView(viewId) {
                    const currentViewEl = document.querySelector('.view.active');
                    const nextViewEl = document.getElementById(viewId + 'View');

                    if (!nextViewEl || (currentViewEl === nextViewEl && this.currentView === viewId)) {
                        return;
                    }

                    document.querySelectorAll('.nav-icon').forEach(icon => icon.classList.remove('active'));
                    const navMap = { measure: 'homeNav', results: 'homeNav', history: 'historyNav', options: 'optionsNav' };
                    const activeNav = document.getElementById(navMap[viewId] || 'homeNav');
                    if (activeNav) activeNav.classList.add('active');

                    this.currentView = viewId;

                    if (currentViewEl) {
                        currentViewEl.classList.remove('active');
                        currentViewEl.classList.add('view-exit');
                    }

                    nextViewEl.classList.remove('view-exit');
                    nextViewEl.classList.add('active');
                    
                    if (viewId === 'history') this.loadHistory();
                    if (viewId === 'options') this.displayDogProfiles();
                }

                addAction(type) {
                    this.vibrate();
                    if (!this.isRunning && this.elapsedTime === 0) {
                        this.startTimer();
                    }
                    this.actionHistory.push({ type: type, time: this.elapsedTime });
                    this.updateDisplay();
                    this.updateLiveTimeline();
                }

                undoAction() {
                    this.vibrate();
                    if (!this.isRunning && this.isRunCompleted) {
                        this.isRunCompleted = false;
                        this.isRunSaved = false;
                        this.startTimer();
                        this.enableCountingButtons();
                        document.getElementById('endRunBtn').textContent = 'Fin du Run';
                        document.getElementById('undoBtn').textContent = 'UNDO';
                        return;
                    }
                    if (this.actionHistory.length === 0) return;
                    this.actionHistory.pop();
                    this.updateDisplay();
                    this.updateLiveTimeline();
                }
                
                startTimer() {
                    this.isRunning = true;
                    this.startTime = Date.now() - this.elapsedTime;
                    document.getElementById('timer').classList.add('running');
                    this.timerInterval = setInterval(() => {
                        this.elapsedTime = Date.now() - this.startTime;
                        this.updateTimer();
                    }, 100);
                }

                stopTimer() {
                    this.isRunning = false;
                    clearInterval(this.timerInterval);
                    this.timerInterval = null;
                }

                updateTimer() {
                    const totalSeconds = Math.floor(this.elapsedTime / 1000);
                    const minutes = String(Math.floor(totalSeconds / 60)).padStart(2, '0');
                    const seconds = String(totalSeconds % 60).padStart(2, '0');
                    const timerEl = document.getElementById('timer');
                    timerEl.textContent = `${minutes}:${seconds}`;

                    timerEl.className = 'timer running';
                    if (totalSeconds <= 80) timerEl.classList.add('green');
                    else if (totalSeconds <= 105) timerEl.classList.add('yellow');
                    else if (totalSeconds <= 120) timerEl.classList.add('red');
                    else timerEl.classList.add('critical');
                }

                endRun() {
                    if (this.isRunCompleted) {
                        this.showResults();
                        return;
                    }
                    this.stopTimer();
                    this.vibrate([50, 30, 50, 30, 50]);
                    this.isRunCompleted = true;
                    this.isRunSaved = false;
                    this.disableCountingButtons();
                    document.getElementById('timer').classList.remove('running');
                    document.getElementById('endRunBtn').textContent = 'RESULTS';
                    document.getElementById('undoBtn').textContent = 'Retour au Run';
                }

                enableCountingButtons() {
                    ['catchBtn', 'dropBtn', 'takeBtn', 'faultBtn'].forEach(id => document.getElementById(id).disabled = false);
                }
                
                disableCountingButtons() {
                    ['catchBtn', 'dropBtn', 'takeBtn', 'faultBtn'].forEach(id => document.getElementById(id).disabled = true);
                }
                
                showResults() {
                    const runStats = this.getRunStats();
                    const score = this.calculateScore(runStats.catches + runStats.takes, runStats.totalThrows);
                    
                    let mainSummary = `Catch Ratio : ${score.toFixed(1)} / ${runStats.totalThrows} lancés`;
                    if(this.seuilMinimum > 0 && runStats.totalThrows < this.seuilMinimum) mainSummary += ` (Seuil: ${this.seuilMinimum})`;
                    document.getElementById('scoreSummary').textContent = mainSummary;

                    // Mise à jour de la nouvelle structure compacte
                    document.getElementById('compactCatches').innerHTML = `<span class="stat-letter-c">C</span> ${runStats.catches} Catches`;
                    document.getElementById('compactTakes').innerHTML = `<span class="stat-letter-t">T</span> ${runStats.takes} Takes`;
                    document.getElementById('compactDrops').innerHTML = `<span class="stat-letter-d">D</span> ${runStats.drops} Drops`;

                    const totalSeconds = Math.floor(this.elapsedTime / 1000);
                    const minutes = String(Math.floor(totalSeconds / 60)).padStart(2, '0');
                    const seconds = String(totalSeconds % 60).padStart(2, '0');
                    document.getElementById('compactTime').textContent = `${minutes}:${seconds}`;
                    
                    this.displayTimelineForResults();
                    this.showView('results');
                }

                getRunStats() {
                    const stats = { catches: 0, drops: 0, takes: 0, faults: 0 };
                    this.actionHistory.forEach(action => {
                        if (action.type === 'C') stats.catches++;
                        else if (action.type === 'D') stats.drops++;
                        else if (action.type === 'T') stats.takes++;
                        else if (action.type === 'F') stats.faults++;
                    });
                    stats.totalThrows = this.actionHistory.filter(a => a.type !== 'F').length;
                    return stats;
                }

                calculateScore(success, total) {
                    const divisor = total < this.seuilMinimum ? this.seuilMinimum : total;
                    return divisor === 0 ? 0 : (success / divisor) * 10;
                }

                displayTimelineForResults() {
                    const timelineContainer = document.getElementById('timeline');
                    timelineContainer.innerHTML = '';
                    this.actionHistory.forEach(action => {
                        const span = document.createElement('span');
                        span.className = 'timeline-item';
                        span.textContent = action.type;
                        span.classList.add({ 'C': 'timeline-catch', 'D': 'timeline-drop', 'T': 'timeline-take', 'F': 'timeline-fault' }[action.type]);
                        timelineContainer.appendChild(span);
                    });
                }
                
                confirmResetRun() {
                    this.showModal('Vraiment réinitialiser ce run ?', () => this.resetRun(true));
                }
                
                resetRun(navigateToMeasure = true) {
                    this.stopTimer();
                    this.actionHistory = [];
                    this.elapsedTime = 0;
                    this.isRunning = false;
                    this.isRunCompleted = false;
                    this.isRunSaved = false;
                    this.lastRunData = null;
                    
                    this.enableCountingButtons();
                    document.getElementById('endRunBtn').textContent = 'Fin du Run';
                    document.getElementById('undoBtn').textContent = 'UNDO';
                    document.getElementById('timer').textContent = '00:00';
                    document.getElementById('timer').className = 'timer';
                    this.updateDisplay();
                    this.updateLiveTimeline();
                    if (navigateToMeasure) {
                        this.showView('measure');
                    }
                }

                updateDisplay() {
                    const stats = this.getRunStats();
                    document.getElementById('totalThrows').textContent = stats.totalThrows;
                }

                updateLiveTimeline() {
                    const timelineContainer = document.getElementById('liveTimeline');
                    if (this.actionHistory.length === 0) {
                        timelineContainer.innerHTML = '<span style="color:#999;font-style:italic;">Aucun lancer...</span>';
                        return;
                    }
                    timelineContainer.innerHTML = '';
                    this.actionHistory.forEach(action => {
                        const span = document.createElement('span');
                        span.className = 'live-timeline-item';
                        span.textContent = action.type;
                        span.classList.add({ 'C': 'live-timeline-catch', 'D': 'live-timeline-drop', 'T': 'live-timeline-take', 'F': 'live-timeline-fault' }[action.type]);
                        timelineContainer.appendChild(span);
                    });
                }
                
                showSaveModal() {
                    const modal = document.getElementById('saveModal');
                    modal.style.display = 'block';
                    setTimeout(() => modal.classList.add('visible'), 10);
                    document.getElementById('dogNameInput').focus();
                }

                hideSaveModal() {
                    const modal = document.getElementById('saveModal');
                    modal.classList.remove('visible');
                    setTimeout(() => modal.style.display = 'none', 300);
                }

                showEditModal(runId) {
                    const history = JSON.parse(localStorage.getItem('discDogHistory') || '[]');
                    const run = history.find(r => r.id === runId);
                    if (run) {
                        this.editingRunId = runId;
                        document.getElementById('editDogNameInput').value = run.dogName;
                        document.getElementById('editNotesInput').value = run.notes || '';
                        const modal = document.getElementById('editModal');
                        modal.style.display = 'block';
                        setTimeout(() => modal.classList.add('visible'), 10);
                    }
                }

                hideEditModal() {
                    const modal = document.getElementById('editModal');
                    modal.classList.remove('visible');
                    setTimeout(() => modal.style.display = 'none', 300);
                    this.editingRunId = null;
                }

                handleDogNameInput(e) {
                    const query = e.target.value;
                    this.showSuggestions(query.length >= 2 ? this.findDogNameSuggestions(query) : []);
                    this.updateTagPreview(query);
                }

                findDogNameSuggestions(query) {
                    return this.dogProfiles.filter(profile => profile.name.toLowerCase().includes(query.toLowerCase()));
                }

                showSuggestions(suggestions) {
                    const suggestionsBox = document.getElementById('suggestions');
                    suggestionsBox.innerHTML = '';
                    suggestions.forEach(profile => {
                        const item = document.createElement('div');
                        item.className = 'suggestion-item';
                        item.textContent = profile.name;
                        item.addEventListener('click', () => {
                            document.getElementById('dogNameInput').value = profile.name;
                            suggestionsBox.style.display = 'none';
                            this.updateTagPreview(profile.name);
                        });
                        suggestionsBox.appendChild(item);
                    });
                    suggestionsBox.style.display = suggestions.length > 0 ? 'block' : 'none';
                }
                
                updateTagPreview(name) {
                    const previewEl = document.getElementById('tagPreview');
                    previewEl.textContent = name.trim() ? `Sera sauvegardé comme : #Run${this.getNextRunNumber(name)}` : '';
                }

                getNextRunNumber(dogName) {
                    const history = JSON.parse(localStorage.getItem('discDogHistory') || '[]');
                    return history.filter(run => run.dogName.toLowerCase() === dogName.toLowerCase()).length + 1;
                }

                saveRun() {
                    const dogName = document.getElementById('dogNameInput').value.trim();
                    if (!dogName) {
                        alert('Veuillez entrer le nom du chien');
                        return;
                    }
                    if (!this.dogProfiles.some(profile => profile.name.toLowerCase() === dogName.toLowerCase())) {
                        this.addDogProfile(dogName);
                    }

                    const history = JSON.parse(localStorage.getItem('discDogHistory') || '[]');
                    const runStats = this.getRunStats();
                    const advancedStats = this.calculateAdvancedStats(this.actionHistory, this.elapsedTime);

                    const newRun = {
                        id: Date.now(),
                        dogName: dogName,
                        runTag: `#Run${this.getNextRunNumber(dogName)}`,
                        date: (new Date()).toLocaleDateString('fr-FR'),
                        time: (new Date()).toLocaleTimeString('fr-FR'),
                        score: this.calculateScore(runStats.catches + runStats.takes, runStats.totalThrows),
                        totalThrows: runStats.totalThrows,
                        catches: runStats.catches,
                        drops: runStats.drops,
                        takes: runStats.takes,
                        faults: runStats.faults,
                        elapsedTime: this.elapsedTime,
                        seuilMinimum: this.seuilMinimum,
                        actionHistory: this.actionHistory,
                        advancedStats: advancedStats,
                        notes: document.getElementById('notesInput').value.trim(),
                    };
                    history.push(newRun);
                    localStorage.setItem('discDogHistory', JSON.stringify(history));
                    this.isRunSaved = true;
                    this.hideSaveModal();
                    this.resetRun();
                }

                updateRun() {
                    const history = JSON.parse(localStorage.getItem('discDogHistory') || '[]');
                    const runIndex = history.findIndex(run => run.id === this.editingRunId);
                    if (runIndex !== -1) {
                        history[runIndex].dogName = document.getElementById('editDogNameInput').value.trim();
                        history[runIndex].notes = document.getElementById('editNotesInput').value.trim();
                        localStorage.setItem('discDogHistory', JSON.stringify(history));
                        this.hideEditModal();
                        this.loadHistory();
                    }
                }

                confirmDeleteRun(runId) {
                    this.showModal('Vraiment supprimer ce run ?', () => this.deleteRun(runId));
                }

                deleteRun(runId) {
                    let history = JSON.parse(localStorage.getItem('discDogHistory') || '[]');
                    let updatedHistory = history.filter(run => run.id !== runId);
                    localStorage.setItem('discDogHistory', JSON.stringify(updatedHistory));
                    this.loadHistory();
                }
                
                loadHistory() {
                    this.filterAndSortHistory();
                }

                displayHistory(runs) {
                    const list = document.getElementById('historyList');
                    list.innerHTML = '';
                    if (runs.length === 0) {
                        list.innerHTML = '<p style="text-align:center;color:#999;padding:20px 0;">Aucun run correspondant.</p>';
                        return;
                    }
                    
                    runs.forEach(run => {
                        const item = document.createElement('div');
                        item.className = 'history-item';
                        const totalSeconds = Math.floor((run.elapsedTime || 0) / 1000);
                        const minutes = String(Math.floor(totalSeconds / 60)).padStart(2, '0');
                        const seconds = String(totalSeconds % 60).padStart(2, '0');
                
                        if (!run.advancedStats && run.actionHistory) {
                            run.advancedStats = this.calculateAdvancedStats(run.actionHistory, run.elapsedTime);
                        }
                        const stats = run.advancedStats || { startRatio: -1, endRatio: -1, bestStreak: 0, bestStreakSequence: [], consistency: { firstHalf: -1, secondHalf: -1 } };

                        item.innerHTML = `
                            <div class="history-header">
                                <div class="history-header-info">
                                    <span class="dog-name">${run.dogName}</span> <span class="run-tag">${run.runTag}</span>
                                </div>
                                <div class="history-item-actions">
                                     <button class="history-action-btn share-btn" title="Partager"><span class="material-icons">share</span></button>
                                    <button class="history-action-btn edit-btn" title="Modifier"><span class="material-icons">edit</span></button>
                                    <button class="history-action-btn delete-btn" title="Supprimer"><span class="material-icons">delete</span></button>
                                </div>
                            </div>
                            <div class="history-details">
                                <div><strong>Date:</strong> ${run.date}</div>
                                <div><strong>Heure:</strong> ${run.time}</div>
                                <div><strong>Temps:</strong> ${minutes}:${seconds}</div>
                                <div><strong>Catch Ratio:</strong> <span class="history-score">${run.score.toFixed(1)}</span></div>
                                <div><strong>Lancés:</strong> ${run.totalThrows}${run.totalThrows < run.seuilMinimum ? ` (S${run.seuilMinimum})` : ''}</div>
                                <div><strong>Catches:</strong> ${run.catches}</div>
                                <div><strong>Drops:</strong> ${run.drops}</div>
                                <div><strong>Takes:</strong> ${run.takes}</div>
                            </div>
                            <div class="advanced-stats-container" style="display: ${this.advancedStatsVisible ? 'block' : 'none'};">
                                <div class="advanced-stats-title">Statistiques Avancées</div>
                                <div class="advanced-stat">
                                    <span class="stat-label">Ratio Début (30s)</span>
                                    <span class="stat-value">${stats.startRatio === -1 ? 'N/A' : `${stats.startRatio.toFixed(1)}`}</span>
                                </div>
                                <div class="advanced-stat">
                                    <span class="stat-label">Ratio Fin (30s)</span>
                                    <span class="stat-value">${stats.endRatio === -1 ? 'N/A' : `${stats.endRatio.toFixed(1)}`}</span>
                                </div>
                                <div class="advanced-stat">
                                    <span class="stat-label">Meilleure Série</span>
                                    <span class="stat-value">${stats.bestStreak}</span>
                                </div>
                                <div class="advanced-stat">
                                    <span class="stat-label">Consistance</span>
                                    <span class="stat-value">${stats.consistency.firstHalf === -1 ? 'N/A' : `${stats.consistency.firstHalf.toFixed(1)} &rarr; ${stats.consistency.secondHalf.toFixed(1)}`}</span>
                                </div>
                            </div>
                            ${run.notes ? `<div class="history-notes">${run.notes}</div>` : ''}
                        `;
                        item.querySelector('.edit-btn').addEventListener('click', () => this.showEditModal(run.id));
                        item.querySelector('.delete-btn').addEventListener('click', () => this.confirmDeleteRun(run.id));
                        item.querySelector('.share-btn').addEventListener('click', () => this.generateShareImage(run));
                
                        list.appendChild(item);
                    });
                }
                
                sortHistory(sortBy) {
                    document.querySelectorAll('.sort-button').forEach(btn => btn.classList.remove('active'));
                    document.querySelector(`.sort-button[data-sort="${sortBy}"]`)?.classList.add('active');
                    this.filterAndSortHistory();
                }

                filterAndSortHistory() {
                    const searchTerm = document.getElementById('searchInput').value.toLowerCase();
                    const activeSort = document.querySelector('.sort-button.active')?.dataset.sort || 'date';
                    let runs = JSON.parse(localStorage.getItem('discDogHistory') || '[]');

                    // 1. Filtrer par terme de recherche
                    if (searchTerm) {
                        runs = runs.filter(run => 
                            run.dogName.toLowerCase().includes(searchTerm) ||
                            (run.notes && run.notes.toLowerCase().includes(searchTerm))
                        );
                    }

                    // 2. Trier les résultats filtrés
                    const sortFunctions = {
                        'name': (a, b) => a.dogName.localeCompare(b.dogName),
                        'score': (a, b) => b.score - a.score,
                        'date': (a, b) => b.id - a.id
                    };
                    runs.sort(sortFunctions[activeSort] || sortFunctions['date']);

                    this.displayHistory(runs);
                }

                calculateAdvancedStats(actions, totalTime) {
                    if (!actions || actions.length === 0) {
                        return { startRatio: -1, endRatio: -1, bestStreak: 0, bestStreakSequence: [], consistency: { firstHalf: -1, secondHalf: -1 } };
                    }

                    const startActions = actions.filter(a => a.time <= 30000);
                    const startSuccess = startActions.filter(a => a.type === 'C' || a.type === 'T').length;
                    const startRatio = startActions.length > 0 ? (startSuccess / startActions.length) * 10 : -1;

                    const endActions = actions.filter(a => a.time > (totalTime - 30000));
                    const endSuccess = endActions.filter(a => a.type === 'C' || a.type === 'T').length;
                    const endRatio = endActions.length > 0 ? (endSuccess / endActions.length) * 10 : -1;

                    let bestStreak = 0;
                    let currentStreak = 0;
                    let bestStreakSequence = [];
                    let currentStreakSequence = [];
                    actions.forEach(action => {
                        if (action.type === 'C' || action.type === 'T') {
                            currentStreak++;
                            currentStreakSequence.push(action.type);
                        } else {
                            if (currentStreak > bestStreak) {
                                bestStreak = currentStreak;
                                bestStreakSequence = [...currentStreakSequence];
                            }
                            currentStreak = 0;
                            currentStreakSequence = [];
                        }
                    });
                    if (currentStreak > bestStreak) {
                        bestStreak = currentStreak;
                        bestStreakSequence = [...currentStreakSequence];
                    }

                    const midPoint = Math.floor(actions.length / 2);
                    const firstHalfActions = actions.slice(0, midPoint);
                    const secondHalfActions = actions.slice(midPoint);
                    const firstHalfSuccess = firstHalfActions.filter(a => a.type === 'C' || a.type === 'T').length;
                    const firstHalfRatio = firstHalfActions.length > 0 ? (firstHalfSuccess / firstHalfActions.length) * 10 : -1;
                    const secondHalfSuccess = secondHalfActions.filter(a => a.type === 'C' || a.type === 'T').length;
                    const secondHalfRatio = secondHalfActions.length > 0 ? (secondHalfSuccess / secondHalfActions.length) * 10 : -1;
                    
                    return { startRatio, endRatio, bestStreak, bestStreakSequence, consistency: { firstHalf: firstHalfRatio, secondHalf: secondHalfRatio } };
                }

                loadOptions() {
                    document.getElementById('themeToggle').checked = this.theme === 'dark';
                    document.getElementById('themeLabel').textContent = this.theme === 'dark' ? 'Sombre' : 'Clair';
                    
                    document.getElementById('transitionToggle').checked = this.transitionStyle === 'vertical';
                    document.getElementById('transitionLabel').textContent = this.transitionStyle === 'vertical' ? 'Verticale' : 'Horizontale';
                    
                    document.getElementById('hapticToggle').checked = this.hapticEnabled;
                    document.getElementById('shareToggle').checked = this.shareEnabled;
                    document.getElementById('advancedStatsToggle').checked = this.advancedStatsVisible;
                    document.getElementById('csvToggle').checked = this.csvButtonsEnabled;
                    document.getElementById('csvLabel').textContent = this.csvButtonsEnabled ? 'Activé' : 'Désactivé';
                    
                    document.getElementById('faultButtonToggle').checked = this.faultButtonEnabled;
                    document.getElementById('faultButtonLabel').textContent = this.faultButtonEnabled ? 'Activé' : 'Désactivé';

                    document.getElementById('seuilInput').value = this.seuilMinimum;
                    this.updateColorTheme(this.colorTheme);
                }

                toggleTheme() {
                    this.theme = this.theme === 'light' ? 'dark' : 'light';
                    document.body.setAttribute('data-theme', this.theme);
                    localStorage.setItem('theme', this.theme);
                    document.getElementById('themeLabel').textContent = this.theme === 'dark' ? 'Sombre' : 'Clair';
                }

                toggleTransition() {
                    this.transitionStyle = this.transitionStyle === 'horizontal' ? 'vertical' : 'horizontal';
                    localStorage.setItem('transitionStyle', this.transitionStyle);
                    document.getElementById('transitionLabel').textContent = this.transitionStyle === 'vertical' ? 'Verticale' : 'Horizontale';
                    const contentEl = document.querySelector('.content');
                    contentEl.className = 'content'; // Reset classes
                    contentEl.classList.add(`transition-${this.transitionStyle}`);
                }
                
                toggleHaptic() {
                    this.hapticEnabled = !this.hapticEnabled;
                    localStorage.setItem('hapticEnabled', String(this.hapticEnabled));
                }
                
                toggleShare() {
                    this.shareEnabled = !this.shareEnabled;
                    localStorage.setItem('shareEnabled', String(this.shareEnabled));
                }

                toggleAdvancedStats() {
                    this.advancedStatsVisible = !this.advancedStatsVisible;
                    localStorage.setItem('advancedStatsVisible', String(this.advancedStatsVisible));
                    this.loadHistory();
                }

                toggleCsvButtons() {
                    this.csvButtonsEnabled = !this.csvButtonsEnabled;
                    localStorage.setItem('csvButtonsEnabled', String(this.csvButtonsEnabled));
                    document.getElementById('csvLabel').textContent = this.csvButtonsEnabled ? 'Activé' : 'Désactivé';
                    this.updateCsvButtonsVisibility();
                }

                toggleFaultButton() {
                    this.faultButtonEnabled = !this.faultButtonEnabled;
                    localStorage.setItem('faultButtonEnabled', String(this.faultButtonEnabled));
                    document.getElementById('faultButtonLabel').textContent = this.faultButtonEnabled ? 'Activé' : 'Désactivé';
                    this.updateFaultButtonVisibility();
                }

                updateFaultButtonVisibility() {
                    const faultBtn = document.getElementById('faultBtn');
                    if (faultBtn) {
                        faultBtn.style.display = this.faultButtonEnabled ? 'block' : 'none';
                    }
                }

                updateCsvButtonsVisibility() {
                    const importBtnLabel = document.querySelector('label[for="importCsvInput"]');
                    const exportBtn = document.getElementById('exportBtn');
                    if (importBtnLabel && exportBtn) {
                        const displayStyle = this.csvButtonsEnabled ? '' : 'none';
                        importBtnLabel.style.display = displayStyle;
                        exportBtn.style.display = displayStyle;
                    }
                }

                updateSeuil(e) {
                    this.seuilMinimum = parseInt(e.target.value, 10) || 0;
                    localStorage.setItem('seuilMinimum', String(this.seuilMinimum));
                }

                purgeHistory() {
                    this.showModal('Vraiment supprimer tout l\'historique ? Cette action est irréversible.', () => {
                        localStorage.removeItem('discDogHistory');
                        this.loadHistory();
                    });
                }
                
                exportData() {
                    const dataToBackup = {};
                    // Itérer sur toutes les clés du localStorage pour créer un objet complet
                    for (let i = 0; i < localStorage.length; i++) {
                        const key = localStorage.key(i);
                        dataToBackup[key] = localStorage.getItem(key);
                    }

                    const jsonString = JSON.stringify(dataToBackup, null, 2);
                    const blob = new Blob([jsonString], { type: 'application/json' });
                    const url = URL.createObjectURL(blob);
                    
                    const a = document.createElement('a');
                    const date = new Date().toISOString().slice(0, 10);
                    a.href = url;
                    a.download = `catch-ratio-backup-${date}.json`;
                    document.body.appendChild(a);
                    a.click();
                    document.body.removeChild(a);
                    URL.revokeObjectURL(url);
                }

                importData(event) {
                    const file = event.target.files[0];
                    if (!file) {
                        return;
                    }

                    const reader = new FileReader();
                    reader.onload = (e) => {
                        try {
                            const data = JSON.parse(e.target.result);
                            // Valider que le fichier semble correct (optionnel mais recommandé)
                            if (data.discDogHistory && data.discDogProfiles) {
                                // Supprimer les anciennes données avant de restaurer
                                localStorage.clear();
                                // Itérer sur les nouvelles données et les sauvegarder
                                for (const key in data) {
                                    if (Object.hasOwnProperty.call(data, key)) {
                                        localStorage.setItem(key, data[key]);
                                    }
                                }
                                // Afficher un message et recharger la page
                                this.showModal("Restauration réussie. L'application va maintenant se recharger pour appliquer les modifications.", () => {
                                    location.reload();
                                });
                            } else {
                                this.showModal("Erreur : Le fichier de sauvegarde semble invalide ou corrompu.", null);
                            }
                        } catch (error) {
                            this.showModal(`Erreur lors de la lecture du fichier : ${error.message}`, null);
                        }
                    };
                    reader.readAsText(file);
                    // Réinitialiser la valeur de l'input pour permettre de sélectionner le même fichier à nouveau
                    event.target.value = '';
                }

                handleCsvImport(event) {
                    const file = event.target.files[0];
                    if (!file) return;

                    const reader = new FileReader();
                    reader.onload = (e) => {
                        const text = e.target.result;
                        console.log("La fonction d'importation doit être mise à jour pour la nouvelle structure de données.");
                    };
                    reader.readAsText(file, 'UTF-8');
                }

                showImportStatus(message, type) {
                    const statusEl = document.getElementById('importStatus');
                    statusEl.textContent = message;
                    statusEl.style.color = type === 'error' ? '#F44336' : '#4CAF50';
                    setTimeout(() => statusEl.textContent = '', 5000);
                }

                exportCSV() {
                    console.log("La fonction d'exportation doit être mise à jour pour la nouvelle structure de données.");
                    alert("La fonction d'exportation doit être mise à jour.");
                }

                showShareModal() {
                    const modal = document.getElementById('shareModal');
                    modal.style.display = 'block';
                    setTimeout(() => modal.classList.add('visible'), 10);
                }

                hideShareModal() {
                    const modal = document.getElementById('shareModal');
                    modal.classList.remove('visible');
                    setTimeout(() => modal.style.display = 'none', 300);
                }
                
                generateShareImage(runData = null) {
                    const dataToShare = runData || this.lastRunData;
                    if (!dataToShare) return;

                    this.showShareModal();
                    const imageContainer = document.getElementById('shareImageContainer');
                    imageContainer.innerHTML = '<div class="loader"></div>';

                    const dateToFormat = dataToShare.date instanceof Date ? dataToShare.date : new Date(dataToShare.date.split('/').reverse().join('-') + 'T' + (dataToShare.time || "12:00:00"));
                    
                    document.getElementById('shareDogName').textContent = dataToShare.dogName || 'N/A';
                    document.getElementById('shareDate').textContent = dateToFormat.toLocaleDateString('fr-FR', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
                    document.getElementById('shareRunTag').textContent = (dataToShare.runTag || '').replace('#Run', 'Run ');
                    document.getElementById('shareScore').textContent = dataToShare.score.toFixed(1);
                    document.getElementById('shareCatches').textContent = dataToShare.catches;
                    document.getElementById('shareDrops').textContent = dataToShare.drops;
                    document.getElementById('shareTakes').textContent = dataToShare.takes;
                    document.getElementById('shareTotal').textContent = dataToShare.totalThrows;
                    
                    const timelineContainer = document.getElementById('shareTimeline');
                    timelineContainer.innerHTML = '';
                    const actions = dataToShare.actionHistory ? dataToShare.actionHistory.map(a => a.type) : [];
                    actions.forEach(item => {
                        const span = document.createElement('span');
                        span.className = 'timeline-item';
                        span.textContent = item;
                        const classMap = { 'C': 'timeline-catch', 'D': 'timeline-drop', 'T': 'timeline-take', 'F': 'timeline-fault' };
                        span.classList.add(classMap[item]);
                        timelineContainer.appendChild(span);
                    });

                    const advancedStatsContainer = document.getElementById('shareAdvancedStats');
                    advancedStatsContainer.innerHTML = '';
                    if (this.advancedStatsVisible && dataToShare.advancedStats) {
                        const stats = dataToShare.advancedStats;
                
                        advancedStatsContainer.innerHTML = `
                            <div class="share-advanced-stat"><strong>Ratio Début (30s):</strong><span>${stats.startRatio === -1 ? 'N/A' : stats.startRatio.toFixed(1)}</span></div>
                            <div class="share-advanced-stat"><strong>Ratio Fin (30s):</strong><span>${stats.endRatio === -1 ? 'N/A' : stats.endRatio.toFixed(1)}</span></div>
                            <div class="share-advanced-stat"><strong>Meilleure Série:</strong><div id="streak-timeline-container" class="share-streak-timeline"></div></div>
                            <div class="share-advanced-stat"><strong>Consistance:</strong><span>${stats.consistency.firstHalf === -1 ? 'N/A' : `${stats.consistency.firstHalf.toFixed(1)} &rarr; ${stats.consistency.secondHalf.toFixed(1)}`}</span></div>
                        `;
                        
                        const streakContainer = advancedStatsContainer.querySelector('#streak-timeline-container');
                        if (stats.bestStreakSequence && stats.bestStreakSequence.length > 0) {
                            stats.bestStreakSequence.forEach(actionType => {
                                const span = document.createElement('span');
                                span.className = 'timeline-item';
                                span.textContent = actionType;
                                span.classList.add({ 'C': 'timeline-catch', 'D': 'timeline-drop', 'T': 'timeline-take' }[actionType]);
                                streakContainer.appendChild(span);
                            });
                        } else {
                            streakContainer.textContent = "0";
                        }
                    }

                    const card = document.getElementById('shareCard');
                    setTimeout(() => {
                        html2canvas(card, {
                            backgroundColor: getComputedStyle(document.body).getPropertyValue('--app-bg'),
                            useCORS: true,
                            scale: 2
                        }).then(canvas => {
                            const imageUrl = canvas.toDataURL('image/png');
                            imageContainer.innerHTML = `<img src="${imageUrl}" alt="Résumé du run">`;
                            
                            const downloadBtn = document.getElementById('downloadShareBtn');
                            if (this.shareEnabled) {
                                downloadBtn.href = imageUrl;
                                downloadBtn.classList.remove('disabled');
                                downloadBtn.removeAttribute('title');
                            } else {
                                downloadBtn.href = 'javascript:void(0)';
                                downloadBtn.classList.add('disabled');
                                downloadBtn.title = "La fonction de partage est désactivée dans les options.";
                            }

                        }).catch(err => {
                            console.error("Oops, something went wrong!", err);
                            imageContainer.textContent = "Erreur lors de la génération de l'image.";
                        });
                    }, 500);
                }

                showModal(message, callback) {
                    const modal = document.getElementById('confirmModal');
                    document.getElementById('modalMessage').textContent = message;
                    modal.style.display = 'block';
                    setTimeout(() => modal.classList.add('visible'), 10);
                    this.modalConfirmCallback = callback;
                }

                hideModal() {
                    const modal = document.getElementById('confirmModal');
                    modal.classList.remove('visible');
                    setTimeout(() => modal.style.display = 'none', 300);
                    this.modalConfirmCallback = null;
                }

                confirmModal() {
                    if (this.modalConfirmCallback) {
                        this.modalConfirmCallback();
                    }
                    this.hideModal();
                }
            }

            new DiscDogScorer();
        });
    </script>
</body>
</html>
