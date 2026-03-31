<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>StudyFlow — Tabela de Estudos</title>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=DM+Mono:ital,wght@0,300;0,400;1,300&family=Fraunces:ital,wght@0,300;0,600;1,300&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0a0f;
    --surface: #111118;
    --surface2: #18181f;
    --border: #2a2a3a;
    --accent: #7c5cfc;
    --accent2: #fc5c7d;
    --accent3: #5cf0b0;
    --accent4: #fcb85c;
    --text: #e8e8f0;
    --text-muted: #7070a0;
    --text-dim: #40405a;
    --easy: #5cf0b0;
    --medium: #fcb85c;
    --hard: #fc5c7d;
    --radius: 12px;
    --radius-sm: 6px;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: #0a0a0f;
    background: var(--bg);
    color: #e8e8f0;
    color: var(--text);
    font-family: 'DM Mono', monospace;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* ── BACKGROUND ── */
  .bg-grid {
    position: fixed; inset: 0; z-index: 0;
    background-image:
      linear-gradient(rgba(124,92,252,.04) 1px, transparent 1px),
      linear-gradient(90deg, rgba(124,92,252,.04) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
  }
  .bg-blob {
    position: fixed; z-index: 0; pointer-events: none;
    border-radius: 50%; filter: blur(80px); opacity: .12;
  }
  .bg-blob-1 { width: 500px; height: 500px; background: var(--accent); top: -100px; left: -150px; }
  .bg-blob-2 { width: 400px; height: 400px; background: var(--accent2); bottom: 0; right: -100px; }
  .bg-blob-3 { width: 300px; height: 300px; background: var(--accent3); top: 40%; left: 50%; transform: translateX(-50%); opacity: .08; }

  /* ── LAYOUT ── */
  .wrapper { position: relative; z-index: 1; max-width: 1400px; margin: 0 auto; padding: 0 24px 60px; background: #0a0a0f; background: var(--bg); }

  /* ── HEADER ── */
  header {
    display: flex; align-items: center; justify-content: space-between;
    padding: 32px 0 48px;
    border-bottom: 1px solid var(--border);
    margin-bottom: 48px;
    flex-wrap: wrap; gap: 16px;
  }
  .logo {
    font-family: 'Syne', sans-serif; font-weight: 800; font-size: 28px; letter-spacing: -1px;
    display: flex; align-items: center; gap: 10px;
  }
  .logo-dot { width: 10px; height: 10px; border-radius: 50%; background: var(--accent); box-shadow: 0 0 12px var(--accent); }
  .header-meta {
    display: flex; flex-direction: column; align-items: flex-end; gap: 4px;
  }
  .week-label {
    font-family: 'Fraunces', serif; font-style: italic; font-size: 13px; color: var(--text-muted);
  }
  .week-input {
    background: var(--surface2); border: 1px solid var(--border); border-radius: var(--radius-sm);
    color: var(--text); font-family: 'DM Mono', monospace; font-size: 13px;
    padding: 6px 12px; outline: none;
    transition: border-color .2s;
  }
  .week-input:focus { border-color: var(--accent); }

  /* ── PRINCIPLES BAR (toggleable tabs) ── */
  .principles { display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 0; }
  .prin-tab {
    display: flex; align-items: center; gap: 7px;
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 8px 16px;
    font-size: 11px; letter-spacing: .05em; color: var(--text-muted);
    text-transform: uppercase; cursor: pointer; transition: all .2s;
    user-select: none; font-family: 'Syne', sans-serif; font-weight: 600;
  }
  .prin-tab:hover { border-color: var(--accent); color: var(--text); }
  .prin-tab.active {
    color: #fff; box-shadow: 0 0 16px rgba(0,0,0,.3);
  }
  .prin-tab .prin-dot { width: 7px; height: 7px; border-radius: 50%; flex-shrink:0; }
  .prin-tab .prin-arrow {
    font-size: 9px; margin-left: 2px; transition: transform .25s;
    opacity: .6;
  }
  .prin-tab.active .prin-arrow { transform: rotate(180deg); }

  /* Hours tab special style */
  .prin-tab.hours-tab {
    border-color: rgba(92,240,176,.4); color: var(--easy);
  }
  .prin-tab.hours-tab.active {
    background: rgba(92,240,176,.15); border-color: var(--easy);
  }

  /* Panel below tabs */
  .prin-panels { margin-bottom: 40px; }
  .prin-panel {
    display: none; margin-top: 12px;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 24px 28px;
    animation: fadeUp .25s ease;
  }
  .prin-panel.active { display: block; }

  .prin-panel-title {
    font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700;
    margin-bottom: 14px; display: flex; align-items: center; gap: 8px;
  }
  .prin-panel p {
    font-size: 12px; color: var(--text-muted); line-height: 1.8; max-width: 800px;
  }
  .prin-panel ul {
    list-style: none; display: flex; flex-direction: column; gap: 8px; margin-top: 12px;
  }
  .prin-panel ul li {
    font-size: 12px; color: var(--text-muted); line-height: 1.6;
    padding-left: 16px; position: relative;
  }
  .prin-panel ul li::before {
    content: '▸'; position: absolute; left: 0; color: var(--accent); font-size: 10px;
  }

  /* Hours panel grid */
  .hours-grid {
    display: grid; grid-template-columns: repeat(7, 1fr); gap: 12px; margin-top: 8px;
  }
  @media(max-width:900px){ .hours-grid { grid-template-columns: repeat(4,1fr); } }
  @media(max-width:500px){ .hours-grid { grid-template-columns: repeat(2,1fr); } }

  .hours-day-card {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 14px 12px;
    display: flex; flex-direction: column; gap: 8px; align-items: center;
    transition: border-color .2s;
  }
  .hours-day-card:hover { border-color: var(--easy); }
  .hours-day-label {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    text-transform: uppercase; letter-spacing: .07em; color: var(--text-muted);
  }
  .hours-input-wrap { position: relative; width: 100%; }
  .hours-number {
    width: 100%; background: var(--bg); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 8px 10px 8px 10px;
    color: var(--easy); font-family: 'Syne', sans-serif; font-size: 22px; font-weight: 800;
    text-align: center; outline: none; transition: border-color .2s;
    -moz-appearance: textfield;
  }
  .hours-number::-webkit-inner-spin-button,
  .hours-number::-webkit-outer-spin-button { -webkit-appearance: none; }
  .hours-number:focus { border-color: var(--easy); box-shadow: 0 0 0 2px rgba(92,240,176,.1); }
  .hours-unit { font-size: 10px; color: var(--text-dim); letter-spacing: .05em; text-transform: uppercase; }
  .hours-blocks { font-size: 10px; color: var(--text-dim); text-align: center; min-height: 14px; }

  /* Total bar */
  .hours-total-row {
    display: flex; align-items: center; justify-content: space-between;
    flex-wrap: wrap; gap: 12px;
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 14px 20px; margin-top: 16px;
  }
  .hours-total-label { font-size: 12px; color: var(--text-muted); }
  .hours-total-val {
    font-family: 'Syne', sans-serif; font-size: 20px; font-weight: 800; color: var(--easy);
  }
  .hours-bar-wrap {
    width: 100%; height: 6px; background: var(--border); border-radius: 100px; margin-top: 8px; overflow: hidden;
  }
  .hours-bar-fill {
    height: 100%; border-radius: 100px;
    background: linear-gradient(90deg, var(--easy), var(--accent));
    transition: width .4s ease;
  }

  /* ── POMODORO TIMER ── */
  .pomo-timer-wrap {
    display: flex; flex-direction: column; align-items: center;
    margin: 28px 0 8px; gap: 0;
  }
  .pomo-phase-badge {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .14em; text-transform: uppercase; margin-bottom: 18px;
    padding: 5px 16px; border-radius: 100px; border: 1px solid;
    transition: all .4s;
  }
  .phase-study  { color: var(--accent);  border-color: rgba(124,92,252,.4); background: rgba(124,92,252,.08); }
  .phase-break  { color: var(--easy);    border-color: rgba(92,240,176,.4); background: rgba(92,240,176,.08); }
  .phase-long   { color: var(--accent4); border-color: rgba(252,184,92,.4); background: rgba(252,184,92,.08); }
  .phase-idle   { color: var(--text-dim);border-color: var(--border);       background: transparent; }

  /* SVG ring clock */
  .pomo-ring-wrap { position: relative; width: 220px; height: 220px; }
  .pomo-ring-wrap svg { transform: rotate(-90deg); }
  .pomo-ring-bg   { fill: none; stroke: var(--border); stroke-width: 10; }
  .pomo-ring-fill {
    fill: none; stroke-width: 10; stroke-linecap: round;
    transition: stroke-dashoffset .8s ease, stroke .4s;
  }
  .pomo-ring-center {
    position: absolute; inset: 0; display: flex; flex-direction: column;
    align-items: center; justify-content: center; gap: 2px;
  }
  .pomo-time-display {
    font-family: 'Syne', sans-serif; font-size: 46px; font-weight: 800;
    letter-spacing: -2px; line-height: 1; color: var(--text);
    transition: color .4s;
  }
  .pomo-time-label {
    font-size: 10px; letter-spacing: .1em; text-transform: uppercase;
    color: var(--text-dim);
  }

  /* Controls */
  .pomo-controls { display: flex; gap: 12px; margin-top: 24px; flex-wrap: wrap; justify-content: center; }
  .pomo-ctrl-btn {
    display: flex; align-items: center; gap: 7px;
    padding: 11px 24px; border-radius: var(--radius-sm); border: 1px solid;
    font-family: 'Syne', sans-serif; font-size: 12px; font-weight: 700;
    letter-spacing: .06em; cursor: pointer; transition: all .2s;
  }
  .pomo-ctrl-btn:hover { transform: translateY(-1px); }
  .pomo-ctrl-start  { background: var(--accent);  border-color: var(--accent);  color: #fff; box-shadow: 0 0 0 0 rgba(124,92,252,.4); }
  .pomo-ctrl-start:hover { box-shadow: 0 0 20px rgba(124,92,252,.5); }
  .pomo-ctrl-start.running { background: var(--surface2); border-color: var(--accent2); color: var(--accent2); }
  .pomo-ctrl-reset  { background: transparent; border-color: var(--border); color: var(--text-muted); }
  .pomo-ctrl-reset:hover { border-color: var(--accent4); color: var(--accent4); }
  .pomo-ctrl-skip   { background: transparent; border-color: var(--border); color: var(--text-muted); }
  .pomo-ctrl-skip:hover { border-color: var(--easy); color: var(--easy); }

  /* Block tracker */
  .pomo-blocks-row { display: flex; gap: 7px; margin-top: 20px; flex-wrap: wrap; justify-content: center; }
  .pomo-block-dot {
    width: 28px; height: 28px; border-radius: 50%; border: 2px solid var(--border);
    background: transparent; display: flex; align-items: center; justify-content: center;
    font-size: 12px; transition: all .35s;
  }
  .pomo-block-dot.done-study { background: rgba(124,92,252,.25); border-color: var(--accent); }
  .pomo-block-dot.done-break { background: rgba(92,240,176,.2);  border-color: var(--easy); }
  .pomo-block-dot.current    { border-color: var(--accent4); box-shadow: 0 0 10px rgba(252,184,92,.4); }

  /* Notification banner */
  .pomo-notify {
    position: fixed; top: 24px; right: 24px; z-index: 9999;
    background: var(--surface); border: 1px solid var(--accent);
    border-radius: var(--radius); padding: 16px 22px;
    box-shadow: 0 8px 40px rgba(0,0,0,.5);
    font-family: 'Syne', sans-serif; font-size: 14px; font-weight: 700;
    transform: translateX(120%); transition: transform .4s cubic-bezier(.2,.8,.3,1);
    max-width: 320px; display: flex; align-items: center; gap: 12px;
  }
  .pomo-notify.show { transform: translateX(0); }
  .pomo-notify-icon { font-size: 28px; }
  .pomo-notify-text { display: flex; flex-direction: column; gap: 3px; }
  .pomo-notify-title { color: var(--text); }
  .pomo-notify-sub   { font-size: 11px; color: var(--text-muted); font-weight: 400; }


    display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 16px; margin-top: 16px;
  }
  @media(max-width:600px){ .pomo-grid { grid-template-columns: 1fr 1fr; } }
  .pomo-card {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 14px 16px;
    display: flex; flex-direction: column; gap: 6px;
  }
  .pomo-card label { font-size: 10px; color: var(--text-dim); text-transform: uppercase; letter-spacing: .07em; }
  .pomo-card input {
    background: var(--bg); border: 1px solid var(--border);
    border-radius: 4px; padding: 6px 10px; color: var(--accent4);
    font-family: 'Syne', sans-serif; font-size: 20px; font-weight: 700;
    text-align: center; outline: none; width: 100%;
    transition: border-color .2s; -moz-appearance: textfield;
  }
  .pomo-card input::-webkit-inner-spin-button { -webkit-appearance: none; }
  .pomo-card input:focus { border-color: var(--accent4); }
  .pomo-card .pomo-note { font-size: 10px; color: var(--text-dim); }



  /* ── WEEKLY MATRIX TABLE ── */
  .matrix-section { margin-bottom: 48px; }
  .matrix-section-title {
    font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700;
    letter-spacing: .1em; text-transform: uppercase; color: var(--text-muted);
    margin-bottom: 20px; display: flex; align-items: center; gap: 10px;
  }
  .matrix-section-title::after { content:''; flex:1; height:1px; background:var(--border); }

  .matrix-wrap { overflow-x: auto; border-radius: var(--radius); border: 1px solid #2a2a3a; border: 1px solid var(--border); }
  .matrix-table { width: 100%; border-collapse: collapse; font-size: 12px; min-width: 720px; background: #111118; background: var(--surface); }
  .matrix-table th {
    padding: 14px 18px; font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .08em; text-transform: uppercase; color: #7070a0; color: var(--text-muted);
    border-bottom: 1px solid #2a2a3a; border-bottom: 1px solid var(--border);
    background: #18181f; background: var(--surface2);
    text-align: center; white-space: nowrap;
  }
  .matrix-table th:first-child { text-align: left; width: 100px; }
  .matrix-table td {
    padding: 0; border-bottom: 1px solid rgba(42,42,58,.5);
    border-right: 1px solid rgba(42,42,58,.4); vertical-align: top;
    background: #111118; background: var(--surface);
  }
  .matrix-table td:last-child { border-right: none; }
  .matrix-table tr:last-child td { border-bottom: none; }
  .matrix-table tr:hover td { background: rgba(255,255,255,.015); }

  .matrix-period {
    padding: 12px 18px; font-size: 11px; font-weight: 600;
    color: #7070a0; color: var(--text-muted); letter-spacing: .05em; text-transform: uppercase;
    background: #18181f; background: var(--surface2);
    border-right: 1px solid #2a2a3a; border-right: 1px solid var(--border);
    white-space: nowrap; vertical-align: middle;
  }

  .cell-wrap {
    padding: 10px 12px; display: flex; flex-direction: column; gap: 6px; min-height: 70px;
  }
  .subj-tag {
    display: inline-flex; align-items: center; gap: 6px;
    padding: 5px 10px; border-radius: var(--radius-sm);
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 600;
    border: 1px solid; cursor: default; transition: transform .15s;
    white-space: nowrap; max-width: 100%;
  }
  .subj-tag:hover { transform: translateY(-1px); }
  .subj-tag .tag-dot { width: 5px; height: 5px; border-radius: 50%; flex-shrink: 0; }
  .subj-tag.empty {
    color: var(--text-dim); border-color: var(--border); background: transparent;
    font-weight: 400; font-family: 'DM Mono', monospace; font-size: 10px;
    opacity: .5; cursor: default;
  }

  /* ── SUBJECT LEGEND / TABS ── */
  .subj-legend { margin-bottom: 32px; }
  .subj-legend-title {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .1em; text-transform: uppercase; color: var(--text-muted);
    margin-bottom: 14px; display: flex; align-items: center; gap: 8px;
  }
  .subj-legend-title::after { content:''; flex:1; height:1px; background:var(--border); }
  .subj-tabs-row { display: flex; flex-wrap: wrap; gap: 8px; }
  .subj-pill {
    display: inline-flex; align-items: center; gap: 8px;
    padding: 8px 16px; border-radius: var(--radius-sm);
    font-family: 'Syne', sans-serif; font-size: 12px; font-weight: 700;
    border: 1px solid; cursor: pointer; transition: all .2s;
    user-select: none; position: relative;
  }
  .subj-pill .sp-dot { width: 8px; height: 8px; border-radius: 50%; flex-shrink:0; }
  .subj-pill .sp-name { outline: none; background: transparent; border: none;
    font-family: 'Syne', sans-serif; font-size: 12px; font-weight: 700;
    color: inherit; min-width: 80px; cursor: text; }
  .subj-pill .sp-remove {
    width: 16px; height: 16px; border-radius: 50%; background: rgba(255,255,255,.1);
    display: flex; align-items: center; justify-content: center;
    font-size: 10px; cursor: pointer; flex-shrink: 0; transition: background .2s;
  }
  .subj-pill .sp-remove:hover { background: rgba(252,92,125,.4); }
  .add-subj-btn {
    display: inline-flex; align-items: center; gap: 6px;
    padding: 8px 16px; border-radius: var(--radius-sm);
    border: 1px dashed var(--border); background: transparent;
    font-family: 'DM Mono', monospace; font-size: 11px; color: var(--text-dim);
    cursor: pointer; transition: all .2s;
  }
  .add-subj-btn:hover { border-color: var(--accent); color: var(--accent); }

  /* ── GOALS ROW ── */
  .meta-row {
    display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 40px;
  }
  @media(max-width:700px){ .meta-row { grid-template-columns: 1fr; } }

  .card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 20px 24px;
    transition: border-color .2s;
  }
  .card:hover { border-color: var(--accent); }
  .card-label {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 600;
    letter-spacing: .1em; text-transform: uppercase; color: var(--text-muted);
    margin-bottom: 10px; display: flex; align-items: center; gap: 6px;
  }
  .card-label .icon { font-size: 14px; }
  textarea, input[type=text] {
    background: transparent; border: none; outline: none; resize: none;
    color: var(--text); font-family: 'DM Mono', monospace; font-size: 13px;
    line-height: 1.7; width: 100%;
  }
  textarea::placeholder, input[type=text]::placeholder { color: var(--text-dim); }

  /* Force override for number/time inputs inside panels */
  input[type=number], input[type=time] {
    -webkit-appearance: none; -moz-appearance: textfield;
  }
  input[type=number]::-webkit-inner-spin-button,
  input[type=number]::-webkit-outer-spin-button { -webkit-appearance: none; margin: 0; }

  /* Stepper control (replaces plain number input for hours/pomo) */
  .stepper {
    display: flex; align-items: center; gap: 0;
    background: var(--bg); border: 1px solid var(--border);
    border-radius: var(--radius-sm); overflow: hidden; width: 100%;
    transition: border-color .2s;
  }
  .stepper:focus-within { border-color: var(--easy); box-shadow: 0 0 0 2px rgba(92,240,176,.1); }
  .stepper.pomo-stepper:focus-within { border-color: var(--accent4); box-shadow: 0 0 0 2px rgba(252,184,92,.1); }
  .stepper-btn {
    background: var(--surface2); border: none; color: var(--text-muted);
    font-size: 16px; padding: 0 12px; height: 44px; cursor: pointer;
    transition: background .15s, color .15s; flex-shrink: 0;
    font-family: 'Syne', sans-serif; font-weight: 700;
    display: flex; align-items: center; justify-content: center;
  }
  .stepper-btn:hover { background: var(--border); color: var(--text); }
  .stepper-btn:active { transform: scale(.92); }
  .stepper-val {
    flex: 1; text-align: center; background: transparent; border: none; outline: none;
    color: var(--easy); font-family: 'Syne', sans-serif; font-size: 20px; font-weight: 800;
    padding: 0; height: 44px; min-width: 0; cursor: text;
    -moz-appearance: textfield;
  }
  .stepper-val::-webkit-inner-spin-button { -webkit-appearance: none; }
  .pomo-stepper .stepper-val { color: var(--accent4); font-size: 18px; }

  /* ── WEEK TABS ── */
  .days-nav {
    display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 32px;
  }
  .day-tab {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 8px 18px;
    font-family: 'Syne', sans-serif; font-weight: 600; font-size: 12px;
    letter-spacing: .05em; text-transform: uppercase; color: var(--text-muted);
    cursor: pointer; transition: all .2s;
    user-select: none;
  }
  .day-tab:hover { border-color: var(--accent); color: var(--text); }
  .day-tab.active {
    background: var(--accent); border-color: var(--accent);
    color: #fff; box-shadow: 0 0 20px rgba(124,92,252,.4);
  }

  /* ── DAY PANEL ── */
  .day-panel { display: none; animation: fadeUp .35s ease; }
  .day-panel.active { display: block; }
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(12px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  /* ── SUBJECT BLOCKS ── */
  .blocks-grid {
    display: grid; grid-template-columns: 1fr 1fr; gap: 24px; margin-bottom: 28px;
  }
  @media(max-width:900px){ .blocks-grid { grid-template-columns: 1fr; } }

  .block {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); overflow: hidden; transition: border-color .2s;
    position: relative;
  }
  .block:hover { border-color: var(--border); box-shadow: 0 0 0 1px var(--accent2); }
  .block-accent-bar { height: 3px; width: 100%; }

  .block-header {
    display: flex; align-items: center; justify-content: space-between;
    padding: 18px 20px 12px;
    border-bottom: 1px solid var(--border);
  }
  .block-title-group { display: flex; align-items: center; gap: 10px; }
  .block-icon {
    width: 32px; height: 32px; border-radius: 8px;
    display: flex; align-items: center; justify-content: center;
    font-size: 15px;
  }
  .block-subject {
    font-family: 'Syne', sans-serif; font-weight: 700; font-size: 14px;
    color: var(--text); outline: none; border: none; background: transparent;
    min-width: 160px;
  }
  .block-subject::placeholder { color: var(--text-dim); font-weight: 400; }
  .block-time {
    font-size: 11px; color: var(--text-muted); display: flex; align-items: center; gap: 6px;
  }
  .check-done {
    width: 22px; height: 22px; border-radius: 50%; border: 2px solid var(--border);
    display: flex; align-items: center; justify-content: center; cursor: pointer;
    font-size: 11px; transition: all .2s; flex-shrink: 0;
  }
  .check-done.done { background: var(--accent3); border-color: var(--accent3); color: #0a0a0f; }

  .block-body { padding: 16px 20px; display: flex; flex-direction: column; gap: 14px; }

  /* Row with label + input */
  .field-row { display: flex; flex-direction: column; gap: 4px; }
  .field-label {
    font-size: 10px; letter-spacing: .08em; text-transform: uppercase;
    color: var(--text-dim);
  }
  .field-input {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 8px 12px;
    color: var(--text); font-family: 'DM Mono', monospace; font-size: 12px;
    outline: none; transition: border-color .2s; width: 100%;
  }
  .field-input:focus { border-color: var(--accent); }
  .field-input::placeholder { color: var(--text-dim); }

  /* Difficulty selector */
  .diff-row { display: flex; gap: 6px; }
  .diff-btn {
    flex: 1; padding: 6px; border-radius: var(--radius-sm);
    border: 1px solid var(--border); background: transparent;
    font-family: 'DM Mono', monospace; font-size: 11px; cursor: pointer;
    color: var(--text-muted); transition: all .2s; text-align: center;
  }
  .diff-btn:hover { border-color: currentColor; }
  .diff-btn[data-diff="easy"].sel  { background: rgba(92,240,176,.15); border-color: var(--easy); color: var(--easy); }
  .diff-btn[data-diff="med"].sel   { background: rgba(252,184,92,.15); border-color: var(--medium); color: var(--medium); }
  .diff-btn[data-diff="hard"].sel  { background: rgba(252,92,125,.15); border-color: var(--hard); color: var(--hard); }

  /* Method pills */
  .method-row { display: flex; flex-wrap: wrap; gap: 6px; }
  .method-btn {
    padding: 4px 10px; border-radius: 100px; border: 1px solid var(--border);
    background: transparent; font-family: 'DM Mono', monospace; font-size: 10px;
    cursor: pointer; color: var(--text-muted); transition: all .2s;
    letter-spacing: .03em;
  }
  .method-btn.sel { background: rgba(124,92,252,.2); border-color: var(--accent); color: var(--accent); }

  /* Revision chips */
  .revision-chips { display: flex; gap: 6px; flex-wrap: wrap; }
  .rev-chip {
    padding: 4px 10px; border-radius: 100px; font-size: 10px;
    background: rgba(92,240,176,.08); border: 1px solid rgba(92,240,176,.2);
    color: var(--accent3); letter-spacing: .03em;
  }

  /* ── SCHEDULE TIMELINE ── */
  .timeline { margin-bottom: 28px; }
  .timeline-label {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 600;
    letter-spacing: .1em; text-transform: uppercase; color: var(--text-muted);
    margin-bottom: 14px; display: flex; align-items: center; gap: 6px;
  }
  .timeline-slots { display: flex; flex-direction: column; gap: 6px; }
  .slot {
    display: grid; grid-template-columns: 90px 1fr 120px;
    align-items: center; gap: 12px;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 10px 16px;
  }
  .slot-time { font-size: 12px; color: var(--accent); font-weight: 400; }
  .slot-desc { font-size: 12px; color: var(--text); }
  .slot-tag {
    text-align: right; font-size: 10px; letter-spacing: .06em;
    text-transform: uppercase;
  }
  .tag-study { color: var(--accent); }
  .tag-break { color: var(--accent4); }
  .tag-review { color: var(--accent3); }

  /* ── DAY NOTES ── */
  .day-notes-grid {
    display: grid; grid-template-columns: 1fr 1fr; gap: 20px;
  }
  @media(max-width:700px){ .day-notes-grid { grid-template-columns: 1fr; } }

  /* ── REVISION CALENDAR ── */
  .rev-section { margin-top: 48px; }
  .section-title {
    font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700;
    letter-spacing: .1em; text-transform: uppercase; color: var(--text-muted);
    margin-bottom: 20px; display: flex; align-items: center; gap: 8px;
  }
  .section-title::after {
    content: ''; flex: 1; height: 1px; background: var(--border);
  }
  .rev-table {
    width: 100%; border-collapse: collapse; font-size: 12px;
  }
  .rev-table th {
    text-align: left; padding: 10px 16px;
    font-family: 'Syne', sans-serif; font-weight: 600; font-size: 10px;
    letter-spacing: .1em; text-transform: uppercase; color: var(--text-dim);
    border-bottom: 1px solid var(--border);
  }
  .rev-table td {
    padding: 12px 16px; border-bottom: 1px solid rgba(42,42,58,.6);
    color: var(--text-muted);
  }
  .rev-table tr:hover td { background: var(--surface2); }
  .rev-table td:first-child { color: var(--text); }
  .rev-badge {
    display: inline-block; padding: 2px 8px; border-radius: 100px;
    font-size: 10px; letter-spacing: .04em;
  }
  .rev-again  { background: rgba(252,92,125,.15); color: var(--hard);   border: 1px solid rgba(252,92,125,.3); }
  .rev-hard   { background: rgba(252,184,92,.15); color: var(--accent4); border: 1px solid rgba(252,184,92,.3); }
  .rev-good   { background: rgba(124,92,252,.15); color: var(--accent);  border: 1px solid rgba(124,92,252,.3); }
  .rev-easy   { background: rgba(92,240,176,.15); color: var(--easy);    border: 1px solid rgba(92,240,176,.3); }

  /* Anki rating buttons */
  .anki-btns { display: flex; gap: 5px; flex-wrap: wrap; }
  .anki-btn {
    padding: 4px 10px; border-radius: var(--radius-sm); border: 1px solid var(--border);
    background: transparent; font-family: 'DM Mono', monospace; font-size: 10px;
    cursor: pointer; color: var(--text-muted); transition: all .2s; white-space: nowrap;
  }
  .anki-btn:hover { opacity: .85; }
  .anki-btn.again { border-color: var(--hard);   color: var(--hard); }
  .anki-btn.hard  { border-color: var(--accent4); color: var(--accent4); }
  .anki-btn.good  { border-color: var(--accent);  color: var(--accent); }
  .anki-btn.easy  { border-color: var(--easy);    color: var(--easy); }
  .anki-btn.active { font-weight: 600; }
  .anki-btn.again.active { background: rgba(252,92,125,.15); }
  .anki-btn.hard.active  { background: rgba(252,184,92,.15); }
  .anki-btn.good.active  { background: rgba(124,92,252,.15); }
  .anki-btn.easy.active  { background: rgba(92,240,176,.15); }

  /* Anki info box */
  .anki-info {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 20px 24px; margin-bottom: 28px;
  }
  .anki-info-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; margin-top: 14px; }
  @media(max-width:700px){ .anki-info-grid { grid-template-columns: 1fr 1fr; } }
  .anki-card {
    border-radius: var(--radius-sm); padding: 12px 14px; border: 1px solid;
    display: flex; flex-direction: column; gap: 4px;
  }
  .anki-card-label { font-size: 10px; letter-spacing: .08em; text-transform: uppercase; opacity: .7; }
  .anki-card-interval { font-family: 'Syne', sans-serif; font-size: 18px; font-weight: 700; }
  .anki-card-note { font-size: 10px; opacity: .6; }

  /* ═══ ANKI INTELIGENTE — novo CSS ═══ */
  .anki-ctrl-btn {
    padding: 7px 14px; border-radius: var(--radius-sm);
    border: 1px solid var(--border); background: var(--surface2);
    color: var(--text-muted); font-family: 'Syne', sans-serif;
    font-size: 11px; font-weight: 700; cursor: pointer; transition: all .2s;
  }
  .anki-ctrl-btn:hover { border-color: var(--accent); color: var(--text); }
  .anki-ctrl-study {
    background: var(--accent); border-color: var(--accent); color: #fff !important;
  }
  .anki-ctrl-study:hover { opacity: .88; transform: translateY(-1px); }

  /* Dashboard */
  .anki-dashboard {
    display: grid; grid-template-columns: repeat(6,1fr); gap: 10px; margin-bottom: 24px;
  }
  @media(max-width:900px){ .anki-dashboard { grid-template-columns: repeat(3,1fr); } }
  @media(max-width:500px){ .anki-dashboard { grid-template-columns: repeat(2,1fr); } }
  .anki-dash-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 14px 16px;
    display: flex; flex-direction: column; gap: 4px; text-align: center;
    transition: border-color .2s;
  }
  .anki-dash-card:hover { border-color: var(--bord2); }
  .anki-dash-card.adc-atrasado { border-color: rgba(252,92,125,.3); background: rgba(252,92,125,.04); }
  .anki-dash-card.adc-hoje     { border-color: rgba(124,92,252,.3); background: rgba(124,92,252,.04); }
  .anki-dash-card.adc-novos    { border-color: rgba(92,184,252,.3); background: rgba(92,184,252,.04); }
  .anki-dash-card.adc-dominados{ border-color: rgba(92,240,176,.3); background: rgba(92,240,176,.04); }
  .adc-num {
    font-family: 'Syne', sans-serif; font-size: 24px; font-weight: 800; color: var(--text);
  }
  .adc-lbl { font-size: 10px; color: var(--text-dim); letter-spacing: .04em; }

  /* Sessão de estudo */
  .anki-sess-header {
    display: flex; align-items: center; gap: 14px;
    margin-bottom: 20px; flex-wrap: wrap;
  }
  .anki-sess-progress {
    height: 6px; background: var(--border); border-radius: 100px;
    overflow: hidden; flex: 1;
  }

  /* Card flip 3D */
  .anki-card-wrap {
    width: 100%; perspective: 1400px; cursor: pointer;
    margin-bottom: 16px; min-height: 220px;
  }
  .anki-card-inner {
    width: 100%; position: relative; transform-style: preserve-3d;
    transition: transform .55s cubic-bezier(.4,0,.2,1); min-height: 220px;
  }
  .anki-card-inner.flipped { transform: rotateY(180deg); }
  .anki-face {
    position: absolute; inset: 0; backface-visibility: hidden;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 28px 32px;
    display: flex; flex-direction: column; justify-content: center;
    min-height: 220px;
  }
  .anki-verso { transform: rotateY(180deg); border-color: var(--accent); }
  .anki-face-tag {
    font-size: 10px; letter-spacing: .12em; text-transform: uppercase;
    color: var(--text-dim); margin-bottom: 10px;
  }
  .anki-face-materia {
    font-size: 11px; border-radius: 100px; padding: 3px 10px;
    display: inline-flex; align-items: center; margin-bottom: 14px;
    width: fit-content; font-weight: 700;
  }
  .anki-face-texto {
    font-size: 17px; color: var(--text); line-height: 1.6; font-weight: 500;
  }
  .anki-face-hint { font-size: 11px; color: var(--text-dim); margin-top: 8px; }
  .anki-face-obs {
    font-size: 12px; color: var(--text-muted); margin-top: 14px;
    padding-top: 12px; border-top: 1px solid var(--border); line-height: 1.5;
  }
  .anki-flip-hint {
    font-size: 11px; color: var(--text-dim); margin-top: 18px;
    text-align: center; animation: pulse 2s ease-in-out infinite;
  }
  @keyframes pulse { 0%,100%{opacity:.4} 50%{opacity:.9} }

  /* Grade buttons */
  .anki-grade-row {
    display: grid; grid-template-columns: repeat(4,1fr); gap: 10px;
    margin-bottom: 20px;
  }
  @media(max-width:500px){ .anki-grade-row { grid-template-columns: repeat(2,1fr); } }
  .anki-grade-btn {
    display: flex; flex-direction: column; align-items: center; gap: 4px;
    padding: 14px 12px; border-radius: var(--radius-sm); border: 1px solid;
    background: transparent; cursor: pointer; transition: all .18s;
  }
  .anki-grade-btn:hover { transform: translateY(-2px); }
  .anki-grade-btn.again { border-color: rgba(252,92,125,.4); color: var(--hard); }
  .anki-grade-btn.hard  { border-color: rgba(252,184,92,.4); color: var(--accent4); }
  .anki-grade-btn.good  { border-color: rgba(124,92,252,.4); color: var(--accent); }
  .anki-grade-btn.easy  { border-color: rgba(92,240,176,.4); color: var(--easy); }
  .anki-grade-btn.again:hover { background: rgba(252,92,125,.1); }
  .anki-grade-btn.hard:hover  { background: rgba(252,184,92,.1); }
  .anki-grade-btn.good:hover  { background: rgba(124,92,252,.1); }
  .anki-grade-btn.easy:hover  { background: rgba(92,240,176,.1); }
  .agb-emoji    { font-size: 22px; }
  .agb-label    { font-family: 'Syne',sans-serif; font-size: 11px; font-weight: 700; }
  .agb-interval {
    font-size: 10px; opacity: .7; font-family: 'DM Mono',monospace;
  }

  /* Filter bar */
  .anki-filter-bar {
    display: flex; gap: 6px; flex-wrap: wrap; align-items: center;
    margin-bottom: 14px;
  }
  .anki-filter-btn {
    padding: 5px 12px; border-radius: 100px; border: 1px solid var(--border);
    background: transparent; color: var(--text-muted); font-size: 11px;
    font-family: 'Syne',sans-serif; font-weight: 700; cursor: pointer; transition: all .15s;
  }
  .anki-filter-btn:hover { border-color: var(--accent); color: var(--text); }
  .anki-filter-btn.active {
    background: var(--accent); border-color: var(--accent); color: #fff;
  }
  .anki-busca {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 6px 12px; color: var(--text);
    font-family: 'DM Mono',monospace; font-size: 11px; outline: none;
    transition: border-color .2s; min-width: 160px;
  }
  .anki-busca:focus { border-color: var(--accent); }
  .anki-table-wrap { overflow-x: auto; }

  /* Status badges in table */
  .anki-status-badge {
    font-size: 9px; padding: 2px 7px; border-radius: 100px;
    font-weight: 700; letter-spacing: .05em; border: 1px solid; white-space: nowrap;
  }
  .asb-atrasado { color: var(--hard);   border-color: rgba(252,92,125,.4); background: rgba(252,92,125,.1); }
  .asb-hoje     { color: var(--accent); border-color: rgba(124,92,252,.4); background: rgba(124,92,252,.1); }
  .asb-novo     { color: #5cb8fc;       border-color: rgba(92,184,252,.4); background: rgba(92,184,252,.1); }
  .asb-ok       { color: var(--easy);   border-color: rgba(92,240,176,.4); background: rgba(92,240,176,.1); }
  .asb-dominado { color: var(--easy);   border-color: rgba(92,240,176,.5); background: rgba(92,240,176,.15); }

  /* EF bar */
  .ef-bar-wrap { display: flex; align-items: center; gap: 5px; }
  .ef-bar { height: 4px; border-radius: 100px; flex: 1; background: var(--border); overflow: hidden; }
  .ef-bar-fill { height: 100%; border-radius: 100px; transition: width .4s; }



  /* ── POMODORO TIMER ── */
  .timer-section {
    margin-top: 28px; padding-top: 24px;
    border-top: 1px solid var(--border);
  }
  .timer-section-label {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .1em; text-transform: uppercase; color: var(--text-muted);
    margin-bottom: 20px; display: flex; align-items: center; gap: 8px;
  }
  .timer-section-label::after { content:''; flex:1; height:1px; background:var(--border); }

  .timer-wrap {
    display: flex; flex-direction: column; align-items: center; gap: 0;
  }

  /* SVG ring */
  .timer-ring-wrap {
    position: relative; width: 240px; height: 240px;
    margin: 0 auto 24px;
  }
  .timer-ring-wrap svg { transform: rotate(-90deg); }
  .timer-ring-bg { fill: none; stroke: var(--border); stroke-width: 8; }
  .timer-ring-fill {
    fill: none; stroke-width: 8; stroke-linecap: round;
    transition: stroke-dashoffset .8s linear, stroke .4s;
  }
  .timer-display {
    position: absolute; inset: 0;
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    gap: 4px; pointer-events: none;
  }
  .timer-phase-label {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .12em; text-transform: uppercase;
    transition: color .4s;
  }
  .timer-time {
    font-family: 'Syne', sans-serif; font-size: 46px; font-weight: 800;
    letter-spacing: -2px; line-height: 1;
    transition: color .4s;
  }
  .timer-block-count {
    font-size: 11px; color: var(--text-dim); letter-spacing: .05em;
  }

  /* Phase indicator dots */
  .timer-phases {
    display: flex; gap: 6px; margin-bottom: 20px;
  }
  .phase-dot {
    width: 8px; height: 8px; border-radius: 50%;
    background: var(--border); transition: background .3s, transform .3s;
  }
  .phase-dot.study { background: var(--accent); transform: scale(1.25); }
  .phase-dot.break { background: var(--accent3); transform: scale(1.25); }
  .phase-dot.done  { background: var(--accent4); }

  /* Controls */
  .timer-controls {
    display: flex; gap: 10px; flex-wrap: wrap; justify-content: center; margin-bottom: 20px;
  }
  .timer-btn {
    padding: 11px 28px; border-radius: var(--radius-sm); border: 1px solid;
    font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700;
    letter-spacing: .05em; cursor: pointer; transition: all .2s;
    display: flex; align-items: center; gap: 7px;
  }
  .timer-btn-start {
    background: var(--accent); border-color: var(--accent); color: #fff;
    box-shadow: 0 0 0 rgba(124,92,252,0);
  }
  .timer-btn-start:hover { box-shadow: 0 0 22px rgba(124,92,252,.45); transform: translateY(-1px); }
  .timer-btn-start.running { background: var(--hard); border-color: var(--hard); }
  .timer-btn-start.running:hover { box-shadow: 0 0 22px rgba(252,92,125,.4); }
  .timer-btn-reset {
    background: transparent; border-color: var(--border); color: var(--text-muted);
  }
  .timer-btn-reset:hover { border-color: var(--accent); color: var(--text); }
  .timer-btn-skip {
    background: transparent; border-color: var(--border); color: var(--text-muted);
    font-size: 12px; padding: 11px 18px;
  }
  .timer-btn-skip:hover { border-color: var(--accent3); color: var(--accent3); }

  /* Session blocks row */
  .timer-session-row {
    display: flex; gap: 6px; flex-wrap: wrap; justify-content: center;
    margin-bottom: 16px; max-width: 400px;
  }
  .session-block {
    width: 28px; height: 28px; border-radius: 6px; border: 1px solid var(--border);
    background: var(--surface2); display: flex; align-items: center; justify-content: center;
    font-size: 12px; transition: all .3s;
  }
  .session-block.study-done  { background: rgba(124,92,252,.25); border-color: var(--accent); }
  .session-block.break-done  { background: rgba(92,240,176,.2);  border-color: var(--accent3); }
  .session-block.long-done   { background: rgba(252,184,92,.2);  border-color: var(--accent4); }
  .session-block.active      { border-color: currentColor; box-shadow: 0 0 8px currentColor; }

  /* Sound toggle */
  .timer-sound-row {
    display: flex; align-items: center; gap: 10px; font-size: 12px; color: var(--text-muted);
  }
  .sound-toggle {
    position: relative; width: 36px; height: 20px; cursor: pointer;
  }
  .sound-toggle input { display: none; }
  .sound-track {
    position: absolute; inset: 0; border-radius: 100px;
    background: var(--border); transition: background .25s;
  }
  .sound-toggle input:checked + .sound-track { background: var(--accent3); }
  .sound-knob {
    position: absolute; top: 3px; left: 3px; width: 14px; height: 14px;
    border-radius: 50%; background: #fff; transition: transform .25s;
    box-shadow: 0 1px 3px rgba(0,0,0,.3);
  }
  .sound-toggle input:checked ~ .sound-knob { transform: translateX(16px); }

  /* ── POMODORO TIMER ── */
  .pomo-timer-section { margin-top: 56px; margin-bottom: 56px; }
  .pomo-timer-wrap {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); overflow: hidden;
  }
  .pomo-timer-header {
    display: flex; align-items: center; justify-content: space-between;
    padding: 20px 28px; border-bottom: 1px solid var(--border); flex-wrap: wrap; gap: 12px;
  }
  .pomo-timer-title {
    font-family: 'Syne', sans-serif; font-size: 14px; font-weight: 800;
    display: flex; align-items: center; gap: 8px;
  }
  .pomo-mode-tabs { display: flex; gap: 6px; }
  .pomo-mode-btn {
    padding: 5px 14px; border-radius: var(--radius-sm); border: 1px solid var(--border);
    background: transparent; font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 600;
    letter-spacing: .05em; cursor: pointer; color: var(--text-muted); transition: all .2s;
  }
  .pomo-mode-btn.active { background: var(--accent4); border-color: var(--accent4); color: #111; }
  .pomo-mode-btn.break-mode.active { background: var(--easy); border-color: var(--easy); }
  .pomo-mode-btn.longbreak-mode.active { background: var(--accent); border-color: var(--accent); color: #fff; }

  .pomo-timer-body {
    display: grid; grid-template-columns: 1fr 320px;
    min-height: 340px;
  }
  @media(max-width:820px){ .pomo-timer-body { grid-template-columns: 1fr; } }

  /* Clock face */
  .pomo-clock-area {
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    padding: 40px 32px; border-right: 1px solid var(--border); position: relative;
  }
  @media(max-width:820px){ .pomo-clock-area { border-right: none; border-bottom: 1px solid var(--border); } }

  .pomo-svg-wrap { position: relative; width: 220px; height: 220px; }
  .pomo-svg-wrap svg { width: 220px; height: 220px; }
  .pomo-time-display {
    position: absolute; inset: 0; display: flex; flex-direction: column;
    align-items: center; justify-content: center;
  }
  .pomo-time-text {
    font-family: 'Syne', sans-serif; font-size: 46px; font-weight: 800;
    letter-spacing: -2px; color: var(--text); line-height: 1;
  }
  .pomo-mode-label {
    font-size: 10px; letter-spacing: .12em; text-transform: uppercase;
    color: var(--text-muted); margin-top: 4px;
  }

  .pomo-controls { display: flex; gap: 12px; margin-top: 24px; align-items: center; }
  .pomo-ctrl-btn {
    width: 48px; height: 48px; border-radius: 50%; border: 1px solid var(--border);
    background: var(--surface2); cursor: pointer; display: flex; align-items: center;
    justify-content: center; font-size: 18px; transition: all .2s; color: var(--text);
  }
  .pomo-ctrl-btn:hover { border-color: var(--accent); transform: scale(1.05); }
  .pomo-play-btn {
    width: 64px; height: 64px; border-radius: 50%;
    background: var(--accent4); border: none; color: #111;
    font-size: 24px; cursor: pointer; transition: all .2s;
    display: flex; align-items: center; justify-content: center;
    box-shadow: 0 0 24px rgba(252,184,92,.3);
  }
  .pomo-play-btn:hover { transform: scale(1.08); box-shadow: 0 0 32px rgba(252,184,92,.5); }
  .pomo-play-btn.running { background: var(--hard); box-shadow: 0 0 24px rgba(252,92,125,.3); }
  .pomo-play-btn.break-running { background: var(--easy); box-shadow: 0 0 24px rgba(92,240,176,.3); }

  .pomo-session-counter {
    display: flex; gap: 6px; margin-top: 20px;
  }
  .pomo-dot {
    width: 10px; height: 10px; border-radius: 50%;
    background: var(--border); transition: background .3s;
  }
  .pomo-dot.done { background: var(--accent4); }
  .pomo-dot.active { background: var(--accent4); box-shadow: 0 0 8px var(--accent4); }

  /* Right panel */
  .pomo-right-panel { padding: 28px 24px; display: flex; flex-direction: column; gap: 20px; }
  .pomo-right-title {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    text-transform: uppercase; letter-spacing: .08em; color: var(--text-muted);
  }

  .pomo-settings-grid { display: flex; flex-direction: column; gap: 12px; }
  .pomo-setting-row {
    display: flex; align-items: center; justify-content: space-between; gap: 12px;
  }
  .pomo-setting-label { font-size: 12px; color: var(--text-muted); }
  .pomo-setting-val {
    display: flex; align-items: center; gap: 8px;
  }
  .pomo-step-btn {
    width: 26px; height: 26px; border-radius: 50%; border: 1px solid var(--border);
    background: var(--surface2); color: var(--text); font-size: 14px; cursor: pointer;
    display: flex; align-items: center; justify-content: center; transition: all .15s;
  }
  .pomo-step-btn:hover { border-color: var(--accent); background: rgba(124,92,252,.1); }
  .pomo-min-val {
    font-family: 'Syne', sans-serif; font-weight: 700; font-size: 16px;
    color: var(--text); min-width: 32px; text-align: center;
  }

  .pomo-log { display: flex; flex-direction: column; gap: 6px; max-height: 120px; overflow-y: auto; }
  .pomo-log-item {
    font-size: 11px; color: var(--text-dim); padding: 5px 10px;
    background: var(--surface2); border-radius: var(--radius-sm);
    border-left: 2px solid var(--border);
  }
  .pomo-log-item.study { border-left-color: var(--accent4); color: var(--text-muted); }
  .pomo-log-item.brk   { border-left-color: var(--easy); }
  .pomo-log-item.long  { border-left-color: var(--accent); }

  /* ── DAY SUBJECT MANAGER ── */
  .day-subjects-section { margin-bottom: 28px; }
  .dsm-header {
    display: flex; align-items: center; justify-content: space-between;
    margin-bottom: 14px; flex-wrap: wrap; gap: 10px;
  }
  .dsm-title {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    text-transform: uppercase; letter-spacing: .1em; color: var(--text-muted);
    display: flex; align-items: center; gap: 8px;
  }
  .dsm-add-btn {
    display: flex; align-items: center; gap: 6px;
    padding: 7px 16px; border-radius: var(--radius-sm);
    border: 1px dashed var(--border); background: transparent;
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    color: var(--text-dim); cursor: pointer; transition: all .2s;
    letter-spacing: .04em;
  }
  .dsm-add-btn:hover { border-color: var(--accent); color: var(--accent); }
  .dsm-saved-badge {
    font-size: 10px; color: var(--easy); background: rgba(92,240,176,.1);
    border: 1px solid rgba(92,240,176,.25); border-radius: 100px;
    padding: 3px 10px; opacity: 0; transition: opacity .4s;
    font-family: 'Syne', sans-serif; font-weight: 700;
  }

  /* Subject card */
  .subj-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); overflow: hidden; margin-bottom: 14px;
    transition: border-color .2s; position: relative;
  }
  .subj-card:hover { border-color: rgba(124,92,252,.3); }
  .subj-card-bar { height: 3px; }
  .subj-card-top {
    display: flex; align-items: center; gap: 12px;
    padding: 14px 18px 10px; border-bottom: 1px solid var(--border);
  }
  .subj-card-icon {
    width: 30px; height: 30px; border-radius: 8px;
    display: flex; align-items: center; justify-content: center; font-size: 14px;
    flex-shrink: 0;
  }
  .subj-card-name-input {
    font-family: 'Syne', sans-serif; font-weight: 700; font-size: 15px;
    color: var(--text); background: transparent; border: none; outline: none;
    flex: 1; min-width: 0;
  }
  .subj-card-name-input::placeholder { color: var(--text-dim); font-weight: 400; }
  .subj-card-actions { display: flex; align-items: center; gap: 8px; flex-shrink: 0; }
  .subj-card-check {
    width: 26px; height: 26px; border-radius: 50%;
    border: 2px solid var(--border); background: transparent;
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; font-size: 12px; transition: all .2s; flex-shrink: 0;
  }
  .subj-card-check.done { background: var(--easy); border-color: var(--easy); color: #0a0a0f; }
  .subj-card-del {
    width: 24px; height: 24px; border-radius: 50%;
    border: 1px solid var(--border); background: transparent;
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; font-size: 11px; color: var(--text-dim);
    transition: all .2s; flex-shrink: 0;
  }
  .subj-card-del:hover { border-color: var(--hard); color: var(--hard); background: rgba(252,92,125,.1); }
  .subj-card-collapse {
    width: 24px; height: 24px; border-radius: 50%;
    border: 1px solid var(--border); background: transparent;
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; font-size: 10px; color: var(--text-dim);
    transition: all .2s; flex-shrink: 0;
  }
  .subj-card-collapse:hover { border-color: var(--accent); color: var(--accent); }

  .subj-card-body { padding: 16px 18px; display: flex; flex-direction: column; gap: 12px; }
  .subj-card-body.collapsed { display: none; }

  .subj-save-btn {
    display: flex; align-items: center; gap: 6px;
    padding: 8px 20px; border-radius: var(--radius-sm);
    border: 1px solid var(--accent); background: rgba(124,92,252,.12);
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    color: var(--accent); cursor: pointer; transition: all .2s;
    letter-spacing: .04em; align-self: flex-start;
  }
  .subj-save-btn:hover { background: var(--accent); color: #fff; }


  .fc-section { margin-top: 56px; }
  .fc-top-bar {
    display: flex; align-items: center; justify-content: space-between;
    flex-wrap: wrap; gap: 14px; margin-bottom: 24px;
  }
  .fc-deck-tabs { display: flex; gap: 8px; flex-wrap: wrap; }
  .fc-deck-tab {
    padding: 7px 16px; border-radius: var(--radius-sm);
    border: 1px solid var(--border); background: var(--surface2);
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 600;
    letter-spacing: .06em; text-transform: uppercase;
    color: var(--text-muted); cursor: pointer; transition: all .2s;
    display: flex; align-items: center; gap: 7px;
  }
  .fc-deck-tab:hover { border-color: var(--accent); color: var(--text); }
  .fc-deck-tab.active { background: var(--accent); border-color: var(--accent); color: #fff; box-shadow: 0 0 18px rgba(124,92,252,.35); }
  .fc-deck-tab .fc-count {
    background: rgba(255,255,255,.18); border-radius: 100px;
    padding: 1px 7px; font-size: 10px;
  }
  .fc-actions { display: flex; gap: 8px; flex-wrap: wrap; }
  .fc-btn {
    padding: 8px 18px; border-radius: var(--radius-sm); border: 1px solid;
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .06em; cursor: pointer; transition: all .2s;
  }
  .fc-btn-primary { background: var(--accent); border-color: var(--accent); color: #fff; }
  .fc-btn-primary:hover { box-shadow: 0 0 18px rgba(124,92,252,.4); transform: translateY(-1px); }
  .fc-btn-ghost { background: transparent; border-color: var(--border); color: var(--text-muted); }
  .fc-btn-ghost:hover { border-color: var(--accent); color: var(--text); }
  .fc-btn-danger { background: transparent; border-color: rgba(252,92,125,.4); color: var(--hard); }
  .fc-btn-danger:hover { background: rgba(252,92,125,.1); }

  /* ── STUDY MODE ── */
  .fc-study-area { display: none; }
  .fc-study-area.active { display: block; }
  .fc-progress-bar-wrap { height: 4px; background: var(--border); border-radius: 100px; margin-bottom: 24px; overflow: hidden; }
  .fc-progress-bar { height: 100%; background: linear-gradient(90deg, var(--accent), var(--accent3)); border-radius: 100px; transition: width .4s ease; }
  .fc-progress-label { display: flex; justify-content: space-between; font-size: 11px; color: var(--text-dim); margin-bottom: 8px; }

  /* The card */
  .fc-card-stage { perspective: 1200px; margin: 0 auto 28px; max-width: 680px; height: 320px; cursor: pointer; }
  .fc-card-inner {
    width: 100%; height: 100%; position: relative;
    transform-style: preserve-3d; transition: transform .55s cubic-bezier(.4,0,.2,1);
  }
  .fc-card-inner.flipped { transform: rotateY(180deg); }
  .fc-card-face {
    position: absolute; inset: 0; backface-visibility: hidden;
    border-radius: var(--radius); border: 1px solid var(--border);
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    padding: 36px 40px; text-align: center;
  }
  .fc-card-front { background: var(--surface); }
  .fc-card-back  { background: var(--surface2); transform: rotateY(180deg); border-color: var(--accent); }
  .fc-card-tag {
    position: absolute; top: 16px; left: 20px;
    font-size: 10px; letter-spacing: .08em; text-transform: uppercase;
    color: var(--text-dim); font-family: 'Syne', sans-serif;
  }
  .fc-card-back .fc-card-tag { color: var(--accent); }
  .fc-card-q {
    font-family: 'Fraunces', serif; font-size: 22px; font-weight: 600;
    color: var(--text); line-height: 1.5;
  }
  .fc-card-a {
    font-family: 'DM Mono', monospace; font-size: 15px;
    color: var(--text); line-height: 1.7;
  }
  .fc-flip-hint {
    position: absolute; bottom: 16px; right: 20px;
    font-size: 10px; color: var(--text-dim); letter-spacing: .05em;
    display: flex; align-items: center; gap: 4px;
  }
  .fc-card-deck-badge {
    position: absolute; top: 16px; right: 20px;
    font-size: 10px; padding: 3px 9px; border-radius: 100px;
    border: 1px solid var(--border); color: var(--text-dim);
  }

  /* Rating row */
  .fc-rating-row {
    display: flex; gap: 10px; justify-content: center; flex-wrap: wrap;
    margin-bottom: 24px; opacity: 0; transform: translateY(8px);
    transition: opacity .3s .1s, transform .3s .1s; pointer-events: none;
  }
  .fc-rating-row.visible { opacity: 1; transform: none; pointer-events: all; }
  .fc-rate-btn {
    display: flex; flex-direction: column; align-items: center; gap: 4px;
    padding: 12px 22px; border-radius: var(--radius-sm); border: 1px solid;
    background: transparent; cursor: pointer; transition: all .2s;
    font-family: 'DM Mono', monospace;
  }
  .fc-rate-btn .fc-rate-label { font-size: 12px; font-weight: 600; }
  .fc-rate-btn .fc-rate-interval { font-size: 10px; opacity: .7; }
  .fc-rate-btn:hover { transform: translateY(-2px); }
  .fc-rate-again { border-color: rgba(252,92,125,.5); color: var(--hard); }
  .fc-rate-again:hover { background: rgba(252,92,125,.12); }
  .fc-rate-hard  { border-color: rgba(252,184,92,.5);  color: var(--accent4); }
  .fc-rate-hard:hover  { background: rgba(252,184,92,.12); }
  .fc-rate-good  { border-color: rgba(124,92,252,.5);  color: var(--accent); }
  .fc-rate-good:hover  { background: rgba(124,92,252,.12); }
  .fc-rate-easy  { border-color: rgba(92,240,176,.5);  color: var(--easy); }
  .fc-rate-easy:hover  { background: rgba(92,240,176,.12); }

  /* Session stats */
  .fc-session-stats {
    display: grid; grid-template-columns: repeat(4,1fr); gap: 12px; margin-bottom: 28px;
  }
  @media(max-width:600px){ .fc-session-stats { grid-template-columns: repeat(2,1fr); } }
  .fc-stat {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 12px 16px;
    display: flex; flex-direction: column; gap: 4px;
  }
  .fc-stat-label { font-size: 10px; color: var(--text-dim); text-transform: uppercase; letter-spacing: .07em; }
  .fc-stat-val { font-family: 'Syne', sans-serif; font-size: 22px; font-weight: 800; }

  /* Finished screen */
  .fc-done-screen {
    display: none; text-align: center; padding: 60px 24px;
    background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius);
  }
  .fc-done-screen.active { display: block; }
  .fc-done-emoji { font-size: 56px; margin-bottom: 16px; }
  .fc-done-title { font-family: 'Syne', sans-serif; font-size: 26px; font-weight: 800; margin-bottom: 8px; }
  .fc-done-sub { font-size: 13px; color: var(--text-muted); margin-bottom: 28px; }
  .fc-done-results {
    display: inline-flex; gap: 24px; flex-wrap: wrap; justify-content: center;
    margin-bottom: 28px;
  }
  .fc-done-stat { display: flex; flex-direction: column; align-items: center; gap: 4px; }
  .fc-done-stat-val { font-family: 'Syne', sans-serif; font-size: 32px; font-weight: 800; }
  .fc-done-stat-label { font-size: 11px; color: var(--text-muted); text-transform: uppercase; letter-spacing: .07em; }

  /* ── CREATE CARD PANEL ── */
  .fc-create-panel {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 28px; margin-bottom: 28px;
    display: none;
  }
  .fc-create-panel.active { display: block; }
  .fc-create-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
  @media(max-width:700px){ .fc-create-grid { grid-template-columns: 1fr; } }
  .fc-field { display: flex; flex-direction: column; gap: 6px; }
  .fc-field label { font-size: 10px; color: var(--text-dim); text-transform: uppercase; letter-spacing: .08em; }
  .fc-field textarea, .fc-field select, .fc-field input {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 10px 14px;
    color: var(--text); font-family: 'DM Mono', monospace; font-size: 13px;
    outline: none; transition: border-color .2s; width: 100%; resize: vertical;
  }
  .fc-field textarea:focus, .fc-field select:focus, .fc-field input:focus { border-color: var(--accent); }
  .fc-field select option { background: var(--surface2); }

  /* ── CARD LIST ── */
  .fc-list-section { margin-top: 8px; }
  .fc-list-header {
    display: flex; align-items: center; justify-content: space-between;
    margin-bottom: 14px; flex-wrap: wrap; gap: 8px;
  }
  .fc-list-title { font-family: 'Syne', sans-serif; font-size: 12px; font-weight: 700; color: var(--text-muted); text-transform: uppercase; letter-spacing: .08em; }
  .fc-search {
    background: var(--surface2); border: 1px solid var(--border); border-radius: var(--radius-sm);
    padding: 7px 14px; color: var(--text); font-family: 'DM Mono', monospace; font-size: 12px;
    outline: none; transition: border-color .2s; min-width: 220px;
  }
  .fc-search:focus { border-color: var(--accent); }
  .fc-grid {
    display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); gap: 14px;
  }
  .fc-item {
    background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-sm);
    padding: 16px 18px; display: flex; flex-direction: column; gap: 10px;
    transition: border-color .2s; position: relative; overflow: hidden;
  }
  .fc-item:hover { border-color: var(--accent); }
  .fc-item-bar { position: absolute; top: 0; left: 0; right: 0; height: 2px; }
  .fc-item-deck { font-size: 10px; text-transform: uppercase; letter-spacing: .07em; color: var(--text-dim); }
  .fc-item-q { font-family: 'Fraunces', serif; font-size: 14px; font-weight: 600; color: var(--text); line-height: 1.4; }
  .fc-item-a { font-size: 11px; color: var(--text-muted); line-height: 1.5; }
  .fc-item-meta { display: flex; align-items: center; justify-content: space-between; gap: 8px; }
  .fc-item-ef { font-size: 10px; color: var(--text-dim); }
  .fc-item-next { font-size: 10px; padding: 2px 8px; border-radius: 100px; }
  .fc-item-del {
    position: absolute; top: 10px; right: 12px;
    width: 20px; height: 20px; border-radius: 50%; background: rgba(252,92,125,.1);
    border: none; color: var(--hard); font-size: 12px; cursor: pointer;
    display: flex; align-items: center; justify-content: center; opacity: 0; transition: opacity .2s;
  }
  .fc-item:hover .fc-item-del { opacity: 1; }

  /* Empty state */
  .fc-empty {
    text-align: center; padding: 48px 24px; color: var(--text-dim);
    border: 1px dashed var(--border); border-radius: var(--radius);
  }
  .fc-empty-icon { font-size: 40px; margin-bottom: 12px; }
  .fc-empty-msg { font-size: 13px; }

  /* ── DAY SUBTITLE INPUT FIX ── */
  .day-subtitle-input {
    font-size: 11px !important; color: var(--text-muted) !important;
    background: transparent !important; border: none !important; outline: none !important;
    font-family: 'DM Mono', monospace !important;
    width: 100% !important; min-width: 160px; max-width: 340px;
    cursor: text; pointer-events: all;
  }
  .day-subtitle-input:focus { color: var(--text) !important; }
  .day-subtitle-wrap {
    display: flex; align-items: center; gap: 6px;
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: 100px; padding: 4px 14px; cursor: text;
    transition: border-color .2s; flex-shrink: 0;
  }
  .day-subtitle-wrap:focus-within { border-color: var(--accent); }

  /* ── FLOATING POMODORO TIMER ── */
  #pomodoroFloat {
    position: fixed; bottom: 28px; right: 28px; z-index: 9999;
    width: 300px;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 20px;
    box-shadow: 0 8px 48px rgba(0,0,0,.6), 0 0 0 1px rgba(124,92,252,.15);
    overflow: hidden;
    transition: transform .3s cubic-bezier(.4,0,.2,1), opacity .3s;
    transform-origin: bottom right;
  }
  #pomodoroFloat.hidden { transform: scale(.85) translateY(20px); opacity:0; pointer-events:none; }

  .pf-header {
    display: flex; align-items: center; justify-content: space-between;
    padding: 13px 16px 10px;
    border-bottom: 1px solid var(--border);
    background: var(--surface2);
    cursor: move; user-select: none;
  }
  .pf-title {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .08em; text-transform: uppercase; color: var(--text-muted);
    display: flex; align-items: center; gap: 7px;
  }
  .pf-phase-dot {
    width: 7px; height: 7px; border-radius: 50%; background: var(--accent);
    box-shadow: 0 0 7px var(--accent); transition: background .4s, box-shadow .4s;
  }
  .pf-close {
    width: 22px; height: 22px; border-radius: 50%; border: 1px solid var(--border);
    background: transparent; color: var(--text-muted); cursor: pointer;
    display: flex; align-items: center; justify-content: center; font-size: 10px;
    transition: all .2s;
  }
  .pf-close:hover { background: rgba(252,92,125,.15); color: var(--hard); border-color: var(--hard); }

  .pf-body { padding: 18px 20px 16px; }

  .pf-ring-wrap {
    position: relative; width: 148px; height: 148px;
    margin: 0 auto 16px; cursor: pointer;
  }
  .pf-ring-wrap svg { display: block; }
  .pf-ring-bg  { fill: none; stroke: var(--border); stroke-width: 9; }
  .pf-ring-fill {
    fill: none; stroke-width: 9; stroke-linecap: round;
    transform: rotate(-90deg); transform-origin: 50% 50%;
    transition: stroke-dashoffset .95s linear, stroke .4s;
  }
  .pf-ring-center {
    position: absolute; inset: 0;
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    gap: 1px; pointer-events: none;
  }
  .pf-phase-label {
    font-family: 'Syne', sans-serif; font-size: 9px; font-weight: 700;
    letter-spacing: .14em; text-transform: uppercase; color: var(--accent);
    transition: color .4s;
  }
  .pf-time {
    font-family: 'Syne', sans-serif; font-size: 32px; font-weight: 800;
    color: var(--text); letter-spacing: -1px; line-height: 1;
  }
  .pf-block-label { font-size: 10px; color: var(--text-dim); letter-spacing: .04em; margin-top: 2px; }

  .pf-controls {
    display: flex; gap: 7px; justify-content: center; margin-bottom: 12px;
  }
  .pf-btn {
    padding: 8px 14px; border-radius: var(--radius-sm); border: 1px solid;
    font-family: 'Syne', sans-serif; font-size: 10px; font-weight: 700;
    letter-spacing: .05em; cursor: pointer; transition: all .2s;
  }
  .pf-btn-start { background: var(--accent); border-color: var(--accent); color: #fff; flex: 1; }
  .pf-btn-start:hover { box-shadow: 0 0 14px rgba(124,92,252,.5); }
  .pf-btn-skip  { background: transparent; border-color: var(--border); color: var(--text-muted); }
  .pf-btn-skip:hover { border-color: var(--accent4); color: var(--accent4); }
  .pf-btn-reset { background: transparent; border-color: var(--border); color: var(--text-muted); }
  .pf-btn-reset:hover { border-color: var(--hard); color: var(--hard); }

  .pf-dots { display: flex; gap: 5px; justify-content: center; margin-bottom: 12px; }
  .pf-dot {
    width: 9px; height: 9px; border-radius: 50%;
    border: 1.5px solid var(--border); background: transparent; transition: all .3s;
  }
  .pf-dot.done { background: var(--accent); border-color: var(--accent); box-shadow: 0 0 4px var(--accent); }
  .pf-dot.active { border-color: var(--accent); animation: pfPulse 1.4s infinite; }
  @keyframes pfPulse { 0%,100%{box-shadow:0 0 0 0 rgba(124,92,252,.5)} 50%{box-shadow:0 0 0 4px rgba(124,92,252,0)} }

  .pf-sound-row {
    display: flex; align-items: center; justify-content: space-between;
    padding-top: 10px; border-top: 1px solid var(--border);
    font-size: 10px; color: var(--text-muted);
  }
  .pf-toggle { position: relative; width: 32px; height: 17px; cursor: pointer; display: inline-block; }
  .pf-toggle input { opacity:0; width:0; height:0; position:absolute; }
  .pf-track {
    position: absolute; inset: 0; background: var(--border);
    border-radius: 100px; transition: background .2s;
  }
  .pf-knob {
    position: absolute; width: 11px; height: 11px; border-radius: 50%;
    background: var(--text-muted); top: 3px; left: 3px; transition: transform .2s, background .2s;
  }
  .pf-toggle input:checked ~ .pf-track { background: var(--easy); }
  .pf-toggle input:checked ~ .pf-knob { transform: translateX(15px); background: #0a0a0f; }

  /* Phase color overrides */
  #pomodoroFloat.phase-break .pf-phase-dot { background:var(--easy); box-shadow:0 0 7px var(--easy); }
  #pomodoroFloat.phase-break .pf-phase-label { color:var(--easy) !important; }
  #pomodoroFloat.phase-break .pf-ring-fill { stroke:var(--easy) !important; }
  #pomodoroFloat.phase-break .pf-btn-start { background:var(--easy); border-color:var(--easy); color:#0a0a0f; }
  #pomodoroFloat.phase-longbreak .pf-phase-dot { background:var(--accent4); box-shadow:0 0 7px var(--accent4); }
  #pomodoroFloat.phase-longbreak .pf-phase-label { color:var(--accent4) !important; }
  #pomodoroFloat.phase-longbreak .pf-ring-fill { stroke:var(--accent4) !important; }
  #pomodoroFloat.phase-longbreak .pf-btn-start { background:var(--accent4); border-color:var(--accent4); color:#0a0a0f; }

  /* FAB */
  #pomodoroFab {
    position: fixed; bottom: 28px; right: 28px; z-index: 9998;
    width: 52px; height: 52px; border-radius: 50%;
    background: var(--accent); border: none; cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    font-size: 20px; box-shadow: 0 4px 20px rgba(124,92,252,.55);
    transition: all .2s;
  }
  #pomodoroFab:hover { transform: scale(1.1); box-shadow: 0 6px 28px rgba(124,92,252,.75); }
  #pomodoroFab.hidden { display: none; }
  .fab-badge {
    position: absolute; top: -4px; right: -4px;
    width: 18px; height: 18px; border-radius: 50%;
    background: var(--easy); color: #0a0a0f; font-size: 9px;
    font-family: 'Syne', sans-serif; font-weight: 800;
    display: flex; align-items: center; justify-content: center;
    border: 2px solid var(--bg); display: none;
  }

  /* ══════════════════════════════════════════
     REVISION TRACKER (Anki-style per content)
  ══════════════════════════════════════════ */
  .rt-section { margin-top: 64px; }

  /* Top controls */
  .rt-topbar {
    display: flex; align-items: center; justify-content: space-between;
    flex-wrap: wrap; gap: 14px; margin-bottom: 24px;
  }
  .rt-subject-tabs { display: flex; gap: 8px; flex-wrap: wrap; }
  .rt-stab {
    padding: 7px 16px; border-radius: var(--radius-sm);
    border: 1px solid var(--border); background: var(--surface2);
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .06em; text-transform: uppercase;
    color: var(--text-muted); cursor: pointer; transition: all .2s;
    display: flex; align-items: center; gap: 7px;
  }
  .rt-stab:hover { border-color: var(--accent); color: var(--text); }
  .rt-stab.active { color: #fff; box-shadow: 0 0 16px rgba(0,0,0,.3); }
  .rt-stab .rt-stab-dot { width: 7px; height: 7px; border-radius: 50%; flex-shrink: 0; }
  .rt-stab .rt-badge {
    background: rgba(255,255,255,.15); border-radius: 100px;
    padding: 1px 7px; font-size: 10px;
  }
  .rt-stab .rt-due-badge {
    background: rgba(252,92,125,.35); color: #fff; border-radius: 100px;
    padding: 1px 7px; font-size: 10px;
  }
  .rt-actions { display: flex; gap: 8px; flex-wrap: wrap; }
  .rt-btn {
    padding: 8px 18px; border-radius: var(--radius-sm); border: 1px solid;
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .05em; cursor: pointer; transition: all .2s; white-space: nowrap;
  }
  .rt-btn-primary { background: var(--accent); border-color: var(--accent); color: #fff; }
  .rt-btn-primary:hover { box-shadow: 0 0 16px rgba(124,92,252,.4); transform: translateY(-1px); }
  .rt-btn-ghost { background: transparent; border-color: var(--border); color: var(--text-muted); }
  .rt-btn-ghost:hover { border-color: var(--accent); color: var(--text); }

  /* Add content form */
  .rt-add-form {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 24px 28px; margin-bottom: 24px;
    display: none;
  }
  .rt-add-form.active { display: block; }
  .rt-add-form-title { font-family:'Syne',sans-serif; font-weight:700; font-size:14px; margin-bottom:18px; color:var(--text); }
  .rt-form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; }
  @media(max-width:700px){ .rt-form-grid { grid-template-columns: 1fr; } }
  .rt-form-field { display: flex; flex-direction: column; gap: 5px; }
  .rt-form-field label { font-size: 10px; color: var(--text-dim); text-transform: uppercase; letter-spacing: .08em; }
  .rt-form-field input, .rt-form-field select, .rt-form-field textarea {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 9px 13px;
    color: var(--text); font-family: 'DM Mono', monospace; font-size: 13px;
    outline: none; transition: border-color .2s; width: 100%; resize: none;
  }
  .rt-form-field input:focus, .rt-form-field select:focus, .rt-form-field textarea:focus { border-color: var(--accent); }
  .rt-form-field select option { background: var(--surface2); }

  /* Week day selector */
  .rt-day-selector { display: flex; gap: 6px; flex-wrap: wrap; }
  .rt-day-btn {
    width: 38px; height: 38px; border-radius: 50%; border: 1px solid var(--border);
    background: transparent; font-family: 'Syne', sans-serif; font-size: 10px;
    font-weight: 700; cursor: pointer; color: var(--text-muted); transition: all .2s;
    display: flex; align-items: center; justify-content: center;
  }
  .rt-day-btn:hover { border-color: var(--accent); color: var(--text); }
  .rt-day-btn.sel { background: var(--accent); border-color: var(--accent); color: #fff; }

  /* Stats summary bar */
  .rt-stats-bar {
    display: grid; grid-template-columns: repeat(4,1fr); gap: 12px; margin-bottom: 24px;
  }
  @media(max-width:600px){ .rt-stats-bar { grid-template-columns: repeat(2,1fr); } }
  .rt-stat-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 14px 18px;
    display: flex; flex-direction: column; gap: 4px;
  }
  .rt-stat-label { font-size: 10px; color: var(--text-dim); text-transform: uppercase; letter-spacing: .07em; }
  .rt-stat-val { font-family:'Syne',sans-serif; font-size: 24px; font-weight: 800; }

  /* Content cards grid */
  .rt-grid {
    display: grid; grid-template-columns: repeat(auto-fill, minmax(320px,1fr)); gap: 16px;
  }

  /* Individual content card */
  .rt-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); overflow: hidden; transition: border-color .2s;
    position: relative;
  }
  .rt-card:hover { border-color: rgba(124,92,252,.4); }
  .rt-card.due { border-color: rgba(92,240,176,.5); box-shadow: 0 0 0 1px rgba(92,240,176,.15); }
  .rt-card.overdue { border-color: rgba(252,92,125,.5); box-shadow: 0 0 0 1px rgba(252,92,125,.1); }
  .rt-card-strip { height: 3px; width: 100%; }
  .rt-card-head { padding: 16px 18px 10px; display: flex; align-items: flex-start; justify-content: space-between; gap: 10px; }
  .rt-card-subject { font-size: 10px; text-transform: uppercase; letter-spacing: .08em; font-family:'Syne',sans-serif; font-weight:700; }
  .rt-card-title { font-family:'Fraunces',serif; font-size:16px; font-weight:600; color:var(--text); line-height:1.4; margin-top:4px; }
  .rt-card-desc { font-size:12px; color:var(--text-muted); line-height:1.6; margin-top:4px; }
  .rt-card-del {
    width:20px; height:20px; border-radius:50%; border:none; background:transparent;
    color:var(--text-dim); font-size:13px; cursor:pointer; flex-shrink:0;
    display:flex; align-items:center; justify-content:center; transition:color .2s;
    opacity:0;
  }
  .rt-card:hover .rt-card-del { opacity:1; }
  .rt-card-del:hover { color:var(--hard); }

  /* Anki info row */
  .rt-card-anki {
    display: flex; align-items: center; gap: 8px; flex-wrap: wrap;
    padding: 8px 18px; border-top: 1px solid rgba(42,42,58,.6);
    background: var(--surface2);
  }
  .rt-anki-chip {
    padding: 3px 9px; border-radius: 100px; font-size: 10px;
    border: 1px solid; display: flex; align-items: center; gap: 4px;
  }

  /* Day chips */
  .rt-card-days { display: flex; gap: 5px; padding: 8px 18px 6px; flex-wrap: wrap; }
  .rt-day-chip {
    width: 32px; height: 32px; border-radius: 50%; border: 1px solid var(--border);
    display: flex; align-items: center; justify-content: center;
    font-family: 'Syne', sans-serif; font-size: 9px; font-weight: 700;
    color: var(--text-dim); letter-spacing: .03em;
    transition: all .2s;
  }
  .rt-day-chip.scheduled { border-color: var(--accent); color: var(--accent); background: rgba(124,92,252,.1); }
  .rt-day-chip.today { background: var(--accent); border-color: var(--accent); color: #fff; box-shadow: 0 0 8px rgba(124,92,252,.4); }

  /* Next revision info */
  .rt-card-next { padding: 10px 18px 14px; display: flex; align-items: center; justify-content: space-between; flex-wrap: wrap; gap: 10px; }
  .rt-next-label { font-size: 11px; color: var(--text-dim); }
  .rt-next-date { font-family:'Syne',sans-serif; font-size:13px; font-weight:700; }

  /* THE BIG BUTTON */
  .rt-studied-btn {
    display: flex; align-items: center; gap: 8px;
    padding: 9px 16px; border-radius: var(--radius-sm); border: 1px solid;
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .05em; cursor: pointer; transition: all .25s;
    background: transparent;
  }
  .rt-studied-btn.not-done { border-color: rgba(92,240,176,.4); color: var(--easy); }
  .rt-studied-btn.not-done:hover { background: rgba(92,240,176,.12); transform: scale(1.02); }
  .rt-studied-btn.done { background: rgba(92,240,176,.15); border-color: var(--easy); color: var(--easy); }

  /* Rating panel (shows after marking as studied) */
  .rt-rate-panel {
    padding: 10px 18px 14px; border-top: 1px solid rgba(42,42,58,.6);
    display: none;
  }
  .rt-rate-panel.active { display: block; }
  .rt-rate-label { font-size: 10px; color: var(--text-dim); text-transform: uppercase; letter-spacing: .07em; margin-bottom: 8px; }
  .rt-rate-btns { display: flex; gap: 6px; flex-wrap: wrap; }
  .rt-rbtn {
    flex: 1; min-width: 60px; padding: 8px 6px; border-radius: var(--radius-sm);
    border: 1px solid; background: transparent; cursor: pointer; transition: all .2s;
    font-family: 'DM Mono', monospace; font-size: 11px; text-align: center;
    display: flex; flex-direction: column; align-items: center; gap: 2px;
  }
  .rt-rbtn .rbl { font-weight: 600; font-size: 12px; }
  .rt-rbtn .rbi { font-size: 10px; opacity: .7; }
  .rt-rbtn:hover { transform: translateY(-2px); }
  .rt-rbtn.again { border-color: rgba(252,92,125,.5); color: var(--hard); }
  .rt-rbtn.again:hover { background: rgba(252,92,125,.1); }
  .rt-rbtn.hard  { border-color: rgba(252,184,92,.5);  color: var(--accent4); }
  .rt-rbtn.hard:hover  { background: rgba(252,184,92,.1); }
  .rt-rbtn.good  { border-color: rgba(124,92,252,.5);  color: var(--accent); }
  .rt-rbtn.good:hover  { background: rgba(124,92,252,.1); }
  .rt-rbtn.easy  { border-color: rgba(92,240,176,.5);  color: var(--easy); }
  .rt-rbtn.easy:hover  { background: rgba(92,240,176,.1); }

  /* History log */
  .rt-history {
    padding: 0 18px 14px; display: none;
  }
  .rt-history.active { display: block; }
  .rt-history-title { font-size:10px; color:var(--text-dim); text-transform:uppercase; letter-spacing:.07em; margin-bottom:6px; }
  .rt-history-row {
    display: flex; align-items: center; gap: 8px; font-size: 11px;
    color: var(--text-muted); padding: 3px 0; border-bottom: 1px solid rgba(42,42,58,.4);
  }
  .rt-history-row:last-child { border-bottom: none; }

  /* Empty state */
  .rt-empty {
    grid-column: 1/-1; text-align:center; padding:56px 24px;
    border: 1px dashed var(--border); border-radius: var(--radius);
    color: var(--text-dim);
  }
  .rt-empty-icon { font-size:42px; margin-bottom:12px; }
  .rt-empty-msg { font-size:13px; line-height: 1.6; }

  /* ── SAVE BUTTON & HISTORY ── */
  .day-save-bar {
    display: flex; align-items: center; justify-content: space-between;
    flex-wrap: wrap; gap: 12px;
    margin-top: 20px; padding: 16px 20px;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); border-left: 3px solid var(--easy);
  }
  .day-save-info { font-size: 12px; color: var(--text-muted); }
  .day-save-info strong { color: var(--text); }
  .btn-save-day {
    display: flex; align-items: center; gap: 8px;
    padding: 10px 22px; border-radius: var(--radius-sm);
    background: var(--easy); border: none; color: #0a0a0f;
    font-family: 'Syne', sans-serif; font-size: 12px; font-weight: 800;
    letter-spacing: .06em; text-transform: uppercase;
    cursor: pointer; transition: all .2s;
  }
  .btn-save-day:hover { transform: translateY(-2px); box-shadow: 0 4px 20px rgba(92,240,176,.45); }
  .btn-save-day:active { transform: scale(.97); }
  .history-section { margin-top: 48px; }
  .history-empty {
    text-align: center; padding: 48px 24px;
    border: 1px dashed var(--border); border-radius: var(--radius);
    color: var(--text-dim); font-size: 13px;
  }
  .history-empty-icon { font-size: 36px; margin-bottom: 10px; }
  .history-grid {
    display: grid; grid-template-columns: repeat(auto-fill, minmax(340px,1fr));
    gap: 16px;
  }
  .history-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); overflow: hidden;
    transition: border-color .2s; animation: fadeUp .3s ease;
  }
  .history-card:hover { border-color: var(--accent); }
  .history-card-head {
    display: flex; align-items: center; justify-content: space-between;
    padding: 14px 18px 12px; border-bottom: 1px solid var(--border);
  }
  .history-card-day { display: flex; align-items: center; gap: 8px; }
  .history-day-dot { width: 8px; height: 8px; border-radius: 50%; flex-shrink: 0; }
  .history-day-name { font-family: 'Syne', sans-serif; font-size: 14px; font-weight: 800; }
  .history-timestamp { font-size: 10px; color: var(--text-dim); letter-spacing: .04em; }
  .history-card-actions { display: flex; gap: 6px; }
  .btn-hist-edit {
    padding: 5px 12px; border-radius: var(--radius-sm);
    border: 1px solid rgba(124,92,252,.4); background: transparent;
    color: var(--accent); font-family: 'Syne', sans-serif; font-size: 10px;
    font-weight: 700; letter-spacing: .06em; cursor: pointer; transition: all .2s;
  }
  .btn-hist-edit:hover { background: rgba(124,92,252,.15); }
  .btn-hist-del {
    padding: 5px 10px; border-radius: var(--radius-sm);
    border: 1px solid rgba(252,92,125,.3); background: transparent;
    color: var(--hard); font-size: 11px; cursor: pointer; transition: all .2s;
  }
  .btn-hist-del:hover { background: rgba(252,92,125,.1); }
  .history-card-body { padding: 14px 18px; display: flex; flex-direction: column; gap: 10px; }
  .history-materia {
    display: flex; align-items: flex-start; gap: 10px;
    padding: 10px 12px; border-radius: var(--radius-sm);
    background: var(--surface2); border: 1px solid var(--border);
  }
  .hm-color-bar { width: 3px; border-radius: 2px; align-self: stretch; flex-shrink: 0; min-height: 20px; }
  .hm-info { flex: 1; display: flex; flex-direction: column; gap: 4px; min-width: 0; }
  .hm-name { font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700; color: var(--text); }
  .hm-meta { display: flex; flex-wrap: wrap; gap: 6px; }
  .hm-pill {
    font-size: 10px; padding: 2px 8px; border-radius: 100px;
    background: var(--surface); border: 1px solid var(--border); color: var(--text-muted);
  }
  .hm-notes { font-size: 11px; color: var(--text-muted); line-height: 1.5; margin-top: 2px; }
  .history-day-subtitle { font-size: 11px; color: var(--text-muted); padding: 0 18px 14px; }
  .hist-filter-row {
    display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 20px; align-items: center;
  }
  .hist-filter-btn {
    padding: 5px 14px; border-radius: 100px; border: 1px solid var(--border);
    background: var(--surface2); color: var(--text-muted);
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 600;
    cursor: pointer; transition: all .2s;
  }
  .hist-filter-btn.active { background: var(--accent); border-color: var(--accent); color: #fff; }
  .hist-clear-all {
    margin-left: auto; padding: 5px 14px; border-radius: 100px;
    border: 1px solid rgba(252,92,125,.3); background: transparent;
    color: var(--hard); font-family: 'Syne', sans-serif; font-size: 11px;
    font-weight: 600; cursor: pointer; transition: all .2s;
  }
  .hist-clear-all:hover { background: rgba(252,92,125,.1); }

  /* ── FLASHCARD HIERARCHY ── */
  .fc-field-input {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 9px 14px;
    color: var(--text); font-family: 'DM Mono', monospace; font-size: 13px;
    outline: none; transition: border-color .2s; width: 100%;
  }
  .fc-field-input:focus { border-color: var(--accent); }

  .fc-materia-tab {
    padding: 8px 16px; border-radius: var(--radius-sm);
    border: 1px solid var(--border); background: var(--surface2);
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    cursor: pointer; transition: all .2s; color: var(--text-muted);
    display: flex; align-items: center; gap: 7px; user-select: none;
  }
  .fc-materia-tab:hover { border-color: var(--accent); color: var(--text); }
  .fc-materia-tab.active { color: #fff; box-shadow: 0 0 14px rgba(0,0,0,.2); }
  .fc-materia-tab .fc-m-count {
    background: rgba(255,255,255,.18); border-radius: 100px;
    padding: 1px 6px; font-size: 10px;
  }
  .fc-materia-tab .fc-m-del {
    width: 14px; height: 14px; border-radius: 50%;
    background: rgba(255,255,255,.15); display: flex;
    align-items: center; justify-content: center; font-size: 9px;
    cursor: pointer; transition: background .15s;
  }
  .fc-materia-tab .fc-m-del:hover { background: rgba(252,92,125,.5); }

  .fc-topico-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 14px 18px;
    display: flex; align-items: center; justify-content: space-between;
    gap: 12px; cursor: pointer; transition: all .2s; margin-bottom: 8px;
  }
  .fc-topico-card:hover { border-color: var(--accent); transform: translateX(4px); }
  .fc-topico-card.active { border-color: var(--accent); background: rgba(124,92,252,.08); }
  .fc-topico-left { display: flex; align-items: center; gap: 10px; }
  .fc-topico-icon { font-size: 16px; }
  .fc-topico-name { font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700; color: var(--text); }
  .fc-topico-count { font-size: 11px; color: var(--text-muted); }
  .fc-topico-actions { display: flex; gap: 6px; opacity: 0; transition: opacity .2s; }
  .fc-topico-card:hover .fc-topico-actions { opacity: 1; }
  .fc-topico-del {
    padding: 3px 8px; border-radius: 4px; border: 1px solid rgba(252,92,125,.3);
    background: transparent; color: var(--hard); font-size: 11px; cursor: pointer;
  }

  .fc-breadcrumb-item {
    color: var(--text-muted); cursor: pointer; transition: color .15s;
  }
  .fc-breadcrumb-item:hover { color: var(--accent); }
  .fc-breadcrumb-sep { color: var(--text-dim); }
  .fc-breadcrumb-current { color: var(--text); font-weight: 700; }

  /* ── BACKUP & LINK ── */
  .backup-section { margin-top: 48px; }
  .backup-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
  @media(max-width:700px){ .backup-grid { grid-template-columns: 1fr; } }
  .backup-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 24px 26px;
    display: flex; flex-direction: column; gap: 14px;
  }
  .backup-card-title {
    font-family: 'Syne', sans-serif; font-size: 14px; font-weight: 700;
    display: flex; align-items: center; gap: 8px;
  }
  .backup-card p { font-size: 12px; color: var(--text-muted); line-height: 1.6; }
  .backup-btn-row { display: flex; gap: 8px; flex-wrap: wrap; }
  .btn-backup {
    display: flex; align-items: center; gap: 7px;
    padding: 10px 18px; border-radius: var(--radius-sm);
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    letter-spacing: .05em; cursor: pointer; transition: all .2s; border: 1px solid;
  }
  .btn-backup-save  { background: var(--easy); border-color: var(--easy); color: #0a0a0f; }
  .btn-backup-save:hover  { transform: translateY(-1px); box-shadow: 0 4px 16px rgba(92,240,176,.4); }
  .btn-backup-load  { background: transparent; border-color: var(--accent); color: var(--accent); }
  .btn-backup-load:hover  { background: rgba(124,92,252,.1); }
  .btn-backup-reset { background: transparent; border-color: rgba(252,92,125,.4); color: var(--hard); }
  .btn-backup-reset:hover { background: rgba(252,92,125,.1); }
  .link-box {
    display: flex; align-items: center; gap: 8px;
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 10px 14px;
  }
  .link-box input {
    flex: 1; background: transparent; border: none; outline: none;
    color: var(--accent); font-family: 'DM Mono', monospace; font-size: 12px;
    min-width: 0;
  }
  .btn-copy {
    padding: 6px 14px; border-radius: var(--radius-sm);
    background: var(--accent); border: none; color: #fff;
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    cursor: pointer; white-space: nowrap; transition: all .2s; flex-shrink: 0;
  }
  .btn-copy:hover { box-shadow: 0 0 14px rgba(124,92,252,.5); }
  .backup-stats {
    display: flex; flex-wrap: wrap; gap: 10px; margin-top: 4px;
  }
  .bstat {
    font-size: 11px; padding: 3px 10px; border-radius: 100px;
    background: var(--surface2); border: 1px solid var(--border); color: var(--text-muted);
  }
  .bstat strong { color: var(--text); }

  /* ═══ APP NAVIGATION ═══ */
  .app-nav {
    display: flex; gap: 4px; flex-wrap: wrap;
    padding: 16px 0 0; margin-bottom: 32px;
    border-bottom: 1px solid var(--border);
  }
  .app-tab {
    padding: 9px 18px; border-radius: var(--radius-sm) var(--radius-sm) 0 0;
    border: 1px solid transparent; border-bottom: none;
    background: var(--surface2); color: var(--text-muted);
    font-family: 'Syne', sans-serif; font-size: 12px; font-weight: 700;
    letter-spacing: .05em; cursor: pointer; transition: all .2s;
    position: relative; bottom: -1px;
  }
  .app-tab:hover { color: var(--text); background: var(--surface); }
  .app-tab.active {
    background: var(--bg); border-color: var(--border);
    color: var(--accent); border-bottom-color: var(--bg);
  }
  .app-tab-prova { color: var(--accent4) !important; }
  .app-tab-prova.active { color: var(--accent4) !important; }

  .app-page { display: none; animation: fadeUp .3s ease; }
  .app-page.active { display: block; }

  /* ═══ PAGE HEADERS ═══ */
  .page-header-row {
    display: flex; align-items: center; justify-content: space-between;
    flex-wrap: wrap; gap: 12px; margin-bottom: 24px;
  }
  .page-title {
    font-family: 'Syne', sans-serif; font-size: 22px; font-weight: 800;
    color: var(--text); margin: 0;
  }
  .btn-primary-lg {
    padding: 10px 24px; border-radius: var(--radius-sm);
    background: var(--accent); border: none; color: #fff;
    font-family: 'Syne', sans-serif; font-size: 12px; font-weight: 800;
    cursor: pointer; transition: all .2s; letter-spacing: .05em;
  }
  .btn-primary-lg:hover { transform: translateY(-2px); box-shadow: 0 4px 20px rgba(124,92,252,.4); }
  .btn-primary-sm {
    padding: 8px 16px; border-radius: var(--radius-sm);
    background: var(--accent); border: none; color: #fff;
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    cursor: pointer; transition: all .2s;
  }
  .btn-ghost-sm {
    padding: 8px 16px; border-radius: var(--radius-sm);
    background: transparent; border: 1px solid var(--border); color: var(--text-muted);
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    cursor: pointer; transition: all .2s;
  }
  .btn-ghost-sm:hover { border-color: var(--accent); color: var(--text); }

  /* ═══ HOJE ═══ */
  .hoje-stats-bar {
    display: grid; grid-template-columns: repeat(4,1fr); gap: 12px; margin-bottom: 28px;
  }
  @media(max-width:600px){ .hoje-stats-bar { grid-template-columns: repeat(2,1fr); } }
  .hoje-stat {
    background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-sm);
    padding: 14px 16px; display: flex; flex-direction: column; gap: 4px;
  }
  .hs-num { font-family: 'Syne', sans-serif; font-size: 26px; font-weight: 800; color: var(--text); }
  .hs-lbl { font-size: 11px; color: var(--text-muted); }
  .hoje-section { margin-bottom: 24px; }
  .hoje-section-title {
    font-family: 'Syne', sans-serif; font-size: 12px; font-weight: 700;
    letter-spacing: .08em; text-transform: uppercase; margin-bottom: 12px;
    display: flex; align-items: center; gap: 8px;
    padding: 10px 14px; border-radius: var(--radius-sm); border: 1px solid;
  }
  .hoje-section-title.atrasado { background: rgba(252,92,125,.08); border-color: rgba(252,92,125,.3); color: var(--hard); }
  .hoje-section-title.revisao  { background: rgba(124,92,252,.08); border-color: rgba(124,92,252,.3); color: var(--accent); }
  .hoje-section-title.novo     { background: rgba(92,240,176,.08); border-color: rgba(92,240,176,.3); color: var(--easy); }
  .hoje-cnt {
    margin-left: auto; background: rgba(255,255,255,.1); border-radius: 100px;
    padding: 1px 8px; font-size: 11px;
  }
  .hoje-cards-preview { display: flex; flex-wrap: wrap; gap: 8px; }
  .hoje-card-chip {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 8px 14px;
    font-size: 11px; color: var(--text-muted); cursor: default;
    border-left: 3px solid;
  }

  /* ═══ MODO ESTUDO OVERLAY ═══ */
  .modo-estudo-overlay {
    position: fixed; inset: 0; z-index: 10000;
    background: var(--bg); overflow-y: auto;
    display: flex; flex-direction: column; align-items: center;
    padding: 24px 16px 40px;
  }
  .me-header {
    width: 100%; max-width: 680px; display: flex;
    align-items: center; gap: 16px; margin-bottom: 32px;
  }
  .me-close {
    background: var(--surface); border: 1px solid var(--border);
    color: var(--text-muted); width: 36px; height: 36px; border-radius: 50%;
    font-size: 14px; cursor: pointer; flex-shrink: 0; transition: all .2s;
  }
  .me-close:hover { color: var(--hard); border-color: var(--hard); }
  .me-progress-wrap { flex: 1; display: flex; align-items: center; gap: 10px; }
  .me-progress-bar {
    flex: 1; height: 6px; background: var(--border); border-radius: 100px; overflow: hidden;
  }
  .me-progress-bar div { height: 100%; background: var(--accent); border-radius: 100px; transition: width .4s; }
  .me-card-container { width: 100%; max-width: 680px; perspective: 1400px; margin-bottom: 24px; }
  .me-card {
    width: 100%; min-height: 340px; cursor: pointer;
    transform-style: preserve-3d; position: relative;
  }
  .me-card-inner {
    width: 100%; height: 100%; position: relative;
    transform-style: preserve-3d; transition: transform .55s cubic-bezier(.4,0,.2,1);
  }
  .me-card-inner.flipped { transform: rotateY(180deg); }
  .me-face {
    position: absolute; inset: 0; backface-visibility: hidden;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 32px 36px;
    display: flex; flex-direction: column; justify-content: center;
    min-height: 340px;
  }
  .me-back { transform: rotateY(180deg); border-color: var(--accent); }
  .me-tipo {
    font-size: 10px; letter-spacing: .1em; text-transform: uppercase;
    color: var(--text-dim); margin-bottom: 8px;
  }
  .me-materia-badge {
    font-size: 11px; padding: 3px 10px; border-radius: 100px;
    display: inline-flex; align-items: center; margin-bottom: 16px;
    width: fit-content;
  }
  .me-pergunta { font-family: 'Fraunces', serif; font-size: 20px; font-weight: 600; color: var(--text); line-height: 1.5; }
  .me-artigo { font-size: 11px; color: var(--text-dim); margin-top: 12px; }
  .me-flip-hint { font-size: 11px; color: var(--text-dim); margin-top: 20px; text-align: center; }
  .me-resposta { font-family: 'JetBrains Mono', monospace; font-size: 14px; color: var(--text); line-height: 1.7; }
  .me-obs { font-size: 12px; color: var(--text-muted); margin-top: 14px; padding-top: 14px; border-top: 1px solid var(--border); }
  .me-actions {
    display: flex; gap: 12px; width: 100%; max-width: 680px;
    justify-content: center; flex-wrap: wrap;
  }
  .me-btn {
    flex: 1; min-width: 120px; max-width: 200px; padding: 14px 20px;
    border-radius: var(--radius-sm); border: 1px solid; background: transparent;
    font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700;
    cursor: pointer; transition: all .2s;
  }
  .me-errei  { border-color: rgba(252,92,125,.5); color: var(--hard); }
  .me-errei:hover  { background: rgba(252,92,125,.12); transform: translateY(-2px); }
  .me-dificil{ border-color: rgba(252,184,92,.5); color: var(--accent4); }
  .me-dificil:hover{ background: rgba(252,184,92,.12); transform: translateY(-2px); }
  .me-facil  { border-color: rgba(92,240,176,.5); color: var(--easy); }
  .me-facil:hover  { background: rgba(92,240,176,.12); transform: translateY(-2px); }
  .me-reveal-btn { text-align: center; width: 100%; max-width: 680px; }
  .btn-reveal {
    padding: 12px 40px; border-radius: var(--radius-sm);
    background: var(--accent); border: none; color: #fff;
    font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700; cursor: pointer;
    transition: all .2s;
  }
  .btn-reveal:hover { transform: translateY(-2px); box-shadow: 0 4px 20px rgba(124,92,252,.4); }

  /* ═══ CALENDÁRIO ═══ */
  .cal-grid-wrap { overflow-x: auto; }
  .cal-dow-header {
    display: grid; grid-template-columns: repeat(7,1fr);
    text-align: center; font-size: 11px; font-family: 'Syne', sans-serif;
    font-weight: 700; color: var(--text-dim); letter-spacing: .07em;
    text-transform: uppercase; margin-bottom: 4px; min-width: 420px;
  }
  .cal-grid { display: grid; grid-template-columns: repeat(7,1fr); gap: 3px; min-width: 420px; }
  .cal-day {
    min-height: 72px; background: var(--surface); border: 1px solid var(--border);
    border-radius: 6px; padding: 6px; cursor: pointer; transition: all .15s;
    position: relative; overflow: hidden;
  }
  .cal-day:hover { border-color: var(--accent); background: var(--surface2); }
  .cal-day.hoje-dia { border-color: var(--easy); }
  .cal-day.outro-mes { opacity: .35; cursor: default; }
  .cal-day.outro-mes:hover { border-color: var(--border); background: var(--surface); }
  .cal-num {
    font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 700;
    color: var(--text-muted); margin-bottom: 4px;
  }
  .cal-day.hoje-dia .cal-num { color: var(--easy); }
  .cal-plan-chip {
    font-size: 9px; border-radius: 3px; padding: 1px 5px;
    margin-bottom: 2px; color: #fff; font-weight: 600;
    overflow: hidden; text-overflow: ellipsis; white-space: nowrap;
    display: block;
  }
  .btn-nav-mes {
    width: 32px; height: 32px; border-radius: 50%;
    background: var(--surface); border: 1px solid var(--border);
    color: var(--text-muted); font-size: 18px; cursor: pointer;
    transition: all .2s; display: flex; align-items: center; justify-content: center;
  }
  .btn-nav-mes:hover { border-color: var(--accent); color: var(--accent); }
  .cal-modal-overlay {
    position: fixed; inset: 0; z-index: 9000; background: rgba(0,0,0,.7);
    display: flex; align-items: center; justify-content: center; padding: 16px;
  }
  .cal-modal {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 28px 32px; width: 100%; max-width: 480px;
    max-height: 90vh; overflow-y: auto; animation: fadeUp .25s ease;
  }
  .cal-modal-title {
    font-family: 'Syne', sans-serif; font-size: 16px; font-weight: 800;
    color: var(--text); margin-bottom: 18px;
  }
  .cal-form .cf-field { display: flex; flex-direction: column; gap: 5px; margin-bottom: 12px; }
  .cal-form .cf-field label { font-size: 10px; color: var(--text-dim); text-transform: uppercase; letter-spacing: .07em; }
  .cf-select, .cf-input, .cf-textarea {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 9px 12px;
    color: var(--text); font-family: 'JetBrains Mono', monospace; font-size: 12px;
    outline: none; transition: border-color .2s; width: 100%; resize: vertical;
  }
  .cf-select:focus, .cf-input:focus, .cf-textarea:focus { border-color: var(--accent); }
  .cf-select option { background: var(--surface2); }
  .cal-plan-item {
    display: flex; align-items: center; gap: 8px; padding: 8px 12px;
    background: var(--surface2); border-radius: 6px; margin-bottom: 6px;
    border-left: 3px solid;
  }
  .cal-plan-del {
    margin-left: auto; background: transparent; border: none;
    color: var(--hard); font-size: 14px; cursor: pointer; padding: 2px 6px;
  }

  /* ═══ FLASHCARD TREE ═══ */
  .fc-tree-materia {
    margin-bottom: 10px; background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); overflow: hidden;
  }
  .fc-tree-materia-header {
    display: flex; align-items: center; gap: 10px; padding: 14px 18px;
    cursor: pointer; transition: background .15s; user-select: none;
    border-left: 4px solid;
  }
  .fc-tree-materia-header:hover { background: var(--surface2); }
  .fc-tree-m-name { font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700; flex: 1; }
  .fc-tree-m-cnt { font-size: 11px; color: var(--text-dim); }
  .fc-tree-m-arrow { font-size: 12px; color: var(--text-dim); transition: transform .2s; }
  .fc-tree-materia.open .fc-tree-m-arrow { transform: rotate(90deg); }
  .fc-tree-topicos { display: none; padding: 0 0 8px 0; }
  .fc-tree-materia.open .fc-tree-topicos { display: block; }
  .fc-tree-topico {
    margin: 6px 16px; background: var(--surface2); border: 1px solid var(--border);
    border-radius: 6px; overflow: hidden;
  }
  .fc-tree-topico-header {
    display: flex; align-items: center; gap: 8px; padding: 10px 14px;
    cursor: pointer; transition: background .15s; user-select: none;
  }
  .fc-tree-topico-header:hover { background: var(--sur3); }
  .fc-tree-t-name { font-size: 12px; font-weight: 600; color: var(--text); flex: 1; }
  .fc-tree-t-cnt { font-size: 10px; color: var(--text-dim); }
  .fc-tree-t-arrow { font-size: 10px; color: var(--text-dim); transition: transform .2s; }
  .fc-tree-topico.open .fc-tree-t-arrow { transform: rotate(90deg); }
  .fc-tree-cards-list { display: none; padding: 6px 12px 10px; }
  .fc-tree-topico.open .fc-tree-cards-list { display: block; }
  .fc-tree-card {
    background: var(--bg); border: 1px solid var(--border); border-radius: 6px;
    padding: 12px 14px; margin-bottom: 6px; position: relative;
    transition: border-color .15s;
  }
  .fc-tree-card:hover { border-color: var(--bord2); }
  .fc-tree-card-q { font-size: 13px; color: var(--text); margin-bottom: 6px; font-weight: 500; }
  .fc-tree-card-a { font-size: 11px; color: var(--text-muted); line-height: 1.5; }
  .fc-tree-card-meta { display: flex; gap: 8px; margin-top: 8px; flex-wrap: wrap; align-items: center; }
  .fc-tipo-badge {
    font-size: 9px; padding: 2px 7px; border-radius: 100px; border: 1px solid;
    font-weight: 700; letter-spacing: .05em; text-transform: uppercase;
  }
  .fc-tipo-lei     { border-color: rgba(92,184,252,.4); color: #5cb8fc; }
  .fc-tipo-juris   { border-color: rgba(252,184,92,.4); color: var(--accent4); }
  .fc-tipo-doutrina{ border-color: rgba(192,92,252,.4); color: #c05cfc; }
  .fc-card-edit-btn, .fc-card-del-btn {
    padding: 3px 8px; border-radius: 4px; border: 1px solid var(--border);
    background: transparent; font-size: 10px; cursor: pointer; transition: all .15s;
  }
  .fc-card-edit-btn { color: var(--accent); }
  .fc-card-del-btn  { color: var(--hard); border-color: rgba(252,92,125,.3); }
  .fc-busca-global {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 8px 14px; color: var(--text);
    font-family: 'JetBrains Mono', monospace; font-size: 12px; outline: none;
    transition: border-color .2s; min-width: 240px;
  }
  .fc-busca-global:focus { border-color: var(--accent); }

  /* ═══ ERROS FREQUENTES ═══ */
  .erro-card {
    background: var(--surface); border: 1px solid rgba(252,92,125,.25);
    border-radius: var(--radius-sm); padding: 16px 20px; margin-bottom: 10px;
    border-left: 4px solid var(--hard);
  }
  .erro-card-q { font-size: 13px; color: var(--text); font-weight: 600; margin-bottom: 6px; }
  .erro-card-meta { font-size: 11px; color: var(--text-muted); display: flex; gap: 12px; flex-wrap: wrap; }
  .erro-cnt-badge {
    background: rgba(252,92,125,.15); border: 1px solid rgba(252,92,125,.3);
    color: var(--hard); border-radius: 100px; padding: 1px 8px; font-size: 10px; font-weight: 700;
  }

  /* ═══ DESEMPENHO ═══ */
  .kpi-grid { display: grid; grid-template-columns: repeat(3,1fr); gap: 14px; }
  @media(max-width:600px){ .kpi-grid { grid-template-columns: repeat(2,1fr); } }
  .kpi-card {
    background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-sm);
    padding: 18px 20px; display: flex; flex-direction: column; gap: 6px;
  }
  .kpi-num { font-family: 'Syne', sans-serif; font-size: 28px; font-weight: 800; color: var(--accent); }
  .kpi-lbl { font-size: 11px; color: var(--text-muted); }
  .desempenho-materia-row {
    display: flex; align-items: center; gap: 10px; padding: 12px 0;
    border-bottom: 1px solid var(--border);
  }
  .desmrow-name { width: 180px; flex-shrink: 0; font-size: 13px; font-weight: 600; }
  .desmrow-bar-wrap { flex: 1; height: 8px; background: var(--border); border-radius: 100px; overflow: hidden; }
  .desmrow-bar { height: 100%; border-radius: 100px; transition: width .5s ease; }
  .desmrow-pct { font-size: 12px; color: var(--text-muted); width: 40px; text-align: right; }
  .grafico-wrap { display: flex; align-items: flex-end; gap: 6px; height: 100px; padding: 12px 0; }
  .graf-bar { flex: 1; border-radius: 4px 4px 0 0; min-width: 24px; transition: all .4s; position: relative; }
  .graf-bar-lbl {
    position: absolute; bottom: -20px; left: 50%; transform: translateX(-50%);
    font-size: 9px; color: var(--text-dim); white-space: nowrap;
  }
  .graf-bar-val {
    position: absolute; top: -18px; left: 50%; transform: translateX(-50%);
    font-size: 9px; color: var(--text-muted); white-space: nowrap;
  }

  /* ═══ MODO PROVA ═══ */
  .prova-options { display: grid; grid-template-columns: repeat(3,1fr); gap: 16px; margin-bottom: 24px; }
  @media(max-width:700px){ .prova-options { grid-template-columns: 1fr; } }
  .prova-opt-card {
    background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius);
    padding: 28px 24px; text-align: center; cursor: pointer; transition: all .2s;
    display: flex; flex-direction: column; align-items: center; gap: 8px;
  }
  .prova-opt-card:hover { border-color: var(--accent); transform: translateY(-3px); box-shadow: 0 8px 24px rgba(0,0,0,.3); }
  .poc-icon { font-size: 36px; }
  .poc-title { font-family: 'Syne', sans-serif; font-size: 14px; font-weight: 800; color: var(--text); }
  .poc-desc { font-size: 12px; color: var(--text-muted); }
  .poc-cnt { font-size: 11px; color: var(--accent); font-weight: 700; }
  .prova-filtros { display: flex; align-items: center; gap: 12px; flex-wrap: wrap; }

  /* ═══ HISTORY TREE VIEW ═══ */
  .hist-view-toggle {
    display: flex; background: var(--surface2); border: 1px solid var(--border);
    border-radius: var(--radius-sm); overflow: hidden;
  }
  .hvt-btn {
    padding: 6px 14px; border: none; background: transparent;
    color: var(--text-muted); font-family: 'Syne', sans-serif;
    font-size: 11px; font-weight: 700; cursor: pointer; transition: all .15s;
  }
  .hvt-btn.active { background: var(--accent); color: #fff; }

  .hist-summary-bar {
    display: flex; gap: 16px; flex-wrap: wrap; padding: 12px 16px;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); margin-bottom: 16px;
    font-size: 12px; color: var(--text-muted);
  }
  .hsb-item { display: flex; align-items: center; gap: 6px; }
  .hsb-num  { font-family: 'Syne', sans-serif; font-weight: 800; font-size: 16px; color: var(--text); }

  /* Tree container */
  .hist-tree { display: flex; flex-direction: column; gap: 6px; }

  /* Level 1 — Matéria */
  .ht-materia {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); overflow: hidden;
    transition: border-color .2s;
  }
  .ht-materia:hover { border-color: var(--bord2); }
  .ht-materia.open  { border-color: var(--bord2); }
  .ht-m-header {
    display: flex; align-items: center; gap: 10px; padding: 13px 16px;
    cursor: pointer; user-select: none; border-left: 4px solid transparent;
  }
  .ht-m-arrow { font-size: 10px; color: var(--text-dim); transition: transform .2s; flex-shrink: 0; }
  .ht-materia.open .ht-m-arrow { transform: rotate(90deg); }
  .ht-m-icon  { font-size: 16px; flex-shrink: 0; }
  .ht-m-name  { font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700; flex: 1; }
  .ht-m-stats { display: flex; gap: 8px; align-items: center; }
  .ht-stat-pill {
    font-size: 10px; padding: 2px 8px; border-radius: 100px;
    border: 1px solid; font-weight: 600;
  }
  .ht-progress-wrap { width: 80px; height: 4px; background: var(--border); border-radius: 100px; overflow: hidden; }
  .ht-progress-fill { height: 100%; border-radius: 100px; transition: width .5s; }
  .ht-check {
    width: 18px; height: 18px; border-radius: 4px; border: 1.5px solid;
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; font-size: 11px; flex-shrink: 0; transition: all .15s;
  }
  .ht-check.done { background: var(--green); border-color: var(--green); color: #0a0a0f; }

  /* Level 1 children */
  .ht-m-children { display: none; padding: 0 0 8px 20px; border-top: 1px solid var(--border); }
  .ht-materia.open .ht-m-children { display: block; }

  /* Level 2 — Tópico */
  .ht-topico {
    margin: 5px 0 5px 0; border: 1px solid var(--border);
    border-radius: 6px; background: var(--surface2); overflow: hidden;
  }
  .ht-t-header {
    display: flex; align-items: center; gap: 8px; padding: 9px 14px;
    cursor: pointer; user-select: none;
  }
  .ht-t-arrow { font-size: 9px; color: var(--text-dim); transition: transform .2s; flex-shrink: 0; }
  .ht-topico.open .ht-t-arrow { transform: rotate(90deg); }
  .ht-t-icon  { font-size: 13px; }
  .ht-t-name  { font-size: 12px; font-weight: 600; color: var(--text); flex: 1; }
  .ht-t-cnt   { font-size: 10px; color: var(--text-dim); }
  .ht-t-children { display: none; padding: 2px 0 8px 16px; }
  .ht-topico.open .ht-t-children { display: block; }

  /* Level 3 — Subtópico / Conteúdo */
  .ht-subtopico {
    margin: 4px 0; border: 1px solid var(--border);
    border-radius: 4px; background: var(--bg); overflow: hidden;
  }
  .ht-s-header {
    display: flex; align-items: center; gap: 8px; padding: 7px 12px;
    cursor: pointer; user-select: none;
  }
  .ht-s-arrow { font-size: 9px; color: var(--text-dim); transition: transform .2s; flex-shrink: 0; }
  .ht-subtopico.open .ht-s-arrow { transform: rotate(90deg); }
  .ht-s-icon  { font-size: 12px; }
  .ht-s-name  { font-size: 11px; font-weight: 600; color: var(--text-muted); flex: 1; }
  .ht-s-meta  { font-size: 10px; color: var(--text-dim); display: flex; gap: 6px; }
  .ht-s-children { display: none; padding: 2px 0 6px 16px; }
  .ht-subtopico.open .ht-s-children { display: block; }

  /* Level 4 — Registro de estudo */
  .ht-registro {
    display: flex; align-items: flex-start; gap: 8px;
    padding: 7px 10px; margin: 2px 0;
    border-left: 2px solid var(--border); background: transparent;
    border-radius: 0 4px 4px 0; transition: border-color .15s;
  }
  .ht-registro:hover { border-left-color: var(--accent); background: rgba(124,92,252,.04); }
  .ht-reg-dot {
    width: 6px; height: 6px; border-radius: 50%;
    flex-shrink: 0; margin-top: 4px;
  }
  .ht-reg-content { flex: 1; }
  .ht-reg-date  { font-size: 10px; color: var(--text-dim); margin-bottom: 2px; }
  .ht-reg-desc  { font-size: 11px; color: var(--text-muted); line-height: 1.5; }
  .ht-reg-pills { display: flex; gap: 4px; flex-wrap: wrap; margin-top: 4px; }
  .ht-reg-pill  {
    font-size: 9px; padding: 1px 6px; border-radius: 100px;
    border: 1px solid var(--border); color: var(--text-dim);
  }
  .ht-reg-del {
    background: transparent; border: none; color: var(--text-dim);
    font-size: 11px; cursor: pointer; padding: 1px 4px; flex-shrink: 0;
    opacity: 0; transition: opacity .15s;
  }
  .ht-registro:hover .ht-reg-del { opacity: 1; }

  /* Empty tree */
  .hist-tree-empty {
    text-align: center; padding: 48px 24px; color: var(--text-dim);
  }
  .hist-tree-empty-icon { font-size: 40px; margin-bottom: 12px; }

  /* ═══ FOOTER CSS ═══ */
  footer {
    margin-top: 64px; padding-top: 24px; border-top: 1px solid var(--border);
    display: flex; justify-content: space-between; align-items: center;
    flex-wrap: wrap; gap: 12px;
  }
  .footer-brand { font-family: 'Syne', sans-serif; font-size: 12px; font-weight: 700; color: var(--text-dim); }
  .footer-note { font-size: 11px; color: var(--text-dim); }

  /* ── PRINT ── */
  @media print {
    .bg-grid, .bg-blob { display: none; }
    body { background: #fff; color: #111; }
    .card, .block, .slot { border-color: #ddd; background: #fff; }
    .day-panel { display: block !important; page-break-before: always; }
    .day-tab { display: none; }
  }
</style>
</head>
<body>

<div class="bg-grid"></div>
<div class="bg-blob bg-blob-1"></div>
<div class="bg-blob bg-blob-2"></div>
<div class="bg-blob bg-blob-3"></div>

<div class="wrapper">

  <!-- HEADER -->
  <header>
    <div class="logo">
      <div class="logo-dot"></div>
      StudyFlow
    </div>
    <div class="header-meta">
      <span class="week-label">Semana de referência</span>
      <input type="week" class="week-input" id="weekPicker">
    </div>
  </header>

  <!-- ═══ MAIN APP NAVIGATION ═══ -->
  <nav class="app-nav" id="appNav">
    <button class="app-tab active" data-page="planner" onclick="switchPage('planner',this)">📅 Planner</button>
    <button class="app-tab" data-page="hoje" onclick="switchPage('hoje',this)">⚡ Hoje</button>
    <button class="app-tab" data-page="planejamento" onclick="switchPage('planejamento',this)">🗓 Calendário</button>
    <button class="app-tab" data-page="flashcards" onclick="switchPage('flashcards',this)">🃏 Flashcards</button>
    <button class="app-tab" data-page="erros" onclick="switchPage('erros',this)">❌ Erros</button>
    <button class="app-tab" data-page="desempenho" onclick="switchPage('desempenho',this)">📊 Desempenho</button>
    <button class="app-tab app-tab-prova" data-page="prova" onclick="switchPage('prova',this)">🎯 Modo Prova</button>
  </nav>

  <!-- ═══ PAGE: PLANNER (existing content) ═══ -->
  <div class="app-page active" id="page-planner">
  <div class="principles" id="prinTabs">
    <div class="prin-tab hours-tab" data-panel="hours" onclick="togglePrinPanel(this)">
      <span class="prin-dot" style="background:var(--easy)"></span>⏱ Horas de Estudo<span class="prin-arrow">▼</span>
    </div>
    <div class="prin-tab" data-panel="pomodoro" onclick="togglePrinPanel(this)" style="--tc:#fcb85c">
      <span class="prin-dot" style="background:#fcb85c"></span>🍅 Pomodoro<span class="prin-arrow">▼</span>
    </div>
    <div class="prin-tab" data-panel="espacada" onclick="togglePrinPanel(this)">
      <span class="prin-dot" style="background:#7c5cfc"></span>Repetição Espaçada<span class="prin-arrow">▼</span>
    </div>
    <div class="prin-tab" data-panel="interleaving" onclick="togglePrinPanel(this)">
      <span class="prin-dot" style="background:#fc5c7d"></span>Interleaving<span class="prin-arrow">▼</span>
    </div>
    <div class="prin-tab" data-panel="recall" onclick="togglePrinPanel(this)">
      <span class="prin-dot" style="background:#5cf0b0"></span>Active Recall<span class="prin-arrow">▼</span>
    </div>
    <div class="prin-tab" data-panel="periodica" onclick="togglePrinPanel(this)">
      <span class="prin-dot" style="background:#5cb8fc"></span>Revisão Periódica<span class="prin-arrow">▼</span>
    </div>
  </div>

  <!-- PRINCIPLE PANELS -->
  <div class="prin-panels">

    <!-- HOURS PANEL -->
    <div class="prin-panel" id="panel-hours">
      <div class="prin-panel-title" style="color:var(--easy)">⏱ Horas de Estudo por Dia</div>
      <p>Defina quantas horas você pretende estudar em cada dia da semana. Os blocos Pomodoro equivalentes são calculados automaticamente.</p>
      <div class="hours-grid" id="hoursGrid">
        <!-- populated by JS -->
      </div>
      <div class="hours-total-row">
        <div>
          <div class="hours-total-label">Total semanal de estudo</div>
          <div class="hours-bar-wrap" style="width:260px"><div class="hours-bar-fill" id="hoursBarFill" style="width:0%"></div></div>
        </div>
        <div style="text-align:right">
          <div class="hours-total-label">Horas acumuladas</div>
          <div class="hours-total-val"><span id="hoursTotalVal">0</span>h</div>
        </div>
        <div style="text-align:right">
          <div class="hours-total-label">Blocos Pomodoro totais</div>
          <div class="hours-total-val" style="color:var(--accent4)"><span id="pomoTotalVal">0</span></div>
        </div>
        <div style="display:flex;align-items:center">
          <span id="hoursSavedBadge" style="
            font-size:10px;color:var(--easy);background:rgba(92,240,176,.12);
            border:1px solid rgba(92,240,176,.3);border-radius:100px;
            padding:4px 12px;opacity:0;transition:opacity .4s;
            font-family:'Syne',sans-serif;font-weight:700;letter-spacing:.05em;
          ">✓ Salvo</span>
        </div>
      </div>

      <!-- ── TIMER ── -->
      <div class="timer-section">
        <div class="timer-section-label">🕐 Cronômetro Pomodoro</div>
        <div class="timer-wrap">

          <!-- SVG ring clock -->
          <div class="timer-ring-wrap">
            <svg width="240" height="240" viewBox="0 0 240 240">
              <circle class="timer-ring-bg" cx="120" cy="120" r="108"/>
              <circle class="timer-ring-fill" id="timerRing" cx="120" cy="120" r="108"
                stroke="var(--accent)"
                stroke-dasharray="678.58"
                stroke-dashoffset="0"/>
            </svg>
            <div class="timer-display">
              <span class="timer-phase-label" id="timerPhaseLabel" style="color:var(--accent)">FOCO</span>
              <span class="timer-time" id="timerDisplay" style="color:var(--text)">50:00</span>
              <span class="timer-block-count" id="timerBlockCount">Bloco 1</span>
            </div>
          </div>

          <!-- Phase dots -->
          <div class="timer-phases" id="timerPhaseDots">
            <!-- populated by JS -->
          </div>

          <!-- Session block history -->
          <div class="timer-session-row" id="timerSessionRow">
            <!-- populated by JS -->
          </div>

          <!-- Controls -->
          <div class="timer-controls">
            <button class="timer-btn timer-btn-start" id="timerStartBtn" onclick="timerToggle()">▶ Iniciar</button>
            <button class="timer-btn timer-btn-skip"  onclick="timerSkip()">⏭ Pular fase</button>
            <button class="timer-btn timer-btn-reset" onclick="timerReset()">↺ Reiniciar</button>
          </div>

          <!-- Sound toggle -->
          <div class="timer-sound-row">
            <span>🔔 Som de aviso</span>
            <label class="sound-toggle">
              <input type="checkbox" id="soundToggle" checked>
              <div class="sound-track"></div>
              <div class="sound-knob"></div>
            </label>
            <span id="soundLabel" style="color:var(--easy)">Ativado</span>
          </div>

        </div>
      </div>
    </div>

    <!-- POMODORO PANEL -->
    <div class="prin-panel" id="panel-pomodoro">
      <div class="prin-panel-title" style="color:var(--accent4)">🍅 Temporizador Pomodoro</div>
      <p>Configure seus blocos e inicie o cronômetro. Um sinal sonoro avisará quando o tempo acabar — hora de pausar ou retomar!</p>

      <!-- TIMER -->
      <div class="pomo-timer-wrap">
        <div class="pomo-phase-badge phase-idle" id="pomoPhaseBadge">⏸ Aguardando início</div>

        <div class="pomo-ring-wrap">
          <svg width="220" height="220" viewBox="0 0 220 220">
            <circle class="pomo-ring-bg"   cx="110" cy="110" r="96"/>
            <circle class="pomo-ring-fill" cx="110" cy="110" r="96" id="pomoRing"
              stroke="var(--accent)"
              stroke-dasharray="603.2"
              stroke-dashoffset="0"/>
          </svg>
          <div class="pomo-ring-center">
            <div class="pomo-time-display" id="pomoDisplay">50:00</div>
            <div class="pomo-time-label" id="pomoSubLabel">ESTUDO</div>
          </div>
        </div>

        <div class="pomo-controls">
          <button class="pomo-ctrl-btn pomo-ctrl-start" id="pomoStartBtn" onclick="pomoToggle()">▶ Iniciar</button>
          <button class="pomo-ctrl-btn pomo-ctrl-skip"  onclick="pomoSkip()">⏭ Pular fase</button>
          <button class="pomo-ctrl-btn pomo-ctrl-reset" onclick="pomoReset()">↺ Resetar</button>
        </div>

        <div class="pomo-blocks-row" id="pomoBlocksRow">
          <!-- dots rendered by JS -->
        </div>
      </div>

      <!-- SETTINGS -->
      <div style="border-top:1px solid var(--border);padding-top:20px;margin-top:8px">
        <div style="font-family:'Syne',sans-serif;font-size:11px;font-weight:700;letter-spacing:.08em;text-transform:uppercase;color:var(--text-dim);margin-bottom:14px">⚙️ Configurações</div>
        <div class="pomo-grid">
          <div class="pomo-card">
            <label>⏳ Estudo (min)</label>
            <input type="number" id="pomoStudy" value="50" min="1" max="120" oninput="updateHoursGrid();pomoReset()">
            <span class="pomo-note">Tempo de foco contínuo</span>
          </div>
          <div class="pomo-card">
            <label>☕ Pausa curta (min)</label>
            <input type="number" id="pomoBreak" value="10" min="1" max="30" oninput="updateHoursGrid()">
            <span class="pomo-note">Pausa entre blocos</span>
          </div>
          <div class="pomo-card">
            <label>🛋 Pausa longa (min)</label>
            <input type="number" id="pomoLong" value="30" min="5" max="60">
            <span class="pomo-note">A cada 4 blocos</span>
          </div>
        </div>
      </div>
    </div>

    <!-- REPETIÇÃO ESPAÇADA -->
    <div class="prin-panel" id="panel-espacada">
      <div class="prin-panel-title" style="color:#b8a0ff">🔁 Repetição Espaçada</div>
      <p>Técnica baseada na curva do esquecimento de Ebbinghaus. O conteúdo é revisado em intervalos crescentes, forçando a memória a consolidar antes que o esquecimento ocorra.</p>
      <ul>
        <li>Revise o conteúdo logo após aprender (D+1), depois espaçe progressivamente</li>
        <li>Neste planner, o sistema Anki (SM-2) calcula automaticamente o próximo intervalo</li>
        <li>Conteúdos difíceis voltam mais cedo; fáceis ficam mais espaçados</li>
        <li>O Fator de Facilidade (EF) começa em 2.5 e se ajusta com cada avaliação</li>
      </ul>
    </div>

    <!-- INTERLEAVING -->
    <div class="prin-panel" id="panel-interleaving">
      <div class="prin-panel-title" style="color:#ff9db5">🔀 Interleaving (Intercalação)</div>
      <p>Em vez de estudar um único assunto por horas (bloqueado), você alterna entre matérias diferentes. Isso força o cérebro a recuperar contexto a cada troca, acelerando a consolidação.</p>
      <ul>
        <li>Alterne entre matérias dentro do mesmo dia (ex: Penal → Civil → Penal)</li>
        <li>Evite estudar a mesma matéria por mais de 90 min seguidos</li>
        <li>A sensação de dificuldade é normal — é sinal de aprendizagem real</li>
        <li>Combine com a grade semanal para distribuir matérias diferentes por período</li>
      </ul>
    </div>

    <!-- ACTIVE RECALL -->
    <div class="prin-panel" id="panel-recall">
      <div class="prin-panel-title" style="color:#7affbe">🧠 Active Recall</div>
      <p>Ao invés de reler passivamente, você força o cérebro a recuperar a informação. Isso fortalece as conexões neurais muito mais do que qualquer leitura repetida.</p>
      <ul>
        <li>Use flashcards (Anki), questões, ou feche o material e escreva tudo que lembrar</li>
        <li>Faça perguntas a si mesmo antes de abrir o livro</li>
        <li>O campo "Desempenho" em cada bloco serve para registrar sua taxa de acerto</li>
        <li>Prefira resolver questões a reler — questões são active recall por natureza</li>
      </ul>
    </div>

    <!-- REVISÃO PERIÓDICA -->
    <div class="prin-panel" id="panel-periodica">
      <div class="prin-panel-title" style="color:#8ad4ff">📆 Revisão Periódica</div>
      <p>Blocos reservados exclusivamente para revisar conteúdos antigos. Sem revisão periódica, mesmo o conteúdo bem aprendido decai em semanas.</p>
      <ul>
        <li>Reserve ao menos 20–30% do seu tempo semanal para revisões</li>
        <li>Domingo é ideal para revisão geral da semana</li>
        <li>O calendário Anki abaixo indica exatamente o que revisar e quando</li>
        <li>Combine com active recall: revise sem olhar o material primeiro</li>
      </ul>
    </div>

  </div>

  <!-- SUBJECT LEGEND / TABS -->
  <div class="subj-legend">
    <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:10px;margin-bottom:14px">
      <div class="subj-legend-title" style="margin-bottom:0">📚 Matérias da Semana — clique no nome para editar</div>
      <div style="display:flex;align-items:center;gap:8px">
        <span id="subjSavedBadge" style="font-size:10px;color:var(--easy);opacity:0;transition:opacity .4s;
          background:rgba(92,240,176,.12);border:1px solid rgba(92,240,176,.3);border-radius:100px;
          padding:3px 10px;font-family:'Syne',sans-serif;font-weight:700">✓ Salvo</span>
        <button onclick="saveSubjectsNow()" style="
          display:flex;align-items:center;gap:6px;padding:7px 16px;border-radius:6px;
          background:var(--easy);border:none;color:#0a0a0f;
          font-family:'Syne',sans-serif;font-size:11px;font-weight:800;
          cursor:pointer;transition:all .2s;letter-spacing:.05em">
          💾 Salvar Matérias
        </button>
      </div>
    </div>
    <div class="subj-tabs-row" id="subjTabsRow">
      <!-- populated by JS -->
    </div>
  </div>

  <!-- WEEKLY MATRIX TABLE -->
  <div class="matrix-section">
    <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:10px;margin-bottom:20px">
      <div class="matrix-section-title" style="margin-bottom:0;flex:1">📅 Grade Semanal de Estudos</div>
      <div style="display:flex;align-items:center;gap:8px">
        <span id="matrixSavedBadge" style="font-size:10px;color:var(--easy);opacity:0;transition:opacity .4s;
          background:rgba(92,240,176,.12);border:1px solid rgba(92,240,176,.3);border-radius:100px;
          padding:3px 10px;font-family:'Syne',sans-serif;font-weight:700">✓ Salvo</span>
        <button onclick="saveMatrixNow()" style="
          display:flex;align-items:center;gap:6px;padding:7px 16px;border-radius:6px;
          background:var(--accent);border:none;color:#fff;
          font-family:'Syne',sans-serif;font-size:11px;font-weight:800;
          cursor:pointer;transition:all .2s;letter-spacing:.05em">
          💾 Salvar Grade
        </button>
      </div>
    </div>
    <div class="matrix-wrap">
      <table class="matrix-table" id="matrixTable">
        <thead>
          <tr>
            <th>Período</th>
            <th>Segunda</th>
            <th>Terça</th>
            <th>Quarta</th>
            <th>Quinta</th>
            <th>Sexta</th>
            <th>Sábado</th>
            <th>Domingo</th>
          </tr>
        </thead>
        <tbody id="matrixBody">
          <!-- populated by JS -->
        </tbody>
      </table>
    </div>
  </div>

  <!-- META ROW -->
  <div class="meta-row">
    <div class="card">
      <div class="card-label"><span class="icon">🎯</span> Metas da Semana</div>
      <textarea rows="4" placeholder="Ex: Terminar capítulo 5 de Cálculo, resolver 50 questões de Química..."></textarea>
    </div>
    <div class="card">
      <div class="card-label"><span class="icon">📝</span> Observações Gerais</div>
      <textarea rows="4" placeholder="Compromissos, eventos, lembretes importantes para a semana..."></textarea>
    </div>
  </div>

  <!-- DAY NAVIGATION -->
  <div class="days-nav" id="daysNav">
    <div class="day-tab active" data-day="0">SEG</div>
    <div class="day-tab" data-day="1">TER</div>
    <div class="day-tab" data-day="2">QUA</div>
    <div class="day-tab" data-day="3">QUI</div>
    <div class="day-tab" data-day="4">SEX</div>
    <div class="day-tab" data-day="5">SÁB</div>
    <div class="day-tab" data-day="6">DOM</div>
  </div>

  <!-- DAY PANELS -->
  <div id="dayPanels"></div>

  <!-- ═══════════════════════════════════════════════ -->
  <!-- HISTÓRICO DE ESTUDOS                             -->
  <!-- ═══════════════════════════════════════════════ -->
  <div class="history-section">
    <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:12px;margin-bottom:20px">
      <div class="section-title" style="margin:0">📋 Histórico de Estudos</div>
      <div style="display:flex;gap:8px;flex-wrap:wrap;align-items:center">
        <!-- View toggle -->
        <div class="hist-view-toggle">
          <button class="hvt-btn active" id="hvtTree" onclick="setHistView('tree',this)">🌳 Árvore</button>
          <button class="hvt-btn" id="hvtCards" onclick="setHistView('cards',this)">📄 Cards</button>
        </div>
        <button class="hist-clear-all" onclick="clearAllHistory()">🗑 Limpar</button>
      </div>
    </div>

    <!-- TREE VIEW -->
    <div id="histViewTree" class="hist-tree-wrap">
      <!-- Summary bar -->
      <div class="hist-summary-bar" id="histSummaryBar"></div>
      <!-- Tree container -->
      <div id="histTree" class="hist-tree"></div>
    </div>

    <!-- CARDS VIEW (original) -->
    <div id="histViewCards" style="display:none">
      <div class="hist-filter-row" id="histFilterRow">
        <button class="hist-filter-btn active" data-filter="all" onclick="setHistFilter(this)">Todos</button>
        <button class="hist-filter-btn" data-filter="0" onclick="setHistFilter(this)">Seg</button>
        <button class="hist-filter-btn" data-filter="1" onclick="setHistFilter(this)">Ter</button>
        <button class="hist-filter-btn" data-filter="2" onclick="setHistFilter(this)">Qua</button>
        <button class="hist-filter-btn" data-filter="3" onclick="setHistFilter(this)">Qui</button>
        <button class="hist-filter-btn" data-filter="4" onclick="setHistFilter(this)">Sex</button>
        <button class="hist-filter-btn" data-filter="5" onclick="setHistFilter(this)">Sáb</button>
        <button class="hist-filter-btn" data-filter="6" onclick="setHistFilter(this)">Dom</button>
      </div>
      <div id="historyGrid" class="history-grid"></div>
    </div>
  </div>
  <div class="rev-section" id="ankiSection">
    <!-- HEADER -->
    <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:12px;margin-bottom:20px">
      <div class="section-title" style="margin:0">🃏 Revisão Anki — SM-2</div>
      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <button class="anki-ctrl-btn" onclick="ankiSyncFromFlashcards()" title="Sincronizar com flashcards criados">🔄 Sincronizar</button>
        <button class="anki-ctrl-btn anki-ctrl-study" onclick="ankiIniciarSessao()" id="ankiBtnSessao">▶ Estudar agora</button>
      </div>
    </div>

    <!-- DASHBOARD STATS -->
    <div class="anki-dashboard" id="ankiDashboard">
      <div class="anki-dash-card">
        <span class="adc-num" id="adcTotal">0</span>
        <span class="adc-lbl">Total de itens</span>
      </div>
      <div class="anki-dash-card adc-atrasado">
        <span class="adc-num" id="adcAtrasado">0</span>
        <span class="adc-lbl">⚠ Atrasados</span>
      </div>
      <div class="anki-dash-card adc-hoje">
        <span class="adc-num" id="adcHoje">0</span>
        <span class="adc-lbl">📅 Para hoje</span>
      </div>
      <div class="anki-dash-card adc-novos">
        <span class="adc-num" id="adcNovos">0</span>
        <span class="adc-lbl">✨ Novos</span>
      </div>
      <div class="anki-dash-card">
        <span class="adc-num" id="adcAprendendo">0</span>
        <span class="adc-lbl">📖 Aprendendo</span>
      </div>
      <div class="anki-dash-card adc-dominados">
        <span class="adc-num" id="adcDominados">0</span>
        <span class="adc-lbl">✅ Dominados</span>
      </div>
    </div>

    <!-- MODO SESSÃO DE ESTUDO -->
    <div id="ankiSessaoArea" style="display:none">
      <!-- Progress -->
      <div class="anki-sess-header">
        <button class="anki-ctrl-btn" onclick="ankiFecharSessao()">✕ Fechar</button>
        <div style="flex:1">
          <div style="display:flex;justify-content:space-between;margin-bottom:4px">
            <span style="font-size:11px;color:var(--text-muted)" id="ankiSessProgLabel">0 / 0</span>
            <span style="font-size:11px;color:var(--text-muted)" id="ankiSessStats"></span>
          </div>
          <div class="anki-sess-progress"><div id="ankiSessProgBar" style="width:0%;height:100%;background:var(--accent);border-radius:100px;transition:width .4s"></div></div>
        </div>
      </div>

      <!-- Card flip -->
      <div class="anki-card-wrap" id="ankiCardWrap" onclick="ankiVirar()">
        <div class="anki-card-inner" id="ankiCardInner">
          <div class="anki-face anki-frente">
            <div class="anki-face-tag">PERGUNTA</div>
            <div class="anki-face-materia" id="ankiCardMateria"></div>
            <div class="anki-face-texto" id="ankiCardPergunta"></div>
            <div class="anki-face-hint" id="ankiCardArtigo"></div>
            <div class="anki-flip-hint">clique para revelar</div>
          </div>
          <div class="anki-face anki-verso">
            <div class="anki-face-tag" style="color:var(--accent)">RESPOSTA</div>
            <div class="anki-face-texto" id="ankiCardResposta"></div>
            <div class="anki-face-obs" id="ankiCardObs"></div>
          </div>
        </div>
      </div>

      <!-- Reveal -->
      <div id="ankiRevealBtn" style="text-align:center;margin:16px 0">
        <button class="btn-reveal" onclick="ankiVirar()">↩ Revelar Resposta</button>
      </div>

      <!-- Grade buttons — shown after flip -->
      <div class="anki-grade-row" id="ankiGradeRow" style="display:none">
        <button class="anki-grade-btn again" onclick="ankiResponder(0)">
          <span class="agb-emoji">😵</span>
          <span class="agb-label">Again</span>
          <span class="agb-interval" id="agbAgain">1d</span>
        </button>
        <button class="anki-grade-btn hard" onclick="ankiResponder(3)">
          <span class="agb-emoji">😓</span>
          <span class="agb-label">Hard</span>
          <span class="agb-interval" id="agbHard">—</span>
        </button>
        <button class="anki-grade-btn good" onclick="ankiResponder(4)">
          <span class="agb-emoji">😊</span>
          <span class="agb-label">Good</span>
          <span class="agb-interval" id="agbGood">—</span>
        </button>
        <button class="anki-grade-btn easy" onclick="ankiResponder(5)">
          <span class="agb-emoji">🚀</span>
          <span class="agb-label">Easy</span>
          <span class="agb-interval" id="agbEasy">—</span>
        </button>
      </div>
    </div>

    <!-- FILTROS + TABELA -->
    <div id="ankiListaArea">
      <!-- Filter bar -->
      <div class="anki-filter-bar">
        <button class="anki-filter-btn active" data-filter="todos" onclick="ankiSetFiltro(this,'todos')">Todos</button>
        <button class="anki-filter-btn" data-filter="atrasados" onclick="ankiSetFiltro(this,'atrasados')">⚠ Atrasados</button>
        <button class="anki-filter-btn" data-filter="hoje" onclick="ankiSetFiltro(this,'hoje')">📅 Hoje</button>
        <button class="anki-filter-btn" data-filter="novos" onclick="ankiSetFiltro(this,'novos')">✨ Novos</button>
        <button class="anki-filter-btn" data-filter="dominados" onclick="ankiSetFiltro(this,'dominados')">✅ Dominados</button>
        <input type="text" id="ankiBusca" class="anki-busca" placeholder="🔍 Buscar..." oninput="renderAnkiTable()">
        <div style="margin-left:auto;display:flex;gap:8px">
          <button class="anki-ctrl-btn" onclick="ankiAddManual()" title="Adicionar item manualmente">➕ Adicionar</button>
          <button class="anki-ctrl-btn" style="color:var(--hard);border-color:rgba(252,92,125,.3)"
            onclick="if(confirm('Limpar todos os itens Anki?')){ankiEntries=[];saveAnkiEntries();renderAnkiTable();ankiUpdateDashboard();}">🗑</button>
        </div>
      </div>

      <!-- Table -->
      <div class="anki-table-wrap">
        <table class="rev-table" id="ankiTable">
          <thead>
            <tr>
              <th>Matéria / Tópico</th>
              <th>Data Estudo</th>
              <th>Próxima Revisão</th>
              <th>Intervalo</th>
              <th>EF</th>
              <th>Reps</th>
              <th>Status</th>
              <th>Avaliar</th>
            </tr>
          </thead>
          <tbody id="revisionTbody"></tbody>
        </table>
      </div>

      <!-- Add manual panel -->
      <div id="ankiAddPanel" style="display:none;margin-top:12px;padding:16px 20px;background:var(--surface2);border:1px solid var(--border);border-radius:var(--radius)">
        <div style="font-family:'Syne',sans-serif;font-weight:700;font-size:13px;margin-bottom:12px;color:var(--text)">➕ Adicionar Item Manualmente</div>
        <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-bottom:10px">
          <div>
            <label style="font-size:10px;color:var(--text-dim);text-transform:uppercase;letter-spacing:.07em;display:block;margin-bottom:4px">Matéria / Tópico</label>
            <input id="ankiAddName" type="text" placeholder="Ex: Direito Penal — Dolo"
              style="background:var(--surface);border:1px solid var(--border);border-radius:6px;padding:8px 12px;color:var(--text);font-family:'DM Mono',monospace;font-size:12px;outline:none;width:100%"
              onkeydown="if(event.key==='Enter')addAnkiEntry()">
          </div>
          <div>
            <label style="font-size:10px;color:var(--text-dim);text-transform:uppercase;letter-spacing:.07em;display:block;margin-bottom:4px">Data de estudo</label>
            <input id="ankiAddDate" type="date"
              style="background:var(--surface);border:1px solid var(--border);border-radius:6px;padding:8px 12px;color:var(--text);font-family:'DM Mono',monospace;font-size:12px;outline:none;width:100%">
          </div>
        </div>
        <div style="display:flex;gap:8px">
          <button onclick="addAnkiEntry()" class="btn-primary-sm">💾 Salvar</button>
          <button onclick="document.getElementById('ankiAddPanel').style.display='none'" class="btn-ghost-sm">Cancelar</button>
        </div>
      </div>
    </div>
  </div>

  <!-- ═══════════════════════════════════════════════ -->
  <!-- FLASHCARDS — Matéria › Tópico › Cards           -->
  <!-- ═══════════════════════════════════════════════ -->
  <div class="fc-section">
    <div class="section-title">🃏 Flashcards</div>

    <!-- DUE TODAY ALERT -->
    <div id="fcDueSummary" style="display:none;align-items:center;gap:8px;flex-wrap:wrap;
      padding:12px 16px;margin-bottom:16px;border-radius:var(--radius-sm);
      background:rgba(252,92,125,.06);border:1px solid rgba(252,92,125,.25)">
    </div>

    <!-- TOP TOOLBAR -->
    <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:12px;margin-bottom:20px">
      <div style="display:flex;gap:8px;flex-wrap:wrap" id="fcMateriaTabsRow">
        <!-- matéria tabs populated by JS -->
      </div>
      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <button class="fc-btn fc-btn-ghost" onclick="fcOpenAddCard()" id="fcAddCardBtn" style="display:none">＋ Novo Card</button>
        <button class="fc-btn fc-btn-ghost" onclick="fcOpenAddTopico()" id="fcAddTopicoBtn" style="display:none">＋ Novo Tópico</button>
        <button class="fc-btn fc-btn-ghost" onclick="fcOpenAddMateria()">＋ Nova Matéria</button>
        <button class="fc-btn fc-btn-primary" onclick="fcStartStudy()" id="fcStudyBtn">▶ Estudar</button>
      </div>
    </div>

    <!-- ADD MATERIA PANEL -->
    <div class="fc-create-panel" id="fcAddMateriaPanel">
      <div style="font-family:'Syne',sans-serif;font-weight:700;font-size:13px;margin-bottom:14px">📚 Nova Matéria</div>
      <div style="display:flex;gap:10px;flex-wrap:wrap">
        <input id="fcNewMateriaName" type="text" placeholder="Ex: Direito Penal" class="fc-field-input" style="flex:1;min-width:200px" onkeydown="if(event.key==='Enter')fcSaveMateria()">
        <button class="fc-btn fc-btn-primary" onclick="fcSaveMateria()">Criar Matéria</button>
        <button class="fc-btn fc-btn-ghost" onclick="document.getElementById('fcAddMateriaPanel').classList.remove('active')">Cancelar</button>
      </div>
    </div>

    <!-- ADD TOPICO PANEL -->
    <div class="fc-create-panel" id="fcAddTopicoPanel">
      <div style="font-family:'Syne',sans-serif;font-weight:700;font-size:13px;margin-bottom:14px">📌 Novo Tópico em <span id="fcTopicoMateriaLabel" style="color:var(--accent)">—</span></div>
      <div style="display:flex;gap:10px;flex-wrap:wrap">
        <input id="fcNewTopicoName" type="text" placeholder="Ex: Crimes contra a vida" class="fc-field-input" style="flex:1;min-width:200px" onkeydown="if(event.key==='Enter')fcSaveTopico()">
        <button class="fc-btn fc-btn-primary" onclick="fcSaveTopico()">Criar Tópico</button>
        <button class="fc-btn fc-btn-ghost" onclick="document.getElementById('fcAddTopicoPanel').classList.remove('active')">Cancelar</button>
      </div>
    </div>

    <!-- ADD CARD PANEL -->
    <div class="fc-create-panel" id="fcAddCardPanel">
      <div style="font-family:'Syne',sans-serif;font-weight:700;font-size:13px;margin-bottom:14px">
        🃏 Novo Card — <span id="fcCardBreadcrumb" style="color:var(--accent)">—</span>
      </div>
      <div class="fc-create-grid">
        <div class="fc-field" style="grid-column:1/-1">
          <label>Pergunta (Frente)</label>
          <textarea id="fcQ" rows="3" placeholder="Ex: O que é dolo eventual?"></textarea>
        </div>
        <div class="fc-field" style="grid-column:1/-1">
          <label>Resposta (Verso)</label>
          <textarea id="fcA" rows="3" placeholder="Ex: Ocorre quando o agente assume o risco do resultado..."></textarea>
        </div>
      </div>
      <div style="display:flex;gap:10px;margin-top:14px;flex-wrap:wrap">
        <button class="fc-btn fc-btn-primary" onclick="fcSaveCard()">💾 Salvar Card</button>
        <button class="fc-btn fc-btn-ghost" onclick="document.getElementById('fcAddCardPanel').classList.remove('active')">Cancelar</button>
      </div>
    </div>

    <!-- BREADCRUMB -->
    <div id="fcBreadcrumb" style="display:none;align-items:center;gap:6px;margin-bottom:16px;
      font-family:'Syne',sans-serif;font-size:12px;font-weight:600;flex-wrap:wrap">
    </div>

    <!-- TOPICOS LIST (shown when matéria selected) -->
    <div id="fcTopicosSection" style="display:none;margin-bottom:24px">
      <div id="fcTopicosList"></div>
    </div>

    <!-- CARDS LIST (shown when tópico selected) -->
    <div id="fcCardsSection" style="display:none">
      <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:14px;flex-wrap:wrap;gap:8px">
        <span id="fcCardsTitle" style="font-family:'Syne',sans-serif;font-weight:700;font-size:13px;color:var(--text-muted)"></span>
        <input type="text" id="fcSearch" class="fc-search" placeholder="🔍 Buscar..." oninput="fcRenderCards()" style="min-width:180px">
      </div>
      <div id="fcGrid" class="fc-grid"></div>
    </div>

    <!-- SESSION STATS -->
    <div class="fc-session-stats" id="fcStats" style="display:none">
      <div class="fc-stat"><span class="fc-stat-label">Restantes</span><span class="fc-stat-val" id="statRem" style="color:var(--accent)">0</span></div>
      <div class="fc-stat"><span class="fc-stat-label">Again</span><span class="fc-stat-val" id="statAgain" style="color:var(--hard)">0</span></div>
      <div class="fc-stat"><span class="fc-stat-label">Hard+Good</span><span class="fc-stat-val" id="statHardGood" style="color:var(--accent4)">0</span></div>
      <div class="fc-stat"><span class="fc-stat-label">Easy</span><span class="fc-stat-val" id="statEasy" style="color:var(--easy)">0</span></div>
    </div>

    <!-- STUDY AREA -->
    <div class="fc-study-area" id="fcStudyArea">
      <div class="fc-progress-label"><span id="fcProgLabel">Card 1 de 1</span><span id="fcProgPct">0%</span></div>
      <div class="fc-progress-bar-wrap"><div class="fc-progress-bar" id="fcProgBar" style="width:0%"></div></div>
      <div class="fc-card-stage" id="fcCardStage" onclick="flipCard()">
        <div class="fc-card-inner" id="fcCardInner">
          <div class="fc-card-face fc-card-front">
            <span class="fc-card-tag">PERGUNTA</span>
            <span class="fc-card-deck-badge" id="fcCardDeckBadge">—</span>
            <div class="fc-card-q" id="fcCardQ">—</div>
            <span class="fc-flip-hint">clique para virar ↩</span>
          </div>
          <div class="fc-card-face fc-card-back">
            <span class="fc-card-tag">RESPOSTA</span>
            <div class="fc-card-a" id="fcCardA">—</div>
          </div>
        </div>
      </div>
      <div class="fc-rating-row" id="fcRatingRow">
        <button class="fc-rate-btn fc-rate-again" onclick="rateCard(0)"><span class="fc-rate-label">😵 Again</span><span class="fc-rate-interval" id="intAgain">1d</span></button>
        <button class="fc-rate-btn fc-rate-hard"  onclick="rateCard(3)"><span class="fc-rate-label">😓 Hard</span><span class="fc-rate-interval" id="intHard">—</span></button>
        <button class="fc-rate-btn fc-rate-good"  onclick="rateCard(4)"><span class="fc-rate-label">😊 Good</span><span class="fc-rate-interval" id="intGood">—</span></button>
        <button class="fc-rate-btn fc-rate-easy"  onclick="rateCard(5)"><span class="fc-rate-label">🚀 Easy</span><span class="fc-rate-interval" id="intEasy">—</span></button>
      </div>
      <div style="text-align:center;margin-bottom:12px">
        <button class="fc-btn fc-btn-ghost" onclick="endStudy()" style="font-size:11px;padding:6px 14px">✕ Encerrar sessão</button>
      </div>
    </div>

    <!-- DONE SCREEN -->
    <div class="fc-done-screen" id="fcDoneScreen">
      <div class="fc-done-emoji">🎉</div>
      <div class="fc-done-title">Sessão Concluída!</div>
      <div class="fc-done-sub">Ótimo trabalho. O Anki calculou suas próximas revisões.</div>
      <div class="fc-done-results">
        <div class="fc-done-stat"><span class="fc-done-stat-val" id="doneTotal" style="color:var(--accent)">0</span><span class="fc-done-stat-label">Cards revisados</span></div>
        <div class="fc-done-stat"><span class="fc-done-stat-val" id="doneAgain" style="color:var(--hard)">0</span><span class="fc-done-stat-label">Again</span></div>
        <div class="fc-done-stat"><span class="fc-done-stat-val" id="doneGood" style="color:var(--accent4)">0</span><span class="fc-done-stat-label">Hard+Good</span></div>
        <div class="fc-done-stat"><span class="fc-done-stat-val" id="doneEasy" style="color:var(--easy)">0</span><span class="fc-done-stat-label">Easy</span></div>
      </div>
      <button class="fc-btn fc-btn-primary" onclick="endStudy()" style="margin-right:10px">↩ Voltar</button>
      <button class="fc-btn fc-btn-ghost" onclick="fcStartStudy()">▶ Estudar Novamente</button>
    </div>

  </div>

  <!-- ═══════════════════════════════════════════════ -->
  <!-- BACKUP & LINK                                    -->
  <!-- ═══════════════════════════════════════════════ -->
  <div class="backup-section">
    <div class="section-title">💾 Backup & Sincronização</div>
    <div class="backup-grid">

      <!-- LOCAL BACKUP -->
      <div class="backup-card">
        <div class="backup-card-title">💾 Backup Local</div>
        <p>Exporte todos os seus dados — matérias, grade, histórico, Anki e flashcards.</p>
        <div class="backup-stats" id="backupStats"></div>
        <div class="backup-btn-row">
          <button class="btn-backup btn-backup-save" onclick="exportBackup()">⬇ Baixar .json</button>
          <label class="btn-backup btn-backup-load" style="cursor:pointer">
            ⬆ Restaurar .json
            <input type="file" accept=".json" style="display:none" onchange="importBackup(event)">
          </label>
          <button class="btn-backup btn-backup-reset" onclick="resetAllData()">🗑 Resetar</button>
        </div>
      </div>

      <!-- GITHUB GIST SYNC -->
      <div class="backup-card" style="border-color:rgba(124,92,252,.3);background:rgba(124,92,252,.04)">
        <div class="backup-card-title" style="color:var(--accent)">☁️ Sincronização GitHub Gist</div>
        <p>Salve seus dados na nuvem via GitHub Gist — gratuito, funciona em qualquer dispositivo, sem servidor.</p>

        <!-- Status -->
        <div id="gistStatus" style="display:flex;align-items:center;gap:8px;margin:10px 0;font-size:11px;
          padding:8px 12px;border-radius:6px;background:var(--surface2);border:1px solid var(--border)">
          <span id="gistStatusDot" style="width:8px;height:8px;border-radius:50%;background:#555;flex-shrink:0"></span>
          <span id="gistStatusText">Não configurado</span>
        </div>

        <!-- Token input -->
        <div id="gistSetupArea">
          <div style="font-size:10px;color:var(--text-dim);margin-bottom:6px;text-transform:uppercase;letter-spacing:.07em">
            GitHub Personal Access Token (gist scope)
          </div>
          <div style="display:flex;gap:8px;flex-wrap:wrap">
            <input type="password" id="gistTokenInput" placeholder="ghp_xxxxxxxxxxxxxxxxxxxx"
              style="flex:1;min-width:180px;background:var(--surface);border:1px solid var(--border);
                border-radius:6px;padding:8px 12px;color:var(--text);font-family:'DM Mono',monospace;
                font-size:11px;outline:none"
              oninput="gistTokenChanged()">
            <button class="btn-backup btn-backup-save" onclick="gistSalvarToken()" style="white-space:nowrap">
              ✓ Salvar token
            </button>
          </div>
          <p style="font-size:10px;color:var(--text-dim);margin-top:6px;line-height:1.5">
            💡 Crie em <strong style="color:var(--accent)">github.com → Settings → Developer settings → Personal access tokens → Fine-grained</strong>.
            Marque apenas o escopo <strong style="color:var(--accent)">gist</strong>.
          </p>
        </div>

        <!-- Gist ID (auto-criado) -->
        <div id="gistIdArea" style="display:none;margin-top:8px">
          <div style="font-size:10px;color:var(--text-dim);margin-bottom:4px;text-transform:uppercase;letter-spacing:.07em">Gist ID (compartilhável)</div>
          <div style="display:flex;gap:6px">
            <input type="text" id="gistIdInput" placeholder="cole um Gist ID existente"
              style="flex:1;background:var(--surface);border:1px solid var(--border);border-radius:6px;
                padding:6px 10px;color:var(--text);font-family:'DM Mono',monospace;font-size:11px;outline:none">
            <button class="btn-backup btn-backup-load" onclick="gistUsarId()" style="white-space:nowrap;font-size:10px">Usar ID</button>
          </div>
        </div>

        <!-- Sync buttons -->
        <div style="display:flex;gap:8px;margin-top:12px;flex-wrap:wrap" id="gistBtnArea">
          <button class="btn-backup btn-backup-save" onclick="gistSalvar()" id="gistBtnSalvar" style="display:none">
            ☁️ Salvar na nuvem
          </button>
          <button class="btn-backup btn-backup-load" onclick="gistCarregar()" id="gistBtnCarregar" style="display:none">
            ⬇ Carregar da nuvem
          </button>
          <button class="btn-backup" onclick="gistDesconectar()" id="gistBtnDesconectar"
            style="display:none;border-color:rgba(252,92,125,.3);color:var(--hard);background:transparent;font-size:10px">
            Desconectar
          </button>
        </div>

        <!-- Last sync info -->
        <div id="gistLastSync" style="font-size:10px;color:var(--text-dim);margin-top:8px"></div>
      </div>

      <!-- LINK CARD -->
      <div class="backup-card" style="grid-column:1/-1">
        <div class="backup-card-title">🔗 Link do Programa</div>
        <p>Copie o endereço para acessar de qualquer lugar ou compartilhar.</p>
        <div class="link-box">
          <input type="text" id="programLink" readonly value="Carregando...">
          <button class="btn-copy" onclick="copyLink()">📋 Copiar</button>
        </div>
        <div style="display:flex;gap:8px;margin-top:8px;flex-wrap:wrap">
          <button class="btn-backup btn-backup-load" onclick="openInNewTab()" style="width:fit-content">
            🌐 Abrir em nova aba
          </button>
        </div>
      </div>

    </div>
  </div>

  <footer>
    <span class="footer-brand">StudyFlow Pro ◆ Sistema Completo para Concursos</span>
    <span class="footer-note">Repetição Espaçada · Anki SM-2 · Planejamento Inteligente</span>
  </footer>

  </div><!-- /page-planner -->

  <!-- ═══ PAGE: HOJE ═══ -->
  <div class="app-page" id="page-hoje">
    <div class="page-header-row">
      <div>
        <h2 class="page-title">⚡ Hoje</h2>
        <div id="hojeDataLabel" style="font-size:13px;color:var(--text-muted);font-family:'JetBrains Mono',monospace"></div>
      </div>
      <button class="btn-primary-lg" onclick="iniciarEstudo()" id="btnIniciarEstudo">▶ Iniciar Estudo</button>
    </div>

    <!-- Streaks & stats bar -->
    <div class="hoje-stats-bar">
      <div class="hoje-stat"><span class="hs-num" id="streakNum">0</span><span class="hs-lbl">🔥 Dias seguidos</span></div>
      <div class="hoje-stat"><span class="hs-num" id="hojeAcertosNum">0</span><span class="hs-lbl">✅ Acertos hoje</span></div>
      <div class="hoje-stat"><span class="hs-num" id="hojeErrosNum">0</span><span class="hs-lbl">❌ Erros hoje</span></div>
      <div class="hoje-stat"><span class="hs-num" id="hojeTaxaNum">—</span><span class="hs-lbl">📈 Taxa acerto</span></div>
    </div>

    <!-- Seção 1: Atrasados -->
    <div class="hoje-section" id="hoje-atrasados-sec">
      <div class="hoje-section-title atrasado">⚠️ Cards Atrasados <span id="cntAtrasados" class="hoje-cnt">0</span></div>
      <div id="hoje-atrasados-list" class="hoje-cards-preview"></div>
    </div>

    <!-- Seção 2: Revisão do dia -->
    <div class="hoje-section" id="hoje-revisao-sec">
      <div class="hoje-section-title revisao">🔁 Revisão do Dia <span id="cntRevisao" class="hoje-cnt">0</span></div>
      <div id="hoje-revisao-list" class="hoje-cards-preview"></div>
    </div>

    <!-- Seção 3: Conteúdo novo -->
    <div class="hoje-section" id="hoje-novos-sec">
      <div class="hoje-section-title novo">✨ Conteúdo Novo <span id="cntNovos" class="hoje-cnt">0</span></div>
      <div id="hoje-novos-list" class="hoje-cards-preview"></div>
    </div>

    <!-- Modo Estudo overlay -->
    <div id="modoEstudoOverlay" class="modo-estudo-overlay" style="display:none">
      <div class="me-header">
        <button class="me-close" onclick="fecharEstudo()">✕</button>
        <div class="me-progress-wrap">
          <div class="me-progress-bar"><div id="meProgressFill" style="width:0%"></div></div>
          <span id="meProgressLabel" style="font-size:11px;color:var(--text-muted)">0 / 0</span>
        </div>
      </div>

      <div class="me-card-container">
        <div class="me-card" id="meCard" onclick="virarCard()">
          <div class="me-card-inner" id="meCardInner">
            <div class="me-face me-front">
              <div class="me-tipo" id="meTipoLabel"></div>
              <div class="me-materia-badge" id="meMateriaBadge"></div>
              <div class="me-pergunta" id="mePergunta"></div>
              <div class="me-artigo" id="meArtigo"></div>
              <div class="me-flip-hint">toque para revelar →</div>
            </div>
            <div class="me-face me-back">
              <div class="me-resposta" id="meResposta"></div>
              <div class="me-obs" id="meObs"></div>
            </div>
          </div>
        </div>
      </div>

      <div class="me-actions" id="meActions" style="display:none">
        <button class="me-btn me-errei" onclick="responderCard('errei')">😵 Errei</button>
        <button class="me-btn me-dificil" onclick="responderCard('dificil')">😓 Difícil</button>
        <button class="me-btn me-facil" onclick="responderCard('facil')">😊 Fácil</button>
      </div>
      <div class="me-reveal-btn" id="meRevealBtn">
        <button class="btn-reveal" onclick="virarCard()">↩ Revelar Resposta</button>
      </div>
    </div>
  </div>

  <!-- ═══ PAGE: PLANEJAMENTO / CALENDÁRIO ═══ -->
  <div class="app-page" id="page-planejamento">
    <div class="page-header-row">
      <h2 class="page-title">🗓 Calendário de Estudos</h2>
      <div style="display:flex;gap:8px">
        <button class="btn-nav-mes" onclick="mudarMes(-1)">‹</button>
        <span id="calMesLabel" style="font-family:'Syne',sans-serif;font-weight:700;font-size:14px;color:var(--text);padding:0 8px;display:flex;align-items:center"></span>
        <button class="btn-nav-mes" onclick="mudarMes(1)">›</button>
      </div>
    </div>
    <div class="cal-grid-wrap">
      <div class="cal-dow-header">
        <span>Seg</span><span>Ter</span><span>Qua</span><span>Qui</span><span>Sex</span><span>Sáb</span><span>Dom</span>
      </div>
      <div class="cal-grid" id="calGrid"></div>
    </div>

    <!-- Modal planejamento dia -->
    <div class="cal-modal-overlay" id="calModalOverlay" style="display:none" onclick="fecharCalModal(event)">
      <div class="cal-modal">
        <div class="cal-modal-title" id="calModalTitle">Planejar Dia</div>
        <div id="calPlanejamentosExistentes" style="margin-bottom:12px"></div>
        <div class="cal-form">
          <div class="cf-field">
            <label>Matéria</label>
            <select id="calMateriaSelect" class="cf-select"></select>
          </div>
          <div class="cf-field">
            <label>Tópico</label>
            <input type="text" id="calTopicoInput" class="cf-input" placeholder="Ex: Crimes contra a vida">
          </div>
          <div class="cf-field">
            <label>Observação (opcional)</label>
            <input type="text" id="calObsInput" class="cf-input" placeholder="Ex: Cap. 3 do livro...">
          </div>
          <div style="display:flex;gap:8px;margin-top:12px">
            <button class="btn-primary-sm" onclick="salvarPlanejamento()">💾 Salvar</button>
            <button class="btn-ghost-sm" onclick="fecharCalModal()">Cancelar</button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- ═══ PAGE: FLASHCARDS ═══ -->
  <div class="app-page" id="page-flashcards">
    <div class="page-header-row">
      <h2 class="page-title">🃏 Flashcards</h2>
      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <input type="text" id="fcBuscaGlobal" class="fc-busca-global" placeholder="🔍 Buscar por pergunta, tipo, tópico...">
        <select id="fcFiltroTipo" class="cf-select" style="min-width:140px" onchange="fcRenderTree()">
          <option value="">Todos os tipos</option>
          <option value="lei">Lei Seca</option>
          <option value="juris">Jurisprudência</option>
          <option value="doutrina">Doutrina</option>
        </select>
        <button class="btn-primary-sm" onclick="fcAbrirNovoCard()">＋ Novo Card</button>
      </div>
    </div>

    <!-- Tree: Matéria > Tópico > Cards -->
    <div id="fcTree"></div>

    <!-- Modal criar/editar card -->
    <div class="cal-modal-overlay" id="fcModalOverlay" style="display:none" onclick="fcFecharModal(event)">
      <div class="cal-modal" style="max-width:580px">
        <div class="cal-modal-title" id="fcModalTitle">Novo Flashcard</div>
        <div class="cal-form">
          <div class="cf-field">
            <label>Matéria</label>
            <select id="fcEditMateria" class="cf-select" onchange="fcEditMateriaChange()"></select>
          </div>
          <div class="cf-field">
            <label>Tópico</label>
            <input type="text" id="fcEditTopico" class="cf-input" placeholder="Nome do tópico">
          </div>
          <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px">
            <div class="cf-field">
              <label>Tipo</label>
              <select id="fcEditTipo" class="cf-select">
                <option value="lei">Lei Seca</option>
                <option value="juris">Jurisprudência</option>
                <option value="doutrina">Doutrina</option>
              </select>
            </div>
            <div class="cf-field">
              <label>Artigo relacionado</label>
              <input type="text" id="fcEditArtigo" class="cf-input" placeholder="Ex: Art. 121 CP">
            </div>
          </div>
          <div class="cf-field">
            <label>Pergunta</label>
            <textarea id="fcEditPergunta" class="cf-textarea" rows="3" placeholder="Escreva a pergunta..."></textarea>
          </div>
          <div class="cf-field">
            <label>Resposta</label>
            <textarea id="fcEditResposta" class="cf-textarea" rows="3" placeholder="Escreva a resposta..."></textarea>
          </div>
          <div class="cf-field">
            <label>Observações</label>
            <textarea id="fcEditObs" class="cf-textarea" rows="2" placeholder="Contexto, exceções, macetes..."></textarea>
          </div>
          <div style="display:flex;gap:8px;margin-top:14px">
            <button class="btn-primary-sm" onclick="fcSalvarCardModal()">💾 Salvar Card</button>
            <button class="btn-ghost-sm" onclick="fcFecharModal()">Cancelar</button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- ═══ PAGE: ERROS FREQUENTES ═══ -->
  <div class="app-page" id="page-erros">
    <div class="page-header-row">
      <h2 class="page-title">❌ Erros Frequentes</h2>
      <button class="btn-primary-lg" onclick="estudarErros()">▶ Estudar Só Erros</button>
    </div>
    <p style="font-size:13px;color:var(--text-muted);margin-bottom:20px">Cards errados 3 ou mais vezes seguidas. Estude-os com atenção.</p>
    <div id="errosFrequentesList"></div>
  </div>

  <!-- ═══ PAGE: DESEMPENHO ═══ -->
  <div class="app-page" id="page-desempenho">
    <div class="page-header-row">
      <h2 class="page-title">📊 Desempenho</h2>
      <button class="btn-ghost-sm" onclick="renderDesempenho()">↺ Atualizar</button>
    </div>

    <!-- KPIs -->
    <div class="kpi-grid">
      <div class="kpi-card"><span class="kpi-num" id="kpiHoje">0</span><span class="kpi-lbl">Cards estudados hoje</span></div>
      <div class="kpi-card"><span class="kpi-num" id="kpiSemana">0</span><span class="kpi-lbl">Esta semana</span></div>
      <div class="kpi-card"><span class="kpi-num" id="kpiTaxa" style="color:var(--green)">—</span><span class="kpi-lbl">Taxa de acerto</span></div>
      <div class="kpi-card"><span class="kpi-num" id="kpiStreak" style="color:#fcb85c">0🔥</span><span class="kpi-lbl">Streak (dias)</span></div>
      <div class="kpi-card"><span class="kpi-num" id="kpiTotal">0</span><span class="kpi-lbl">Total de cards</span></div>
      <div class="kpi-card"><span class="kpi-num" id="kpiVencer" style="color:var(--hard)">0</span><span class="kpi-lbl">Para revisar hoje</span></div>
    </div>

    <!-- Por matéria -->
    <div class="section-title" style="margin-top:32px">📚 Desempenho por Matéria</div>
    <div id="desempMaterias"></div>

    <!-- Gráfico simples (últimos 7 dias) -->
    <div class="section-title" style="margin-top:32px">📈 Atividade (últimos 7 dias)</div>
    <div id="grafico7dias" class="grafico-wrap"></div>
  </div>

  <!-- ═══ PAGE: MODO PROVA ═══ -->
  <div class="app-page" id="page-prova">
    <div class="page-header-row">
      <h2 class="page-title">🎯 Modo Prova</h2>
    </div>
    <p style="font-size:13px;color:var(--text-muted);margin-bottom:24px">Simula condições de prova. Mostra apenas cards errados e os mais revisados.</p>
    <div class="prova-options">
      <div class="prova-opt-card" onclick="iniciarModoProva('erros')">
        <div class="poc-icon">❌</div>
        <div class="poc-title">Somente Erros</div>
        <div class="poc-desc">Cards com 3+ erros seguidos</div>
        <div class="poc-cnt" id="provaCntErros">0 cards</div>
      </div>
      <div class="prova-opt-card" onclick="iniciarModoProva('revisados')">
        <div class="poc-icon">🔁</div>
        <div class="poc-title">Mais Revisados</div>
        <div class="poc-desc">Cards com maior número de repetições</div>
        <div class="poc-cnt" id="provaCntRevisados">0 cards</div>
      </div>
      <div class="prova-opt-card" onclick="iniciarModoProva('todos')">
        <div class="poc-icon">🎲</div>
        <div class="poc-title">Aleatório Geral</div>
        <div class="poc-desc">Todos os cards em ordem aleatória</div>
        <div class="poc-cnt" id="provaCntTodos">0 cards</div>
      </div>
    </div>
    <div class="prova-filtros">
      <label style="font-size:12px;color:var(--text-muted)">Filtrar por matéria:</label>
      <select id="provaFiltroMateria" class="cf-select" style="min-width:200px">
        <option value="">Todas as matérias</option>
      </select>
    </div>
  </div>



<!-- ═══════════ FLOATING POMODORO TIMER ═══════════ -->
<button id="pomodoroFab" onclick="pfOpen()" title="Abrir Cronômetro Pomodoro">
  🍅
</button>

<div id="pomodoroFloat" class="hidden">
  <div class="pf-header" id="pfHeader">
    <div class="pf-title">
      <span class="pf-phase-dot"></span>
      🍅 Pomodoro
    </div>
    <button class="pf-close" onclick="pfClose()">✕</button>
  </div>
  <div class="pf-body">
    <!-- Ring -->
    <div class="pf-ring-wrap" onclick="pfToggle()" title="Clique para iniciar/pausar">
      <svg width="148" height="148" viewBox="0 0 148 148">
        <circle class="pf-ring-bg" cx="74" cy="74" r="60"/>
        <circle class="pf-ring-fill" id="pfRing" cx="74" cy="74" r="60"
          stroke="var(--accent)"
          stroke-dasharray="376.99"
          stroke-dashoffset="0"/>
      </svg>
      <div class="pf-ring-center">
        <span class="pf-phase-label" id="pfPhaseLabel">ESTUDO</span>
        <span class="pf-time" id="pfTime">50:00</span>
        <span class="pf-block-label" id="pfBlockLabel">Bloco 1 de 4</span>
      </div>
    </div>

    <!-- Dots -->
    <div class="pf-dots" id="pfDots"></div>

    <!-- Controls -->
    <div class="pf-controls">
      <button class="pf-btn pf-btn-start" id="pfStartBtn" onclick="pfToggle()">▶ Iniciar</button>
      <button class="pf-btn pf-btn-skip"  onclick="pfSkip()">⏭</button>
      <button class="pf-btn pf-btn-reset" onclick="pfReset()">↺</button>
    </div>

    <!-- Sound -->
    <div class="pf-sound-row">
      <span>🔔 Som de aviso</span>
      <div style="display:flex;align-items:center;gap:8px">
        <label class="pf-toggle">
          <input type="checkbox" id="pfSound" checked onchange="pfSoundLabel()">
          <div class="pf-track"></div>
          <div class="pf-knob"></div>
        </label>
        <span id="pfSoundLabel" style="color:var(--easy);font-size:10px">Ativado</span>
      </div>
    </div>
  </div>
</div>

<script>
// ── PRINCIPLE TABS ──
function togglePrinPanel(tab) {
  const panelId = 'panel-' + tab.dataset.panel;
  const panel = document.getElementById(panelId);
  const isActive = tab.classList.contains('active');

  // Close all
  document.querySelectorAll('.prin-tab').forEach(t => t.classList.remove('active'));
  document.querySelectorAll('.prin-panel').forEach(p => p.classList.remove('active'));

  if (!isActive) {
    tab.classList.add('active');
    // Color active tab
    const dot = tab.querySelector('.prin-dot');
    const c = dot ? dot.style.background : 'var(--accent)';
    tab.style.background = c.replace(')', ',.18)').replace('rgb(','rgba(') ;
    tab.style.borderColor = c;
    tab.style.color = '#fff';
    panel.classList.add('active');
  }
  // Reset non-active tabs styling
  document.querySelectorAll('.prin-tab:not(.active)').forEach(t => {
    t.style.background = '';
    t.style.borderColor = '';
    t.style.color = '';
  });
}

// ── HOURS GRID ──
const DAY_NAMES_SHORT = ['Seg','Ter','Qua','Qui','Sex','Sáb','Dom'];

// Load from localStorage or use defaults
function loadHoursData() {
  try {
    const saved = localStorage.getItem('sf_hoursData');
    if (saved) return JSON.parse(saved);
  } catch(e) {}
  return [3, 3, 3, 3, 3, 4, 2];
}
function saveHoursData() {
  try { localStorage.setItem('sf_hoursData', JSON.stringify(hoursData)); } catch(e) {}
}
const hoursData = loadHoursData();

function buildHoursGrid() {
  const grid = document.getElementById('hoursGrid');
  if (!grid) return;
  grid.innerHTML = '';

  DAY_NAMES_SHORT.forEach((d, i) => {
    const card = document.createElement('div');
    card.className = 'hours-day-card';

    const label = document.createElement('div');
    label.className = 'hours-day-label';
    label.textContent = d;

    // Stepper row: minus button, input, plus button
    const stepRow = document.createElement('div');
    stepRow.style.cssText = 'display:flex;align-items:center;gap:0;width:100%';

    const btnMinus = document.createElement('button');
    btnMinus.textContent = '−';
    btnMinus.style.cssText = `width:28px;height:44px;border-radius:6px 0 0 6px;border:1px solid var(--border);
      background:var(--surface2);color:var(--text-muted);font-size:18px;cursor:pointer;
      font-family:'Syne',sans-serif;transition:all .15s;flex-shrink:0;`;

    const inp = document.createElement('input');
    inp.type = 'number';
    inp.className = 'hours-number';
    inp.min = 0; inp.max = 16; inp.step = 0.5;
    inp.value = hoursData[i];
    inp.dataset.idx = i;
    inp.style.cssText += 'border-radius:0;border-left:none;border-right:none;';

    const btnPlus = document.createElement('button');
    btnPlus.textContent = '+';
    btnPlus.style.cssText = `width:28px;height:44px;border-radius:0 6px 6px 0;border:1px solid var(--border);
      background:var(--surface2);color:var(--text-muted);font-size:18px;cursor:pointer;
      font-family:'Syne',sans-serif;transition:all .15s;flex-shrink:0;`;

    function updateVal(v) {
      const clamped = Math.max(0, Math.min(16, parseFloat(v) || 0));
      const rounded = Math.round(clamped * 2) / 2; // snap to .5
      hoursData[i] = rounded;
      inp.value = rounded;
      saveHoursData();
      updateHoursGrid();
    }

    inp.addEventListener('input',  () => updateVal(inp.value));
    inp.addEventListener('change', () => updateVal(inp.value));
    inp.addEventListener('blur',   () => updateVal(inp.value));
    btnMinus.addEventListener('click', () => updateVal(hoursData[i] - 0.5));
    btnPlus.addEventListener('click',  () => updateVal(hoursData[i] + 0.5));

    // Hover effect on buttons
    [btnMinus, btnPlus].forEach(btn => {
      btn.addEventListener('mouseenter', () => { btn.style.color = 'var(--easy)'; btn.style.borderColor = 'var(--easy)'; });
      btn.addEventListener('mouseleave', () => { btn.style.color = ''; btn.style.borderColor = ''; });
    });

    stepRow.appendChild(btnMinus);
    stepRow.appendChild(inp);
    stepRow.appendChild(btnPlus);

    const unit = document.createElement('div');
    unit.className = 'hours-unit';
    unit.textContent = 'horas';

    const blocks = document.createElement('div');
    blocks.className = 'hours-blocks';
    blocks.id = `hoursBlocks-${i}`;

    card.appendChild(label);
    card.appendChild(stepRow);
    card.appendChild(unit);
    card.appendChild(blocks);
    grid.appendChild(card);
  });

  updateHoursGrid();
}

function updateHoursGrid() {
  const studyMin = parseInt(document.getElementById('pomoStudy')?.value || 50);
  const breakMin = parseInt(document.getElementById('pomoBreak')?.value || 10);
  const blockTotal = studyMin + breakMin;

  let total = 0;
  hoursData.forEach((h, i) => {
    total += h;
    const blocks = Math.floor((h * 60) / blockTotal);
    const el = document.getElementById(`hoursBlocks-${i}`);
    if (el) el.textContent = blocks > 0 ? `${blocks} bloco${blocks>1?'s':''} Pomodoro` : '—';
    // Also update the input value in case it was set programmatically
    const inp = document.querySelector(`#hoursGrid [data-idx="${i}"]`);
    if (inp && parseFloat(inp.value) !== h) inp.value = h;
  });

  const totalEl = document.getElementById('hoursTotalVal');
  if (totalEl) totalEl.textContent = total % 1 === 0 ? total : total.toFixed(1);

  const pomoTotal = Math.floor((total * 60) / blockTotal);
  const pomoEl = document.getElementById('pomoTotalVal');
  if (pomoEl) pomoEl.textContent = `${pomoTotal} blocos`;

  const maxWeek = 60;
  const pct = Math.min((total / maxWeek) * 100, 100);
  const bar = document.getElementById('hoursBarFill');
  if (bar) bar.style.width = pct + '%';

  // Show saved flash
  const saved = document.getElementById('hoursSavedBadge');
  if (saved) {
    saved.style.opacity = '1';
    clearTimeout(saved._t);
    saved._t = setTimeout(() => { saved.style.opacity = '0'; }, 1500);
  }
}

// ── SUBJECT & MATRIX STATE ──
const SUBJ_PALETTE = [
  { bg:'rgba(124,92,252,.18)', border:'rgba(124,92,252,.5)', text:'#b8a0ff', dot:'#7c5cfc' },
  { bg:'rgba(252,92,125,.18)', border:'rgba(252,92,125,.5)', text:'#ff9db5', dot:'#fc5c7d' },
  { bg:'rgba(92,240,176,.18)', border:'rgba(92,240,176,.5)', text:'#7affc9', dot:'#5cf0b0' },
  { bg:'rgba(252,184,92,.18)', border:'rgba(252,184,92,.5)', text:'#ffd08a', dot:'#fcb85c' },
  { bg:'rgba(92,184,252,.18)', border:'rgba(92,184,252,.5)', text:'#8ad4ff', dot:'#5cb8fc' },
  { bg:'rgba(192,92,252,.18)', border:'rgba(192,92,252,.5)', text:'#df8aff', dot:'#c05cfc' },
  { bg:'rgba(252,124,92,.18)', border:'rgba(252,124,92,.5)', text:'#ffb09a', dot:'#fc7c5c' },
  { bg:'rgba(240,220,92,.18)', border:'rgba(240,220,92,.5)', text:'#fff08a', dot:'#f0dc5c' },
  { bg:'rgba(92,240,240,.18)', border:'rgba(92,240,240,.5)', text:'#8affff', dot:'#5cf0f0' },
  { bg:'rgba(252,92,200,.18)', border:'rgba(252,92,200,.5)', text:'#ff8ae8', dot:'#fc5cc8' },
  { bg:'rgba(140,252,92,.18)', border:'rgba(140,252,92,.5)', text:'#b4ff8a', dot:'#8cfc5c' },
  { bg:'rgba(92,120,252,.18)', border:'rgba(92,120,252,.5)', text:'#8aaaff', dot:'#5c78fc' },
  { bg:'rgba(252,160,92,.18)', border:'rgba(252,160,92,.5)', text:'#ffcc8a', dot:'#fca05c' },
  { bg:'rgba(92,252,160,.18)', border:'rgba(92,252,160,.5)', text:'#8affcc', dot:'#5cfca0' },
];

// Default subjects
const DEFAULT_SUBJECTS = [
  { id:'s0', name:'Direito Penal' },
  { id:'s1', name:'Processo Penal' },
  { id:'s2', name:'Direito Civil' },
  { id:'s3', name:'Processo Civil' },
  { id:'s4', name:'Direito Constitucional' },
  { id:'s5', name:'Direito Administrativo' },
  { id:'s6', name:'Direito Tributário' },
  { id:'s7', name:'Direito do Trabalho' },
  { id:'s8', name:'Legislação Especial' },
  { id:'s9', name:'Ética e Estatuto' },
];
const DEFAULT_MATRIX = [
  [['s0'],['s2'],['s4'],['s6'],['s0'],['s2'],['s4']],
  [['s1'],['s3'],['s5'],['s7'],['s1'],['s3'],['s5']],
  [['s8'],['s9'],['s8'],['s9'],['s8'],['s9'],[]]
];

function loadSubjectsMatrix() {
  try {
    const raw = localStorage.getItem('sf_subjectsMatrix');
    if (raw) {
      const parsed = JSON.parse(raw);
      return { subjects: parsed.subjects || DEFAULT_SUBJECTS, matrix: parsed.matrix || DEFAULT_MATRIX, nextSubjId: parsed.nextSubjId || 10 };
    }
  } catch(e) {}
  return { subjects: JSON.parse(JSON.stringify(DEFAULT_SUBJECTS)), matrix: JSON.parse(JSON.stringify(DEFAULT_MATRIX)), nextSubjId: 10 };
}
function saveSubjectsMatrix() {
  try { localStorage.setItem('sf_subjectsMatrix', JSON.stringify({ subjects, matrix, nextSubjId })); } catch(e) {}
}

const _sm = loadSubjectsMatrix();
let subjects    = _sm.subjects;
let matrix      = _sm.matrix;
let nextSubjId  = _sm.nextSubjId;

function getSubj(id) { return subjects.find(s => s.id === id); }
function getPalette(id) {
  const idx = subjects.findIndex(s => s.id === id);
  return SUBJ_PALETTE[idx % SUBJ_PALETTE.length];
}

function renderSubjTabs() {
  const row = document.getElementById('subjTabsRow');
  row.innerHTML = '';

  subjects.forEach((s, i) => {
    const p = SUBJ_PALETTE[i % SUBJ_PALETTE.length];
    const pill = document.createElement('div');
    pill.className = 'subj-pill';
    pill.style.cssText = `background:${p.bg};border-color:${p.border};color:${p.text}`;
    pill.dataset.id = s.id;

    pill.innerHTML = `
      <span class="sp-dot" style="background:${p.dot}"></span>
      <input class="sp-name" type="text" value="${s.name}" title="Clique para editar o nome" placeholder="Matéria...">
      <span class="sp-remove" title="Remover matéria" onclick="removeSubject('${s.id}')">✕</span>`;

    pill.querySelector('.sp-name').addEventListener('input', e => {
      s.name = e.target.value;
      renderMatrix();
      saveSubjectsMatrix();
    });
    row.appendChild(pill);
  });

  // Add button
  const addBtn = document.createElement('button');
  addBtn.className = 'add-subj-btn';
  addBtn.innerHTML = '+ Nova Matéria';
  addBtn.onclick = addSubject;
  row.appendChild(addBtn);
}

function addSubject() {
  const id = 's' + nextSubjId++;
  subjects.push({ id, name: 'Nova Matéria' });
  saveSubjectsMatrix();
  renderSubjTabs();
  renderMatrix();
  setTimeout(() => {
    const inp = document.querySelector(`.subj-pill[data-id="${id}"] .sp-name`);
    if (inp) { inp.select(); }
  }, 50);
}

function removeSubject(id) {
  subjects = subjects.filter(s => s.id !== id);
  matrix = matrix.map(row => row.map(cell => cell.filter(c => c !== id)));
  saveSubjectsMatrix();
  renderSubjTabs();
  renderMatrix();
}

function renderMatrix() {
  const PERIODS    = ['☀️ Manhã', '🌤 Tarde', '🌙 Noite'];
  const DAYS_SHORT = ['Segunda','Terça','Quarta','Quinta','Sexta','Sábado','Domingo'];
  const tbody = document.getElementById('matrixBody');
  tbody.innerHTML = '';

  PERIODS.forEach((period, pi) => {
    const tr = document.createElement('tr');

    const tdPeriod = document.createElement('td');
    tdPeriod.className = 'matrix-period';
    tdPeriod.textContent = period;
    tr.appendChild(tdPeriod);

    DAYS_SHORT.forEach((day, di) => {
      const td = document.createElement('td');
      const wrap = document.createElement('div');
      wrap.className = 'cell-wrap';
      wrap.dataset.period = pi;
      wrap.dataset.day = di;

      const cellSubjs = matrix[pi][di] || [];

      if (cellSubjs.length === 0) {
        const empty = document.createElement('span');
        empty.className = 'subj-tag empty';
        empty.textContent = '— livre';
        wrap.appendChild(empty);
      } else {
        cellSubjs.forEach(sid => {
          const s = getSubj(sid);
          if (!s) return;
          const p = getPalette(sid);
          const tag = document.createElement('div');
          tag.className = 'subj-tag';
          tag.style.cssText = `background:${p.bg};border-color:${p.border};color:${p.text}`;
          tag.innerHTML = `<span class="tag-dot" style="background:${p.dot}"></span>${s.name}`;
          tag.title = 'Clique para remover deste horário';
          tag.onclick = () => { removeCellSubj(pi, di, sid); };
          wrap.appendChild(tag);
        });
      }

      // Dropdown to add subject to cell
      if (cellSubjs.length < 2) {
        const sel = document.createElement('select');
        sel.style.cssText = `background:var(--surface2);border:1px dashed var(--border);border-radius:4px;
          color:var(--text-dim);font-size:10px;padding:3px 6px;outline:none;cursor:pointer;
          font-family:'DM Mono',monospace;margin-top:2px;max-width:100%;`;
        sel.innerHTML = `<option value="">+ adicionar</option>` +
          subjects.filter(s => !cellSubjs.includes(s.id))
            .map(s => `<option value="${s.id}">${s.name}</option>`).join('');
        sel.onchange = () => {
          if (sel.value) { addCellSubj(pi, di, sel.value); sel.value = ''; }
        };
        wrap.appendChild(sel);
      }

      td.appendChild(wrap);
      tr.appendChild(td);
    });

    tbody.appendChild(tr);
  });
}

function addCellSubj(pi, di, sid) {
  if (!matrix[pi][di]) matrix[pi][di] = [];
  if (!matrix[pi][di].includes(sid) && matrix[pi][di].length < 2) {
    matrix[pi][di].push(sid);
    saveSubjectsMatrix();
    renderMatrix();
  }
}

function removeCellSubj(pi, di, sid) {
  matrix[pi][di] = (matrix[pi][di] || []).filter(s => s !== sid);
  saveSubjectsMatrix();
  renderMatrix();
}

// ── MAIN STATE ──
const DAYS = ['Segunda-feira','Terça-feira','Quarta-feira','Quinta-feira','Sexta-feira','Sábado','Domingo'];
const COLORS = ['#7c5cfc','#fc5c7d','#5cf0b0','#fcb85c','#5cb8fc','#fc7c5c','#c05cfc'];
const ICONS = ['📘','📗','📙','📕','📓','📒','📔'];

const SCHEDULES = [
  // Segunda — Terça — Quarta — etc
  [
    {time:'07:00–07:50', desc:'Bloco 1 — Matéria A', tag:'study'},
    {time:'07:50–08:00', desc:'Pausa ativa', tag:'break'},
    {time:'08:00–08:50', desc:'Bloco 2 — Matéria A (continuação)', tag:'study'},
    {time:'08:50–09:00', desc:'Pausa', tag:'break'},
    {time:'14:00–14:50', desc:'Bloco 3 — Matéria B', tag:'study'},
    {time:'14:50–15:00', desc:'Pausa ativa', tag:'break'},
    {time:'15:00–15:50', desc:'Bloco 4 — Matéria B (questões)', tag:'study'},
    {time:'20:00–20:30', desc:'Revisão do dia / Active recall', tag:'review'},
  ],
  [
    {time:'07:00–07:50', desc:'Bloco 1 — Matéria A', tag:'study'},
    {time:'07:50–08:00', desc:'Pausa ativa', tag:'break'},
    {time:'08:00–08:50', desc:'Bloco 2 — Revisão D+1 de ontem', tag:'review'},
    {time:'14:00–14:50', desc:'Bloco 3 — Matéria B', tag:'study'},
    {time:'14:50–15:00', desc:'Pausa', tag:'break'},
    {time:'15:00–15:50', desc:'Bloco 4 — Matéria B (mapa mental)', tag:'study'},
    {time:'20:00–20:30', desc:'Revisão rápida / Flashcards', tag:'review'},
  ],
  [
    {time:'07:00–07:50', desc:'Bloco 1 — Matéria A (questões)', tag:'study'},
    {time:'07:50–08:00', desc:'Pausa ativa', tag:'break'},
    {time:'08:00–08:50', desc:'Bloco 2 — Matéria A', tag:'study'},
    {time:'14:00–14:50', desc:'Bloco 3 — Matéria B', tag:'study'},
    {time:'14:50–15:00', desc:'Pausa', tag:'break'},
    {time:'15:00–15:50', desc:'Bloco 4 — Revisão D+7 da semana passada', tag:'review'},
    {time:'20:00–20:30', desc:'Active recall noturno', tag:'review'},
  ],
  [
    {time:'07:00–07:50', desc:'Bloco 1 — Matéria A', tag:'study'},
    {time:'07:50–08:00', desc:'Pausa ativa', tag:'break'},
    {time:'08:00–08:50', desc:'Bloco 2 — Matéria A (resumo)', tag:'study'},
    {time:'14:00–14:50', desc:'Bloco 3 — Matéria B', tag:'study'},
    {time:'14:50–15:00', desc:'Pausa', tag:'break'},
    {time:'15:00–15:50', desc:'Bloco 4 — Matéria B (questões)', tag:'study'},
    {time:'20:00–20:30', desc:'Revisão do dia', tag:'review'},
  ],
  [
    {time:'07:00–07:50', desc:'Bloco 1 — Matéria A', tag:'study'},
    {time:'07:50–08:00', desc:'Pausa ativa', tag:'break'},
    {time:'08:00–08:50', desc:'Bloco 2 — Revisão D+7 da semana', tag:'review'},
    {time:'14:00–14:50', desc:'Bloco 3 — Matéria B', tag:'study'},
    {time:'14:50–15:00', desc:'Pausa', tag:'break'},
    {time:'15:00–15:50', desc:'Bloco 4 — Matéria B', tag:'study'},
    {time:'20:00–20:30', desc:'Revisão semanal leve', tag:'review'},
  ],
  [
    {time:'09:00–09:50', desc:'Bloco 1 — Matéria A (revisão intensa)', tag:'study'},
    {time:'09:50–10:00', desc:'Pausa', tag:'break'},
    {time:'10:00–10:50', desc:'Bloco 2 — Questões gerais', tag:'study'},
    {time:'15:00–15:50', desc:'Bloco 3 — Matéria B', tag:'study'},
    {time:'15:50–16:00', desc:'Pausa ativa', tag:'break'},
    {time:'16:00–16:30', desc:'Revisão D+30 do mês', tag:'review'},
  ],
  [
    {time:'10:00–10:50', desc:'Revisão geral da semana — Matéria A', tag:'review'},
    {time:'10:50–11:00', desc:'Pausa ativa', tag:'break'},
    {time:'11:00–11:50', desc:'Revisão geral da semana — Matéria B', tag:'review'},
    {time:'15:00–15:30', desc:'Planejamento da próxima semana', tag:'study'},
    {time:'15:30–16:00', desc:'Active recall de tudo estudado', tag:'review'},
  ],
];

const METHODS = ['Leitura','Questões','Revisão','Mapa mental','Flashcards','Audiovisual','Resolução de problemas','Resumo escrito'];

// ── DATE HELPERS (bulletproof, no UTC confusion) ──

// Returns today as YYYY-MM-DD string in LOCAL time
function todayLocalStr() {
  const n = new Date();
  return `${n.getFullYear()}-${String(n.getMonth()+1).padStart(2,'0')}-${String(n.getDate()).padStart(2,'0')}`;
}

// Parse YYYY-MM-DD → local midnight Date (no timezone drift)
function parseLocalDate(str) {
  const [y,m,d] = str.split('-').map(Number);
  return new Date(y, m-1, d, 0, 0, 0, 0);
}

// Add days to a local Date
function addDaysLocal(date, n) {
  const r = new Date(date);
  r.setDate(r.getDate() + n);
  return r;
}

// ISO week number from local date
function getISOWeekFromDate(date) {
  const d = new Date(date.getFullYear(), date.getMonth(), date.getDate());
  d.setDate(d.getDate() + 4 - (d.getDay() || 7));
  const yearStart = new Date(d.getFullYear(), 0, 1);
  return {
    week: Math.ceil((((d - yearStart) / 86400000) + 1) / 7),
    year: d.getFullYear()
  };
}

// Monday of ISO week (local dates, no UTC)
function getMondayOfISOWeek(year, week) {
  const jan4   = new Date(year, 0, 4, 0, 0, 0, 0);
  const dow    = jan4.getDay() || 7;            // Mon=1…Sun=7
  const mon1   = new Date(jan4);
  mon1.setDate(jan4.getDate() - (dow - 1));     // Monday of week 1
  const result = new Date(mon1);
  result.setDate(mon1.getDate() + (week - 1) * 7);
  return result;                                // already local midnight
}

// Get the 7 real calendar dates for the current/selected week
function getWeekDates(weekVal) {
  if (!weekVal) {
    const now = new Date();
    const iso = getISOWeekFromDate(now);
    weekVal = `${iso.year}-W${String(iso.week).padStart(2,'0')}`;
  }
  const [yr, wk] = weekVal.split('-W');
  const monday   = getMondayOfISOWeek(parseInt(yr), parseInt(wk));
  return Array.from({length:7}, (_, i) => addDaysLocal(monday, i));
}

// Returns Monday of the selected week (local, no timezone drift)
function getWeekBase() {
  const weekVal = document.getElementById('weekPicker')?.value;
  if (!weekVal) {
    const n = new Date(); n.setHours(0,0,0,0); return n;
  }
  const [yr, wk] = weekVal.split('-W');
  return getMondayOfISOWeek(parseInt(yr), parseInt(wk));
}

function fmtDate(dt) {
  if (!dt) return '—';
  const d = (dt instanceof Date) ? dt : new Date(dt);
  return d.toLocaleDateString('pt-BR');
}

function nextRevDate(studyDate, intervalDays) {
  const d = new Date(studyDate);
  d.setDate(d.getDate() + Math.round(intervalDays));
  return d;
}

// ══════════════════════════════════════════════════
// ANKI INTELIGENTE — SM-2 COMPLETO
// ══════════════════════════════════════════════════

// SM-2 algorithm — returns new state
function sm2(state, grade) {
  let { ef=2.5, interval=1, reps=0 } = state;
  if (grade < 3) {
    reps=0; interval=1; ef=Math.max(1.3, ef-0.20);
  } else {
    if      (reps===0) interval=1;
    else if (reps===1) interval=6;
    else if (grade===3) interval=Math.ceil(interval*1.2);
    else if (grade===4) interval=Math.ceil(interval*ef);
    else                interval=Math.ceil(interval*ef*1.3);
    if      (grade===3) ef=Math.max(1.3, ef-0.15);
    else if (grade===5) ef=Math.min(ef+0.10, 4.0);
    reps++;
  }
  return { ef:parseFloat(ef.toFixed(2)), interval:Math.max(1,interval), reps };
}

// Predict next interval without applying
function sm2Preview(state, grade) {
  return sm2(state, grade).interval;
}

// Classify entry status
function ankiStatus(e) {
  const hoje = todayLocalStr();
  // Never been studied and no study date = new
  if (!e.reps && !e.studyDate) return 'novo';
  if (!e.reps) return 'novo';

  // Has been studied — calculate next review date
  const sd = (e.studyDate || hoje).substring(0, 10);
  let sdDate;
  try { sdDate = parseLocalDate(sd); } catch(_) { sdDate = new Date(); }
  const next    = addDaysLocal(sdDate, Math.max(1, e.interval || 1));
  const nextStr = next.getFullYear() + '-' +
    String(next.getMonth()+1).padStart(2,'0') + '-' +
    String(next.getDate()).padStart(2,'0');

  if (nextStr < hoje)   return 'atrasado';
  if (nextStr === hoje) return 'hoje';
  if ((e.interval || 1) >= 21) return 'dominado';
  return 'ok';
}

// ── Persistent store ──
let ankiEntries = [];
function loadAnkiEntries() {
  try { const r=localStorage.getItem('sf_ankiEntries'); if(r) ankiEntries=JSON.parse(r); } catch(e){ ankiEntries=[]; }
}
function saveAnkiEntries() {
  try { localStorage.setItem('sf_ankiEntries', JSON.stringify(ankiEntries)); } catch(e){}
}

// ── Filter state ──
let ankiFiltroAtivo = 'todos';

// ── Study session state ──
let ankiSessQueue = [];
let ankiSessIdx   = 0;
let ankiSessVirado = false;
let ankiSessAcertos = 0;
let ankiSessErros   = 0;

// ══ SYNC: pull all fcData cards into ankiEntries ══
function ankiSyncFromFlashcards() {
  if (typeof fcData === 'undefined') { showToast('Nenhum flashcard encontrado','warn'); return; }
  let added = 0, updated = 0;
  const hoje = todayLocalStr();
  fcData.materias.forEach(m => {
    m.topicos.forEach(t => {
      t.cards.forEach(c => {
        const existing = ankiEntries.find(e => e.fcCardId === c.id);
        if (existing) {
          // Update SM-2 data from fcData (keep the one with more reps)
          if ((c.repeticoes||0) > (existing.reps||0)) {
            existing.ef       = c.facilidade || existing.ef;
            existing.interval = c.intervalo  || existing.interval;
            existing.reps     = c.repeticoes || existing.reps;
            existing.studyDate= existing.studyDate || hoje;
            updated++;
          }
        } else {
          ankiEntries.push({
            id: 'fc_'+c.id, fcCardId: c.id,
            name: m.name + ' › ' + t.name,
            pergunta: c.q || '', resposta: c.a || '',
            tipo: c.tipo||'', artigo: c.artigo||'', obs: c.obs||'',
            materiaColor: m.color||'#7c5cfc', materiaNome: m.name,
            ef: c.facilidade||2.5, interval: c.intervalo||1,
            reps: c.repeticoes||0, lastGrade: null,
            studyDate: hoje,
            blockId: null,
          });
          added++;
        }
      });
    });
  });
  saveAnkiEntries();
  renderAnkiTable();
  ankiUpdateDashboard();
  showToast(`✓ Sincronizado — ${added} novos, ${updated} atualizados`, 'ok');
}

// ══ DASHBOARD ══
function ankiUpdateDashboard() {
  const hoje = todayLocalStr();
  let total=0, atrasado=0, hojeQ=0, novos=0, aprendendo=0, dominados=0;
  ankiEntries.forEach(e => {
    total++;
    const s = ankiStatus(e);
    if (s==='atrasado') atrasado++;
    if (s==='hoje')     hojeQ++;
    if (s==='novo')     novos++;
    if (s==='dominado') dominados++;
    if (s==='ok')       aprendendo++;
  });

  function setEl(id, val) {
    const el = document.getElementById(id);
    if (el) el.textContent = val;
  }
  setEl('adcTotal',     total);
  setEl('adcAtrasado',  atrasado);
  setEl('adcHoje',      hojeQ);
  setEl('adcNovos',     novos);
  setEl('adcAprendendo',aprendendo);
  setEl('adcDominados', dominados);

  const btn = document.getElementById('ankiBtnSessao');
  if (btn) {
    const urgente = atrasado + hojeQ + novos;
    btn.textContent = urgente > 0 ? `▶ Estudar agora (${urgente})` : '▶ Estudar';
    btn.style.animation = urgente > 0 ? 'pulse 2s ease-in-out infinite' : 'none';
  }
}

// ══ FILTER ══
function ankiSetFiltro(btn, filtro) {
  document.querySelectorAll('.anki-filter-btn').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
  ankiFiltroAtivo = filtro;
  renderAnkiTable();
}

// ══ RENDER TABLE ══
function renderAnkiTable() {
  const tbody = document.getElementById('revisionTbody');
  if (!tbody) return;

  const hoje   = todayLocalStr();
  const busca  = (document.getElementById('ankiBusca')?.value||'').toLowerCase();

  // Filter
  let lista = ankiEntries.filter(e => {
    if (busca && !e.name.toLowerCase().includes(busca) &&
        !(e.pergunta||'').toLowerCase().includes(busca)) return false;
    const s = ankiStatus(e);
    if (ankiFiltroAtivo==='atrasados')  return s==='atrasado';
    if (ankiFiltroAtivo==='hoje')       return s==='hoje';
    if (ankiFiltroAtivo==='novos')      return s==='novo';
    if (ankiFiltroAtivo==='dominados')  return s==='dominado';
    return true;
  });

  // Sort: atrasados > hoje > novos > aprendendo > dominados
  const ordem = {atrasado:0, hoje:1, novo:2, ok:3, dominado:4};
  lista.sort((a,b) => (ordem[ankiStatus(a)]||5) - (ordem[ankiStatus(b)]||5));

  if (lista.length===0) {
    tbody.innerHTML=`<tr><td colspan="8" style="text-align:center;color:var(--text-dim);padding:32px">
      ${ankiFiltroAtivo==='todos'
        ? 'Nenhuma revisão ainda. Preencha matérias nos dias ou clique 🔄 Sincronizar para importar seus flashcards.'
        : 'Nenhum item nesta categoria.'}
    </td></tr>`;
    ankiUpdateDashboard();
    return;
  }

  const tipoLabel = {lei:'Lei Seca', juris:'Juris.', doutrina:'Doutrina'};

  tbody.innerHTML = lista.map(e => {
    const sd      = e.studyDate ? parseLocalDate(e.studyDate.substring(0,10)) : new Date();
    const next    = addDaysLocal(sd, e.interval);
    const nextStr = next.getFullYear()+'-'+String(next.getMonth()+1).padStart(2,'0')+'-'+String(next.getDate()).padStart(2,'0');
    const status  = ankiStatus(e);
    const efPct   = Math.round(((e.ef||2.5)-1.3)/(4.0-1.3)*100);
    const efColor = (e.ef||2.5)>=2.5?'var(--easy)':(e.ef||2.5)>=1.8?'var(--accent4)':'var(--hard)';

    const statusHtml = {
      atrasado:`<span class="anki-status-badge asb-atrasado">⚠ Atrasado</span>`,
      hoje:    `<span class="anki-status-badge asb-hoje">📅 Hoje</span>`,
      novo:    `<span class="anki-status-badge asb-novo">✨ Novo</span>`,
      ok:      `<span class="anki-status-badge asb-ok">✓ Em dia</span>`,
      dominado:`<span class="anki-status-badge asb-dominado">🏆 Dominado</span>`,
    }[status]||'';

    // Preview next intervals
    const pAgain = sm2Preview(e, 0);
    const pHard  = sm2Preview(e, 3);
    const pGood  = sm2Preview(e, 4);
    const pEasy  = sm2Preview(e, 5);

    return `
    <tr>
      <td>
        <div style="display:flex;flex-direction:column;gap:3px">
          <div style="display:flex;align-items:center;gap:6px">
            <span style="font-weight:700;color:${e.materiaColor||'var(--accent)'}">
              ${e.materiaNome||e.name.split(' › ')[0]||e.name}
            </span>
            <button onclick="deleteAnkiEntry('${e.id}')" title="Remover"
              style="background:transparent;border:none;color:var(--text-dim);cursor:pointer;font-size:12px;padding:1px 4px;border-radius:3px;line-height:1;flex-shrink:0"
              onmouseover="this.style.color='var(--hard)'" onmouseout="this.style.color='var(--text-dim)'">✕</button>
          </div>
          <span style="font-size:11px;color:var(--text-muted)">${e.name.includes('›')?e.name.split('›').slice(1).join('›').trim():''}</span>
          ${e.tipo?`<span style="font-size:9px;color:var(--text-dim)">${tipoLabel[e.tipo]||e.tipo}</span>`:''}
        </div>
      </td>
      <td>
        <span style="font-size:11px;color:var(--text)">${fmtDate(sd)}</span>
        <div style="font-size:10px;color:var(--text-dim);margin-top:2px">
          D+1: ${fmtDate(addDaysLocal(sd,1))} · D+7: ${fmtDate(addDaysLocal(sd,7))}
        </div>
      </td>
      <td>
        <span class="rev-badge" style="background:${status==='atrasado'?'rgba(252,92,125,.12)':status==='hoje'?'rgba(124,92,252,.12)':'rgba(92,240,176,.1)'};
          border:1px solid ${status==='atrasado'?'rgba(252,92,125,.3)':status==='hoje'?'rgba(124,92,252,.3)':'rgba(92,240,176,.25)'};
          color:${status==='atrasado'?'var(--hard)':status==='hoje'?'var(--accent)':'var(--easy)'}">
          ${fmtDate(next)}${status==='atrasado'?' ⚠':''}
        </span>
      </td>
      <td>
        <span style="font-family:'Syne',sans-serif;font-weight:700;font-size:13px;color:var(--accent)">
          ${e.interval}d
        </span>
      </td>
      <td>
        <div class="ef-bar-wrap">
          <div class="ef-bar"><div class="ef-bar-fill" style="width:${efPct}%;background:${efColor}"></div></div>
          <span style="font-size:11px;color:${efColor};font-weight:700;min-width:28px">${e.ef||2.5}</span>
        </div>
      </td>
      <td style="text-align:center">
        <span style="font-family:'Syne',sans-serif;font-weight:700;font-size:14px;color:var(--text-muted)">${e.reps||0}</span>
      </td>
      <td>${statusHtml}</td>
      <td>
        <div class="anki-btns">
          <button class="anki-btn again ${e.lastGrade===0?'active':''}" onclick="applyAnkiGrade('${e.id}',0,this)" title="Próxima: ${pAgain}d">😵 Again<br><span style="font-size:9px;opacity:.7">${pAgain}d</span></button>
          <button class="anki-btn hard  ${e.lastGrade===3?'active':''}" onclick="applyAnkiGrade('${e.id}',3,this)" title="Próxima: ${pHard}d">😓 Hard<br><span style="font-size:9px;opacity:.7">${pHard}d</span></button>
          <button class="anki-btn good  ${e.lastGrade===4?'active':''}" onclick="applyAnkiGrade('${e.id}',4,this)" title="Próxima: ${pGood}d">😊 Good<br><span style="font-size:9px;opacity:.7">${pGood}d</span></button>
          <button class="anki-btn easy  ${e.lastGrade===5?'active':''}" onclick="applyAnkiGrade('${e.id}',5,this)" title="Próxima: ${pEasy}d">🚀 Easy<br><span style="font-size:9px;opacity:.7">${pEasy}d</span></button>
        </div>
      </td>
    </tr>`;
  }).join('');

  ankiUpdateDashboard();
}

function applyAnkiGrade(id, grade, btnEl) {
  const entry = ankiEntries.find(e=>e.id===id);
  if (!entry) return;
  btnEl.closest('.anki-btns').querySelectorAll('.anki-btn').forEach(b=>b.classList.remove('active'));
  btnEl.classList.add('active');

  // Set studyDate to today on first review
  if (!entry.studyDate || !entry.reps) entry.studyDate = todayLocalStr();

  const updated = sm2(entry, grade);
  Object.assign(entry, updated, { lastGrade: grade });

  // Sync back to fcData if linked
  if (entry.fcCardId && typeof fcData!=='undefined') {
    fcData.materias.forEach(m => m.topicos.forEach(t => {
      const c = t.cards.find(c=>c.id===entry.fcCardId);
      if (c) { c.facilidade=entry.ef; c.intervalo=entry.interval; c.repeticoes=entry.reps; }
    }));
    fcSaveData();
  }
  saveAnkiEntries();
  renderAnkiTable();
  showToast(`✓ Próxima revisão em ${entry.interval} dia${entry.interval>1?'s':''}`, 'ok');
}

function deleteAnkiEntry(id) {
  ankiEntries = ankiEntries.filter(e=>e.id!==id);
  saveAnkiEntries(); renderAnkiTable(); ankiUpdateDashboard();
}

function addAnkiEntry() {
  const nameEl = document.getElementById('ankiAddName');
  const dateEl = document.getElementById('ankiAddDate');
  const name   = nameEl?.value.trim();
  if (!name) { nameEl?.focus(); return; }
  const dateVal   = dateEl?.value || todayLocalStr();
  ankiEntries.push({
    id:'manual_'+Date.now(), fcCardId:null,
    name, pergunta:'', resposta:'', tipo:'', artigo:'', obs:'',
    materiaColor:'#7c5cfc', materiaNome: name.split('›')[0].trim(),
    ef:2.5, interval:1, reps:0, lastGrade:null, studyDate:dateVal, blockId:null
  });
  if (nameEl) nameEl.value='';
  document.getElementById('ankiAddPanel').style.display='none';
  saveAnkiEntries(); renderAnkiTable(); ankiUpdateDashboard();
  showToast('✓ Item adicionado ao Anki!','ok');
}

function ankiAddManual() {
  const panel = document.getElementById('ankiAddPanel');
  if (!panel) return;
  panel.style.display = panel.style.display==='none'?'block':'none';
  if (panel.style.display==='block') {
    // Set today's date
    const el = document.getElementById('ankiAddDate');
    if (el && !el.value) el.value = todayLocalStr();
    document.getElementById('ankiAddName')?.focus();
  }
}

// ══ STUDY SESSION ══
function ankiIniciarSessao() {
  // Priority: atrasados → hoje → novos
  const hoje = todayLocalStr();
  const prioridade = (e) => {
    const s = ankiStatus(e);
    if (s==='atrasado') return 0;
    if (s==='hoje')     return 1;
    if (s==='novo')     return 2;
    return 99;
  };
  const fila = ankiEntries
    .filter(e => prioridade(e) < 99)
    .sort((a,b) => prioridade(a)-prioridade(b) || (a.ef||2.5)-(b.ef||2.5));

  if (!fila.length) {
    showToast('🎉 Tudo em dia! Nenhum card para revisar agora.','ok');
    return;
  }

  ankiSessQueue   = fila;
  ankiSessIdx     = 0;
  ankiSessVirado  = false;
  ankiSessAcertos = 0;
  ankiSessErros   = 0;

  document.getElementById('ankiSessaoArea').style.display  = 'block';
  document.getElementById('ankiListaArea').style.display   = 'none';
  document.getElementById('ankiDashboard').style.display   = 'none';
  document.getElementById('ankiBtnSessao').style.display   = 'none';

  ankiCarregarCard();
}

function ankiFecharSessao() {
  document.getElementById('ankiSessaoArea').style.display  = 'none';
  document.getElementById('ankiListaArea').style.display   = 'block';
  document.getElementById('ankiDashboard').style.display   = '';
  document.getElementById('ankiBtnSessao').style.display   = '';
  renderAnkiTable();
  ankiUpdateDashboard();
  const total = ankiSessAcertos + ankiSessErros;
  if (total > 0) {
    const taxa = Math.round(ankiSessAcertos / total * 100);
    showToast(`Sessão: ${total} cards · ${taxa}% acerto`, 'ok');
  }
}

function ankiCarregarCard() {
  const card = ankiSessQueue[ankiSessIdx];
  if (!card) {
    // Done!
    ankiFecharSessao();
    showToast('🎉 Sessão concluída! Ótimo trabalho.','ok');
    return;
  }

  ankiSessVirado = false;
  const inner = document.getElementById('ankiCardInner');
  if (inner) { inner.style.transition='none'; inner.classList.remove('flipped'); setTimeout(()=>{inner.style.transition='';},50); }
  document.getElementById('ankiGradeRow').style.display   = 'none';
  document.getElementById('ankiRevealBtn').style.display  = 'block';

  // Populate card
  const mat = document.getElementById('ankiCardMateria');
  if (mat) {
    mat.textContent = card.materiaNome || card.name;
    mat.style.cssText = `background:${card.materiaColor||'#7c5cfc'}22;border:1px solid ${card.materiaColor||'#7c5cfc'}44;color:${card.materiaColor||'#7c5cfc'};border-radius:100px;padding:3px 10px;font-size:11px;display:inline-flex;margin-bottom:14px;width:fit-content;font-weight:700`;
  }
  document.getElementById('ankiCardPergunta').textContent = card.pergunta || card.name;
  document.getElementById('ankiCardArtigo').textContent   = card.artigo ? '📌 '+card.artigo : '';
  document.getElementById('ankiCardResposta').textContent = card.resposta || '(sem resposta cadastrada)';
  document.getElementById('ankiCardObs').textContent      = card.obs || '';
  document.getElementById('ankiCardObs').style.display    = card.obs ? '' : 'none';

  // Preview intervals
  document.getElementById('agbAgain').textContent = sm2Preview(card,0)+'d';
  document.getElementById('agbHard').textContent  = sm2Preview(card,3)+'d';
  document.getElementById('agbGood').textContent  = sm2Preview(card,4)+'d';
  document.getElementById('agbEasy').textContent  = sm2Preview(card,5)+'d';

  // Progress
  const total = ankiSessQueue.length;
  const done  = ankiSessIdx;
  const pct   = Math.round(done/total*100);
  const progBar = document.getElementById('ankiSessProgBar');
  if (progBar) progBar.style.width = pct+'%';
  document.getElementById('ankiSessProgLabel').textContent = `${done} / ${total}`;
  document.getElementById('ankiSessStats').textContent     =
    `✅ ${ankiSessAcertos}  ❌ ${ankiSessErros}`;
}

function ankiVirar() {
  if (ankiSessVirado) return;
  ankiSessVirado = true;
  document.getElementById('ankiCardInner').classList.add('flipped');
  setTimeout(()=>{
    document.getElementById('ankiRevealBtn').style.display='none';
    document.getElementById('ankiGradeRow').style.display='grid';
  }, 300);
}

function ankiResponder(grade) {
  const card = ankiSessQueue[ankiSessIdx];
  if (!card) return;

  // Set study date on first review
  if (!card.studyDate || !card.reps) card.studyDate = todayLocalStr();

  const updated = sm2(card, grade);
  Object.assign(card, updated, { lastGrade: grade });

  // Sync to main ankiEntries list
  const entry = ankiEntries.find(e => e.id === card.id);
  if (entry) {
    entry.studyDate = card.studyDate;
    Object.assign(entry, updated, { lastGrade: grade });
  }

  // Sync to fcData flashcards if linked
  if (card.fcCardId && typeof fcData !== 'undefined') {
    fcData.materias.forEach(m => m.topicos.forEach(t => {
      const c = t.cards.find(c => c.id === card.fcCardId);
      if (c) { c.facilidade = card.ef; c.intervalo = card.interval; c.repeticoes = card.reps; }
    }));
    fcSaveData();
  }
  saveAnkiEntries();

  if (grade === 0) ankiSessErros++; else ankiSessAcertos++;
  ankiSessIdx++;

  // Animate card out then load next
  const inner = document.getElementById('ankiCardInner');
  if (inner) {
    inner.style.transition = 'transform .2s, opacity .2s';
    inner.style.transform  = 'scale(.95)';
    inner.style.opacity    = '0';
    setTimeout(() => {
      inner.style.transition = '';
      inner.style.transform  = '';
      inner.style.opacity    = '';
      ankiCarregarCard();
    }, 200);
  }
}

// updateRevisions now also calls ankiUpdateDashboard
function updateRevisions() {
  const weekVal   = document.getElementById('weekPicker')?.value;
  const weekDates = getWeekDates(weekVal);

  for (let d=0; d<7; d++) {
    for (let b=0; b<2; b++) {
      const blockId = `d${d}b${b}`;
      const subj    = document.getElementById(`subj-${blockId}`);
      const chips   = document.getElementById(`revChips-${blockId}`);

      if (!subj || !subj.value.trim()) {
        if (chips) chips.innerHTML=`<span class="rev-chip">Preencha a matéria acima</span>`;
        continue;
      }

      const studyDate = weekDates[d];
      const sdStr = `${studyDate.getFullYear()}-${String(studyDate.getMonth()+1).padStart(2,'0')}-${String(studyDate.getDate()).padStart(2,'0')}`;

      let entry = ankiEntries.find(e=>e.blockId===blockId);
      if (!entry) {
        entry = {
          id:blockId, blockId, fcCardId:null,
          name:subj.value.trim(), pergunta:'', resposta:'',
          tipo:'', artigo:'', obs:'',
          materiaColor:'#7c5cfc', materiaNome:subj.value.trim(),
          ef:2.5, interval:1, reps:0, lastGrade:null, studyDate:sdStr
        };
        ankiEntries.push(entry);
      } else {
        entry.name      = subj.value.trim();
        entry.studyDate = sdStr;
      }

      const sd  = parseLocalDate(entry.studyDate);
      const d1  = addDaysLocal(sd,1);
      const d7  = addDaysLocal(sd,7);
      const d30 = addDaysLocal(sd,30);
      const nxt = addDaysLocal(sd,entry.interval);
      if (chips) {
        chips.innerHTML=`
          <span class="rev-chip" style="background:rgba(124,92,252,.1);border-color:rgba(124,92,252,.35);color:#b8a0ff">📅 ${fmtDate(sd)}</span>
          <span class="rev-chip" style="background:rgba(252,92,125,.08);border-color:rgba(252,92,125,.3);color:var(--hard)">D+1: ${fmtDate(d1)}</span>
          <span class="rev-chip" style="background:rgba(252,184,92,.08);border-color:rgba(252,184,92,.3);color:var(--accent4)">D+7: ${fmtDate(d7)}</span>
          <span class="rev-chip" style="background:rgba(92,240,176,.08);border-color:rgba(92,240,176,.3);color:var(--easy)">D+30: ${fmtDate(d30)}</span>
          <span class="rev-chip" style="background:rgba(124,92,252,.06);border-color:rgba(124,92,252,.2);color:var(--accent)">EF ${entry.ef} · próx. ${fmtDate(nxt)}</span>`;
      }
    }
  }
  saveAnkiEntries();
  renderAnkiTable();
  ankiUpdateDashboard();
}

function buildDayPanel(dayIndex) {
  const color = COLORS[dayIndex];
  const icon = ICONS[dayIndex];
  const sched = SCHEDULES[dayIndex];

  const panel = document.createElement('div');
  panel.className = 'day-panel' + (dayIndex === 0 ? ' active' : '');
  panel.id = `day-${dayIndex}`;

  // Subject blocks
  const blocksHtml = `
    <div class="blocks-grid">
      ${buildBlock(dayIndex, 0, color)}
      ${buildBlock(dayIndex, 1, COLORS[(dayIndex+3)%7])}
    </div>`;

  // Timeline
  const slotsHtml = sched.map(s => `
    <div class="slot">
      <span class="slot-time">${s.time}</span>
      <span class="slot-desc">${s.desc}</span>
      <span class="slot-tag tag-${s.tag}">${s.tag === 'study' ? '● Estudo' : s.tag === 'break' ? '◌ Pausa' : '↺ Revisão'}</span>
    </div>`).join('');

  // Day notes
  const notesHtml = `
    <div class="day-notes-grid">
      <div class="card">
        <div class="card-label"><span class="icon">🎯</span> Metas do Dia</div>
        <textarea rows="3" placeholder="O que preciso concluir hoje?"></textarea>
      </div>
      <div class="card">
        <div class="card-label"><span class="icon">💬</span> Observações do Dia</div>
        <textarea rows="3" placeholder="Como foi o estudo? Pontos de atenção..."></textarea>
      </div>
    </div>`;

  panel.innerHTML = `
    <div style="margin-bottom:28px; display:flex; align-items:center; gap:12px; flex-wrap:wrap;">
      <span style="font-family:'Syne',sans-serif; font-size:22px; font-weight:800; color:${color}">${DAYS[dayIndex]}</span>
      <span id="dayDateBadge-${dayIndex}" style="
        font-size:12px; color:var(--text-muted);
        background:var(--surface2); border:1px solid var(--border);
        border-radius:100px; padding:4px 14px;
        font-family:'DM Mono',monospace; letter-spacing:.03em;
      ">—</span>
      <div class="day-subtitle-wrap" onclick="this.querySelector('input').focus()">
        <span style="font-size:11px;color:var(--text-dim);flex-shrink:0">📚</span>
        <input type="text" class="day-subtitle-input" id="daySubtitle-${dayIndex}"
          value=""
          placeholder="Clique para descrever o plano do dia...">
      </div>
    </div>
    ${blocksHtml}
    <div class="timeline">
      <div class="timeline-label">⏱ Cronograma do Dia</div>
      <div class="timeline-slots">${slotsHtml}</div>
    </div>
    ${notesHtml}
    <div class="day-save-bar">
      <div class="day-save-info">
        💾 Ao salvar, os dados deste dia serão <strong>registrados no histórico</strong> e os campos serão limpos para um novo preenchimento.
      </div>
      <button class="btn-save-day" onclick="saveDay(${dayIndex})">
        ✓ Salvar ${DAYS[dayIndex].split('-')[0]}
      </button>
    </div>
  `;

  return panel;
}

function buildBlock(dayIndex, blockIndex, color) {
  const blockId = `d${dayIndex}b${blockIndex}`;
  const methodsHtml = METHODS.map(m =>
    `<button class="method-btn" data-block="${blockId}" data-method="${m}" onclick="toggleMethod(this)">${m}</button>`
  ).join('');

  return `
  <div class="block" id="block-${blockId}">
    <div class="block-accent-bar" style="background:linear-gradient(90deg,${color},transparent)"></div>
    <div class="block-header">
      <div class="block-title-group">
        <div class="block-icon" style="background:${color}20; color:${color}">📚</div>
        <input class="block-subject" type="text" placeholder="Nome da matéria / tópico" id="subj-${blockId}"
          oninput="updateRevisions()">
      </div>
      <div style="display:flex;align-items:center;gap:10px;">
        <span class="block-time" id="time-${blockId}">
          <span id="timeDisp-${blockId}">07:00</span>
          <span style="opacity:.4">|</span>50 min
        </span>
        <div class="check-done" id="check-${blockId}" onclick="toggleDone('${blockId}')" title="Marcar como concluído">✓</div>
      </div>
    </div>
    <div class="block-body">

      <div class="field-row">
        <span class="field-label">Horário de início</span>
        <input type="time" class="field-input" value="07:00" onchange="document.getElementById('timeDisp-${blockId}').textContent=this.value">
      </div>

      <div class="field-row">
        <span class="field-label">Nível de dificuldade percebida</span>
        <div class="diff-row">
          <button class="diff-btn" data-diff="easy" data-block="${blockId}" onclick="selectDiff(this)">😊 Fácil</button>
          <button class="diff-btn" data-diff="med"  data-block="${blockId}" onclick="selectDiff(this)">😐 Médio</button>
          <button class="diff-btn" data-diff="hard" data-block="${blockId}" onclick="selectDiff(this)">😓 Difícil</button>
        </div>
      </div>

      <div class="field-row">
        <span class="field-label">Método utilizado</span>
        <div class="method-row">${methodsHtml}</div>
      </div>

      <div class="field-row">
        <span class="field-label">Anotações & pontos de dificuldade</span>
        <textarea class="field-input" rows="3" placeholder="Conceitos que precisam de atenção, dúvidas, insights..."></textarea>
      </div>

      <div class="field-row">
        <span class="field-label">Desempenho (0–10)</span>
        <input type="number" min="0" max="10" class="field-input" placeholder="Ex: 7" style="max-width:120px">
      </div>

      <div class="field-row">
        <span class="field-label">🃏 Próxima Revisão Anki</span>
        <div class="revision-chips" id="revChips-${blockId}">
          <span class="rev-chip">Preencha a matéria e avalie na tabela abaixo</span>
        </div>
      </div>

    </div>
  </div>`;
}

function toggleDone(blockId) {
  const el = document.getElementById('check-' + blockId);
  el.classList.toggle('done');
}

function selectDiff(btn) {
  const blockId = btn.dataset.block;
  document.querySelectorAll(`.diff-btn[data-block="${blockId}"]`).forEach(b => b.classList.remove('sel'));
  btn.classList.add('sel');
}

function toggleMethod(btn) {
  btn.classList.toggle('sel');
}


// Build panels
const panelsContainer = document.getElementById('dayPanels');
for (let i = 0; i < 7; i++) {
  panelsContainer.appendChild(buildDayPanel(i));
}

// Init subject tabs and matrix
renderSubjTabs();
renderMatrix();

// Init hours grid
buildHoursGrid();

// Init Anki entries from localStorage
loadAnkiEntries();
renderAnkiTable();
ankiUpdateDashboard();

// Refresh day labels with real dates (called after panels exist)
setTimeout(refreshDayLabels, 100);

// Tabs
document.getElementById('daysNav').addEventListener('click', e => {
  const tab = e.target.closest('.day-tab');
  if (!tab) return;
  document.querySelectorAll('.day-tab').forEach(t => t.classList.remove('active'));
  document.querySelectorAll('.day-panel').forEach(p => p.classList.remove('active'));
  tab.classList.add('active');
  document.getElementById(`day-${tab.dataset.day}`).classList.add('active');
});

// ══════════════════════════════════════════════════
// FLASHCARD SYSTEM — Hierárquico: Matéria › Tópico › Card
// ══════════════════════════════════════════════════

// ── DATA STRUCTURE ──
// fcData = { materias: [ { id, name, color, topicos: [ { id, name, cards: [ {id,q,a,ef,interval,reps,due,lastGrade} ] } ] } ] }

const FC_KEY = 'sf_fcData_v2';

function fcLoadData() {
  // Try dedicated key first
  try {
    const raw = localStorage.getItem(FC_KEY);
    if (raw) {
      const parsed = JSON.parse(raw);
      if (parsed && parsed.materias) return parsed;
    }
  } catch(e) {}
  // Fallback: try to recover from master state
  try {
    const master = JSON.parse(localStorage.getItem(SF_KEY) || '{}');
    if (master.fcData && master.fcData.materias) return master.fcData;
  } catch(e) {}
  // Return empty structure (no defaults that would overwrite user data)
  return { materias: [] };
}

function fcSaveData() {
  try {
    localStorage.setItem(FC_KEY, JSON.stringify(fcData));
    // Also embed in master state key so it survives HTML updates
    try {
      const master = JSON.parse(localStorage.getItem(SF_KEY) || '{}');
      master.fcData = fcData;
      master.fcDataTs = Date.now();
      localStorage.setItem(SF_KEY, JSON.stringify(master));
    } catch(e2) {}
  } catch(e) {}
  updateBackupStats();
  fcRenderDueSummary();
}

let fcData = fcLoadData();

// Navigation state
let fcActiveMateria = null; // materia id
let fcActiveTopico  = null; // topico id

// Study state
let fcStudyQueue = [];
let fcStudyIndex = 0;
let fcFlipped    = false;
let fcSession    = { again:0, hardGood:0, easy:0 };

// Unique id generator
let fcIdCounter = Date.now();
function fcNewId() { return 'fc' + (fcIdCounter++); }

const FC_COLORS = ['#7c5cfc','#fc5c7d','#5cf0b0','#fcb85c','#5cb8fc','#c05cfc','#fc7c5c','#f0dc5c'];

// ── FIND HELPERS ──
function fcGetMateria(mid) { return fcData.materias.find(m=>m.id===mid); }
function fcGetTopico(mid,tid) {
  const m = fcGetMateria(mid);
  return m ? m.topicos.find(t=>t.id===tid) : null;
}
function fcGetCard(mid,tid,cid) {
  const t = fcGetTopico(mid,tid);
  return t ? t.cards.find(c=>c.id===cid) : null;
}
function fcAllCards() {
  const all = [];
  fcData.materias.forEach(m => m.topicos.forEach(t => t.cards.forEach(c => all.push({...c,materia:m.name,topico:t.name,mid:m.id,tid:t.id}))));
  return all;
}

// ── RENDER MATERIA TABS ──
function fcRenderMateriaTabs() {
  const row = document.getElementById('fcMateriaTabsRow');
  if (!row) return;
  row.innerHTML = '';

  fcData.materias.forEach((m,i) => {
    const totalCards = m.topicos.reduce((s,t)=>s+t.cards.length,0);
    const color = m.color || FC_COLORS[i%FC_COLORS.length];
    const tab = document.createElement('div');
    tab.className = 'fc-materia-tab' + (m.id===fcActiveMateria?' active':'');
    tab.style.cssText = m.id===fcActiveMateria
      ? `background:${color};border-color:${color};color:#fff`
      : `border-color:${color}30;color:${color}`;
    tab.innerHTML = `
      <span>${m.name}</span>
      <span class="fc-m-count">${totalCards}</span>
      <span class="fc-m-del" onclick="event.stopPropagation();fcDeleteMateria('${m.id}')" title="Apagar matéria">✕</span>`;
    tab.addEventListener('click', () => fcSelectMateria(m.id));
    row.appendChild(tab);
  });
}

// ── SELECT MATERIA ──
function fcSelectMateria(mid) {
  fcActiveMateria = mid;
  fcActiveTopico  = null;
  const m = fcGetMateria(mid);
  document.getElementById('fcAddTopicoBtn').style.display = m ? '' : 'none';
  document.getElementById('fcAddCardBtn').style.display   = 'none';
  document.getElementById('fcTopicosSection').style.display  = '';
  document.getElementById('fcCardsSection').style.display    = 'none';
  document.getElementById('fcBreadcrumb').style.display      = 'flex';
  fcRenderMateriaTabs();
  fcRenderTopicos();
  fcRenderBreadcrumb();
  // Show topico label in add panel
  const lbl = document.getElementById('fcTopicoMateriaLabel');
  if (lbl && m) lbl.textContent = m.name;
}

// ── RENDER TOPICOS ──
function fcRenderTopicos() {
  const list = document.getElementById('fcTopicosList');
  if (!list) return;
  const m = fcGetMateria(fcActiveMateria);
  if (!m) { list.innerHTML=''; return; }

  if (m.topicos.length === 0) {
    list.innerHTML = `<div class="fc-empty"><div class="fc-empty-icon">📌</div><div class="fc-empty-msg">Nenhum tópico ainda. Crie o primeiro!</div></div>`;
    return;
  }

  list.innerHTML = m.topicos.map(t => {
    const due = t.cards.filter(c=>!c.due||new Date(c.due)<=new Date()).length;
    return `
    <div class="fc-topico-card${t.id===fcActiveTopico?' active':''}" onclick="fcSelectTopico('${t.id}')">
      <div class="fc-topico-left">
        <span class="fc-topico-icon">📌</span>
        <div>
          <div class="fc-topico-name">${t.name}</div>
          <div class="fc-topico-count">${t.cards.length} card${t.cards.length!==1?'s':''} ${due>0?`<span style="color:var(--hard)">· ${due} para revisar</span>`:''}
          </div>
        </div>
      </div>
      <div class="fc-topico-actions">
        <button class="fc-topico-del" onclick="event.stopPropagation();fcDeleteTopico('${m.id}','${t.id}')">✕ Apagar</button>
      </div>
    </div>`;
  }).join('');
}

// ── SELECT TOPICO ──
function fcSelectTopico(tid) {
  fcActiveTopico = tid;
  document.getElementById('fcAddCardBtn').style.display = '';
  document.getElementById('fcCardsSection').style.display = '';
  const t = fcGetTopico(fcActiveMateria, tid);
  const title = document.getElementById('fcCardsTitle');
  if (title && t) title.textContent = `${t.name} — ${t.cards.length} card${t.cards.length!==1?'s':''}`;
  fcRenderTopicos();
  fcRenderCards();
  fcRenderBreadcrumb();
  // update breadcrumb in card panel
  const bc = document.getElementById('fcCardBreadcrumb');
  const m  = fcGetMateria(fcActiveMateria);
  if (bc && m && t) bc.textContent = m.name + ' › ' + t.name;
}

// ── RENDER CARDS (only when tópico is explicitly selected) ──
function fcRenderCards() {
  const grid = document.getElementById('fcGrid');
  if (!grid) return;

  // If no tópico selected, hide the cards section completely
  if (!fcActiveTopico) {
    document.getElementById('fcCardsSection').style.display = 'none';
    grid.innerHTML = '';
    return;
  }

  const t = fcGetTopico(fcActiveMateria, fcActiveTopico);
  if (!t) { grid.innerHTML=''; return; }

  const q = (document.getElementById('fcSearch')?.value||'').toLowerCase();
  let cards = t.cards;
  if (q) cards = cards.filter(c=>c.q.toLowerCase().includes(q)||c.a.toLowerCase().includes(q));

  document.getElementById('fcCardsSection').style.display = '';

  if (cards.length===0) {
    grid.innerHTML=`<div class="fc-empty" style="grid-column:1/-1"><div class="fc-empty-icon">🃏</div><div class="fc-empty-msg">Nenhum card ainda. Clique em <strong>＋ Novo Card</strong> para criar o primeiro.</div></div>`;
    const title = document.getElementById('fcCardsTitle');
    if (title) title.textContent = t.name + ' — 0 cards';
    return;
  }

  const today = new Date();
  grid.innerHTML = cards.map(c => {
    const due = !c.due || new Date(c.due) <= today;
    const nextStr = c.reps===0 ? 'Novo' : (due?'Revisar hoje':'Em '+c.interval+'d');
    const nc = c.reps===0?'var(--accent)':(due?'var(--hard)':'var(--easy)');
    const efColor = c.ef>=2.5?'var(--easy)':c.ef>=1.8?'var(--accent4)':'var(--hard)';
    return `
    <div class="fc-item">
      <button class="fc-item-del" onclick="fcDeleteCard('${c.id}')" title="Apagar card">✕</button>
      <div class="fc-item-q">${c.q}</div>
      <div class="fc-item-a">${c.a}</div>
      <div class="fc-item-meta">
        <span class="fc-item-ef" style="color:${efColor}">EF ${c.ef} · ${c.reps} rep${c.reps!==1?'s':''}</span>
        <span class="fc-item-next" style="background:${nc}18;border:1px solid ${nc}44;color:${nc}">${nextStr}</span>
      </div>
    </div>`;
  }).join('');

  const title = document.getElementById('fcCardsTitle');
  if (title) title.textContent = t.name + ' — ' + t.cards.length + ' card' + (t.cards.length!==1?'s':'');
}

// ── DUE TODAY SUMMARY (shown at top of flashcard section) ──
function fcRenderDueSummary() {
  let summaryEl = document.getElementById('fcDueSummary');
  if (!summaryEl) return;
  const allCards = fcAllCards();
  const today = new Date();
  const due = allCards.filter(c => !c.due || new Date(c.due) <= today);
  if (due.length === 0) {
    summaryEl.style.display = 'none';
    return;
  }
  // Group by matéria
  const byMateria = {};
  due.forEach(c => {
    if (!byMateria[c.materia]) byMateria[c.materia] = 0;
    byMateria[c.materia]++;
  });
  summaryEl.style.display = 'flex';
  summaryEl.innerHTML = `
    <span style="font-size:12px;color:var(--hard);font-weight:700">🔔 ${due.length} card${due.length!==1?'s':''} para revisar hoje:</span>
    ${Object.entries(byMateria).map(([m,n])=>`<span style="font-size:11px;background:rgba(252,92,125,.1);border:1px solid rgba(252,92,125,.25);color:var(--hard);border-radius:100px;padding:3px 10px">${m}: ${n}</span>`).join('')}
    <button class="fc-btn fc-btn-primary" onclick="fcStartStudy()" style="margin-left:auto;padding:6px 14px;font-size:11px">▶ Revisar agora</button>
  `;
}

// ── BREADCRUMB ──
function fcRenderBreadcrumb() {
  const bc = document.getElementById('fcBreadcrumb');
  if (!bc) return;
  const m = fcGetMateria(fcActiveMateria);
  const t = fcGetTopico(fcActiveMateria, fcActiveTopico);
  let html = `<span class="fc-breadcrumb-item" onclick="fcClearNav()">Flashcards</span>`;
  if (m) html += `<span class="fc-breadcrumb-sep"> › </span><span class="${t?'fc-breadcrumb-item':'fc-breadcrumb-current'}" onclick="fcSelectMateria('${m.id}')">${m.name}</span>`;
  if (t) html += `<span class="fc-breadcrumb-sep"> › </span><span class="fc-breadcrumb-current">${t.name}</span>`;
  bc.innerHTML = html;
}

function fcClearNav() {
  fcActiveMateria = null; fcActiveTopico = null;
  document.getElementById('fcTopicosSection').style.display='none';
  document.getElementById('fcCardsSection').style.display='none';
  document.getElementById('fcBreadcrumb').style.display='none';
  document.getElementById('fcAddTopicoBtn').style.display='none';
  document.getElementById('fcAddCardBtn').style.display='none';
  fcRenderMateriaTabs();
}

// ── ADD PANELS ──
function fcOpenAddMateria() {
  ['fcAddMateriaPanel','fcAddTopicoPanel','fcAddCardPanel'].forEach(id=>document.getElementById(id)?.classList.remove('active'));
  document.getElementById('fcAddMateriaPanel').classList.add('active');
  setTimeout(()=>document.getElementById('fcNewMateriaName')?.focus(),50);
}
function fcOpenAddTopico() {
  if (!fcActiveMateria) { showToast('Selecione uma matéria primeiro','warn'); return; }
  ['fcAddMateriaPanel','fcAddTopicoPanel','fcAddCardPanel'].forEach(id=>document.getElementById(id)?.classList.remove('active'));
  document.getElementById('fcAddTopicoPanel').classList.add('active');
  setTimeout(()=>document.getElementById('fcNewTopicoName')?.focus(),50);
}
function fcOpenAddCard() {
  if (!fcActiveTopico) { showToast('Selecione um tópico primeiro','warn'); return; }
  ['fcAddMateriaPanel','fcAddTopicoPanel','fcAddCardPanel'].forEach(id=>document.getElementById(id)?.classList.remove('active'));
  document.getElementById('fcAddCardPanel').classList.add('active');
  setTimeout(()=>document.getElementById('fcQ')?.focus(),50);
}

// ── SAVE ──
function fcSaveMateria() {
  const name = document.getElementById('fcNewMateriaName')?.value.trim();
  if (!name) return;
  const id = fcNewId();
  const color = FC_COLORS[fcData.materias.length % FC_COLORS.length];
  fcData.materias.push({ id, name, color, topicos:[] });
  fcSaveData();
  document.getElementById('fcNewMateriaName').value='';
  document.getElementById('fcAddMateriaPanel').classList.remove('active');
  fcRenderMateriaTabs();
  fcSelectMateria(id);
  showToast('✓ Matéria criada!','ok');
}

function fcSaveTopico() {
  const name = document.getElementById('fcNewTopicoName')?.value.trim();
  if (!name || !fcActiveMateria) return;
  const m = fcGetMateria(fcActiveMateria);
  if (!m) return;
  const id = fcNewId();
  m.topicos.push({ id, name, cards:[] });
  fcSaveData();
  document.getElementById('fcNewTopicoName').value='';
  document.getElementById('fcAddTopicoPanel').classList.remove('active');
  fcRenderTopicos();
  fcSelectTopico(id);
  showToast('✓ Tópico criado!','ok');
}

function fcSaveCard() {
  const q = document.getElementById('fcQ')?.value.trim();
  const a = document.getElementById('fcA')?.value.trim();
  if (!q||!a) { showToast('Preencha pergunta e resposta!','warn'); return; }
  const t = fcGetTopico(fcActiveMateria, fcActiveTopico);
  if (!t) return;
  t.cards.push({ id:fcNewId(), q, a, ef:2.5, interval:1, reps:0, due:new Date().toISOString(), lastGrade:null });
  fcSaveData();
  document.getElementById('fcQ').value='';
  document.getElementById('fcA').value='';
  document.getElementById('fcAddCardPanel').classList.remove('active');
  fcRenderCards();
  showToast('✓ Card salvo!','ok');
}

// ── DELETE ──
function fcDeleteMateria(mid) {
  if (!confirm('Apagar esta matéria e TODOS os seus tópicos e cards?')) return;
  fcData.materias = fcData.materias.filter(m=>m.id!==mid);
  if (fcActiveMateria===mid) fcActiveMateria=null, fcActiveTopico=null;
  fcSaveData();
  fcClearNav();
  showToast('Matéria apagada','warn');
}
function fcDeleteTopico(mid,tid) {
  if (!confirm('Apagar este tópico e todos os seus cards?')) return;
  const m = fcGetMateria(mid);
  if (m) m.topicos = m.topicos.filter(t=>t.id!==tid);
  if (fcActiveTopico===tid) { fcActiveTopico=null; document.getElementById('fcCardsSection').style.display='none'; }
  fcSaveData();
  fcRenderTopicos();
  showToast('Tópico apagado','warn');
}
function fcDeleteCard(cid) {
  if (!confirm('Apagar este card?')) return;
  const t = fcGetTopico(fcActiveMateria, fcActiveTopico);
  if (t) t.cards = t.cards.filter(c=>c.id!==cid);
  fcSaveData();
  fcRenderCards();
}

// ── SM-2 ──
function fcSM2(card, grade) {
  let {ef,interval,reps} = card;
  if (grade<3) { reps=0; interval=1; ef=Math.max(1.3,ef-0.20); }
  else {
    if(reps===0) interval=1;
    else if(reps===1) interval=6;
    else if(grade===3) interval=Math.ceil(interval*1.2);
    else if(grade===4) interval=Math.ceil(interval*ef);
    else               interval=Math.ceil(interval*ef*1.3);
    if(grade===3) ef=Math.max(1.3,ef-0.15);
    else if(grade===5) ef=Math.min(ef+0.10,4.0);
    reps+=1;
  }
  const due=new Date(); due.setDate(due.getDate()+Math.round(interval));
  return { ef:parseFloat(ef.toFixed(2)), interval, reps, due:due.toISOString() };
}

// ── STUDY SESSION ──
function fcStartStudy() {
  let pool;
  if (fcActiveTopico) {
    const t = fcGetTopico(fcActiveMateria, fcActiveTopico);
    pool = t ? t.cards.map(c=>({...c,mid:fcActiveMateria,tid:fcActiveTopico})) : [];
  } else if (fcActiveMateria) {
    pool = [];
    const m = fcGetMateria(fcActiveMateria);
    if (m) m.topicos.forEach(t=>t.cards.forEach(c=>pool.push({...c,mid:m.id,tid:t.id})));
  } else {
    pool = fcAllCards();
  }
  pool = pool.sort((a,b)=>{
    const ad=!a.due||new Date(a.due)<=new Date()?0:1;
    const bd=!b.due||new Date(b.due)<=new Date()?0:1;
    return ad-bd||a.interval-b.interval;
  });
  if (!pool.length){ showToast('Nenhum card para estudar!','warn'); return; }
  fcStudyQueue = pool;
  fcStudyIndex = 0;
  fcSession    = {again:0,hardGood:0,easy:0};
  document.getElementById('fcStats').style.display='grid';
  document.getElementById('fcStudyArea').classList.add('active');
  document.getElementById('fcDoneScreen').classList.remove('active');
  document.getElementById('fcTopicosSection').style.display='none';
  document.getElementById('fcCardsSection').style.display='none';
  document.getElementById('fcStudyBtn').style.display='none';
  fcUpdateStats();
  fcLoadCard();
}

function endStudy() {
  document.getElementById('fcStudyArea').classList.remove('active');
  document.getElementById('fcDoneScreen').classList.remove('active');
  document.getElementById('fcStats').style.display='none';
  document.getElementById('fcStudyBtn').style.display='';
  if (fcActiveMateria) document.getElementById('fcTopicosSection').style.display='';
  if (fcActiveTopico)  document.getElementById('fcCardsSection').style.display='';
  fcRenderCards();
}

function fcLoadCard() {
  if (fcStudyIndex>=fcStudyQueue.length){ fcShowDone(); return; }
  fcFlipped=false;
  document.getElementById('fcCardInner').classList.remove('flipped');
  document.getElementById('fcRatingRow').classList.remove('visible');
  const c = fcStudyQueue[fcStudyIndex];
  document.getElementById('fcCardQ').textContent = c.q;
  document.getElementById('fcCardA').textContent = c.a;
  document.getElementById('fcCardDeckBadge').textContent = c.materia||'';
  document.getElementById('intAgain').textContent = '1d';
  document.getElementById('intHard').textContent  = Math.ceil((c.interval||1)*1.2)+'d';
  document.getElementById('intGood').textContent  = Math.ceil((c.interval||1)*(c.ef||2.5))+'d';
  document.getElementById('intEasy').textContent  = Math.ceil((c.interval||1)*(c.ef||2.5)*1.3)+'d';
  const total=fcStudyQueue.length, done=fcStudyIndex;
  const pct=Math.round((done/total)*100);
  document.getElementById('fcProgBar').style.width=pct+'%';
  document.getElementById('fcProgLabel').textContent=`Card ${done+1} de ${total}`;
  document.getElementById('fcProgPct').textContent=pct+'%';
}

function flipCard() {
  if(fcFlipped) return;
  fcFlipped=true;
  document.getElementById('fcCardInner').classList.add('flipped');
  setTimeout(()=>document.getElementById('fcRatingRow').classList.add('visible'),300);
}

function rateCard(grade) {
  const src = fcStudyQueue[fcStudyIndex];
  if (!src) return;
  // Update card in fcData
  const t = fcGetTopico(src.mid, src.tid);
  if (t) {
    const card = t.cards.find(c=>c.id===src.id);
    if (card) Object.assign(card, fcSM2(card,grade), {lastGrade:grade});
  }
  fcSaveData();
  if(grade===0) fcSession.again++;
  else if(grade===5) fcSession.easy++;
  else fcSession.hardGood++;
  fcStudyIndex++;
  fcUpdateStats();
  const inner=document.getElementById('fcCardInner');
  inner.style.transition='transform .2s,opacity .2s';
  inner.style.transform='scale(.96)'; inner.style.opacity='0';
  setTimeout(()=>{ inner.style.transition=''; inner.style.transform=''; inner.style.opacity=''; fcLoadCard(); },200);
}

function fcUpdateStats() {
  document.getElementById('statRem').textContent    = fcStudyQueue.length-fcStudyIndex;
  document.getElementById('statAgain').textContent  = fcSession.again;
  document.getElementById('statHardGood').textContent = fcSession.hardGood;
  document.getElementById('statEasy').textContent   = fcSession.easy;
}

function fcShowDone() {
  document.getElementById('fcStudyArea').classList.remove('active');
  document.getElementById('fcDoneScreen').classList.add('active');
  document.getElementById('doneTotal').textContent = fcStudyQueue.length;
  document.getElementById('doneAgain').textContent = fcSession.again;
  document.getElementById('doneGood').textContent  = fcSession.hardGood;
  document.getElementById('doneEasy').textContent  = fcSession.easy;
}

// Legacy aliases so old html buttons still work
function startStudy() { fcStartStudy(); }
function showDone()   { fcShowDone();   }

// Keyboard shortcuts
document.addEventListener('keydown', e => {
  if(!document.getElementById('fcStudyArea').classList.contains('active')) return;
  if(e.code==='Space'){ e.preventDefault(); flipCard(); return; }
  if(!fcFlipped) return;
  if(e.key==='1') rateCard(0);
  if(e.key==='2') rateCard(3);
  if(e.key==='3') rateCard(4);
  if(e.key==='4') rateCard(5);
});

// Init
fcRenderMateriaTabs();
fcRenderDueSummary();


// ══════════════════════════════════════════════════
// POMODORO TIMER
// ══════════════════════════════════════════════════

const CIRCUMFERENCE = 2 * Math.PI * 108; // r=108 → ~678.58

let timerState = {
  running: false,
  phase: 'study',
  secondsLeft: 0,
  totalSeconds: 0,
  blocksDone: 0,
  sessionLog: [],
  interval: null,
  blocksBeforeLong: 4,
};

function timerGetStudy()  { return (parseInt(document.getElementById('pomoStudy')?.value)  || 50) * 60; }
function timerGetBreak()  { return (parseInt(document.getElementById('pomoBreak')?.value)  || 10) * 60; }
function timerGetLong()   { return (parseInt(document.getElementById('pomoLong')?.value)   || 30) * 60; }

function timerInit() {
  timerState.phase = 'study';
  timerState.secondsLeft = timerGetStudy();
  timerState.totalSeconds = timerGetStudy();
  timerState.blocksDone = 0;
  timerState.sessionLog = [];
  timerRenderUI();
  timerRenderPhaseDots();
  timerRenderSessionRow();
}

function timerToggle() {
  timerState.running ? timerPause() : timerStart();
}

function timerStart() {
  if (timerState.secondsLeft <= 0) timerInit();
  timerState.running = true;
  const btn = document.getElementById('timerStartBtn');
  if(btn){ btn.innerHTML = '⏸ Pausar'; btn.classList.add('running'); }
  timerState.interval = setInterval(timerTick, 1000);
}

function timerPause() {
  timerState.running = false;
  clearInterval(timerState.interval);
  const btn = document.getElementById('timerStartBtn');
  if(btn){ btn.innerHTML = '▶ Continuar'; btn.classList.remove('running'); }
}

function timerReset() {
  timerPause();
  timerInit();
  const btn = document.getElementById('timerStartBtn');
  if(btn){ btn.innerHTML = '▶ Iniciar'; }
}

function timerSkip() {
  timerPause();
  timerState.sessionLog.push(timerState.phase);
  timerAdvancePhase();
}

function timerTick() {
  timerState.secondsLeft--;
  timerRenderUI();
  timerRenderSessionRow();
  if (timerState.secondsLeft <= 0) timerPhaseComplete();
}

function timerPhaseComplete() {
  clearInterval(timerState.interval);
  timerState.running = false;
  playSound(timerState.phase === 'study' ? 'break' : 'study');
  showTimerNotification(timerState.phase);
  timerState.sessionLog.push(timerState.phase);
  timerAdvancePhase();
  setTimeout(() => timerStart(), 1800);
}

function timerAdvancePhase() {
  if (timerState.phase === 'study') {
    timerState.blocksDone++;
    const isLong = timerState.blocksDone % timerState.blocksBeforeLong === 0;
    timerState.phase = isLong ? 'longbreak' : 'break';
    timerState.totalSeconds = isLong ? timerGetLong() : timerGetBreak();
  } else {
    timerState.phase = 'study';
    timerState.totalSeconds = timerGetStudy();
  }
  timerState.secondsLeft = timerState.totalSeconds;
  timerRenderUI();
  timerRenderPhaseDots();
  timerRenderSessionRow();
}

function timerRenderUI() {
  const { phase, secondsLeft, totalSeconds, blocksDone } = timerState;
  const m = String(Math.floor(secondsLeft / 60)).padStart(2,'0');
  const s = String(secondsLeft % 60).padStart(2,'0');
  const disp = document.getElementById('timerDisplay');
  if(disp) disp.textContent = `${m}:${s}`;

  const colors = {
    study:     { stroke:'#7c5cfc', glow:'rgba(124,92,252,.35)', label:'FOCO 📚',        labelColor:'#b8a0ff' },
    break:     { stroke:'#5cf0b0', glow:'rgba(92,240,176,.25)', label:'PAUSA ☕',        labelColor:'#5cf0b0' },
    longbreak: { stroke:'#fcb85c', glow:'rgba(252,184,92,.25)', label:'PAUSA LONGA 🛋', labelColor:'#fcb85c' },
  };
  const c = colors[phase] || colors.study;

  const ring = document.getElementById('timerRing');
  if(ring){
    const pct = totalSeconds > 0 ? secondsLeft / totalSeconds : 0;
    ring.style.strokeDashoffset = CIRCUMFERENCE * (1 - pct);
    ring.style.stroke = c.stroke;
    ring.style.filter = `drop-shadow(0 0 6px ${c.glow})`;
  }

  const lbl = document.getElementById('timerPhaseLabel');
  if(lbl){ lbl.textContent = c.label; lbl.style.color = c.labelColor; }

  const disp2 = document.getElementById('timerDisplay');
  if(disp2) disp2.style.color = secondsLeft <= 10 && timerState.running ? c.stroke : 'var(--text)';

  const bc = document.getElementById('timerBlockCount');
  if(bc){
    const studyBlock = phase === 'study' ? blocksDone + 1 : blocksDone;
    bc.textContent = phase === 'study'
      ? `Bloco ${studyBlock} de estudo`
      : phase === 'longbreak' ? `Pausa longa — ${blocksDone} blocos concluídos`
      : `Pausa ${blocksDone % timerState.blocksBeforeLong || timerState.blocksBeforeLong}`;
  }
}

function timerRenderPhaseDots() {
  const wrap = document.getElementById('timerPhaseDots');
  if(!wrap) return;
  wrap.innerHTML = '';
  for(let i = 0; i < timerState.blocksBeforeLong; i++){
    const dot = document.createElement('div');
    dot.className = 'phase-dot';
    const completedInCycle = timerState.blocksDone % timerState.blocksBeforeLong;
    if(i < completedInCycle || (timerState.blocksDone > 0 && completedInCycle === 0)) dot.classList.add('done');
    else if(i === completedInCycle && timerState.phase === 'study') dot.classList.add('study');
    else if(i === completedInCycle) dot.classList.add('break');
    wrap.appendChild(dot);
  }
}

function timerRenderSessionRow() {
  const row = document.getElementById('timerSessionRow');
  if(!row) return;
  const icons = { study:'📚', break:'☕', longbreak:'🛋' };
  const cls   = { study:'study-done', break:'break-done', longbreak:'long-done' };
  let html = timerState.sessionLog.map(p =>
    `<div class="session-block ${cls[p]}" title="${p}">${icons[p]}</div>`
  ).join('');
  if(timerState.running){
    const cc = timerState.phase === 'study' ? 'var(--accent)' : timerState.phase === 'longbreak' ? 'var(--accent4)' : 'var(--accent3)';
    html += `<div class="session-block active" style="color:${cc}">${icons[timerState.phase]}</div>`;
  }
  row.innerHTML = html;
}

// ── WEB AUDIO SOUND ENGINE ──
let audioCtx = null;
function getAudioCtx() {
  if(!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  return audioCtx;
}
function playTone(freq, dur, type='sine', vol=0.35, delay=0) {
  if(!document.getElementById('soundToggle')?.checked) return;
  try {
    const ctx = getAudioCtx();
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();
    osc.connect(gain); gain.connect(ctx.destination);
    osc.type = type;
    osc.frequency.setValueAtTime(freq, ctx.currentTime + delay);
    gain.gain.setValueAtTime(vol, ctx.currentTime + delay);
    gain.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + delay + dur);
    osc.start(ctx.currentTime + delay);
    osc.stop(ctx.currentTime + delay + dur + 0.05);
  } catch(e){}
}
function playSound(type) {
  if(type === 'break') {
    // Study done → 3 ascending celebratory tones
    playTone(523.25, 0.18, 'sine', 0.4, 0.00);  // C5
    playTone(659.25, 0.18, 'sine', 0.4, 0.22);  // E5
    playTone(783.99, 0.40, 'sine', 0.4, 0.44);  // G5
  } else {
    // Break done → 2 calm "back to work" tones
    playTone(392.00, 0.25, 'sine', 0.3, 0.00);  // G4
    playTone(523.25, 0.45, 'sine', 0.3, 0.30);  // C5
  }
}

function showTimerNotification(completedPhase) {
  const msgs = {
    study:     '⏸ Bloco concluído! Hora da pausa ☕',
    break:     '▶ Pausa encerrada! Hora de focar 📚',
    longbreak: '▶ Pausa longa encerrada! Novo ciclo 🚀',
  };
  // Flash panel border
  const panel = document.getElementById('panel-hours');
  if(panel){
    const flashColor = completedPhase === 'study' ? 'var(--accent3)' : 'var(--accent)';
    const flashGlow  = completedPhase === 'study' ? 'rgba(92,240,176,.2)' : 'rgba(124,92,252,.2)';
    panel.style.borderColor = flashColor;
    panel.style.boxShadow   = `0 0 32px ${flashGlow}`;
    setTimeout(()=>{ panel.style.borderColor=''; panel.style.boxShadow=''; }, 2500);
  }
  const orig = document.title;
  document.title = msgs[completedPhase] || orig;
  setTimeout(()=>{ document.title = orig; }, 5000);
}

function updateSoundLabel() {
  const on  = document.getElementById('soundToggle')?.checked;
  const lbl = document.getElementById('soundLabel');
  if(lbl){ lbl.textContent = on ? 'Ativado' : 'Desativado'; lbl.style.color = on ? 'var(--easy)' : 'var(--text-dim)'; }
}

// Init timer after DOM is ready
setTimeout(() => {
  timerInit();
  document.getElementById('soundToggle')?.addEventListener('change', updateSoundLabel);
  ['pomoStudy','pomoBreak','pomoLong'].forEach(id => {
    document.getElementById(id)?.addEventListener('change', () => {
      if(!timerState.running) timerInit();
    });
  });
}, 200);

// ══════════════════════════════════════════════════
// AUTO DATE SYSTEM
// ══════════════════════════════════════════════════

function refreshDayLabels() {
  const weekVal = document.getElementById('weekPicker')?.value;
  const dates   = getWeekDates(weekVal);
  const today   = new Date(); today.setHours(0,0,0,0);
  const DAY_SHORT = ['SEG','TER','QUA','QUI','SEX','SÁB','DOM'];

  dates.forEach((date, i) => {
    const isToday = date.getTime() === today.getTime();
    const dd = String(date.getDate()).padStart(2,'0');
    const mm = String(date.getMonth()+1).padStart(2,'0');

    const tab = document.querySelector(`.day-tab[data-day="${i}"]`);
    if (tab) {
      tab.innerHTML = `${DAY_SHORT[i]}<br><span style="font-size:9px;font-weight:400;opacity:.8">${dd}/${mm}</span>`;
      tab.style.position = 'relative';
      const existing = tab.querySelector('.today-dot');
      if (existing) existing.remove();
      if (isToday) {
        const dot = document.createElement('span');
        dot.className = 'today-dot';
        dot.style.cssText = `position:absolute;top:4px;right:4px;width:6px;height:6px;border-radius:50%;background:var(--easy);box-shadow:0 0 6px var(--easy)`;
        tab.appendChild(dot);
      }
    }

    const badge = document.getElementById(`dayDateBadge-${i}`);
    if (badge) {
      badge.textContent = date.toLocaleDateString('pt-BR', {weekday:'long',day:'2-digit',month:'long',year:'numeric'});
      badge.style.color = isToday ? 'var(--easy)' : 'var(--text-muted)';
    }
  });

  // Auto-activate today's tab if viewing current week
  const iso = getISOWeekFromDate(today);
  const currentWeekVal = `${iso.year}-W${String(iso.week).padStart(2,'0')}`;
  if (!weekVal || weekVal === currentWeekVal) {
    const todayIdx = today.getDay() === 0 ? 6 : today.getDay() - 1;
    document.querySelectorAll('.day-tab').forEach(t => t.classList.remove('active'));
    document.querySelectorAll('.day-panel').forEach(p => p.classList.remove('active'));
    const todayTab   = document.querySelector(`.day-tab[data-day="${todayIdx}"]`);
    const todayPanel = document.getElementById(`day-${todayIdx}`);
    if (todayTab)   todayTab.classList.add('active');
    if (todayPanel) todayPanel.classList.add('active');
  }
}

// Set weekPicker to today's ISO week
(function initWeek() {
  const now = new Date();
  const iso = getISOWeekFromDate(now);
  document.getElementById('weekPicker').value = `${iso.year}-W${String(iso.week).padStart(2,'0')}`;
  updateRevisions();
  setTimeout(refreshDayLabels, 80);
})();

document.getElementById('weekPicker').addEventListener('change', () => {
  updateRevisions();
  refreshDayLabels();
});

// Auto-refresh at midnight
(function scheduleMidnightRefresh() {
  const now  = new Date();
  const next = new Date(now.getFullYear(), now.getMonth(), now.getDate()+1, 0, 0, 5);
  setTimeout(() => { refreshDayLabels(); updateRevisions(); scheduleMidnightRefresh(); }, next - now);
})();

// ══════════════════════════════════════════════════
// FLOATING POMODORO TIMER
// ══════════════════════════════════════════════════
const PF = {
  running: false,
  phase: 'study',
  block: 1,
  totalBlocks: 4,
  secondsLeft: 0,
  totalSeconds: 0,
  timer: null,
  audioCtx: null,
};
const PF_CIRC = 2 * Math.PI * 60; // r=60 → 376.99

function pfGetStudy()  { return (parseInt(document.getElementById('pomoStudy')?.value)  || 50) * 60; }
function pfGetBreak()  { return (parseInt(document.getElementById('pomoBreak')?.value)  || 10) * 60; }
function pfGetLong()   { return (parseInt(document.getElementById('pomoLong')?.value)   || 30) * 60; }

function pfOpen()  {
  document.getElementById('pomodoroFab').classList.add('hidden');
  document.getElementById('pomodoroFloat').classList.remove('hidden');
  if (PF.secondsLeft === 0) pfReset();
}
function pfClose() {
  document.getElementById('pomodoroFloat').classList.add('hidden');
  document.getElementById('pomodoroFab').classList.remove('hidden');
}

function pfReset() {
  clearInterval(PF.timer);
  PF.running = false; PF.phase = 'study'; PF.block = 1;
  PF.secondsLeft = pfGetStudy(); PF.totalSeconds = PF.secondsLeft;
  pfRender();
}

function pfToggle() {
  if (PF.running) {
    clearInterval(PF.timer); PF.running = false;
    document.getElementById('pfStartBtn').textContent = '▶ Continuar';
  } else {
    if (PF.secondsLeft <= 0) pfReset();
    PF.running = true;
    document.getElementById('pfStartBtn').textContent = '⏸ Pausar';
    PF.timer = setInterval(pfTick, 1000);
  }
}

function pfTick() {
  PF.secondsLeft--;
  if (PF.secondsLeft <= 0) pfPhaseEnd();
  else pfRender();
}

function pfPhaseEnd() {
  clearInterval(PF.timer); PF.running = false;
  pfPlaySound(PF.phase === 'study' ? 'break' : 'study');
  pfFlashTitle(PF.phase);
  if (PF.phase === 'study') {
    if (PF.block >= PF.totalBlocks) {
      PF.phase = 'longbreak'; PF.secondsLeft = pfGetLong(); PF.totalSeconds = PF.secondsLeft;
    } else {
      PF.phase = 'break'; PF.secondsLeft = pfGetBreak(); PF.totalSeconds = PF.secondsLeft;
    }
  } else {
    if (PF.phase !== 'longbreak') PF.block = Math.min(PF.block + 1, PF.totalBlocks);
    else PF.block = 1;
    PF.phase = 'study'; PF.secondsLeft = pfGetStudy(); PF.totalSeconds = PF.secondsLeft;
  }
  pfRender();
  PF.running = true;
  document.getElementById('pfStartBtn').textContent = '⏸ Pausar';
  PF.timer = setInterval(pfTick, 1000);
}

function pfSkip() {
  clearInterval(PF.timer); PF.running = false; PF.secondsLeft = 0; pfPhaseEnd();
}

function pfRender() {
  const m = String(Math.floor(PF.secondsLeft/60)).padStart(2,'0');
  const s = String(PF.secondsLeft%60).padStart(2,'0');
  document.getElementById('pfTime').textContent = m+':'+s;

  const pct = PF.totalSeconds > 0 ? PF.secondsLeft / PF.totalSeconds : 1;
  document.getElementById('pfRing').style.strokeDashoffset = PF_CIRC * (1 - pct);

  const el = document.getElementById('pomodoroFloat');
  el.classList.remove('phase-break','phase-longbreak');
  let lbl = 'ESTUDO';
  if (PF.phase==='break')     { lbl='PAUSA';       el.classList.add('phase-break'); }
  if (PF.phase==='longbreak') { lbl='PAUSA LONGA'; el.classList.add('phase-longbreak'); }
  document.getElementById('pfPhaseLabel').textContent = lbl;
  document.getElementById('pfBlockLabel').textContent = `Bloco ${PF.block} de ${PF.totalBlocks}`;
  if (!PF.running) document.getElementById('pfStartBtn').textContent = PF.totalSeconds===PF.secondsLeft?'▶ Iniciar':'▶ Continuar';

  // Dots
  let dots = '';
  for(let i=1;i<=PF.totalBlocks;i++){
    const done = i<PF.block||(i===PF.block&&PF.phase!=='study');
    const active= i===PF.block&&PF.phase==='study'&&PF.running;
    dots+=`<div class="pf-dot${done?' done':''}${active?' active':''}"></div>`;
  }
  document.getElementById('pfDots').innerHTML = dots;

  // Tab title when running
  if (PF.running) document.title = `🍅 ${m}:${s} — StudyFlow`;
  else document.title = 'StudyFlow — Tabela de Estudos';
}

function pfFlashTitle(phase) {
  const msgs = {study:'⏸ Pausa! ☕',break:'▶ Hora de focar 📚',longbreak:'▶ Novo ciclo 🚀'};
  document.title = msgs[phase]||'StudyFlow';
  setTimeout(()=>{ document.title='StudyFlow — Tabela de Estudos'; },4000);
  // Flash the FAB if closed
  const fab = document.getElementById('pomodoroFab');
  if(!fab.classList.contains('hidden')){
    fab.style.boxShadow='0 0 0 0 rgba(124,92,252,.7)';
    let t=0; const iv=setInterval(()=>{
      t++; fab.style.transform=t%2?'scale(1.18)':'scale(1)';
      if(t>5){ clearInterval(iv); fab.style.transform=''; }
    },160);
  }
}

function pfPlaySound(type) {
  if(!document.getElementById('pfSound')?.checked) return;
  try {
    if(!PF.audioCtx) PF.audioCtx = new(window.AudioContext||window.webkitAudioContext)();
    const ctx = PF.audioCtx;
    // Study done → 3-note ascending chime (C5 E5 G5)
    // Break done → 2-note descending ping (G5 C5)
    const notes = type==='break'
      ? [{f:523,t:0},{f:659,t:.16},{f:784,t:.32},{f:1047,t:.52}]  // C5 E5 G5 C6 — ascending fanfare
      : [{f:784,t:0},{f:659,t:.18},{f:523,t:.36}];                 // G5 E5 C5 — descending calm
    notes.forEach(({f,t})=>{
      const o=ctx.createOscillator(), g=ctx.createGain(), comp=ctx.createDynamicsCompressor();
      o.connect(g); g.connect(comp); comp.connect(ctx.destination);
      o.type='triangle'; o.frequency.value=f;
      // Add slight vibrato
      const lfo=ctx.createOscillator(), lfoG=ctx.createGain();
      lfo.frequency.value=5; lfoG.gain.value=2;
      lfo.connect(lfoG); lfoG.connect(o.frequency);
      const now=ctx.currentTime+t;
      g.gain.setValueAtTime(0,now);
      g.gain.linearRampToValueAtTime(.28,now+.03);
      g.gain.exponentialRampToValueAtTime(.001,now+.75);
      o.start(now); o.stop(now+.8);
      lfo.start(now); lfo.stop(now+.8);
    });
  } catch(e){}
}

function pfSoundLabel() {
  const on=document.getElementById('pfSound')?.checked;
  const lbl=document.getElementById('pfSoundLabel');
  if(lbl){lbl.textContent=on?'Ativado':'Desativado';lbl.style.color=on?'var(--easy)':'var(--text-dim)';}
}

// Drag
(()=>{
  let drag=false,ox=0,oy=0;
  const hdr=()=>document.getElementById('pfHeader');
  const flt=()=>document.getElementById('pomodoroFloat');
  document.addEventListener('mousedown',e=>{
    if(!hdr()||!flt()) return;
    if(!hdr().contains(e.target)) return;
    drag=true; const r=flt().getBoundingClientRect(); ox=e.clientX-r.left; oy=e.clientY-r.top;
    flt().style.transition='none';
  });
  document.addEventListener('mousemove',e=>{
    if(!drag||!flt()) return;
    flt().style.right='auto'; flt().style.bottom='auto';
    flt().style.left=(e.clientX-ox)+'px'; flt().style.top=(e.clientY-oy)+'px';
  });
  document.addEventListener('mouseup',()=>{ drag=false; if(flt()) flt().style.transition=''; });
})();

// Init floating timer
setTimeout(()=>{ pfReset(); }, 300);

// ══════════════════════════════════════════════════
// SAVE DAY & HISTORY SYSTEM
// ══════════════════════════════════════════════════

let studyHistory = [];
let histFilterActive = 'all';

function loadHistory() {
  try {
    const raw = localStorage.getItem('sf_studyHistory');
    if (raw) studyHistory = JSON.parse(raw);
  } catch(e) { studyHistory = []; }
}

function persistHistory() {
  try { localStorage.setItem('sf_studyHistory', JSON.stringify(studyHistory)); } catch(e) {}
}

function collectDayData(dayIndex) {
  const data = {
    dayIndex, dayName: DAYS[dayIndex], color: COLORS[dayIndex],
    timestamp: new Date().toISOString(), subtitle: '', materias: [], metas: '', obs: ''
  };

  const sub = document.getElementById(`daySubtitle-${dayIndex}`);
  data.subtitle = sub ? sub.value.trim() : '';

  const dayPanel = document.getElementById(`day-${dayIndex}`);
  if (dayPanel) {
    const tas = dayPanel.querySelectorAll('.day-notes-grid textarea');
    data.metas = tas[0]?.value.trim() || '';
    data.obs   = tas[1]?.value.trim() || '';
  }

  for (let b = 0; b < 2; b++) {
    const blockId = `d${dayIndex}b${b}`;
    const subjEl  = document.getElementById(`subj-${blockId}`);
    if (!subjEl || !subjEl.value.trim()) continue;

    const blockEl = document.getElementById(`block-${blockId}`);
    const materia = { name: subjEl.value.trim(), color: COLORS[b===0 ? dayIndex : (dayIndex+3)%7] };

    const timeEl = blockEl?.querySelector('input[type="time"]');
    materia.horario = timeEl?.value || '';

    const diffSel = blockEl?.querySelector('.diff-btn.sel');
    materia.dificuldade = diffSel ? diffSel.dataset.diff : '';

    const methSels = blockEl?.querySelectorAll('.method-btn.sel');
    materia.metodos = methSels ? [...methSels].map(m => m.dataset.method) : [];

    const notesTA = blockEl?.querySelector('.block-body textarea.field-input');
    materia.notas = notesTA?.value.trim() || '';

    const perfEl = blockEl?.querySelector('.field-input[type="number"]');
    materia.desempenho = perfEl?.value || '';

    data.materias.push(materia);
  }
  return data;
}

function resetDayFields(dayIndex) {
  const sub = document.getElementById(`daySubtitle-${dayIndex}`);
  if (sub) sub.value = '';

  const dayPanel = document.getElementById(`day-${dayIndex}`);
  if (dayPanel) {
    dayPanel.querySelectorAll('.day-notes-grid textarea').forEach(t => t.value = '');
  }

  for (let b = 0; b < 2; b++) {
    const blockId = `d${dayIndex}b${b}`;
    const blockEl = document.getElementById(`block-${blockId}`);
    if (!blockEl) continue;

    const subjEl = document.getElementById(`subj-${blockId}`);
    if (subjEl) subjEl.value = '';

    const timeEl = blockEl.querySelector('input[type="time"]');
    if (timeEl) timeEl.value = '07:00';
    const timeDisp = document.getElementById(`timeDisp-${blockId}`);
    if (timeDisp) timeDisp.textContent = '07:00';

    blockEl.querySelectorAll('.diff-btn').forEach(b => b.classList.remove('sel'));
    blockEl.querySelectorAll('.method-btn').forEach(b => b.classList.remove('sel'));
    blockEl.querySelectorAll('.block-body textarea').forEach(t => t.value = '');

    const perfEl = blockEl.querySelector('.field-input[type="number"]');
    if (perfEl) perfEl.value = '';

    const chk = document.getElementById(`check-${blockId}`);
    if (chk) chk.classList.remove('done');

    const chips = document.getElementById(`revChips-${blockId}`);
    if (chips) chips.innerHTML = `<span class="rev-chip">Preencha a matéria e avalie na tabela abaixo</span>`;
  }

  updateRevisions();
}

function saveDay(dayIndex) {
  const data = collectDayData(dayIndex);
  if (data.materias.length === 0 && !data.subtitle && !data.metas) {
    showToast('⚠️ Nenhum dado para salvar. Preencha ao menos uma matéria.', 'warn');
    return;
  }

  // Use correct calendar date for this weekday (no UTC drift)
  const weekVal   = document.getElementById('weekPicker')?.value;
  const weekDates = getWeekDates(weekVal);
  const studyDate = weekDates[dayIndex];

  for (let b = 0; b < 2; b++) {
    const blockId = `d${dayIndex}b${b}`;
    const subjEl  = document.getElementById(`subj-${blockId}`);
    if (!subjEl || !subjEl.value.trim()) continue;
    const name = subjEl.value.trim();
    // Only add if not already tracked
    const existing = ankiEntries.find(e => e.id === blockId);
    if (!existing) {
      ankiEntries.push({ id: 'saved_' + blockId + '_' + Date.now(), blockId: null, name, ef: 2.5, interval: 1, reps: 0, lastGrade: null,
        studyDate: `${studyDate.getFullYear()}-${String(studyDate.getMonth()+1).padStart(2,'0')}-${String(studyDate.getDate()).padStart(2,'0')}` });
    }
  }
  saveAnkiEntries();
  renderAnkiTable();

  data.id = 'hist_' + Date.now();
  studyHistory.unshift(data);
  persistHistory();
  resetDayFields(dayIndex);
  renderHistoryAll();
  showToast(`✓ ${data.dayName.split('-')[0]} salvo no histórico!`, 'ok');
}

function editHistoryItem(id) {
  const idx = studyHistory.findIndex(h => h.id === id);
  if (idx < 0) return;
  const data = studyHistory[idx];
  const di = data.dayIndex;

  // Switch to that day tab
  document.querySelectorAll('.day-tab').forEach(t => t.classList.remove('active'));
  document.querySelectorAll('.day-panel').forEach(p => p.classList.remove('active'));
  const tab = document.querySelector(`.day-tab[data-day="${di}"]`);
  if (tab) tab.classList.add('active');
  const panel = document.getElementById(`day-${di}`);
  if (panel) { panel.classList.add('active'); setTimeout(()=>panel.scrollIntoView({behavior:'smooth',block:'start'}),80); }

  const sub = document.getElementById(`daySubtitle-${di}`);
  if (sub) sub.value = data.subtitle || '';

  const dayPanel = document.getElementById(`day-${di}`);
  if (dayPanel) {
    const tas = dayPanel.querySelectorAll('.day-notes-grid textarea');
    if (tas[0]) tas[0].value = data.metas || '';
    if (tas[1]) tas[1].value = data.obs   || '';
  }

  data.materias.forEach((mat, b) => {
    const blockId = `d${di}b${b}`;
    const blockEl = document.getElementById(`block-${blockId}`);
    if (!blockEl) return;

    const subjEl = document.getElementById(`subj-${blockId}`);
    if (subjEl) subjEl.value = mat.name;

    const timeEl = blockEl.querySelector('input[type="time"]');
    if (timeEl && mat.horario) timeEl.value = mat.horario;
    const timeDisp = document.getElementById(`timeDisp-${blockId}`);
    if (timeDisp && mat.horario) timeDisp.textContent = mat.horario;

    blockEl.querySelectorAll('.diff-btn').forEach(btn => {
      btn.classList.toggle('sel', btn.dataset.diff === mat.dificuldade);
    });
    blockEl.querySelectorAll('.method-btn').forEach(btn => {
      btn.classList.toggle('sel', (mat.metodos||[]).includes(btn.dataset.method));
    });

    const noteTA = blockEl.querySelector('.block-body textarea.field-input');
    if (noteTA) noteTA.value = mat.notas || '';

    const perfEl = blockEl.querySelector('.field-input[type="number"]');
    if (perfEl) perfEl.value = mat.desempenho || '';
  });

  studyHistory.splice(idx, 1);
  persistHistory();
  renderHistoryAll();
  updateRevisions();
  showToast(`✏️ ${data.dayName.split('-')[0]} restaurado para edição.`, 'ok');
}

function deleteHistoryItem(id) {
  if (!confirm('Remover este registro do histórico?')) return;
  studyHistory = studyHistory.filter(h => h.id !== id);
  persistHistory();
  renderHistoryAll();
}

function clearAllHistory() {
  if (!confirm('Apagar TODO o histórico? Esta ação não pode ser desfeita.')) return;
  studyHistory = [];
  persistHistory();
  renderHistoryAll();
}

function setHistFilter(btn) {
  document.querySelectorAll('.hist-filter-btn').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
  histFilterActive = btn.dataset.filter;
  renderHistoryAll();
}

// ══════════════════════════════════════════════════
// HISTÓRICO — VIEW MANAGER
// ══════════════════════════════════════════════════
let histViewMode = 'tree'; // 'tree' | 'cards'

function setHistView(mode, btn) {
  histViewMode = mode;
  document.querySelectorAll('.hvt-btn').forEach(b => b.classList.remove('active'));
  if (btn) btn.classList.add('active');
  document.getElementById('histViewTree').style.display  = mode === 'tree'  ? '' : 'none';
  document.getElementById('histViewCards').style.display = mode === 'cards' ? '' : 'none';
  if (mode === 'tree')  renderHistTree();
  if (mode === 'cards') renderHistory();
}

// ── TREE VIEW ──────────────────────────────────────
function renderHistTree() {
  const treeEl   = document.getElementById('histTree');
  const summaryEl= document.getElementById('histSummaryBar');
  if (!treeEl) return;

  // Build tree data from studyHistory + fcData
  // Node structure: materia -> topico -> subtopico -> registros[]
  const tree = buildHistoryTree();

  // Summary bar
  if (summaryEl) {
    const totalMat  = Object.keys(tree).length;
    const totalSess = studyHistory.length;
    const totalCards= typeof fcData !== 'undefined'
      ? fcData.materias.reduce((s,m)=>s+m.topicos.reduce((ss,t)=>ss+t.cards.length,0),0) : 0;
    const concluidos = Object.values(tree).filter(m=>m.done).length;
    summaryEl.innerHTML = `
      <div class="hsb-item"><span class="hsb-num">${totalMat}</span> matérias</div>
      <div class="hsb-item"><span class="hsb-num">${totalSess}</span> sessões salvas</div>
      <div class="hsb-item"><span class="hsb-num">${totalCards}</span> flashcards</div>
      <div class="hsb-item"><span class="hsb-num" style="color:var(--green)">${concluidos}</span> concluídas</div>
    `;
  }

  if (Object.keys(tree).length === 0) {
    treeEl.innerHTML = `<div class="hist-tree-empty">
      <div class="hist-tree-empty-icon">🌳</div>
      <div>Nenhum registro ainda.</div>
      <div style="font-size:12px;margin-top:8px">Preencha os dados de um dia e clique <strong style="color:var(--easy)">Salvar</strong>.</div>
    </div>`;
    return;
  }

  treeEl.innerHTML = Object.entries(tree).map(([matName, matData]) =>
    buildMatNode(matName, matData)
  ).join('');
}

// Safe hash for DOM IDs
function htSafeId(str) {
  let h = 0;
  for (let i = 0; i < str.length; i++) { h = (Math.imul(31, h) + str.charCodeAt(i)) | 0; }
  return Math.abs(h).toString(36);
}

function buildHistoryTree() {
  const tree = {};
  const diffLabel = {easy:'Fácil', med:'Médio', hard:'Difícil'};
  const diffColor = {easy:'var(--easy)', med:'var(--accent4)', hard:'var(--hard)'};

  // 1. Populate from studyHistory
  studyHistory.forEach(rec => {
    const ts = new Date(rec.timestamp);
    const dateStr = ts.toLocaleDateString('pt-BR', {day:'2-digit', month:'2-digit', year:'numeric'});
    const timeStr = ts.toLocaleTimeString('pt-BR', {hour:'2-digit', minute:'2-digit'});

    rec.materias.forEach(mat => {
      const matKey = mat.name.trim();
      if (!tree[matKey]) {
        tree[matKey] = {
          color: mat.color || rec.color || '#7c5cfc',
          topicos: {}, totalSessoes: 0, done: false,
        };
      }
      tree[matKey].totalSessoes++;

      // Tópico = subtitle or day name
      const topicoKey = rec.subtitle || rec.dayName || 'Geral';
      if (!tree[matKey].topicos[topicoKey]) {
        tree[matKey].topicos[topicoKey] = { subtopicos: {}, cnt: 0 };
      }
      tree[matKey].topicos[topicoKey].cnt++;

      // Subtópico = methods used or 'Estudo geral'
      const metStr = (mat.metodos||[]).join(', ') || 'Estudo geral';
      if (!tree[matKey].topicos[topicoKey].subtopicos[metStr]) {
        tree[matKey].topicos[topicoKey].subtopicos[metStr] = { registros: [] };
      }

      // Level 4 registro
      tree[matKey].topicos[topicoKey].subtopicos[metStr].registros.push({
        id: rec.id,
        date: dateStr + ' ' + timeStr,
        desc: [
          mat.notas,
          mat.horario ? '⏰ '+mat.horario : '',
          mat.desempenho ? '📊 '+mat.desempenho+'/10' : '',
          mat.dificuldade ? diffLabel[mat.dificuldade] : '',
        ].filter(Boolean).join(' · ') || rec.metas || '(sem anotações)',
        color: mat.color || rec.color,
        pills: [
          mat.horario ? '⏰ '+mat.horario : '',
          mat.dificuldade ? diffLabel[mat.dificuldade]||'' : '',
          mat.desempenho ? '📊 '+mat.desempenho+'/10' : '',
        ].filter(Boolean),
      });
    });
  });

  // 2. Enrich with fcData flashcards (add a 'Flashcards' subtópico per tópico)
  if (typeof fcData !== 'undefined') {
    fcData.materias.forEach(m => {
      const matKey = m.name;
      if (!tree[matKey]) {
        tree[matKey] = {
          color: m.color||'#7c5cfc',
          topicos: {}, totalSessoes: 0, done: false,
        };
      }
      m.topicos.forEach(t => {
        if (!t.cards.length) return;
        const topicoKey = t.name;
        if (!tree[matKey].topicos[topicoKey]) {
          tree[matKey].topicos[topicoKey] = { subtopicos: {}, cnt: 0 };
        }
        const subKey = '🃏 Flashcards ('+t.cards.length+')';
        if (!tree[matKey].topicos[topicoKey].subtopicos[subKey]) {
          tree[matKey].topicos[topicoKey].subtopicos[subKey] = { registros: [] };
        }
        // One registro per card with SM-2 info
        t.cards.forEach(c => {
          const dueStr = c.proximaRev || 'Nunca revisado';
          tree[matKey].topicos[topicoKey].subtopicos[subKey].registros.push({
            id: null,
            date: c.proximaRev ? 'Próxima: '+c.proximaRev : 'Novo',
            desc: c.q.substring(0,80)+(c.q.length>80?'…':''),
            color: m.color||'#7c5cfc',
            pills: [
              c.tipo ? {lei:'Lei',juris:'Juris',doutrina:'Doutrina'}[c.tipo]||'' : '',
              c.repeticoes ? c.repeticoes+' reps' : '',
              c.facilidade ? 'EF '+c.facilidade : '',
            ].filter(Boolean),
          });
        });
      });
    });
  }

  return tree;
}

function buildMatNode(matName, matData) {
  const color   = matData.color || '#7c5cfc';
  const topicosCount = Object.keys(matData.topicos).length;
  const totalRecs = Object.values(matData.topicos).reduce((s,t)=>
    s+Object.values(t.subtopicos).reduce((ss,sub)=>ss+sub.registros.length,0),0);
  const progress = matData.done ? 100 :
    Math.min(100, Math.round(matData.totalSessoes / Math.max(1, topicosCount) * 25));

  const nodeId = 'hm_'+htSafeId(matName);
  const doneKey = 'hist_done_'+matName;
  const isDone  = localStorage.getItem(doneKey) === '1';

  const topicosHtml = Object.entries(matData.topicos).map(([tname, tdata]) =>
    buildTopicoNode(tname, tdata, color, nodeId)
  ).join('');

  return `
  <div class="ht-materia" id="${nodeId}">
    <div class="ht-m-header" style="border-left-color:${color}" onclick="htToggle('${nodeId}')">
      <span class="ht-m-arrow">▶</span>
      <span class="ht-m-icon">📚</span>
      <span class="ht-m-name" style="color:${color}">${matName}</span>
      <div class="ht-m-stats">
        <span class="ht-stat-pill" style="color:${color};border-color:${color}44;background:${color}11">${topicosCount} tópico${topicosCount!==1?'s':''}</span>
        <span class="ht-stat-pill" style="color:var(--text-dim);border-color:var(--border)">${totalRecs} registros</span>
        <div class="ht-progress-wrap" title="${progress}% progresso">
          <div class="ht-progress-fill" style="width:${progress}%;background:${color}"></div>
        </div>
        <div class="ht-check ${isDone?'done':''}" style="border-color:${color}"
          onclick="event.stopPropagation();htToggleDone('${matName}','${nodeId}')"
          title="Marcar como concluída">
          ${isDone?'✓':''}
        </div>
      </div>
    </div>
    <div class="ht-m-children">${topicosHtml}</div>
  </div>`;
}

function buildTopicoNode(tname, tdata, color, parentId) {
  const nodeId = parentId+'_t_'+htSafeId(tname);
  const totalCards = Object.values(tdata.subtopicos).reduce((s,sub)=>s+sub.registros.length,0);

  const subtopicosHtml = Object.entries(tdata.subtopicos).map(([sname, sdata]) =>
    buildSubtopicoNode(sname, sdata, color, nodeId)
  ).join('');

  return `
  <div class="ht-topico" id="${nodeId}">
    <div class="ht-t-header" onclick="htToggle('${nodeId}')">
      <span class="ht-t-arrow">▶</span>
      <span class="ht-t-icon">📌</span>
      <span class="ht-t-name">${tname}</span>
      <span class="ht-t-cnt">${totalCards} item${totalCards!==1?'s':''}</span>
    </div>
    <div class="ht-t-children">${subtopicosHtml}</div>
  </div>`;
}

function buildSubtopicoNode(sname, sdata, color, parentId) {
  const nodeId = parentId+'_s_'+htSafeId(sname);
  const cnt = sdata.registros.length;

  // Sort by date desc
  const regs = [...sdata.registros].sort((a,b) => {
    const da = a.date||'', db = b.date||'';
    return db.localeCompare(da);
  });

  const registrosHtml = regs.map(r => `
    <div class="ht-registro">
      <div class="ht-reg-dot" style="background:${r.color||color}"></div>
      <div class="ht-reg-content">
        <div class="ht-reg-date">${r.date}</div>
        <div class="ht-reg-desc">${r.desc}</div>
        ${r.pills.length?`<div class="ht-reg-pills">${r.pills.map(p=>`<span class="ht-reg-pill">${p}</span>`).join('')}</div>`:''}
      </div>
      ${r.id?`<button class="ht-reg-del" onclick="event.stopPropagation();deleteHistoryItem('${r.id}')" title="Remover">✕</button>`:''}
    </div>`).join('');

  const icon = sname.startsWith('🃏') ? '' : '📝';
  return `
  <div class="ht-subtopico" id="${nodeId}">
    <div class="ht-s-header" onclick="htToggle('${nodeId}')">
      <span class="ht-s-arrow">▶</span>
      <span class="ht-s-icon">${icon}</span>
      <span class="ht-s-name">${sname}</span>
      <div class="ht-s-meta">
        <span>${cnt} item${cnt!==1?'s':''}</span>
      </div>
    </div>
    <div class="ht-s-children">${registrosHtml}</div>
  </div>`;
}

function htToggle(nodeId) {
  const el = document.getElementById(nodeId);
  if (el) el.classList.toggle('open');
}

function htToggleDone(matName, nodeId) {
  const key = 'hist_done_'+matName;
  const isDone = localStorage.getItem(key) === '1';
  localStorage.setItem(key, isDone ? '0' : '1');
  renderHistTree();
}

// ── CARDS VIEW (original renderHistory) ──────────
function renderHistory() {
  const grid = document.getElementById('historyGrid');
  if (!grid) return;

  const filtered = histFilterActive === 'all'
    ? studyHistory
    : studyHistory.filter(h => String(h.dayIndex) === histFilterActive);

  if (filtered.length === 0) {
    grid.innerHTML = `<div class="history-empty" style="grid-column:1/-1">
      <div class="history-empty-icon">📋</div>
      <div>Nenhum registro ainda.<br>Preencha os dados de um dia e clique em <strong style="color:var(--easy)">Salvar</strong>.</div>
    </div>`;
    return;
  }

  const diffLabel = {easy:'Fácil',med:'Médio',hard:'Difícil'};
  const diffColor = {easy:'var(--easy)',med:'var(--accent4)',hard:'var(--hard)'};

  grid.innerHTML = filtered.map(rec => {
    const ts = new Date(rec.timestamp);
    const tsStr = ts.toLocaleDateString('pt-BR') + ' ' + ts.toLocaleTimeString('pt-BR',{hour:'2-digit',minute:'2-digit'});

    const materiasHtml = rec.materias.length > 0
      ? rec.materias.map(m => `
        <div class="history-materia">
          <div class="hm-color-bar" style="background:${m.color}"></div>
          <div class="hm-info">
            <div class="hm-name">${m.name}</div>
            <div class="hm-meta">
              ${m.horario?`<span class="hm-pill">⏰ ${m.horario}</span>`:''}
              ${m.dificuldade?`<span class="hm-pill" style="color:${diffColor[m.dificuldade]||''}">${diffLabel[m.dificuldade]||m.dificuldade}</span>`:''}
              ${m.desempenho?`<span class="hm-pill">📊 ${m.desempenho}/10</span>`:''}
              ${(m.metodos||[]).map(mt=>`<span class="hm-pill">${mt}</span>`).join('')}
            </div>
            ${m.notas?`<div class="hm-notes">${m.notas}</div>`:''}
          </div>
        </div>`).join('')
      : `<div style="font-size:12px;color:var(--text-dim)">Nenhuma matéria registrada.</div>`;

    const extraHtml = (rec.metas||rec.obs) ? `
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-top:2px">
        ${rec.metas?`<div style="background:var(--surface2);border:1px solid var(--border);border-radius:6px;padding:10px 12px">
          <div style="font-size:10px;color:var(--text-dim);text-transform:uppercase;letter-spacing:.07em;margin-bottom:4px">🎯 Metas</div>
          <div style="font-size:11px;color:var(--text-muted);line-height:1.5">${rec.metas}</div></div>`:''}
        ${rec.obs?`<div style="background:var(--surface2);border:1px solid var(--border);border-radius:6px;padding:10px 12px">
          <div style="font-size:10px;color:var(--text-dim);text-transform:uppercase;letter-spacing:.07em;margin-bottom:4px">💬 Obs.</div>
          <div style="font-size:11px;color:var(--text-muted);line-height:1.5">${rec.obs}</div></div>`:''}
      </div>` : '';

    return `
    <div class="history-card" id="hcard-${rec.id}">
      <div class="history-card-head">
        <div class="history-card-day">
          <div class="history-day-dot" style="background:${rec.color}"></div>
          <span class="history-day-name" style="color:${rec.color}">${rec.dayName}</span>
        </div>
        <div style="display:flex;flex-direction:column;align-items:flex-end;gap:6px">
          <span class="history-timestamp">${tsStr}</span>
          <div class="history-card-actions">
            <button class="btn-hist-edit" onclick="editHistoryItem('${rec.id}')">✏️ Editar</button>
            <button class="btn-hist-del"  onclick="deleteHistoryItem('${rec.id}')">✕</button>
          </div>
        </div>
      </div>
      ${rec.subtitle?`<div class="history-day-subtitle">📚 ${rec.subtitle}</div>`:''}
      <div class="history-card-body">${materiasHtml}${extraHtml}</div>
    </div>`;
  }).join('');
}

// renderHistoryAll — keeps both views in sync
function renderHistoryAll() {
  renderHistory();
  if (histViewMode === 'tree') renderHistTree();
}



function showToast(msg, type='ok') {
  let t = document.getElementById('sfToast');
  if (!t) {
    t = document.createElement('div'); t.id='sfToast';
    t.style.cssText=`position:fixed;bottom:90px;left:50%;transform:translateX(-50%) translateY(20px);
      padding:10px 24px;border-radius:100px;font-family:'Syne',sans-serif;font-size:12px;font-weight:700;
      z-index:99999;opacity:0;transition:all .3s;pointer-events:none;white-space:nowrap;`;
    document.body.appendChild(t);
  }
  t.textContent=msg;
  t.style.background=type==='ok'?'rgba(92,240,176,.97)':'rgba(252,184,92,.97)';
  t.style.color='#0a0a0f';
  t.style.boxShadow=`0 4px 24px ${type==='ok'?'rgba(92,240,176,.4)':'rgba(252,184,92,.4)'}`;
  t.style.opacity='1'; t.style.transform='translateX(-50%) translateY(0)';
  clearTimeout(t._t);
  t._t=setTimeout(()=>{t.style.opacity='0';t.style.transform='translateX(-50%) translateY(20px)';},2800);
}


// ══════════════════════════════════════════════════
// BACKUP & LINK SYSTEM
// ══════════════════════════════════════════════════

function getAllData() {
  return {
    version: 2,
    exported: new Date().toISOString(),
    subjectsMatrix:  { subjects, matrix, nextSubjId },
    hoursData:       hoursData,
    studyHistory:    studyHistory,
    ankiEntries:     ankiEntries,
    fcData:          fcData,
    weekPicker:      document.getElementById('weekPicker')?.value || '',
    daySubtitles:    Array.from({length:7}, (_,i) => document.getElementById(`daySubtitle-${i}`)?.value || ''),
    dayMetas:        Array.from({length:7}, (_,i) => {
      const p = document.getElementById(`day-${i}`);
      if (!p) return {metas:'',obs:''};
      const tas = p.querySelectorAll('.day-notes-grid textarea');
      return { metas: tas[0]?.value || '', obs: tas[1]?.value || '' };
    }),
    blockData: (() => {
      const blocks = {};
      for (let d=0;d<7;d++) for (let b=0;b<2;b++) {
        const bid = `d${d}b${b}`;
        const el  = document.getElementById(`block-${bid}`);
        if (!el) continue;
        const subj = document.getElementById(`subj-${bid}`)?.value || '';
        const time = el.querySelector('input[type="time"]')?.value || '';
        const diff = el.querySelector('.diff-btn.sel')?.dataset.diff || '';
        const meths = [...(el.querySelectorAll('.method-btn.sel')||[])].map(m=>m.dataset.method);
        const notes = el.querySelector('.block-body textarea.field-input')?.value || '';
        const perf  = el.querySelector('.field-input[type="number"]')?.value || '';
        const done  = document.getElementById(`check-${bid}`)?.classList.contains('done') || false;
        blocks[bid] = { subj, time, diff, meths, notes, perf, done };
      }
      return blocks;
    })()
  };
}

function exportBackup() {
  const data = getAllData();
  const json = JSON.stringify(data, null, 2);
  const blob = new Blob([json], { type: 'application/json' });
  const url  = URL.createObjectURL(blob);
  const a    = document.createElement('a');
  const date = new Date().toLocaleDateString('pt-BR').replace(/\//g,'-');
  a.href     = url;
  a.download = `studyflow_backup_${date}.json`;
  a.click();
  URL.revokeObjectURL(url);
  showToast('✓ Backup baixado com sucesso!', 'ok');
}

function importBackup(event) {
  const file = event.target.files[0];
  if (!file) return;
  if (!confirm(`Restaurar backup de "${file.name}"?\n\nIsso substituirá TODOS os dados atuais.`)) return;
  const reader = new FileReader();
  reader.onload = e => {
    try {
      const data = JSON.parse(e.target.result);
      if (!data.version) throw new Error('Arquivo inválido');

      // Restore subjects/matrix
      if (data.subjectsMatrix) {
        subjects   = data.subjectsMatrix.subjects || subjects;
        matrix     = data.subjectsMatrix.matrix   || matrix;
        nextSubjId = data.subjectsMatrix.nextSubjId || subjects.length;
        saveSubjectsMatrix();
      }

      // Restore hours
      if (data.hoursData) {
        data.hoursData.forEach((v,i) => { if(i<7) hoursData[i]=v; });
        saveHoursData();
        buildHoursGrid();
      }

      // Restore history
      if (data.studyHistory) {
        studyHistory = data.studyHistory;
        persistHistory();
        renderHistoryAll();
      }

      // Restore anki entries
      if (data.ankiEntries) {
        ankiEntries = data.ankiEntries;
        saveAnkiEntries();
        renderAnkiTable();
      }

      // Restore flashcards (new hierarchical structure)
      if (data.fcData) {
        fcData = data.fcData;
        fcSaveData();
        fcRenderMateriaTabs();
      } else if (data.fcCards) {
        // Legacy migration: convert old flat fcCards to new structure
        const legacyMateria = { id:fcNewId(), name:'Importados', color:'#7c5cfc', topicos:[] };
        const byDeck = {};
        data.fcCards.forEach(c => {
          const deck = c.deck||'Geral';
          if (!byDeck[deck]) byDeck[deck] = [];
          byDeck[deck].push({id:c.id||fcNewId(),q:c.q,a:c.a,ef:c.ef||2.5,interval:c.interval||1,reps:c.reps||0,due:c.due||new Date().toISOString(),lastGrade:null});
        });
        Object.entries(byDeck).forEach(([deck,cards])=>{ legacyMateria.topicos.push({id:fcNewId(),name:deck,cards}); });
        fcData.materias.push(legacyMateria);
        fcSaveData();
        fcRenderMateriaTabs();
      }

      // Restore week
      if (data.weekPicker) {
        document.getElementById('weekPicker').value = data.weekPicker;
        updateRevisions();
      }

      // Re-render subjects/matrix
      renderSubjTabs();
      renderMatrix();
      refreshDayLabels();

      // Restore day subtitles
      if (data.daySubtitles) {
        data.daySubtitles.forEach((v,i) => {
          const el = document.getElementById(`daySubtitle-${i}`);
          if (el) el.value = v;
        });
      }

      // Restore day metas/obs
      if (data.dayMetas) {
        data.dayMetas.forEach((m,i) => {
          const p = document.getElementById(`day-${i}`);
          if (!p) return;
          const tas = p.querySelectorAll('.day-notes-grid textarea');
          if (tas[0]) tas[0].value = m.metas || '';
          if (tas[1]) tas[1].value = m.obs   || '';
        });
      }

      // Restore block data
      if (data.blockData) {
        Object.entries(data.blockData).forEach(([bid, bd]) => {
          const subj = document.getElementById(`subj-${bid}`);
          if (subj) subj.value = bd.subj || '';
          const el = document.getElementById(`block-${bid}`);
          if (!el) return;
          const timeEl = el.querySelector('input[type="time"]');
          if (timeEl && bd.time) { timeEl.value = bd.time; const td=document.getElementById(`timeDisp-${bid}`); if(td) td.textContent=bd.time; }
          el.querySelectorAll('.diff-btn').forEach(btn => btn.classList.toggle('sel', btn.dataset.diff === bd.diff));
          el.querySelectorAll('.method-btn').forEach(btn => btn.classList.toggle('sel', (bd.meths||[]).includes(btn.dataset.method)));
          const noteTA = el.querySelector('.block-body textarea.field-input');
          if (noteTA) noteTA.value = bd.notes || '';
          const perfEl = el.querySelector('.field-input[type="number"]');
          if (perfEl) perfEl.value = bd.perf || '';
          const chk = document.getElementById(`check-${bid}`);
          if (chk) chk.classList.toggle('done', !!bd.done);
        });
        updateRevisions();
      }

      updateBackupStats();
      showToast('✓ Backup restaurado com sucesso!', 'ok');
    } catch(err) {
      alert('Erro ao restaurar backup: ' + err.message);
    }
  };
  reader.readAsText(file);
  event.target.value = ''; // reset input
}

function resetAllData() {
  if (!confirm('⚠️ ATENÇÃO: Isso apagará TODOS os dados.\n\nTem certeza?')) return;
  if (!confirm('Confirmação final: apagar tudo?')) return;
  localStorage.clear();
  showToast('Dados apagados. Recarregando...', 'warn');
  setTimeout(() => location.reload(), 1200);
}

// ══════════════════════════════════════════════════
// ☁️ GITHUB GIST — SINCRONIZAÇÃO NA NUVEM
// ══════════════════════════════════════════════════

const GIST_TOKEN_KEY = 'sf_gist_token';
const GIST_ID_KEY    = 'sf_gist_id';
const GIST_LAST_KEY  = 'sf_gist_last';
const GIST_FILE      = 'studyflow_backup.json';
const GIST_API       = 'https://api.github.com/gists';

// ── Helpers ──
function gistGetToken() { return localStorage.getItem(GIST_TOKEN_KEY) || ''; }
function gistGetId()    { return localStorage.getItem(GIST_ID_KEY)    || ''; }

function gistUpdateStatus(type, msg) {
  const dot  = document.getElementById('gistStatusDot');
  const text = document.getElementById('gistStatusText');
  if (!dot || !text) return;
  const colors = { ok:'#5cf0b0', error:'#fc5c7d', loading:'#fcb85c', none:'#555' };
  dot.style.background  = colors[type] || '#555';
  text.textContent      = msg;
}

function gistTokenChanged() {
  const v = document.getElementById('gistTokenInput')?.value.trim();
  // Show/hide by typing
}

// ── Setup ──
function gistInitUI() {
  const token = gistGetToken();
  const id    = gistGetId();
  const last  = localStorage.getItem(GIST_LAST_KEY);

  const tokenInput = document.getElementById('gistTokenInput');
  const idArea     = document.getElementById('gistIdArea');
  const idInput    = document.getElementById('gistIdInput');
  const btnSalvar  = document.getElementById('gistBtnSalvar');
  const btnCarregar= document.getElementById('gistBtnCarregar');
  const btnDescon  = document.getElementById('gistBtnDesconectar');
  const lastEl     = document.getElementById('gistLastSync');

  if (!token) {
    gistUpdateStatus('none', 'Não configurado — cole seu token abaixo');
    if (btnSalvar)   btnSalvar.style.display   = 'none';
    if (btnCarregar) btnCarregar.style.display  = 'none';
    if (btnDescon)   btnDescon.style.display    = 'none';
    if (idArea)      idArea.style.display       = 'none';
    return;
  }

  // Token exists
  if (tokenInput) tokenInput.value = token.substring(0, 6) + '••••••••••••••';
  if (idArea)     idArea.style.display = 'block';
  if (idInput && id) idInput.value = id;
  if (btnSalvar)   btnSalvar.style.display   = '';
  if (btnCarregar) btnCarregar.style.display  = '';
  if (btnDescon)   btnDescon.style.display    = '';

  if (id) {
    gistUpdateStatus('ok', `Conectado · Gist: ${id.substring(0,12)}...`);
  } else {
    gistUpdateStatus('loading', 'Token salvo — clique ☁️ Salvar para criar seu Gist');
  }

  if (last && lastEl) {
    const d = new Date(parseInt(last));
    lastEl.textContent = `Última sync: ${d.toLocaleDateString('pt-BR')} às ${d.toLocaleTimeString('pt-BR',{hour:'2-digit',minute:'2-digit'})}`;
  }
}

function gistSalvarToken() {
  const input = document.getElementById('gistTokenInput');
  const val   = input?.value.trim();
  if (!val || val.includes('•')) {
    showToast('Cole um token válido (ghp_...)', 'warn');
    return;
  }
  if (!val.startsWith('ghp_') && !val.startsWith('github_pat_')) {
    showToast('Token inválido. Deve começar com ghp_ ou github_pat_', 'warn');
    return;
  }
  localStorage.setItem(GIST_TOKEN_KEY, val);
  gistInitUI();
  showToast('✓ Token salvo! Clique ☁️ Salvar na nuvem', 'ok');
}

function gistUsarId() {
  const input = document.getElementById('gistIdInput');
  const val   = input?.value.trim();
  if (!val) { showToast('Cole um Gist ID', 'warn'); return; }
  localStorage.setItem(GIST_ID_KEY, val);
  gistInitUI();
  showToast('✓ Gist ID configurado! Clique ⬇ Carregar', 'ok');
}

function gistDesconectar() {
  if (!confirm('Desconectar da nuvem? Seus dados locais não serão apagados.')) return;
  localStorage.removeItem(GIST_TOKEN_KEY);
  localStorage.removeItem(GIST_ID_KEY);
  localStorage.removeItem(GIST_LAST_KEY);
  const inp = document.getElementById('gistTokenInput');
  if (inp) inp.value = '';
  gistInitUI();
  showToast('Desconectado da nuvem', 'warn');
}

// ── Save to Gist ──
async function gistSalvar() {
  const token = gistGetToken();
  if (!token) { showToast('Configure o token primeiro', 'warn'); return; }

  gistUpdateStatus('loading', 'Salvando na nuvem...');
  const btn = document.getElementById('gistBtnSalvar');
  if (btn) { btn.disabled = true; btn.textContent = '⏳ Salvando...'; }

  try {
    // Force save everything first
    sfSaveAll();

    const payload = JSON.stringify(getAllData(), null, 2);
    const id      = gistGetId();

    let res, data;
    if (id) {
      // Update existing gist
      res = await fetch(`${GIST_API}/${id}`, {
        method: 'PATCH',
        headers: { 'Authorization': `token ${token}`, 'Content-Type': 'application/json' },
        body: JSON.stringify({
          description: `StudyFlow Backup — ${new Date().toLocaleString('pt-BR')}`,
          files: { [GIST_FILE]: { content: payload } }
        })
      });
    } else {
      // Create new gist
      res = await fetch(GIST_API, {
        method: 'POST',
        headers: { 'Authorization': `token ${token}`, 'Content-Type': 'application/json' },
        body: JSON.stringify({
          description: 'StudyFlow Pro — Backup automático',
          public: false,
          files: { [GIST_FILE]: { content: payload } }
        })
      });
    }

    if (!res.ok) {
      const err = await res.json().catch(()=>({}));
      throw new Error(err.message || `HTTP ${res.status}`);
    }

    data = await res.json();
    const newId = data.id;
    localStorage.setItem(GIST_ID_KEY, newId);
    localStorage.setItem(GIST_LAST_KEY, Date.now().toString());

    // Show gist ID to user
    const idInput = document.getElementById('gistIdInput');
    if (idInput) idInput.value = newId;

    gistUpdateStatus('ok', `✓ Salvo! Gist: ${newId.substring(0,12)}...`);
    gistInitUI();
    showToast('☁️ Dados salvos na nuvem!', 'ok');

  } catch(e) {
    gistUpdateStatus('error', `Erro: ${e.message}`);
    showToast(`Erro ao salvar: ${e.message}`, 'warn');
    console.error('Gist save error:', e);
  } finally {
    if (btn) { btn.disabled = false; btn.textContent = '☁️ Salvar na nuvem'; }
  }
}

// ── Load from Gist ──
async function gistCarregar() {
  const token = gistGetToken();
  const id    = gistGetId();
  if (!token) { showToast('Configure o token primeiro', 'warn'); return; }
  if (!id)    { showToast('Faça uma primeira sync para criar o Gist', 'warn'); return; }

  if (!confirm('Carregar dados da nuvem?\n\nIsso irá sobrescrever os dados locais atuais com os dados salvos na nuvem.')) return;

  gistUpdateStatus('loading', 'Carregando da nuvem...');
  const btn = document.getElementById('gistBtnCarregar');
  if (btn) { btn.disabled = true; btn.textContent = '⏳ Carregando...'; }

  try {
    const res = await fetch(`${GIST_API}/${id}`, {
      headers: { 'Authorization': `token ${token}` }
    });

    if (!res.ok) {
      const err = await res.json().catch(()=>({}));
      throw new Error(err.message || `HTTP ${res.status}`);
    }

    const gistData = await res.json();
    const fileContent = gistData.files?.[GIST_FILE]?.content;
    if (!fileContent) throw new Error('Arquivo não encontrado no Gist');

    const data = JSON.parse(fileContent);

    // Import all data (reuse importBackup logic)
    await gistImportData(data);
    localStorage.setItem(GIST_LAST_KEY, Date.now().toString());

    gistUpdateStatus('ok', `✓ Dados carregados da nuvem`);
    gistInitUI();
    showToast('☁️ Dados carregados da nuvem!', 'ok');

    // Reload after 1.5s to apply all data
    setTimeout(() => location.reload(), 1500);

  } catch(e) {
    gistUpdateStatus('error', `Erro: ${e.message}`);
    showToast(`Erro ao carregar: ${e.message}`, 'warn');
    console.error('Gist load error:', e);
  } finally {
    if (btn) { btn.disabled = false; btn.textContent = '⬇ Carregar da nuvem'; }
  }
}

// ── Apply loaded data ──
async function gistImportData(data) {
  if (!data || !data.version) throw new Error('Formato de backup inválido');

  // Save everything to localStorage
  if (data.subjectsMatrix) {
    localStorage.setItem('sf_subjectsMatrix', JSON.stringify(data.subjectsMatrix));
  }
  if (data.studyHistory) {
    localStorage.setItem('sf_studyHistory', JSON.stringify(data.studyHistory));
  }
  if (data.ankiEntries) {
    localStorage.setItem('sf_ankiEntries', JSON.stringify(data.ankiEntries));
  }
  if (data.fcData) {
    localStorage.setItem('sf_fcData_v2', JSON.stringify(data.fcData));
  }
  if (data.hoursData) {
    localStorage.setItem('sf_hoursData', JSON.stringify(data.hoursData));
  }
  if (data.planejamentos) {
    localStorage.setItem('sf_planejamento_v1', JSON.stringify(data.planejamentos));
  }
  if (data.sessoes) {
    localStorage.setItem('sf_sessoes_v1', JSON.stringify(data.sessoes));
  }
  // Save master state
  const state = { v:3, ts:Date.now(), ...data };
  localStorage.setItem('sf_fullState_v3', JSON.stringify(state));
}

// ── Auto-sync on save (optional, triggered after every sfSaveAll) ──
let gistAutoSyncTimer = null;
function gistScheduleAutoSync() {
  if (!gistGetToken() || !gistGetId()) return; // only if configured
  clearTimeout(gistAutoSyncTimer);
  gistAutoSyncTimer = setTimeout(() => {
    gistSalvar().catch(() => {}); // silent fail on auto-sync
  }, 30000); // sync 30s after last change
}

// Init gist UI on load
setTimeout(gistInitUI, 500);


// ══════════════════════════════════════════════════
// PERSISTENT STATE — saves & restores EVERYTHING
// automatically on every change
// ══════════════════════════════════════════════════

const SF_KEY = 'sf_fullState_v3';

function sfSaveAll() {
  try {
    const state = {
      v: 3,
      ts: Date.now(),
      // Week is intentionally NOT saved — always recalculated from today's date on load
      subtitles: Array.from({length:7}, (_,i) =>
        document.getElementById(`daySubtitle-${i}`)?.value || ''
      ),
      dayNotes: Array.from({length:7}, (_,i) => {
        const p = document.getElementById(`day-${i}`);
        if (!p) return {metas:'', obs:''};
        const tas = p.querySelectorAll('.day-notes-grid textarea');
        return { metas: tas[0]?.value || '', obs: tas[1]?.value || '' };
      }),
      blocks: (() => {
        const out = {};
        for (let d=0;d<7;d++) for (let b=0;b<2;b++) {
          const bid = `d${d}b${b}`;
          const el  = document.getElementById(`block-${bid}`);
          if (!el) continue;
          const timeEl = el.querySelector('input[type="time"]');
          const diffEl = el.querySelector('.diff-btn.sel');
          const methEls= el.querySelectorAll('.method-btn.sel');
          const noteTA = el.querySelector('.block-body textarea.field-input');
          const perfEl = el.querySelector('.field-input[type="number"]');
          const chkEl  = document.getElementById(`check-${bid}`);
          out[bid] = {
            subj : document.getElementById(`subj-${bid}`)?.value || '',
            time : timeEl?.value || '07:00',
            diff : diffEl?.dataset.diff || '',
            meths: [...(methEls||[])].map(m=>m.dataset.method),
            notes: noteTA?.value || '',
            perf : perfEl?.value || '',
            done : chkEl?.classList.contains('done') || false,
          };
        }
        return out;
      })(),
      // ── embed ALL critical data so nothing is lost on HTML update ──
      fcData:          typeof fcData !== 'undefined' ? fcData : null,
      ankiEntries:     typeof ankiEntries !== 'undefined' ? ankiEntries : [],
      studyHistory:    typeof studyHistory !== 'undefined' ? studyHistory : [],
      subjectsMatrix:  typeof subjects !== 'undefined' ? { subjects, matrix, nextSubjId } : null,
      hoursData:       typeof hoursData !== 'undefined' ? hoursData : null,
      planejamentos:   (() => { try { return JSON.parse(localStorage.getItem('sf_planejamento_v1')||'{}'); } catch(e) { return {}; } })(),
      sessoes:         (() => { try { return JSON.parse(localStorage.getItem('sf_sessoes_v1')||'{}'); } catch(e) { return {}; } })(),
    };
    localStorage.setItem(SF_KEY, JSON.stringify(state));
    // Schedule cloud auto-sync if configured
    if (typeof gistScheduleAutoSync === 'function') gistScheduleAutoSync();
  } catch(e) { console.warn('sfSaveAll error:', e); }
}

function sfRestoreAll() {
  try {
    const raw = localStorage.getItem(SF_KEY);
    if (!raw) return;
    const state = JSON.parse(raw);
    if (!state || state.v !== 3) return;

    // Week — always recalculate current ISO week; never restore an old saved value
    (function setCorrectWeek() {
      const now = new Date();
      const iso = getISOWeekFromDate(now);
      const currentWeekVal = `${iso.year}-W${String(iso.week).padStart(2,'0')}`;
      const wp = document.getElementById('weekPicker');
      if (wp) wp.value = currentWeekVal;
      updateRevisions();
      setTimeout(refreshDayLabels, 60);
    })();

    // Subtitles
    (state.subtitles || []).forEach((v,i) => {
      const el = document.getElementById(`daySubtitle-${i}`);
      if (el) el.value = v;
    });

    // Day notes
    (state.dayNotes || []).forEach((n,i) => {
      const p = document.getElementById(`day-${i}`);
      if (!p) return;
      const tas = p.querySelectorAll('.day-notes-grid textarea');
      if (tas[0]) tas[0].value = n.metas || '';
      if (tas[1]) tas[1].value = n.obs   || '';
    });

    // Blocks
    Object.entries(state.blocks || {}).forEach(([bid, bd]) => {
      // Subject
      const subjEl = document.getElementById(`subj-${bid}`);
      if (subjEl) subjEl.value = bd.subj || '';

      const blockEl = document.getElementById(`block-${bid}`);
      if (!blockEl) return;

      // Time
      const timeEl = blockEl.querySelector('input[type="time"]');
      if (timeEl && bd.time) {
        timeEl.value = bd.time;
        const disp = document.getElementById(`timeDisp-${bid}`);
        if (disp) disp.textContent = bd.time;
      }

      // Difficulty
      blockEl.querySelectorAll('.diff-btn').forEach(btn =>
        btn.classList.toggle('sel', btn.dataset.diff === bd.diff)
      );

      // Methods
      blockEl.querySelectorAll('.method-btn').forEach(btn =>
        btn.classList.toggle('sel', (bd.meths||[]).includes(btn.dataset.method))
      );

      // Notes textarea
      const noteTA = blockEl.querySelector('.block-body textarea.field-input');
      if (noteTA) noteTA.value = bd.notes || '';

      // Performance
      const perfEl = blockEl.querySelector('.field-input[type="number"]');
      if (perfEl) perfEl.value = bd.perf || '';

      // Done checkbox
      const chkEl = document.getElementById(`check-${bid}`);
      if (chkEl) chkEl.classList.toggle('done', !!bd.done);
    });

    // Refresh revisions with restored data
    updateRevisions();

    // ── Recover embedded data if dedicated keys are missing ──
    // fcData
    if (state.fcData && state.fcData.materias) {
      try {
        const existing = localStorage.getItem(FC_KEY);
        if (!existing) {
          fcData = state.fcData;
          localStorage.setItem(FC_KEY, JSON.stringify(fcData));
          fcRenderMateriaTabs();
        }
      } catch(e2) {}
    }
    // ankiEntries
    if (state.ankiEntries && state.ankiEntries.length) {
      try {
        const existing = localStorage.getItem('sf_ankiEntries');
        if (!existing) {
          ankiEntries = state.ankiEntries;
          saveAnkiEntries();
          renderAnkiTable();
        }
      } catch(e2) {}
    }
    // studyHistory
    if (state.studyHistory && state.studyHistory.length) {
      try {
        const existing = localStorage.getItem('sf_studyHistory');
        if (!existing) {
          studyHistory = state.studyHistory;
          persistHistory();
          renderHistoryAll();
        }
      } catch(e2) {}
    }
    // subjectsMatrix
    if (state.subjectsMatrix && state.subjectsMatrix.subjects) {
      try {
        const existing = localStorage.getItem('sf_subjectsMatrix');
        if (!existing) {
          subjects    = state.subjectsMatrix.subjects;
          matrix      = state.subjectsMatrix.matrix || matrix;
          nextSubjId  = state.subjectsMatrix.nextSubjId || subjects.length;
          saveSubjectsMatrix();
          renderSubjTabs();
          renderMatrix();
        }
      } catch(e2) {}
    }
    // hoursData
    if (state.hoursData && state.hoursData.length) {
      try {
        const existing = localStorage.getItem('sf_hoursData');
        if (!existing) {
          state.hoursData.forEach((v,i) => { if(i<7) hoursData[i]=v; });
          saveHoursData();
          buildHoursGrid();
        }
      } catch(e2) {}
    }
    // planejamentos (calendar planning)
    if (state.planejamentos && Object.keys(state.planejamentos).length) {
      try {
        const existing = localStorage.getItem('sf_planejamento_v1');
        if (!existing || existing === '{}') {
          localStorage.setItem('sf_planejamento_v1', JSON.stringify(state.planejamentos));
        }
      } catch(e2) {}
    }
    // sessoes (study sessions stats)
    if (state.sessoes && Object.keys(state.sessoes).length) {
      try {
        const existing = localStorage.getItem('sf_sessoes_v1');
        if (!existing || existing === '{}') {
          localStorage.setItem('sf_sessoes_v1', JSON.stringify(state.sessoes));
        }
      } catch(e2) {}
    }

  } catch(e) { console.warn('sfRestoreAll error:', e); }
}

// Attach real-time save listeners to every editable element in the page
function sfAttachListeners() {
  // All text inputs, textareas, number inputs, time inputs, selects
  const selectors = [
    '.block-subject',
    'input[type="time"].field-input',
    '.block-body textarea.field-input',
    '.field-input[type="number"]',
    '.day-subtitle-input',
    '.day-notes-grid textarea',
  ];
  selectors.forEach(sel => {
    document.querySelectorAll(sel).forEach(el => {
      el.removeEventListener('input',  sfSaveAll);
      el.removeEventListener('change', sfSaveAll);
      el.addEventListener('input',  sfSaveAll);
      el.addEventListener('change', sfSaveAll);
    });
  });

  // Difficulty buttons
  document.querySelectorAll('.diff-btn').forEach(btn => {
    btn.removeEventListener('click', sfSaveAll);
    btn.addEventListener('click', () => setTimeout(sfSaveAll, 50));
  });

  // Method buttons
  document.querySelectorAll('.method-btn').forEach(btn => {
    btn.removeEventListener('click', sfSaveAll);
    btn.addEventListener('click', () => setTimeout(sfSaveAll, 50));
  });

  // Check-done buttons
  document.querySelectorAll('.check-done').forEach(btn => {
    btn.removeEventListener('click', sfSaveAll);
    btn.addEventListener('click', () => setTimeout(sfSaveAll, 50));
  });

  // Week picker
  const wp = document.getElementById('weekPicker');
  if (wp) {
    wp.removeEventListener('change', sfSaveAll);
    wp.addEventListener('change', sfSaveAll);
  }
}

// Update backup stats
function updateBackupStats() {
  const el = document.getElementById('backupStats');
  if (!el) return;
  el.innerHTML = `
    <span class="bstat">📚 <strong>${subjects.length}</strong> matérias</span>
    <span class="bstat">📋 <strong>${studyHistory.length}</strong> histórico</span>
    <span class="bstat">🃏 <strong>${ankiEntries.length}</strong> revisões Anki</span>
    <span class="bstat">🎴 <strong>${fcAllCards().length}</strong> flashcards</span>`;
}

// Update stats whenever data changes
const _origSaveAnki = saveAnkiEntries;
saveAnkiEntries = function() { _origSaveAnki(); updateBackupStats(); };
const _origPersistHist = persistHistory;
persistHistory = function() { _origPersistHist(); updateBackupStats(); };


// Set today's date in the Anki add form
const _ankiDateInput = document.getElementById('ankiAddDate');
if (_ankiDateInput) {
  const _t = new Date();
  _ankiDateInput.value = `${_t.getFullYear()}-${String(_t.getMonth()+1).padStart(2,'0')}-${String(_t.getDate()).padStart(2,'0')}`;
}

// ── SAVE BUTTONS FOR SUBJECTS & MATRIX ──
function showSavedBadge(id) {
  const el = document.getElementById(id);
  if (!el) return;
  el.style.opacity = '1';
  clearTimeout(el._t);
  el._t = setTimeout(() => { el.style.opacity = '0'; }, 2000);
}

function saveSubjectsNow() {
  saveSubjectsMatrix();
  showSavedBadge('subjSavedBadge');
  showToast('✓ Matérias salvas com segurança!', 'ok');
}

function saveMatrixNow() {
  saveSubjectsMatrix();
  showSavedBadge('matrixSavedBadge');
  showToast('✓ Grade semanal salva com segurança!', 'ok');
}

// ── AUTO-UPDATE LINK ──
function updateProgramLink() {
  const el = document.getElementById('programLink');
  if (el) el.value = window.location.href;
}
function copyLink() {
  const el = document.getElementById('programLink');
  if (!el) return;
  navigator.clipboard.writeText(el.value)
    .then(()  => showToast('✓ Link copiado!', 'ok'))
    .catch(()  => { el.select(); document.execCommand('copy'); showToast('✓ Link copiado!', 'ok'); });
}
function openInNewTab() { window.open(window.location.href, '_blank'); }

// Update link on any URL change (hash, query, etc.)
window.addEventListener('popstate',   updateProgramLink);
window.addEventListener('hashchange', updateProgramLink);
// Poll every 2s for changes (covers Netlify/file:// where URL might update)
setInterval(updateProgramLink, 2000);

// ── BOOT SEQUENCE ──
// 1. Restore all block/field data
setTimeout(() => {
  sfRestoreAll();
  sfAttachListeners();
  updateProgramLink();
  updateBackupStats();
  fcRenderDueSummary();
  ankiUpdateDashboard();
  renderHistTree();
}, 150);

// 2. Also re-attach after any tab click (panels are already rendered)
document.getElementById('daysNav').addEventListener('click', () => {
  setTimeout(sfAttachListeners, 80);
});

// 3. Save before page unloads (belt-and-suspenders)
window.addEventListener('beforeunload', sfSaveAll);
window.addEventListener('visibilitychange', () => {
  if (document.visibilityState === 'hidden') sfSaveAll();
});







// ══════════════════════════════════════════════════
// APP NAVIGATION
// ══════════════════════════════════════════════════
function switchPage(page, btn) {
  document.querySelectorAll('.app-tab').forEach(t=>t.classList.remove('active'));
  document.querySelectorAll('.app-page').forEach(p=>p.classList.remove('active'));
  const tabEl = btn || document.querySelector('.app-tab[data-page="'+page+'"]');
  if (tabEl) tabEl.classList.add('active');
  const pg = document.getElementById('page-'+page);
  if (pg) pg.classList.add('active');
  if (page==='planner') {
    refreshDayLabels();
    updateRevisions();
    renderSubjTabs();
    renderMatrix();
    buildHoursGrid();
    renderAnkiTable();
    ankiUpdateDashboard();
    renderHistoryAll();
    fcRenderMateriaTabs();
    fcRenderDueSummary();
    updateBackupStats();
    updateProgramLink();
    setTimeout(sfAttachListeners, 100);
  }
  if (page==='hoje')         renderHoje();
  if (page==='planejamento') { renderCalendario(); popularSelectsMaterias(); }
  if (page==='flashcards')   { fcRenderTree(); popularSelectsMaterias(); }
  if (page==='erros')        renderErros();
  if (page==='desempenho')   renderDesempenho();
  if (page==='prova')        renderProva();
}

const PLAN_KEY = 'sf_planejamento_v1';
const SESS_KEY = 'sf_sessoes_v1';
function loadPlanejamentos(){ try{ return JSON.parse(localStorage.getItem(PLAN_KEY)||'{}'); }catch(e){ return {}; } }
function savePlanejamentos(d){ localStorage.setItem(PLAN_KEY, JSON.stringify(d)); }
function loadSessoes(){ try{ return JSON.parse(localStorage.getItem(SESS_KEY)||'{}'); }catch(e){ return {}; } }
function saveSessoes(d){ localStorage.setItem(SESS_KEY, JSON.stringify(d)); }

function getMateriaColor(id){ const ms=typeof subjects!=='undefined'?subjects:[]; const idx=ms.findIndex(m=>m.id===id); return COLORS[idx>=0?idx%COLORS.length:0]||'#7c5cfc'; }
function getMateriaName(id){ const ms=typeof subjects!=='undefined'?subjects:[]; const s=ms.find(m=>m.id===id||m.name===id); return s?s.name:id; }

function popularSelectsMaterias(){
  const mats=typeof subjects!=='undefined'?subjects:[];
  ['calMateriaSelect','fcEditMateria'].forEach(selId=>{
    const sel=document.getElementById(selId); if(!sel) return;
    const cur=sel.value;
    sel.innerHTML=mats.map(m=>'<option value="'+m.id+'">'+m.name+'</option>').join('');
    if(cur) sel.value=cur;
  });
  const pSel=document.getElementById('provaFiltroMateria');
  if(pSel){ const cur=pSel.value; pSel.innerHTML='<option value="">Todas as matérias</option>'+mats.map(m=>'<option value="'+m.id+'">'+m.name+'</option>').join(''); if(cur) pSel.value=cur; }
}

function getAllCardsFlat(){
  if(typeof fcData==='undefined') return [];
  const cards=[];
  fcData.materias.forEach(m=>m.topicos.forEach(t=>t.cards.forEach(c=>{
    cards.push({...c,materiaId:m.id,materiaNome:m.name,materiaColor:m.color||'#7c5cfc',topicoNome:t.name,topicoId:t.id,
      repeticoes:c.repeticoes||0,intervalo:c.intervalo||1,facilidade:c.facilidade||2.5,
      proximaRev:c.proximaRev||null,historico:c.historico||[],erros:c.erros||0});
  })));
  return cards;
}

function updateCardSM2(mid,tid,cid,fields){
  const m=fcData.materias.find(m=>m.id===mid);
  const t=m&&m.topicos.find(t=>t.id===tid);
  const c=t&&t.cards.find(c=>c.id===cid);
  if(c){ Object.assign(c,fields); fcSaveData(); }
}

function calcSM2(card,resposta){
  let{repeticoes=0,intervalo=1,facilidade=2.5,historico=[],erros=0}=card;
  const hoje=todayLocalStr();
  if(resposta==='errei'){ repeticoes=0;intervalo=1;facilidade=Math.max(1.3,facilidade-0.2);erros+=1; }
  else if(resposta==='dificil'){ repeticoes+=1;intervalo=Math.max(1,Math.ceil(intervalo*1.5));facilidade=Math.max(1.3,facilidade-0.1);erros=Math.max(0,erros-1); }
  else{ repeticoes+=1;intervalo=Math.max(1,Math.ceil(intervalo*facilidade));facilidade+=0.05;erros=0; }
  const pd=addDaysLocal(parseLocalDate(hoje),intervalo);
  const proximaRev=pd.getFullYear()+'-'+String(pd.getMonth()+1).padStart(2,'0')+'-'+String(pd.getDate()).padStart(2,'0');
  historico=[...(historico||[]).slice(-29),{data:hoje,resp:resposta}];
  return{repeticoes,intervalo,facilidade:parseFloat(facilidade.toFixed(3)),proximaRev,historico,erros};
}

// ── HOJE ──
let estudo={fila:[],idx:0,virado:false};

function renderHoje(){
  const hoje=todayLocalStr();
  const lbl=document.getElementById('hojeDataLabel');
  if(lbl) lbl.textContent=parseLocalDate(hoje).toLocaleDateString('pt-BR',{weekday:'long',day:'2-digit',month:'long',year:'numeric'});
  const todos=getAllCardsFlat();
  const atrasados=todos.filter(c=>c.proximaRev&&c.proximaRev<hoje);
  const revisaoHoje=todos.filter(c=>c.proximaRev===hoje);
  const planos=loadPlanejamentos(),planosHoje=planos[hoje]||[],novosIds=new Set();
  planosHoje.forEach(p=>todos.filter(c=>c.materiaId===p.materiaId&&c.topicoNome===p.topico&&!c.proximaRev).forEach(c=>novosIds.add(c.id)));
  const novos=todos.filter(c=>novosIds.has(c.id));
  document.getElementById('cntAtrasados').textContent=atrasados.length;
  document.getElementById('cntRevisao').textContent=revisaoHoje.length;
  document.getElementById('cntNovos').textContent=novos.length;
  function renderChips(list,cid){
    const el=document.getElementById(cid); if(!el) return;
    if(!list.length){el.innerHTML='<span style="font-size:12px;color:var(--text-dim)">Nenhum card.</span>';return;}
    el.innerHTML=list.slice(0,6).map(c=>'<div class="hoje-card-chip" style="border-left-color:'+c.materiaColor+'"><div style="font-weight:600;color:var(--text);font-size:11px;margin-bottom:2px">'+c.materiaNome+'</div><div>'+c.q.substring(0,60)+(c.q.length>60?'…':'')+'</div></div>').join('')+(list.length>6?'<div class="hoje-card-chip" style="border-left-color:var(--text-dim);color:var(--text-dim)">+'+( list.length-6)+' mais</div>':'');
  }
  renderChips(atrasados,'hoje-atrasados-list');
  renderChips(revisaoHoje,'hoje-revisao-list');
  renderChips(novos,'hoje-novos-list');
  document.getElementById('hoje-atrasados-sec').style.display=atrasados.length?'':'none';
  document.getElementById('hoje-revisao-sec').style.display=revisaoHoje.length?'':'none';
  document.getElementById('hoje-novos-sec').style.display=novos.length?'':'none';
  const sess=loadSessoes(),hs=sess[hoje]||{acertos:0,erros:0};
  document.getElementById('hojeAcertosNum').textContent=hs.acertos;
  document.getElementById('hojeErrosNum').textContent=hs.erros;
  const tot=hs.acertos+hs.erros;
  document.getElementById('hojeTaxaNum').textContent=tot?Math.round(hs.acertos/tot*100)+'%':'—';
  document.getElementById('streakNum').textContent=calcStreak();
  estudo.fila=[...atrasados,...revisaoHoje,...novos];estudo.idx=0;estudo.virado=false;
}

function iniciarEstudo(){
  renderHoje();
  if(!estudo.fila.length){showToast('Nenhum card para estudar hoje!','warn');return;}
  estudo.idx=0;estudo.virado=false;
  document.getElementById('modoEstudoOverlay').style.display='flex';
  carregarCard();
}

function fecharEstudo(){
  document.getElementById('modoEstudoOverlay').style.display='none';
  renderHoje();updateStreak();
}

function carregarCard(){
  const card=estudo.fila[estudo.idx];
  if(!card){fecharEstudo();showToast('🎉 Sessão concluída!','ok');return;}
  estudo.virado=false;
  document.getElementById('meCardInner').classList.remove('flipped');
  document.getElementById('meActions').style.display='none';
  document.getElementById('meRevealBtn').style.display='block';
  const tipoMap={lei:'Lei Seca',juris:'Jurisprudência',doutrina:'Doutrina'};
  document.getElementById('meTipoLabel').textContent=tipoMap[card.tipo]||'';
  document.getElementById('meMateriaBadge').textContent=card.materiaNome||'';
  document.getElementById('meMateriaBadge').style.cssText='background:'+card.materiaColor+'22;border:1px solid '+card.materiaColor+'55;color:'+card.materiaColor+';border-radius:100px;padding:3px 10px;font-size:11px;display:inline-flex;margin-bottom:16px;width:fit-content';
  document.getElementById('mePergunta').textContent=card.q;
  document.getElementById('meArtigo').textContent=card.artigo?'📌 '+card.artigo:'';
  document.getElementById('meResposta').textContent=card.a;
  document.getElementById('meObs').textContent=card.obs||'';
  document.getElementById('meObs').style.display=card.obs?'':'none';
  const total=estudo.fila.length,done=estudo.idx;
  document.getElementById('meProgressFill').style.width=Math.round(done/total*100)+'%';
  document.getElementById('meProgressLabel').textContent=done+'/'+total;
}

function virarCard(){
  if(estudo.virado) return;
  estudo.virado=true;
  document.getElementById('meCardInner').classList.add('flipped');
  setTimeout(()=>{document.getElementById('meRevealBtn').style.display='none';document.getElementById('meActions').style.display='flex';},300);
}

function responderCard(resposta){
  const card=estudo.fila[estudo.idx]; if(!card) return;
  updateCardSM2(card.materiaId,card.topicoId,card.id,calcSM2(card,resposta));
  const hoje=todayLocalStr(),sess=loadSessoes();
  if(!sess[hoje]) sess[hoje]={acertos:0,erros:0};
  if(resposta==='errei') sess[hoje].erros++; else sess[hoje].acertos++;
  saveSessoes(sess);
  estudo.idx++;carregarCard();
}

// ── CALENDÁRIO ──
let calAno=new Date().getFullYear(),calMes=new Date().getMonth(),calDiaSel=null;

function mudarMes(delta){
  calMes+=delta;
  if(calMes<0){calMes=11;calAno--;} if(calMes>11){calMes=0;calAno++;}
  renderCalendario();
}

function renderCalendario(){
  const meses=['Janeiro','Fevereiro','Março','Abril','Maio','Junho','Julho','Agosto','Setembro','Outubro','Novembro','Dezembro'];
  document.getElementById('calMesLabel').textContent=meses[calMes]+' '+calAno;
  const grid=document.getElementById('calGrid'),planos=loadPlanejamentos(),hoje=todayLocalStr();
  const firstDay=new Date(calAno,calMes,1);
  let off=firstDay.getDay()-1; if(off<0) off=6;
  const diasMes=new Date(calAno,calMes+1,0).getDate();
  const diasPrev=new Date(calAno,calMes,0).getDate();
  let html='';
  for(let i=off;i>0;i--) html+='<div class="cal-day outro-mes"><div class="cal-num">'+(diasPrev-i+1)+'</div></div>';
  for(let d=1;d<=diasMes;d++){
    const mm=String(calMes+1).padStart(2,'0'),dd=String(d).padStart(2,'0'),key=calAno+'-'+mm+'-'+dd;
    const chips=(planos[key]||[]).map(p=>'<span class="cal-plan-chip" style="background:'+getMateriaColor(p.materiaId)+'">'+getMateriaName(p.materiaId).substring(0,10)+'</span>').join('');
    html+='<div class="cal-day'+(key===hoje?' hoje-dia':'')+'" onclick="abrirCalModal(\''+key+'\')"><div class="cal-num">'+d+'</div>'+chips+'</div>';
  }
  const tot=off+diasMes,trail=tot%7===0?0:7-tot%7;
  for(let i=1;i<=trail;i++) html+='<div class="cal-day outro-mes"><div class="cal-num">'+i+'</div></div>';
  grid.innerHTML=html;
}

function abrirCalModal(key){
  calDiaSel=key;
  document.getElementById('calModalTitle').textContent='📅 '+parseLocalDate(key).toLocaleDateString('pt-BR',{weekday:'long',day:'2-digit',month:'long'});
  const lista=(loadPlanejamentos()[key]||[]);
  document.getElementById('calPlanejamentosExistentes').innerHTML=lista.map((p,i)=>
    '<div class="cal-plan-item" style="border-left-color:'+getMateriaColor(p.materiaId)+'">'+
    '<span style="font-size:12px;font-weight:600;color:var(--text)">'+getMateriaName(p.materiaId)+'</span>'+
    '<span style="font-size:11px;color:var(--text-muted)"> — '+( p.topico||'')+'</span>'+
    (p.obs?'<span style="font-size:10px;color:var(--text-dim)">'+p.obs+'</span>':'')+
    '<button class="cal-plan-del" onclick="deletePlanejamento(\''+key+'\','+i+')">✕</button></div>').join('');
  popularSelectsMaterias();
  document.getElementById('calTopicoInput').value='';
  document.getElementById('calObsInput').value='';
  document.getElementById('calModalOverlay').style.display='flex';
}

function fecharCalModal(e){if(e&&e.target!==document.getElementById('calModalOverlay')) return;document.getElementById('calModalOverlay').style.display='none';}

function salvarPlanejamento(){
  if(!calDiaSel) return;
  const materiaId=document.getElementById('calMateriaSelect').value;
  const topico=document.getElementById('calTopicoInput').value.trim();
  if(!materiaId||!topico){showToast('Preencha matéria e tópico','warn');return;}
  const obs=document.getElementById('calObsInput').value.trim();
  const planos=loadPlanejamentos();
  if(!planos[calDiaSel]) planos[calDiaSel]=[];
  planos[calDiaSel].push({materiaId,topico,obs,ts:Date.now()});
  savePlanejamentos(planos);showToast('✓ Salvo!','ok');
  abrirCalModal(calDiaSel);renderCalendario();
}

function deletePlanejamento(key,idx){
  const planos=loadPlanejamentos();
  if(planos[key]){planos[key].splice(idx,1);savePlanejamentos(planos);}
  abrirCalModal(key);renderCalendario();
}

// ── FLASHCARDS TREE ──
let fcEditingCardId=null;

function fcRenderTree(){
  if(typeof fcData==='undefined') return;
  const tree=document.getElementById('fcTree'); if(!tree) return;
  const busca=(document.getElementById('fcBuscaGlobal')?.value||'').toLowerCase();
  const filtTipo=document.getElementById('fcFiltroTipo')?.value||'';
  if(!fcData.materias.length){tree.innerHTML='<div style="text-align:center;padding:48px;color:var(--text-dim)"><div style="font-size:36px;margin-bottom:12px">🃏</div>Nenhum flashcard. Clique <strong>+ Novo Card</strong>.</div>';return;}
  const tipoMap={lei:'Lei Seca',juris:'Jurisprudência',doutrina:'Doutrina'};
  tree.innerHTML=fcData.materias.map(m=>{
    const color=m.color||'#7c5cfc',totalCards=m.topicos.reduce((s,t)=>s+t.cards.length,0);
    const topicosHtml=m.topicos.map(t=>{
      let cards=t.cards;
      if(busca) cards=cards.filter(c=>c.q.toLowerCase().includes(busca)||c.a.toLowerCase().includes(busca)||t.name.toLowerCase().includes(busca));
      if(filtTipo) cards=cards.filter(c=>c.tipo===filtTipo);
      const cardsHtml=cards.map(c=>{
        const tc=c.tipo==='lei'?'fc-tipo-lei':c.tipo==='juris'?'fc-tipo-juris':'fc-tipo-doutrina';
        const dueStr=c.proximaRev?(c.proximaRev<=todayLocalStr()?'<span style="color:var(--hard);font-size:10px">● Revisar hoje</span>':'<span style="font-size:10px;color:var(--text-dim)">📅 '+c.proximaRev+'</span>'):'<span style="font-size:10px;color:var(--accent)">✨ Novo</span>';
        return '<div class="fc-tree-card"><div class="fc-tree-card-q">'+c.q+'</div><div class="fc-tree-card-a">'+c.a.substring(0,100)+(c.a.length>100?'…':'')+'</div>'+(c.obs?'<div style="font-size:10px;color:var(--text-dim);margin-top:5px;font-style:italic">'+c.obs+'</div>':'')+'<div class="fc-tree-card-meta"><span class="fc-tipo-badge '+tc+'">'+( tipoMap[c.tipo]||c.tipo||'—')+'</span>'+(c.artigo?'<span style="font-size:10px;color:var(--text-dim)">📌 '+c.artigo+'</span>':'')+dueStr+'<div style="margin-left:auto;display:flex;gap:6px"><button class="fc-card-edit-btn" onclick="fcAbrirEditCard(\''+m.id+'\',\''+t.id+'\',\''+c.id+'\')">✏️</button><button class="fc-card-del-btn" onclick="fcDeleteCardUI(\''+m.id+'\',\''+t.id+'\',\''+c.id+'\')">✕</button></div></div></div>';
      }).join('');
      if(!cardsHtml&&(busca||filtTipo)) return '';
      return '<div class="fc-tree-topico" id="fctop-'+t.id+'"><div class="fc-tree-topico-header" onclick="fcToggleTopico(\''+t.id+'\')">'+'<span class="fc-tree-t-arrow">▶</span><span class="fc-tree-t-name">📌 '+t.name+'</span><span class="fc-tree-t-cnt">'+t.cards.length+' cards</span><button style="margin-left:8px;padding:2px 8px;border-radius:4px;border:1px solid var(--border);background:transparent;color:var(--accent);font-size:10px;cursor:pointer" onclick="event.stopPropagation();fcAbrirNovoCardTopico(\''+m.id+'\',\''+t.id+'\',\''+t.name+'\')">+ Card</button></div><div class="fc-tree-cards-list">'+(cardsHtml||'<div style="font-size:12px;color:var(--text-dim);padding:8px">Nenhum card.</div>')+'</div></div>';
    }).join('');
    return '<div class="fc-tree-materia" id="fcmat-'+m.id+'"><div class="fc-tree-materia-header" style="border-left-color:'+color+'" onclick="fcToggleMateria(\''+m.id+'\')"><span class="fc-tree-m-arrow">▶</span><span class="fc-tree-m-name" style="color:'+color+'">'+m.name+'</span><span class="fc-tree-m-cnt">'+totalCards+' cards</span><button style="margin-left:8px;padding:2px 8px;border-radius:4px;border:1px solid '+color+'55;background:transparent;color:'+color+';font-size:10px;cursor:pointer" onclick="event.stopPropagation();fcAbrirNovoCardMateria(\''+m.id+'\')">+ Card</button></div><div class="fc-tree-topicos">'+topicosHtml+'</div></div>';
  }).join('');
  const busEl=document.getElementById('fcBuscaGlobal');
  if(busEl) busEl.oninput=()=>fcRenderTree();
}

function fcToggleMateria(id){document.getElementById('fcmat-'+id)?.classList.toggle('open');}
function fcToggleTopico(id){document.getElementById('fctop-'+id)?.classList.toggle('open');}

function fcAbrirNovoCard(){
  fcEditingCardId=null;
  document.getElementById('fcModalTitle').textContent='🃏 Novo Flashcard';
  popularSelectsMaterias();
  ['fcEditTopico','fcEditArtigo','fcEditObs','fcEditPergunta','fcEditResposta'].forEach(id=>{const e=document.getElementById(id);if(e)e.value='';});
  if(document.getElementById('fcEditTipo')) document.getElementById('fcEditTipo').value='lei';
  document.getElementById('fcModalOverlay').style.display='flex';
}
function fcAbrirNovoCardMateria(mid){fcAbrirNovoCard();document.getElementById('fcEditMateria').value=mid;}
function fcAbrirNovoCardTopico(mid,tid,tname){fcAbrirNovoCard();document.getElementById('fcEditMateria').value=mid;document.getElementById('fcEditTopico').value=tname;}
function fcAbrirEditCard(mid,tid,cid){
  const m=fcData.materias.find(m=>m.id===mid),t=m&&m.topicos.find(t=>t.id===tid),c=t&&t.cards.find(c=>c.id===cid);
  if(!c) return;
  fcEditingCardId=cid;
  document.getElementById('fcModalTitle').textContent='✏️ Editar Flashcard';
  popularSelectsMaterias();
  document.getElementById('fcEditMateria').value=mid;
  document.getElementById('fcEditTopico').value=t.name;
  if(document.getElementById('fcEditTipo')) document.getElementById('fcEditTipo').value=c.tipo||'lei';
  document.getElementById('fcEditArtigo').value=c.artigo||'';
  document.getElementById('fcEditPergunta').value=c.q;
  document.getElementById('fcEditResposta').value=c.a;
  document.getElementById('fcEditObs').value=c.obs||'';
  document.getElementById('fcModalOverlay').dataset.editMid=mid;
  document.getElementById('fcModalOverlay').dataset.editTid=tid;
  document.getElementById('fcModalOverlay').style.display='flex';
}
function fcFecharModal(e){if(e&&e.target!==document.getElementById('fcModalOverlay')) return;document.getElementById('fcModalOverlay').style.display='none';}
function fcSalvarCardModal(){
  const mid=document.getElementById('fcEditMateria').value;
  const tname=document.getElementById('fcEditTopico').value.trim();
  const tipo=document.getElementById('fcEditTipo')?.value||'lei';
  const artigo=document.getElementById('fcEditArtigo').value.trim();
  const q=document.getElementById('fcEditPergunta').value.trim();
  const a=document.getElementById('fcEditResposta').value.trim();
  const obs=document.getElementById('fcEditObs').value.trim();
  if(!mid||!tname||!q||!a){showToast('Preencha matéria, tópico, pergunta e resposta','warn');return;}
  if(fcEditingCardId){
    const em=document.getElementById('fcModalOverlay').dataset.editMid;
    const et=document.getElementById('fcModalOverlay').dataset.editTid;
    const tm=fcData.materias.find(m=>m.id===em),tt=tm&&tm.topicos.find(t=>t.id===et),cc=tt&&tt.cards.find(c=>c.id===fcEditingCardId);
    if(cc) Object.assign(cc,{q,a,tipo,artigo,obs});
    fcSaveData();document.getElementById('fcModalOverlay').style.display='none';fcRenderTree();showToast('✓ Card atualizado!','ok');return;
  }
  const m=fcData.materias.find(m=>m.id===mid); if(!m) return;
  let t=m.topicos.find(t=>t.name.toLowerCase()===tname.toLowerCase());
  if(!t){t={id:fcNewId(),name:tname,cards:[]};m.topicos.push(t);}
  t.cards.push({id:fcNewId(),q,a,tipo,artigo,obs,repeticoes:0,intervalo:1,facilidade:2.5,proximaRev:null,historico:[],erros:0});
  fcSaveData();document.getElementById('fcModalOverlay').style.display='none';fcRenderTree();
  setTimeout(()=>{document.getElementById('fcmat-'+mid)?.classList.add('open');document.getElementById('fctop-'+t.id)?.classList.add('open');},100);
  showToast('✓ Flashcard criado!','ok');
}
function fcDeleteCardUI(mid,tid,cid){
  if(!confirm('Apagar este flashcard?')) return;
  const m=fcData.materias.find(m=>m.id===mid),t=m&&m.topicos.find(t=>t.id===tid);
  if(t) t.cards=t.cards.filter(c=>c.id!==cid);
  fcSaveData();fcRenderTree();
}

// ── ERROS ──
function renderErros(){
  const lista=document.getElementById('errosFrequentesList'); if(!lista) return;
  const cards=getAllCardsFlat().filter(c=>(c.erros||0)>=3).sort((a,b)=>(b.erros||0)-(a.erros||0));
  if(!cards.length){lista.innerHTML='<div style="text-align:center;padding:48px;color:var(--text-dim)"><div style="font-size:36px">🎉</div>Nenhum card com 3+ erros!</div>';return;}
  lista.innerHTML=cards.map(c=>'<div class="erro-card"><div class="erro-card-q">'+c.q+'</div><div style="font-size:12px;color:var(--text-muted);margin:6px 0">'+c.a.substring(0,100)+(c.a.length>100?'…':'')+'</div><div class="erro-card-meta"><span style="color:'+c.materiaColor+';font-weight:600">'+c.materiaNome+'</span><span>📌 '+c.topicoNome+'</span><span class="erro-cnt-badge">'+c.erros+' erros</span></div></div>').join('');
}

function estudarErros(){
  const cards=getAllCardsFlat().filter(c=>(c.erros||0)>=3).sort((a,b)=>(b.erros||0)-(a.erros||0));
  if(!cards.length){showToast('Nenhum card de erro!','warn');return;}
  estudo.fila=cards;estudo.idx=0;estudo.virado=false;
  document.getElementById('modoEstudoOverlay').style.display='flex';
  carregarCard();switchPage('hoje');
}

// ── DESEMPENHO ──
function renderDesempenho(){
  const sess=loadSessoes(),hoje=todayLocalStr(),todos=getAllCardsFlat();
  const hs=sess[hoje]||{acertos:0,erros:0};
  const tot=hs.acertos+hs.erros;
  document.getElementById('kpiHoje').textContent=tot;
  document.getElementById('kpiTaxa').textContent=tot?Math.round(hs.acertos/tot*100)+'%':'—';
  document.getElementById('kpiStreak').textContent=calcStreak()+'🔥';
  document.getElementById('kpiTotal').textContent=todos.length;
  document.getElementById('kpiVencer').textContent=todos.filter(c=>!c.proximaRev||c.proximaRev<=hoje).length;
  let sem=0;
  for(let i=0;i<7;i++){const d=addDaysLocal(parseLocalDate(hoje),-i);const k=d.getFullYear()+'-'+String(d.getMonth()+1).padStart(2,'0')+'-'+String(d.getDate()).padStart(2,'0');const s=sess[k]||{acertos:0,erros:0};sem+=s.acertos+s.erros;}
  document.getElementById('kpiSemana').textContent=sem;
  const desEl=document.getElementById('desempMaterias'); if(!desEl) return;
  const mats=typeof fcData!=='undefined'?fcData.materias:[];
  desEl.innerHTML=mats.length?mats.map(m=>{
    const mc=m.topicos.flatMap(t=>t.cards);
    const ac=mc.reduce((s,c)=>s+(c.historico||[]).filter(h=>h.resp!=='errei').length,0);
    const tl=mc.reduce((s,c)=>s+(c.historico||[]).length,0);
    const pct=tl?Math.round(ac/tl*100):0;
    const cor=pct>=80?'var(--easy)':pct>=50?'var(--accent4)':'var(--hard)';
    return '<div class="desempenho-materia-row"><div class="desmrow-name" style="color:'+(m.color||'#7c5cfc')+'">'+m.name+'</div><div class="desmrow-bar-wrap"><div class="desmrow-bar" style="width:'+pct+'%;background:'+cor+'"></div></div><div class="desmrow-pct" style="color:'+cor+'">'+pct+'%</div><div style="font-size:10px;color:var(--text-dim);margin-left:8px">'+tl+' rev.</div></div>';
  }).join(''):'<p style="color:var(--text-dim)">Nenhuma matéria.</p>';
  const graf=document.getElementById('grafico7dias'); if(!graf) return;
  const labs=[],vals=[];
  for(let i=6;i>=0;i--){const d=addDaysLocal(parseLocalDate(hoje),-i);const k=d.getFullYear()+'-'+String(d.getMonth()+1).padStart(2,'0')+'-'+String(d.getDate()).padStart(2,'0');const s=sess[k]||{acertos:0,erros:0};labs.push(d.toLocaleDateString('pt-BR',{weekday:'short'}));vals.push(s.acertos+s.erros);}
  const mx=Math.max(...vals,1);
  graf.innerHTML=vals.map((v,i)=>'<div class="graf-bar" style="height:'+( Math.round(v/mx*80)+8)+'%;background:'+(v>0?'var(--accent)':'var(--border)')+';position:relative">'+(v>0?'<span class="graf-bar-val">'+v+'</span>':'')+'<span class="graf-bar-lbl">'+labs[i]+'</span></div>').join('');
}

function calcStreak(){
  const sess=loadSessoes();let streak=0;let d=parseLocalDate(todayLocalStr());
  while(true){const k=d.getFullYear()+'-'+String(d.getMonth()+1).padStart(2,'0')+'-'+String(d.getDate()).padStart(2,'0');const s=sess[k];if(!s||(s.acertos+s.erros)===0) break;streak++;d=addDaysLocal(d,-1);}
  return streak;
}
function updateStreak(){const el=document.getElementById('streakNum');if(el)el.textContent=calcStreak();}

// ── MODO PROVA ──
function renderProva(){
  popularSelectsMaterias();
  const todos=getAllCardsFlat();
  document.getElementById('provaCntErros').textContent=todos.filter(c=>(c.erros||0)>=3).length+' cards';
  document.getElementById('provaCntRevisados').textContent=Math.min(50,todos.length)+' cards';
  document.getElementById('provaCntTodos').textContent=todos.length+' cards';
}

function iniciarModoProva(tipo){
  const fil=document.getElementById('provaFiltroMateria')?.value||'';
  let todos=getAllCardsFlat();
  if(fil) todos=todos.filter(c=>c.materiaId===fil);
  let fila;
  if(tipo==='erros') fila=todos.filter(c=>(c.erros||0)>=3).sort((a,b)=>(b.erros||0)-(a.erros||0));
  else if(tipo==='revisados') fila=[...todos].sort((a,b)=>(b.repeticoes||0)-(a.repeticoes||0)).slice(0,50);
  else fila=todos.sort(()=>Math.random()-.5);
  if(!fila.length){showToast('Nenhum card!','warn');return;}
  estudo.fila=fila;estudo.idx=0;estudo.virado=false;
  document.getElementById('modoEstudoOverlay').style.display='flex';
  carregarCard();switchPage('hoje');
}

// ── Init ──
setTimeout(()=>{
  popularSelectsMaterias();
  renderHoje();
  calAno=new Date().getFullYear();calMes=new Date().getMonth();
},400);

</script>
</body>
</html>
