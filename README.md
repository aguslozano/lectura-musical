[index.html.html](https://github.com/user-attachments/files/26326877/index.html.html)[Uploading index.html<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Lectura Musical</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js"></script>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Libre+Baskerville:ital,wght@0,400;0,700;1,400&family=Space+Mono:wght@400;700&display=swap');

  :root {
    --ink: #1a1208;
    --paper: #f5eedc;
    --paper2: #ede3c8;
    --accent: #8b2e0a;
    --accent2: #2e5c8b;
    --line: #c8b98a;
    --correct: #2e6b3e;
    --wrong: #8b1a1a;
    --staff-line: #4a3c22;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--paper);
    color: var(--ink);
    font-family: 'Libre Baskerville', serif;
    min-height: 100vh;
    padding: 0;
    background-image:
      repeating-linear-gradient(0deg, transparent, transparent 27px, rgba(180,160,100,0.15) 27px, rgba(180,160,100,0.15) 28px),
      radial-gradient(ellipse at 20% 0%, rgba(200,160,80,0.12) 0%, transparent 60%),
      radial-gradient(ellipse at 80% 100%, rgba(160,120,60,0.1) 0%, transparent 50%);
  }

  header {
    border-bottom: 2px solid var(--ink);
    padding: 1.4rem 2rem 1rem;
    display: flex;
    align-items: baseline;
    gap: 1.2rem;
    background: var(--paper2);
    box-shadow: 0 2px 12px rgba(0,0,0,0.08);
  }

  header h1 {
    font-size: 1.7rem;
    letter-spacing: -0.02em;
    font-style: italic;
    color: var(--accent);
  }

  header span {
    font-family: 'Space Mono', monospace;
    font-size: 0.72rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    opacity: 0.55;
    padding-top: 0.2rem;
  }

  .score-bar {
    margin-left: auto;
    display: flex;
    gap: 1.5rem;
    align-items: center;
    font-family: 'Space Mono', monospace;
    font-size: 0.85rem;
  }

  .score-bar .stat { display: flex; flex-direction: column; align-items: center; }
  .score-bar .val { font-size: 1.3rem; font-weight: 700; line-height: 1; }
  .score-bar .lbl { font-size: 0.6rem; opacity: 0.5; letter-spacing: 0.1em; text-transform: uppercase; }
  .score-bar .val.green { color: var(--correct); }
  .score-bar .val.red { color: var(--wrong); }

  main {
    max-width: 700px;
    margin: 0 auto;
    padding: 2rem 1.5rem 4rem;
  }

  .mode-tabs {
    display: flex;
    gap: 0;
    border: 1.5px solid var(--ink);
    border-radius: 2px;
    overflow: hidden;
    margin-bottom: 2rem;
    font-family: 'Space Mono', monospace;
    font-size: 0.62rem;
    letter-spacing: 0.03em;
    text-transform: uppercase;
    background: white;
  }

  .mode-tabs button {
    flex: 1;
    padding: 0.6rem 0.5rem;
    background: none;
    border: none;
    border-right: 1.5px solid var(--ink);
    cursor: pointer;
    color: var(--ink);
    opacity: 1;
    transition: all 0.15s;
    font-family: inherit;
    font-size: inherit;
    letter-spacing: inherit;
    font-weight: 700;
  }

  .mode-tabs button:last-child { border-right: none; }
  .mode-tabs button.active {
    background: var(--ink);
    color: var(--paper);
    opacity: 1;
  }
  .mode-tabs button:hover:not(.active) { opacity: 0.75; background: #f0f0f0; }

  .exercise-card {
    background: white;
    border: 1.5px solid var(--ink);
    border-radius: 2px;
    padding: 2rem 1.8rem 1.8rem;
    box-shadow: 4px 4px 0 var(--ink);
    margin-bottom: 1.5rem;
    position: relative;
  }

  .instruction {
    font-size: 0.78rem;
    font-family: 'Space Mono', monospace;
    text-transform: uppercase;
    letter-spacing: 0.12em;
    opacity: 1;
    margin-bottom: 1.2rem;
  }

  /* SVG staff */
  .staff-container {
    display: flex;
    justify-content: center;
    margin: 1rem 0 1.5rem;
  }

  svg.staff { overflow: visible; }

  .options-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 0.6rem;
  }

  .opt-btn {
    padding: 0.7rem 0.4rem;
    border: 1.5px solid var(--ink);
    background: none;
    cursor: pointer;
    font-family: 'Libre Baskerville', serif;
    font-size: 1rem;
    color: var(--ink);
    border-radius: 2px;
    transition: all 0.12s;
    position: relative;
  }

  .opt-btn:hover:not(:disabled) {
    background: var(--paper2);
    transform: translateY(-1px);
    box-shadow: 2px 2px 0 var(--ink);
  }

  .opt-btn.correct {
    background: var(--correct);
    border-color: var(--correct);
    color: white;
    box-shadow: 2px 2px 0 #1a4025;
  }

  .opt-btn.wrong {
    background: var(--wrong);
    border-color: var(--wrong);
    color: white;
  }

  .opt-btn:disabled { cursor: default; }

  .feedback {
    text-align: center;
    font-size: 0.9rem;
    min-height: 1.4rem;
    margin-top: 1rem;
    font-style: italic;
    transition: opacity 0.2s;
  }
  .feedback.ok { color: var(--correct); font-weight: 700; }
  .feedback.fail { color: var(--wrong); }

  .next-btn {
    display: block;
    margin: 1.2rem auto 0;
    padding: 0.65rem 2.5rem;
    background: var(--ink);
    color: var(--paper);
    border: none;
    cursor: pointer;
    font-family: 'Space Mono', monospace;
    font-size: 0.78rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    border-radius: 2px;
    box-shadow: 3px 3px 0 var(--accent);
    transition: all 0.12s;
  }
  .next-btn:hover { transform: translate(-1px,-1px); box-shadow: 4px 4px 0 var(--accent); }
  .next-btn:active { transform: translate(1px,1px); box-shadow: 2px 2px 0 var(--accent); }

  /* Ledger-line exercise */
  .interval-display {
    display: flex;
    gap: 1.5rem;
    justify-content: center;
    align-items: flex-end;
    margin: 1rem 0 1.5rem;
  }

  /* Rhythm mode */
  .rhythm-display {
    font-size: 3.5rem;
    text-align: center;
    letter-spacing: 0.3em;
    margin: 0.5rem 0 1.5rem;
    padding: 1rem;
    border-bottom: 1px solid var(--line);
    line-height: 1;
    min-height: 5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.3em;
  }

  .progress-bar-wrap {
    height: 3px;
    background: var(--line);
    border-radius: 2px;
    overflow: hidden;
    margin-bottom: 0.4rem;
  }
  .progress-bar-fill {
    height: 100%;
    background: var(--accent);
    transition: width 0.3s;
    border-radius: 2px;
  }
  .progress-label {
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    opacity: 0.4;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 1rem;
  }

  .play-btn {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.4rem;
    margin: 0 auto 1rem;
    padding: 0.5rem 1.4rem;
    background: var(--accent2);
    color: white;
    border: none;
    border-radius: 2px;
    cursor: pointer;
    font-family: 'Space Mono', monospace;
    font-size: 0.72rem;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    box-shadow: 2px 2px 0 #1a3a5c;
    transition: all 0.12s;
  }
  .play-btn:hover { transform: translate(-1px,-1px); box-shadow: 3px 3px 0 #1a3a5c; }
  .play-btn:active { transform: translate(1px,1px); box-shadow: 1px 1px 0 #1a3a5c; }

  .clef-badge {
    position: absolute;
    top: 0.9rem;
    right: 1rem;
    font-family: 'Space Mono', monospace;
    font-size: 0.6rem;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    opacity: 0.3;
  }
</style>
</head>
<body>

<header>
  <h1>Lectura Musical</h1>
  <span>Ejercicios / Solfeo</span>
  <div class="score-bar">
    <div class="stat"><div class="val green" id="scoreOk">0</div><div class="lbl">Correctas</div></div>
    <div class="stat"><div class="val red" id="scoreErr">0</div><div class="lbl">Errores</div></div>
    <div class="stat"><div class="val" id="scoreStreak">0</div><div class="lbl">Racha</div></div>
  </div>
</header>

<main>
  <div class="mode-tabs">
    <button class="active" onclick="setMode('notas')">Notas</button>
    <button onclick="setMode('alteraciones')">Alteraciones</button>
    <button onclick="setMode('figuras')">Figuras</button>
    <button onclick="setMode('intervalos')">Intervalos</button>
  </div>

  <div id="exerciseArea"></div>
</main>

<script>
// ─── STATE ───────────────────────────────────────────────────────────────────
let mode = 'notas';
let ok = 0, err = 0, streak = 0;
let answered = false;
let currentAnswer = '';
let questionCount = 0;

// ─── NOTES DATA ──────────────────────────────────────────────────────────────
// Positions on treble clef: y offset from middle line (B4), each step = 7.5px
// Lines (bottom to top): E4, G4, B4, D5, F5
// Spaces: F4, A4, C5, E5
// We'll represent positions as semitone steps above C4 for reference
// Staff y-coordinate: pos 0 = middle line B4 = y=50; each step up = -7.5

const TREBLE_NOTES = [
  // ledger line below
  { name:'Do', octave:4, staffPos:-2, label:'Do₄' },
  { name:'Re', octave:4, staffPos:-1, label:'Re₄' },
  // normal range
  { name:'Mi', octave:4, staffPos:0,  label:'Mi₄' },
  { name:'Fa', octave:4, staffPos:1,  label:'Fa₄' },
  { name:'Sol', octave:4, staffPos:2, label:'Sol₄' },
  { name:'La', octave:4, staffPos:3,  label:'La₄' },
  { name:'Si', octave:4, staffPos:4,  label:'Si₄' },
  { name:'Do', octave:5, staffPos:5,  label:'Do₅' },
  { name:'Re', octave:5, staffPos:6,  label:'Re₅' },
  { name:'Mi', octave:5, staffPos:7,  label:'Mi₅' },
  { name:'Fa', octave:5, staffPos:8,  label:'Fa₅' },
  { name:'Sol', octave:5, staffPos:9, label:'Sol₅' },
  { name:'La', octave:5, staffPos:10, label:'La₅' },
  { name:'Si', octave:5, staffPos:11, label:'Si₅' },
  { name:'Do', octave:6, staffPos:12, label:'Do₆' },
];

// staffPos: 0=E4(first line), 1=F4(first space), 2=G4, ...
// y = staffTop + (4 - (pos - 0)) * step... let me recalculate cleanly
// 5 lines: y= 20,30,40,50,60 (top to bottom = F5,D5,B4,G4,E4)
// pos 0 = E4 = y=60, pos 1=F4=y=55... step per note = 5px
// Actually let's do step = 6.5px, top line y=18

const STAFF_TOP = 36;   // pushed down to leave room for ledger lines above
const NOTE_STEP = 6.5;  // px per diatonic step
// line positions: E4=pos0 y=60, G4=pos2 y=47, B4=pos4 y=34, D5=pos6 y=21, F5=pos8 y=8
// Let me fix: top line F5 at y=18, each step down = +6.5
// F5 = diatonic pos 8 (0=E4), D5=6, B4=4, G4=2, E4=0
// y(pos) = 18 + (8-pos)*6.5

function noteY(staffPos) {
  return STAFF_TOP + (8 - staffPos) * NOTE_STEP;
}

const ALTERATION_NOTES = [
  { label:'Fa♯₄',  pitch:'Fa♯', pos:1,  acc:'#' },
  { label:'Do♯₄',  pitch:'Do♯', pos:-2, acc:'#' },
  { label:'Sol♯₄', pitch:'Sol♯',pos:2,  acc:'#' },
  { label:'Re♯₄',  pitch:'Re♯', pos:-1, acc:'#' },
  { label:'Si♭₄',  pitch:'Si♭', pos:4,  acc:'b' },
  { label:'Mi♭₄',  pitch:'Mi♭', pos:0,  acc:'b' },
  { label:'La♭₄',  pitch:'La♭', pos:3,  acc:'b' },
  { label:'Fa♯₅',  pitch:'Fa♯', pos:8,  acc:'#' },
  { label:'Si♭₄',  pitch:'Si♭', pos:4,  acc:'b' },
];

const RHYTHM_FIGURES = [
  { svg: '𝅝', name: 'Redonda', value: 4, html: '&#119133;' },
  { svg: '𝅗𝅥', name: 'Blanca', value: 2, html: '&#119134;' },
  { svg: '♩', name: 'Negra', value: 1, html: '&#9833;' },
  { svg: '♪', name: 'Corchea', value: 0.5, html: '&#9834;' },
  { svg: '𝅘𝅥𝅮', name: 'Semicorchea', value: 0.25, html: '&#119136;' },
];

const INTERVAL_NAMES = ['Unísono','Segunda','Tercera','Cuarta','Quinta','Sexta','Séptima','Octava'];

// ─── DRAW STAFF ──────────────────────────────────────────────────────────────
function drawStaff(svgEl, notePos, accidental = null, showLedger = true) {
  const W = 220, H = 130;
  svgEl.setAttribute('viewBox', `0 0 ${W} ${H}`);
  svgEl.setAttribute('width', W);
  svgEl.setAttribute('height', H);
  svgEl.innerHTML = '';

  const lineColor = '#4a3c22';
  const noteR = 5.5;
  const noteX = 130;

  // Draw 5 staff lines
  for (let i = 0; i < 5; i++) {
    const y = STAFF_TOP + i * NOTE_STEP * 2;
    const line = document.createElementNS('http://www.w3.org/2000/svg','line');
    line.setAttribute('x1', 20); line.setAttribute('x2', W - 20);
    line.setAttribute('y1', y); line.setAttribute('y2', y);
    line.setAttribute('stroke', lineColor);
    line.setAttribute('stroke-width', '1.2');
    svgEl.appendChild(line);
  }

  // Treble clef (simplified using text)
  const clef = document.createElementNS('http://www.w3.org/2000/svg','text');
  clef.setAttribute('x', 25);
  clef.setAttribute('y', STAFF_TOP + NOTE_STEP * 6 + 4);
  clef.setAttribute('font-size', '52');
  clef.setAttribute('fill', lineColor);
  clef.setAttribute('font-family', 'serif');
  clef.textContent = '𝄞';
  svgEl.appendChild(clef);

  // Ledger lines if needed
  const y = noteY(notePos);
  if (showLedger) {
    // below staff: notePos < 0
    // Lines are on even positions: -2 (Do4)
    if (notePos <= -2) {
      for (let lp = -2; lp >= notePos; lp -= 2) {
        const ly = noteY(lp);
        const ll = document.createElementNS('http://www.w3.org/2000/svg','line');
        ll.setAttribute('x1', noteX - 11); ll.setAttribute('x2', noteX + 11);
        ll.setAttribute('y1', ly); ll.setAttribute('y2', ly);
        ll.setAttribute('stroke', lineColor); ll.setAttribute('stroke-width', '1.2');
        svgEl.appendChild(ll);
      }
    }
    // above staff: notePos > 8 (Fa5=8 is top line)
    // Sol5=9 (space, no ledger), La5=10 (ledger line), Si5=11 (space on ledger), Do6=12 (ledger line)
    if (notePos >= 10) {
      for (let lp = 10; lp <= notePos; lp += 2) {
        const ly = noteY(lp);
        const ll = document.createElementNS('http://www.w3.org/2000/svg','line');
        ll.setAttribute('x1', noteX - 11); ll.setAttribute('x2', noteX + 11);
        ll.setAttribute('y1', ly); ll.setAttribute('y2', ly);
        ll.setAttribute('stroke', lineColor); ll.setAttribute('stroke-width', '1.2');
        svgEl.appendChild(ll);
      }
    }
  }

  // Accidental
  if (accidental) {
    const accText = document.createElementNS('http://www.w3.org/2000/svg','text');
    accText.setAttribute('x', noteX - 14);
    accText.setAttribute('y', y + 4);
    accText.setAttribute('font-size', accidental === '#' ? '13' : '16');
    accText.setAttribute('fill', '#8b2e0a');
    accText.setAttribute('font-family', 'serif');
    accText.setAttribute('text-anchor', 'middle');
    accText.textContent = accidental === '#' ? '♯' : '♭';
    svgEl.appendChild(accText);
  }

  // Notehead (filled)
  const ellipse = document.createElementNS('http://www.w3.org/2000/svg','ellipse');
  ellipse.setAttribute('cx', noteX);
  ellipse.setAttribute('cy', y);
  ellipse.setAttribute('rx', noteR);
  ellipse.setAttribute('ry', noteR * 0.72);
  ellipse.setAttribute('fill', '#1a1208');
  ellipse.setAttribute('transform', `rotate(-15, ${noteX}, ${y})`);
  svgEl.appendChild(ellipse);

  // Stem
  const stemDir = notePos < 4 ? -1 : 1; // up if below middle, down if above
  const stemX = stemDir === -1 ? noteX + noteR - 0.5 : noteX - noteR + 0.5;
  const stemY1 = y;
  const stemY2 = y + stemDir * 30;
  const stem = document.createElementNS('http://www.w3.org/2000/svg','line');
  stem.setAttribute('x1', stemX); stem.setAttribute('x2', stemX);
  stem.setAttribute('y1', stemY1); stem.setAttribute('y2', stemY2);
  stem.setAttribute('stroke', '#1a1208'); stem.setAttribute('stroke-width', '1.3');
  svgEl.appendChild(stem);

  return svgEl;
}

// ─── SHUFFLE / PICK ───────────────────────────────────────────────────────────
function pick(arr, n) {
  const shuffled = [...arr].sort(() => Math.random() - 0.5);
  return shuffled.slice(0, n);
}

function pickExcluding(arr, exclude, n) {
  const others = arr.filter(x => x !== exclude).sort(() => Math.random() - 0.5);
  return others.slice(0, n);
}

function drawTwoNotes(svgEl, pos1, pos2) {
  const W = 300, H = 130;
  svgEl.setAttribute('viewBox', `0 0 ${W} ${H}`);
  svgEl.setAttribute('width', W);
  svgEl.setAttribute('height', H);
  svgEl.innerHTML = '';

  const lineColor = '#4a3c22';
  const noteR = 5.5;
  const note1X = 140, note2X = 220;

  // 5 staff lines
  for (let i = 0; i < 5; i++) {
    const y = STAFF_TOP + i * NOTE_STEP * 2;
    const line = document.createElementNS('http://www.w3.org/2000/svg','line');
    line.setAttribute('x1', 20); line.setAttribute('x2', W - 10);
    line.setAttribute('y1', y); line.setAttribute('y2', y);
    line.setAttribute('stroke', lineColor); line.setAttribute('stroke-width', '1.2');
    svgEl.appendChild(line);
  }

  // Treble clef
  const clef = document.createElementNS('http://www.w3.org/2000/svg','text');
  clef.setAttribute('x', 25);
  clef.setAttribute('y', STAFF_TOP + NOTE_STEP * 6 + 4);
  clef.setAttribute('font-size', '52');
  clef.setAttribute('fill', lineColor);
  clef.setAttribute('font-family', 'serif');
  clef.textContent = '𝄞';
  svgEl.appendChild(clef);

  // Draw each note
  [[pos1, note1X], [pos2, note2X]].forEach(([pos, nx]) => {
    const y = noteY(pos);

    // Ledger lines below
    if (pos <= -1) {
      for (let lp = -2; lp <= pos; lp += 2) {
        const ly = noteY(lp);
        const ll = document.createElementNS('http://www.w3.org/2000/svg','line');
        ll.setAttribute('x1', nx-11); ll.setAttribute('x2', nx+11);
        ll.setAttribute('y1', ly); ll.setAttribute('y2', ly);
        ll.setAttribute('stroke', lineColor); ll.setAttribute('stroke-width', '1.2');
        svgEl.appendChild(ll);
      }
    }
    if (pos === -2) {
      const ll = document.createElementNS('http://www.w3.org/2000/svg','line');
      ll.setAttribute('x1', nx-11); ll.setAttribute('x2', nx+11);
      ll.setAttribute('y1', noteY(-2)); ll.setAttribute('y2', noteY(-2));
      ll.setAttribute('stroke', lineColor); ll.setAttribute('stroke-width', '1.2');
      svgEl.appendChild(ll);
    }

    // Notehead
    const ellipse = document.createElementNS('http://www.w3.org/2000/svg','ellipse');
    ellipse.setAttribute('cx', nx); ellipse.setAttribute('cy', y);
    ellipse.setAttribute('rx', noteR); ellipse.setAttribute('ry', noteR * 0.72);
    ellipse.setAttribute('fill', '#1a1208');
    ellipse.setAttribute('transform', `rotate(-15, ${nx}, ${y})`);
    svgEl.appendChild(ellipse);

    // Stem
    const stemDir = pos < 4 ? -1 : 1;
    const stemX = stemDir === -1 ? nx + noteR - 0.5 : nx - noteR + 0.5;
    const stem = document.createElementNS('http://www.w3.org/2000/svg','line');
    stem.setAttribute('x1', stemX); stem.setAttribute('x2', stemX);
    stem.setAttribute('y1', y); stem.setAttribute('y2', y + stemDir * 30);
    stem.setAttribute('stroke', '#1a1208'); stem.setAttribute('stroke-width', '1.3');
    svgEl.appendChild(stem);
  });
}


function setMode(m) {
  mode = m;
  document.querySelectorAll('.mode-tabs button').forEach((b,i) => {
    b.classList.toggle('active', ['notas','alteraciones','figuras','intervalos'][i] === m);
  });
  ok=0; err=0; streak=0; questionCount=0;
  updateScore();
  render();
}

function updateScore() {
  document.getElementById('scoreOk').textContent = ok;
  document.getElementById('scoreErr').textContent = err;
  document.getElementById('scoreStreak').textContent = streak;
}

// ─── RENDER ───────────────────────────────────────────────────────────────────
function render() {
  if (mode === 'notas') renderNotas();
  else if (mode === 'alteraciones') renderAlteraciones();
  else if (mode === 'figuras') renderFiguras();
  else renderIntervalos();
}

function renderNotas() {
  questionCount++;
  answered = false;
  const pool = questionCount <= 8 ? TREBLE_NOTES.filter(n => n.staffPos >= 0 && n.staffPos <= 8) : TREBLE_NOTES;
  const idx = Math.floor(Math.random() * pool.length);
  const note = pool[idx];
  currentAnswer = note.label;

  // Pick 3 closest notes by index distance as distractors
  const others = pool
    .map((n, i) => ({ n, dist: Math.abs(i - idx) }))
    .filter(x => x.n.label !== note.label)
    .sort((a, b) => a.dist - b.dist);

  const wrongs = others.slice(0, 3).map(x => x.n);
  const options = [note, ...wrongs].sort(() => Math.random() - 0.5);
  const progress = Math.min(questionCount / pool.length, 1);

  initSampler();
  document.getElementById('exerciseArea').innerHTML = `
    <div class="exercise-card">
      <div class="clef-badge">Clave de Sol</div>
      <div class="instruction">¿Qué nota es esta?</div>
      <div class="progress-bar-wrap"><div class="progress-bar-fill" style="width:${progress*100}%"></div></div>
      <div class="progress-label">Pregunta ${questionCount}</div>
      <div class="staff-container">
        <svg class="staff" id="staffSvg"></svg>
      </div>
      <button class="play-btn" onclick="playNote('${note.label}')">▶ Escuchar</button>
      <div class="options-grid">
        ${options.map((o, i) => `<button class="opt-btn" data-label="${o.label}" data-correct="${note.label}" onclick="checkAnswerBtn(this)">${o.label}</button>`).join('')}
      </div>
      <div class="feedback" id="feedback"></div>
    </div>
  `;
  drawStaff(document.getElementById('staffSvg'), note.staffPos);
}

function renderAlteraciones() {
  questionCount++;
  answered = false;
  const note = ALTERATION_NOTES[Math.floor(Math.random() * ALTERATION_NOTES.length)];
  currentAnswer = note.pitch;

  const allPitches = [...new Set(ALTERATION_NOTES.map(n=>n.pitch))];
  const wrongs = pick(allPitches.filter(p => p !== note.pitch), 3);
  const options = [note.pitch, ...wrongs].sort(() => Math.random() - 0.5);

  document.getElementById('exerciseArea').innerHTML = `
    <div class="exercise-card">
      <div class="clef-badge">Clave de Sol</div>
      <div class="instruction">Nota con alteración — ¿cuál es?</div>
      <div class="progress-bar-wrap"><div class="progress-bar-fill" style="width:${Math.min(questionCount/12,1)*100}%"></div></div>
      <div class="staff-container">
        <svg class="staff" id="staffSvg"></svg>
      </div>
      <div class="options-grid">
        ${options.map(o => `<button class="opt-btn" onclick="checkAnswer(this,'${o}','${note.pitch}')">${o}</button>`).join('')}
      </div>
      <div class="feedback" id="feedback"></div>
    </div>
  `;
  drawStaff(document.getElementById('staffSvg'), note.pos, note.acc);
}

function renderFiguras() {
  questionCount++;
  answered = false;
  // Show figure name, ask for duration in beats
  const fig = RHYTHM_FIGURES[Math.floor(Math.random() * RHYTHM_FIGURES.length)];
  currentAnswer = fig.name;

  const wrongs = pick(RHYTHM_FIGURES.filter(f => f.name !== fig.name), 3);
  const options = [fig, ...wrongs].sort(() => Math.random() - 0.5);

  const beats = {4:'4 tiempos', 2:'2 tiempos', 1:'1 tiempo', 0.5:'½ tiempo', 0.25:'¼ tiempo'};

  document.getElementById('exerciseArea').innerHTML = `
    <div class="exercise-card">
      <div class="instruction">¿Cómo se llama esta figura rítmica?</div>
      <div class="progress-bar-wrap"><div class="progress-bar-fill" style="width:${Math.min(questionCount/10,1)*100}%"></div></div>
      <div class="rhythm-display">${fig.html}</div>
      <div style="text-align:center;font-size:0.78rem;opacity:0.45;margin:-1rem 0 1.2rem;font-family:'Space Mono',monospace;">= ${beats[fig.value]}</div>
      <div class="options-grid" style="width:100%;box-sizing:border-box;">
        ${options.map(o => `<button class="opt-btn" onclick="checkAnswer(this,'${o.name}','${fig.name}')">${o.name}</button>`).join('')}
      </div>
      <div class="feedback" id="feedback"></div>
    </div>
  `;
}

function renderIntervalos() {
  questionCount++;
  answered = false;
  // Pick two notes and ask the interval
  const notes = TREBLE_NOTES.filter(n => n.staffPos >= 0 && n.staffPos <= 8);
  const n1 = notes[Math.floor(Math.random() * notes.length)];
  let n2;
  do { n2 = notes[Math.floor(Math.random() * notes.length)]; } while(n2 === n1);

  const diff = Math.abs(n1.staffPos - n2.staffPos) + 1;
  const intervalIdx = Math.min(diff - 1, 7);
  currentAnswer = INTERVAL_NAMES[intervalIdx];

  const wrongs = pick(INTERVAL_NAMES.filter(n => n !== currentAnswer), 3);
  const options = [currentAnswer, ...wrongs].sort(() => Math.random() - 0.5);

  const lo = n1.staffPos < n2.staffPos ? n1 : n2;
  const hi = n1.staffPos < n2.staffPos ? n2 : n1;

  document.getElementById('exerciseArea').innerHTML = `
    <div class="exercise-card">
      <div class="clef-badge">Intervalo</div>
      <div class="instruction">¿Qué intervalo forman estas dos notas?</div>
      <div class="progress-bar-wrap"><div class="progress-bar-fill" style="width:${Math.min(questionCount/10,1)*100}%"></div></div>
      <div class="staff-container">
        <svg class="staff" id="staffSvg1" style="overflow:hidden;"></svg>
      </div>
      <div style="text-align:center;font-size:0.8rem;opacity:0.5;margin:-0.5rem 0 1rem;font-family:'Space Mono',monospace">${lo.label} → ${hi.label}</div>
      <div class="options-grid">
        ${options.map(o => `<button class="opt-btn" onclick="checkAnswer(this,'${o}','${currentAnswer}')">${o}</button>`).join('')}
      </div>
      <div class="feedback" id="feedback"></div>
    </div>
  `;
  drawTwoNotes(document.getElementById('staffSvg1'), lo.staffPos, hi.staffPos);
}

// ─── CHECK ANSWER YES/NO (notas mode) ────────────────────────────────────────
function checkAnswerYN(btn, given) {
  if (answered) return;
  answered = true;
  const allBtns = document.querySelectorAll('.opt-btn');
  allBtns.forEach(b => b.disabled = true);
  const fb = document.getElementById('feedback');

  if (given === currentAnswer) {
    btn.classList.add('correct');
    ok++; streak++;
    const msgs = ['¡Exacto!', '¡Muy bien!', '¡Correcto!', '¡Perfecto!'];
    fb.textContent = streak >= 3 ? `¡Racha de ${streak}! 🔥` : msgs[Math.floor(Math.random()*msgs.length)];
    if (currentAnswer === 'no') fb.textContent += ` Es ${window._notaCorrecta}`;
    fb.className = 'feedback ok';
  } else {
    btn.classList.add('wrong');
    err++; streak = 0;
    // highlight correct button
    allBtns.forEach(b => {
      if ((currentAnswer === 'si' && b.textContent === 'Sí') ||
          (currentAnswer === 'no' && b.textContent === 'No')) {
        b.classList.add('correct');
      }
    });
    fb.textContent = currentAnswer === 'si'
      ? `Era sí — es ${window._notaCorrecta}`
      : `Era no — es ${window._notaCorrecta}`;
    fb.className = 'feedback fail';
  }

  updateScore();
  const card = document.querySelector('.exercise-card');
  const nb = document.createElement('button');
  nb.className = 'next-btn';
  nb.textContent = 'Siguiente →';
  nb.onclick = render;
  card.appendChild(nb);
}

function checkAnswerBtn(btn) {
  checkAnswer(btn, btn.dataset.label, btn.dataset.correct);
}

// ─── CHECK ANSWER ─────────────────────────────────────────────────────────────
function checkAnswer(btn, given, correct) {
  if (answered) return;
  answered = true;

  const allBtns = document.querySelectorAll('.opt-btn');
  allBtns.forEach(b => b.disabled = true);

  const fb = document.getElementById('feedback');

  if (given === correct) {
    btn.classList.add('correct');
    ok++; streak++;
    fb.textContent = 'Sí';
    fb.className = 'feedback ok';
    // play the correct note if we know its pitch
    if (NOTE_TO_PITCH[correct]) playNote(correct);
  } else {
    btn.classList.add('wrong');
    err++; streak = 0;
    allBtns.forEach(b => {
      if (b.dataset.label === correct || b.dataset.correct === correct && b.dataset.label === correct) {
        b.classList.add('correct');
      }
    });
    fb.textContent = 'No';
    fb.className = 'feedback fail';
  }

  updateScore();

  // Add next button
  const card = document.querySelector('.exercise-card');
  const nb = document.createElement('button');
  nb.className = 'next-btn';
  nb.textContent = 'Siguiente →';
  nb.onclick = render;
  card.appendChild(nb);
}

// ─── AUDIO ────────────────────────────────────────────────────────────────────
// NOTE: nbrosowsky saxophone samples are labeled one octave LOW
// e.g. D4.mp3 actually sounds as D5 — so we request one octave lower than we want
const NOTE_TO_PITCH = {
  'Do₄':'C3','Re₄':'D3','Mi₄':'E3','Fa₄':'F3','Sol₄':'G3','La₄':'A3','Si₄':'B3',
  'Do₅':'C4','Re₅':'D4','Mi₅':'E4','Fa₅':'F4','Sol₅':'G4','La₅':'A4','Si₅':'B4',
  'Do₆':'C5',
};

let sampler = null;
let samplerReady = false;

function initSampler() {
  if (sampler) return;

  const reverb = new Tone.Reverb({ decay: 2.5, wet: 0.35 }).toDestination();
  const vol = new Tone.Volume(-4).connect(reverb);

  sampler = new Tone.Sampler({
    urls: {
      'C3': 'C3.mp3',
      'D3': 'D3.mp3',
      'G3': 'G3.mp3',
      'C4': 'C4.mp3',
      'D4': 'D4.mp3',
      'G4': 'G4.mp3',
      'B4': 'B4.mp3',
      'C5': 'C5.mp3',
    },
    baseUrl: 'https://nbrosowsky.github.io/tonejs-instruments/samples/saxophone/',
    onload: () => { samplerReady = true; },
    onerror: () => {
      const fallback = new Tone.Synth({
        oscillator: { type: 'triangle' },
        envelope: { attack: 0.05, decay: 0.3, sustain: 0.6, release: 1.2 }
      }).connect(vol);
      sampler = fallback;
      samplerReady = true;
    }
  }).connect(vol);
}

async function playNote(label) {
  await Tone.start();
  if (!sampler) initSampler();
  const pitch = NOTE_TO_PITCH[label];
  if (!pitch) return;
  const fire = () => {
    try { sampler.triggerAttackRelease(pitch, '2n'); } catch(e) {}
  };
  if (samplerReady) fire();
  else setTimeout(() => { if (samplerReady) fire(); }, 1000);
}

// ─── INIT ─────────────────────────────────────────────────────────────────────
render();
</script>
</body>
</html>
.html…]()
