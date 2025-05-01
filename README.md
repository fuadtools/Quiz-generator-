<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Advanced Quiz Video Generator</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        body {
            font-family: 'Arial', sans-serif;
            padding: 10px;
            background-color: #f5f5f5;
            color: #333;
            overflow-x: hidden;
            width: 100%;
            min-width: 320px;
        }
        .container {
            max-width: 100%;
            margin: 0 auto;
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        h1, h2 {
            text-align: center;
            color: #2c3e50;
            margin-bottom: 10px;
            word-wrap: break-word;
        }
        h1 { font-size: 1.5rem; }
        h2 { font-size: 1.2rem; margin-top: 5px; }
        .section {
            background-color: white;
            padding: 12px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            width: 100%;
        }
        textarea {
            width: 100%;
            min-height: 120px;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-family: monospace;
            resize: vertical;
            font-size: 14px;
        }
        .preview-container {
            position: relative;
            margin: 10px auto;
            overflow: hidden;
            background-size: cover;
            background-position: center;
            width: 100%;
            aspect-ratio: 16/9;
            border: 1px solid #ddd;
        }
        .video-frame {
            position: relative;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            gap: 5px;
        }
        .question-box, .answer-box {
            margin: 5px;
            padding: 12px;
            border-radius: 8px;
            text-align: center;
            max-width: 90%;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
            transition: all 0.5s ease;
            position: relative;
            width: 80%;
            line-height: 1.5;
            word-wrap: break-word;
        }
        .question-box {
            background-color: #3498db;
            color: white;
            opacity: 0;
        }
        .question-box.visible { opacity: 1; }
        .answer-box {
            background-color: #2ecc71;
            color: white;
            opacity: 0;
            display: none;
        }
        .answer-box.visible {
            opacity: 1;
            display: block;
        }
        /* Animation classes for questions */
        .question-slide-up.visible { animation: questionSlideUp 0.5s ease forwards; }
        .question-slide-down.visible { animation: questionSlideDown 0.5s ease forwards; }
        .question-slide-left.visible { animation: questionSlideLeft 0.5s ease forwards; }
        .question-slide-right.visible { animation: questionSlideRight 0.5s ease forwards; }
        .question-zoom-in.visible { animation: questionZoomIn 0.5s ease forwards; }
        .question-rotate-in.visible { animation: questionRotateIn 0.5s ease forwards; }
        .question-bounce-in.visible { animation: questionBounceIn 0.5s ease forwards; }
        .question-fade-in.visible { animation: questionFadeIn 0.5s ease forwards; }
        .question-flip-in.visible { animation: questionFlipIn 0.5s ease forwards; }
        
        /* Animation classes for answers */
        .answer-slide-up.visible { animation: answerSlideUp 0.5s ease forwards; }
        .answer-slide-down.visible { animation: answerSlideDown 0.5s ease forwards; }
        .answer-slide-left.visible { animation: answerSlideLeft 0.5s ease forwards; }
        .answer-slide-right.visible { animation: answerSlideRight 0.5s ease forwards; }
        .answer-zoom-in.visible { animation: answerZoomIn 0.5s ease forwards; }
        .answer-rotate-in.visible { animation: answerRotateIn 0.5s ease forwards; }
        .answer-bounce-in.visible { animation: answerBounceIn 0.5s ease forwards; }
        .answer-fade-in.visible { animation: answerFadeIn 0.5s ease forwards; }
        .answer-flip-in.visible { animation: answerFlipIn 0.5s ease forwards; }

        @keyframes questionSlideUp {
            0% { opacity: 0; transform: translateY(50px); }
            100% { opacity: 1; transform: translateY(0); }
        }
        @keyframes questionSlideDown {
            0% { opacity: 0; transform: translateY(-50px); }
            100% { opacity: 1; transform: translateY(0); }
        }
        @keyframes questionSlideLeft {
            0% { opacity: 0; transform: translateX(50px); }
            100% { opacity: 1; transform: translateX(0); }
        }
        @keyframes questionSlideRight {
            0% { opacity: 0; transform: translateX(-50px); }
            100% { opacity: 1; transform: translateX(0); }
        }
        @keyframes questionZoomIn {
            0% { opacity: 0; transform: scale(0.5); }
            100% { opacity: 1; transform: scale(1); }
        }
        @keyframes questionRotateIn {
            0% { opacity: 0; transform: rotate(-180deg) scale(0.5); }
            100% { opacity: 1; transform: rotate(0) scale(1); }
        }
        @keyframes questionBounceIn {
            0% { opacity: 0; transform: scale(0.3); }
            50% { opacity: 1; transform: scale(1.1); }
            70% { transform: scale(0.9); }
            100% { opacity: 1; transform: scale(1); }
        }
        @keyframes questionFadeIn { 
            0% { opacity: 0; } 
            100% { opacity: 1; } 
        }
        @keyframes questionFlipIn {
            0% { opacity: 0; transform: perspective(400px) rotateY(90deg); }
            40% { transform: perspective(400px) rotateY(-20deg); }
            70% { transform: perspective(400px) rotateY(10deg); }
            100% { opacity: 1; transform: perspective(400px) rotateY(0); }
        }
        @keyframes answerSlideUp {
            0% { opacity: 0; transform: translateY(50px); }
            100% { opacity: 1; transform: translateY(0); }
        }
        @keyframes answerSlideDown {
            0% { opacity: 0; transform: translateY(-50px); }
            100% { opacity: 1; transform: translateY(0); }
        }
        @keyframes answerSlideLeft {
            0% { opacity: 0; transform: translateX(50px); }
            100% { opacity: 1; transform: translateX(0); }
        }
        @keyframes answerSlideRight {
            0% { opacity: 0; transform: translateX(-50px); }
            100% { opacity: 1; transform: translateX(0); }
        }
        @keyframes answerZoomIn {
            0% { opacity: 0; transform: scale(0.5); }
            100% { opacity: 1; transform: scale(1); }
        }
        @keyframes answerRotateIn {
            0% { opacity: 0; transform: rotate(-180deg) scale(0.5); }
            100% { opacity: 1; transform: rotate(0) scale(1); }
        }
        @keyframes answerBounceIn {
            0% { opacity: 0; transform: scale(0.3); }
            50% { opacity: 1; transform: scale(1.1); }
            70% { transform: scale(0.9); }
            100% { opacity: 1; transform: scale(1); }
        }
        @keyframes answerFadeIn { 0% { opacity: 0; } 100% { opacity: 1; } }
        @keyframes answerFlipIn {
            0% { opacity: 0; transform: perspective(400px) rotateY(90deg); }
            40% { transform: perspective(400px) rotateY(-20deg); }
            70% { transform: perspective(400px) rotateY(10deg); }
            100% { opacity: 1; transform: perspective(400px) rotateY(0); }
        }
        .countdown {
            font-size: 1.8rem;
            font-weight: bold;
            color: #e74c3c;
            position: absolute;
            opacity: 0;
            transition: opacity 0.3s ease;
            z-index: 10;
        }
        .countdown.visible { opacity: 1; }
        .branding-container {
            position: absolute;
            z-index: 10;
            max-width: 30%;
        }
        .branding-logo {
            max-width: 100%;
            max-height: 80px;
        }
        .branding-text {
            color: white;
            padding: 4px 8px;
            border-radius: 4px;
            background-color: rgba(0,0,0,0.3);
            font-size: 14px;
        }
        #bg-video-element {
            position: absolute;
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: none;
        }
        
        /* New Settings Panel Style */
        .settings-panel {
            display: none;
            position: relative;
            margin-bottom: 15px;
        }
        .settings-panel.active {
            display: block;
        }
        .settings-scroller {
            display: flex;
            overflow-x: auto;
            padding: 10px 0;
            gap: 10px;
            scrollbar-width: thin;
            scrollbar-color: #3498db #f0f0f0;
        }
        .settings-scroller::-webkit-scrollbar {
            height: 6px;
        }
        .settings-scroller::-webkit-scrollbar-track {
            background: #f0f0f0;
            border-radius: 3px;
        }
        .settings-scroller::-webkit-scrollbar-thumb {
            background-color: #3498db;
            border-radius: 3px;
        }
        .settings-category {
            flex: 0 0 auto;
            width: 180px;
            background: #3498db;
            color: white;
            border-radius: 8px;
            padding: 12px;
            cursor: pointer;
            transition: all 0.2s;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        .settings-category:hover {
            background: #2980b9;
            transform: translateY(-2px);
        }
        .settings-category i {
            margin-right: 8px;
            font-size: 1.1rem;
        }
        .settings-category-title {
            font-weight: bold;
            font-size: 0.9rem;
        }
        .settings-content {
            display: none;
            position: absolute;
            top: 100%;
            left: 0;
            right: 0;
            background: white;
            border-radius: 0 0 8px 8px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.15);
            padding: 15px;
            z-index: 10;
            max-height: 70vh;
            overflow-y: auto;
        }
        .settings-content.active {
            display: block;
        }
        .settings-group {
            margin-bottom: 15px;
        }
        .settings-group h3 {
            font-size: 0.9rem;
            color: #2c3e50;
            margin-bottom: 8px;
            display: flex;
            align-items: center;
            padding-bottom: 5px;
            border-bottom: 1px solid #eee;
        }
        .settings-group h3 i {
            margin-right: 8px;
            font-size: 0.9rem;
            color: #3498db;
        }
        .control-item {
            margin-bottom: 8px;
        }
        .control-item label {
            display: block;
            font-size: 0.75rem;
            font-weight: bold;
            color: #555;
            margin-bottom: 3px;
        }
        .control-item select, 
        .control-item input:not([type="color"]):not([type="checkbox"]):not([type="range"]), 
        .control-item button {
            width: 100%;
            padding: 5px 8px;
            font-size: 0.75rem;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        .control-item input[type="color"] {
            width: 100%;
            height: 30px;
            padding: 0;
            border: 1px solid #ddd;
        }
        .control-item input[type="range"] {
            width: 100%;
        }
        .control-item input[type="checkbox"] {
            margin-right: 5px;
        }
        .control-row {
            display: flex;
            align-items: center;
            margin-bottom: 5px;
        }
        .control-row label {
            flex: 1;
            margin-bottom: 0;
        }
        .control-row input[type="number"] {
            width: 50px;
            margin-left: 5px;
        }
        .mini-btn {
            width: 25px;
            height: 25px;
            padding: 0;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            margin: 0 2px;
            font-size: 0.7rem;
            background: #f0f0f0;
            border: 1px solid #ddd;
            border-radius: 3px;
            cursor: pointer;
        }
        .mini-btn:hover {
            background: #e0e0f0;
        }
        .mini-btn.active {
            background: #3498db;
            color: white;
            border-color: #2980b9;
        }
        .file-input-wrapper {
            position: relative;
            overflow: hidden;
            display: inline-block;
            width: 100%;
        }
        .file-input-wrapper input[type="file"] {
            font-size: 0;
            position: absolute;
            left: 0;
            top: 0;
            opacity: 0;
            width: 100%;
            height: 100%;
            cursor: pointer;
        }
        .file-input-btn {
            display: block;
            padding: 5px 8px;
            font-size: 0.75rem;
            background: #f0f0f0;
            border: 1px solid #ddd;
            border-radius: 4px;
            text-align: center;
            cursor: pointer;
        }
        .file-input-btn:hover {
            background: #e0e0e0;
        }
        .file-name {
            font-size: 0.7rem;
            margin-top: 3px;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
            display: flex;
            align-items: center;
        }
        .file-name span {
            flex: 1;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        .file-info {
            margin-left: 5px;
            color: #3498db;
            cursor: pointer;
        }
        .position-controls {
            display: flex;
            flex-wrap: wrap;
            gap: 3px;
            margin-top: 5px;
        }
        .position-btn {
            padding: 3px 6px;
            font-size: 0.7rem;
            background-color: #f0f0f0;
            color: #333;
            border: 1px solid #ddd;
            border-radius: 3px;
            cursor: pointer;
        }
        .position-btn:hover {
            background-color: #e0e0e0;
        }
        .position-btn.active {
            background-color: #3498db;
            color: white;
            border-color: #2980b9;
        }
        .adjust-position {
            display: flex;
            flex-wrap: wrap;
            gap: 3px;
            margin-top: 5px;
        }
        .adjust-btn {
            width: 25px;
            height: 25px;
            padding: 0;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            font-size: 0.7rem;
            background: #f0f0f0;
            border: 1px solid #ddd;
            border-radius: 3px;
            cursor: pointer;
        }
        .adjust-btn:hover {
            background: #e0e0e0;
        }
        .preview-controls {
            display: flex;
            gap: 8px;
            margin-top: 12px;
            flex-wrap: wrap;
            justify-content: center;
        }
        .preview-btn {
            background-color: #3498db;
            color: white;
            padding: 8px 12px;
            border-radius: 4px;
            border: none;
            cursor: pointer;
            font-weight: bold;
            transition: background-color 0.3s;
            font-size: 0.85rem;
        }
        .preview-btn:hover { background-color: #2980b9; }
        .fullscreen-btn {
            background-color: rgba(0,0,0,0.5);
            color: white;
            border: none;
            border-radius: 4px;
            padding: 5px 10px;
            cursor: pointer;
            z-index: 100;
            margin-bottom: 10px;
        }
        .fullscreen-preview-btn {
            position: absolute;
            top: 10px;
            right: 10px;
            background-color: rgba(0,0,0,0.5);
            color: white;
            border: none;
            border-radius: 4px;
            padding: 5px 10px;
            cursor: pointer;
            z-index: 100;
            display: none;
        }
        /* Popup styles */
        .popup-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 1000;
            display: none;
            justify-content: center;
            align-items: center;
        }
        .popup-content {
            background: white;
            padding: 15px;
            border-radius: 8px;
            max-width: 90%;
            max-height: 90vh;
            overflow-y: auto;
            box-shadow: 0 4px 20px rgba(0,0,0,0.2);
            position: relative;
        }
        .popup-close {
            position: absolute;
            top: 5px;
            right: 5px;
            background: none;
            border: none;
            font-size: 1.2rem;
            cursor: pointer;
            color: #555;
        }
        .popup-title {
            margin-bottom: 10px;
            font-weight: bold;
        }
        .popup-text {
            word-break: break-all;
        }
        /* Reset button */
        .reset-btn {
            background-color: #e74c3c;
            color: white;
            border: none;
            padding: 8px 12px;
            border-radius: 4px;
            cursor: pointer;
            font-weight: bold;
            margin-top: 10px;
        }
        .reset-btn:hover {
            background-color: #c0392b;
        }
        @media (min-width: 768px) {
            h1 { font-size: 1.8rem; }
            h2 { font-size: 1.3rem; }
            .section { padding: 15px; }
            .question-box, .answer-box { padding: 15px; }
            .countdown { font-size: 2rem; }
        }
        @media (orientation: landscape) {
            .preview-container {
                width: 100%;
                height: auto;
                aspect-ratio: 16/9;
                max-height: 100vh;
                max-width: calc(100vh * 16/9);
                margin: 0 auto;
            }
        }
        /* Fullscreen styles */
        .fullscreen-mode {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: black;
            z-index: 9999;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
            box-sizing: border-box;
        }
        .prevent-minimize {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: transparent;
            z-index: 9998;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Advanced Quiz Video Generator</h1>
        
        <div class="section">
            <h2>Enter Questions and Answers</h2>
            <p>Format: One question and answer per line, separated by "|"</p>
            <textarea id="qa-input" placeholder="What is the capital of France?|Paris
How many continents are there?|7
What is 2+2?|4"></textarea>
        </div>
        
        <!-- New Settings Panel -->
        <div class="section">
            <button id="settings-toggle-btn" class="settings-toggle">
                <span><i class="fas fa-cog"></i> Settings</span>
                <i class="fas fa-chevron-down"></i>
            </button>
            
            <div class="settings-panel" id="settings-panel">
                <div class="settings-scroller">
                    <!-- Layout Settings -->
                    <div class="settings-category" data-category="layout">
                        <i class="fas fa-sliders-h"></i>
                        <span class="settings-category-title">Layout</span>
                    </div>
                    
                    <!-- Background Settings -->
                    <div class="settings-category" data-category="background">
                        <i class="fas fa-image"></i>
                        <span class="settings-category-title">Background</span>
                    </div>
                    
                    <!-- Box Style -->
                    <div class="settings-category" data-category="box-style">
                        <i class="fas fa-square"></i>
                        <span class="settings-category-title">Box Style</span>
                    </div>
                    
                    <!-- Text Settings -->
                    <div class="settings-category" data-category="text">
                        <i class="fas fa-font"></i>
                        <span class="settings-category-title">Text</span>
                    </div>
                    
                    <!-- Animation -->
                    <div class="settings-category" data-category="animation">
                        <i class="fas fa-film"></i>
                        <span class="settings-category-title">Animation</span>
                    </div>
                    
                    <!-- Position -->
                    <div class="settings-category" data-category="position">
                        <i class="fas fa-arrows-alt"></i>
                        <span class="settings-category-title">Position</span>
                    </div>
                    
                    <!-- Audio -->
                    <div class="settings-category" data-category="audio">
                        <i class="fas fa-volume-up"></i>
                        <span class="settings-category-title">Audio</span>
                    </div>
                    
                    <!-- Branding -->
                    <div class="settings-category" data-category="branding">
                        <i class="fas fa-tag"></i>
                        <span class="settings-category-title">Branding</span>
                    </div>
                    
                    <!-- Reset -->
                    <div class="settings-category" data-category="reset">
                        <i class="fas fa-undo"></i>
                        <span class="settings-category-title">Reset</span>
                    </div>
                </div>
                
                <!-- Layout Settings Content -->
                <div class="settings-content" id="layout-content">
                    <div class="settings-group">
                        <h3><i class="fas fa-sliders-h"></i> Layout Settings</h3>
                        <div class="control-item">
                            <label>Aspect Ratio</label>
                            <select id="aspect-ratio">
                                <option value="16/9">16:9 (Horizontal)</option>
                                <option value="9/16">9:16 (Vertical)</option>
                                <option value="1/1">1:1 (Square)</option>
                            </select>
                        </div>
                        <div class="control-item">
                            <label>Layout</label>
                            <select id="layout">
                                <option value="stacked">Stacked (Vertical)</option>
                                <option value="side-by-side">Side by Side</option>
                            </select>
                        </div>
                        <div class="control-row">
                            <label>Q Time (s)</label>
                            <input type="number" id="question-time" min="1" max="60" value="5">
                        </div>
                        <div class="control-row">
                            <label>A Time (s)</label>
                            <input type="number" id="answer-time" min="1" max="60" value="5">
                        </div>
                        <div class="control-row">
                            <label>Countdown (s)</label>
                            <input type="number" id="countdown-time" min="1" max="10" value="3">
                        </div>
                    </div>
                </div>
                
                <!-- Background Settings Content -->
                <div class="settings-content" id="background-content">
                    <div class="settings-group">
                        <h3><i class="fas fa-image"></i> Background Settings</h3>
                        <div class="control-item">
                            <label>Type</label>
                            <select id="bg-type">
                                <option value="color">Color</option>
                                <option value="image">Image</option>
                                <option value="gradient">Gradient</option>
                                <option value="video">Video</option>
                            </select>
                        </div>
                        <div id="bg-color-control" class="control-item">
                            <label>Color</label>
                            <input type="color" id="bg-color" value="#ffffff">
                        </div>
                        <div id="bg-image-control" class="control-item" style="display:none">
                            <label>Image</label>
                            <div class="file-input-wrapper">
                                <button class="file-input-btn">Choose Image</button>
                                <input type="file" id="bg-image" accept="image/*">
                            </div>
                            <div id="bg-image-name" class="file-name">
                                <span></span>
                                <i class="fas fa-info-circle file-info" data-file="bg-image"></i>
                            </div>
                        </div>
                        <div id="bg-video-control" class="control-item" style="display:none">
                            <label>Video</label>
                            <div class="file-input-wrapper">
                                <button class="file-input-btn">Choose Video</button>
                                <input type="file" id="bg-video" accept="video/mp4,video/webm,video/ogg">
                            </div>
                            <div id="bg-video-name" class="file-name">
                                <span></span>
                                <i class="fas fa-info-circle file-info" data-file="bg-video"></i>
                            </div>
                            <label>Volume</label>
                            <input type="range" id="video-volume" min="0" max="1" step="0.1" value="0.5">
                        </div>
                        <div id="bg-gradient-control" class="control-item" style="display:none">
                            <label>Color 1</label>
                            <input type="color" id="gradient-color1" value="#3498db">
                            <label>Color 2</label>
                            <input type="color" id="gradient-color2" value="#2ecc71">
                            <label>Direction</label>
                            <select id="gradient-direction">
                                <option value="to right">Horizontal</option>
                                <option value="to bottom">Vertical</option>
                                <option value="to bottom right">Diagonal</option>
                                <option value="circle">Radial</option>
                            </select>
                        </div>
                    </div>
                </div>
                
                <!-- Box Style Content -->
                <div class="settings-content" id="box-style-content">
                    <div class="settings-group">
                        <h3><i class="fas fa-square"></i> Box Style</h3>
                        <div class="control-item">
                            <label>Q Color</label>
                            <input type="color" id="question-color" value="#3498db">
                        </div>
                        <div class="control-item">
                            <label>A Color</label>
                            <input type="color" id="answer-color" value="#2ecc71">
                        </div>
                        <div class="control-item">
                            <label>Text Color</label>
                            <input type="color" id="text-color" value="#ffffff">
                        </div>
                        <div class="control-item">
                            <label>Opacity</label>
                            <input type="range" id="box-opacity" min="0.5" max="1" step="0.1" value="1">
                        </div>
                        <div class="control-item">
                            <label>Border (px)</label>
                            <input type="number" id="box-border" min="0" max="10" value="0">
                        </div>
                    </div>
                </div>
                
                <!-- Text Settings Content -->
                <div class="settings-content" id="text-content">
                    <div class="settings-group">
                        <h3><i class="fas fa-font"></i> Text Settings</h3>
                        <div class="control-item">
                            <label>Font</label>
                            <select id="font-family">
                                <option value="Arial, sans-serif">Arial</option>
                                <option value="'Helvetica Neue', sans-serif">Helvetica</option>
                                <option value="'Times New Roman', serif">Times New Roman</option>
                                <option value="Georgia, serif">Georgia</option>
                                <option value="'Courier New', monospace">Courier New</option>
                                <option value="'Palatino Linotype', serif">Palatino</option>
                                <option value="'Gill Sans', sans-serif">Gill Sans</option>
                                <option value="'Trebuchet MS', sans-serif">Trebuchet</option>
                                <option value="Verdana, sans-serif">Verdana</option>
                                <option value="'Impact', sans-serif">Impact</option>
                                <option value="'Comic Sans MS', cursive">Comic Sans</option>
                                <option value="'Brush Script MT', cursive">Brush Script</option>
                            </select>
                        </div>
                        <div class="control-item">
                            <label>Size</label>
                            <div class="control-row">
                                <button id="font-decrease" class="mini-btn">-</button>
                                <span id="font-size-value" style="flex:1; text-align:center">1.2rem</span>
                                <button id="font-increase" class="mini-btn">+</button>
                            </div>
                        </div>
                        <div class="control-item">
                            <label>Q Line Height</label>
                            <div class="control-row">
                                <button id="q-line-decrease" class="mini-btn">-</button>
                                <span id="q-line-value" style="flex:1; text-align:center">1.5</span>
                                <button id="q-line-increase" class="mini-btn">+</button>
                            </div>
                        </div>
                        <div class="control-item">
                            <label>A Line Height</label>
                            <div class="control-row">
                                <button id="a-line-decrease" class="mini-btn">-</button>
                                <span id="a-line-value" style="flex:1; text-align:center">1.5</span>
                                <button id="a-line-increase" class="mini-btn">+</button>
                            </div>
                        </div>
                        <div class="control-item">
                            <label>
                                <input type="checkbox" id="text-shadow"> Text Shadow
                            </label>
                        </div>
                    </div>
                </div>
                
                <!-- Animation Content -->
                <div class="settings-content" id="animation-content">
                    <div class="settings-group">
                        <h3><i class="fas fa-film"></i> Animation</h3>
                        <div class="control-item">
                            <label>Q Transition</label>
                            <select id="question-transition">
                                <option value="slide-up">Slide Up</option>
                                <option value="slide-down">Slide Down</option>
                                <option value="slide-left">Slide Left</option>
                                <option value="slide-right">Slide Right</option>
                                <option value="zoom-in">Zoom In</option>
                                <option value="rotate-in">Rotate In</option>
                                <option value="bounce-in">Bounce In</option>
                                <option value="fade-in">Fade In</option>
                                <option value="flip-in">Flip In</option>
                            </select>
                        </div>
                        <div class="control-item">
                            <label>A Transition</label>
                            <select id="answer-transition">
                                <option value="slide-up">Slide Up</option>
                                <option value="slide-down">Slide Down</option>
                                <option value="slide-left">Slide Left</option>
                                <option value="slide-right">Slide Right</option>
                                <option value="zoom-in">Zoom In</option>
                                <option value="rotate-in">Rotate In</option>
                                <option value="bounce-in">Bounce In</option>
                                <option value="fade-in">Fade In</option>
                                <option value="flip-in">Flip In</option>
                            </select>
                        </div>
                        <div class="control-item">
                            <label>
                                <input type="checkbox" id="countdown-enabled" checked> Countdown
                            </label>
                        </div>
                        <div class="control-item">
                            <label>Countdown Color</label>
                            <input type="color" id="countdown-color" value="#e74c3c">
                        </div>
                    </div>
                </div>
                
                <!-- Position Content -->
                <div class="settings-content" id="position-content">
                    <div class="settings-group">
                        <h3><i class="fas fa-arrows-alt"></i> Countdown Position</h3>
                        <div class="position-controls" id="countdown-position-controls">
                            <button class="position-btn" data-position="below" data-element="countdown">Below</button>
                            <button class="position-btn" data-position="top" data-element="countdown">Top</button>
                            <button class="position-btn" data-position="bottom" data-element="countdown">Bottom</button>
                            <button class="position-btn" data-position="left" data-element="countdown">Left</button>
                            <button class="position-btn" data-position="right" data-element="countdown">Right</button>
                        </div>
                        <div class="adjust-position">
                            <button class="adjust-btn" data-direction="up" data-element="countdown"><i class="fas fa-arrow-up"></i></button>
                            <button class="adjust-btn" data-direction="down" data-element="countdown"><i class="fas fa-arrow-down"></i></button>
                            <button class="adjust-btn" data-direction="left" data-element="countdown"><i class="fas fa-arrow-left"></i></button>
                            <button class="adjust-btn" data-direction="right" data-element="countdown"><i class="fas fa-arrow-right"></i></button>
                        </div>
                    </div>
                    <div class="settings-group">
                        <h3><i class="fas fa-question"></i> Question Box Position</h3>
                        <div class="position-controls" id="question-position-controls">
                            <button class="position-btn" data-position="center" data-element="question">Center</button>
                            <button class="position-btn" data-position="top" data-element="question">Top</button>
                            <button class="position-btn" data-position="bottom" data-element="question">Bottom</button>
                            <button class="position-btn" data-position="left" data-element="question">Left</button>
                            <button class="position-btn" data-position="right" data-element="question">Right</button>
                        </div>
                    </div>
                    <div class="settings-group">
                        <h3><i class="fas fa-check"></i> Answer Box Position</h3>
                        <div class="position-controls" id="answer-position-controls">
                            <button class="position-btn" data-position="center" data-element="answer">Center</button>
                            <button class="position-btn" data-position="top" data-element="answer">Top</button>
                            <button class="position-btn" data-position="bottom" data-element="answer">Bottom</button>
                            <button class="position-btn" data-position="left" data-element="answer">Left</button>
                            <button class="position-btn" data-position="right" data-element="answer">Right</button>
                        </div>
                    </div>
                </div>
                
                <!-- Audio Content -->
                <div class="settings-content" id="audio-content">
                    <div class="settings-group">
                        <h3><i class="fas fa-volume-up"></i> Audio Settings</h3>
                        <div class="control-item">
                            <label>Background</label>
                            <div class="file-input-wrapper">
                                <button class="file-input-btn">Choose Audio</button>
                                <input type="file" id="bg-audio" accept="audio/mpeg,audio/wav,audio/ogg">
                            </div>
                            <div id="bg-audio-name" class="file-name">
                                <span></span>
                                <i class="fas fa-info-circle file-info" data-file="bg-audio"></i>
                            </div>
                            <label>Volume</label>
                            <input type="range" id="audio-volume" min="0" max="1" step="0.1" value="0.5">
                        </div>
                        <div class="control-item">
                            <label>
                                <input type="checkbox" id="audio-loop" checked> Loop Audio
                            </label>
                        </div>
                        <div class="control-item">
                            <label>Q Sound</label>
                            <div class="file-input-wrapper">
                                <button class="file-input-btn">Choose SFX</button>
                                <input type="file" id="question-sfx" accept="audio/*">
                            </div>
                            <div id="question-sfx-name" class="file-name">
                                <span></span>
                                <i class="fas fa-info-circle file-info" data-file="question-sfx"></i>
                            </div>
                            <label>Volume</label>
                            <input type="range" id="question-sfx-volume" min="0" max="1" step="0.1" value="0.5">
                        </div>
                        <div class="control-item">
                            <label>A Sound</label>
                            <div class="file-input-wrapper">
                                <button class="file-input-btn">Choose SFX</button>
                                <input type="file" id="answer-sfx" accept="audio/*">
                            </div>
                            <div id="answer-sfx-name" class="file-name">
                                <span></span>
                                <i class="fas fa-info-circle file-info" data-file="answer-sfx"></i>
                            </div>
                            <label>Volume</label>
                            <input type="range" id="answer-sfx-volume" min="0" max="1" step="0.1" value="0.5">
                        </div>
                        <div class="control-item">
                            <label>Countdown SFX</label>
                            <div class="file-input-wrapper">
                                <button class="file-input-btn">Choose SFX</button>
                                <input type="file" id="countdown-sfx" accept="audio/*">
                            </div>
                            <div id="countdown-sfx-name" class="file-name">
                                <span></span>
                                <i class="fas fa-info-circle file-info" data-file="countdown-sfx"></i>
                            </div>
                            <label>Volume</label>
                            <input type="range" id="countdown-sfx-volume" min="0" max="1" step="0.1" value="0.5">
                        </div>
                        <div class="control-item">
                            <label>Warning SFX</label>
                            <div class="file-input-wrapper">
                                <button class="file-input-btn">Choose SFX</button>
                                <input type="file" id="warning-sfx" accept="audio/*">
                            </div>
                            <div id="warning-sfx-name" class="file-name">
                                <span></span>
                                <i class="fas fa-info-circle file-info" data-file="warning-sfx"></i>
                            </div>
                            <label>Volume</label>
                            <input type="range" id="warning-sfx-volume" min="0" max="1" step="0.1" value="0.5">
                        </div>
                    </div>
                </div>
                
                <!-- Branding Content -->
                <div class="settings-content" id="branding-content">
                    <div class="settings-group">
                        <h3><i class="fas fa-tag"></i> Branding Settings</h3>
                        <div class="control-item">
                            <label>Type</label>
                            <select id="branding-type">
                                <option value="none">None</option>
                                <option value="logo">Logo</option>
                                <option value="text">Text</option>
                            </select>
                        </div>
                        <div id="branding-logo-control" class="control-item" style="display:none">
                            <label>Logo</label>
                            <div class="file-input-wrapper">
                                <button class="file-input-btn">Choose Logo</button>
                                <input type="file" id="branding-logo" accept="image/*">
                            </div>
                            <div id="branding-logo-name" class="file-name">
                                <span></span>
                                <i class="fas fa-info-circle file-info" data-file="branding-logo"></i>
                            </div>
                            <label>Size (%)</label>
                            <input type="range" id="logo-size" min="5" max="30" value="15">
                            <div class="adjust-position">
                                <button class="adjust-btn" data-direction="up" data-element="branding"><i class="fas fa-arrow-up"></i></button>
                                <button class="adjust-btn" data-direction="down" data-element="branding"><i class="fas fa-arrow-down"></i></button>
                                <button class="adjust-btn" data-direction="left" data-element="branding"><i class="fas fa-arrow-left"></i></button>
                                <button class="adjust-btn" data-direction="right" data-element="branding"><i class="fas fa-arrow-right"></i></button>
                            </div>
                        </div>
                        <div id="branding-text-control" class="control-item" style="display:none">
                            <label>Text</label>
                            <input type="text" id="branding-text" placeholder="Your brand">
                            <label>Font</label>
                            <select id="text-font">
                                <option value="Arial, sans-serif">Arial</option>
                                <option value="'Helvetica Neue', sans-serif">Helvetica</option>
                                <option value="'Times New Roman', serif">Times New Roman</option>
                                <option value="Georgia, serif">Georgia</option>
                                <option value="'Courier New', monospace">Courier New</option>
                                <option value="'Palatino Linotype', serif">Palatino</option>
                                <option value="'Gill Sans', sans-serif">Gill Sans</option>
                                <option value="'Trebuchet MS', sans-serif">Trebuchet</option>
                                <option value="Verdana, sans-serif">Verdana</option>
                                <option value="'Impact', sans-serif">Impact</option>
                                <option value="'Comic Sans MS', cursive">Comic Sans</option>
                                <option value="'Brush Script MT', cursive">Brush Script</option>
                            </select>
                            <label>Size (px)</label>
                            <input type="range" id="text-size" min="10" max="50" value="20">
                            <label>Color</label>
                            <input type="color" id="branding-text-color" value="#ffffff">
                            <div class="adjust-position">
                                <button class="adjust-btn" data-direction="up" data-element="branding"><i class="fas fa-arrow-up"></i></button>
                                <button class="adjust-btn" data-direction="down" data-element="branding"><i class="fas fa-arrow-down"></i></button>
                                <button class="adjust-btn" data-direction="left" data-element="branding"><i class="fas fa-arrow-left"></i></button>
                                <button class="adjust-btn" data-direction="right" data-element="branding"><i class="fas fa-arrow-right"></i></button>
                            </div>
                        </div>
                    </div>
                </div>
                
                <!-- Reset Content -->
                <div class="settings-content" id="reset-content">
                    <div class="settings-group">
                        <h3><i class="fas fa-undo"></i> Reset Settings</h3>
                        <p>Reset all settings to their default values.</p>
                        <button id="reset-btn" class="reset-btn">
                            <i class="fas fa-trash-alt"></i> Reset All Settings
                        </button>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="section">
            <div class="preview-controls">
                <button id="preview-btn" class="preview-btn">Preview First QA</button>
                <button id="start-preview-btn" class="preview-btn">Start Full Preview</button>
                <button id="stop-preview-btn" class="preview-btn" disabled>Stop Preview</button>
                <button id="restart-preview-btn" class="preview-btn">Restart Preview</button>
                <button id="fullscreen-btn" class="preview-btn">Full Screen</button>
            </div>
            <h2>Preview</h2>
            <div class="preview-container" id="preview-container">
                <button id="fullscreen-preview-btn" class="fullscreen-preview-btn">Start Full Preview</button>
                <video id="bg-video-element" autoplay loop muted></video>
                <audio id="bg-audio-element" loop style="display: none;"></audio>
                <audio id="question-sfx-element" style="display: none;"></audio>
                <audio id="answer-sfx-element" style="display: none;"></audio>
                <audio id="countdown-sfx-element" style="display: none;"></audio>
                <audio id="warning-sfx-element" style="display: none;"></audio>
                <div class="video-frame" id="video-frame">
                    <div class="question-box" id="question-box"></div>
                    <div class="answer-box" id="answer-box"></div>
                    <div class="countdown" id="countdown"></div>
                    <div id="branding-element" class="branding-container" style="display: none;"></div>
                </div>
            </div>
        </div>
    </div>

    <!-- Fullscreen container -->
    <div id="fullscreen-container" style="display:none;"></div>

    <!-- File Info Popup -->
    <div class="popup-overlay" id="file-info-popup">
        <div class="popup-content">
            <button class="popup-close" id="file-info-close">&times;</button>
            <div class="popup-title">File Name</div>
            <div class="popup-text" id="file-info-text"></div>
        </div>
    </div>

    <script>
        // DOM Elements
        const elements = {
            qaInput: document.getElementById('qa-input'),
            aspectRatio: document.getElementById('aspect-ratio'),
            layout: document.getElementById('layout'),
            questionTime: document.getElementById('question-time'),
            answerTime: document.getElementById('answer-time'),
            countdownTime: document.getElementById('countdown-time'),
            bgType: document.getElementById('bg-type'),
            bgColor: document.getElementById('bg-color'),
            bgImage: document.getElementById('bg-image'),
            bgImageName: document.getElementById('bg-image-name').querySelector('span'),
            bgVideoInput: document.getElementById('bg-video'),
            bgVideoName: document.getElementById('bg-video-name').querySelector('span'),
            bgVideoElement: document.getElementById('bg-video-element'),
            videoVolume: document.getElementById('video-volume'),
            gradientColor1: document.getElementById('gradient-color1'),
            gradientColor2: document.getElementById('gradient-color2'),
            gradientDirection: document.getElementById('gradient-direction'),
            questionColor: document.getElementById('question-color'),
            answerColor: document.getElementById('answer-color'),
            textColor: document.getElementById('text-color'),
            boxOpacity: document.getElementById('box-opacity'),
            boxBorder: document.getElementById('box-border'),
            fontFamily: document.getElementById('font-family'),
            countdownEnabled: document.getElementById('countdown-enabled'),
            countdownColor: document.getElementById('countdown-color'),
            previewBtn: document.getElementById('preview-btn'),
            startPreviewBtn: document.getElementById('start-preview-btn'),
            stopPreviewBtn: document.getElementById('stop-preview-btn'),
            restartPreviewBtn: document.getElementById('restart-preview-btn'),
            previewContainer: document.getElementById('preview-container'),
            videoFrame: document.getElementById('video-frame'),
            questionBox: document.getElementById('question-box'),
            answerBox: document.getElementById('answer-box'),
            countdown: document.getElementById('countdown'),
            bgColorControl: document.getElementById('bg-color-control'),
            bgImageControl: document.getElementById('bg-image-control'),
            bgVideoControl: document.getElementById('bg-video-control'),
            bgGradientControl: document.getElementById('bg-gradient-control'),
            questionTransition: document.getElementById('question-transition'),
            answerTransition: document.getElementById('answer-transition'),
            textShadow: document.getElementById('text-shadow'),
            fontDecreaseBtn: document.getElementById('font-decrease'),
            fontIncreaseBtn: document.getElementById('font-increase'),
            fontSizeValue: document.getElementById('font-size-value'),
            qLineDecreaseBtn: document.getElementById('q-line-decrease'),
            qLineIncreaseBtn: document.getElementById('q-line-increase'),
            qLineValue: document.getElementById('q-line-value'),
            aLineDecreaseBtn: document.getElementById('a-line-decrease'),
            aLineIncreaseBtn: document.getElementById('a-line-increase'),
            aLineValue: document.getElementById('a-line-value'),
            bgAudioInput: document.getElementById('bg-audio'),
            bgAudioName: document.getElementById('bg-audio-name').querySelector('span'),
            bgAudioElement: document.getElementById('bg-audio-element'),
            audioVolume: document.getElementById('audio-volume'),
            audioLoop: document.getElementById('audio-loop'),
            questionSfxInput: document.getElementById('question-sfx'),
            questionSfxName: document.getElementById('question-sfx-name').querySelector('span'),
            answerSfxInput: document.getElementById('answer-sfx'),
            answerSfxName: document.getElementById('answer-sfx-name').querySelector('span'),
            countdownSfxInput: document.getElementById('countdown-sfx'),
            countdownSfxName: document.getElementById('countdown-sfx-name').querySelector('span'),
            warningSfxInput: document.getElementById('warning-sfx'),
            warningSfxName: document.getElementById('warning-sfx-name').querySelector('span'),
            questionSfxElement: document.getElementById('question-sfx-element'),
            answerSfxElement: document.getElementById('answer-sfx-element'),
            countdownSfxElement: document.getElementById('countdown-sfx-element'),
            warningSfxElement: document.getElementById('warning-sfx-element'),
            questionSfxVolume: document.getElementById('question-sfx-volume'),
            answerSfxVolume: document.getElementById('answer-sfx-volume'),
            countdownSfxVolume: document.getElementById('countdown-sfx-volume'),
            warningSfxVolume: document.getElementById('warning-sfx-volume'),
            brandingType: document.getElementById('branding-type'),
            brandingLogoControl: document.getElementById('branding-logo-control'),
            brandingTextControl: document.getElementById('branding-text-control'),
            brandingLogoInput: document.getElementById('branding-logo'),
            brandingLogoName: document.getElementById('branding-logo-name').querySelector('span'),
            logoSize: document.getElementById('logo-size'),
            brandingTextInput: document.getElementById('branding-text'),
            textFont: document.getElementById('text-font'),
            textSize: document.getElementById('text-size'),
            brandingTextColor: document.getElementById('branding-text-color'),
            brandingElement: document.getElementById('branding-element'),
            fullscreenBtn: document.getElementById('fullscreen-btn'),
            fullscreenPreviewBtn: document.getElementById('fullscreen-preview-btn'),
            fullscreenContainer: document.getElementById('fullscreen-container'),
            countdownPositionControls: document.getElementById('countdown-position-controls'),
            questionPositionControls: document.getElementById('question-position-controls'),
            answerPositionControls: document.getElementById('answer-position-controls'),
            settingsToggleBtn: document.getElementById('settings-toggle-btn'),
            settingsPanel: document.getElementById('settings-panel'),
            resetBtn: document.getElementById('reset-btn'),
            fileInfoPopup: document.getElementById('file-info-popup'),
            fileInfoClose: document.getElementById('file-info-close'),
            fileInfoText: document.getElementById('file-info-text')
        };

        // State
        let state = {
            qaPairs: [],
            isPreviewing: false,
            currentIndex: 0,
            backgroundImage: null,
            backgroundVideo: null,
            backgroundAudio: null,
            brandingLogo: null,
            previewInterval: null,
            countdownInterval: null,
            warningInterval: null,
            audioContext: null,
            currentFontSize: 1.2,
            questionLineHeight: 1.5,
            answerLineHeight: 1.5,
            actionQueue: [],
            isProcessingAction: false,
            questionSfx: null,
            answerSfx: null,
            countdownSfx: null,
            warningSfx: null,
            isFullscreen: false,
            positions: {
                countdown: 'below',
                question: 'center',
                answer: 'center',
                branding: 'top-left'
            },
            offsets: {
                countdown: { x: 0, y: 0 },
                branding: { x: 0, y: 0 }
            },
            defaultSettings: {
                aspectRatio: '16/9',
                layout: 'stacked',
                questionTime: 5,
                answerTime: 5,
                countdownTime: 3,
                bgType: 'color',
                bgColor: '#ffffff',
                gradientColor1: '#3498db',
                gradientColor2: '#2ecc71',
                gradientDirection: 'to right',
                questionColor: '#3498db',
                answerColor: '#2ecc71',
                textColor: '#ffffff',
                boxOpacity: 1,
                boxBorder: 0,
                fontFamily: 'Arial, sans-serif',
                countdownEnabled: true,
                countdownColor: '#e74c3c',
                questionTransition: 'slide-up',
                answerTransition: 'slide-up',
                textShadow: false,
                audioLoop: true,
                audioVolume: 0.5,
                questionSfxVolume: 0.5,
                answerSfxVolume: 0.5,
                countdownSfxVolume: 0.5,
                warningSfxVolume: 0.5,
                videoVolume: 0.5,
                brandingType: 'none',
                logoSize: 15,
                brandingText: '',
                textFont: 'Arial, sans-serif',
                textSize: 20,
                brandingTextColor: '#ffffff',
                positions: {
                    countdown: 'below',
                    question: 'center',
                    answer: 'center',
                    branding: 'top-left'
                },
                offsets: {
                    countdown: { x: 0, y: 0 },
                    branding: { x: 0, y: 0 }
                },
                currentFontSize: 1.2,
                questionLineHeight: 1.5,
                answerLineHeight: 1.5
            },
            activeSettingsCategory: null,
            originalPreviewContainerParent: null,
            originalPreviewContainerNextSibling: null,
            fileData: {
                'bg-image': null,
                'bg-video': null,
                'bg-audio': null,
                'question-sfx': null,
                'answer-sfx': null,
                'countdown-sfx': null,
                'warning-sfx': null,
                'branding-logo': null
            }
        };

        // Initialize
function init() {
    // Store original parent and sibling of preview container
    state.originalPreviewContainerParent = elements.previewContainer.parentNode;
    state.originalPreviewContainerNextSibling = elements.previewContainer.nextSibling;
    
    setupEventListeners();
    loadSettings();
    updateBackgroundControls();
    updateBrandingControls();
    updatePreview();
    updateFontSizeDisplay();
    updateLineHeightDisplay();
    
    try {
        state.audioContext = new(window.AudioContext || window.webkitAudioContext)();
    } catch (e) {
        console.error("Web Audio API not supported");
    }
    
    // Initialize text shadow as unchecked
    elements.textShadow.checked = false;
    
    // Set up fullscreen change listeners for all browsers
    document.addEventListener('fullscreenchange', handleFullscreenChange);
    document.addEventListener('webkitfullscreenchange', handleFullscreenChange);
    document.addEventListener('mozfullscreenchange', handleFullscreenChange);
    document.addEventListener('MSFullscreenChange', handleFullscreenChange);
    
    // Initialize position buttons
    initPositionButtons();
}

        function initPositionButtons() {
            // Set up position buttons for all elements
            const positionControls = [
                elements.countdownPositionControls,
                elements.questionPositionControls,
                elements.answerPositionControls
            ];
            
            positionControls.forEach(control => {
                if (control) {
                    control.addEventListener('click', (e) => {
                        if (e.target.classList.contains('position-btn')) {
                            const element = e.target.dataset.element;
                            const position = e.target.dataset.position;
                            
                            // Update active button
                            const buttons = control.querySelectorAll('.position-btn');
                            buttons.forEach(btn => btn.classList.remove('active'));
                            e.target.classList.add('active');
                            
                            // Update position in state
                            state.positions[element] = position;
                            
                            // Reset offsets when changing position
                            if (element === 'countdown' || element === 'branding') {
                                state.offsets[element] = { x: 0, y: 0 };
                            }
                            
                            // Update preview
                            updateElementPosition(element, position);
                            
                            // Save settings
                            saveSettings();
                        }
                    });
                }
            });
            
            // Set up adjust buttons
            document.querySelectorAll('.adjust-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    const element = this.dataset.element;
                    const direction = this.dataset.direction;
                    
                    // Update offsets
                    switch(direction) {
                        case 'up': state.offsets[element].y -= 5; break;
                        case 'down': state.offsets[element].y += 5; break;
                        case 'left': state.offsets[element].x -= 5; break;
                        case 'right': state.offsets[element].x += 5; break;
                    }
                    
                    // Apply new position
                    updateElementPosition(element, state.positions[element]);
                    
                    // Save settings
                    saveSettings();
                });
            });
            
            // Set initial active buttons
            setActivePositionButtons();
        }

        function setActivePositionButtons() {
            // Set active buttons based on current positions
            for (const [element, position] of Object.entries(state.positions)) {
                const control = document.querySelector(`#${element}-position-controls`);
                if (control) {
                    const buttons = control.querySelectorAll('.position-btn');
                    buttons.forEach(btn => {
                        if (btn.dataset.position === position) {
                            btn.classList.add('active');
                        } else {
                            btn.classList.remove('active');
                        }
                    });
                }
            }
        }

        function updateElementPosition(element, position) {
            const el = element === 'countdown' ? elements.countdown : 
                      element === 'question' ? elements.questionBox :
                      element === 'answer' ? elements.answerBox :
                      elements.brandingElement;
            
            if (!el) return;
            
            // Reset all positions
            el.style.top = '';
            el.style.bottom = '';
            el.style.left = '';
            el.style.right = '';
            el.style.transform = '';
            
            // Apply offsets if they exist for this element
            const offset = state.offsets[element] || { x: 0, y: 0 };
            
            switch(element) {
                case 'countdown':
                    positionCountdown(position, offset);
                    break;
                case 'question':
                    positionQuestionBox(position);
                    break;
                case 'answer':
                    positionAnswerBox(position);
                    break;
                case 'branding':
                    positionBranding(position, offset);
                    break;
            }
        }

        function positionCountdown(position, offset) {
            const countdown = elements.countdown;
            let transform = '';
            
            // Apply horizontal offset
            if (offset.x !== 0) {
                transform += ` translateX(${offset.x}px)`;
            }
            
            // Apply vertical offset
            if (offset.y !== 0) {
                transform += ` translateY(${offset.y}px)`;
            }
            
            switch(position) {
                case 'below':
                    countdown.style.top = 'calc(100% + 20px)';
                    countdown.style.left = '50%';
                    countdown.style.transform = `translateX(-50%)${transform}`;
                    break;
                case 'top':
                    countdown.style.top = `calc(20px + ${offset.y}px)`;
                    countdown.style.left = '50%';
                    countdown.style.transform = `translateX(calc(-50% + ${offset.x}px))`;
                    break;
                case 'bottom':
                    countdown.style.bottom = `calc(20px - ${offset.y}px)`;
                    countdown.style.left = '50%';
                    countdown.style.transform = `translateX(calc(-50% + ${offset.x}px))`;
                    break;
                case 'left':
                    countdown.style.top = `calc(50% + ${offset.y}px)`;
                    countdown.style.left = `calc(20px + ${offset.x}px)`;
                    countdown.style.transform = 'translateY(-50%)';
                    break;
                case 'right':
                    countdown.style.top = `calc(50% + ${offset.y}px)`;
                    countdown.style.right = `calc(20px - ${offset.x}px)`;
                    countdown.style.transform = 'translateY(-50%)';
                    break;
            }
        }

        function positionQuestionBox(position) {
            const questionBox = elements.questionBox;
            
            switch(position) {
                case 'center':
                    questionBox.style.alignSelf = 'center';
                    break;
                case 'top':
                    questionBox.style.alignSelf = 'flex-start';
                    break;
                case 'bottom':
                    questionBox.style.alignSelf = 'flex-end';
                    break;
                case 'left':
                    questionBox.style.alignSelf = 'flex-start';
                    break;
                case 'right':
                    questionBox.style.alignSelf = 'flex-end';
                    break;
            }
        }

        function positionAnswerBox(position) {
            const answerBox = elements.answerBox;
            
            switch(position) {
                case 'center':
                    answerBox.style.alignSelf = 'center';
                    break;
                case 'top':
                    answerBox.style.alignSelf = 'flex-start';
                    break;
                case 'bottom':
                    answerBox.style.alignSelf = 'flex-end';
                    break;
                case 'left':
                    answerBox.style.alignSelf = 'flex-start';
                    break;
                case 'right':
                    answerBox.style.alignSelf = 'flex-end';
                    break;
            }
        }

        function positionBranding(position, offset) {
            const branding = elements.brandingElement;
            
            switch(position) {
                case 'top-left':
                    branding.style.top = `calc(10px + ${offset.y}px)`;
                    branding.style.left = `calc(10px + ${offset.x}px)`;
                    branding.style.right = '';
                    branding.style.bottom = '';
                    break;
                case 'top-right':
                    branding.style.top = `calc(10px + ${offset.y}px)`;
                    branding.style.right = `calc(10px - ${offset.x}px)`;
                    branding.style.left = '';
                    branding.style.bottom = '';
                    break;
                case 'bottom-left':
                    branding.style.bottom = `calc(10px - ${offset.y}px)`;
                    branding.style.left = `calc(10px + ${offset.x}px)`;
                    branding.style.top = '';
                    branding.style.right = '';
                    break;
                case 'bottom-right':
                    branding.style.bottom = `calc(10px - ${offset.y}px)`;
                    branding.style.right = `calc(10px - ${offset.x}px)`;
                    branding.style.top = '';
                    branding.style.left = '';
                    break;
            }
        }

        // Settings persistence
        function saveSettings() {
            const settings = {
                aspectRatio: elements.aspectRatio.value,
                layout: elements.layout.value,
                questionTime: elements.questionTime.value,
                answerTime: elements.answerTime.value,
                countdownTime: elements.countdownTime.value,
                bgType: elements.bgType.value,
                bgColor: elements.bgColor.value,
                gradientColor1: elements.gradientColor1.value,
                gradientColor2: elements.gradientColor2.value,
                gradientDirection: elements.gradientDirection.value,
                questionColor: elements.questionColor.value,
                answerColor: elements.answerColor.value,
                textColor: elements.textColor.value,
                boxOpacity: elements.boxOpacity.value,
                boxBorder: elements.boxBorder.value,
                fontFamily: elements.fontFamily.value,
                countdownEnabled: elements.countdownEnabled.checked,
                countdownColor: elements.countdownColor.value,
                questionTransition: elements.questionTransition.value,
                answerTransition: elements.answerTransition.value,
                textShadow: elements.textShadow.checked,
                audioLoop: elements.audioLoop.checked,
                audioVolume: elements.audioVolume.value,
                questionSfxVolume: elements.questionSfxVolume.value,
                answerSfxVolume: elements.answerSfxVolume.value,
                countdownSfxVolume: elements.countdownSfxVolume.value,
                warningSfxVolume: elements.warningSfxVolume.value,
                videoVolume: elements.videoVolume.value,
                brandingType: elements.brandingType.value,
                logoSize: elements.logoSize.value,
                brandingText: elements.brandingTextInput.value,
                textFont: elements.textFont.value,
                textSize: elements.textSize.value,
                brandingTextColor: elements.brandingTextColor.value,
                positions: state.positions,
                offsets: state.offsets,
                currentFontSize: state.currentFontSize,
                questionLineHeight: state.questionLineHeight,
                answerLineHeight: state.answerLineHeight,
                qaInput: elements.qaInput.value,
                fileData: state.fileData
            };
            
            localStorage.setItem('quizVideoGeneratorSettings', JSON.stringify(settings));
        }

        function loadSettings() {
            const savedSettings = localStorage.getItem('quizVideoGeneratorSettings');
            
            if (savedSettings) {
                const settings = JSON.parse(savedSettings);
                
                // Apply all settings
                elements.aspectRatio.value = settings.aspectRatio || state.defaultSettings.aspectRatio;
                elements.layout.value = settings.layout || state.defaultSettings.layout;
                elements.questionTime.value = settings.questionTime || state.defaultSettings.questionTime;
                elements.answerTime.value = settings.answerTime || state.defaultSettings.answerTime;
                elements.countdownTime.value = settings.countdownTime || state.defaultSettings.countdownTime;
                elements.bgType.value = settings.bgType || state.defaultSettings.bgType;
                elements.bgColor.value = settings.bgColor || state.defaultSettings.bgColor;
                elements.gradientColor1.value = settings.gradientColor1 || state.defaultSettings.gradientColor1;
                elements.gradientColor2.value = settings.gradientColor2 || state.defaultSettings.gradientColor2;
                elements.gradientDirection.value = settings.gradientDirection || state.defaultSettings.gradientDirection;
                elements.questionColor.value = settings.questionColor || state.defaultSettings.questionColor;
                elements.answerColor.value = settings.answerColor || state.defaultSettings.answerColor;
                elements.textColor.value = settings.textColor || state.defaultSettings.textColor;
                elements.boxOpacity.value = settings.boxOpacity || state.defaultSettings.boxOpacity;
                elements.boxBorder.value = settings.boxBorder || state.defaultSettings.boxBorder;
                elements.fontFamily.value = settings.fontFamily || state.defaultSettings.fontFamily;
                elements.countdownEnabled.checked = settings.countdownEnabled !== undefined ? settings.countdownEnabled : state.defaultSettings.countdownEnabled;
                elements.countdownColor.value = settings.countdownColor || state.defaultSettings.countdownColor;
                elements.questionTransition.value = settings.questionTransition || state.defaultSettings.questionTransition;
                elements.answerTransition.value = settings.answerTransition || state.defaultSettings.answerTransition;
                elements.textShadow.checked = settings.textShadow !== undefined ? settings.textShadow : state.defaultSettings.textShadow;
                elements.audioLoop.checked = settings.audioLoop !== undefined ? settings.audioLoop : state.defaultSettings.audioLoop;
                elements.audioVolume.value = settings.audioVolume || state.defaultSettings.audioVolume;
                elements.questionSfxVolume.value = settings.questionSfxVolume || state.defaultSettings.questionSfxVolume;
                elements.answerSfxVolume.value = settings.answerSfxVolume || state.defaultSettings.answerSfxVolume;
                elements.countdownSfxVolume.value = settings.countdownSfxVolume || state.defaultSettings.countdownSfxVolume;
                elements.warningSfxVolume.value = settings.warningSfxVolume || state.defaultSettings.warningSfxVolume;
                elements.videoVolume.value = settings.videoVolume || state.defaultSettings.videoVolume;
                elements.brandingType.value = settings.brandingType || state.defaultSettings.brandingType;
                elements.logoSize.value = settings.logoSize || state.defaultSettings.logoSize;
                elements.brandingTextInput.value = settings.brandingText || state.defaultSettings.brandingText;
                elements.textFont.value = settings.textFont || state.defaultSettings.textFont;
                elements.textSize.value = settings.textSize || state.defaultSettings.textSize;
                elements.brandingTextColor.value = settings.brandingTextColor || state.defaultSettings.brandingTextColor;
                elements.qaInput.value = settings.qaInput || '';
                
                // Update state
                state.positions = settings.positions || state.defaultSettings.positions;
                state.offsets = settings.offsets || state.defaultSettings.offsets;
                state.currentFontSize = settings.currentFontSize || state.defaultSettings.currentFontSize;
                state.questionLineHeight = settings.questionLineHeight || state.defaultSettings.questionLineHeight;
                state.answerLineHeight = settings.answerLineHeight || state.defaultSettings.answerLineHeight;
                
                // Load file data if available
                if (settings.fileData) {
                    state.fileData = settings.fileData;
                    
                    // Restore file inputs and previews
                    restoreFileInput('bg-image');
                    restoreFileInput('bg-video');
                    restoreFileInput('bg-audio');
                    restoreFileInput('question-sfx');
                    restoreFileInput('answer-sfx');
                    restoreFileInput('countdown-sfx');
                    restoreFileInput('warning-sfx');
                    restoreFileInput('branding-logo');
                }
            }
        }

        function restoreFileInput(fileId) {
            if (state.fileData[fileId]) {
                const fileData = state.fileData[fileId];
                const nameElement = document.getElementById(`${fileId}-name`).querySelector('span');
                
                if (nameElement) {
                    nameElement.textContent = fileData.name;
                }
                
                // For images and logos, restore the preview
                if (fileId === 'bg-image') {
                    state.backgroundImage = fileData.data;
                    updatePreview();
                } else if (fileId === 'branding-logo') {
                    state.brandingLogo = fileData.data;
                    updateBranding();
                }
                
                // For audio/video files, restore the playback
                if (fileId === 'bg-video') {
                    elements.bgVideoElement.src = fileData.data;
                    elements.bgVideoElement.style.display = 'block';
                    elements.bgVideoElement.play().catch(e => {
                        console.log("Video autoplay prevented:", e);
                        elements.bgVideoElement.muted = true;
                        elements.bgVideoElement.play().catch(e => console.log("Muted play also prevented:", e));
                    });
                } else if (fileId === 'bg-audio') {
                    elements.bgAudioElement.src = fileData.data;
                    elements.bgAudioElement.volume = elements.audioVolume.value;
                    elements.bgAudioElement.loop = elements.audioLoop.checked;
                } else if (fileId === 'question-sfx') {
                    elements.questionSfxElement.src = fileData.data;
                    elements.questionSfxElement.volume = elements.questionSfxVolume.value;
                } else if (fileId === 'answer-sfx') {
                    elements.answerSfxElement.src = fileData.data;
                    elements.answerSfxElement.volume = elements.answerSfxVolume.value;
                } else if (fileId === 'countdown-sfx') {
                    elements.countdownSfxElement.src = fileData.data;
                    elements.countdownSfxElement.volume = elements.countdownSfxVolume.value;
                } else if (fileId === 'warning-sfx') {
                    elements.warningSfxElement.src = fileData.data;
                    elements.warningSfxElement.volume = elements.warningSfxVolume.value;
                }
            }
        }

        function resetSettings() {
            if (confirm('Are you sure you want to reset all settings to default?')) {
                localStorage.removeItem('quizVideoGeneratorSettings');
                
                // Reset form elements to defaults
                elements.aspectRatio.value = state.defaultSettings.aspectRatio;
                elements.layout.value = state.defaultSettings.layout;
                elements.questionTime.value = state.defaultSettings.questionTime;
                elements.answerTime.value = state.defaultSettings.answerTime;
                elements.countdownTime.value = state.defaultSettings.countdownTime;
                elements.bgType.value = state.defaultSettings.bgType;
                elements.bgColor.value = state.defaultSettings.bgColor;
                elements.gradientColor1.value = state.defaultSettings.gradientColor1;
                elements.gradientColor2.value = state.defaultSettings.gradientColor2;
                elements.gradientDirection.value = state.defaultSettings.gradientDirection;
                elements.questionColor.value = state.defaultSettings.questionColor;
                elements.answerColor.value = state.defaultSettings.answerColor;
                elements.textColor.value = state.defaultSettings.textColor;
                elements.boxOpacity.value = state.defaultSettings.boxOpacity;
                elements.boxBorder.value = state.defaultSettings.boxBorder;
                elements.fontFamily.value = state.defaultSettings.fontFamily;
                elements.countdownEnabled.checked = state.defaultSettings.countdownEnabled;
                elements.countdownColor.value = state.defaultSettings.countdownColor;
                elements.questionTransition.value = state.defaultSettings.questionTransition;
                elements.answerTransition.value = state.defaultSettings.answerTransition;
                elements.textShadow.checked = state.defaultSettings.textShadow;
                elements.audioLoop.checked = state.defaultSettings.audioLoop;
                elements.audioVolume.value = state.defaultSettings.audioVolume;
                elements.questionSfxVolume.value = state.defaultSettings.questionSfxVolume;
                elements.answerSfxVolume.value = state.defaultSettings.answerSfxVolume;
                elements.countdownSfxVolume.value = state.defaultSettings.countdownSfxVolume;
                elements.warningSfxVolume.value = state.defaultSettings.warningSfxVolume;
                elements.videoVolume.value = state.defaultSettings.videoVolume;
                elements.brandingType.value = state.defaultSettings.brandingType;
                elements.logoSize.value = state.defaultSettings.logoSize;
                elements.brandingTextInput.value = state.defaultSettings.brandingText;
                elements.textFont.value = state.defaultSettings.textFont;
                elements.textSize.value = state.defaultSettings.textSize;
                elements.brandingTextColor.value = state.defaultSettings.brandingTextColor;
                
                // Reset state
                state.positions = JSON.parse(JSON.stringify(state.defaultSettings.positions));
                state.offsets = JSON.parse(JSON.stringify(state.defaultSettings.offsets));
                state.currentFontSize = state.defaultSettings.currentFontSize;
                state.questionLineHeight = state.defaultSettings.questionLineHeight;
                state.answerLineHeight = state.defaultSettings.answerLineHeight;
                state.fileData = {
                    'bg-image': null,
                    'bg-video': null,
                    'bg-audio': null,
                    'question-sfx': null,
                    'answer-sfx': null,
                    'countdown-sfx': null,
                    'warning-sfx': null,
                    'branding-logo': null
                };
                
                // Clear file inputs
                document.querySelectorAll('input[type="file"]').forEach(input => {
                    input.value = '';
                    const nameElement = document.getElementById(`${input.id}-name`).querySelector('span');
                    if (nameElement) {
                        nameElement.textContent = '';
                    }
                });
                
                // Update UI
                updateBackgroundControls();
                updateBrandingControls();
                updatePreview();
                updateFontSizeDisplay();
                updateLineHeightDisplay();
                setActivePositionButtons();
                
                // Clear media elements
                elements.bgVideoElement.src = '';
                elements.bgVideoElement.style.display = 'none';
                elements.bgAudioElement.src = '';
                elements.questionSfxElement.src = '';
                elements.answerSfxElement.src = '';
                elements.countdownSfxElement.src = '';
                elements.warningSfxElement.src = '';
                elements.brandingElement.innerHTML = '';
                elements.brandingElement.style.display = 'none';
                
                alert('All settings have been reset to default values.');
            }
        }

        // Event Listeners
        function setupEventListeners() {
            // Settings panel toggle
            elements.settingsToggleBtn.addEventListener('click', function() {
                elements.settingsPanel.classList.toggle('active');
                this.querySelector('.fa-chevron-down').classList.toggle('fa-rotate-180');
            });
            
            // Settings category click handlers
            document.querySelectorAll('.settings-category').forEach(category => {
                category.addEventListener('click', function() {
                    const categoryId = this.dataset.category;
                    
                    // Close all other content panels
                    document.querySelectorAll('.settings-content').forEach(content => {
                        content.classList.remove('active');
                    });
                    
                    // Open the selected content panel
                    const contentPanel = document.getElementById(`${categoryId}-content`);
                    if (contentPanel) {
                        contentPanel.classList.add('active');
                    }
                    
                    // Save the active category
                    state.activeSettingsCategory = categoryId;
                });
            });
            
            // File info popup
            document.querySelectorAll('.file-info').forEach(info => {
                info.addEventListener('click', function() {
                    const fileInputId = this.dataset.file;
                    const fileInput = document.getElementById(fileInputId);
                    if (fileInput.files.length > 0) {
                        elements.fileInfoText.textContent = fileInput.files[0].name;
                        elements.fileInfoPopup.style.display = 'flex';
                    }
                });
            });
            
            elements.fileInfoClose.addEventListener('click', function() {
                elements.fileInfoPopup.style.display = 'none';
            });
            
            // Reset button
            elements.resetBtn.addEventListener('click', resetSettings);
            
            // Form elements
            elements.bgType.addEventListener('change', function() {
                updateBackgroundControls();
                saveSettings();
            });
            elements.bgImage.addEventListener('change', function() {
                handleImageUpload({ target: this });
                saveSettings();
            });
            elements.bgVideoInput.addEventListener('change', function() {
                handleVideoUpload({ target: this });
                saveSettings();
            });
            elements.videoVolume.addEventListener('input', function() {
                updateVideoVolume();
                saveSettings();
            });
            elements.gradientDirection.addEventListener('change', function() {
                updatePreview();
                saveSettings();
            });
            elements.bgAudioInput.addEventListener('change', function() {
                handleAudioUpload({ target: this });
                saveSettings();
            });
            elements.audioVolume.addEventListener('input', function() {
                updateAudioVolume();
                saveSettings();
            });
            elements.audioLoop.addEventListener('change', function() {
                updateAudioLoop();
                saveSettings();
            });
            elements.questionSfxInput.addEventListener('change', function() {
                handleSfxUpload({ target: this });
                saveSettings();
            });
            elements.answerSfxInput.addEventListener('change', function() {
                handleSfxUpload({ target: this });
                saveSettings();
            });
            elements.countdownSfxInput.addEventListener('change', function() {
                handleSfxUpload({ target: this });
                saveSettings();
            });
            elements.warningSfxInput.addEventListener('change', function() {
                handleSfxUpload({ target: this });
                saveSettings();
            });
            elements.questionSfxVolume.addEventListener('input', function() {
                updateSfxVolume({ target: this });
                saveSettings();
            });
            elements.answerSfxVolume.addEventListener('input', function() {
                updateSfxVolume({ target: this });
                saveSettings();
            });
            elements.countdownSfxVolume.addEventListener('input', function() {
                updateSfxVolume({ target: this });
                saveSettings();
            });
            elements.warningSfxVolume.addEventListener('input', function() {
                updateSfxVolume({ target: this });
                saveSettings();
            });
            elements.brandingType.addEventListener('change', function() {
                updateBrandingControls();
                saveSettings();
            });
            elements.brandingLogoInput.addEventListener('change', function() {
                handleLogoUpload({ target: this });
                saveSettings();
            });
            elements.logoSize.addEventListener('input', function() {
                updateBrandingSize();
                saveSettings();
            });
            elements.brandingTextInput.addEventListener('input', function() {
                updateBrandingText();
                saveSettings();
            });
            elements.textFont.addEventListener('change', function() {
                updateBrandingText();
                saveSettings();
            });
            elements.textSize.addEventListener('input', function() {
                updateBrandingSize();
                saveSettings();
            });
            elements.brandingTextColor.addEventListener('input', function() {
                updateBrandingText();
                saveSettings();
            });
            elements.previewBtn.addEventListener('click', previewFirstQA);
            elements.startPreviewBtn.addEventListener('click', startFullPreview);
            elements.stopPreviewBtn.addEventListener('click', stopFullPreview);
            elements.restartPreviewBtn.addEventListener('click', restartPreview);
            elements.fontDecreaseBtn.addEventListener('click', function() {
                decreaseFontSize();
                saveSettings();
            });
            elements.fontIncreaseBtn.addEventListener('click', function() {
                increaseFontSize();
                saveSettings();
            });
            elements.fontFamily.addEventListener('change', function() {
                updateFontFamily();
                saveSettings();
            });
            elements.qLineDecreaseBtn.addEventListener('click', function() {
                changeLineHeight('question', -0.1);
                saveSettings();
            });
            elements.qLineIncreaseBtn.addEventListener('click', function() {
                changeLineHeight('question', 0.1);
                saveSettings();
            });
            elements.aLineDecreaseBtn.addEventListener('click', function() {
                changeLineHeight('answer', -0.1);
                saveSettings();
            });
            elements.aLineIncreaseBtn.addEventListener('click', function() {
                changeLineHeight('answer', 0.1);
                saveSettings();
            });
            elements.fullscreenBtn.addEventListener('click', toggleFullscreen);
            elements.fullscreenPreviewBtn.addEventListener('click', function() {
                startFullPreviewInFullscreen();
                // Hide button and start preview after 2 seconds
                setTimeout(() => {
                    if (state.isFullscreen) {
                        elements.fullscreenPreviewBtn.style.display = 'none';
                    }
                }, 2000);
            });
            
            // File input change handlers to show file names
            document.querySelectorAll('input[type="file"]').forEach(input => {
                input.addEventListener('change', function() {
                    const file = this.files[0];
                    if (file) {
                        const fileName = file.name;
                        const nameElement = document.getElementById(`${this.id}-name`).querySelector('span');
                        if (nameElement) {
                            // Show only first 5 characters followed by "..."
                            if (fileName.length > 7) {
                                nameElement.textContent = fileName.substring(0, 5) + '...';
                            } else {
                                nameElement.textContent = fileName;
                            }
                        }
                        
                        // Save file data for persistence
                        saveFileData(this.id, file);
                    }
                });
            });
            
            // QA input save
            elements.qaInput.addEventListener('input', function() {
                saveSettings();
            });
            
            const previewUpdateElements = [
                elements.aspectRatio, elements.layout,
                elements.questionColor, elements.answerColor,
                elements.textColor, elements.bgColor,
                elements.gradientColor1, elements.gradientColor2,
                elements.countdownEnabled, elements.countdownColor,
                elements.boxOpacity, elements.boxBorder,
                elements.questionTransition, elements.answerTransition,
                elements.textShadow
            ];
            
            previewUpdateElements.forEach(el => {
                el.addEventListener('input', function() {
                    updatePreview();
                    saveSettings();
                });
                el.addEventListener('change', function() {
                    updatePreview();
                    saveSettings();
                });
            });
        }

        function saveFileData(inputId, file) {
            const reader = new FileReader();
            
            reader.onload = function(e) {
                state.fileData[inputId] = {
                    name: file.name,
                    type: file.type,
                    data: e.target.result
                };
                saveSettings();
                
                // For images and videos, update the preview immediately
                if (inputId === 'bg-image') {
                    state.backgroundImage = e.target.result;
                    updatePreview();
                } else if (inputId === 'bg-video') {
                    elements.bgVideoElement.src = e.target.result;
                    elements.bgVideoElement.style.display = 'block';
                    elements.bgVideoElement.play().catch(err => {
                        console.log("Video autoplay prevented:", err);
                        elements.bgVideoElement.muted = true;
                        elements.bgVideoElement.play().catch(e => console.log("Muted play also prevented:", e));
                    });
                } else if (inputId === 'branding-logo') {
                    state.brandingLogo = e.target.result;
                    updateBranding();
                } else if (inputId === 'bg-audio') {
                    elements.bgAudioElement.src = e.target.result;
                    elements.bgAudioElement.volume = elements.audioVolume.value;
                    elements.bgAudioElement.loop = elements.audioLoop.checked;
                } else if (inputId === 'question-sfx') {
                    elements.questionSfxElement.src = e.target.result;
                    elements.questionSfxElement.volume = elements.questionSfxVolume.value;
                } else if (inputId === 'answer-sfx') {
                    elements.answerSfxElement.src = e.target.result;
                    elements.answerSfxElement.volume = elements.answerSfxVolume.value;
                } else if (inputId === 'countdown-sfx') {
                    elements.countdownSfxElement.src = e.target.result;
                    elements.countdownSfxElement.volume = elements.countdownSfxVolume.value;
                } else if (inputId === 'warning-sfx') {
                    elements.warningSfxElement.src = e.target.result;
                    elements.warningSfxElement.volume = elements.warningSfxVolume.value;
                }
            };
            
            if (inputId === 'bg-image' || inputId === 'branding-logo') {
                reader.readAsDataURL(file);
            } else {
                reader.readAsDataURL(file);
            }
        }

function handleFullscreenChange() {
    state.isFullscreen = !!document.fullscreenElement;
    
    if (state.isFullscreen) {
        // When entering fullscreen
        elements.fullscreenContainer.style.display = 'flex';
        
        // Add overlay to prevent minimize
        const overlay = document.createElement('div');
        overlay.className = 'prevent-minimize';
        overlay.id = 'prevent-minimize-overlay';
        document.body.appendChild(overlay);
        
        // Show fullscreen preview button
        elements.fullscreenPreviewBtn.style.display = 'block';
    } else {
        // When exiting fullscreen - ensure proper container restoration
        if (elements.previewContainer.parentNode === elements.fullscreenContainer) {
            if (state.originalPreviewContainerParent) {
                if (state.originalPreviewContainerNextSibling) {
                    state.originalPreviewContainerParent.insertBefore(
                        elements.previewContainer,
                        state.originalPreviewContainerNextSibling
                    );
                } else {
                    state.originalPreviewContainerParent.appendChild(elements.previewContainer);
                }
            }
        }
        
        // Cleanup UI
        elements.previewContainer.style.display = 'block';
        elements.fullscreenContainer.style.display = 'none';
        
        // Remove overlay
        const overlay = document.getElementById('prevent-minimize-overlay');
        if (overlay) overlay.remove();
        
        // Hide fullscreen button
        elements.fullscreenPreviewBtn.style.display = 'none';
        
        // Refresh UI
        setTimeout(() => {
            updatePreview();
            setActivePositionButtons();
        }, 100);
    }
}

        function handleVideoUpload(e) {
            const file = e.target.files[0];
            if (file) {
                if (state.backgroundVideo) {
                    URL.revokeObjectURL(state.backgroundVideo);
                }
                
                const videoURL = URL.createObjectURL(file);
                elements.bgVideoElement.src = videoURL;
                elements.bgVideoElement.style.display = 'block';
                state.backgroundVideo = videoURL;
                
                elements.bgVideoElement.onerror = function() {
                    console.error("Error loading video:", this.error);
                    alert("Error loading video. Please try a different file format (MP4, WebM).");
                };
                
                elements.bgVideoElement.oncanplay = function() {
                    this.play().catch(e => {
                        console.log("Video autoplay prevented:", e);
                        this.muted = true;
                        this.play().catch(e => console.log("Muted play also prevented:", e));
                    });
                };
                
                // Save file data
                saveFileData(e.target.id, file);
            }
        }

        function updateVideoVolume() {
            elements.bgVideoElement.volume = elements.videoVolume.value;
        }

        function handleAudioUpload(e) {
            const file = e.target.files[0];
            if (file) {
                if (state.backgroundAudio) {
                    URL.revokeObjectURL(state.backgroundAudio);
                }
                
                const audioURL = URL.createObjectURL(file);
                elements.bgAudioElement.src = audioURL;
                state.backgroundAudio = audioURL;
                
                elements.bgAudioElement.onerror = function() {
                    console.error("Error loading audio:", this.error);
                    alert("Error loading audio. Please try a different file format (MP3, WAV, OGG).");
                };
                
                // Set volume and loop based on current settings
                elements.bgAudioElement.volume = elements.audioVolume.value;
                elements.bgAudioElement.loop = elements.audioLoop.checked;
                
                // Save file data
                saveFileData(e.target.id, file);
            }
        }

        function playBackgroundAudio() {
            if (elements.bgAudioElement.src) {
                elements.bgAudioElement.currentTime = 0;
                elements.bgAudioElement.play().catch(e => {
                    console.log("Audio play failed:", e);
                });
            }
        }

        function updateAudioVolume() {
            elements.bgAudioElement.volume = elements.audioVolume.value;
        }

        function updateAudioLoop() {
            elements.bgAudioElement.loop = elements.audioLoop.checked;
        }

        function handleSfxUpload(e) {
            const file = e.target.files[0];
            if (file) {
                const audioURL = URL.createObjectURL(file);
                const type = e.target.id.split('-')[0]; // 'question', 'answer', 'countdown', or 'warning'
                
                if (type === 'question') {
                    state.questionSfx = audioURL;
                    elements.questionSfxElement.src = audioURL;
                    elements.questionSfxElement.volume = elements.questionSfxVolume.value;
                } else if (type === 'answer') {
                    state.answerSfx = audioURL;
                    elements.answerSfxElement.src = audioURL;
                    elements.answerSfxElement.volume = elements.answerSfxVolume.value;
                } else if (type === 'countdown') {
                    state.countdownSfx = audioURL;
                    elements.countdownSfxElement.src = audioURL;
                    elements.countdownSfxElement.volume = elements.countdownSfxVolume.value;
                } else if (type === 'warning') {
                    state.warningSfx = audioURL;
                    elements.warningSfxElement.src = audioURL;
                    elements.warningSfxElement.volume = elements.warningSfxVolume.value;
                }
                
                // Save file data
                saveFileData(e.target.id, file);
            }
        }

        function updateSfxVolume(e) {
            const type = e.target.id.split('-')[0]; // 'question', 'answer', 'countdown', or 'warning'
            const volume = e.target.value;
            
            if (type === 'question') {
                elements.questionSfxElement.volume = volume;
            } else if (type === 'answer') {
                elements.answerSfxElement.volume = volume;
            } else if (type === 'countdown') {
                elements.countdownSfxElement.volume = volume;
            } else if (type === 'warning') {
                elements.warningSfxElement.volume = volume;
            }
        }

        function playSound(type) {
            let audioElement;
            let defaultSound = true;
            
            if (type === 'question' && state.questionSfx) {
                audioElement = elements.questionSfxElement;
                defaultSound = false;
            } else if (type === 'answer' && state.answerSfx) {
                audioElement = elements.answerSfxElement;
                defaultSound = false;
            } else if (type === 'tick' && state.countdownSfx) {
                audioElement = elements.countdownSfxElement;
                defaultSound = false;
            } else if (type === 'warning' && state.warningSfx) {
                audioElement = elements.warningSfxElement;
                defaultSound = false;
            }
            
            if (!defaultSound && audioElement) {
                audioElement.currentTime = 0;
                audioElement.play().catch(e => {
                    console.log(`SFX play failed, using default sound for ${type}:`, e);
                    playDefaultSound(type);
                });
            } else {
                playDefaultSound(type);
            }
        }

        function playDefaultSound(type) {
            if (!state.audioContext) return;
            
            const oscillator = state.audioContext.createOscillator();
            const gainNode = state.audioContext.createGain();
            
            oscillator.connect(gainNode);
            gainNode.connect(state.audioContext.destination);
            
            if (type === 'question') {
                oscillator.frequency.value = 880;
            } else if (type === 'answer') {
                oscillator.frequency.value = 440;
            } else if (type === 'tick') {
                oscillator.frequency.value = 660;
            } else if (type === 'warning') {
                oscillator.frequency.value = 880;
                gainNode.gain.setValueAtTime(0.5, state.audioContext.currentTime);
                oscillator.start();
                gainNode.gain.exponentialRampToValueAtTime(0.01, state.audioContext.currentTime + 0.1);
                oscillator.stop(state.audioContext.currentTime + 0.1);
                return;
            }
            
            gainNode.gain.setValueAtTime(0.5, state.audioContext.currentTime);
            oscillator.start();
            gainNode.gain.exponentialRampToValueAtTime(0.01, state.audioContext.currentTime + 0.3);
            oscillator.stop(state.audioContext.currentTime + 0.3);
        }

        function updateBrandingControls() {
            const type = elements.brandingType.value;
            elements.brandingLogoControl.style.display = type === 'logo' ? 'block' : 'none';
            elements.brandingTextControl.style.display = type === 'text' ? 'block' : 'none';
            
            if (type === 'none') {
                elements.brandingElement.style.display = 'none';
            } else {
                elements.brandingElement.style.display = 'block';
                updateBranding();
            }
        }

        function handleLogoUpload(e) {
            const file = e.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (event) => {
                    state.brandingLogo = event.target.result;
                    updateBranding();
                    
                    // Save file data
                    saveFileData(e.target.id, file);
                };
                reader.readAsDataURL(file);
            }
        }

        function updateBranding() {
            const type = elements.brandingType.value;
            elements.brandingElement.innerHTML = '';
            
            if (type === 'logo' && state.brandingLogo) {
                const img = document.createElement('img');
                img.src = state.brandingLogo;
                img.className = 'branding-logo';
                elements.brandingElement.appendChild(img);
                updateElementPosition('branding', state.positions.branding);
                updateBrandingSize();
            } else if (type === 'text' && elements.brandingTextInput.value) {
                const text = document.createElement('div');
                text.className = 'branding-text';
                text.textContent = elements.brandingTextInput.value;
                elements.brandingElement.appendChild(text);
                updateElementPosition('branding', state.positions.branding);
                updateBrandingText();
                updateBrandingSize();
            }
        }

        function updateBrandingText() {
            const textElement = elements.brandingElement.querySelector('.branding-text');
            if (textElement) {
                textElement.style.fontFamily = elements.textFont.value;
                textElement.style.color = elements.brandingTextColor.value;
                textElement.style.textShadow = 'none'; // Ensure no text shadow
            }
        }

        function updateBrandingSize() {
            const type = elements.brandingType.value;
            if (type === 'logo') {
                const img = elements.brandingElement.querySelector('.branding-logo');
                if (img) {
                    img.style.maxWidth = `${elements.logoSize.value}%`;
                }
            } else if (type === 'text') {
                const text = elements.brandingElement.querySelector('.branding-text');
                if (text) {
                    text.style.fontSize = `${elements.textSize.value}px`;
                }
            }
        }

        function updateFontSizeDisplay() {
            elements.fontSizeValue.textContent = `${state.currentFontSize}rem`;
            elements.questionBox.style.fontSize = `${state.currentFontSize}rem`;
            elements.answerBox.style.fontSize = `${state.currentFontSize}rem`;
        }

        function decreaseFontSize() {
            if (state.currentFontSize > 0.8) {
                state.currentFontSize -= 0.1;
                updateFontSizeDisplay();
            }
        }

        function increaseFontSize() {
            if (state.currentFontSize < 2.5) {
                state.currentFontSize += 0.1;
                updateFontSizeDisplay();
            }
        }

        function updateFontFamily() {
            const fontFamily = elements.fontFamily.value;
            elements.questionBox.style.fontFamily = fontFamily;
            elements.answerBox.style.fontFamily = fontFamily;
        }

        function updateLineHeightDisplay() {
            elements.qLineValue.textContent = state.questionLineHeight;
            elements.aLineValue.textContent = state.answerLineHeight;
            elements.questionBox.style.lineHeight = state.questionLineHeight;
            elements.answerBox.style.lineHeight = state.answerLineHeight;
        }

        function changeLineHeight(type, change) {
            if (type === 'question') {
                state.questionLineHeight = Math.max(1, Math.min(3, state.questionLineHeight + change));
            } else {
                state.answerLineHeight = Math.max(1, Math.min(3, state.answerLineHeight + change));
            }
            updateLineHeightDisplay();
        }

        function updateBackgroundControls() {
            const type = elements.bgType.value;
            elements.bgColorControl.style.display = type === 'color' ? 'block' : 'none';
            elements.bgImageControl.style.display = type === 'image' ? 'block' : 'none';
            elements.bgVideoControl.style.display = type === 'video' ? 'block' : 'none';
            elements.bgGradientControl.style.display = type === 'gradient' ? 'block' : 'none';
            
            if (type === 'video') {
                elements.bgVideoElement.style.display = 'block';
            } else {
                elements.bgVideoElement.style.display = 'none';
            }
            
            updatePreview();
        }

        function handleImageUpload(e) {
            const file = e.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (event) => {
                    state.backgroundImage = event.target.result;
                    updatePreview();
                    
                    // Save file data
                    saveFileData(e.target.id, file);
                };
                reader.readAsDataURL(file);
            }
        }

        function updatePreview() {
            elements.previewContainer.style.aspectRatio = elements.aspectRatio.value;
            
            if (elements.layout.value === 'stacked') {
                elements.videoFrame.style.flexDirection = 'column';
                elements.videoFrame.style.gap = '5px';
            } else {
                elements.videoFrame.style.flexDirection = 'row';
                elements.videoFrame.style.gap = '10px';
            }
            
            elements.questionBox.style.backgroundColor = elements.questionColor.value;
            elements.answerBox.style.backgroundColor = elements.answerColor.value;
            elements.questionBox.style.color = elements.textColor.value;
            elements.answerBox.style.color = elements.textColor.value;
            
            elements.questionBox.style.opacity = elements.boxOpacity.value;
            elements.answerBox.style.opacity = elements.boxOpacity.value;
            elements.questionBox.style.border = `${elements.boxBorder.value}px solid rgba(0,0,0,0.2)`;
            elements.answerBox.style.border = `${elements.boxBorder.value}px solid rgba(0,0,0,0.2)`;
            
            // Ensure text shadow is always off
            elements.questionBox.style.textShadow = 'none';
            elements.answerBox.style.textShadow = 'none';
            
            switch(elements.bgType.value) {
                case 'color':
                    elements.previewContainer.style.background = elements.bgColor.value;
                    break;
                case 'image':
                    if (state.backgroundImage) {
                        elements.previewContainer.style.background = `url('${state.backgroundImage}')`;
                        elements.previewContainer.style.backgroundSize = 'cover';
                    }
                    break;
                case 'video':
                    break;
                case 'gradient':
                    const direction = elements.gradientDirection.value;
                    if (direction === 'circle') {
                        elements.previewContainer.style.background = 
                            `radial-gradient(${elements.gradientColor1.value}, ${elements.gradientColor2.value})`;
                    } else {
                        elements.previewContainer.style.background = 
                            `linear-gradient(${direction}, ${elements.gradientColor1.value}, ${elements.gradientColor2.value})`;
                    }
                    break;
            }
            
            elements.countdown.style.color = elements.countdownColor.value;
            
            // Update all element positions
            updateElementPosition('countdown', state.positions.countdown);
            updateElementPosition('question', state.positions.question);
            updateElementPosition('answer', state.positions.answer);
            updateElementPosition('branding', state.positions.branding);
        }

        function parseQAInput() {
            const lines = elements.qaInput.value.split('\n');
            state.qaPairs = lines
                .filter(line => line.trim() !== '')
                .map(line => {
                    const [question, answer] = line.split('|').map(part => part.trim());
                    return { question, answer };
                })
                .filter(qa => qa.question && qa.answer);
            
            return state.qaPairs.length > 0;
        }

        function showQuestion(index) {
            if (index < 0 || index >= state.qaPairs.length) return false;
            
            hideQA();
            hideCountdown();
            
            elements.questionBox.textContent = state.qaPairs[index].question;
            
            const transition = elements.questionTransition.value;
            elements.questionBox.className = 'question-box';
            setTimeout(() => {
                elements.questionBox.classList.add('visible');
                elements.questionBox.classList.add(`question-${transition}`);
            }, 10);
            
            playSound('question');
            
            return true;
        }

        function showAnswer(index) {
            if (index < 0 || index >= state.qaPairs.length) return false;
            
            elements.answerBox.textContent = state.qaPairs[index].answer;
            
            const transition = elements.answerTransition.value;
            elements.answerBox.className = 'answer-box';
            setTimeout(() => {
                elements.answerBox.style.display = 'block';
                elements.answerBox.classList.add('visible');
                elements.answerBox.classList.add(`answer-${transition}`);
            }, 10);
            
            playSound('answer');
            
            return true;
        }

        function hideQA() {
            // Hide both question and answer immediately without animation
            elements.questionBox.style.opacity = '0';
            elements.questionBox.classList.remove('visible');
            elements.questionBox.className = 'question-box';
            
            elements.answerBox.style.opacity = '0';
            elements.answerBox.classList.remove('visible');
            elements.answerBox.className = 'answer-box';
            elements.answerBox.style.display = 'none';
        }

        function showCountdown(seconds, callback) {
            if (!elements.countdownEnabled.checked) {
                if (callback) setTimeout(callback, seconds * 1000);
                return;
            }
            
            let remaining = seconds;
            elements.countdown.textContent = remaining;
            elements.countdown.classList.add('visible');
            
            // Play initial tick sound
            playSound('tick');
            
            state.countdownInterval = setInterval(() => {
                remaining--;
                elements.countdown.textContent = remaining;
                
                if (remaining > 0) {
                    // Regular tick sound for countdown
                    playSound('tick');
                    
                    // If we're at 1.25 seconds remaining, stop countdown sound and start warning sound
                    if (remaining <= 1.25) {
                        clearInterval(state.countdownInterval);
                        elements.countdownSfxElement.pause();
                        elements.countdownSfxElement.currentTime = 0;
                        
                        // Start warning sound
                        playSound('warning');
                        
                        // Set interval for warning sound to play every 0.1 seconds
                        state.warningInterval = setInterval(() => {
                            playSound('warning');
                        }, 100);
                        
                        // Hide countdown and execute callback when time is up
                        setTimeout(() => {
                            clearInterval(state.warningInterval);
                            hideCountdown();
                            if (callback) {
                                callback();
                            }
                        }, 1250);
                    }
                } else {
                    clearInterval(state.countdownInterval);
                    clearInterval(state.warningInterval);
                    
                    // Let the current sound finish playing naturally
                    hideCountdown();
                    if (callback) {
                        setTimeout(callback, 500);
                    }
                }
            }, 1000);
        }

        function hideCountdown() {
            elements.countdown.classList.remove('visible');
            // Don't stop the sound here - let it play out naturally
        }

        function queueAction(action) {
            state.actionQueue.push(action);
            if (!state.isProcessingAction) {
                processNextAction();
            }
        }

        function processNextAction() {
            if (state.actionQueue.length === 0) {
                state.isProcessingAction = false;
                return;
            }
            
            state.isProcessingAction = true;
            const action = state.actionQueue.shift();
            action(() => {
                setTimeout(processNextAction, 0);
            });
        }

        function previewFirstQA() {
            if (!parseQAInput()) {
                alert('Please enter valid questions and answers.');
                return;
            }
            
            updatePreview();
            
            queueAction((next) => {
                showQuestion(0);
                setTimeout(() => {
                    showCountdown(elements.countdownTime.value, () => {
                        showAnswer(0);
                        setTimeout(() => {
                            // Hide both question and answer at the same time
                            hideQA();
                            // Add 1 second delay before finishing
                            setTimeout(() => {
                                next();
                            }, 1000);
                        }, elements.answerTime.value * 1000);
                    });
                    next();
                }, elements.questionTime.value * 1000);
            });
        }

        function startFullPreview() {
            if (!parseQAInput()) {
                alert('Please enter valid questions and answers.');
                return;
            }
            
            state.isPreviewing = true;
            state.currentIndex = 0;
            elements.startPreviewBtn.disabled = true;
            elements.stopPreviewBtn.disabled = false;
            updatePreview();
            
            // Play background audio if available
            playBackgroundAudio();
            
            runPreviewSequence();
        }

        function startFullPreviewInFullscreen() {
            if (!state.isFullscreen) return;
            
            // 1. Hide the button immediately
            elements.fullscreenPreviewBtn.style.display = 'none';
            
            // 2. Start preview after 2 seconds
            setTimeout(() => {
                startFullPreview();
            }, 2000);
        }

        function runPreviewSequence() {
            if (!state.isPreviewing || state.currentIndex >= state.qaPairs.length) {
                stopFullPreview();
                return;
            }
            
            queueAction((next) => {
                showQuestion(state.currentIndex);
                setTimeout(() => {
                    showCountdown(elements.countdownTime.value, () => {
                        showAnswer(state.currentIndex);
                        
                        setTimeout(() => {
                            // Hide both question and answer at the same time
                            hideQA();
                            state.currentIndex++;
                            
                            // Add 0.5 seconds delay before next question starts (changed from 2 seconds)
                            setTimeout(() => {
                                runPreviewSequence();
                                next();
                            }, 500);
                        }, elements.answerTime.value * 1000);
                    });
                    next();
                }, elements.questionTime.value * 1000);
            });
        }

        function stopFullPreview() {
            state.isPreviewing = false;
            clearInterval(state.countdownInterval);
            clearInterval(state.warningInterval);
            hideCountdown();
            elements.startPreviewBtn.disabled = false;
            elements.stopPreviewBtn.disabled = true;
            hideQA();
            state.actionQueue = [];
            state.isProcessingAction = false;
            state.currentIndex = 0;
            
            // Pause background audio if playing
            if (elements.bgAudioElement) {
                elements.bgAudioElement.pause();
                elements.bgAudioElement.currentTime = 0;
            }
            
            if (state.isFullscreen) {
                elements.fullscreenPreviewBtn.style.display = 'block';
            }
            
            // Force reflow to ensure UI updates properly
            void elements.previewContainer.offsetHeight;
        }

        function restartPreview() {
            stopFullPreview();
            startFullPreview();
        }

function toggleFullscreen() {
    if (!document.fullscreenElement) {
        // Store original container position
        state.originalPreviewContainerParent = elements.previewContainer.parentNode;
        state.originalPreviewContainerNextSibling = elements.previewContainer.nextSibling;
        
        // Move to fullscreen container
        elements.fullscreenContainer.appendChild(elements.previewContainer);
        elements.fullscreenContainer.style.display = 'flex';
        
        // Request fullscreen with error handling
        elements.fullscreenContainer.requestFullscreen().catch(err => {
            console.error("Fullscreen error:", err);
            // Revert container position if failed
            if (state.originalPreviewContainerParent) {
                if (state.originalPreviewContainerNextSibling) {
                    state.originalPreviewContainerParent.insertBefore(
                        elements.previewContainer,
                        state.originalPreviewContainerNextSibling
                    );
                } else {
                    state.originalPreviewContainerParent.appendChild(elements.previewContainer);
                }
            }
            elements.fullscreenContainer.style.display = 'none';
            alert("Fullscreen mode unavailable. Please try another browser if this persists.");
        });
    } else {
        document.exitFullscreen().catch(err => {
            console.error("Exit fullscreen error:", err);
        });
    }
}

        // Initialize
        init();
    </script>
</body>
</html>
