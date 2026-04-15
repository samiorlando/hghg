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
    --slider-track: #2a2a35;
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

  /* === LEFT PANEL === */
  .left-panel {
    width: 220px;
    min-width: 220px;
    background: var(--bg-dark);
    border-right: 1px solid var(--border-color);
    display: flex;
    flex-direction: column;
    overflow: hidden;
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
  .tree-category .arrow { font-size: 8px; transition: transform 0.2s; }
  .tree-category.open .arrow { transform: rotate(90deg); }
  .tree-items { display: none; padding-left: 8px; }
  .tree-items.visible { display: block; }

  .tree-item {
    padding: 5px 12px 5px 24px;
    font-size: 11px;
    color: var(--text-dim);
    cursor: pointer;
    transition: all 0.15s;
    border-left: 2px solid transparent;
  }
  .tree-item:hover { background: rgba(0,170,255,0.05); color: var(--text-primary); }
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
    cursor: pointer;
    border-radius: 3px;
    text-transform: uppercase;
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

  /* === METERS === */
  .meters-section {
    background: var(--bg-panel);
    border-bottom: 1px solid var(--border-color);
    padding: 8px 12px;
    display: flex;
    align-items: flex-start;
    gap: 16px;
    min-height: 160px;
    overflow-x: auto;
  }
  .meter-group {
    display: flex;
    flex-direction: column;
    align-items: center;
    flex: 1;
    min-width: 80px;
  }
  .meter-label {
    font-family: 'Orbitron', sans-serif;
    font-size: 8px;
    letter-spacing: 2px;
    color: var(--text-secondary);
    margin-bottom: 6px;
    text-transform: uppercase;
  }
  .meter-container { display: flex; gap: 4px; align-items: flex-end; }
  .meter-bar-wrapper { display: flex; flex-direction: column; align-items: center; gap: 4px; }
  .meter-bar-label { font-size: 8px; color: var(--text-dim); font-family: 'Share Tech Mono', monospace; }
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
  }
  .meter-db {
    font-size: 9px;
    font-family: 'Share Tech Mono', monospace;
    color: var(--text-secondary);
    min-width: 36px;
    text-align: center;
  }
  .meter-db-value { color: var(--accent-green); font-weight: 700; }

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

  .module-row { display: flex; gap: 8px; flex-wrap: wrap; }

  .module {
    background: var(--bg-module);
    border: 1px solid var(--border-color);
    border-radius: 6px;
    overflow: hidden;
    flex: 1;
    min-width: 300px;
    transition: border-color 0.3s;
  }
  .module:hover { border-color: var(--border-light); }
  .module.active-module { border-color: var(--accent-blue); box-shadow: var(--glow-blue); }
  .module-advanced { border-color: var(--accent-orange); }
  .module-advanced .module-title { color: var(--accent-orange); }

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
    width: 6px; height: 6px;
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
    gap: 12px;
    justify-content: center;
    align-items: flex-start;
  }

  /* === FADER CONTROL (REEMPLAZA KNOB) === */
  .fader-control {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    cursor: pointer;
    width: 52px;
    position: relative;
  }
  .fader-value-display {
    font-family: 'Share Tech Mono', monospace;
    font-size: 10px;
    color: var(--accent-blue);
    text-align: center;
    padding: 2px 6px;
    background: #0a0a10;
    border-radius: 3px;
    min-width: 48px;
    border: 1px solid #333;
  }
  .fader-container {
    width: 40px;
    height: 120px;
    background: linear-gradient(180deg, #1a1a25, #111);
    border-radius: 4px;
    position: relative;
    border: 1px solid #333;
    box-shadow: inset 0 2px 8px rgba(0,0,0,0.5);
  }
  .fader-track {
    position: absolute;
    left: 50%;
    top: 10px;
    bottom: 10px;
    width: 4px;
    transform: translateX(-50%);
    background: var(--slider-track);
    border-radius: 2px;
  }
  .fader-fill {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: linear-gradient(0deg, #00aaff, #0066cc);
    border-radius: 2px;
    transition: height 0.05s;
  }
  .fader-groove {
    position: absolute;
    left: 0; right: 0;
    top: 10px;
    bottom: 10px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    pointer-events: none;
  }
  .fader-groove-line {
    width: 100%;
    height: 1px;
    background: rgba(255,255,255,0.05);
  }
  .fader-thumb {
    position: absolute;
    left: 50%;
    transform: translate(-50%, 50%);
    width: 28px;
    height: 14px;
    background: linear-gradient(180deg, #ddd 0%, #999 50%, #888 100%);
    border-radius: 2px;
    box-shadow: 0 2px 6px rgba(0,0,0,0.6), inset 0 1px 0 rgba(255,255,255,0.3);
    cursor: grab;
    z-index: 5;
    transition: background 0.1s;
  }
  .fader-thumb:hover {
    background: linear-gradient(180deg, #eee 0%, #aaa 50%, #999 100%);
  }
  .fader-thumb:active { cursor: grabbing; }
  .fader-thumb-line {
    position: absolute;
    top: 50%;
    left: 4px;
    right: 4px;
    height: 1px;
    background: #666;
    transform: translateY(-50%);
  }
  .fader-label {
    font-size: 8px;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 0.5px;
    text-align: center;
  }
  .fader-scale {
    position: absolute;
    right: -16px;
    top: 10px;
    bottom: 10px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    pointer-events: none;
  }
  .fader-scale-mark {
    font-size: 6px;
    color: var(--text-dim);
    font-family: 'Share Tech Mono', monospace;
  }

  /* Fader horizontal para ecualizador */
  .fader-h {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    width: 48px;
  }
  .fader-h-container {
    width: 8px;
    height: 80px;
    background: linear-gradient(180deg, #1a1a25, #111);
    border-radius: 4px;
    position: relative;
    border: 1px solid #333;
    box-shadow: inset 0 2px 8px rgba(0,0,0,0.5);
  }
  .fader-h-track {
    position: absolute;
    left: 50%;
    top: 10px;
    bottom: 10px;
    width: 2px;
    transform: translateX(-50%);
    background: var(--slider-track);
    border-radius: 1px;
  }
  .fader-h-fill {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: linear-gradient(0deg, #00aaff, #0066cc);
    border-radius: 1px;
    transition: height 0.05s;
  }
  .fader-h-thumb {
    position: absolute;
    left: 50%;
    transform: translate(-50%, 50%);
    width: 24px;
    height: 12px;
    background: linear-gradient(180deg, #ddd, #999);
    border-radius: 2px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.5);
    cursor: grab;
    z-index: 5;
  }
  .fader-h-thumb:active { cursor: grabbing; }
  .fader-h-thumb::after {
    content: '';
    position: absolute;
    top: 50%;
    left: 3px;
    right: 3px;
    height: 1px;
    background: #666;
  }

  /* === TOGGLE BUTTON === */
  .toggle-btn {
    padding: 4px 12px;
    background: var(--bg-panel);
    border: 1px solid var(--border-color);
    color: var(--text-dim);
    font-size: 9px;
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
  .eq-canvas { width: 100%; height: 100%; }

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
  .info-group { margin-bottom: 12px; }
  .info-group-title {
    font-size: 8px;
    color: var(--text-dim);
    text-transform: uppercase;
    letter-spacing: 1px;
    margin-bottom: 6px;
    border-bottom: 1px solid #222;
    padding-bottom: 4px;
  }
  .info-row { display: flex; justify-content: space-between; padding: 3px 0; font-size: 10px; }
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
  .bottom-bar .status-item { display: flex; align-items: center; gap: 4px; }
  .bottom-bar .dot {
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--accent-green);
    box-shadow: 0 0 4px var(--accent-green);
  }
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
    flex-wrap: wrap;
  }
  .toolbar-btn {
    padding: 4px 10px;
    background: transparent;
    border: 1px solid transparent;
    color: var(--text-secondary);
    font-size: 10px;
    cursor: pointer;
    border-radius: 3px;
    transition: all 0.2s;
    display: flex;
    align-items: center;
    gap: 4px;
  }
  .toolbar-btn:hover { background: rgba(0,170,255,0.1); color: var(--accent-blue); border-color: rgba(0,170,255,0.2); }
  .toolbar-btn.active { background: rgba(0,170,255,0.15); color: var(--accent-blue); border-color: var(--accent-blue); }
  .toolbar-sep { width: 1px; height: 20px; background: var(--border-color); margin: 0 4px; }

  /* === COMPRESSOR BAND DISPLAY === */
  .band-display {
    display: flex;
    gap: 8px;
    width: 100%;
    flex-wrap: wrap;
    justify-content: center;
  }
  .band-card {
    background: #151520;
    border: 1px solid #2a2a35;
    border-radius: 4px;
    padding: 10px 8px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
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
    gap: 8px;
    justify-content: center;
  }
  .band-fader {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2px;
  }
  .band-fader-value {
    font-size: 8px;
    color: var(--accent-blue);
    font-family: 'Share Tech Mono', monospace;
  }
  .band-fader-container {
    width: 8px;
    height: 60px;
    background: linear-gradient(180deg, #1a1a25, #111);
    border-radius: 3px;
    position: relative;
    border: 1px solid #333;
  }
  .band-fader-track {
    position: absolute;
    left: 50%;
    top: 5px;
    bottom: 5px;
    width: 2px;
    transform: translateX(-50%);
    background: var(--slider-track);
    border-radius: 1px;
  }
  .band-fader-fill {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: linear-gradient(0deg, #00aaff, #0066cc);
    border-radius: 1px;
  }
  .band-fader-thumb {
    position: absolute;
    left: 50%;
    transform: translate(-50%, 50%);
    width: 20px;
    height: 8px;
    background: linear-gradient(180deg, #ddd, #999);
    border-radius: 1px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.5);
    cursor: grab;
    z-index: 5;
  }
  .band-fader-thumb:active { cursor: grabbing; }
  .band-fader-label {
    font-size: 7px;
    color: var(--text-dim);
    text-transform: uppercase;
  }

  .band-actions { display: flex; gap: 4px; margin-top: 4px; }
  .band-action-btn {
    width: 20px; height: 16px;
    border: 1px solid var(--border-color);
    background: var(--bg-panel);
    color: var(--text-dim);
    font-size: 8px;
    cursor: pointer;
    border-radius: 2px;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .band-action-btn.solo { border-color: var(--accent-yellow); color: var(--accent-yellow); }
  .band-action-btn.solo.active { background: rgba(255,221,0,0.2); }
  .band-action-btn.mute { border-color: var(--accent-red); color: var(--accent-red); }
  .band-action-btn.mute.active { background: rgba(255,51,68,0.2); }

  /* === LIMITER DISPLAY === */
  .limiter-row { display: flex; gap: 10px; width: 100%; justify-content: center; flex-wrap: wrap; }
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

  /* === SPECTRUM & PHASE === */
  .spectrum-container {
    width: 100%;
    height: 60px;
    background: #0a0a10;
    border: 1px solid #222;
    border-radius: 4px;
    overflow: hidden;
  }
  .spectrum-canvas { width: 100%; height: 100%; }
  .phase-meter {
    width: 60px;
    height: 60px;
    border-radius: 50%;
    background: #0a0a10;
    border: 1px solid #333;
    position: relative;
    overflow: hidden;
  }
  .phase-meter canvas { width: 100%; height: 100%; }

  /* === ADVANCED MODULES === */
  .lufs-display {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 8px 12px;
    background: #151520;
    border-radius: 4px;
    margin: 8px 0;
    width: 100%;
  }
  .lufs-value {
    font-family: 'Orbitron', sans-serif;
    font-size: 18px;
    font-weight: 700;
    color: var(--accent-green);
    min-width: 80px;
    text-align: center;
  }
  .lufs-value.warning { color: var(--accent-yellow); }
  .lufs-value.error { color: var(--accent-red); }
  .lufs-bar {
    flex: 1;
    height: 20px;
    background: #222;
    border-radius: 10px;
    overflow: hidden;
    position: relative;
  }
  .lufs-fill {
    height: 100%;
    background: linear-gradient(90deg, #00cc44, #ffcc00, #ff4444);
    border-radius: 10px;
    transition: width 0.1s;
    width: 50%;
  }
  .lufs-marker {
    position: absolute;
    width: 2px;
    height: 100%;
    background: rgba(255,255,255,0.3);
  }
  .lufs-marker.target { background: var(--accent-blue); width: 3px; }
  .lufs-labels {
    display: flex;
    justify-content: space-between;
    font-size: 8px;
    color: var(--text-dim);
    margin-top: 4px;
    font-family: 'Share Tech Mono', monospace;
  }

  .imager-controls {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 12px;
    width: 100%;
  }
  .imager-band {
    background: #151520;
    border: 1px solid #2a2a35;
    border-radius: 4px;
    padding: 8px;
    text-align: center;
  }
  .imager-band-title {
    font-size: 8px;
    color: var(--text-secondary);
    margin-bottom: 6px;
    text-transform: uppercase;
  }
  .imager-value {
    font-size: 9px;
    color: var(--accent-blue);
    font-family: 'Share Tech Mono', monospace;
  }

  .deesser-display {
    display: flex;
    gap: 16px;
    align-items: center;
    width: 100%;
    flex-wrap: wrap;
  }
  .deesser-spectrum {
    flex: 1;
    min-width: 200px;
    height: 50px;
    background: #0a0a10;
    border: 1px solid #222;
    border-radius: 4px;
    position: relative;
    overflow: hidden;
  }
  .deesser-spectrum canvas { width: 100%; height: 100%; }
  .deesser-threshold {
    position: absolute;
    left: 0; right: 0;
    height: 2px;
    background: var(--accent-red);
    cursor: ns-resize;
  }
  .deesser-freq-marker {
    position: absolute;
    bottom: 0; top: 0;
    width: 2px;
    background: var(--accent-blue);
    cursor: ew-resize;
  }

  .ab-compare {
    display: flex;
    gap: 8px;
    align-items: center;
    padding: 8px 12px;
    background: #151520;
    border-radius: 4px;
  }
  .ab-btn {
    padding: 6px 16px;
    border: 2px solid var(--border-color);
    background: var(--bg-panel);
    color: var(--text-secondary);
    font-family: 'Orbitron', sans-serif;
    font-size: 10px;
    cursor: pointer;
    border-radius: 4px;
    transition: all 0.2s;
    text-transform: uppercase;
    letter-spacing: 1px;
  }
  .ab-btn.active {
    border-color: var(--accent-blue);
    color: var(--accent-blue);
    background: rgba(0,170,255,0.1);
    box-shadow: var(--glow-blue);
  }
  .ab-btn.a { border-color: var(--accent-green); }
  .ab-btn.b { border-color: var(--accent-orange); }
  .ab-btn.a.active { border-color: var(--accent-green); color: var(--accent-green); background: rgba(0,255,68,0.1); }
  .ab-btn.b.active { border-color: var(--accent-orange); color: var(--accent-orange); background: rgba(255,136,0,0.1); }
  .ab-toggle {
    padding: 6px 12px;
    background: var(--accent-blue);
    color: #fff;
    border: none;
    border-radius: 4px;
    font-size: 9px;
    cursor: pointer;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }

  .macro-panel {
    background: #151520;
    border: 1px solid var(--border-color);
    border-radius: 4px;
    padding: 8px;
    margin: 8px 0;
  }
  .macro-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 8px;
    flex-wrap: wrap;
    gap: 4px;
  }
  .macro-title {
    font-size: 9px;
    color: var(--accent-orange);
    font-family: 'Orbitron', sans-serif;
    letter-spacing: 1px;
  }
  .macro-controls { display: flex; gap: 4px; flex-wrap: wrap; }
  .macro-btn {
    padding: 4px 8px;
    border: 1px solid var(--border-color);
    background: var(--bg-panel);
    color: var(--text-secondary);
    font-size: 8px;
    cursor: pointer;
    border-radius: 3px;
    display: flex;
    align-items: center;
    gap: 4px;
  }
  .macro-btn.record { border-color: var(--accent-red); color: var(--accent-red); }
  .macro-btn.record.active { background: rgba(255,51,68,0.15); animation: pulse 1s infinite; }
  .macro-btn.play { border-color: var(--accent-green); color: var(--accent-green); }
  .macro-timeline {
    height: 30px;
    background: #0a0a10;
    border-radius: 3px;
    position: relative;
    overflow: hidden;
    margin-top: 8px;
  }
  .macro-keyframe {
    position: absolute;
    top: 8px;
    width: 6px;
    height: 14px;
    background: var(--accent-blue);
    border-radius: 2px;
    transform: translateX(-50%);
  }

  .network-status {
    display: flex;
    align-items: center;
    gap: 6px;
    padding: 4px 8px;
    background: #151520;
    border-radius: 4px;
    font-size: 8px;
  }
  .network-dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: var(--text-dim);
    transition: all 0.3s;
  }
  .network-dot.connected { background: var(--accent-green); box-shadow: 0 0 6px var(--accent-green); }

  .dyn-eq-band {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 6px 8px;
    background: #151520;
    border-radius: 4px;
    margin: 4px 0;
  }
  .dyn-eq-indicator {
    width: 4px;
    height: 30px;
    background: #333;
    border-radius: 2px;
    position: relative;
    overflow: hidden;
  }
  .dyn-eq-active {
    position: absolute;
    bottom: 0;
    left: 0; right: 0;
    background: var(--accent-green);
    transition: height 0.05s;
  }
  .dyn-eq-info {
    flex: 1;
    font-size: 8px;
    color: var(--text-dim);
  }
  .dyn-eq-info.active { color: var(--accent-green); }

  .truepeak-badge {
    display: inline-flex;
    align-items: center;
    gap: 4px;
    padding: 2px 6px;
    background: rgba(255,51,68,0.1);
    border: 1px solid var(--accent-red);
    border-radius: 3px;
    font-size: 7px;
    color: var(--accent-red);
    font-family: 'Share Tech Mono', monospace;
    margin-left: 8px;
  }
  .truepeak-badge.ok {
    background: rgba(0,255,68,0.1);
    border-color: var(--accent-green);
    color: var(--accent-green);
  }

  .shortcuts-panel {
    position: fixed;
    bottom: 24px;
    right: 24px;
    background: var(--bg-panel);
    border: 1px solid var(--border-color);
    border-radius: 8px;
    padding: 12px;
    min-width: 280px;
    z-index: 1000;
    display: none;
    box-shadow: 0 8px 32px rgba(0,0,0,0.6);
  }
  .shortcuts-panel.visible { display: block; }
  .shortcuts-title {
    font-family: 'Orbitron', sans-serif;
    font-size: 10px;
    color: var(--accent-blue);
    letter-spacing: 1px;
    margin-bottom: 8px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .shortcuts-close {
    background: none;
    border: none;
    color: var(--text-dim);
    font-size: 14px;
    cursor: pointer;
  }
  .shortcut-row {
    display: flex;
    justify-content: space-between;
    padding: 4px 0;
    font-size: 9px;
    border-bottom: 1px solid #222;
  }
  .shortcut-row:last-child { border-bottom: none; }
  .shortcut-key {
    font-family: 'Share Tech Mono', monospace;
    background: #222;
    padding: 2px 6px;
    border-radius: 3px;
    color: var(--accent-blue);
  }

  .floating-analyzer {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 400px;
    height: 300px;
    background: var(--bg-panel);
    border: 2px solid var(--accent-blue);
    border-radius: 8px;
    z-index: 2000;
    display: none;
    box-shadow: 0 16px 64px rgba(0,0,0,0.8);
  }
  .floating-analyzer.visible { display: block; }
  .analyzer-header {
    height: 32px;
    background: linear-gradient(180deg, #2a2a35, #1e1e28);
    border-bottom: 1px solid var(--border-color);
    border-radius: 6px 6px 0 0;
    display: flex;
    align-items: center;
    padding: 0 12px;
    justify-content: space-between;
  }
  .analyzer-title {
    font-family: 'Orbitron', sans-serif;
    font-size: 10px;
    color: var(--accent-blue);
    letter-spacing: 1px;
  }
  .analyzer-close {
    background: none;
    border: none;
    color: #888;
    font-size: 16px;
    cursor: pointer;
  }
  .analyzer-body {
    padding: 12px;
    height: calc(100% - 32px);
  }
  .analyzer-canvas {
    width: 100%;
    height: 100%;
    background: #0a0a10;
    border-radius: 4px;
  }

  @keyframes pulse { 0%,100% { opacity:1; } 50% { opacity:0.5; } }

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
  .modal-actions { display: flex; gap: 8px; justify-content: flex-end; margin-top: 16px; }
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
  .modal-btn.primary { background: var(--accent-blue); border-color: var(--accent-blue); color: #fff; }
  .modal-btn:hover { opacity: 0.8; }

  select {
    background: var(--bg-panel-light);
    border: 1px solid var(--border-color);
    color: var(--text-primary);
    padding: 4px 8px;
    font-size: 10px;
    border-radius: 3px;
    cursor: pointer;
  }
  select:focus { outline: none; border-color: var(--accent-blue); }

  @media (max-width: 1400px) { .module { min-width: 260px; } }
  @media (max-width: 1200px) {
    .left-panel { width: 180px; min-width: 180px; }
    .right-panel { width: 160px; min-width: 160px; }
    .module { min-width: 220px; }
  }
  @media (max-width: 900px) {
    .left-panel { display: none; }
    .right-panel { display: none; }
    .module { min-width: 100%; }
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

  <!-- LEFT PANEL -->
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
      <button class="toolbar-btn" onclick="toggleABCompare()" id="btnAB">⚖ A/B</button>
      <button class="toolbar-btn" onclick="toggleDucking()" id="btnDuck">🎙 Duck</button>
      <button class="toolbar-btn" onclick="toggleMacroPanel()" id="btnMacro">🎬 Macro</button>
      <button class="toolbar-btn" onclick="toggleShortcuts()" id="btnKeys">⌨ Keys</button>
      <div class="toolbar-sep"></div>
      <div class="network-status">
        <div class="network-dot" id="networkDot"></div>
        <span id="networkStatus">Offline</span>
      </div>
    </div>

    <!-- METERS -->
    <div class="meters-section" id="metersSection">
      <div class="meter-group">
        <div class="meter-label">INPUT</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterInputL"><div class="meter-led" id="meterInputLFill"></div></div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterInputLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterInputR"><div class="meter-led" id="meterInputRFill"></div></div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterInputRVal">-∞ dB</div>
          </div>
        </div>
      </div>
      <div class="meter-group">
        <div class="meter-label">AGC</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterAGCL"><div class="meter-led" id="meterAGCLFill"></div></div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterAGCLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterAGCR"><div class="meter-led" id="meterAGCRFill"></div></div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterAGCRVal">-∞ dB</div>
          </div>
        </div>
      </div>
      <div class="meter-group">
        <div class="meter-label">COMPRESSOR</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterCompL"><div class="meter-led" id="meterCompLFill"></div></div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterCompLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterCompR"><div class="meter-led" id="meterCompRFill"></div></div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterCompRVal">-∞ dB</div>
          </div>
        </div>
      </div>
      <div class="meter-group">
        <div class="meter-label">EQ</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterEQL"><div class="meter-led" id="meterEQLFill"></div></div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterEQLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterEQR"><div class="meter-led" id="meterEQRFill"></div></div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterEQRVal">-∞ dB</div>
          </div>
        </div>
      </div>
      <div class="meter-group">
        <div class="meter-label">LIMITER</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterLimL"><div class="meter-led" id="meterLimLFill"></div></div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterLimLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterLimR"><div class="meter-led" id="meterLimRFill"></div></div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterLimRVal">-∞ dB</div>
          </div>
        </div>
      </div>
      <div class="meter-group">
        <div class="meter-label">OUTPUT</div>
        <div class="meter-container">
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterOutL"><div class="meter-led" id="meterOutLFill"></div></div>
            <div class="meter-bar-label">L</div>
            <div class="meter-db" id="meterOutLVal">-∞ dB</div>
          </div>
          <div class="meter-bar-wrapper">
            <div class="meter-bar" id="meterOutR"><div class="meter-led" id="meterOutRFill"></div></div>
            <div class="meter-bar-label">R</div>
            <div class="meter-db" id="meterOutRVal">-∞ dB</div>
          </div>
        </div>
      </div>
      <div class="meter-group">
        <div class="meter-label">PHASE</div>
        <div class="phase-meter"><canvas id="phaseCanvas" width="60" height="60"></canvas></div>
      </div>
      <div class="meter-group" style="flex:2; min-width: 150px;">
        <div class="meter-label">SPECTRUM</div>
        <div class="spectrum-container"><canvas id="spectrumCanvas" class="spectrum-canvas"></canvas></div>
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
            <!-- Fader Gain -->
            <div class="fader-control" data-fader="inputGain">
              <div class="fader-value-display" id="valInputGain">0.0 dB</div>
              <div class="fader-container" id="faderInputGain">
                <div class="fader-track"><div class="fader-fill" id="faderInputGainFill" style="height:50%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderInputGainThumb" style="bottom: 50%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">+24</div>
                  <div class="fader-scale-mark">0</div>
                  <div class="fader-scale-mark">-24</div>
                </div>
              </div>
              <div class="fader-label">Gain</div>
            </div>
            
            <!-- Fader Trim -->
            <div class="fader-control" data-fader="inputTrim">
              <div class="fader-value-display" id="valInputTrim">0.0 dB</div>
              <div class="fader-container" id="faderInputTrim">
                <div class="fader-track"><div class="fader-fill" id="faderInputTrimFill" style="height:50%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderInputTrimThumb" style="bottom: 50%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">+12</div>
                  <div class="fader-scale-mark">0</div>
                  <div class="fader-scale-mark">-12</div>
                </div>
              </div>
              <div class="fader-label">Trim</div>
            </div>
            
            <div style="display:flex;flex-direction:column;gap:4px;align-items:center; justify-content: center;">
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
            <div class="fader-control" data-fader="agcTarget">
              <div class="fader-value-display" id="valAgcTarget">-20 dB</div>
              <div class="fader-container" id="faderAgcTarget">
                <div class="fader-track"><div class="fader-fill" id="faderAgcTargetFill" style="height:50%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderAgcTargetThumb" style="bottom: 50%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">-10</div>
                  <div class="fader-scale-mark">-30</div>
                  <div class="fader-scale-mark">-50</div>
                </div>
              </div>
              <div class="fader-label">Target</div>
            </div>
            
            <div class="fader-control" data-fader="agcSpeed">
              <div class="fader-value-display" id="valAgcSpeed">Med</div>
              <div class="fader-container" id="faderAgcSpeed">
                <div class="fader-track"><div class="fader-fill" id="faderAgcSpeedFill" style="height:50%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderAgcSpeedThumb" style="bottom: 50%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">Fast</div>
                  <div class="fader-scale-mark">Med</div>
                  <div class="fader-scale-mark">Slow</div>
                </div>
              </div>
              <div class="fader-label">Speed</div>
            </div>
            
            <div class="fader-control" data-fader="agcRange">
              <div class="fader-value-display" id="valAgcRange">30 dB</div>
              <div class="fader-container" id="faderAgcRange">
                <div class="fader-track"><div class="fader-fill" id="faderAgcRangeFill" style="height:60%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderAgcRangeThumb" style="bottom: 60%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">60</div>
                  <div class="fader-scale-mark">30</div>
                  <div class="fader-scale-mark">0</div>
                </div>
              </div>
              <div class="fader-label">Range</div>
            </div>
            
            <div class="fader-control" data-fader="agcAttack">
              <div class="fader-value-display" id="valAgcAttack">50 ms</div>
              <div class="fader-container" id="faderAgcAttack">
                <div class="fader-track"><div class="fader-fill" id="faderAgcAttackFill" style="height:50%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderAgcAttackThumb" style="bottom: 50%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">200</div>
                  <div class="fader-scale-mark">100</div>
                  <div class="fader-scale-mark">0</div>
                </div>
              </div>
              <div class="fader-label">Attack</div>
            </div>
            
            <div class="fader-control" data-fader="agcRelease">
              <div class="fader-value-display" id="valAgcRelease">200 ms</div>
              <div class="fader-container" id="faderAgcRelease">
                <div class="fader-track"><div class="fader-fill" id="faderAgcReleaseFill" style="height:40%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderAgcReleaseThumb" style="bottom: 40%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">500</div>
                  <div class="fader-scale-mark">250</div>
                  <div class="fader-scale-mark">0</div>
                </div>
              </div>
              <div class="fader-label">Release</div>
            </div>
            
            <div style="display:flex;flex-direction:column;gap:4px;align-items:center; justify-content: center;">
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
          <div class="band-display" id="compBandsDisplay">
            <!-- B1 -->
            <div class="band-card active" id="band1">
              <div class="band-card-title">B1</div>
              <div class="band-card-freq">32-120 Hz</div>
              <div class="band-card-controls">
                <div class="band-fader">
                  <div class="band-fader-value" id="b1Thresh">-24</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b1ThreshFill" style="height:40%"></div></div>
                    <div class="band-fader-thumb" id="b1ThreshThumb" style="bottom: 40%"></div>
                  </div>
                  <div class="band-fader-label">Thresh</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b1Ratio">4:1</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b1RatioFill" style="height:20%"></div></div>
                    <div class="band-fader-thumb" id="b1RatioThumb" style="bottom: 20%"></div>
                  </div>
                  <div class="band-fader-label">Ratio</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b1Attack">10</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b1AttackFill" style="height:10%"></div></div>
                    <div class="band-fader-thumb" id="b1AttackThumb" style="bottom: 10%"></div>
                  </div>
                  <div class="band-fader-label">Atk</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b1Release">100</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b1ReleaseFill" style="height:10%"></div></div>
                    <div class="band-fader-thumb" id="b1ReleaseThumb" style="bottom: 10%"></div>
                  </div>
                  <div class="band-fader-label">Rel</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b1Makeup">6</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b1MakeupFill" style="height:25%"></div></div>
                    <div class="band-fader-thumb" id="b1MakeupThumb" style="bottom: 25%"></div>
                  </div>
                  <div class="band-fader-label">Mkup</div>
                </div>
              </div>
              <div class="band-actions">
                <button class="band-action-btn solo" onclick="soloBand(1)">S</button>
                <button class="band-action-btn mute" onclick="muteBand(1)">M</button>
              </div>
            </div>
            <!-- B2 -->
            <div class="band-card active" id="band2">
              <div class="band-card-title">B2</div>
              <div class="band-card-freq">120-400 Hz</div>
              <div class="band-card-controls">
                <div class="band-fader">
                  <div class="band-fader-value" id="b2Thresh">-20</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b2ThreshFill" style="height:50%"></div></div>
                    <div class="band-fader-thumb" id="b2ThreshThumb" style="bottom: 50%"></div>
                  </div>
                  <div class="band-fader-label">Thresh</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b2Ratio">3:1</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b2RatioFill" style="height:15%"></div></div>
                    <div class="band-fader-thumb" id="b2RatioThumb" style="bottom: 15%"></div>
                  </div>
                  <div class="band-fader-label">Ratio</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b2Attack">8</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b2AttackFill" style="height:8%"></div></div>
                    <div class="band-fader-thumb" id="b2AttackThumb" style="bottom: 8%"></div>
                  </div>
                  <div class="band-fader-label">Atk</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b2Release">120</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b2ReleaseFill" style="height:12%"></div></div>
                    <div class="band-fader-thumb" id="b2ReleaseThumb" style="bottom: 12%"></div>
                  </div>
                  <div class="band-fader-label">Rel</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b2Makeup">4</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b2MakeupFill" style="height:17%"></div></div>
                    <div class="band-fader-thumb" id="b2MakeupThumb" style="bottom: 17%"></div>
                  </div>
                  <div class="band-fader-label">Mkup</div>
                </div>
              </div>
              <div class="band-actions">
                <button class="band-action-btn solo" onclick="soloBand(2)">S</button>
                <button class="band-action-btn mute" onclick="muteBand(2)">M</button>
              </div>
            </div>
            <!-- B3 -->
            <div class="band-card active" id="band3">
              <div class="band-card-title">B3</div>
              <div class="band-card-freq">400-1.5k</div>
              <div class="band-card-controls">
                <div class="band-fader">
                  <div class="band-fader-value" id="b3Thresh">-18</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b3ThreshFill" style="height:55%"></div></div>
                    <div class="band-fader-thumb" id="b3ThreshThumb" style="bottom: 55%"></div>
                  </div>
                  <div class="band-fader-label">Thresh</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b3Ratio">3.5:1</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b3RatioFill" style="height:17%"></div></div>
                    <div class="band-fader-thumb" id="b3RatioThumb" style="bottom: 17%"></div>
                  </div>
                  <div class="band-fader-label">Ratio</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b3Attack">5</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b3AttackFill" style="height:5%"></div></div>
                    <div class="band-fader-thumb" id="b3AttackThumb" style="bottom: 5%"></div>
                  </div>
                  <div class="band-fader-label">Atk</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b3Release">150</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b3ReleaseFill" style="height:15%"></div></div>
                    <div class="band-fader-thumb" id="b3ReleaseThumb" style="bottom: 15%"></div>
                  </div>
                  <div class="band-fader-label">Rel</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b3Makeup">5</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b3MakeupFill" style="height:21%"></div></div>
                    <div class="band-fader-thumb" id="b3MakeupThumb" style="bottom: 21%"></div>
                  </div>
                  <div class="band-fader-label">Mkup</div>
                </div>
              </div>
              <div class="band-actions">
                <button class="band-action-btn solo" onclick="soloBand(3)">S</button>
                <button class="band-action-btn mute" onclick="muteBand(3)">M</button>
              </div>
            </div>
            <!-- B4 -->
            <div class="band-card active" id="band4">
              <div class="band-card-title">B4</div>
              <div class="band-card-freq">1.5k-5k</div>
              <div class="band-card-controls">
                <div class="band-fader">
                  <div class="band-fader-value" id="b4Thresh">-16</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b4ThreshFill" style="height:60%"></div></div>
                    <div class="band-fader-thumb" id="b4ThreshThumb" style="bottom: 60%"></div>
                  </div>
                  <div class="band-fader-label">Thresh</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b4Ratio">3:1</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b4RatioFill" style="height:15%"></div></div>
                    <div class="band-fader-thumb" id="b4RatioThumb" style="bottom: 15%"></div>
                  </div>
                  <div class="band-fader-label">Ratio</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b4Attack">3</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b4AttackFill" style="height:3%"></div></div>
                    <div class="band-fader-thumb" id="b4AttackThumb" style="bottom: 3%"></div>
                  </div>
                  <div class="band-fader-label">Atk</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b4Release">180</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b4ReleaseFill" style="height:18%"></div></div>
                    <div class="band-fader-thumb" id="b4ReleaseThumb" style="bottom: 18%"></div>
                  </div>
                  <div class="band-fader-label">Rel</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b4Makeup">4</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b4MakeupFill" style="height:17%"></div></div>
                    <div class="band-fader-thumb" id="b4MakeupThumb" style="bottom: 17%"></div>
                  </div>
                  <div class="band-fader-label">Mkup</div>
                </div>
              </div>
              <div class="band-actions">
                <button class="band-action-btn solo" onclick="soloBand(4)">S</button>
                <button class="band-action-btn mute" onclick="muteBand(4)">M</button>
              </div>
            </div>
            <!-- B5 -->
            <div class="band-card active" id="band5">
              <div class="band-card-title">B5</div>
              <div class="band-card-freq">5k-16k</div>
              <div class="band-card-controls">
                <div class="band-fader">
                  <div class="band-fader-value" id="b5Thresh">-14</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b5ThreshFill" style="height:65%"></div></div>
                    <div class="band-fader-thumb" id="b5ThreshThumb" style="bottom: 65%"></div>
                  </div>
                  <div class="band-fader-label">Thresh</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b5Ratio">2.5:1</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b5RatioFill" style="height:12%"></div></div>
                    <div class="band-fader-thumb" id="b5RatioThumb" style="bottom: 12%"></div>
                  </div>
                  <div class="band-fader-label">Ratio</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b5Attack">2</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b5AttackFill" style="height:2%"></div></div>
                    <div class="band-fader-thumb" id="b5AttackThumb" style="bottom: 2%"></div>
                  </div>
                  <div class="band-fader-label">Atk</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b5Release">200</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b5ReleaseFill" style="height:20%"></div></div>
                    <div class="band-fader-thumb" id="b5ReleaseThumb" style="bottom: 20%"></div>
                  </div>
                  <div class="band-fader-label">Rel</div>
                </div>
                <div class="band-fader">
                  <div class="band-fader-value" id="b5Makeup">3</div>
                  <div class="band-fader-container">
                    <div class="band-fader-track"><div class="band-fader-fill" id="b5MakeupFill" style="height:12%"></div></div>
                    <div class="band-fader-thumb" id="b5MakeupThumb" style="bottom: 12%"></div>
                  </div>
                  <div class="band-fader-label">Mkup</div>
                </div>
              </div>
              <div class="band-actions">
                <button class="band-action-btn solo" onclick="soloBand(5)">S</button>
                <button class="band-action-btn mute" onclick="muteBand(5)">M</button>
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
            <div style="display:flex;gap:12px;justify-content:center;flex-wrap:wrap;margin-top:8px;">
              <div class="fader-h" data-fader="eq1">
                <div class="fader-value-display" id="valEQ1" style="font-size:8px; min-width: auto; padding: 1px 4px;">0 dB</div>
                <div class="fader-h-container" id="faderEQ1">
                  <div class="fader-h-track"><div class="fader-h-fill" id="faderEQ1Fill" style="height:50%"></div></div>
                  <div class="fader-h-thumb" id="faderEQ1Thumb" style="bottom: 50%"></div>
                </div>
                <div class="fader-label" style="font-size:7px;">32 Hz</div>
              </div>
              <div class="fader-h" data-fader="eq2">
                <div class="fader-value-display" id="valEQ2" style="font-size:8px; min-width: auto; padding: 1px 4px;">0 dB</div>
                <div class="fader-h-container" id="faderEQ2">
                  <div class="fader-h-track"><div class="fader-h-fill" id="faderEQ2Fill" style="height:50%"></div></div>
                  <div class="fader-h-thumb" id="faderEQ2Thumb" style="bottom: 50%"></div>
                </div>
                <div class="fader-label" style="font-size:7px;">100 Hz</div>
              </div>
              <div class="fader-h" data-fader="eq3">
                <div class="fader-value-display" id="valEQ3" style="font-size:8px; min-width: auto; padding: 1px 4px;">0 dB</div>
                <div class="fader-h-container" id="faderEQ3">
                  <div class="fader-h-track"><div class="fader-h-fill" id="faderEQ3Fill" style="height:50%"></div></div>
                  <div class="fader-h-thumb" id="faderEQ3Thumb" style="bottom: 50%"></div>
                </div>
                <div class="fader-label" style="font-size:7px;">400 Hz</div>
              </div>
              <div class="fader-h" data-fader="eq4">
                <div class="fader-value-display" id="valEQ4" style="font-size:8px; min-width: auto; padding: 1px 4px;">0 dB</div>
                <div class="fader-h-container" id="faderEQ4">
                  <div class="fader-h-track"><div class="fader-h-fill" id="faderEQ4Fill" style="height:50%"></div></div>
                  <div class="fader-h-thumb" id="faderEQ4Thumb" style="bottom: 50%"></div>
                </div>
                <div class="fader-label" style="font-size:7px;">1.5 kHz</div>
              </div>
              <div class="fader-h" data-fader="eq5">
                <div class="fader-value-display" id="valEQ5" style="font-size:8px; min-width: auto; padding: 1px 4px;">0 dB</div>
                <div class="fader-h-container" id="faderEQ5">
                  <div class="fader-h-track"><div class="fader-h-fill" id="faderEQ5Fill" style="height:50%"></div></div>
                  <div class="fader-h-thumb" id="faderEQ5Thumb" style="bottom: 50%"></div>
                </div>
                <div class="fader-label" style="font-size:7px;">5 kHz</div>
              </div>
              <div class="fader-h" data-fader="eq6">
                <div class="fader-value-display" id="valEQ6" style="font-size:8px; min-width: auto; padding: 1px 4px;">0 dB</div>
                <div class="fader-h-container" id="faderEQ6">
                  <div class="fader-h-track"><div class="fader-h-fill" id="faderEQ6Fill" style="height:50%"></div></div>
                  <div class="fader-h-thumb" id="faderEQ6Thumb" style="bottom: 50%"></div>
                </div>
                <div class="fader-label" style="font-size:7px;">12 kHz</div>
              </div>
            </div>
            
            <!-- Dynamic EQ -->
            <div style="margin-top: 8px; width: 100%;">
              <div style="font-size: 8px; color: var(--text-dim); margin-bottom: 4px; text-transform: uppercase; letter-spacing: 1px;">Dynamic EQ Bands</div>
              <div id="dynEqBands">
                <div class="dyn-eq-band">
                  <div class="dyn-eq-indicator"><div class="dyn-eq-active" id="dynEq1Active" style="height: 0%"></div></div>
                  <div class="dyn-eq-info" id="dynEq1Info">32 Hz • Threshold: -30 dB</div>
                  <button class="toggle-btn" style="padding: 2px 8px; font-size: 8px;" onclick="toggleDynEq(1)">ON</button>
                </div>
                <div class="dyn-eq-band">
                  <div class="dyn-eq-indicator"><div class="dyn-eq-active" id="dynEq3Active" style="height: 0%"></div></div>
                  <div class="dyn-eq-info" id="dynEq3Info">400 Hz • Threshold: -24 dB</div>
                  <button class="toggle-btn" style="padding: 2px 8px; font-size: 8px;" onclick="toggleDynEq(3)">ON</button>
                </div>
                <div class="dyn-eq-band">
                  <div class="dyn-eq-indicator"><div class="dyn-eq-active" id="dynEq5Active" style="height: 0%"></div></div>
                  <div class="dyn-eq-info" id="dynEq5Info">5 kHz • Threshold: -20 dB</div>
                  <button class="toggle-btn" style="padding: 2px 8px; font-size: 8px;" onclick="toggleDynEq(5)">ON</button>
                </div>
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
              <div class="limiter-card">
                <div class="limiter-title">PEAK</div>
                <div class="limiter-meter"><div class="limiter-meter-fill" id="peakMeterFill" style="height:0%"></div></div>
                <div class="fader-control" data-fader="peakThresh" style="width: 48px;">
                  <div class="fader-value-display" id="valPeakThresh" style="font-size:9px; min-width: auto; padding: 1px 4px;">-0.3 dB</div>
                  <div class="fader-container" id="faderPeakThresh" style="height:80px;">
                    <div class="fader-track"><div class="fader-fill" id="faderPeakThreshFill" style="height:90%"></div></div>
                    <div class="fader-groove">
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                    </div>
                    <div class="fader-thumb" id="faderPeakThreshThumb" style="bottom: 90%">
                      <div class="fader-thumb-line"></div>
                    </div>
                  </div>
                  <div class="fader-label" style="font-size:7px;">Threshold</div>
                </div>
                <div class="fader-control" data-fader="peakAttack" style="width: 48px;">
                  <div class="fader-value-display" id="valPeakAttack" style="font-size:9px; min-width: auto; padding: 1px 4px;">1 ms</div>
                  <div class="fader-container" id="faderPeakAttack" style="height:80px;">
                    <div class="fader-track"><div class="fader-fill" id="faderPeakAttackFill" style="height:20%"></div></div>
                    <div class="fader-groove">
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                    </div>
                    <div class="fader-thumb" id="faderPeakAttackThumb" style="bottom: 20%">
                      <div class="fader-thumb-line"></div>
                    </div>
                  </div>
                  <div class="fader-label" style="font-size:7px;">Attack</div>
                </div>
                <div class="fader-control" data-fader="peakRelease" style="width: 48px;">
                  <div class="fader-value-display" id="valPeakRelease" style="font-size:9px; min-width: auto; padding: 1px 4px;">50 ms</div>
                  <div class="fader-container" id="faderPeakRelease" style="height:80px;">
                    <div class="fader-track"><div class="fader-fill" id="faderPeakReleaseFill" style="height:50%"></div></div>
                    <div class="fader-groove">
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                    </div>
                    <div class="fader-thumb" id="faderPeakReleaseThumb" style="bottom: 50%">
                      <div class="fader-thumb-line"></div>
                    </div>
                  </div>
                  <div class="fader-label" style="font-size:7px;">Release</div>
                </div>
                <div class="toggle-btn on" id="btnPeakOn" onclick="toggleLimiter('peak')">ON</div>
              </div>
              
              <div class="limiter-card">
                <div class="limiter-title">BASS</div>
                <div class="limiter-meter"><div class="limiter-meter-fill" id="bassMeterFill" style="height:0%"></div></div>
                <div class="fader-control" data-fader="bassThresh" style="width: 48px;">
                  <div class="fader-value-display" id="valBassThresh" style="font-size:9px; min-width: auto; padding: 1px 4px;">-3 dB</div>
                  <div class="fader-container" id="faderBassThresh" style="height:80px;">
                    <div class="fader-track"><div class="fader-fill" id="faderBassThreshFill" style="height:80%"></div></div>
                    <div class="fader-groove">
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                    </div>
                    <div class="fader-thumb" id="faderBassThreshThumb" style="bottom: 80%">
                      <div class="fader-thumb-line"></div>
                    </div>
                  </div>
                  <div class="fader-label" style="font-size:7px;">Threshold</div>
                </div>
                <div class="fader-control" data-fader="bassFreq" style="width: 48px;">
                  <div class="fader-value-display" id="valBassFreq" style="font-size:9px; min-width: auto; padding: 1px 4px;">80 Hz</div>
                  <div class="fader-container" id="faderBassFreq" style="height:80px;">
                    <div class="fader-track"><div class="fader-fill" id="faderBassFreqFill" style="height:40%"></div></div>
                    <div class="fader-groove">
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                    </div>
                    <div class="fader-thumb" id="faderBassFreqThumb" style="bottom: 40%">
                      <div class="fader-thumb-line"></div>
                    </div>
                  </div>
                  <div class="fader-label" style="font-size:7px;">Crossover</div>
                </div>
                <div class="toggle-btn on" id="btnBassOn" onclick="toggleLimiter('bass')">ON</div>
              </div>
              
              <div class="limiter-card">
                <div class="limiter-title">LOUDNESS</div>
                <div class="limiter-meter"><div class="limiter-meter-fill" id="loudMeterFill" style="height:0%"></div></div>
                <div class="fader-control" data-fader="loudTarget" style="width: 48px;">
                  <div class="fader-value-display" id="valLoudTarget" style="font-size:9px; min-width: auto; padding: 1px 4px;">-16 LUFS</div>
                  <div class="fader-container" id="faderLoudTarget" style="height:80px;">
                    <div class="fader-track"><div class="fader-fill" id="faderLoudTargetFill" style="height:60%"></div></div>
                    <div class="fader-groove">
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                    </div>
                    <div class="fader-thumb" id="faderLoudTargetThumb" style="bottom: 60%">
                      <div class="fader-thumb-line"></div>
                    </div>
                  </div>
                  <div class="fader-label" style="font-size:7px;">Target</div>
                </div>
                <div class="fader-control" data-fader="loudCeiling" style="width: 48px;">
                  <div class="fader-value-display" id="valLoudCeiling" style="font-size:9px; min-width: auto; padding: 1px 4px;">-1 dBTP</div>
                  <div class="fader-container" id="faderLoudCeiling" style="height:80px;">
                    <div class="fader-track"><div class="fader-fill" id="faderLoudCeilingFill" style="height:85%"></div></div>
                    <div class="fader-groove">
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                      <div class="fader-groove-line"></div>
                    </div>
                    <div class="fader-thumb" id="faderLoudCeilingThumb" style="bottom: 85%">
                      <div class="fader-thumb-line"></div>
                    </div>
                  </div>
                  <div class="fader-label" style="font-size:7px;">Ceiling</div>
                </div>
                <div class="toggle-btn on" id="btnLoudOn" onclick="toggleLimiter('loud')">ON</div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- ROW 4: LOUDNESS METER -->
      <div class="module module-advanced" id="modLoudness">
        <div class="module-header">
          <div class="power-btn on" id="pwrLoudness" onclick="togglePower('Loudness')"></div>
          <span class="module-title">LOUDNESS METER</span>
          <div class="module-status">
            <span class="status-led on" id="statusLoudness"></span>
            <span class="truepeak-badge ok" id="truePeakBadge">TP: OK</span>
          </div>
        </div>
        <div class="module-body" style="flex-direction: column; align-items: stretch;">
          <div class="lufs-display">
            <div class="lufs-value" id="lufsValue">-23.0</div>
            <div class="lufs-bar">
              <div class="lufs-fill" id="lufsFill"></div>
              <div class="lufs-marker target" style="left: 50%"></div>
            </div>
            <div style="text-align: right; font-size: 9px;">
              <div style="color: var(--text-secondary);">Integrated</div>
              <div style="color: var(--accent-blue);" id="lufsMomentary">-∞ LUFS</div>
            </div>
          </div>
          <div class="lufs-labels">
            <span>-70 LUFS</span>
            <span>-23 LUFS</span>
            <span>0 LUFS</span>
          </div>
          <div style="display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; margin-top: 8px;">
            <div style="text-align: center;">
              <div style="font-size: 8px; color: var(--text-dim);">Short-term</div>
              <div style="font-size: 11px; color: var(--accent-blue);" id="lufsShort">-∞</div>
            </div>
            <div style="text-align: center;">
              <div style="font-size: 8px; color: var(--text-dim);">Momentary</div>
              <div style="font-size: 11px; color: var(--accent-blue);" id="lufsMoment">-∞</div>
            </div>
            <div style="text-align: center;">
              <div style="font-size: 8px; color: var(--text-dim);">LRA</div>
              <div style="font-size: 11px; color: var(--accent-blue);" id="lufsLRA">0</div>
            </div>
            <div style="text-align: center;">
              <div style="font-size: 8px; color: var(--text-dim);">True Peak</div>
              <div style="font-size: 11px; color: var(--accent-red);" id="truePeakVal">-∞</div>
            </div>
          </div>
        </div>
      </div>

      <!-- ROW 5: STEREO IMAGER -->
      <div class="module module-advanced" id="modImager">
        <div class="module-header">
          <div class="power-btn on" id="pwrImager" onclick="togglePower('Imager')"></div>
          <span class="module-title">STEREO IMAGER</span>
          <div class="module-status">
            <span class="status-led on" id="statusImager"></span>
            <span style="font-size:8px;color:var(--text-dim)">MID/SIDE</span>
          </div>
        </div>
        <div class="module-body">
          <div class="imager-controls">
            <div class="imager-band">
              <div class="imager-band-title">Low (&lt;200Hz)</div>
              <div class="fader-h" data-fader="imagerLow" style="width: 40px;">
                <div class="imager-value" id="valImagerLow" style="font-size:10px;">100%</div>
                <div class="fader-h-container" id="faderImagerLow" style="height:70px;">
                  <div class="fader-h-track"><div class="fader-h-fill" id="faderImagerLowFill" style="height:100%"></div></div>
                  <div class="fader-h-thumb" id="faderImagerLowThumb" style="bottom: 100%"></div>
                </div>
              </div>
            </div>
            <div class="imager-band">
              <div class="imager-band-title">Mid (200-2k)</div>
              <div class="fader-h" data-fader="imagerMid" style="width: 40px;">
                <div class="imager-value" id="valImagerMid" style="font-size:10px;">100%</div>
                <div class="fader-h-container" id="faderImagerMid" style="height:70px;">
                  <div class="fader-h-track"><div class="fader-h-fill" id="faderImagerMidFill" style="height:100%"></div></div>
                  <div class="fader-h-thumb" id="faderImagerMidThumb" style="bottom: 100%"></div>
                </div>
              </div>
            </div>
            <div class="imager-band">
              <div class="imager-band-title">High (&gt;2k)</div>
              <div class="fader-h" data-fader="imagerHigh" style="width: 40px;">
                <div class="imager-value" id="valImagerHigh" style="font-size:10px;">100%</div>
                <div class="fader-h-container" id="faderImagerHigh" style="height:70px;">
                  <div class="fader-h-track"><div class="fader-h-fill" id="faderImagerHighFill" style="height:100%"></div></div>
                  <div class="fader-h-thumb" id="faderImagerHighThumb" style="bottom: 100%"></div>
                </div>
              </div>
            </div>
          </div>
          <div style="display: flex; gap: 12px; margin-top: 12px; justify-content: center; flex-wrap: wrap;">
            <div style="text-align: center;">
              <div style="font-size: 8px; color: var(--text-dim); margin-bottom: 4px;">Correlation</div>
              <div style="width: 60px; height: 60px; border-radius: 50%; background: #0a0a10; border: 2px solid #333; position: relative; margin: 0 auto;">
                <canvas id="correlationCanvas" width="60" height="60"></canvas>
              </div>
              <div style="font-size: 9px; color: var(--accent-blue);" id="corrValue">+1.0</div>
            </div>
            <div style="display: flex; flex-direction: column; gap: 4px; justify-content: center;">
              <button class="toggle-btn on" id="btnMS" onclick="toggleMSMode()">M/S Monitor</button>
              <button class="toggle-btn" id="btnMonoCheck" onclick="toggleMonoCompat()">Mono Check</button>
            </div>
          </div>
        </div>
      </div>

      <!-- ROW 6: DE-ESSER -->
      <div class="module module-advanced" id="modDeesser">
        <div class="module-header">
          <div class="power-btn on" id="pwrDeesser" onclick="togglePower('Deesser')"></div>
          <span class="module-title">DE-ESSER</span>
          <div class="module-status">
            <span class="status-led on" id="statusDeesser"></span>
            <span style="font-size:8px;color:var(--text-dim)">AI DETECT</span>
          </div>
        </div>
        <div class="module-body">
          <div class="deesser-display">
            <div class="deesser-spectrum">
              <canvas id="deesserCanvas"></canvas>
              <div class="deesser-threshold" id="deesserThreshold" style="top: 30%"></div>
              <div class="deesser-freq-marker" id="deesserFreq" style="left: 60%"></div>
            </div>
            <div style="display: flex; gap: 12px;">
              <div class="fader-control" data-fader="deesserFreq" style="width: 48px;">
                <div class="fader-value-display" id="valDeesserFreq" style="font-size:9px; min-width: auto; padding: 1px 4px;">5.5 kHz</div>
                <div class="fader-container" id="faderDeesserFreq" style="height:80px;">
                  <div class="fader-track"><div class="fader-fill" id="faderDeesserFreqFill" style="height:55%"></div></div>
                  <div class="fader-groove">
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                  </div>
                  <div class="fader-thumb" id="faderDeesserFreqThumb" style="bottom: 55%">
                    <div class="fader-thumb-line"></div>
                  </div>
                </div>
                <div class="fader-label" style="font-size:7px;">Frequency</div>
              </div>
              <div class="fader-control" data-fader="deesserThresh" style="width: 48px;">
                <div class="fader-value-display" id="valDeesserThresh" style="font-size:9px; min-width: auto; padding: 1px 4px;">-24 dB</div>
                <div class="fader-container" id="faderDeesserThresh" style="height:80px;">
                  <div class="fader-track"><div class="fader-fill" id="faderDeesserThreshFill" style="height:36%"></div></div>
                  <div class="fader-groove">
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                  </div>
                  <div class="fader-thumb" id="faderDeesserThreshThumb" style="bottom: 36%">
                    <div class="fader-thumb-line"></div>
                  </div>
                </div>
                <div class="fader-label" style="font-size:7px;">Threshold</div>
              </div>
              <div class="fader-control" data-fader="deesserRatio" style="width: 48px;">
                <div class="fader-value-display" id="valDeesserRatio" style="font-size:9px; min-width: auto; padding: 1px 4px;">4:1</div>
                <div class="fader-container" id="faderDeesserRatio" style="height:80px;">
                  <div class="fader-track"><div class="fader-fill" id="faderDeesserRatioFill" style="height:40%"></div></div>
                  <div class="fader-groove">
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                    <div class="fader-groove-line"></div>
                  </div>
                  <div class="fader-thumb" id="faderDeesserRatioThumb" style="bottom: 40%">
                    <div class="fader-thumb-line"></div>
                  </div>
                </div>
                <div class="fader-label" style="font-size:7px;">Ratio</div>
              </div>
            </div>
          </div>
          <div style="display: flex; gap: 8px; margin-top: 8px; justify-content: center; flex-wrap: wrap;">
            <button class="toggle-btn on" id="btnDeesserWide" onclick="toggleDeesserMode('wide')">Wide Band</button>
            <button class="toggle-btn" id="btnDeesserSplit" onclick="toggleDeesserMode('split')">Split Band</button>
            <button class="toggle-btn" id="btnDeesserAuto" onclick="toggleAutoDeesser()">🤖 Auto</button>
          </div>
        </div>
      </div>

      <!-- ROW 7: OUTPUT -->
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
            <div class="fader-control" data-fader="outputGain">
              <div class="fader-value-display" id="valOutputGain">0.0 dB</div>
              <div class="fader-container" id="faderOutputGain">
                <div class="fader-track"><div class="fader-fill" id="faderOutputGainFill" style="height:50%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderOutputGainThumb" style="bottom: 50%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">+24</div>
                  <div class="fader-scale-mark">0</div>
                  <div class="fader-scale-mark">-24</div>
                </div>
              </div>
              <div class="fader-label">Output Gain</div>
            </div>
            
            <div class="fader-control" data-fader="outputCeiling">
              <div class="fader-value-display" id="valOutputCeiling">-0.1 dB</div>
              <div class="fader-container" id="faderOutputCeiling">
                <div class="fader-track"><div class="fader-fill" id="faderOutputCeilingFill" style="height:95%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderOutputCeilingThumb" style="bottom: 95%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">0.0</div>
                  <div class="fader-scale-mark">-1.0</div>
                  <div class="fader-scale-mark">-3.0</div>
                </div>
              </div>
              <div class="fader-label">Ceiling</div>
            </div>
            
            <div class="fader-control" data-fader="outputStereo">
              <div class="fader-value-display" id="valOutputStereo">100%</div>
              <div class="fader-container" id="faderOutputStereo">
                <div class="fader-track"><div class="fader-fill" id="faderOutputStereoFill" style="height:100%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderOutputStereoThumb" style="bottom: 100%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">100</div>
                  <div class="fader-scale-mark">50</div>
                  <div class="fader-scale-mark">0</div>
                </div>
              </div>
              <div class="fader-label">Stereo</div>
            </div>
            
            <div class="fader-control" data-fader="outputClipper">
              <div class="fader-value-display" id="valOutputClipper">OFF</div>
              <div class="fader-container" id="faderOutputClipper">
                <div class="fader-track"><div class="fader-fill" id="faderOutputClipperFill" style="height:0%"></div></div>
                <div class="fader-groove">
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                  <div class="fader-groove-line"></div>
                </div>
                <div class="fader-thumb" id="faderOutputClipperThumb" style="bottom: 0%">
                  <div class="fader-thumb-line"></div>
                </div>
                <div class="fader-scale">
                  <div class="fader-scale-mark">ON</div>
                  <div class="fader-scale-mark"></div>
                  <div class="fader-scale-mark">OFF</div>
                </div>
              </div>
              <div class="fader-label">Clipper</div>
            </div>
            
            <div style="display:flex;flex-direction:column;gap:4px;align-items:center; justify-content: center;">
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
      <div class="status-item"><span>SAMPLE RATE: 48000 Hz</span></div>
      <div class="status-item"><span>BUFFER: 256 samples</span></div>
      <div class="status-item"><span>LATENCY: 5.3 ms</span></div>
      <div class="status-item"><span>CPU: <span id="cpuUsage">12%</span></span></div>
      <div class="spacer"></div>
      <div class="status-item"><span id="clockDisplay">00:00:00</span></div>
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
        <div class="info-row"><span class="info-key">Engine</span><span class="info-val" id="infoEngine">Running</span></div>
        <div class="info-row"><span class="info-key">Input</span><span class="info-val" id="infoInput">Microphone</span></div>
        <div class="info-row"><span class="info-key">Output</span><span class="info-val" id="infoOutput">Default</span></div>
        <div class="info-row"><span class="info-key">Sample Rate</span><span class="info-val" id="infoSampleRate">48 kHz</span></div>
        <div class="info-row"><span class="info-key">Latency</span><span class="info-val" id="infoLatency">5.3 ms</span></div>
      </div>
      <div class="info-group">
        <div class="info-group-title">Processing</div>
        <div class="info-row"><span class="info-key">AGC</span><span class="info-val" id="infoAGC">ON</span></div>
        <div class="info-row"><span class="info-key">Comp Bands</span><span class="info-val">5 Active</span></div>
        <div class="info-row"><span class="info-key">EQ Bands</span><span class="info-val">6 Active</span></div>
        <div class="info-row"><span class="info-key">Limiters</span><span class="info-val">3 Active</span></div>
      </div>
      <div class="info-group">
        <div class="info-group-title">Metering</div>
        <div class="info-row"><span class="info-key">Peak L</span><span class="info-val" id="infoPeakL">-∞</span></div>
        <div class="info-row"><span class="info-key">Peak R</span><span class="info-val" id="infoPeakR">-∞</span></div>
        <div class="info-row"><span class="info-key">RMS L</span><span class="info-val" id="infoRMSL">-∞</span></div>
        <div class="info-row"><span class="info-key">RMS R</span><span class="info-val" id="infoRMSR">-∞</span></div>
      </div>
      <div class="info-group">
        <div class="info-group-title">Current Preset</div>
        <div class="info-row"><span class="info-key">Name</span><span class="info-val" id="infoPresetName">FM Loud</span></div>
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

<!-- A/B COMPARE OVERLAY -->
<div class="ab-compare" id="abCompare" style="display: none; position: absolute; top: 70px; right: 20px; z-index: 500;">
  <button class="ab-btn a active" onclick="selectAB('A')">A</button>
  <button class="ab-btn b" onclick="selectAB('B')">B</button>
  <button class="ab-toggle" onclick="toggleAB()">↹ Compare</button>
  <button class="ab-btn" onclick="copyAToB()">A→B</button>
  <button class="ab-btn" onclick="closeABCompare()">✕</button>
</div>

<!-- MACRO PANEL -->
<div class="macro-panel" id="macroPanel" style="display: none;">
  <div class="macro-header">
    <span class="macro-title">MACRO RECORDER</span>
    <div class="macro-controls">
      <button class="macro-btn record" id="macroRecordBtn" onclick="toggleMacroRecord()">● Rec</button>
      <button class="macro-btn play" id="macroPlayBtn" onclick="toggleMacroPlay()">▶ Play</button>
      <button class="macro-btn" onclick="clearMacro()">🗑 Clear</button>
      <button class="macro-btn" onclick="exportMacro()">💾 Export</button>
    </div>
  </div>
  <div class="macro-timeline" id="macroTimeline"></div>
  <div style="display: flex; justify-content: space-between; margin-top: 4px; font-size: 8px; color: var(--text-dim);">
    <span id="macroTime">00:00</span>
    <span id="macroParams">0 parameters</span>
  </div>
</div>

<!-- KEYBOARD SHORTCUTS -->
<div class="shortcuts-panel" id="shortcutsPanel">
  <div class="shortcuts-title">
    <span>KEYBOARD SHORTCUTS</span>
    <button class="shortcuts-close" onclick="toggleShortcuts()">✕</button>
  </div>
  <div class="shortcut-row"><span>Bypass All</span><span class="shortcut-key">Space</span></div>
  <div class="shortcut-row"><span>Reset All</span><span class="shortcut-key">F5</span></div>
  <div class="shortcut-row"><span>Save Preset</span><span class="shortcut-key">Ctrl+S</span></div>
  <div class="shortcut-row"><span>A/B Compare</span><span class="shortcut-key">Tab</span></div>
  <div class="shortcut-row"><span>Toggle M/S</span><span class="shortcut-key">M</span></div>
  <div class="shortcut-row"><span>Mono Check</span><span class="shortcut-key">Shift+M</span></div>
  <div class="shortcut-row"><span>Quick Solo Band</span><span class="shortcut-key">1-5</span></div>
  <div class="shortcut-row"><span>Network Sync</span><span class="shortcut-key">Ctrl+N</span></div>
  <div class="shortcut-row"><span>Close Panel</span><span class="shortcut-key">Esc</span></div>
</div>

<!-- FLOATING ANALYZER -->
<div class="floating-analyzer" id="floatingAnalyzer">
  <div class="analyzer-header">
    <span class="analyzer-title">REAL-TIME SPECTRAL ANALYZER</span>
    <button class="analyzer-close" onclick="toggleFloatingAnalyzer()">✕</button>
  </div>
  <div class="analyzer-body">
    <canvas id="analyzerCanvas" class="analyzer-canvas"></canvas>
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
// OmniaPro Broadcast Processor v11.2 - Faders
// ============================================

const knobState = {
  inputGain: 50, inputTrim: 50, inputPhase: 50,
  agcTarget: 50, agcSpeed: 50, agcRange: 60, agcAttack: 50, agcRelease: 40,
  eq1: 50, eq2: 50, eq3: 50, eq4: 50, eq5: 50, eq6: 50,
  peakThresh: 90, peakAttack: 20, peakRelease: 50,
  bassThresh: 80, bassFreq: 40,
  loudTarget: 60, loudCeiling: 85,
  outputGain: 50, outputCeiling: 95, outputStereo: 100, outputClipper: 0,
  imagerLow: 100, imagerMid: 100, imagerHigh: 100,
  deesserFreq: 55, deesserThresh: 36, deesserRatio: 40
};

const modules = {
  Input: true, AGC: true, Comp: true, EQ: true, Limiter: true, Output: true,
  Loudness: true, Imager: true, Deesser: true
};

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
let duckingGain = null;

const lufsMeter = {
  integrated: -23.0, shortTerm: -23.0, momentary: -Infinity,
  lra: 0, truePeak: -Infinity, history: [], target: -23
};

const imagerState = { low: 100, mid: 100, high: 100, msMode: false, monoCheck: false };
const deesserState = { freq: 5500, threshold: -24, ratio: 4, mode: 'wide', auto: false, active: false };
const abState = { active: false, current: 'A', presetA: null, presetB: null };
const duckingState = { enabled: false, threshold: -35, attack: 10, release: 200, reduction: -12 };
const macroState = { recording: false, playing: false, startTime: 0, keyframes: [], parameters: ['inputGain', 'agcTarget', 'eq3', 'outputGain'] };
const networkState = { connected: false, ws: null, roomId: null };
const dynEqState = { bands: { 1: { freq: 32, threshold: -30, active: false }, 3: { freq: 400, threshold: -24, active: false }, 5: { freq: 5000, threshold: -20, active: false } } };
const bandState = { solo: null, muted: [] };

const presets = {
  'fm-loud': { name: 'FM Loud', inputGain: 50, agcTarget: 50, agcSpeed: 50, eq: [2, 0, -1, 1, 2, 0], comp: [{thresh:-24,ratio:4,atk:10,rel:100,makeup:6},{thresh:-20,ratio:3,atk:8,rel:120,makeup:4},{thresh:-18,ratio:3.5,atk:5,rel:150,makeup:5},{thresh:-16,ratio:3,atk:3,rel:180,makeup:4},{thresh:-14,ratio:2.5,atk:2,rel:200,makeup:3}], peakThresh: 90, bassThresh: 80, loudTarget: 60, outputGain: 50 },
  'fm-clean': { name: 'FM Clean', inputGain: 50, agcTarget: 60, agcSpeed: 40, eq: [0, 0, 0, 0, 0, 0], comp: [{thresh:-30,ratio:2,atk:15,rel:150,makeup:3},{thresh:-25,ratio:2,atk:12,rel:180,makeup:2},{thresh:-22,ratio:2,atk:10,rel:200,makeup:2},{thresh:-20,ratio:1.5,atk:8,rel:220,makeup:1},{thresh:-18,ratio:1.5,atk:5,rel:250,makeup:1}], peakThresh: 85, bassThresh: 70, loudTarget: 52, outputGain: 50 },
  'fm-hot': { name: 'FM Hot', inputGain: 53, agcTarget: 30, agcSpeed: 70, eq: [3, 1, -2, 2, 4, 1], comp: [{thresh:-18,ratio:6,atk:5,rel:80,makeup:10},{thresh:-15,ratio:5,atk:4,rel:100,makeup:8},{thresh:-12,ratio:5,atk:3,rel:120,makeup:8},{thresh:-10,ratio:4,atk:2,rel:140,makeup:6},{thresh:-8,ratio:3,atk:1,rel:160,makeup:5}], peakThresh: 97, bassThresh: 95, loudTarget: 80, outputGain: 52 },
  'fm-classic': { name: 'FM Classic', inputGain: 50, agcTarget: 55, agcSpeed: 45, eq: [1, 0, 0, 0, -1, -2], comp: [{thresh:-28,ratio:3,atk:12,rel:130,makeup:4},{thresh:-24,ratio:2.5,atk:10,rel:150,makeup:3},{thresh:-20,ratio:2.5,atk:8,rel:180,makeup:3},{thresh:-18,ratio:2,atk:6,rel:200,makeup:2},{thresh:-16,ratio:1.5,atk:5,rel:220,makeup:1}], peakThresh: 90, bassThresh: 75, loudTarget: 55, outputGain: 50 },
  'fm-news': { name: 'FM News/Talk', inputGain: 52, agcTarget: 45, agcSpeed: 60, eq: [-1, 1, 3, 2, 1, -1], comp: [{thresh:-26,ratio:3,atk:8,rel:120,makeup:5},{thresh:-22,ratio:3,atk:6,rel:140,makeup:4},{thresh:-18,ratio:4,atk:4,rel:160,makeup:6},{thresh:-15,ratio:3.5,atk:3,rel:180,makeup:4},{thresh:-12,ratio:2,atk:2,rel:200,makeup:2}], peakThresh: 90, bassThresh: 70, loudTarget: 70, outputGain: 51 },
  'stream-clean': { name: 'Streaming Clean', inputGain: 50, agcTarget: 55, agcSpeed: 35, eq: [0, 0, 0, 0, 0, 0], comp: [{thresh:-32,ratio:2,atk:20,rel:200,makeup:2},{thresh:-28,ratio:1.5,atk:15,rel:220,makeup:1},{thresh:-24,ratio:1.5,atk:12,rel:250,makeup:1},{thresh:-20,ratio:1.5,atk:10,rel:280,makeup:1},{thresh:-16,ratio:1.2,atk:8,rel:300,makeup:0.5}], peakThresh: 80, bassThresh: 60, loudTarget: 60, outputGain: 49 },
  'stream-loud': { name: 'Streaming Loud', inputGain: 52, agcTarget: 35, agcSpeed: 55, eq: [1, 0, 0, 0, 1, 0], comp: [{thresh:-22,ratio:4,atk:5,rel:100,makeup:8},{thresh:-18,ratio:3.5,atk:4,rel:120,makeup:6},{thresh:-15,ratio:3.5,atk:3,rel:140,makeup:6},{thresh:-12,ratio:3,atk:2,rel:160,makeup:5},{thresh:-10,ratio:2.5,atk:1,rel:180,makeup:4}], peakThresh: 90, bassThresh: 90, loudTarget: 70, outputGain: 51 },
  'stream-podcast': { name: 'Podcast', inputGain: 53, agcTarget: 40, agcSpeed: 50, eq: [-2, 1, 3, 2, 0, -2], comp: [{thresh:-24,ratio:4,atk:8,rel:120,makeup:6},{thresh:-20,ratio:3,atk:6,rel:140,makeup:4},{thresh:-16,ratio:4,atk:4,rel:160,makeup:6},{thresh:-14,ratio:3,atk:3,rel:180,makeup:4},{thresh:-12,ratio:2,atk:2,rel:200,makeup:2}], peakThresh: 85, bassThresh: 70, loudTarget: 60, outputGain: 50 },
  'bass-boost': { name: 'Bass Boost', inputGain: 50, agcTarget: 50, agcSpeed: 50, eq: [6, 4, 2, 0, -1, 0], comp: [{thresh:-20,ratio:5,atk:8,rel:100,makeup:8},{thresh:-18,ratio:4,atk:6,rel:120,makeup:6},{thresh:-16,ratio:3,atk:5,rel:150,makeup:4},{thresh:-14,ratio:2.5,atk:3,rel:180,makeup:3},{thresh:-12,ratio:2,atk:2,rel:200,makeup:2}], peakThresh: 90, bassThresh: 90, loudTarget: 70, outputGain: 50 },
  'bright': { name: 'Bright & Airy', inputGain: 50, agcTarget: 50, agcSpeed: 50, eq: [-2, -1, 0, 1, 3, 5], comp: [{thresh:-28,ratio:2.5,atk:12,rel:150,makeup:3},{thresh:-24,ratio:2.5,atk:10,rel:170,makeup:3},{thresh:-20,ratio:2.5,atk:8,rel:190,makeup:3},{thresh:-18,ratio:2.5,atk:6,rel:210,makeup:3},{thresh:-16,ratio:2,atk:4,rel:230,makeup:2}], peakThresh: 85, bassThresh: 70, loudTarget: 55, outputGain: 50 },
  'warm': { name: 'Warm Analog', inputGain: 49, agcTarget: 55, agcSpeed: 45, eq: [2, 1, 0, -1, -2, -3], comp: [{thresh:-26,ratio:2,atk:15,rel:180,makeup:3},{thresh:-22,ratio:2,atk:12,rel:200,makeup:2},{thresh:-20,ratio:1.8,atk:10,rel:220,makeup:2},{thresh:-18,ratio:1.5,atk:8,rel:240,makeup:1},{thresh:-16,ratio:1.2,atk:6,rel:260,makeup:0.5}], peakThresh: 85, bassThresh: 70, loudTarget: 52, outputGain: 49 },
  'rock': { name: 'Rock/Pop', inputGain: 51, agcTarget: 45, agcSpeed: 55, eq: [3, 2, 1, 2, 3, 2], comp: [{thresh:-22,ratio:4,atk:6,rel:100,makeup:7},{thresh:-18,ratio:3.5,atk:5,rel:120,makeup:5},{thresh:-15,ratio:3.5,atk:4,rel:140,makeup:5},{thresh:-12,ratio:3,atk:3,rel:160,makeup:4},{thresh:-10,ratio:2.5,atk:2,rel:180,makeup:3}], peakThresh: 95, bassThresh: 90, loudTarget: 75, outputGain: 51 },
  'custom1': { name: 'User Preset 1', inputGain:50, agcTarget:50, agcSpeed:50, eq:[0,0,0,0,0,0], comp:[{thresh:-24,ratio:4,atk:10,rel:100,makeup:6},{thresh:-20,ratio:3,atk:8,rel:120,makeup:4},{thresh:-18,ratio:3.5,atk:5,rel:150,makeup:5},{thresh:-16,ratio:3,atk:3,rel:180,makeup:4},{thresh:-14,ratio:2.5,atk:2,rel:200,makeup:3}], peakThresh:90, bassThresh:80, loudTarget:60, outputGain:50 },
  'custom2': { name: 'User Preset 2', inputGain:50, agcTarget:50, agcSpeed:50, eq:[0,0,0,0,0,0], comp:[{thresh:-24,ratio:4,atk:10,rel:100,makeup:6},{thresh:-20,ratio:3,atk:8,rel:120,makeup:4},{thresh:-18,ratio:3.5,atk:5,rel:150,makeup:5},{thresh:-16,ratio:3,atk:3,rel:180,makeup:4},{thresh:-14,ratio:2.5,atk:2,rel:200,makeup:3}], peakThresh:90, bassThresh:80, loudTarget:60, outputGain:50 }
};

// ===== FADER INTERACTION =====
let activeFader = null;
let faderStartY = 0;
let faderStartValue = 0;

function initFaderListeners() {
  document.querySelectorAll('.fader-container, .fader-h-container, .band-fader-container').forEach(fader => {
    fader.addEventListener('mousedown', startFader);
    fader.addEventListener('touchstart', startFaderTouch, { passive: false });
  });
}

function startFader(e) {
  e.preventDefault();
  const faderEl = e.target.closest('.fader-control, .fader-h, .band-card .band-fader');
  if (!faderEl) return;
  
  const dataFader = faderEl.getAttribute('data-fader');
  if (!dataFader) return;
  
  activeFader = dataFader;
  faderStartY = e.clientY;
  faderStartValue = knobState[dataFader] !== undefined ? knobState[dataFader] : parseFloat(e.target.closest('.fader-value-display')?.textContent) || 0;
  
  document.addEventListener('mousemove', onFaderMove);
  document.addEventListener('mouseup', onFaderUp);
}

function startFaderTouch(e) {
  e.preventDefault();
  const faderEl = e.target.closest('.fader-control, .fader-h, .band-card .band-fader');
  if (!faderEl) return;
  
  const dataFader = faderEl.getAttribute('data-fader');
  if (!dataFader) return;
  
  activeFader = dataFader;
  faderStartY = e.touches[0].clientY;
  faderStartValue = knobState[dataFader] || 0;
  
  document.addEventListener('touchmove', onFaderMoveTouch, { passive: false });
  document.addEventListener('touchend', onFaderUpTouch);
}

function onFaderMove(e) {
  if (!activeFader) return;
  
  const delta = (faderStartY - e.clientY) * 0.8;
  let newValue = faderStartValue + delta;
  newValue = Math.max(0, Math.min(100, newValue));
  knobState[activeFader] = newValue;
  
  updateFaderDisplay(activeFader);
  applyKnobValue(activeFader, newValue);
}

function onFaderMoveTouch(e) {
  if (!activeFader || !e.touches.length) return;
  e.preventDefault();
  
  const delta = (faderStartY - e.touches[0].clientY) * 0.8;
  let newValue = faderStartValue + delta;
  newValue = Math.max(0, Math.min(100, newValue));
  knobState[activeFader] = newValue;
  
  updateFaderDisplay(activeFader);
  applyKnobValue(activeFader, newValue);
}

function onFaderUp() {
  activeFader = null;
  document.removeEventListener('mousemove', onFaderMove);
  document.removeEventListener('mouseup', onFaderUp);
}

function onFaderUpTouch() {
  activeFader = null;
  document.removeEventListener('touchmove', onFaderMoveTouch);
  document.removeEventListener('touchend', onFaderUpTouch);
}

function updateFaderDisplay(faderId) {
  const value = knobState[faderId];
  const pct = value;
  
  // Update fill height
  const fill = document.getElementById(`fader${capitalize(faderId)}Fill`);
  if (fill) fill.style.height = pct + '%';
  
  // Update thumb position
  const thumb = document.getElementById(`fader${capitalize(faderId)}Thumb`);
  if (thumb) thumb.style.bottom = pct + '%';
  
  // Update value display
  const valDisplay = document.getElementById(`val${capitalize(faderId)}`);
  if (valDisplay) {
    valDisplay.textContent = getFaderDisplayText(faderId, value);
  }
}

function updateBandFaderDisplay(band, param, value) {
  const fill = document.getElementById(`b${band}${capitalize(param)}Fill`);
  if (fill) fill.style.height = value + '%';
  
  const thumb = document.getElementById(`b${band}${capitalize(param)}Thumb`);
  if (thumb) thumb.style.bottom = value + '%';
  
  const valDisplay = document.getElementById(`b${band}${capitalize(param)}`);
  if (valDisplay) {
    valDisplay.textContent = getFaderDisplayText(`b${band}${capitalize(param)}`, value);
  }
}

function getFaderDisplayText(faderId, value) {
  switch(faderId) {
    case 'inputGain': case 'outputGain': return (value - 50).toFixed(1) + ' dB';
    case 'inputTrim': return (value - 50).toFixed(1) + ' dB';
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
    case 'outputCeiling': return ((value - 100) * 0.05).toFixed(1) + ' dB';
    case 'outputStereo': return Math.round(value) + '%';
    case 'outputClipper': return value > 50 ? 'ON' : 'OFF';
    case 'imagerLow': case 'imagerMid': case 'imagerHigh':
      return Math.round(value) + '%';
    case 'deesserFreq': return (value * 100 + 2000) + ' Hz';
    case 'deesserThresh': return Math.round((value - 50) * 1.2 - 30) + ' dB';
    case 'deesserRatio': return (value / 10).toFixed(1) + ':1';
    default: return Math.round(value);
  }
}

function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

function applyKnobValue(faderId, value) {
  if (!audioCtx) return;
  
  try {
    switch(faderId) {
      case 'inputGain':
        if (inputGainNode) inputGainNode.gain.value = dbToGain(value - 50);
        break;
      case 'agcTarget':
        if (agcGainNode) agcGainNode.gain.value = dbToGain((value - 50) * 0.6 - 20);
        break;
      case 'eq1': case 'eq2': case 'eq3': case 'eq4': case 'eq5': case 'eq6':
        const eqIdx = parseInt(faderId.replace('eq', '')) - 1;
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
    console.error('Apply fader error:', e);
  }
}

function dbToGain(db) {
  return Math.pow(10, db / 20);
}

function initAllFaderDisplays() {
  Object.keys(knobState).forEach(id => updateFaderDisplay(id));
}

// ===== AUDIO ENGINE =====
async function initAudio() {
  if (isAudioStarted) return;
  
  try {
    audioCtx = new (window.AudioContext || window.webkitAudioContext)({ sampleRate: 48000 });
    
    inputGainNode = audioCtx.createGain();
    agcGainNode = audioCtx.createGain();
    outputGainNode = audioCtx.createGain();
    
    inputAnalyser = audioCtx.createAnalyser(); inputAnalyser.fftSize = 2048;
    agcAnalyser = audioCtx.createAnalyser(); agcAnalyser.fftSize = 2048;
    compAnalyser = audioCtx.createAnalyser(); compAnalyser.fftSize = 2048;
    eqAnalyser = audioCtx.createAnalyser(); eqAnalyser.fftSize = 2048;
    limiterAnalyser = audioCtx.createAnalyser(); limiterAnalyser.fftSize = 2048;
    outputAnalyser = audioCtx.createAnalyser(); outputAnalyser.fftSize = 2048;
    masterAnalyser = audioCtx.createAnalyser(); masterAnalyser.fftSize = 4096;
    
    const eqFreqs = [32, 100, 400, 1500, 5000, 12000];
    eqFilters = eqFreqs.map((freq, i) => {
      const filter = audioCtx.createBiquadFilter();
      filter.type = i === 0 ? 'lowshelf' : (i === eqFreqs.length - 1 ? 'highshelf' : 'peaking');
      filter.frequency.value = freq;
      filter.gain.value = 0;
      filter.Q.value = 1.4;
      return filter;
    });
    
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
    
    compFilters = [];
    for (let i = 0; i < 4; i++) {
      const lp = audioCtx.createBiquadFilter();
      lp.type = 'lowpass'; lp.frequency.value = compFreqs[i]; lp.Q.value = 0.7;
      const hp = audioCtx.createBiquadFilter();
      hp.type = 'highpass'; hp.frequency.value = compFreqs[i]; hp.Q.value = 0.7;
      compFilters.push({ lp, hp });
    }
    
    peakLimiter = audioCtx.createDynamicsCompressor();
    peakLimiter.threshold.value = -0.3; peakLimiter.ratio.value = 20;
    peakLimiter.attack.value = 0.001; peakLimiter.release.value = 0.05; peakLimiter.knee.value = 0;
    
    bassLimiter = audioCtx.createDynamicsCompressor();
    bassLimiter.threshold.value = -3; bassLimiter.ratio.value = 20;
    bassLimiter.attack.value = 0.005; bassLimiter.release.value = 0.1; bassLimiter.knee.value = 0;
    
    loudnessLimiter = audioCtx.createDynamicsCompressor();
    loudnessLimiter.threshold.value = -16; loudnessLimiter.ratio.value = 20;
    loudnessLimiter.attack.value = 0.01; loudnessLimiter.release.value = 0.1; loudnessLimiter.knee.value = 0;
    
    initDucking();
    await setupInputSource('mic');
    
    isAudioStarted = true;
    document.getElementById('infoEngine').textContent = 'Running';
    document.getElementById('infoSampleRate').textContent = (audioCtx.sampleRate / 1000) + ' kHz';
    
    startMetering();
    startAdvancedLoop();
    updateClock();
    
  } catch (e) {
    console.error('Audio init error:', e);
  }
}

async function setupInputSource(type) {
  if (inputSource) { try { inputSource.disconnect(); } catch(e) {} }
  if (oscillatorNode) { try { oscillatorNode.stop(); } catch(e) {} oscillatorNode = null; }
  if (noiseNode) { try { noiseNode.stop(); } catch(e) {} noiseNode = null; }
  
  currentInputType = type;
  
  switch(type) {
    case 'mic':
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        inputSource = audioCtx.createMediaStreamSource(stream);
        document.getElementById('infoInput').textContent = 'Microphone';
      } catch(e) {
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
      const input = document.createElement('input');
      input.type = 'file'; input.accept = 'audio/*';
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
              source.buffer = buffer; source.loop = true;
              inputSource = source; source.start();
              buildAudioChain();
              document.getElementById('infoInput').textContent = file.name;
            });
          };
          reader.readAsArrayBuffer(file);
        }
      };
      input.click();
      return;
  }
  buildAudioChain();
}

function createTestSignal() {
  oscillatorNode = audioCtx.createOscillator();
  oscillatorNode.type = 'sine'; oscillatorNode.frequency.value = 1000;
  const oscGain = audioCtx.createGain(); oscGain.gain.value = 0.3;
  oscillatorNode.connect(oscGain); inputSource = oscGain;
  oscillatorNode.start();
}

function createNoiseSignal() {
  const bufferSize = audioCtx.sampleRate * 2;
  const buffer = audioCtx.createBuffer(2, bufferSize, audioCtx.sampleRate);
  for (let ch = 0; ch < 2; ch++) {
    const data = buffer.getChannelData(ch);
    for (let i = 0; i < bufferSize; i++) data[i] = (Math.random() * 2 - 1) * 0.15;
  }
  noiseNode = audioCtx.createBufferSource(); noiseNode.buffer = buffer; noiseNode.loop = true;
  noiseGain = audioCtx.createGain(); noiseGain.gain.value = 0.3;
  noiseNode.connect(noiseGain); inputSource = noiseGain;
  noiseNode.start();
}

function buildAudioChain() {
  try {
    if (inputSource) inputSource.disconnect();
    const chainStart = inputSource;
    chainStart.connect(inputGainNode);
    inputGainNode.connect(inputAnalyser);
    inputAnalyser.connect(agcGainNode);
    agcGainNode.connect(agcAnalyser);
    
    const compInput = audioCtx.createGain();
    agcAnalyser.connect(compInput);
    
    const compOutputs = [];
    for (let i = 0; i < 5; i++) {
      const bandOutput = audioCtx.createGain();
      if (i === 0) {
        compInput.connect(compFilters[0].lp);
        compFilters[0].lp.connect(compBands[i]);
        compBands[i].connect(bandOutput);
      } else if (i === 4) {
        compInput.connect(compFilters[3].hp);
        compFilters[3].hp.connect(compBands[i]);
        compBands[i].connect(bandOutput);
      } else {
        compInput.connect(compFilters[i-1].hp);
        compFilters[i-1].hp.connect(compFilters[i].lp);
        compFilters[i].lp.connect(compBands[i]);
        compBands[i].connect(bandOutput);
      }
      bandOutput.connect(compAnalyser);
      compOutputs.push(bandOutput);
    }
    
    const compSum = audioCtx.createGain();
    compOutputs.forEach(o => { try { o.disconnect(); } catch(e) {} o.connect(compSum); });
    
    compSum.connect(eqAnalyser);
    eqFilters.forEach((filter, i) => {
      if (i === 0) eqAnalyser.connect(filter);
      else eqFilters[i-1].connect(filter);
    });
    
    const lastEQ = eqFilters[eqFilters.length - 1];
    lastEQ.connect(limiterAnalyser);
    limiterAnalyser.connect(peakLimiter);
    peakLimiter.connect(bassLimiter);
    bassLimiter.connect(loudnessLimiter);
    loudnessLimiter.connect(outputAnalyser);
    
    outputAnalyser.connect(outputGainNode);
    outputGainNode.connect(masterAnalyser);
    masterAnalyser.connect(audioCtx.destination);
    
    document.getElementById('infoEngine').textContent = 'Running';
  } catch(e) { console.error('Chain build error:', e); }
}

function initDucking() {
  if (!audioCtx) return;
  duckingGain = audioCtx.createGain();
  duckingGain.gain.value = 1;
}

// ===== METERING =====
function startMetering() { setInterval(updateMeters, 50); }

function updateMeters() {
  if (!isAudioStarted || !inputAnalyser) return;
  
  const inputTime = new Float32Array(inputAnalyser.fftSize); inputAnalyser.getFloatTimeDomainData(inputTime);
  const agcTime = new Float32Array(agcAnalyser.fftSize); agcAnalyser.getFloatTimeDomainData(agcTime);
  const compTime = new Float32Array(compAnalyser.fftSize); compAnalyser.getFloatTimeDomainData(compTime);
  const eqTime = new Float32Array(eqAnalyser.fftSize); eqAnalyser.getFloatTimeDomainData(eqTime);
  const limTime = new Float32Array(limiterAnalyser.fftSize); limiterAnalyser.getFloatTimeDomainData(limTime);
  const outTime = new Float32Array(outputAnalyser.fftSize); outputAnalyser.getFloatTimeDomainData(outTime);
  
  const levels = { input: calcRMS(inputTime), agc: calcRMS(agcTime), comp: calcRMS(compTime), eq: calcRMS(eqTime), lim: calcRMS(limTime), out: calcRMS(outTime) };
  const dbLevels = {};
  for (const [key, val] of Object.entries(levels)) dbLevels[key] = val > 0 ? 20 * Math.log10(val) : -Infinity;
  
  updateMeterPair('Input', dbLevels.input, dbLevels.input);
  updateMeterPair('AGC', dbLevels.agc, dbLevels.agc);
  updateMeterPair('Comp', dbLevels.comp, dbLevels.comp);
  updateMeterPair('EQ', dbLevels.eq, dbLevels.eq);
  updateMeterPair('Lim', dbLevels.lim, dbLevels.lim);
  updateMeterPair('Out', dbLevels.out, dbLevels.out);
  
  document.getElementById('infoPeakL').textContent = dbLevels.out > -100 ? dbLevels.out.toFixed(1) + ' dB' : '-∞';
  document.getElementById('infoPeakR').textContent = dbLevels.out > -100 ? dbLevels.out.toFixed(1) + ' dB' : '-∞';
  document.getElementById('infoRMSL').textContent = dbLevels.out > -100 ? dbLevels.out.toFixed(1) + ' dB' : '-∞';
  document.getElementById('infoRMSR').textContent = dbLevels.out > -100 ? dbLevels.out.toFixed(1) + ' dB' : '-∞';
  
  const outPct = Math.max(0, Math.min(100, (dbLevels.out + 60) / 60 * 100));
  document.getElementById('outputLevelFill').style.width = outPct + '%';
  document.getElementById('cpuUsage').textContent = (8 + Math.random() * 15).toFixed(0) + '%';
}

function calcRMS(data) { let sum = 0; for (let i = 0; i < data.length; i++) sum += data[i] * data[i]; return Math.sqrt(sum / data.length); }

function updateMeterPair(name, lDb, rDb) {
  const lPct = dbToPercent(lDb); const rPct = dbToPercent(rDb);
  const lFill = document.getElementById('meter' + name + 'LFill');
  if (lFill) { lFill.style.height = lPct + '%'; lFill.style.background = getMeterGradient(lPct); }
  const rFill = document.getElementById('meter' + name + 'RFill');
  if (rFill) { rFill.style.height = rPct + '%'; rFill.style.background = getMeterGradient(rPct); }
  const lVal = document.getElementById('meter' + name + 'LVal');
  if (lVal) lVal.innerHTML = lDb > -100 ? '<span class="meter-db-value">' + lDb.toFixed(1) + ' dB</span>' : '-∞ dB';
  const rVal = document.getElementById('meter' + name + 'RVal');
  if (rVal) rVal.innerHTML = rDb > -100 ? '<span class="meter-db-value">' + rDb.toFixed(1) + ' dB</span>' : '-∞ dB';
}

function dbToPercent(db) { if (db <= -60) return 0; if (db >= 0) return 100; return ((db + 60) / 60) * 100; }
function getMeterGradient(pct) { if (pct < 60) return '#00cc44'; if (pct < 80) return '#88cc00'; if (pct < 90) return '#ffcc00'; if (pct < 95) return '#ff8800'; return '#ff3344'; }

// ===== LUFS METERING =====
function updateLUFSMeter() {
  if (!isAudioStarted || !masterAnalyser) return;
  
  const timeData = new Float32Array(masterAnalyser.fftSize);
  masterAnalyser.getFloatTimeDomainData(timeData);
  
  let sum = 0;
  for (let i = 0; i < timeData.length; i++) sum += timeData[i] * timeData[i];
  const rms = Math.sqrt(sum / timeData.length);
  const lufs = rms > 0 ? 20 * Math.log10(rms) + 0.691 : -Infinity;
  
  lufsMeter.momentary = lufsMeter.momentary * 0.9 + lufs * 0.1;
  lufsMeter.shortTerm = lufsMeter.shortTerm * 0.95 + lufs * 0.05;
  lufsMeter.history.push(lufs);
  if (lufsMeter.history.length > 600) lufsMeter.history.shift();
  lufsMeter.integrated = lufsMeter.history.reduce((a,b) => a+b, 0) / lufsMeter.history.length;
  
  const maxSample = Math.max(...timeData.map(Math.abs));
  const tp = 20 * Math.log10(maxSample * 1.05);
  lufsMeter.truePeak = Math.max(lufsMeter.truePeak * 0.99, tp);
  
  const sorted = [...lufsMeter.history].sort((a,b) => a-b);
  const p10 = sorted[Math.floor(sorted.length * 0.1)] || -70;
  const p95 = sorted[Math.floor(sorted.length * 0.95)] || 0;
  lufsMeter.lra = Math.max(0, p95 - p10);
  
  document.getElementById('lufsValue').textContent = lufsMeter.integrated.toFixed(1);
  document.getElementById('lufsValue').className = 'lufs-value' + (Math.abs(lufsMeter.integrated - lufsMeter.target) > 2 ? ' warning' : '');
  
  const fillPct = Math.max(0, Math.min(100, (lufsMeter.integrated + 70) / 70 * 100));
  document.getElementById('lufsFill').style.width = fillPct + '%';
  
  document.getElementById('lufsMomentary').textContent = lufsMeter.momentary.toFixed(1) + ' LUFS';
  document.getElementById('lufsShort').textContent = lufsMeter.shortTerm.toFixed(1);
  document.getElementById('lufsLRA').textContent = lufsMeter.lra.toFixed(1);
  document.getElementById('truePeakVal').textContent = lufsMeter.truePeak.toFixed(1) + ' dBTP';
  
  const tpBadge = document.getElementById('truePeakBadge');
  if (lufsMeter.truePeak > -1.0) { tpBadge.textContent = 'TP: CLIP!'; tpBadge.className = 'truepeak-badge'; }
  else { tpBadge.textContent = 'TP: OK'; tpBadge.className = 'truepeak-badge ok'; }
}

// ===== CORRELATION METER =====
function drawCorrelationMeter() {
  const canvas = document.getElementById('correlationCanvas');
  if (!canvas || !masterAnalyser) return;
  const ctx = canvas.getContext('2d');
  const w = canvas.width, h = canvas.height;
  ctx.fillStyle = '#0a0a10'; ctx.fillRect(0, 0, w, h);
  
  const timeL = new Float32Array(masterAnalyser.fftSize);
  const timeR = new Float32Array(masterAnalyser.fftSize);
  masterAnalyser.getFloatTimeDomainData(timeL);
  for(let i=0; i<timeR.length; i++) timeR[i] = timeL[i] * (0.9 + Math.random()*0.2);
  
  let sumLL = 0, sumRR = 0, sumLR = 0;
  for(let i = 0; i < 1024; i++) { sumLL += timeL[i]*timeL[i]; sumRR += timeR[i]*timeR[i]; sumLR += timeL[i]*timeR[i]; }
  const corr = sumLR / Math.sqrt(sumLL * sumRR + 0.0001);
  
  const angle = (corr + 1) / 2 * Math.PI - Math.PI/2;
  const radius = 25;
  ctx.strokeStyle = corr > 0 ? '#00cc44' : '#ff4444'; ctx.lineWidth = 2;
  ctx.beginPath(); ctx.moveTo(w/2, h/2);
  ctx.lineTo(w/2 + Math.cos(angle) * radius, h/2 + Math.sin(angle) * radius);
  ctx.stroke();
  ctx.strokeStyle = 'rgba(255,255,255,0.1)';
  ctx.beginPath(); ctx.arc(w/2, h/2, radius, 0, Math.PI*2); ctx.stroke();
  
  document.getElementById('corrValue').textContent = corr.toFixed(2);
  document.getElementById('corrValue').style.color = corr > 0.7 ? '#00cc44' : corr > 0 ? '#ffcc00' : '#ff4444';
}

// ===== DE-ESSER =====
function drawDeesserSpectrum() {
  const canvas = document.getElementById('deesserCanvas');
  if (!canvas || !masterAnalyser) return;
  const ctx = canvas.getContext('2d');
  const w = canvas.parentElement.clientWidth;
  const h = canvas.parentElement.clientHeight;
  if (canvas.width !== w) canvas.width = w;
  if (canvas.height !== h) canvas.height = h;
  
  const bufferLength = masterAnalyser.frequencyBinCount;
  const dataArray = new Uint8Array(bufferLength);
  masterAnalyser.getByteFrequencyData(dataArray);
  ctx.fillStyle = '#0a0a10'; ctx.fillRect(0, 0, w, h);
  
  const barWidth = w / bufferLength * 3;
  for (let i = 0; i < bufferLength; i++) {
    const freq = i * audioCtx.sampleRate / (bufferLength * 2);
    if (freq > 10000) break;
    const barHeight = (dataArray[i] / 255) * h;
    const x = (Math.log10(freq) - Math.log10(20)) / (Math.log10(10000) - Math.log10(20)) * w;
    const inBand = freq > deesserState.freq * 0.7 && freq < deesserState.freq * 1.4;
    ctx.fillStyle = inBand && deesserState.active ? '#ff4444' : '#00aaff';
    ctx.fillRect(x, h - barHeight, barWidth - 1, barHeight);
  }
  
  const threshY = h - ((deesserState.threshold + 60) / 60 * h);
  ctx.strokeStyle = 'rgba(255,68,68,0.5)'; ctx.setLineDash([4, 2]);
  ctx.beginPath(); ctx.moveTo(0, threshY); ctx.lineTo(w, threshY); ctx.stroke();
  ctx.setLineDash([]);
}

function detectSibilance() {
  if (!masterAnalyser) return false;
  const freqData = new Uint8Array(masterAnalyser.frequencyBinCount);
  masterAnalyser.getByteFrequencyData(freqData);
  const startIdx = Math.floor(4000 / (audioCtx.sampleRate/2) * freqData.length);
  const endIdx = Math.floor(8000 / (audioCtx.sampleRate/2) * freqData.length);
  let sum = 0;
  for (let i = startIdx; i < endIdx; i++) sum += freqData[i];
  const avg = sum / (endIdx - startIdx);
  const detected = avg > (128 + deesserState.threshold + 60);
  deesserState.active = detected;
  return detected;
}

// ===== ADVANCED LOOP =====
function startAdvancedLoop() {
  function animate() {
    drawSpectrum();
    drawPhase();
    drawEQ();
    drawCorrelationMeter();
    drawDeesserSpectrum();
    
    updateLUFSMeter();
    updateDucking();
    updateDynamicEQ();
    
    if (macroState.recording && isAudioStarted) captureKeyframe();
    if (modules.Deesser) detectSibilance();
    
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
  ctx.fillStyle = '#0a0a10'; ctx.fillRect(0, 0, width, height);
  
  const barWidth = (width / bufferLength) * 2.5;
  let x = 0;
  for (let i = 0; i < bufferLength; i++) {
    const barHeight = (dataArray[i] / 255) * height;
    const hue = 120 - (dataArray[i] / 255) * 120;
    ctx.fillStyle = `hsl(${hue}, 100%, ${40 + (dataArray[i] / 255) * 20}%)`;
    ctx.fillRect(x, height - barHeight, barWidth - 1, barHeight);
    x += barWidth;
    if (x > width) break;
  }
}

function drawPhase() {
  const canvas = document.getElementById('phaseCanvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  const w = canvas.width, h = canvas.height;
  ctx.fillStyle = '#0a0a10'; ctx.fillRect(0, 0, w, h);
  
  if (inputAnalyser && isAudioStarted) {
    const timeL = new Float32Array(inputAnalyser.fftSize);
    const timeR = new Float32Array(inputAnalyser.fftSize);
    inputAnalyser.getFloatTimeDomainData(timeL);
    for (let i = 0; i < timeR.length; i++) timeR[i] = timeL[i] * (0.8 + Math.sin(i * 0.01) * 0.2);
    
    ctx.strokeStyle = '#00aaff'; ctx.lineWidth = 1;
    ctx.beginPath();
    const step = Math.floor(timeL.length / 200);
    for (let i = 0; i < timeL.length; i += step) {
      const x = ((timeL[i] + 1) / 2) * w;
      const y = ((timeR[i] + 1) / 2) * h;
      if (i === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);
    }
    ctx.stroke();
  }
  ctx.strokeStyle = 'rgba(255,255,255,0.1)'; ctx.lineWidth = 1;
  ctx.beginPath(); ctx.moveTo(w/2, 0); ctx.lineTo(w/2, h);
  ctx.moveTo(0, h/2); ctx.lineTo(w, h/2); ctx.stroke();
}

function drawEQ() {
  const canvas = document.getElementById('eqCanvas');
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  const w = canvas.width, h = canvas.height;
  if (canvas.width !== canvas.parentElement.clientWidth) {
    canvas.width = canvas.parentElement.clientWidth;
    canvas.height = canvas.parentElement.clientHeight;
  }
  
  ctx.fillStyle = '#0a0a10'; ctx.fillRect(0, 0, w, h);
  const eqValues = [knobState.eq1, knobState.eq2, knobState.eq3, knobState.eq4, knobState.eq5, knobState.eq6];
  
  ctx.strokeStyle = 'rgba(255,255,255,0.05)'; ctx.lineWidth = 1;
  for (let y = 0; y < h; y += h/6) { ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(w, y); ctx.stroke(); }
  for (let x = 0; x < w; x += w/6) { ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, h); ctx.stroke(); }
  ctx.strokeStyle = 'rgba(255,255,255,0.15)'; ctx.beginPath(); ctx.moveTo(0, h/2); ctx.lineTo(w, h/2); ctx.stroke();
  
  ctx.strokeStyle = '#00aaff'; ctx.lineWidth = 2; ctx.shadowColor = '#00aaff'; ctx.shadowBlur = 4;
  ctx.beginPath();
  const freqs = [32, 100, 400, 1500, 5000, 12000];
  for (let x = 0; x < w; x++) {
    const freq = 20 * Math.pow(1000, x / w);
    let gain = 0;
    for (let i = 0; i < freqs.length; i++) {
      const dbVal = (eqValues[i] - 50) / 50 * 12;
      const distance = Math.abs(Math.log2(freq / freqs[i]));
      gain += dbVal * Math.exp(-distance * distance / 8);
    }
    const y = h/2 - (gain / 12) * (h/2);
    if (x === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);
  }
  ctx.stroke(); ctx.shadowBlur = 0;
  ctx.lineTo(w, h); ctx.lineTo(0, h); ctx.closePath();
  const gradient = ctx.createLinearGradient(0, 0, 0, h);
  gradient.addColorStop(0, 'rgba(0,170,255,0.1)'); gradient.addColorStop(0.5, 'rgba(0,170,255,0.02)'); gradient.addColorStop(1, 'rgba(0,170,255,0.05)');
  ctx.fillStyle = gradient; ctx.fill();
}

// ===== TOGGLES =====
function togglePower(moduleName) {
  const btn = document.getElementById('pwr' + moduleName);
  const status = document.getElementById('status' + moduleName);
  const mod = document.getElementById('mod' + moduleName);
  modules[moduleName] = !modules[moduleName];
  if (modules[moduleName]) {
    btn.classList.add('on'); status.className = 'status-led on';
    if (mod) mod.classList.add('active-module');
  } else {
    btn.classList.remove('on'); status.className = 'status-led';
    if (mod) mod.classList.remove('active-module');
  }
  updateInfoPanel();
}

function toggleMono() { document.getElementById('btnInputMono').classList.toggle('on'); }
function toggleMute() {
  document.getElementById('btnInputMute').classList.toggle('on');
  if (inputGainNode) {
    const isMuted = document.getElementById('btnInputMute').classList.contains('on');
    inputGainNode.gain.value = isMuted ? 0 : dbToGain(knobState.inputGain - 50);
  }
}
function togglePhaseInv() { document.getElementById('btnInputPhaseInv').classList.toggle('on'); }
function toggleAgc() { document.getElementById('btnAgcOn').classList.toggle('on'); }
function toggleAgcAuto() { document.getElementById('btnAgcAuto').classList.toggle('on'); }
function toggleLimiter(type) {
  const btnId = 'btn' + type.charAt(0).toUpperCase() + type.slice(1) + 'On';
  document.getElementById(btnId).classList.toggle('on');
}
function toggleDither() { document.getElementById('btnOutDither').classList.toggle('on'); }
function toggleOutputClip() { document.getElementById('btnOutClip').classList.toggle('on'); }
function toggleMSMode() {
  imagerState.msMode = !imagerState.msMode;
  document.getElementById('btnMS').classList.toggle('on', imagerState.msMode);
}
function toggleMonoCompat() {
  imagerState.monoCheck = !imagerState.monoCheck;
  document.getElementById('btnMonoCheck').classList.toggle('on', imagerState.monoCheck);
}
function toggleDeesserMode(mode) {
  deesserState.mode = mode;
  document.getElementById('btnDeesserWide').classList.toggle('on', mode === 'wide');
  document.getElementById('btnDeesserSplit').classList.toggle('on', mode === 'split');
}
function toggleAutoDeesser() {
  deesserState.auto = !deesserState.auto;
  document.getElementById('btnDeesserAuto').classList.toggle('on', deesserState.auto);
}

// ===== COMPRESSOR BAND PARAMS =====
function updateBandParam(band, param, value) {
  const val = parseFloat(value);
  const display = document.getElementById('b' + band + capitalize(param));
  if (display) display.textContent = param === 'ratio' ? val + ':1' : val;
  
  if (compBands[band - 1]) {
    switch(param) {
      case 'threshold': compBands[band-1].threshold.value = val; break;
      case 'ratio': compBands[band-1].ratio.value = val; break;
      case 'attack': compBands[band-1].attack.value = val / 1000; break;
      case 'release': compBands[band-1].release.value = val / 1000; break;
    }
  }
}

// ===== PRESET SYSTEM =====
function loadPreset(presetId) {
  const preset = presets[presetId];
  if (!preset) return;
  
  document.querySelectorAll('.tree-item').forEach(el => el.classList.remove('active'));
  if (event && event.target) event.target.classList.add('active');
  
  knobState.inputGain = preset.inputGain !== undefined ? preset.inputGain : 50;
  knobState.agcTarget = preset.agcTarget !== undefined ? preset.agcTarget : 50;
  
  if (preset.eq) {
    preset.eq.forEach((val, i) => { knobState['eq' + (i+1)] = 50 + (val / 12) * 50; });
  }
  
  initAllFaderDisplays();
  applyAllFaderValues();
  
  document.getElementById('infoPresetName').textContent = preset.name;
}

function applyAllFaderValues() {
  Object.keys(knobState).forEach(id => applyKnobValue(id, knobState[id]));
}

function savePreset() {
  const name = prompt('Enter preset name:', 'My Preset');
  if (!name) return;
  const preset = { name, inputGain: knobState.inputGain, agcTarget: knobState.agcTarget, eq: [], comp: [], peakThresh: knobState.peakThresh, bassThresh: knobState.bassThresh, loudTarget: knobState.loudTarget, outputGain: knobState.outputGain };
  for (let i = 1; i <= 6; i++) preset.eq.push(Math.round((knobState['eq' + i] - 50) / 50 * 12));
  const customNum = Object.keys(presets).filter(k => k.startsWith('custom')).length + 1;
  presets['custom' + customNum] = preset;
  alert('Preset "' + name + '" saved!');
}

function exportPreset() {
  const data = JSON.stringify(presets, null, 2);
  const blob = new Blob([data], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = 'omniapro-presets.json'; a.click();
  URL.revokeObjectURL(url);
}

function importPreset() {
  const input = document.createElement('input'); input.type = 'file'; input.accept = '.json';
  input.onchange = (e) => {
    const reader = new FileReader();
    reader.onload = (ev) => {
      try { Object.assign(presets, JSON.parse(ev.target.result)); alert('Presets imported!'); }
      catch(err) { alert('Error: ' + err.message); }
    };
    reader.readAsText(e.target.files[0]);
  };
  input.click();
}

// ===== INPUT SOURCE =====
function setInputSource(type) {
  document.querySelectorAll('.toolbar-btn').forEach(b => b.classList.remove('active'));
  const btnId = 'btn' + type.charAt(0).toUpperCase() + type.slice(1);
  const btn = document.getElementById(btnId);
  if (btn) btn.classList.add('active');
  
  if (type === 'file') {
    const input = document.createElement('input'); input.type = 'file'; input.accept = 'audio/*';
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
            source.buffer = buffer; source.loop = true;
            inputSource = source; source.start();
            buildAudioChain();
            document.getElementById('infoInput').textContent = file.name;
          });
        };
        reader.readAsArrayBuffer(file);
      }
    };
    input.click();
    return;
  }
  
  if (isAudioStarted) setupInputSource(type);
}

async function changeInputDevice(deviceId) {
  if (!audioCtx || !deviceId) return;
  if (currentInputType === 'mic') {
    const stream = await navigator.mediaDevices.getUserMedia({ audio: { deviceId: deviceId ? { exact: deviceId } : undefined } });
    if (inputSource) try { inputSource.disconnect(); } catch(e) {}
    inputSource = audioCtx.createMediaStreamSource(stream);
    buildAudioChain();
  }
}

async function changeOutputDevice(deviceId) {
  if (!audioCtx || !deviceId) return;
  try { if (audioCtx.setSinkId) await audioCtx.setSinkId(deviceId); } catch(e) { console.warn('setSinkId error:', e); }
}

// ===== PANELS & UI =====
function togglePanel(side) {
  if (side === 'left') document.getElementById('leftPanel').classList.toggle('collapsed');
  else if (side === 'right') document.getElementById('rightPanel').classList.toggle('collapsed');
}

function startResize(event, side) {
  const panel = document.getElementById(side === 'left' ? 'leftPanel' : 'rightPanel');
  const startX = event.clientX;
  const startWidth = panel.offsetWidth;
  
  function onMove(e) {
    const delta = side === 'left' ? e.clientX - startX : startX - e.clientX;
    const newWidth = Math.max(150, Math.min(400, startWidth + delta));
    panel.style.width = newWidth + 'px'; panel.style.minWidth = newWidth + 'px';
  }
  function onUp() { document.removeEventListener('mousemove', onMove); document.removeEventListener('mouseup', onUp); }
  document.addEventListener('mousemove', onMove);
  document.addEventListener('mouseup', onUp);
}

function toggleCategory(el) {
  el.classList.toggle('open');
  const items = el.nextElementSibling;
  if (items) items.classList.toggle('visible');
}

// ===== A/B COMPARE =====
function toggleABCompare() {
  const panel = document.getElementById('abCompare');
  abState.active = !abState.active;
  panel.style.display = abState.active ? 'flex' : 'none';
  if (abState.active && !abState.presetA) abState.presetA = JSON.parse(JSON.stringify(knobState));
}
function selectAB(side) {
  abState.current = side;
  document.querySelectorAll('.ab-btn').forEach(b => b.classList.remove('active'));
  document.querySelector(`.ab-btn.${side.toLowerCase()}`).classList.add('active');
  const preset = side === 'A' ? abState.presetA : abState.presetB;
  if (preset) { Object.assign(knobState, preset); initAllFaderDisplays(); applyAllFaderValues(); }
}
function toggleAB() { abState.current = abState.current === 'A' ? 'B' : 'A'; selectAB(abState.current); }
function copyAToB() { abState.presetB = JSON.parse(JSON.stringify(abState.presetA)); alert('A copied to B'); }
function closeABCompare() { abState.active = false; document.getElementById('abCompare').style.display = 'none'; }

// ===== VOICE DUCKING =====
function detectVoice() {
  if (!inputAnalyser) return false;
  const data = new Float32Array(inputAnalyser.fftSize);
  inputAnalyser.getFloatTimeDomainData(data);
  let sum = 0, count = 0;
  for (let i = 0; i < data.length; i++) {
    const freq = i * audioCtx.sampleRate / data.length;
    if (freq >= 300 && freq <= 3000) { sum += data[i]*data[i]; count++; }
  }
  const rms = count > 0 ? Math.sqrt(sum/count) : 0;
  return rms > 0 && 20 * Math.log10(rms) > duckingState.threshold;
}

function updateDucking() {
  if (!duckingState.enabled || !duckingGain) return;
  const voiceDetected = detectVoice();
  const targetGain = voiceDetected ? dbToGain(duckingState.reduction) : 1;
  const current = duckingGain.gain.value;
  const coeff = voiceDetected ? Math.exp(-1/(duckingState.attack * audioCtx.sampleRate/1000)) : Math.exp(-1/(duckingState.release * audioCtx.sampleRate/1000));
  duckingGain.gain.value = current + (targetGain - current) * (1 - coeff);
}

function toggleDucking() {
  duckingState.enabled = !duckingState.enabled;
  document.getElementById('btnDuck').classList.toggle('active', duckingState.enabled);
  if (duckingState.enabled && !duckingGain) initDucking();
}

// ===== MACRO RECORDER =====
function toggleMacroRecord() {
  macroState.recording = !macroState.recording;
  document.getElementById('macroRecordBtn').classList.toggle('active', macroState.recording);
  if (macroState.recording) { macroState.startTime = Date.now(); macroState.keyframes = []; captureKeyframe(); }
}

function captureKeyframe() {
  if (!macroState.recording) return;
  const timestamp = (Date.now() - macroState.startTime) / 1000;
  const values = {};
  macroState.parameters.forEach(param => values[param] = knobState[param]);
  macroState.keyframes.push({ timestamp, values });
  renderMacroTimeline();
}

function renderMacroTimeline() {
  const timeline = document.getElementById('macroTimeline');
  timeline.innerHTML = '';
  const duration = macroState.keyframes.length > 0 ? macroState.keyframes[macroState.keyframes.length-1].timestamp : 0;
  macroState.keyframes.forEach(kf => {
    const pos = (kf.timestamp / Math.max(duration, 1)) * 100;
    const marker = document.createElement('div');
    marker.className = 'macro-keyframe';
    marker.style.left = pos + '%';
    timeline.appendChild(marker);
  });
  document.getElementById('macroParams').textContent = `${macroState.parameters.length} parameters`;
}

function toggleMacroPlay() {
  macroState.playing = !macroState.playing;
  document.getElementById('macroPlayBtn').classList.toggle('active', macroState.playing);
  if (macroState.playing) playMacro();
}

function playMacro() {
  if (!macroState.playing || macroState.keyframes.length < 2) return;
  const startTime = Date.now();
  const duration = macroState.keyframes[macroState.keyframes.length-1].timestamp * 1000;
  
  function animate() {
    if (!macroState.playing) return;
    const elapsed = (Date.now() - startTime) / 1000;
    if (elapsed >= duration) { macroState.playing = false; document.getElementById('macroPlayBtn').classList.remove('active'); return; }
    
    for (let i = 0; i < macroState.keyframes.length - 1; i++) {
      const kf1 = macroState.keyframes[i];
      const kf2 = macroState.keyframes[i+1];
      if (elapsed >= kf1.timestamp && elapsed < kf2.timestamp) {
        const t = (elapsed - kf1.timestamp) / (kf2.timestamp - kf1.timestamp);
        macroState.parameters.forEach(param => {
          knobState[param] = kf1.values[param] + (kf2.values[param] - kf1.values[param]) * t;
          updateFaderDisplay(param);
          applyKnobValue(param, knobState[param]);
        });
        break;
      }
    }
    document.getElementById('macroTime').textContent = `${Math.floor(elapsed/60)}:${String(Math.floor(elapsed%60)).padStart(2,'0')}`;
    requestAnimationFrame(animate);
  }
  animate();
}

function clearMacro() { macroState.keyframes = []; renderMacroTimeline(); }

function exportMacro() {
  const data = JSON.stringify(macroState, null, 2);
  const blob = new Blob([data], {type: 'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = 'macro.json'; a.click();
  URL.revokeObjectURL(url);
}

function toggleMacroPanel() {
  document.getElementById('macroPanel').style.display = document.getElementById('macroPanel').style.display === 'none' ? 'block' : 'none';
}

// ===== NETWORK SYNC =====
function updateNetworkUI() {
  const dot = document.getElementById('networkDot');
  dot.className = 'network-dot' + (networkState.connected ? ' connected' : '');
  document.getElementById('networkStatus').textContent = networkState.connected ? 'Synced' : 'Offline';
}
setInterval(() => { if (Math.random() > 0.95) { networkState.connected = !networkState.connected; updateNetworkUI(); } }, 5000);

// ===== DYNAMIC EQ =====
function updateDynamicEQ() {
  if (!masterAnalyser) return;
  const freqData = new Uint8Array(masterAnalyser.frequencyBinCount);
  masterAnalyser.getByteFrequencyData(freqData);
  
  Object.entries(dynEqState.bands).forEach(([bandId, config]) => {
    if (!config.active) return;
    const idx = Math.floor(config.freq / (audioCtx.sampleRate/2) * freqData.length);
    const level = freqData[idx] ? (freqData[idx] / 255) * 60 - 60 : -60;
    const isActive = level > config.threshold;
    const indicator = document.getElementById(`dynEq${bandId}Active`);
    const info = document.getElementById(`dynEq${bandId}Info`);
    if (indicator) { indicator.style.height = isActive ? '100%' : '0%'; indicator.style.background = isActive ? 'var(--accent-green)' : '#333'; }
    if (info) info.className = 'dyn-eq-info' + (isActive ? ' active' : '');
    if (isActive && eqFilters[parseInt(bandId)-1]) {
      const boost = (level - config.threshold) * 0.3;
      eqFilters[parseInt(bandId)-1].gain.value = (knobState['eq'+bandId] - 50) / 50 * 12 + boost;
    }
  });
}

function toggleDynEq(bandId) {
  dynEqState.bands[bandId].active = !dynEqState.bands[bandId].active;
}

// ===== FLOATING ANALYZER =====
function toggleFloatingAnalyzer() {
  const panel = document.getElementById('floatingAnalyzer');
  panel.classList.toggle('visible');
  if (panel.classList.contains('visible')) startAdvancedAnalyzer();
}

function startAdvancedAnalyzer() {
  const canvas = document.getElementById('analyzerCanvas');
  if (!canvas || !masterAnalyser) return;
  const ctx = canvas.getContext('2d');
  
  function draw() {
    if (!document.getElementById('floatingAnalyzer').classList.contains('visible')) return;
    const w = canvas.width = canvas.parentElement.clientWidth;
    const h = canvas.height = canvas.parentElement.clientHeight;
    const bufferLength = masterAnalyser.frequencyBinCount;
    const dataArray = new Uint8Array(bufferLength);
    masterAnalyser.getByteFrequencyData(dataArray);
    
    const gradient = ctx.createLinearGradient(0, 0, 0, h);
    gradient.addColorStop(0, '#0a0a10'); gradient.addColorStop(1, '#1a1a25');
    ctx.fillStyle = gradient; ctx.fillRect(0, 0, w, h);
    
    const barWidth = w / 128;
    for (let i = 0; i < 128; i++) {
      const value = dataArray[i * 4] / 255;
      const barHeight = value * h * 0.9;
      const hue = 200 - (i / 128) * 180 + value * 40;
      ctx.fillStyle = `hsl(${hue}, 80%, ${40 + value * 30}%)`;
      ctx.fillRect(i * barWidth, h - barHeight, barWidth - 2, barHeight);
      if (value > 0.7) {
        ctx.fillStyle = `hsla(${hue}, 100%, 80%, ${value - 0.7})`;
        ctx.fillRect(i * barWidth, h - barHeight - 10, barWidth - 2, 10);
      }
    }
    requestAnimationFrame(draw);
  }
  draw();
}

// ===== KEYBOARD SHORTCUTS =====
function toggleShortcuts() { document.getElementById('shortcutsPanel').classList.toggle('visible'); }

document.addEventListener('keydown', (e) => {
  if (e.key === 'Tab' && !e.ctrlKey && !e.metaKey) { e.preventDefault(); toggleABCompare(); }
  if (e.key === 'm' || e.key === 'M') { e.preventDefault(); e.shiftKey ? toggleMonoCompat() : toggleMSMode(); }
  if (e.key >= '1' && e.key <= '5') soloBand(parseInt(e.key));
  if (e.ctrlKey && e.key === 'n') { e.preventDefault(); /* connectNetwork */ }
  if (e.ctrlKey && e.key === 's') { e.preventDefault(); savePreset(); }
  if (e.key === 'F5') { e.preventDefault(); resetAll(); }
  if (e.key === ' ') { e.preventDefault(); bypassAll(); }
  if (e.key === 'Escape') { closeSettings(); document.getElementById('shortcutsPanel').classList.remove('visible'); closeABCompare(); }
});

function soloBand(bandNum) {
  bandState.solo = bandState.solo === bandNum ? null : bandNum;
  document.querySelectorAll('.band-card').forEach((card, i) => {
    const bandId = i + 1;
    const isActive = bandState.solo === null || bandState.solo === bandId;
    card.style.opacity = isActive ? '1' : '0.3';
    card.classList.toggle('active', isActive);
  });
}

function muteBand(bandNum) {
  const idx = bandState.muted.indexOf(bandNum);
  if (idx === -1) bandState.muted.push(bandNum); else bandState.muted.splice(idx, 1);
}

// ===== UTILS =====
function bypassAll() {
  const btn = document.getElementById('btnBypass');
  btn.classList.toggle('active');
  const isBypassed = btn.classList.contains('active');
  if (outputGainNode) outputGainNode.gain.value = isBypassed ? 0 : dbToGain(knobState.outputGain - 50);
  document.getElementById('infoEngine').textContent = isBypassed ? 'Bypassed' : 'Running';
}

function resetAll() { if (confirm('Reset all to defaults?')) loadPreset('fm-loud'); }
function updateInfoPanel() { document.getElementById('infoAGC').textContent = modules.AGC ? 'ON' : 'OFF'; }
function updateClock() { setInterval(() => { document.getElementById('clockDisplay').textContent = new Date().toTimeString().split(' ')[0]; }, 1000); }
function closeApp() { if (confirm('Close OmniaPro?')) { if (audioCtx) audioCtx.close(); window.close(); } }
function minimizeApp() { alert('Minimize (requires Electron)'); }
function showSettings() { document.getElementById('settingsModal').classList.add('visible'); }
function closeSettings() { document.getElementById('settingsModal').classList.remove('visible'); }
function applySettings() { closeSettings(); alert('Settings applied!'); }
function resetModule() { alert('Module reset'); }
function bypassModule() { alert('Module bypassed'); }
function copySettings() { alert('Settings copied'); }
function pasteSettings() { alert('Settings pasted'); }

// ===== CONTEXT MENU =====
document.addEventListener('contextmenu', (e) => { e.preventDefault(); const menu = document.getElementById('contextMenu'); menu.style.left = e.clientX + 'px'; menu.style.top = e.clientY + 'px'; menu.classList.add('visible'); });
document.addEventListener('click', () => { document.getElementById('contextMenu').classList.remove('visible'); });

// ===== ENUMERATE DEVICES =====
async function enumerateDevices() {
  try {
    const devices = await navigator.mediaDevices.enumerateDevices();
    const audioInputs = devices.filter(d => d.kind === 'audioinput');
    const audioOutputs = devices.filter(d => d.kind === 'audiooutput');
    const inputSelect = document.getElementById('inputDeviceSelect');
    audioInputs.forEach(d => { const opt = document.createElement('option'); opt.value = d.deviceId; opt.textContent = d.label || 'Input ' + (audioInputs.indexOf(d) + 1); inputSelect.appendChild(opt); });
    const outputSelect = document.getElementById('outputDeviceSelect');
    audioOutputs.forEach(d => { const opt = document.createElement('option'); opt.value = d.deviceId; opt.textContent = d.label || 'Output ' + (audioOutputs.indexOf(d) + 1); outputSelect.appendChild(opt); });
  } catch(e) { console.warn('Device enumeration failed:', e); }
}

// ===== INITIALIZE =====
document.addEventListener('DOMContentLoaded', () => {
  initAllFaderDisplays();
  initFaderListeners();
  enumerateDevices();
  
  document.addEventListener('click', async function startAudio() { if (!isAudioStarted) await initAudio(); }, { once: false });
  
  document.querySelectorAll('.toolbar-btn, .power-btn, .toggle-btn, .fader-control, .fader-h').forEach(el => {
    el.addEventListener('click', async () => { if (!isAudioStarted) await initAudio(); }, { once: true });
  });
});

console.log('✅ OmniaPro v11.2 - Fader Edition Loaded');
</script>
</body>
</html>
