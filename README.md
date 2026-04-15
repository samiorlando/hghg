<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>OmniaPro Broadcast Processor v11.2</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Roboto+Condensed:wght@300;400;700&family=Orbitron:wght@400;700;900&display=swap');

  :root {
    --bg-darkest: #0a0a0c;
    --bg-dark: #111115;
    --bg-panel: #1a1a20;
    --bg-panel-light: #22222a;
    --bg-module: #1e1e26;
    --border-color: #333340;
    --border-light: #444455;
    --text-primary: #e0e0e8;
    --text-secondary: #888899;
    --text-dim: #555566;
    --accent-blue: #00aaff;
    --accent-green: #00cc66;
    --accent-yellow: #ffcc00;
    --accent-red: #ff3344;
    --accent-orange: #ff8800;
    --led-green: #00ff44;
    --led-yellow: #ffdd00;
    --led-red: #ff2244;
    --knob-bg: #2a2a35;
    --slider-track: #333344;
    --slider-fill: #00aaff;
    --glow-blue: 0 0 10px rgba(0,170,255,0.3);
    --glow-green: 0 0 10px rgba(0,255,68,0.3);
  }

  * { margin:0; padding:0; box-sizing:border-box; }

  body {
    background: var(--bg-darkest);
    color: var(--text-primary);
    font-family: 'Roboto Condensed', sans-serif;
    overflow: hidden;
    height: 100vh;
    width: 100vw;
    user-select: none;
  }

  /* === TITLE BAR === */
  .title-bar {
    height: 32px;
    background: linear-gradient(180deg, #2a2a35 0%, #1a1a22 100%);
    display: flex;
    align-items: center;
    padding: 0 12px;
    border-bottom: 1px solid #444;
    -webkit-app-region: drag;
    z-index: 1000;
  }
  .title-bar .logo-text {
    font-family: 'Orbitron', sans-serif;
    font-weight: 900;
    font-size: 13px;
    background: linear-gradient(90deg, #00aaff, #00ff88);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    letter-spacing: 2px;
  }
  .title-bar .version {
    font-size: 10px;
    color: var(--text-dim);
    margin-left: 12px;
    font-family: 'Share Tech Mono', monospace;
  }
  .title-bar .controls {
    margin-left: auto;
    display: flex;
    gap: 6px;
    -webkit-app-region: no-drag;
  }
  .title-bar .controls button {
    width: 28px; height: 20px;
    border: none;
    background: #333;
    color: #aaa;
    font-size: 12px;
    cursor: pointer;
    border-radius: 3px;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .title-bar .controls button:hover { background: #555; color: #fff; }
  .title-bar .controls button.close:hover { background: #e44; }

  /* === MAIN LAYOUT === */
  .main-container {
    display: flex;
    height: calc(100vh - 32px);
    width: 100vw;
  }

  /* === LEFT PANEL - PRESETS === */
  .left-panel {
    width: 220px;
    min-width: 220px;
    background: var(--bg-dark);
    border-right: 1px solid var(--border-color);
    display: flex;
    flex-direction: column;
    overflow: hidden;
    position: relative;
  }
  .left-panel.collapsed { width: 0; min-width: 0; overflow: hidden; }
  .left-panel-header {
    padding: 10px 12px;
    background: var(--bg-panel);
    border-bottom: 1px solid var(--border-color);
    font-family: 'Orbitron', sans-serif;
    font-size: 10px;
    letter-spacing: 2px;
    color: var(--accent-blue);
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .preset-tree {
    flex: 1;
    overflow-y: auto;
    padding: 8px 0;
  }
  .preset-tree::-webkit-scrollbar { width: 6px; }
  .preset-tree::-webkit-scrollbar-track { background: var(--bg-darkest); }
  .preset-tree::-webkit-scrollbar-thumb { background: #444; border-radius: 3px; }

  .tree-category {
    padding: 6px 12px;
    font-size: 10px;
    font-weight: 700;
    color: var(--text-secondary);
    text-transform: uppercase;
    letter-spacing: 1px;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 6px;
  }
  .tree-category:hover { color: var(--text-primary); }
  .tree-category .arrow {
    font-size: 8px;
    transition: transform 0.2s;
  }
  .tree-category.open .arrow { transform: rotate(90deg); }

  .tree-items {
    display: none;
    padding-left: 8px;
  }
  .tree-items.visible { display: block; }

  .tree-item {
    padding: 5px 12px 5px 24px;
    font-size: 11px;
    color: var(--text-dim);
    cursor: pointer;
    transition: all 0.15s;
    border-left: 2px solid transparent;
  }
  .tree-item:hover {
    background: rgba(0,170,255,0.05);
    color: var(--text-primary);
  }
  .tree-item.active {
    background: rgba(0,170,255,0.1);
    color: var(--accent-blue);
    border-left-color: var(--accent-blue);
  }

  .preset-actions {
    padding: 10px 12px;
    border-top: 1px solid var(--border-color);
    display: flex;
    gap: 6px;
  }
  .preset-actions button {
    flex: 1;
    padding: 6px;
    background: var(--bg-panel);
    border: 1px solid var(--border-color);
    color: var(--text-secondary);
    font-size: 9px;
    font-family: 'Roboto Condensed', sans-serif;
    cursor: pointer;
    border-radius: 3px;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    transition: all 0.2s;
  }
  .preset-actions button:hover {
    background: var(--accent-blue);
    color: #fff;
    border-color: var(--accent-blue);
  }

  /* === CENTER CONTENT === */
  .center-content {
    flex: 1;
    display: flex;
    flex-direction: column;
    overflow: hidden;
    background: var(--bg-darkest);
  }

  /* === TOP METERS === */
  .meters-section {
    background: var(--bg-panel);
    border-bottom: 1px solid var(--border-color);
    padding: 8px 12px;
    display: flex;
    align-items: flex-start;
    gap: 16px;
    min-height: 160px;
  }

  .meter-group {
    display: flex;
    flex-direction: column;
    align-items: center;
    flex: 1;
  }
  .meter-label {
    font-family: 'Orbitron', sans-serif;
    font-size: 8px;
    letter-spacing: 2px;
    color: var(--text-secondary);
    margin-bottom: 6px;
    text-transform: uppercase;
  }
  .meter-container {
    display: flex;
    gap: 4px;
    align-items: flex-end;
  }
  .meter-bar-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
  }
  .meter-bar-label {
    font-size: 8px;
    color: var(--text-dim);
    font-family: 'Share Tech Mono', monospace;
  }
  .meter-bar {
    width: 18px;
    height: 120px;
    background: #111;
    border-radius: 2px;
    position: relative;
    overflow: hidden;
    border: 1px solid #333;
  }
  .meter-led {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    transition: height 0.05s ease-out;
    display: flex;
    flex-direction: column-reverse;
  }
  .meter-led-segment {
    height: 2px;
    width: 100%;
    margin-bottom: 1px;
    border-radius: 1px;
  }
  .meter-db {
    font-size: 9px;
    font-family: 'Share Tech Mono', monospace;
    color: var(--text-secondary);
    min-width: 36px;
    text-align: center;
  }
  .meter-db-value {
    color: var(--accent-green);
    font-weight: 700;
  }

  /* === PROCESSING CHAIN === */
  .processing-chain {
    flex: 1;
    display: flex;
    flex-direction: column;
    overflow-y: auto;
    padding: 10px;
    gap: 8px;
  }
  .processing-chain::-webkit-scrollbar { width: 8px; }
  .processing-chain::-webkit-scrollbar-track { background: var(--bg-darkest); }
  .processing-chain::-webkit-scrollbar-thumb { background: #444; border-radius: 4px; }

  .module-row {
    display: flex;
    gap: 8px;
  }

  .module {
    background: var(--bg-module);
    border: 1px solid var(--border-color);
    border-radius: 6px;
    overflow: hidden;
    flex: 1;
    min-width: 0;
    transition: border-color 0.3s;
  }
  .module:hover { border-color: var(--border-light); }
  .module.active-module { border-color: var(--accent-blue); box-shadow: var(--glow-blue); }

  .module-header {
    padding: 8px 12px;
    background: linear-gradient(180deg, #252530 0%, #1e1e28 100%);
    border-bottom: 1px solid var(--border-color);
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .module-title {
    font-family: 'Orbitron', sans-serif;
    font-size: 9px;
    letter-spacing: 2px;
    color: var(--accent-blue);
    text-transform: uppercase;
  }
  .module-status {
    margin-left: auto;
    display: flex;
    align-items: center;
    gap: 6px;
  }
  .status-led {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #333;
    transition: all 0.3s;
  }
  .status-led.on {
    background: var(--accent-green);
    box-shadow: 0 0 6px var(--accent-green);
  }
  .status-led.warning {
    background: var(--accent-yellow);
    box-shadow: 0 0 6px var(--accent-yellow);
  }
  .status-led.error {
    background: var(--accent-red);
    box-shadow: 0 0 6px var(--accent-red);
  }

  /* Power button */
  .power-btn {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    border: 2px solid #444;
    background: #222;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.3s;
    position: relative;
  }
  .power-btn.on {
    border-color: var(--accent-green);
    background: rgba(0,255,68,0.1);
  }
  .power-btn.on::after {
    content: '';
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: var(--accent-green);
    box-shadow: 0 0 4px var(--accent-green);
  }
  .power-btn::before {
    content: '⏻';
    font-size: 12px;
    color: #555;
    transition: color 0.3s;
  }
  .power-btn.on::before { color: var(--accent-green); }

  .module-body {
    padding: 12px;
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    justify-content: center;
    align-items: flex-start;
  }

  /* === KNOB CONTROL === */
  .knob-control {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    cursor: pointer;
  }
  .knob {
    width: 48px;
    height: 48px;
    border-radius: 50%;
    background: radial-gradient(circle at 35% 35%, #3a3a48, #1a1a25);
    border: 2px solid #444;
    position: relative;
    box-shadow: 0 2px 8px rgba(0,0,0,0.5), inset 0 1px 2px rgba(255,255,255,0.05);
    transition: border-color 0.2s;
  }
  .knob:hover { border-color: #666; }
  .knob-indicator {
    position: absolute;
    width: 3px;
    height: 14px;
    background: var(--accent-blue);
    top: 4px;
    left: 50%;
    margin-left: -1.5px;
    border-radius: 1px;
    box-shadow: 0 0 4px var(--accent-blue);
    transform-origin: bottom center;
  }
  .knob-value {
    font-family: 'Share Tech Mono', monospace;
    font-size: 10px;
    color: var(--accent-blue);
    text-align: center;
  }
  .knob-label {
    font-size: 8px;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 0.5px;
    text-align: center;
  }

  /* === SLIDER CONTROL === */
  .slider-control {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    width: 40px;
  }
  .slider-track-vertical {
    width: 6px;
    height: 80px;
    background: var(--slider-track);
    border-radius: 3px;
    position: relative;
    cursor: pointer;
  }
  .slider-fill-vertical {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: linear-gradient(0deg, var(--accent-blue), #0066cc);
    border-radius: 3px;
    transition: height 0.05s;
  }
  .slider-thumb {
    position: absolute;
    left: 50%;
    transform: translate(-50%, 50%);
    width: 18px;
    height: 8px;
    background: linear-gradient(180deg, #ddd, #999);
    border-radius: 2px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.5);
    cursor: grab;
  }
  .slider-thumb:active { cursor: grabbing; }
  .slider-value {
    font-family: 'Share Tech Mono', monospace;
    font-size: 9px;
    color: var(--accent-blue);
    text-align: center;
  }
  .slider-label {
    font-size: 8px;
    color: var(--text-dim);
    text-transform: uppercase;
    text-align: center;
  }

  /* === TOGGLE BUTTON === */
  .toggle-btn {
    padding: 4px 12px;
    background: var(--bg-panel);
    border: 1px solid var(--border-color);
    color: var(--text-dim);
    font-size: 9px;
    font-family: 'Roboto Condensed', sans-serif;
    cursor: pointer;
    border-radius: 3px;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    transition: all 0.2s;
  }
  .toggle-btn.on {
    background: rgba(0,170,255,0.15);
    border-color: var(--accent-blue);
    color: var(--accent-blue);
  }

  /* === EQ DISPLAY === */
  .eq-display {
    width: 100%;
    height: 100px;
    background: #0a0a10;
    border: 1px solid #222;
    border-radius: 4px;
    position: relative;
    overflow: hidden;
  }
  .eq-canvas {
    width: 100%;
    height: 100%;
  }
  .eq-bands {
    display: flex;
    justify-content: space-around;
    padding: 4px 0;
  }
  .eq-band-label {
    font-size: 7px;
    color: var(--text-dim);
    text-align: center;
    font-family: 'Share Tech Mono', monospace;
  }

  /* === RIGHT PANEL === */
  .right-panel {
    width: 200px;
    min-width: 200px;
    background: var(--bg-dark);
    border-left: 1px solid var(--border-color);
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }
  .right-panel.collapsed { width: 0; min-width: 0; overflow: hidden; }

  .branding-section {
    padding: 16px 12px;
    text-align: center;
    border-bottom: 1px solid var(--border-color);
  }
  .brand-logo {
    font-family: 'Orbitron', sans-serif;
    font-size: 16px;
    font-weight: 900;
    background: linear-gradient(90deg, #00aaff, #00ff88);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    letter-spacing: 3px;
  }
  .brand-sub {
    font-size: 9px;
    color: var(--text-dim);
    letter-spacing: 2px;
    margin-top: 4px;
    text-transform: uppercase;
  }

  .info-section {
    padding: 12px;
    flex: 1;
    overflow-y: auto;
  }
  .info-group {
    margin-bottom: 12px;
  }
  .info-group-title {
    font-size: 8px;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 1px;
    margin-bottom: 6px;
    border-bottom: 1px solid #222;
    padding-bottom: 4px;
  }
  .info-row {
    display: flex;
    justify-content: space-between;
    padding: 3px 0;
    font-size: 10px;
  }
  .info-key { color: var(--text-secondary); }
  .info-val {
    color: var(--accent-blue);
    font-family: 'Share Tech Mono', monospace;
  }

  .output-meter {
    padding: 12px;
    border-top: 1px solid var(--border-color);
  }
  .output-meter-label {
    font-size: 8px;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 1px;
    margin-bottom: 6px;
  }
  .output-meter-bar {
    height: 8px;
    background: #111;
    border-radius: 4px;
    overflow: hidden;
    border: 1px solid #333;
  }
  .output-meter-fill {
    height: 100%;
    border-radius: 4px;
    transition: width 0.05s;
    background: linear-gradient(90deg, #00cc44, #88cc00, #ffcc00, #ff4400);
  }

  /* === RESIZE HANDLES === */
  .resize-handle {
    width: 4px;
    cursor: col-resize;
    background: transparent;
    transition: background 0.2s;
    position: relative;
    z-index: 10;
  }
  .resize-handle:hover { background: var(--accent-blue); }
  .resize-handle-h {
    height: 4px;
    cursor: row-resize;
    background: transparent;
    transition: background 0.2s;
  }
  .resize-handle-h:hover { background: var(--accent-blue); }

  /* === BOTTOM BAR === */
  .bottom-bar {
    height: 24px;
    background: var(--bg-panel);
    border-top: 1px solid var(--border-color);
    display: flex;
    align-items: center;
    padding: 0 12px;
    gap: 16px;
    font-size: 9px;
    color: var(--text-dim);
    font-family: 'Share Tech Mono', monospace;
  }
  .bottom-bar .status-item {
    display: flex;
    align-items: center;
    gap: 4px;
  }
  .bottom-bar .dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: var(--accent-green);
    box-shadow: 0 0 4px var(--accent-green);
  }
  .bottom-bar .dot.warn { background: var(--accent-yellow); box-shadow: 0 0 4px var(--accent-yellow); }
  .bottom-bar .spacer { flex: 1; }

  /* === TOOLBAR === */
  .toolbar {
    height: 36px;
    background: var(--bg-panel);
    border-bottom: 1px solid var(--border-color);
    display: flex;
    align-items: center;
    padding: 0 12px;
    gap: 4px;
  }
  .toolbar-btn {
    padding: 4px 10px;
    background: transparent;
    border: 1px solid transparent;
    color: var(--text-secondary);
    font-size: 10px;
    font-family: 'Roboto Condensed', sans-serif;
    cursor: pointer;
    border-radius: 3px;
    transition: all 0.2s;
    display: flex;
    align-items: center;
    gap: 4px;
  }
  .toolbar-btn:hover { background: rgba(0,170,255,0.1); color: var(--accent-blue); border-color: rgba(0,170,255,0.2); }
  .toolbar-btn.active { background: rgba(0,170,255,0.15); color: var(--accent-blue); border-color: var(--accent-blue); }
  .toolbar-sep {
    width: 1px;
    height: 20px;
    background: var(--border-color);
    margin: 0 4px;
  }

  /* === COMPRESSOR BAND DISPLAY === */
  .band-display {
    display: flex;
    gap: 6px;
    width: 100%;
    flex-wrap: wrap;
    justify-content: center;
  }
  .band-card {
    background: #151520;
    border: 1px solid #2a2a35;
    border-radius: 4px;
    padding: 8px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    min-width: 70px;
    transition: border-color 0.2s;
  }
  .band-card.active { border-color: var(--accent-blue); }
  .band-card-title {
    font-size: 9px;
    font-family: 'Orbitron', sans-serif;
    color: var(--accent-blue);
    letter-spacing: 1px;
  }
  .band-card-freq {
    font-size: 8px;
    color: var(--text-dim);
    font-family: 'Share Tech Mono', monospace;
  }
  .band-card-controls {
    display: flex;
    gap: 4px;
    flex-wrap: wrap;
    justify-content: center;
  }
  .mini-slider {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2px;
  }
  .mini-slider input[type="range"] {
    writing-mode: vertical-lr;
    direction: rtl;
    height: 40px;
    width: 6px;
    -webkit-appearance: none;
    appearance: none;
    background: var(--slider-track);
    border-radius: 3px;
    outline: none;
  }
  .mini-slider input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 14px;
    height: 8px;
    background: linear-gradient(180deg, #ddd, #999);
    border-radius: 2px;
    cursor: pointer;
  }
  .mini-slider-label {
    font-size: 7px;
    color: var(--text-dim);
    text-transform: uppercase;
  }
  .mini-slider-value {
    font-size: 8px;
    color: var(--accent-blue);
    font-family: 'Share Tech Mono', monospace;
  }

  /* === LIMITER DISPLAY === */
  .limiter-row {
    display: flex;
    gap: 10px;
    width: 100%;
    justify-content: center;
    flex-wrap: wrap;
  }
  .limiter-card {
    background: #151520;
    border: 1px solid #2a2a35;
    border-radius: 4px;
    padding: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
    min-width: 100px;
  }
  .limiter-title {
    font-size: 9px;
    font-family: 'Orbitron', sans-serif;
    color: var(--accent-orange);
    letter-spacing: 1px;
  }
  .limiter-meter {
    width: 8px;
    height: 50px;
    background: #111;
    border-radius: 2px;
    border: 1px solid #333;
    overflow: hidden;
    position: relative;
  }
  .limiter-meter-fill {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: linear-gradient(0deg, #00cc44, #ffcc00, #ff3344);
    border-radius: 2px;
    transition: height 0.05s;
  }

  /* === AGC DISPLAY === */
  .agc-display {
    display: flex;
    gap: 12px;
    width: 100%;
    justify-content: center;
    flex-wrap: wrap;
  }
  .agc-param {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
  }
  .agc-param-label {
    font-size: 8px;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }
  .agc-param-value {
    font-size: 11px;
    color: var(--accent-blue);
    font-family: 'Share Tech Mono', monospace;
    font-weight: 700;
  }

  /* === DRAGGABLE WINDOWS === */
  .draggable-panel {
    position: absolute;
    background: var(--bg-panel);
    border: 1px solid var(--border-color);
    border-radius: 6px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.6);
    z-index: 100;
    min-width: 200px;
    min-height: 150px;
    display: none;
  }
  .draggable-panel.visible { display: block; }
  .draggable-panel .panel-titlebar {
    height: 28px;
    background: linear-gradient(180deg, #2a2a35, #1e1e28);
    border-bottom: 1px solid var(--border-color);
    border-radius: 6px 6px 0 0;
    display: flex;
    align-items: center;
    padding: 0 10px;
    cursor: move;
    user-select: none;
  }
  .draggable-panel .panel-titlebar span {
    font-family: 'Orbitron', sans-serif;
    font-size: 9px;
    letter-spacing: 1px;
    color: var(--accent-blue);
  }
  .draggable-panel .panel-close {
    margin-left: auto;
    width: 20px;
    height: 20px;
    border: none;
    background: transparent;
    color: #888;
    font-size: 14px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 3px;
  }
  .draggable-panel .panel-close:hover { background: #e44; color: #fff; }
  .draggable-panel .panel-body {
    padding: 12px;
  }

  /* === RESPONSIVE === */
  @media (max-width: 1200px) {
    .left-panel { width: 180px; min-width: 180px; }
    .right-panel { width: 160px; min-width: 160px; }
  }
  @media (max-width: 900px) {
    .left-panel { display: none; }
    .right-panel { display: none; }
  }

  /* === ANIMATIONS === */
  @keyframes pulse { 0%,100% { opacity:1; } 50% { opacity:0.5; } }
  @keyframes glow { 0%,100% { box-shadow: 0 0 5px var(--accent-blue); } 50% { box-shadow: 0 0 15px var(--accent-blue); } }
  .processing .module.active-module { animation: glow 2s infinite; }

  /* === CONTEXT MENU === */
  .context-menu {
    position: fixed;
    background: var(--bg-panel);
    border: 1px solid var(--border-color);
    border-radius: 4px;
    box-shadow: 0 4px 16px rgba(0,0,0,0.5);
    z-index: 10000;
    display: none;
    min-width: 160px;
  }
  .context-menu.visible { display: block; }
  .context-menu-item {
    padding: 6px 12px;
    font-size: 11px;
    color: var(--text-primary);
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 8px;
    transition: background 0.1s;
  }
  .context-menu-item:hover { background: rgba(0,170,255,0.15); }
  .context-menu-sep { height: 1px; background: var(--border-color); margin: 2px 0; }

  /* === MODAL === */
  .modal-overlay {
    position: fixed;
    top: 0; left: 0; right: 0; bottom: 0;
    background: rgba(0,0,0,0.7);
    z-index: 5000;
    display: none;
    align-items: center;
    justify-content: center;
  }
  .modal-overlay.visible { display: flex; }
  .modal {
    background: var(--bg-panel);
    border: 1px solid var(--border-color);
    border-radius: 8px;
    padding: 24px;
    min-width: 300px;
    max-width: 500px;
    box-shadow: 0 16px 48px rgba(0,0,0,0.5);
  }
  .modal-title {
    font-family: 'Orbitron', sans-serif;
    font-size: 14px;
    color: var(--accent-blue);
    letter-spacing: 2px;
    margin-bottom: 16px;
  }
  .modal-actions {
    display: flex;
    gap: 8px;
    justify-content: flex-end;
    margin-top: 16px;
  }
  .modal-btn {
    padding: 6px 16px;
    border: 1px solid var(--border-color);
    background: var(--bg-panel-light);
    color: var(--text-primary);
    font-size: 11px;
    cursor: pointer;
    border-radius: 4px;
    transition: all 0.2s;
  }
  .modal-btn.primary {
    background: var(--accent-blue);
    border-color: var(--accent-blue);
    color: #fff;
  }
  .modal-btn:hover { opacity: 0.8; }

  /* === SELECT STYLING === */
  select {
    background: var(--bg-panel-light);
    border: 1px solid var(--border-color);
    color: var(--text-primary);
    padding: 4px 8px;
    font-size: 10px;
    border-radius: 3px;
    font-family: 'Roboto Condensed', sans-serif;
    cursor: pointer;
  }
  select:focus { outline: none; border-color: var(--accent-blue); }

  /* === TOOLTIP === */
  .tooltip {
    position: fixed;
    background: #1a1a22;
    border: 1px solid var(--border-color);
    color: var(--text-primary);
    padding: 4px 8px;
    font-size: 10px;
    border-radius: 3px;
    z-index: 10001;
    pointer-events: none;
    display: none;
    box-shadow: 0 2px 8px rgba(0,0,0,0.5);
  }
  .tooltip.visible { display: block; }

  /* === SPECTRUM DISPLAY === */
  .spectrum-container {
    width: 100%;
    height: 60px;
    background: #0a0a10;
    border: 1px solid #222;
    border-radius: 4px;
    overflow: hidden;
  }
  .spectrum-canvas {
    width: 100%;
    height: 100%;
  }

  /* Phase meter */
  .phase-meter {
    width: 60px;
    height: 60px;
    border-radius: 50%;
    background: #0a0a10;
    border: 1px solid #333;
    position: relative;
    overflow: hidden;
  }
  .phase-meter canvas {
    width: 100%;
    height: 100%;
  }
</style>
</head>
<body>

<!-- TITLE BAR -->
<div class="title-bar">
  <span class="logo-text">OmniaPro</span>
  <span class="version">Broadcast Processor v11.2.0</span>
  <div class="controls">
    <button onclick="togglePanel('left')" title="Toggle Presets">☰</button>
    <button onclick="togglePanel('right')" title="Toggle Info">ℹ</button>
    <button onclick="minimizeApp()" title="Minimize">─</button>
    <button class="close" onclick="closeApp()" title="Close">✕</button>
  </div>
</div>

<!-- MAIN LAYOUT -->
<div class="main-container">

  <!-- LEFT PANEL - PRESETS -->
  <div class="left-panel" id="leftPanel">
    <div class="left-panel-header">
      <span>PRESETS</span>
      <span style="font-size:8px;color:var(--text-dim)">📁</span>
    </div>
    <div class="preset-tree" id="presetTree">
      <div class="tree-category open" onclick="toggleCategory(this)">
        <span class="arrow">▶</span> FM Broadcast
      </div>
      <div class="tree-items visible">
        <div class="tree-item active" onclick="loadPreset('fm-loud')">FM Loud</div>
        <div class="tree-item" onclick="loadPreset('fm-clean')">FM Clean</div>
        <div class="tree-item" onclick="loadPreset('fm-hot')">FM Hot</div>
        <div class="tree-item" onclick="loadPreset('fm-classic')">FM Classic</div>
        <div class="tree-item" onclick="loadPreset('fm-news')">FM News/Talk</div>
      </div>
      <div class="tree-category" onclick="toggleCategory(this)">
        <span class="arrow">▶</span> Streaming
      </div>
      <div class="tree-items">
        <div class="tree-item" onclick="loadPreset('stream-clean')">Streaming Clean</div>
        <div class="tree-item" onclick="loadPreset('stream-loud')">Streaming Loud</div>
        <div class="tree-item" onclick="loadPreset('stream-podcast')">Podcast</div>
      </div>
      <div class="tree-category" onclick="toggleCategory(this)">
        <span class="arrow">▶</span> Music
      </div>
      <div class="tree-items">
        <div class="tree-item" onclick="loadPreset('bass-boost')">Bass Boost</div>
        <div class="tree-item" onclick="loadPreset('bright')">Bright & Airy</div>
        <div class="tree-item" onclick="loadPreset('warm')">Warm Analog</div>
        <div class="tree-item" onclick="loadPreset('rock')">Rock/Pop</div>
      </div>
      <div class="tree-category" onclick="toggleCategory(this)">
        <span class="arrow">▶</span> Custom
      </div>
      <div class="tree-items">
        <div class="tree-item" onclick="loadPreset('custom1')">User Preset 1</div>
        <div class="tree-item" onclick="loadPreset('custom2')">User Preset 2</div>
      </div>
    </div>
    <div class="preset-actions">
      <button onclick="savePreset()">💾 Save</button>
      <button onclick="exportPreset()">📤 Export</button>
      <button onclick="importPreset()">📥 Import</button>
    </div>
  </div>

  <div class="resize-handle" id="resizeLeft" onmousedown="startResize(event,'left')"></div>

  <!-- CENTER CONTENT -->
  <div class="center-content">

    <!-- TOOLBAR -->
    <div class="toolbar">
      <button class="toolbar-btn active" onclick="setInputSource('mic')" id="btnMic">🎤 Mic</button>
      <button class="toolbar-btn" onclick="setInputSource('file')" id="btnFile">📁 File</button>
      <button class="toolbar-btn" onclick="setInputSource('sine')" id="btnSine">〰 Sine Test</button>
      <button class="toolbar-btn" onclick="setInputSource('noise')" id="btnNoise">📢 Noise</button>
      <div class="toolbar-sep"></div>
      <select id="inputDeviceSelect" onchange="changeInputDevice(this.value)">
        <option value="">Default Input</option>
      </select>
      <select id="outputDeviceSelect" onchange="changeOutputDevice(this.value)">
        <option value="">Default Output</option>
      </select>
      <div class="toolbar-sep"></div>
      <button class="toolbar-btn" onclick="bypassAll()" id="btnBypass">⏸ Bypass All</button>
      <button class="toolbar-btn" onclick="resetAll()">🔄 Reset</button>
      <div class="toolbar-sep"></div>
      <button class="toolbar-btn" onclick="showSettings()">⚙ Settings</button>
    </div>

    <!-- METERS SECTION -->
    <div class="meters-section" id="metersSection">
      <!-- INPUT METER -->
      <div class="meter-group">
        <div class="meter-label">INPUT</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterInputL">
              <div class="meter-led" id="meterInputLFill"></div>
            </div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterInputLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterInputR">
              <div class="meter-led" id="meterInputRFill"></div>
            </div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterInputRVal">-∞ dB</div>
          </div>
        </div>
      </div>

      <!-- AGC METER -->
      <div class="meter-group">
        <div class="meter-label">AGC</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterAGCL">
              <div class="meter-led" id="meterAGCLFill"></div>
            </div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterAGCLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterAGCR">
              <div class="meter-led" id="meterAGCRFill"></div>
            </div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterAGCRVal">-∞ dB</div>
          </div>
        </div>
      </div>

      <!-- COMPRESSOR METER -->
      <div class="meter-group">
        <div class="meter-label">COMPRESSOR</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterCompL">
              <div class="meter-led" id="meterCompLFill"></div>
            </div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterCompLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterCompR">
              <div class="meter-led" id="meterCompRFill"></div>
            </div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterCompRVal">-∞ dB</div>
          </div>
        </div>
      </div>

      <!-- EQ METER -->
      <div class="meter-group">
        <div class="meter-label">EQ</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterEQL">
              <div class="meter-led" id="meterEQLFill"></div>
            </div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterEQLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterEQR">
              <div class="meter-led" id="meterEQRFill"></div>
            </div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterEQRVal">-∞ dB</div>
          </div>
        </div>
      </div>

      <!-- LIMITER METER -->
      <div class="meter-group">
        <div class="meter-label">LIMITER</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterLimL">
              <div class="meter-led" id="meterLimLFill"></div>
            </div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterLimLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterLimR">
              <div class="meter-led" id="meterLimRFill"></div>
            </div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterLimRVal">-∞ dB</div>
          </div>
        </div>
      </div>

      <!-- OUTPUT METER -->
      <div class="meter-group">
        <div class="meter-label">OUTPUT</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterOutL">
              <div class="meter-led" id="meterOutLFill"></div>
            </div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterOutLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterOutR">
              <div class="meter-led" id="meterOutRFill"></div>
            </div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterOutRVal">-∞ dB</div>
          </div>
        </div>
      </div>

      <!-- PHASE METER -->
      <div class="meter-group">
        <div class="meter-label">PHASE</div>
        <div class="phase-meter">
          <canvas id="phaseCanvas" width="60" height="60"></canvas>
        </div>
      </div>

      <!-- SPECTRUM -->
      <div class="meter-group" style="flex:2">
        <div class="meter-label">SPECTRUM</div>
        <div class="spectrum-container">
          <canvas id="spectrumCanvas" class="spectrum-canvas"></canvas>
        </div>
      </div>
    </div>

    <!-- PROCESSING MODULES -->
    <div class="processing-chain" id="processingChain">

      <!-- ROW 1: INPUT + AGC -->
      <div class="module-row">
        <!-- INPUT MODULE -->
        <div class="module active-module" id="modInput">
          <div class="module-header">
            <div class="power-btn on" id="pwrInput" onclick="togglePower('Input')"></div>
            <span class="module-title">INPUT</span>
            <div class="module-status">
              <span class="status-led on" id="statusInput"></span>
            </div>
          </div>
          <div class="module-body">
            <div class="knob-control" onmousedown="startKnob(event,'inputGain')">
              <div class="knob" id="knobInputGain">
                <div class="knob-indicator" id="knobInputGainInd"></div>
              </div>
              <div class="knob-value" id="valInputGain">0.0 dB</div>
              <div class="knob-label">Gain</div>
            </div>
            <div class="knob-control" onmousedown="startKnob(event,'inputTrim')">
              <div class="knob" id="knobInputTrim">
                <div class="knob-indicator" id="knobInputTrimInd"></div>
              </div>
              <div class="knob-value" id="valInputTrim">0.0 dB</div>
              <div class="knob-label">Trim</div>
            </div>
            <div class="knob-control" onmousedown="startKnob(event,'inputPhase')">
              <div class="knob" id="knobInputPhase">
                <div class="knob-indicator" id="knobInputPhaseInd"></div>
              </div>
              <div class="knob-value" id="valInputPhase">0°</div>
              <div class="knob-label">Phase</div>
            </div>
            <div style="display:flex;flex-direction:column;gap:4px;align-items:center;">
              <div class="toggle-btn on" id="btnInputMono" onclick="toggleMono()">MONO</div>
              <div class="toggle-btn" id="btnInputMute" onclick="toggleMute()">MUTE</div>
              <div class="toggle-btn" id="btnInputPhaseInv" onclick="togglePhaseInv()">PHASE ∅</div>
            </div>
          </div>
        </div>

        <!-- AGC MODULE -->
        <div class="module" id="modAGC">
          <div class="module-header">
            <div class="power-btn on" id="pwrAGC" onclick="togglePower('AGC')"></div>
            <span class="module-title">AGC</span>
            <div class="module-status">
              <span class="status-led on" id="statusAGC"></span>
            </div>
          </div>
          <div class="module-body">
            <div class="knob-control" onmousedown="startKnob(event,'agcTarget')">
              <div class="knob" id="knobAgcTarget">
                <div class="knob-indicator" id="knobAgcTargetInd"></div>
              </div>
              <div class="knob-value" id="valAgcTarget">-20 dB</div>
              <div class="knob-label">Target</div>
            </div>
            <div class="knob-control" onmousedown="startKnob(event,'agcSpeed')">
              <div class="knob" id="knobAgcSpeed">
                <div class="knob-indicator" id="knobAgcSpeedInd"></div>
              </div>
              <div class="knob-value" id="valAgcSpeed">Med</div>
              <div class="knob-label">Speed</div>
            </div>
            <div class="knob-control" onmousedown="startKnob(event,'agcRange')">
              <div class="knob" id="knobAgcRange">
                <div class="knob-indicator" id="knobAgcRangeInd"></div>
              </div>
              <div class="knob-value" id="valAgcRange">30 dB</div>
              <div class="knob-label">Range</div>
            </div>
            <div class="knob-control" onmousedown="startKnob(event,'agcAttack')">
              <div class="knob" id="knobAgcAttack">
                <div class="knob-indicator" id="knobAgcAttackInd"></div>
              </div>
              <div class="knob-value" id="valAgcAttack">50 ms</div>
              <div class="knob-label">Attack</div>
            </div>
            <div class="knob-control" onmousedown="startKnob(event,'agcRelease')">
              <div class="knob" id="knobAgcRelease">
                <div class="knob-indicator" id="knobAgcReleaseInd"></div>
              </div>
              <div class="knob-value" id="valAgcRelease">200 ms</div>
              <div class="knob-label">Release</div>
            </div>
            <div style="display:flex;flex-direction:column;gap:4px;align-items:center;">
              <div class="toggle-btn on" id="btnAgcOn" onclick="toggleAgc()">AGC ON</div>
              <div class="toggle-btn" id="btnAgcAuto" onclick="toggleAgcAuto()">AUTO</div>
            </div>
          </div>
        </div>
      </div>

      <!-- ROW 2: MULTIBAND COMPRESSOR -->
      <div class="module active-module" id="modComp">
        <div class="module-header">
          <div class="power-btn on" id="pwrComp" onclick="togglePower('Comp')"></div>
          <span class="module-title">MULTIBAND COMPRESSOR</span>
          <div class="module-status">
            <span class="status-led on" id="statusComp"></span>
            <span style="font-size:8px;color:var(--text-dim)">5 BANDS</span>
          </div>
        </div>
        <div class="module-body">
          <div class="band-display" id="compBands">
            <!-- B1 -->
            <div class="band-card active" id="band1">
              <div class="band-card-title">B1</div>
              <div class="band-card-freq">32-120 Hz</div>
              <div class="band-card-controls">
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b1Thresh">-24</div>
                  <input type="range" min="-60" max="0" value="-24" id="sliderB1Thresh" oninput="updateBandParam(1,'threshold',this.value)">
                  <div class="mini-slider-label">Thresh</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b1Ratio">4:1</div>
                  <input type="range" min="1" max="20" value="4" id="sliderB1Ratio" oninput="updateBandParam(1,'ratio',this.value)">
                  <div class="mini-slider-label">Ratio</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b1Attack">10</div>
                  <input type="range" min="1" max="100" value="10" id="sliderB1Attack" oninput="updateBandParam(1,'attack',this.value)">
                  <div class="mini-slider-label">Atk</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b1Release">100</div>
                  <input type="range" min="10" max="1000" value="100" id="sliderB1Release" oninput="updateBandParam(1,'release',this.value)">
                  <div class="mini-slider-label">Rel</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b1Makeup">6</div>
                  <input type="range" min="0" max="24" value="6" id="sliderB1Makeup" oninput="updateBandParam(1,'makeup',this.value)">
                  <div class="mini-slider-label">Makeup</div>
                </div>
              </div>
            </div>
            <!-- B2 -->
            <div class="band-card active" id="band2">
              <div class="band-card-title">B2</div>
              <div class="band-card-freq">120-400 Hz</div>
              <div class="band-card-controls">
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b2Thresh">-20</div>
                  <input type="range" min="-60" max="0" value="-20" id="sliderB2Thresh" oninput="updateBandParam(2,'threshold',this.value)">
                  <div class="mini-slider-label">Thresh</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b2Ratio">3:1</div>
                  <input type="range" min="1" max="20" value="3" id="sliderB2Ratio" oninput="updateBandParam(2,'ratio',this.value)">
                  <div class="mini-slider-label">Ratio</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b2Attack">8</div>
                  <input type="range" min="1" max="100" value="8" id="sliderB2Attack" oninput="updateBandParam(2,'attack',this.value)">
                  <div class="mini-slider-label">Atk</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b2Release">120</div>
                  <input type="range" min="10" max="1000" value="120" id="sliderB2Release" oninput="updateBandParam(2,'release',this.value)">
                  <div class="mini-slider-label">Rel</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b2Makeup">4</div>
                  <input type="range" min="0" max="24" value="4" id="sliderB2Makeup" oninput="updateBandParam(2,'makeup',this.value)">
                  <div class="mini-slider-label">Makeup</div>
                </div>
              </div>
            </div>
            <!-- B3 -->
            <div class="band-card active" id="band3">
              <div class="band-card-title">B3</div>
              <div class="band-card-freq">400-1.5k Hz</div>
              <div class="band-card-controls">
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b3Thresh">-18</div>
                  <input type="range" min="-60" max="0" value="-18" id="sliderB3Thresh" oninput="updateBandParam(3,'threshold',this.value)">
                  <div class="mini-slider-label">Thresh</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b3Ratio">3.5:1</div>
                  <input type="range" min="1" max="20" value="3.5" step="0.5" id="sliderB3Ratio" oninput="updateBandParam(3,'ratio',this.value)">
                  <div class="mini-slider-label">Ratio</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b3Attack">5</div>
                  <input type="range" min="1" max="100" value="5" id="sliderB3Attack" oninput="updateBandParam(3,'attack',this.value)">
                  <div class="mini-slider-label">Atk</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b3Release">150</div>
                  <input type="range" min="10" max="1000" value="150" id="sliderB3Release" oninput="updateBandParam(3,'release',this.value)">
                  <div class="mini-slider-label">Rel</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b3Makeup">5</div>
                  <input type="range" min="0" max="24" value="5" id="sliderB3Makeup" oninput="updateBandParam(3,'makeup',this.value)">
                  <div class="mini-slider-label">Makeup</div>
                </div>
              </div>
            </div>
            <!-- B4 -->
            <div class="band-card active" id="band4">
              <div class="band-card-title">B4</div>
              <div class="band-card-freq">1.5k-5k Hz</div>
              <div class="band-card-controls">
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b4Thresh">-16</div>
                  <input type="range" min="-60" max="0" value="-16" id="sliderB4Thresh" oninput="updateBandParam(4,'threshold',this.value)">
                  <div class="mini-slider-label">Thresh</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b4Ratio">3:1</div>
                  <input type="range" min="1" max="20" value="3" id="sliderB4Ratio" oninput="updateBandParam(4,'ratio',this.value)">
                  <div class="mini-slider-label">Ratio</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b4Attack">3</div>
                  <input type="range" min="1" max="100" value="3" id="sliderB4Attack" oninput="updateBandParam(4,'attack',this.value)">
                  <div class="mini-slider-label">Atk</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b4Release">180</div>
                  <input type="range" min="10" max="1000" value="180" id="sliderB4Release" oninput="updateBandParam(4,'release',this.value)">
                  <div class="mini-slider-label">Rel</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b4Makeup">4</div>
                  <input type="range" min="0" max="24" value="4" id="sliderB4Makeup" oninput="updateBandParam(4,'makeup',this.value)">
                  <div class="mini-slider-label">Makeup</div>
                </div>
              </div>
            </div>
            <!-- B5 -->
            <div class="band-card active" id="band5">
              <div class="band-card-title">B5</div>
              <div class="band-card-freq">5k-16k Hz</div>
              <div class="band-card-controls">
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b5Thresh">-14</div>
                  <input type="range" min="-60" max="0" value="-14" id="sliderB5Thresh" oninput="updateBandParam(5,'threshold',this.value)">
                  <div class="mini-slider-label">Thresh</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b5Ratio">2.5:1</div>
                  <input type="range" min="1" max="20" value="2.5" step="0.5" id="sliderB5Ratio" oninput="updateBandParam(5,'ratio',this.value)">
                  <div class="mini-slider-label">Ratio</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b5Attack">2</div>
                  <input type="range" min="1" max="100" value="2" id="sliderB5Attack" oninput="updateBandParam(5,'attack',this.value)">
                  <div class="mini-slider-label">Atk</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b5Release">200</div>
                  <input type="range" min="10" max="1000" value="200" id="sliderB5Release" oninput="updateBandParam(5,'release',this.value)">
                  <div class="mini-slider-label">Rel</div>
                </div>
                <div class="mini-slider">
                  <div class="mini-slider-value" id="b5Makeup">3</div>
                  <input type="range" min="0" max="24" value="3" id="sliderB5Makeup" oninput="updateBandParam(5,'makeup',this.value)">
                  <div class="mini-slider-label">Makeup</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- ROW 3: EQ + LIMITERS -->
      <div class="module-row">
        <!-- EQ MODULE -->
        <div class="module" id="modEQ">
          <div class="module-header">
            <div class="power-btn on" id="pwrEQ" onclick="togglePower('EQ')"></div>
            <span class="module-title">MULTIBAND EQ</span>
            <div class="module-status">
              <span class="status-led on" id="statusEQ"></span>
              <span style="font-size:8px;color:var(--text-dim)">6 BANDS</span>
            </div>
          </div>
          <div class="module-body" style="flex-direction:column;align-items:stretch;">
            <div class="eq-display">
              <canvas id="eqCanvas" class="eq-canvas"></canvas>
            </div>
            <div style="display:flex;gap:8px;justify-content:center;flex-wrap:wrap;margin-top:8px;">
              <div class="knob-control" onmousedown="startKnob(event,'eq1')">
                <div class="knob" id="knobEQ1"><div class="knob-indicator" id="knobEQ1Ind"></div></div>
                <div class="knob-value" id="valEQ1">0 dB</div>
                <div class="knob-label">32 Hz</div>
              </div>
              <div class="knob-control" onmousedown="startKnob(event,'eq2')">
                <div class="knob" id="knobEQ2"><div class="knob-indicator" id="knobEQ2Ind"></div></div>
                <div class="knob-value" id="valEQ2">0 dB</div>
                <div class="knob-label">100 Hz</div>
              </div>
              <div class="knob-control" onmousedown="startKnob(event,'eq3')">
                <div class="knob" id="knobEQ3"><div class="knob-indicator" id="knobEQ3Ind"></div></div>
                <div class="knob-value" id="valEQ3">0 dB</div>
                <div class="knob-label">400 Hz</div>
              </div>
              <div class="knob-control" onmousedown="startKnob(event,'eq4')">
                <div class="knob" id="knobEQ4"><div class="knob-indicator" id="knobEQ4Ind"></div></div>
                <div class="knob-value" id="valEQ4">0 dB</div>
                <div class="knob-label">1.5 kHz</div>
              </div>
              <div class="knob-control" onmousedown="startKnob(event,'eq5')">
                <div class="knob" id="knobEQ5"><div class="knob-indicator" id="knobEQ5Ind"></div></div>
                <div class="knob-value" id="valEQ5">0 dB</div>
                <div class="knob-label">5 kHz</div>
              </div>
              <div class="knob-control" onmousedown="startKnob(event,'eq6')">
                <div class="knob" id="knobEQ6"><div class="knob-indicator" id="knobEQ6Ind"></div></div>
                <div class="knob-value" id="valEQ6">0 dB</div>
                <div class="knob-label">12 kHz</div>
              </div>
            </div>
          </div>
        </div>

        <!-- LIMITERS MODULE -->
        <div class="module" id="modLimiter">
          <div class="module-header">
            <div class="power-btn on" id="pwrLimiter" onclick="togglePower('Limiter')"></div>
            <span class="module-title">LIMITERS</span>
            <div class="module-status">
              <span class="status-led on" id="statusLimiter"></span>
            </div>
          </div>
          <div class="module-body">
            <div class="limiter-row">
              <!-- Peak Limiter -->
              <div class="limiter-card">
                <div class="limiter-title">PEAK</div>
                <div class="limiter-meter">
                  <div class="limiter-meter-fill" id="peakMeterFill" style="height:0%"></div>
                </div>
                <div class="knob-control" onmousedown="startKnob(event,'peakThresh')">
                  <div class="knob" id="knobPeakThresh"><div class="knob-indicator" id="knobPeakThreshInd"></div></div>
                  <div class="knob-value" id="valPeakThresh">-0.3 dB</div>
                  <div class="knob-label">Threshold</div>
                </div>
                <div class="knob-control" onmousedown="startKnob(event,'peakAttack')">
                  <div class="knob" id="knobPeakAttack"><div class="knob-indicator" id="knobPeakAttackInd"></div></div>
                  <div class="knob-value" id="valPeakAttack">1 ms</div>
                  <div class="knob-label">Attack</div>
                </div>
                <div class="knob-control" onmousedown="startKnob(event,'peakRelease')">
                  <div class="knob" id="knobPeakRelease"><div class="knob-indicator" id="knobPeakReleaseInd"></div></div>
                  <div class="knob-value" id="valPeakRelease">50 ms</div>
                  <div class="knob-label">Release</div>
                </div>
                <div class="toggle-btn on" id="btnPeakOn" onclick="toggleLimiter('peak')">ON</div>
              </div>
              <!-- Bass Limiter -->
              <div class="limiter-card">
                <div class="limiter-title">BASS</div>
                <div class="limiter-meter">
                  <div class="limiter-meter-fill" id="bassMeterFill" style="height:0%"></div>
                </div>
                <div class="knob-control" onmousedown="startKnob(event,'bassThresh')">
                  <div class="knob" id="knobBassThresh"><div class="knob-indicator" id="knobBassThreshInd"></div></div>
                  <div class="knob-value" id="valBassThresh">-3 dB</div>
                  <div class="knob-label">Threshold</div>
                </div>
                <div class="knob-control" onmousedown="startKnob(event,'bassFreq')">
                  <div class="knob" id="knobBassFreq"><div class="knob-indicator" id="knobBassFreqInd"></div></div>
                  <div class="knob-value" id="valBassFreq">80 Hz</div>
                  <div class="knob-label">Crossover</div>
                </div>
                <div class="toggle-btn on" id="btnBassOn" onclick="toggleLimiter('bass')">ON</div>
              </div>
              <!-- Loudness Limiter -->
              <div class="limiter-card">
                <div class="limiter-title">LOUDNESS</div>
                <div class="limiter-meter">
                  <div class="limiter-meter-fill" id="loudMeterFill" style="height:0%"></div>
                </div>
                <div class="knob-control" onmousedown="startKnob(event,'loudTarget')">
                  <div class="knob" id="knobLoudTarget"><div class="knob-indicator" id="knobLoudTargetInd"></div></div>
                  <div class="knob-value" id="valLoudTarget">-16 LUFS</div>
                  <div class="knob-label">Target</div>
                </div>
                <div class="knob-control" onmousedown="startKnob(event,'loudCeiling')">
                  <div class="knob" id="knobLoudCeiling"><div class="knob-indicator" id="knobLoudCeilingInd"></div></div>
                  <div class="knob-value" id="valLoudCeiling">-1 dBTP</div>
                  <div class="knob-label">Ceiling</div>
                </div>
                <div class="toggle-btn on" id="btnLoudOn" onclick="toggleLimiter('loud')">ON</div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- ROW 4: OUTPUT -->
      <div class="module-row">
        <div class="module active-module" id="modOutput">
          <div class="module-header">
            <div class="power-btn on" id="pwrOutput" onclick="togglePower('Output')"></div>
            <span class="module-title">OUTPUT</span>
            <div class="module-status">
              <span class="status-led on" id="statusOutput"></span>
            </div>
          </div>
          <div class="module-body">
            <div class="knob-control" onmousedown="startKnob(event,'outputGain')">
              <div class="knob" id="knobOutputGain">
                <div class="knob-indicator" id="knobOutputGainInd"></div>
              </div>
              <div class="knob-value" id="valOutputGain">0.0 dB</div>
              <div class="knob-label">Output Gain</div>
            </div>
            <div class="knob-control" onmousedown="startKnob(event,'outputCeiling')">
              <div class="knob" id="knobOutputCeiling">
                <div class="knob-indicator" id="knobOutputCeilingInd"></div>
              </div>
              <div class="knob-value" id="valOutputCeiling">-0.1 dB</div>
              <div class="knob-label">Ceiling</div>
            </div>
            <div class="knob-control" onmousedown="startKnob(event,'outputStereo')">
              <div class="knob" id="knobOutputStereo">
                <div class="knob-indicator" id="knobOutputStereoInd"></div>
              </div>
              <div class="knob-value" id="valOutputStereo">100%</div>
              <div class="knob-label">Stereo</div>
            </div>
            <div class="knob-control" onmousedown="startKnob(event,'outputClipper')">
              <div class="knob" id="knobOutputClipper">
                <div class="knob-indicator" id="knobOutputClipperInd"></div>
              </div>
              <div class="knob-value" id="valOutputClipper">OFF</div>
              <div class="knob-label">Clipper</div>
            </div>
            <div style="display:flex;flex-direction:column;gap:4px;align-items:center;">
              <div class="toggle-btn on" id="btnOutDither" onclick="toggleDither()">DITHER</div>
              <div class="toggle-btn" id="btnOutClip" onclick="toggleOutputClip()">CLIP</div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- BOTTOM BAR -->
    <div class="bottom-bar">
      <div class="status-item">
        <div class="dot"></div>
        <span>AUDIO ENGINE ACTIVE</span>
      </div>
      <div class="status-item">
        <span>SAMPLE RATE: 48000 Hz</span>
      </div>
      <div class="status-item">
        <span>BUFFER: 256 samples</span>
      </div>
      <div class="status-item">
        <span>LATENCY: 5.3 ms</span>
      </div>
      <div class="status-item">
        <span>CPU: <span id="cpuUsage">12%</span></span>
      </div>
      <div class="spacer"></div>
      <div class="status-item">
        <span id="clockDisplay">00:00:00</span>
      </div>
    </div>
  </div>

  <div class="resize-handle" id="resizeRight" onmousedown="startResize(event,'right')"></div>

  <!-- RIGHT PANEL -->
  <div class="right-panel" id="rightPanel">
    <div class="branding-section">
      <div class="brand-logo">OmniaPro</div>
      <div class="brand-sub">Broadcast Processor</div>
    </div>
    <div class="info-section">
      <div class="info-group">
        <div class="info-group-title">System Status</div>
        <div class="info-row">
          <span class="info-key">Engine</span>
          <span class="info-val" id="infoEngine">Running</span>
        </div>
        <div class="info-row">
          <span class="info-key">Input</span>
          <span class="info-val" id="infoInput">Microphone</span>
        </div>
        <div class="info-row">
          <span class="info-key">Output</span>
          <span class="info-val" id="infoOutput">Default</span>
        </div>
        <div class="info-row">
          <span class="info-key">Sample Rate</span>
          <span class="info-val" id="infoSampleRate">48 kHz</span>
        </div>
        <div class="info-row">
          <span class="info-key">Latency</span>
          <span class="info-val" id="infoLatency">5.3 ms</span>
        </div>
      </div>
      <div class="info-group">
        <div class="info-group-title">Processing</div>
        <div class="info-row">
          <span class="info-key">AGC</span>
          <span class="info-val" id="infoAGC">ON</span>
        </div>
        <div class="info-row">
          <span class="info-key">Comp Bands</span>
          <span class="info-val">5 Active</span>
        </div>
        <div class="info-row">
          <span class="info-key">EQ Bands</span>
          <span class="info-val">6 Active</span>
        </div>
        <div class="info-row">
          <span class="info-key">Limiters</span>
          <span class="info-val">3 Active</span>
        </div>
      </div>
      <div class="info-group">
        <div class="info-group-title">Metering</div>
        <div class="info-row">
          <span class="info-key">Peak L</span>
          <span class="info-val" id="infoPeakL">-∞</span>
        </div>
        <div class="info-row">
          <span class="info-key">Peak R</span>
          <span class="info-val" id="infoPeakR">-∞</span>
        </div>
        <div class="info-row">
          <span class="info-key">RMS L</span>
          <span class="info-val" id="infoRMSL">-∞</span>
        </div>
        <div class="info-row">
          <span class="info-key">RMS R</span>
          <span class="info-val" id="infoRMSR">-∞</span>
        </div>
      </div>
      <div class="info-group">
        <div class="info-group-title">Current Preset</div>
        <div class="info-row">
          <span class="info-key">Name</span>
          <span class="info-val" id="infoPresetName">FM Loud</span>
        </div>
      </div>
    </div>
    <div class="output-meter">
      <div class="output-meter-label">Output Level</div>
      <div class="output-meter-bar">
        <div class="output-meter-fill" id="outputLevelFill" style="width:0%"></div>
      </div>
    </div>
  </div>
</div>

<!-- CONTEXT MENU -->
<div class="context-menu" id="contextMenu">
  <div class="context-menu-item" onclick="resetModule()">↻ Reset Module</div>
  <div class="context-menu-item" onclick="bypassModule()">⏸ Bypass Module</div>
  <div class="context-menu-sep"></div>
  <div class="context-menu-item" onclick="copySettings()">📋 Copy Settings</div>
  <div class="context-menu-item" onclick="pasteSettings()">📄 Paste Settings</div>
</div>

<!-- TOOLTIP -->
<div class="tooltip" id="tooltip"></div>

<!-- SETTINGS MODAL -->
<div class="modal-overlay" id="settingsModal">
  <div class="modal">
    <div class="modal-title">⚙ Settings</div>
    <div style="display:flex;flex-direction:column;gap:12px;">
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <span style="font-size:11px;color:var(--text-secondary);">Sample Rate</span>
        <select id="settingSampleRate">
          <option value="44100">44100 Hz</option>
          <option value="48000" selected>48000 Hz</option>
          <option value="96000">96000 Hz</option>
        </select>
      </div>
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <span style="font-size:11px;color:var(--text-secondary);">Buffer Size</span>
        <select id="settingBufferSize">
          <option value="128">128 samples</option>
          <option value="256" selected>256 samples</option>
          <option value="512">512 samples</option>
          <option value="1024">1024 samples</option>
        </select>
      </div>
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <span style="font-size:11px;color:var(--text-secondary);">Meter Decay</span>
        <select id="settingMeterDecay">
          <option value="fast">Fast</option>
          <option value="medium" selected>Medium</option>
          <option value="slow">Slow</option>
        </select>
      </div>
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <span style="font-size:11px;color:var(--text-secondary);">Peak Hold</span>
        <select id="settingPeakHold">
          <option value="off">Off</option>
          <option value="short">Short</option>
          <option value="medium" selected>Medium</option>
          <option value="long">Long</option>
        </select>
      </div>
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <span style="font-size:11px;color:var(--text-secondary);">Theme</span>
        <select id="settingTheme">
          <option value="dark" selected>Dark</option>
          <option value="midnight">Midnight</option>
        </select>
      </div>
    </div>
    <div class="modal-actions">
      <button class="modal-btn" onclick="closeSettings()">Cancel</button>
      <button class="modal-btn primary" onclick="applySettings()">Apply</button>
    </div>
  </div>
</div>

<script>
// ============================================
// OmniaPro Broadcast Processor - Core Engine
// ============================================

// ===== KNOB STATE =====
const knobState = {
  inputGain: 0, inputTrim: 0, inputPhase: 0,
  agcTarget: 50, agcSpeed: 50, agcRange: 60, agcAttack: 50, agcRelease: 40,
  eq1: 50, eq2: 50, eq3: 50, eq4: 50, eq5: 50, eq6: 50,
  peakThresh: 90, peakAttack: 20, peakRelease: 50,
  bassThresh: 80, bassFreq: 40,
  loudTarget: 60, loudCeiling: 85,
  outputGain: 50, outputCeiling: 95, outputStereo: 100, outputClipper: 0
};

// ===== MODULE STATES =====
const modules = {
  Input: true, AGC: true, Comp: true, EQ: true, Limiter: true, Output: true
};

// ===== AUDIO ENGINE =====
let audioCtx = null;
let inputSource = null;
let inputGainNode = null;
let agcGainNode = null;
let outputGainNode = null;
let masterAnalyser = null;
let inputAnalyser = null;
let agcAnalyser = null;
let compAnalyser = null;
let eqAnalyser = null;
let limiterAnalyser = null;
let outputAnalyser = null;
let eqFilters = [];
let compBands = [];
let compFilters = [];
let peakLimiter = null;
let bassLimiter = null;
let loudnessLimiter = null;
let isAudioStarted = false;
let currentInputType = 'mic';
let oscillatorNode = null;
let noiseNode = null;
let noiseGain = null;

// Meter values
const meterValues = {
  inputL: 0, inputR: 0,
  agcL: 0, agcR: 0,
  compL: 0, compR: 0,
  eqL: 0, eqR: 0,
  limL: 0, limR: 0,
  outL: 0, outR: 0
};

// Presets
const presets = {
  'fm-loud': {
    name: 'FM Loud',
    inputGain: 0, agcTarget: 50, agcSpeed: 50,
    eq: [2, 0, -1, 1, 2, 0],
    comp: [
      {thresh:-24,ratio:4,atk:10,rel:100,makeup:6},
      {thresh:-20,ratio:3,atk:8,rel:120,makeup:4},
      {thresh:-18,ratio:3.5,atk:5,rel:150,makeup:5},
      {thresh:-16,ratio:3,atk:3,rel:180,makeup:4},
      {thresh:-14,ratio:2.5,atk:2,rel:200,makeup:3}
    ],
    peakThresh: -0.3, bassThresh: -3, loudTarget: -16,
    outputGain: 0
  },
  'fm-clean': {
    name: 'FM Clean',
    inputGain: 0, agcTarget: 60, agcSpeed: 40,
    eq: [0, 0, 0, 0, 0, 0],
    comp: [
      {thresh:-30,ratio:2,atk:15,rel:150,makeup:3},
      {thresh:-25,ratio:2,atk:12,rel:180,makeup:2},
      {thresh:-22,ratio:2,atk:10,rel:200,makeup:2},
      {thresh:-20,ratio:1.5,atk:8,rel:220,makeup:1},
      {thresh:-18,ratio:1.5,atk:5,rel:250,makeup:1}
    ],
    peakThresh: -0.5, bassThresh: -6, loudTarget: -18,
    outputGain: 0
  },
  'fm-hot': {
    name: 'FM Hot',
    inputGain: 3, agcTarget: 30, agcSpeed: 70,
    eq: [3, 1, -2, 2, 4, 1],
    comp: [
      {thresh:-18,ratio:6,atk:5,rel:80,makeup:10},
      {thresh:-15,ratio:5,atk:4,rel:100,makeup:8},
      {thresh:-12,ratio:5,atk:3,rel:120,makeup:8},
      {thresh:-10,ratio:4,atk:2,rel:140,makeup:6},
      {thresh:-8,ratio:3,atk:1,rel:160,makeup:5}
    ],
    peakThresh: -0.1, bassThresh: -1, loudTarget: -12,
    outputGain: 2
  },
  'fm-classic': {
    name: 'FM Classic',
    inputGain: 0, agcTarget: 55, agcSpeed: 45,
    eq: [1, 0, 0, 0, -1, -2],
    comp: [
      {thresh:-28,ratio:3,atk:12,rel:130,makeup:4},
      {thresh:-24,ratio:2.5,atk:10,rel:150,makeup:3},
      {thresh:-20,ratio:2.5,atk:8,rel:180,makeup:3},
      {thresh:-18,ratio:2,atk:6,rel:200,makeup:2},
      {thresh:-16,ratio:1.5,atk:5,rel:220,makeup:1}
    ],
    peakThresh: -0.3, bassThresh: -4, loudTarget: -17,
    outputGain: 0
  },
  'fm-news': {
    name: 'FM News/Talk',
    inputGain: 2, agcTarget: 45, agcSpeed: 60,
    eq: [-1, 1, 3, 2, 1, -1],
    comp: [
      {thresh:-26,ratio:3,atk:8,rel:120,makeup:5},
      {thresh:-22,ratio:3,atk:6,rel:140,makeup:4},
      {thresh:-18,ratio:4,atk:4,rel:160,makeup:6},
      {thresh:-15,ratio:3.5,atk:3,rel:180,makeup:4},
      {thresh:-12,ratio:2,atk:2,rel:200,makeup:2}
    ],
    peakThresh: -0.3, bassThresh: -5, loudTarget: -14,
    outputGain: 1
  },
  'stream-clean': {
    name: 'Streaming Clean',
    inputGain: 0, agcTarget: 55, agcSpeed: 35,
    eq: [0, 0, 0, 0, 0, 0],
    comp: [
      {thresh:-32,ratio:2,atk:20,rel:200,makeup:2},
      {thresh:-28,ratio:1.5,atk:15,rel:220,makeup:1},
      {thresh:-24,ratio:1.5,atk:12,rel:250,makeup:1},
      {thresh:-20,ratio:1.5,atk:10,rel:280,makeup:1},
      {thresh:-16,ratio:1.2,atk:8,rel:300,makeup:0.5}
    ],
    peakThresh: -1.0, bassThresh: -8, loudTarget: -16,
    outputGain: -1
  },
  'stream-loud': {
    name: 'Streaming Loud',
    inputGain: 2, agcTarget: 35, agcSpeed: 55,
    eq: [1, 0, 0, 0, 1, 0],
    comp: [
      {thresh:-22,ratio:4,atk:5,rel:100,makeup:8},
      {thresh:-18,ratio:3.5,atk:4,rel:120,makeup:6},
      {thresh:-15,ratio:3.5,atk:3,rel:140,makeup:6},
      {thresh:-12,ratio:3,atk:2,rel:160,makeup:5},
      {thresh:-10,ratio:2.5,atk:1,rel:180,makeup:4}
    ],
    peakThresh: -0.3, bassThresh: -2, loudTarget: -14,
    outputGain: 1
  },
  'stream-podcast': {
    name: 'Podcast',
    inputGain: 3, agcTarget: 40, agcSpeed: 50,
    eq: [-2, 1, 3, 2, 0, -2],
    comp: [
      {thresh:-24,ratio:4,atk:8,rel:120,makeup:6},
      {thresh:-20,ratio:3,atk:6,rel:140,makeup:4},
      {thresh:-16,ratio:4,atk:4,rel:160,makeup:6},
      {thresh:-14,ratio:3,atk:3,rel:180,makeup:4},
      {thresh:-12,ratio:2,atk:2,rel:200,makeup:2}
    ],
    peakThresh: -0.5, bassThresh: -6, loudTarget: -16,
    outputGain: 0
  },
  'bass-boost': {
    name: 'Bass Boost',
    inputGain: 0, agcTarget: 50, agcSpeed: 50,
    eq: [6, 4, 2, 0, -1, 0],
    comp: [
      {thresh:-20,ratio:5,atk:8,rel:100,makeup:8},
      {thresh:-18,ratio:4,atk:6,rel:120,makeup:6},
      {thresh:-16,ratio:3,atk:5,rel:150,makeup:4},
      {thresh:-14,ratio:2.5,atk:3,rel:180,makeup:3},
      {thresh:-12,ratio:2,atk:2,rel:200,makeup:2}
    ],
    peakThresh: -0.3, bassThresh: -2, loudTarget: -14,
    outputGain: 0
  },
  'bright': {
    name: 'Bright & Airy',
    inputGain: 0, agcTarget: 50, agcSpeed: 50,
    eq: [-2, -1, 0, 1, 3, 5],
    comp: [
      {thresh:-28,ratio:2.5,atk:12,rel:150,makeup:3},
      {thresh:-24,ratio:2.5,atk:10,rel:170,makeup:3},
      {thresh:-20,ratio:2.5,atk:8,rel:190,makeup:3},
      {thresh:-18,ratio:2.5,atk:6,rel:210,makeup:3},
      {thresh:-16,ratio:2,atk:4,rel:230,makeup:2}
    ],
    peakThresh: -0.5, bassThresh: -6, loudTarget: -17,
    outputGain: 0
  },
  'warm': {
    name: 'Warm Analog',
    inputGain: -1, agcTarget: 55, agcSpeed: 45,
    eq: [2, 1, 0, -1, -2, -3],
    comp: [
      {thresh:-26,ratio:2,atk:15,rel:180,makeup:3},
      {thresh:-22,ratio:2,atk:12,rel:200,makeup:2},
      {thresh:-20,ratio:1.8,atk:10,rel:220,makeup:2},
      {thresh:-18,ratio:1.5,atk:8,rel:240,makeup:1},
      {thresh:-16,ratio:1.2,atk:6,rel:260,makeup:0.5}
    ],
    peakThresh: -0.5, bassThresh: -5, loudTarget: -18,
    outputGain: -1
  },
  'rock': {
    name: 'Rock/Pop',
    inputGain: 1, agcTarget: 45, agcSpeed: 55,
    eq: [3, 2, 1, 2, 3, 2],
    comp: [
      {thresh:-22,ratio:4,atk:6,rel:100,makeup:7},
      {thresh:-18,ratio:3.5,atk:5,rel:120,makeup:5},
      {thresh:-15,ratio:3.5,atk:4,rel:140,makeup:5},
      {thresh:-12,ratio:3,atk:3,rel:160,makeup:4},
      {thresh:-10,ratio:2.5,atk:2,rel:180,makeup:3}
    ],
    peakThresh: -0.2, bassThresh: -2, loudTarget: -13,
    outputGain: 1
  },
  'custom1': { name: 'User Preset 1', inputGain:0, agcTarget:50, agcSpeed:50, eq:[0,0,0,0,0,0], comp:[{thresh:-24,ratio:4,atk:10,rel:100,makeup:6},{thresh:-20,ratio:3,atk:8,rel:120,makeup:4},{thresh:-18,ratio:3.5,atk:5,rel:150,makeup:5},{thresh:-16,ratio:3,atk:3,rel:180,makeup:4},{thresh:-14,ratio:2.5,atk:2,rel:200,makeup:3}], peakThresh:-0.3, bassThresh:-3, loudTarget:-16, outputGain:0 },
  'custom2': { name: 'User Preset 2', inputGain:0, agcTarget:50, agcSpeed:50, eq:[0,0,0,0,0,0], comp:[{thresh:-24,ratio:4,atk:10,rel:100,makeup:6},{thresh:-20,ratio:3,atk:8,rel:120,makeup:4},{thresh:-18,ratio:3.5,atk:5,rel:150,makeup:5},{thresh:-16,ratio:3,atk:3,rel:180,makeup:4},{thresh:-14,ratio:2.5,atk:2,rel:200,makeup:3}], peakThresh:-0.3, bassThresh:-3, loudTarget:-16, outputGain:0 }
};

// ===== INIT AUDIO ENGINE =====
async function initAudio() {
  if (isAudioStarted) return;
  
  try {
    audioCtx = new (window.AudioContext || window.webkitAudioContext)({ sampleRate: 48000 });
    
    // Create gain nodes
    inputGainNode = audioCtx.createGain();
    agcGainNode = audioCtx.createGain();
    outputGainNode = audioCtx.createGain();
    
    // Create analyzers
    inputAnalyser = audioCtx.createAnalyser();
    inputAnalyser.fftSize = 2048;
    agcAnalyser = audioCtx.createAnalyser();
    agcAnalyser.fftSize = 2048;
    compAnalyser = audioCtx.createAnalyser();
    compAnalyser.fftSize = 2048;
    eqAnalyser = audioCtx.createAnalyser();
    eqAnalyser.fftSize = 2048;
    limiterAnalyser = audioCtx.createAnalyser();
    limiterAnalyser.fftSize = 2048;
    outputAnalyser = audioCtx.createAnalyser();
    outputAnalyser.fftSize = 2048;
    masterAnalyser = audioCtx.createAnalyser();
    masterAnalyser.fftSize = 4096;
    
    // Create EQ filters (6 bands)
    const eqFreqs = [32, 100, 400, 1500, 5000, 12000];
    eqFilters = eqFreqs.map((freq, i) => {
      const filter = audioCtx.createBiquadFilter();
      filter.type = i === 0 ? 'lowshelf' : (i === eqFreqs.length - 1 ? 'highshelf' : 'peaking');
      filter.frequency.value = freq;
      filter.gain.value = 0;
      filter.Q.value = 1.4;
      return filter;
    });
    
    // Create compressor bands (5 bands with crossover filters)
    const compFreqs = [120, 400, 1500, 5000];
    compBands = [];
    compFilters = [];
    
    for (let i = 0; i < 5; i++) {
      const compressor = audioCtx.createDynamicsCompressor();
      compressor.threshold.value = presets['fm-loud'].comp[i].thresh;
      compressor.ratio.value = presets['fm-loud'].comp[i].ratio;
      compressor.attack.value = presets['fm-loud'].comp[i].atk / 1000;
      compressor.release.value = presets['fm-loud'].comp[i].rel / 1000;
      compressor.knee.value = 6;
      compBands.push(compressor);
    }
    
    // Crossover filters for multiband compressor
    compFilters = [];
    for (let i = 0; i < 4; i++) {
      // Low pass for band below
      const lp = audioCtx.createBiquadFilter();
      lp.type = 'lowpass';
      lp.frequency.value = compFreqs[i];
      lp.Q.value = 0.7;
      
      // High pass for band above
      const hp = audioCtx.createBiquadFilter();
      hp.type = 'highpass';
      hp.frequency.value = compFreqs[i];
      hp.Q.value = 0.7;
      
      compFilters.push({ lp, hp });
    }
    
    // Limiters
    peakLimiter = audioCtx.createDynamicsCompressor();
    peakLimiter.threshold.value = -0.3;
    peakLimiter.ratio.value = 20;
    peakLimiter.attack.value = 0.001;
    peakLimiter.release.value = 0.05;
    peakLimiter.knee.value = 0;
    
    bassLimiter = audioCtx.createDynamicsCompressor();
    bassLimiter.threshold.value = -3;
    bassLimiter.ratio.value = 20;
    bassLimiter.attack.value = 0.005;
    bassLimiter.release.value = 0.1;
    bassLimiter.knee.value = 0;
    
    loudnessLimiter = audioCtx.createDynamicsCompressor();
    loudnessLimiter.threshold.value = -16;
    loudnessLimiter.ratio.value = 20;
    loudnessLimiter.attack.value = 0.01;
    loudnessLimiter.release.value = 0.1;
    loudnessLimiter.knee.value = 0;
    
    // Connect input source
    await setupInputSource('mic');
    
    isAudioStarted = true;
    document.getElementById('infoEngine').textContent = 'Running';
    document.getElementById('infoSampleRate').textContent = (audioCtx.sampleRate / 1000) + ' kHz';
    
    startMetering();
    startAnimationLoop();
    updateClock();
    
  } catch (e) {
    console.error('Audio init error:', e);
    alert('Error initializing audio: ' + e.message);
  }
}

async function setupInputSource(type) {
  if (inputSource) {
    try { inputSource.disconnect(); } catch(e) {}
  }
  if (oscillatorNode) {
    try { oscillatorNode.stop(); } catch(e) {}
    oscillatorNode = null;
  }
  if (noiseNode) {
    try { noiseNode.stop(); } catch(e) {}
    noiseNode = null;
  }
  
  currentInputType = type;
  
  switch(type) {
    case 'mic':
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        inputSource = audioCtx.createMediaStreamSource(stream);
        document.getElementById('infoInput').textContent = 'Microphone';
      } catch(e) {
        console.warn('Mic access denied, using test signal');
        createTestSignal();
      }
      break;
    case 'sine':
      createTestSignal();
      document.getElementById('infoInput').textContent = 'Sine Test';
      break;
    case 'noise':
      createNoiseSignal();
      document.getElementById('infoInput').textContent = 'Noise Test';
      break;
    case 'file':
      document.getElementById('infoInput').textContent = 'File (select a file)';
      // File input would require a file dialog
      break;
  }
  
  // Build audio chain
  buildAudioChain();
}

function createTestSignal() {
  oscillatorNode = audioCtx.createOscillator();
  oscillatorNode.type = 'sine';
  oscillatorNode.frequency.value = 1000;
  const oscGain = audioCtx.createGain();
  oscGain.gain.value = 0.3;
  oscillatorNode.connect(oscGain);
  inputSource = oscGain;
  oscillatorNode.start();
}

function createNoiseSignal() {
  const bufferSize = audioCtx.sampleRate * 2;
  const buffer = audioCtx.createBuffer(2, bufferSize, audioCtx.sampleRate);
  for (let ch = 0; ch < 2; ch++) {
    const data = buffer.getChannelData(ch);
    for (let i = 0; i < bufferSize; i++) {
      data[i] = (Math.random() * 2 - 1) * 0.15;
    }
  }
  noiseNode = audioCtx.createBufferSource();
  noiseNode.buffer = buffer;
  noiseNode.loop = true;
  noiseGain = audioCtx.createGain();
  noiseGain.gain.value = 0.3;
  noiseNode.connect(noiseGain);
  inputSource = noiseGain;
  noiseNode.start();
}

function buildAudioChain() {
  try {
    // Disconnect all first
    inputSource.disconnect();
    
    // Chain: Input -> InputGain -> AGC -> Compressor -> EQ -> Limiters -> OutputGain -> Analyser -> Destination
    const chainStart = inputSource;
    
    // Input gain
    chainStart.connect(inputGainNode);
    inputGainNode.connect(inputAnalyser);
    
    // AGC
    inputAnalyser.connect(agcGainNode);
    agcGainNode.connect(agcAnalyser);
    
    // Multiband compressor (parallel bands)
    const compInput = audioCtx.createGain();
    agcAnalyser.connect(compInput);
    
    // Connect each band
    const compOutputs = [];
    for (let i = 0; i < 5; i++) {
      const bandInput = audioCtx.createGain();
      const bandOutput = audioCtx.createGain();
      
      if (i === 0) {
        // B1: Low pass
        const lp = compFilters[0].lp;
        compInput.connect(lp);
        lp.connect(compBands[i]);
        compBands[i].connect(bandOutput);
      } else if (i === 4) {
        // B5: High pass
        const hp = compFilters[3].hp;
        compInput.connect(hp);
        hp.connect(compBands[i]);
        compBands[i].connect(bandOutput);
      } else {
        // Middle bands: bandpass
        const lp = compFilters[i].lp;
        const hp = compFilters[i-1].hp;
        compInput.connect(hp);
        hp.connect(lp);
        lp.connect(compBands[i]);
        compBands[i].connect(bandOutput);
      }
      
      bandOutput.connect(compAnalyser);
      compOutputs.push(bandOutput);
    }
    
    // Sum compressor outputs
    const compSum = audioCtx.createGain();
    compOutputs.forEach(o => {
      try { o.disconnect(); } catch(e) {}
      o.connect(compSum);
    });
    
    // EQ
    let eqChain = compSum;
    eqChain.connect(eqAnalyser);
    
    eqFilters.forEach((filter, i) => {
      if (i === 0) {
        eqAnalyser.connect(filter);
      } else {
        eqFilters[i-1].connect(filter);
      }
    });
    
    const lastEQ = eqFilters[eqFilters.length - 1];
    
    // Limiters chain
    lastEQ.connect(limiterAnalyser);
    limiterAnalyser.connect(peakLimiter);
    peakLimiter.connect(bassLimiter);
    bassLimiter.connect(loudnessLimiter);
    loudnessLimiter.connect(outputAnalyser);
    
    // Output gain
    outputAnalyser.connect(outputGainNode);
    outputGainNode.connect(masterAnalyser);
    masterAnalyser.connect(audioCtx.destination);
    
    document.getElementById('infoEngine').textContent = 'Running';
  } catch(e) {
    console.error('Chain build error:', e);
  }
}

// ===== METERING =====
function startMetering() {
  setInterval(updateMeters, 50);
}

function updateMeters() {
  if (!isAudioStarted || !inputAnalyser) return;
  
  // Get time domain data for level calculation
  const inputTime = new Float32Array(inputAnalyser.fftSize);
  inputAnalyser.getFloatTimeDomainData(inputTime);
  
  const agcTime = new Float32Array(agcAnalyser.fftSize);
  agcAnalyser.getFloatTimeDomainData(agcTime);
  
  const compTime = new Float32Array(compAnalyser.fftSize);
  compAnalyser.getFloatTimeDomainData(compTime);
  
  const eqTime = new Float32Array(eqAnalyser.fftSize);
  eqAnalyser.getFloatTimeDomainData(eqTime);
  
  const limTime = new Float32Array(limiterAnalyser.fftSize);
  limiterAnalyser.getFloatTimeDomainData(limTime);
  
  const outTime = new Float32Array(outputAnalyser.fftSize);
  outputAnalyser.getFloatTimeDomainData(outTime);
  
  // Calculate RMS for each stage
  const levels = {
    input: calcRMS(inputTime),
    agc: calcRMS(agcTime),
    comp: calcRMS(compTime),
    eq: calcRMS(eqTime),
    lim: calcRMS(limTime),
    out: calcRMS(outTime)
  };
  
  // Convert to dB
  const dbLevels = {};
  for (const [key, val] of Object.entries(levels)) {
    dbLevels[key] = val > 0 ? 20 * Math.log10(val) : -Infinity;
  }
  
  // Update meter displays
  updateMeterPair('Input', dbLevels.input, dbLevels.input);
  updateMeterPair('AGC', dbLevels.agc, dbLevels.agc);
  updateMeterPair('Comp', dbLevels.comp, dbLevels.comp);
  updateMeterPair('EQ', dbLevels.eq, dbLevels.eq);
  updateMeterPair('Lim', dbLevels.lim, dbLevels.lim);
  updateMeterPair('Out', dbLevels.out, dbLevels.out);
  
  // Update info panel
  document.getElementById('infoPeakL').textContent = dbLevels.out > -100 ? dbLevels.out.toFixed(1) + ' dB' : '-∞';
  document.getElementById('infoPeakR').textContent = dbLevels.out > -100 ? dbLevels.out.toFixed(1) + ' dB' : '-∞';
  document.getElementById('infoRMSL').textContent = dbLevels.out > -100 ? dbLevels.out.toFixed(1) + ' dB' : '-∞';
  document.getElementById('infoRMSR').textContent = dbLevels.out > -100 ? dbLevels.out.toFixed(1) + ' dB' : '-∞';
  
  // Output level bar
  const outPct = Math.max(0, Math.min(100, (dbLevels.out + 60) / 60 * 100));
  document.getElementById('outputLevelFill').style.width = outPct + '%';
  
  // CPU simulation
  const cpu = 8 + Math.random() * 15;
  document.getElementById('cpuUsage').textContent = cpu.toFixed(0) + '%';
}

function calcRMS(data) {
  let sum = 0;
  for (let i = 0; i < data.length; i++) {
    sum += data[i] * data[i];
  }
  return Math.sqrt(sum / data.length);
}

function updateMeterPair(name, lDb, rDb) {
  const lPct = dbToPercent(lDb);
  const rPct = dbToPercent(rDb);
  
  // L meter
  const lFill = document.getElementById('meter' + name + 'LFill');
  if (lFill) {
    lFill.style.height = lPct + '%';
    lFill.style.background = getMeterGradient(lPct);
  }
  
  // R meter
  const rFill = document.getElementById('meter' + name + 'RFill');
  if (rFill) {
    rFill.style.height = rPct + '%';
    rFill.style.background = getMeterGradient(rPct);
  }
  
  // Value display
  const lVal = document.getElementById('meter' + name + 'LVal');
  if (lVal) lVal.innerHTML = lDb > -100 ? '<span class="meter-db-value">' + lDb.toFixed(1) + ' dB</span>' : '-∞ dB';
  
  const rVal = document.getElementById('meter' + name + 'RVal');
  if (rVal) rVal.innerHTML = rDb > -100 ? '<span class="meter-db-value">' + rDb.toFixed(1) + ' dB</span>' : '-∞ dB';
}

function dbToPercent(db) {
  if (db <= -60) return 0;
  if (db >= 0) return 100;
  return ((db + 60) / 60) * 100;
}

function getMeterGradient(pct) {
  if (pct < 60) return '#00cc44';
  if (pct < 80) return '#88cc00';
  if (pct < 90) return '#ffcc00';
  if (pct < 95) return '#ff8800';
  return '#ff3344';
}

// ===== SPECTRUM & PHASE DISPLAY =====
function startAnimationLoop() {
  function animate() {
    drawSpectrum();
    drawPhase();
    drawEQ();
    requestAnimationFrame(animate);
  }
  animate();
}

function drawSpectrum() {
  const canvas = document.getElementById('spectrumCanvas');
  if (!canvas || !masterAnalyser) return;
  
  const ctx = canvas.getContext('2d');
  const width = canvas.parentElement.clientWidth;
  const height = canvas.parentElement.clientHeight;
  
  if (canvas.width !== width) canvas.width = width;
  if (canvas.height !== height) canvas.height = height;
  
  const bufferLength = masterAnalyser.frequencyBinCount;
  const dataArray = new Uint8Array(bufferLength);
  masterAnalyser.getByteFrequencyData(dataArray);
  
  ctx.fillStyle = '#0a0a10';
  ctx.fillRect(0, 0, width, height);
  
  const barWidth = (width / bufferLength) * 2.5;
  let x = 0;
  
  for (let i = 0; i < bufferLength; i++) {
    const barHeight = (dataArray[i] / 255) * height;
    
    const hue = 120 - (dataArray[i] / 255) * 120;
    const saturation = 100;
    const lightness = 40 + (dataArray[i] / 255) * 20;
    
    ctx.fillStyle = `hsl(${hue}, ${saturation}%, ${lightness}%)`;
    ctx.fillRect(x, height - barHeight, barWidth - 1, barHeight);
    
    x += barWidth;
    if (x > width) break;
  }
  
  // Grid lines
  ctx.strokeStyle = 'rgba(255,255,255,0.05)';
  ctx.lineWidth = 1;
  for (let y = 0; y < height; y += 20) {
    ctx.beginPath();
    ctx.moveTo(0, y);
    ctx.lineTo(width, y);
    ctx.stroke();
  }
}

function drawPhase() {
  const canvas = document.getElementById('phaseCanvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  const w = canvas.width;
  const h = canvas.height;
  
  ctx.fillStyle = '#0a0a10';
  ctx.fillRect(0, 0, w, h);
  
  // Lissajous pattern
  if (inputAnalyser && isAudioStarted) {
    const timeL = new Float32Array(inputAnalyser.fftSize);
    const timeR = new Float32Array(inputAnalyser.fftSize);
    inputAnalyser.getFloatTimeDomainData(timeL);
    
    // Simulate stereo
    for (let i = 0; i < timeR.length; i++) {
      timeR[i] = timeL[i] * (0.8 + Math.sin(i * 0.01) * 0.2);
    }
    
    ctx.strokeStyle = '#00aaff';
    ctx.lineWidth = 1;
    ctx.beginPath();
    
    const step = Math.floor(timeL.length / 200);
    for (let i = 0; i < timeL.length; i += step) {
      const x = ((timeL[i] + 1) / 2) * w;
      const y = ((timeR[i] + 1) / 2) * h;
      if (i === 0) ctx.moveTo(x, y);
      else ctx.lineTo(x, y);
    }
    ctx.stroke();
  }
  
  // Cross lines
  ctx.strokeStyle = 'rgba(255,255,255,0.1)';
  ctx.lineWidth = 1;
  ctx.beginPath();
  ctx.moveTo(w/2, 0); ctx.lineTo(w/2, h);
  ctx.moveTo(0, h/2); ctx.lineTo(w, h/2);
  ctx.stroke();
}

function drawEQ() {
  const canvas = document.getElementById('eqCanvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  const w = canvas.width;
  const h = canvas.height;
  
  if (canvas.width !== canvas.parentElement.clientWidth) {
    canvas.width = canvas.parentElement.clientWidth;
    canvas.height = canvas.parentElement.clientHeight;
  }
  
  ctx.fillStyle = '#0a0a10';
  ctx.fillRect(0, 0, w, h);
  
  // Draw EQ curve
  const eqValues = [
    knobState.eq1, knobState.eq2, knobState.eq3,
    knobState.eq4, knobState.eq5, knobState.eq6
  ];
  
  // Grid
  ctx.strokeStyle = 'rgba(255,255,255,0.05)';
  ctx.lineWidth = 1;
  for (let y = 0; y < h; y += h/6) {
    ctx.beginPath();
    ctx.moveTo(0, y);
    ctx.lineTo(w, y);
    ctx.stroke();
  }
  for (let x = 0; x < w; x += w/6) {
    ctx.beginPath();
    ctx.moveTo(x, 0);
    ctx.lineTo(x, h);
    ctx.stroke();
  }
  
  // Center line
  ctx.strokeStyle = 'rgba(255,255,255,0.15)';
  ctx.beginPath();
  ctx.moveTo(0, h/2);
  ctx.lineTo(w, h/2);
  ctx.stroke();
  
  // EQ curve
  ctx.strokeStyle = '#00aaff';
  ctx.lineWidth = 2;
  ctx.shadowColor = '#00aaff';
  ctx.shadowBlur = 4;
  ctx.beginPath();
  
  const freqs = [32, 100, 400, 1500, 5000, 12000];
  
  for (let x = 0; x < w; x++) {
    const freq = 20 * Math.pow(1000, x / w); // 20 Hz to 20 kHz
    let gain = 0;
    
    for (let i = 0; i < freqs.length; i++) {
      const dbVal = (eqValues[i] - 50) / 50 * 12; // ±12 dB
      const distance = Math.abs(Math.log2(freq / freqs[i]));
      const bandwidth = 2;
      const contribution = dbVal * Math.exp(-distance * distance / (2 * bandwidth * bandwidth));
      gain += contribution;
    }
    
    const y = h/2 - (gain / 12) * (h/2);
    if (x === 0) ctx.moveTo(x, y);
    else ctx.lineTo(x, y);
  }
  ctx.stroke();
  ctx.shadowBlur = 0;
  
  // Fill under curve
  ctx.lineTo(w, h);
  ctx.lineTo(0, h);
  ctx.closePath();
  const gradient = ctx.createLinearGradient(0, 0, 0, h);
  gradient.addColorStop(0, 'rgba(0,170,255,0.1)');
  gradient.addColorStop(0.5, 'rgba(0,170,255,0.02)');
  gradient.addColorStop(1, 'rgba(0,170,255,0.05)');
  ctx.fillStyle = gradient;
  ctx.fill();
}

// ===== KNOB INTERACTION =====
let activeKnob = null;
let knobStartY = 0;
let knobStartValue = 0;

function startKnob(event, knobId) {
  event.preventDefault();
  activeKnob = knobId;
  knobStartY = event.clientY;
  knobStartValue = knobState[knobId];
  
  document.addEventListener('mousemove', onKnobMove);
  document.addEventListener('mouseup', onKnobUp);
}

function onKnobMove(event) {
  if (!activeKnob) return;
  
  const delta = (knobStartY - event.clientY) * 0.5;
  let newValue = knobStartValue + delta;
  newValue = Math.max(0, Math.min(100, newValue));
  knobState[activeKnob] = newValue;
  
  updateKnobDisplay(activeKnob);
  applyKnobValue(activeKnob, newValue);
}

function onKnobUp() {
  activeKnob = null;
  document.removeEventListener('mousemove', onKnobMove);
  document.removeEventListener('mouseup', onKnobUp);
}

function updateKnobDisplay(knobId) {
  const value = knobState[knobId];
  const angle = (value / 100) * 270 - 135; // -135 to +135 degrees
  
  const indicator = document.getElementById('knob' + capitalize(knobId) + 'Ind');
  if (indicator) {
    indicator.style.transform = `rotate(${angle}deg)`;
    indicator.style.transformOrigin = 'center 20px';
  }
  
  // Update value display
  const valDisplay = document.getElementById('val' + capitalize(knobId));
  if (valDisplay) {
    valDisplay.textContent = getKnobDisplayText(knobId, value);
  }
}

function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

function getKnobDisplayText(knobId, value) {
  switch(knobId) {
    case 'inputGain': return (value - 50).toFixed(1) + ' dB';
    case 'inputTrim': return (value - 50).toFixed(1) + ' dB';
    case 'inputPhase': return Math.round(value * 3.6) + '°';
    case 'agcTarget': return Math.round((value - 50) * 0.6 - 20) + ' dB';
    case 'agcSpeed': return ['Slow','Med-S','Med','Med-F','Fast'][Math.round(value/25)] || 'Med';
    case 'agcRange': return Math.round(value * 0.6) + ' dB';
    case 'agcAttack': return Math.round(value * 2) + ' ms';
    case 'agcRelease': return Math.round(value * 5) + ' ms';
    case 'eq1': case 'eq2': case 'eq3': case 'eq4': case 'eq5': case 'eq6':
      return Math.round((value - 50) / 50 * 12) + ' dB';
    case 'peakThresh': return ((value - 100) * 0.1).toFixed(1) + ' dB';
    case 'peakAttack': return Math.max(0.1, Math.round(value * 0.05)) + ' ms';
    case 'peakRelease': return Math.max(10, Math.round(value * 2)) + ' ms';
    case 'bassThresh': return ((value - 100) * 0.1).toFixed(0) + ' dB';
    case 'bassFreq': return Math.round(value * 2) + ' Hz';
    case 'loudTarget': return Math.round((value - 100) * 0.2) + ' LUFS';
    case 'loudCeiling': return ((value - 100) * 0.05).toFixed(1) + ' dBTP';
    case 'outputGain': return (value - 50).toFixed(1) + ' dB';
    case 'outputCeiling': return ((value - 100) * 0.05).toFixed(1) + ' dB';
    case 'outputStereo': return Math.round(value) + '%';
    case 'outputClipper': return value > 50 ? 'ON' : 'OFF';
    default: return value.toFixed(0);
  }
}

function applyKnobValue(knobId, value) {
  if (!audioCtx) return;
  
  try {
    switch(knobId) {
      case 'inputGain':
        if (inputGainNode) inputGainNode.gain.value = dbToGain(value - 50);
        break;
      case 'agcTarget':
        if (agcGainNode) agcGainNode.gain.value = dbToGain((value - 50) * 0.6 - 20);
        break;
      case 'eq1': case 'eq2': case 'eq3': case 'eq4': case 'eq5': case 'eq6':
        const eqIdx = parseInt(knobId.replace('eq', '')) - 1;
        if (eqFilters[eqIdx]) {
          eqFilters[eqIdx].gain.value = (value - 50) / 50 * 12;
        }
        break;
      case 'outputGain':
        if (outputGainNode) outputGainNode.gain.value = dbToGain(value - 50);
        break;
      case 'peakThresh':
        if (peakLimiter) peakLimiter.threshold.value = (value - 100) * 0.1;
        break;
      case 'peakAttack':
        if (peakLimiter) peakLimiter.attack.value = Math.max(0.0001, value * 0.00005);
        break;
      case 'peakRelease':
        if (peakLimiter) peakLimiter.release.value = Math.max(0.01, value * 0.002);
        break;
      case 'bassThresh':
        if (bassLimiter) bassLimiter.threshold.value = (value - 100) * 0.1;
        break;
      case 'loudTarget':
        if (loudnessLimiter) loudnessLimiter.threshold.value = (value - 100) * 0.2;
        break;
      case 'loudCeiling':
        if (loudnessLimiter) loudnessLimiter.knee.value = Math.max(0, (value - 100) * 0.1);
        break;
    }
  } catch(e) {
    console.error('Apply knob error:', e);
  }
}

function dbToGain(db) {
  return Math.pow(10, db / 20);
}

// Initialize all knob displays
function initAllKnobDisplays() {
  Object.keys(knobState).forEach(id => updateKnobDisplay(id));
}

// ===== POWER TOGGLES =====
function togglePower(moduleName) {
  const btn = document.getElementById('pwr' + moduleName);
  const status = document.getElementById('status' + moduleName);
  const mod = document.getElementById('mod' + moduleName);
  
  modules[moduleName] = !modules[moduleName];
  
  if (modules[moduleName]) {
    btn.classList.add('on');
    status.className = 'status-led on';
    if (mod) mod.classList.add('active-module');
  } else {
    btn.classList.remove('on');
    status.className = 'status-led';
    if (mod) mod.classList.remove('active-module');
  }
  
  updateInfoPanel();
}

// ===== MODULE TOGGLES =====
function toggleMono() {
  document.getElementById('btnInputMono').classList.toggle('on');
}
function toggleMute() {
  document.getElementById('btnInputMute').classList.toggle('on');
  if (inputGainNode) {
    const isMuted = document.getElementById('btnInputMute').classList.contains('on');
    inputGainNode.gain.value = isMuted ? 0 : dbToGain(knobState.inputGain - 50);
  }
}
function togglePhaseInv() {
  document.getElementById('btnInputPhaseInv').classList.toggle('on');
}
function toggleAgc() {
  document.getElementById('btnAgcOn').classList.toggle('on');
}
function toggleAgcAuto() {
  document.getElementById('btnAgcAuto').classList.toggle('on');
}
function toggleLimiter(type) {
  const btnId = 'btn' + type.charAt(0).toUpperCase() + type.slice(1) + 'On';
  document.getElementById(btnId).classList.toggle('on');
}
function toggleDither() {
  document.getElementById('btnOutDither').classList.toggle('on');
}
function toggleOutputClip() {
  document.getElementById('btnOutClip').classList.toggle('on');
}

// ===== PRESET SYSTEM =====
function loadPreset(presetId) {
  const preset = presets[presetId];
  if (!preset) return;
  
  // Update active state in tree
  document.querySelectorAll('.tree-item').forEach(el => el.classList.remove('active'));
  event.target.classList.add('active');
  
  // Apply values
  knobState.inputGain = 50 + (preset.inputGain || 0);
  knobState.agcTarget = 50 + ((preset.agcTarget || 50) - 50) * 0.5 + 25;
  
  if (preset.eq) {
    preset.eq.forEach((val, i) => {
      knobState['eq' + (i+1)] = 50 + (val / 12) * 50;
    });
  }
  
  // Update compressor bands
  if (preset.comp) {
    preset.comp.forEach((band, i) => {
      const bandNum = i + 1;
      document.getElementById('sliderB' + bandNum + 'Thresh').value = band.thresh;
      document.getElementById('sliderB' + bandNum + 'Ratio').value = band.ratio;
      document.getElementById('sliderB' + bandNum + 'Attack').value = band.atk;
      document.getElementById('sliderB' + bandNum + 'Release').value = band.rel;
      document.getElementById('sliderB' + bandNum + 'Makeup').value = band.makeup;
      
      document.getElementById('b' + bandNum + 'Thresh').textContent = band.thresh;
      document.getElementById('b' + bandNum + 'Ratio').textContent = band.ratio + ':1';
      document.getElementById('b' + bandNum + 'Attack').textContent = band.atk;
      document.getElementById('b' + bandNum + 'Release').textContent = band.rel;
      document.getElementById('b' + bandNum + 'Makeup').textContent = band.makeup;
    });
  }
  
  // Update all knob displays
  initAllKnobDisplays();
  
  // Apply to audio
  applyAllKnobValues();
  
  // Update info
  document.getElementById('infoPresetName').textContent = preset.name;
}

function applyAllKnobValues() {
  Object.keys(knobState).forEach(id => applyKnobValue(id, knobState[id]));
}

function savePreset() {
  const name = prompt('Enter preset name:', 'My Preset');
  if (!name) return;
  
  const preset = {
    name: name,
    inputGain: knobState.inputGain - 50,
    agcTarget: knobState.agcTarget,
    agcSpeed: knobState.agcSpeed,
    eq: [],
    comp: [],
    peakThresh: knobState.peakThresh,
    bassThresh: knobState.bassThresh,
    loudTarget: knobState.loudTarget,
    outputGain: knobState.outputGain - 50
  };
  
  for (let i = 1; i <= 6; i++) {
    preset.eq.push(Math.round((knobState['eq' + i] - 50) / 50 * 12));
  }
  
  for (let i = 1; i <= 5; i++) {
    preset.comp.push({
      thresh: parseFloat(document.getElementById('sliderB' + i + 'Thresh').value),
      ratio: parseFloat(document.getElementById('sliderB' + i + 'Ratio').value),
      atk: parseInt(document.getElementById('sliderB' + i + 'Attack').value),
      rel: parseInt(document.getElementById('sliderB' + i + 'Release').value),
      makeup: parseInt(document.getElementById('sliderB' + i + 'Makeup').value)
    });
  }
  
  presets['custom' + Object.keys(presets).filter(k => k.startsWith('custom')).length + 1] = preset;
  alert('Preset "' + name + '" saved!');
}

function exportPreset() {
  const data = JSON.stringify(presets, null, 2);
  const blob = new Blob([data], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'omniapro-presets.json';
  a.click();
  URL.revokeObjectURL(url);
}

function importPreset() {
  const input = document.createElement('input');
  input.type = 'file';
  input.accept = '.json';
  input.onchange = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    reader.onload = (ev) => {
      try {
        const imported = JSON.parse(ev.target.result);
        Object.assign(presets, imported);
        alert('Presets imported successfully!');
      } catch(err) {
        alert('Error importing presets: ' + err.message);
      }
    };
    reader.readAsText(file);
  };
  input.click();
}

// ===== COMPRESSOR BAND PARAMS =====
function updateBandParam(band, param, value) {
  const val = parseFloat(value);
  document.getElementById('b' + band + capitalize(param)).textContent = 
    param === 'ratio' ? val + ':1' : val;
  
  if (compBands[band - 1]) {
    switch(param) {
      case 'threshold': compBands[band-1].threshold.value = val; break;
      case 'ratio': compBands[band-1].ratio.value = val; break;
      case 'attack': compBands[band-1].attack.value = val / 1000; break;
      case 'release': compBands[band-1].release.value = val / 1000; break;
    }
  }
}

// ===== INPUT SOURCE =====
function setInputSource(type) {
  document.querySelectorAll('.toolbar-btn').forEach(b => b.classList.remove('active'));
  
  switch(type) {
    case 'mic':
      document.getElementById('btnMic').classList.add('active');
      break;
    case 'file':
      document.getElementById('btnFile').classList.add('active');
      // Open file dialog
      const input = document.createElement('input');
      input.type = 'file';
      input.accept = 'audio/*';
      input.onchange = (e) => {
        const file = e.target.files[0];
        if (file && audioCtx) {
          const reader = new FileReader();
          reader.onload = (ev) => {
            audioCtx.decodeAudioData(ev.target.result, (buffer) => {
              if (inputSource) try { inputSource.disconnect(); } catch(e) {}
              if (oscillatorNode) try { oscillatorNode.stop(); } catch(e) {}
              if (noiseNode) try { noiseNode.stop(); } catch(e) {}
              
              const source = audioCtx.createBufferSource();
              source.buffer = buffer;
              source.loop = true;
              inputSource = source;
              source.start();
              buildAudioChain();
              document.getElementById('infoInput').textContent = file.name;
            });
          };
          reader.readAsArrayBuffer(file);
        }
      };
      input.click();
      return;
    case 'sine':
      document.getElementById('btnSine').classList.add('active');
      break;
    case 'noise':
      document.getElementById('btnNoise').classList.add('active');
      break;
  }
  
  if (isAudioStarted) {
    setupInputSource(type);
  }
}

// ===== DEVICE SELECTION =====
async function changeInputDevice(deviceId) {
  if (!audioCtx || !deviceId) return;
  
  try {
    if (audioCtx.setSinkId) {
      await audioCtx.setSinkId(deviceId);
    }
  } catch(e) {
    console.warn('setSinkId not supported:', e);
  }
  
  // Re-setup with new device
  if (currentInputType === 'mic') {
    const stream = await navigator.mediaDevices.getUserMedia({ 
      audio: { deviceId: deviceId ? { exact: deviceId } : undefined } 
    });
    if (inputSource) try { inputSource.disconnect(); } catch(e) {}
    inputSource = audioCtx.createMediaStreamSource(stream);
    buildAudioChain();
  }
}

async function changeOutputDevice(deviceId) {
  if (!audioCtx || !deviceId) return;
  
  try {
    if (audioCtx.setSinkId) {
      await audioCtx.setSinkId(deviceId);
      document.getElementById('infoOutput').textContent = deviceId.substring(0, 20);
    } else {
      console.warn('setSinkId not supported in this browser');
    }
  } catch(e) {
    console.warn('setSinkId error:', e);
  }
}

// ===== PANEL TOGGLES =====
function togglePanel(side) {
  if (side === 'left') {
    document.getElementById('leftPanel').classList.toggle('collapsed');
  } else if (side === 'right') {
    document.getElementById('rightPanel').classList.toggle('collapsed');
  }
}

// ===== RESIZE HANDLES =====
function startResize(event, side) {
  const panel = document.getElementById(side === 'left' ? 'leftPanel' : 'rightPanel');
  const startX = event.clientX;
  const startWidth = panel.offsetWidth;
  
  function onMove(e) {
    const delta = side === 'left' ? e.clientX - startX : startX - e.clientX;
    const newWidth = Math.max(150, Math.min(400, startWidth + delta));
    panel.style.width = newWidth + 'px';
    panel.style.minWidth = newWidth + 'px';
  }
  
  function onUp() {
    document.removeEventListener('mousemove', onMove);
    document.removeEventListener('mouseup', onUp);
  }
  
  document.addEventListener('mousemove', onMove);
  document.addEventListener('mouseup', onUp);
}

// ===== CONTEXT MENU =====
document.addEventListener('contextmenu', (e) => {
  e.preventDefault();
  const menu = document.getElementById('contextMenu');
  menu.style.left = e.clientX + 'px';
  menu.style.top = e.clientY + 'px';
  menu.classList.add('visible');
});

document.addEventListener('click', () => {
  document.getElementById('contextMenu').classList.remove('visible');
});

function resetModule() { alert('Module reset'); }
function bypassModule() { alert('Module bypassed'); }
function copySettings() { alert('Settings copied'); }
function pasteSettings() { alert('Settings pasted'); }

// ===== SETTINGS =====
function showSettings() {
  document.getElementById('settingsModal').classList.add('visible');
}
function closeSettings() {
  document.getElementById('settingsModal').classList.remove('visible');
}
function applySettings() {
  closeSettings();
  alert('Settings applied!');
}

// ===== UTILITY =====
function bypassAll() {
  const btn = document.getElementById('btnBypass');
  btn.classList.toggle('active');
  const isBypassed = btn.classList.contains('active');
  
  if (outputGainNode) {
    outputGainNode.gain.value = isBypassed ? 0 : dbToGain(knobState.outputGain - 50);
  }
  
  document.getElementById('infoEngine').textContent = isBypassed ? 'Bypassed' : 'Running';
}

function resetAll() {
  if (confirm('Reset all settings to defaults?')) {
    loadPreset('fm-loud');
  }
}

function updateInfoPanel() {
  document.getElementById('infoAGC').textContent = modules.AGC ? 'ON' : 'OFF';
}

function updateClock() {
  setInterval(() => {
    const now = new Date();
    document.getElementById('clockDisplay').textContent = 
      now.toTimeString().split(' ')[0];
  }, 1000);
}

function closeApp() {
  if (confirm('Close OmniaPro?')) {
    if (audioCtx) audioCtx.close();
    window.close();
  }
}

function minimizeApp() {
  // In a real Electron app, this would minimize the window
  alert('Minimize (requires Electron wrapper)');
}

function toggleCategory(el) {
  el.classList.toggle('open');
  const items = el.nextElementSibling;
  items.classList.toggle('visible');
}

// ===== ENUMERATE DEVICES =====
async function enumerateDevices() {
  try {
    const devices = await navigator.mediaDevices.enumerateDevices();
    const audioInputs = devices.filter(d => d.kind === 'audioinput');
    const audioOutputs = devices.filter(d => d.kind === 'audiooutput');
    
    const inputSelect = document.getElementById('inputDeviceSelect');
    audioInputs.forEach(d => {
      const opt = document.createElement('option');
      opt.value = d.deviceId;
      opt.textContent = d.label || 'Input ' + (audioInputs.indexOf(d) + 1);
      inputSelect.appendChild(opt);
    });
    
    const outputSelect = document.getElementById('outputDeviceSelect');
    audioOutputs.forEach(d => {
      const opt = document.createElement('option');
      opt.value = d.deviceId;
      opt.textContent = d.label || 'Output ' + (audioOutputs.indexOf(d) + 1);
      outputSelect.appendChild(opt);
    });
  } catch(e) {
    console.warn('Device enumeration failed:', e);
  }
}

// ===== INITIALIZE =====
document.addEventListener('DOMContentLoaded', () => {
  initAllKnobDisplays();
  enumerateDevices();
  
  // Start audio on first user interaction
  document.addEventListener('click', async function startAudio() {
    if (!isAudioStarted) {
      await initAudio();
    }
    document.removeEventListener('click', startAudio);
  }, { once: false });
  
  // Also try to start on any interaction
  const startElements = document.querySelectorAll('.toolbar-btn, .power-btn, .toggle-btn, .knob-control');
  startElements.forEach(el => {
    el.addEventListener('click', async () => {
      if (!isAudioStarted) {
        await initAudio();
      }
    }, { once: true });
  });
});

// ===== KEYBOARD SHORTCUTS =====
document.addEventListener('keydown', (e) => {
  if (e.ctrlKey && e.key === 's') {
    e.preventDefault();
    savePreset();
  }
  if (e.ctrlKey && e.key === 'z') {
    e.preventDefault();
    // Undo
  }
  if (e.key === 'F5') {
    e.preventDefault();
    resetAll();
  }
  if (e.key === 'Escape') {
    closeSettings();
  }
  if (e.key === ' ') {
    e.preventDefault();
    bypassAll();
  }
});

// ===== TOUCH SUPPORT FOR KNOBS =====
document.addEventListener('touchstart', (e) => {
  const knob = e.target.closest('.knob-control');
  if (knob) {
    const knobId = knob.getAttribute('onmousedown')?.match(/'(\w+)'/)?.[1];
    if (knobId) {
      e.preventDefault();
      activeKnob = knobId;
      knobStartY = e.touches[0].clientY;
      knobStartValue = knobState[knobId];
    }
  }
});

document.addEventListener('touchmove', (e) => {
  if (activeKnob && e.touches.length > 0) {
    e.preventDefault();
    const delta = (knobStartY - e.touches[0].clientY) * 0.5;
    let newValue = knobStartValue + delta;
    newValue = Math.max(0, Math.min(100, newValue));
    knobState[activeKnob] = newValue;
    updateKnobDisplay(activeKnob);
    applyKnobValue(activeKnob, newValue);
  }
});

document.addEventListener('touchend', () => {
  activeKnob = null;
});
</script>
</body>
</html>
