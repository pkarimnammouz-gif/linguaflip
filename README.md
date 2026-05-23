# linguaflip
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <meta name="apple-mobile-web-app-title" content="LinguaFlip">
  <title>LinguaFlip</title>
  <link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;700;800&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
  <style>
    *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

    :root {
      --bg: #0e0e0e;
      --card: #181818;
      --card2: #202020;
      --border: rgba(255,255,255,0.07);
      --text: #f0ece4;
      --sub: rgba(240,236,228,0.4);
      --accent: #e8c547;
      --radius: 24px;
      --t: 0.45s cubic-bezier(0.4,0,0.2,1);
    }

    html, body {
      height: 100%; height: 100dvh;
      overflow: hidden;
      background: var(--bg);
      color: var(--text);
      font-family: 'DM Sans', sans-serif;
    }

    .screen {
      position: fixed; inset: 0;
      display: flex; flex-direction: column;
      align-items: center; justify-content: center;
      padding: 24px 20px;
      transition: opacity 0.4s ease, transform 0.4s ease;
    }
    .screen.hidden { opacity: 0; pointer-events: none; transform: translateY(20px); }

    /* ══ SCREEN 1 ══ */
    #pick-screen { gap: 28px; justify-content: flex-start; padding-top: 60px; }

    .logo { text-align: center; }
    .logo-mark {
      font-family: 'Syne', sans-serif;
      font-size: 36px; font-weight: 800;
      letter-spacing: -0.02em; color: var(--text);
    }
    .logo-mark span { color: var(--accent); }
    .logo-sub { font-size: 13px; color: var(--sub); letter-spacing: 0.05em; margin-top: 4px; }

    .pick-prompt { font-size: 15px; color: var(--sub); text-align: center; }

    .lang-grid {
      display: grid; grid-template-columns: 1fr 1fr;
      gap: 12px; width: 100%; max-width: 360px;
    }
    .lang-option {
      background: var(--card); border: 1.5px solid var(--border);
      border-radius: 20px; padding: 18px 16px;
      display: flex; align-items: center; gap: 12px;
      cursor: pointer; transition: border-color 0.2s, background 0.2s, transform 0.15s;
    }
    .lang-option:active { transform: scale(0.96); }
    .lang-option.selected { border-color: var(--accent); background: rgba(232,197,71,0.07); }
    .lang-option .flag { font-size: 28px; line-height: 1; }
    .lang-option .info .name { font-family: 'Syne', sans-serif; font-size: 15px; font-weight: 700; color: var(--text); }
    .lang-option .info .native { font-size: 11px; color: var(--sub); margin-top: 1px; }
    .lang-option .check {
      width: 20px; height: 20px; border-radius: 50%;
      border: 1.5px solid var(--border);
      display: flex; align-items: center; justify-content: center;
      font-size: 11px; transition: all 0.2s; flex-shrink: 0;
    }
    .lang-option.selected .check { background: var(--accent); border-color: var(--accent); color: #0e0e0e; }

    .start-btn {
      width: 100%; max-width: 360px; padding: 18px;
      background: var(--accent); color: #0e0e0e;
      border: none; border-radius: var(--radius);
      font-family: 'Syne', sans-serif; font-size: 17px; font-weight: 700;
      cursor: pointer; transition: transform 0.15s, opacity 0.2s; letter-spacing: 0.01em;
    }
    .start-btn:active { transform: scale(0.97); }
    .start-btn:disabled { opacity: 0.3; pointer-events: none; }

    /* ══ SCREEN 2 ══ */
    #main-screen {
      gap: 0; justify-content: space-between;
      padding: 52px 20px 40px;
    }

    .topbar {
      width: 100%; display: flex; align-items: center; justify-content: space-between;
    }
    .topbar-logo { font-family: 'Syne', sans-serif; font-size: 20px; font-weight: 800; color: var(--text); }
    .topbar-logo span { color: var(--accent); }
    .change-lang-btn {
      font-size: 12px; color: var(--sub);
      background: var(--card); border: 1px solid var(--border);
      border-radius: 50px; padding: 6px 14px; cursor: pointer;
      transition: color 0.2s, border-color 0.2s;
    }
    .change-lang-btn:active { color: var(--accent); border-color: var(--accent); }

    .hero {
      display: flex; flex-direction: column;
      align-items: center; gap: 20px; width: 100%;
    }

    .mode-label {
      font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700;
      letter-spacing: 0.12em; text-transform: uppercase;
      color: var(--sub); transition: color var(--t);
    }

    /* Big tap button */
    .big-toggle { position: relative; width: 160px; height: 160px; cursor: pointer; }
    .big-toggle-pulse {
      position: absolute; inset: -8px; border-radius: 50%;
      border: 1px solid rgba(232,197,71,0); transition: all 0.5s ease;
    }
    .big-toggle-pulse.active { border-color: rgba(232,197,71,0.2); inset: -16px; }
    .big-toggle-ring {
      position: absolute; inset: 0; border-radius: 50%;
      border: 2px solid var(--border); transition: border-color var(--t);
    }
    .big-toggle-ring.active { border-color: var(--accent); }
    .big-toggle-inner {
      position: absolute; inset: 8px; border-radius: 50%;
      background: var(--card);
      display: flex; flex-direction: column; align-items: center; justify-content: center;
      gap: 4px; transition: background var(--t);
      box-shadow: 0 8px 32px rgba(0,0,0,0.4);
    }
    .big-toggle-inner.active { background: rgba(232,197,71,0.08); }
    .big-flag { font-size: 52px; line-height: 1; transition: transform var(--t); }
    .big-toggle:active .big-flag { transform: scale(0.9); }
    .big-toggle-hint {
      font-size: 10px; color: var(--sub);
      letter-spacing: 0.1em; text-transform: uppercase;
      transition: color var(--t);
    }
    .big-toggle-hint.learning { color: var(--accent); }

    .lang-pair { display: flex; align-items: center; gap: 16px; }
    .pair-item {
      display: flex; flex-direction: column; align-items: center; gap: 4px;
      opacity: 0.35; transition: opacity var(--t);
    }
    .pair-item.active { opacity: 1; }
    .pair-item .p-flag { font-size: 22px; }
    .pair-item .p-name { font-size: 11px; font-weight: 500; letter-spacing: 0.06em; color: var(--text); }
    .pair-arrow { font-size: 16px; color: var(--sub); opacity: 0.4; }

    .status-pill {
      padding: 8px 20px; border-radius: 50px; border: 1px solid var(--border);
      font-size: 12px; letter-spacing: 0.06em; color: var(--sub);
      transition: all var(--t); text-align: center;
    }
    .status-pill.learning {
      border-color: rgba(232,197,71,0.3); color: var(--accent);
      background: rgba(232,197,71,0.05);
    }

    /* Opening settings hint */
    .settings-hint {
      font-size: 11px; color: var(--sub); text-align: center;
      letter-spacing: 0.04em; min-height: 16px;
      transition: color var(--t); opacity: 0;
      transition: opacity 0.3s;
    }
    .settings-hint.show { opacity: 1; }

    .bottom-stack { width: 100%; display: flex; flex-direction: column; gap: 12px; }

    .streak-row {
      display: flex; align-items: center; justify-content: center; gap: 6px;
    }
    .streak-label { font-size: 12px; color: var(--sub); }
    .streak-num { font-family: 'Syne', sans-serif; font-size: 16px; font-weight: 700; color: var(--accent); }

    .word-card {
      background: var(--card); border: 1px solid var(--border);
      border-radius: var(--radius); padding: 16px 20px;
      display: flex; align-items: center; justify-content: space-between;
    }
    .word-left { flex: 1; }
    .word-eyebrow { font-size: 10px; letter-spacing: 0.12em; text-transform: uppercase; color: var(--sub); margin-bottom: 4px; }
    .word-target { font-family: 'Syne', sans-serif; font-size: 22px; font-weight: 700; color: var(--accent); line-height: 1.1; }
    .word-native { font-size: 13px; color: var(--sub); margin-top: 2px; }
    .word-btn {
      width: 40px; height: 40px; border-radius: 50%;
      background: var(--card2); border: 1px solid var(--border);
      display: flex; align-items: center; justify-content: center;
      cursor: pointer; font-size: 18px; transition: transform 0.4s, background 0.2s; flex-shrink: 0;
    }
    .word-btn:active { background: rgba(232,197,71,0.1); }
    .word-btn.spin { transform: rotate(360deg); }

    /* How it works note */
    .how-it-works {
      background: var(--card); border: 1px solid var(--border);
      border-radius: 16px; padding: 14px 18px;
      display: flex; gap: 12px; align-items: flex-start;
    }
    .how-icon { font-size: 16px; margin-top: 1px; flex-shrink: 0; }
    .how-text { font-size: 12px; color: var(--sub); line-height: 1.6; }
    .how-text strong { color: var(--text); font-weight: 500; }

    @keyframes fadeUp {
      from { opacity: 0; transform: translateY(12px); }
      to   { opacity: 1; transform: translateY(0); }
    }
    .anim { animation: fadeUp 0.4s ease both; }
    .anim-d1 { animation-delay: 0.05s; }
    .anim-d2 { animation-delay: 0.1s; }
    .anim-d3 { animation-delay: 0.15s; }
  </style>
</head>
<body>

<!-- SCREEN 1 -->
<div class="screen" id="pick-screen">
  <div class="logo anim">
    <div class="logo-mark">Lingua<span>Flip</span></div>
    <div class="logo-sub">Your phone. Two languages. One tap.</div>
  </div>
  <div class="pick-prompt anim anim-d1">Which language are you learning?</div>
  <div class="lang-grid anim anim-d2" id="lang-grid"></div>
  <button class="start-btn anim anim-d3" id="start-btn" disabled onclick="goToMain()">Let's Go →</button>
</div>

<!-- SCREEN 2 -->
<div class="screen hidden" id="main-screen">

  <div class="topbar">
    <div class="topbar-logo">Lingua<span>Flip</span></div>
    <div class="change-lang-btn" onclick="goToPick()">Change language</div>
  </div>

  <div class="hero">
    <div class="mode-label" id="mode-label">ENGLISH MODE</div>

    <div class="big-toggle" onclick="flipLanguage()" id="big-toggle">
      <div class="big-toggle-pulse" id="pulse"></div>
      <div class="big-toggle-ring" id="ring"></div>
      <div class="big-toggle-inner" id="inner">
        <div class="big-flag" id="big-flag">🇺🇸</div>
        <div class="big-toggle-hint" id="toggle-hint">TAP TO FLIP</div>
      </div>
    </div>

    <div class="lang-pair">
      <div class="pair-item active" id="en-item">
        <div class="p-flag">🇺🇸</div>
        <div class="p-name">ENGLISH</div>
      </div>
      <div class="pair-arrow">⇄</div>
      <div class="pair-item" id="target-item">
        <div class="p-flag" id="target-flag-sm">🇪🇸</div>
        <div class="p-name" id="target-name-sm">ESPAÑOL</div>
      </div>
    </div>

    <div class="status-pill" id="status-pill">You're browsing in English</div>
    <div class="settings-hint" id="settings-hint">Opening iPhone Language Settings…</div>
  </div>

  <div class="bottom-stack">
    <div class="streak-row">
      <span class="streak-label">🔥 Practice streak:</span>
      <span class="streak-num" id="streak-num">0</span>
      <span class="streak-label">days</span>
    </div>

    <div class="word-card">
      <div class="word-left">
        <div class="word-eyebrow">Word of the day</div>
        <div class="word-target" id="word-target">—</div>
        <div class="word-native" id="word-native">—</div>
      </div>
      <div class="word-btn" id="word-btn" onclick="nextWord()">↻</div>
    </div>

    <div class="how-it-works">
      <div class="how-icon">💡</div>
      <div class="how-text">
        <strong>Tap the flag</strong> — it flips your tracker and opens iPhone Settings so you can confirm the language switch in one flow.
      </div>
    </div>
  </div>

</div>

<script>
const LANGUAGES = [
  {
    code: 'es', name: 'Spanish', native: 'Español', flag: '🇪🇸',
    words: [
      { w: 'Madrugada',  m: 'The hours between midnight & dawn' },
      { w: 'Sobremesa',  m: 'Lingering at the table after eating' },
      { w: 'Añoranza',   m: 'A deep, aching nostalgia' },
      { w: 'Estrenar',   m: 'To use something for the first time' },
      { w: 'Trasnochar', m: 'To stay up all night' },
      { w: 'Mariposa',   m: 'Butterfly' },
      { w: 'Amanecer',   m: 'Dawn / to dawn' },
      { w: 'Bienvenido', m: 'Welcome' },
      { w: 'Desvelado',  m: 'Sleepless, wide awake at night' },
      { w: 'Crepúsculo', m: 'Twilight / dusk' },
    ]
  },
  {
    code: 'fr', name: 'French', native: 'Français', flag: '🇫🇷',
    words: [
      { w: 'Dépaysement',  m: 'The feeling of being in a foreign place' },
      { w: 'Flâner',       m: 'To stroll without a destination' },
      { w: 'Retrouvailles',m: 'The joy of reuniting with someone' },
      { w: 'Gourmand',     m: 'One who loves to eat well' },
      { w: 'Papillon',     m: 'Butterfly' },
      { w: 'Bonsoir',      m: 'Good evening' },
      { w: 'Lumière',      m: 'Light' },
      { w: 'Douce',        m: 'Gentle / soft' },
      { w: 'Crépuscule',   m: 'Twilight / dusk' },
      { w: 'Bienvenue',    m: 'Welcome' },
    ]
  },
  {
    code: 'it', name: 'Italian', native: 'Italiano', flag: '🇮🇹',
    words: [
      { w: 'Abbiocco',    m: 'Sleepiness after a big meal' },
      { w: 'Culaccino',   m: 'Ring left by a wet glass on a table' },
      { w: 'Meriggiare',  m: 'To rest at noon in the shade' },
      { w: 'Farfalla',    m: 'Butterfly' },
      { w: 'Benvenuto',   m: 'Welcome' },
      { w: 'Alba',        m: 'Dawn' },
      { w: 'Tramonto',    m: 'Sunset' },
      { w: 'Dolcezza',    m: 'Sweetness / tenderness' },
      { w: 'Passeggiata', m: 'An evening stroll' },
      { w: 'Gattara',     m: 'A woman who cares for stray cats' },
    ]
  },
  {
    code: 'pt', name: 'Portuguese', native: 'Português', flag: '🇧🇷',
    words: [
      { w: 'Saudade',      m: 'A deep longing for something absent' },
      { w: 'Madrugada',    m: 'The hours before dawn' },
      { w: 'Borboleta',    m: 'Butterfly' },
      { w: 'Boas-vindas',  m: 'Welcome' },
      { w: 'Amanhecer',    m: 'Sunrise / to dawn' },
      { w: 'Crepúsculo',   m: 'Twilight' },
      { w: 'Doçura',       m: 'Sweetness' },
      { w: 'Devagar',      m: 'Slowly / unhurried' },
      { w: 'Anoitecer',    m: 'Nightfall' },
      { w: 'Desenrascanço',m: 'Improvising a clever solution' },
    ]
  },
  {
    code: 'de', name: 'German', native: 'Deutsch', flag: '🇩🇪',
    words: [
      { w: 'Fernweh',       m: 'A longing to travel far away' },
      { w: 'Waldeinsamkeit',m: 'Solitude of being alone in a forest' },
      { w: 'Schmetterling', m: 'Butterfly' },
      { w: 'Willkommen',    m: 'Welcome' },
      { w: 'Morgenrot',     m: 'Red sky at dawn' },
      { w: 'Gemütlichkeit', m: 'Coziness / warmth' },
      { w: 'Weltschmerz',   m: 'Pain felt from the state of the world' },
      { w: 'Dämmerung',     m: 'Twilight / dusk' },
      { w: 'Sehnsucht',     m: 'A deep yearning or longing' },
      { w: 'Zweisamkeit',   m: 'The feeling of togetherness for two' },
    ]
  },
  {
    code: 'ja', name: 'Japanese', native: '日本語', flag: '🇯🇵',
    words: [
      { w: 'Komorebi',     m: 'Sunlight filtering through leaves' },
      { w: 'Wabi-sabi',    m: 'Beauty in imperfection & impermanence' },
      { w: 'Mono no aware',m: 'The bittersweet transience of things' },
      { w: 'Ikigai',       m: 'A reason for being' },
      { w: 'Natsukashii',  m: 'Sweet nostalgia' },
      { w: 'Yugen',        m: 'A mysterious sense of the universe' },
      { w: 'Chō',          m: 'Butterfly (蝶)' },
      { w: 'Yōkoso',       m: 'Welcome (ようこそ)' },
      { w: 'Akebono',      m: 'Dawn / daybreak (曙)' },
      { w: 'Ma',           m: 'Meaningful pause / negative space (間)' },
    ]
  },
];

let selectedLang = null;
let isLearning = false;
let wordIdx = 0;
let streak = parseInt(localStorage.getItem('lf_streak') || '0');
let lastDate = localStorage.getItem('lf_date') || '';

function buildGrid() {
  const grid = document.getElementById('lang-grid');
  grid.innerHTML = '';
  LANGUAGES.forEach(lang => {
    const el = document.createElement('div');
    el.className = 'lang-option';
    el.innerHTML = `
      <div class="flag">${lang.flag}</div>
      <div class="info">
        <div class="name">${lang.name}</div>
        <div class="native">${lang.native}</div>
      </div>
      <div class="check">✓</div>
    `;
    el.onclick = () => {
      document.querySelectorAll('.lang-option').forEach(o => o.classList.remove('selected'));
      el.classList.add('selected');
      selectedLang = lang;
      document.getElementById('start-btn').disabled = false;
    };
    grid.appendChild(el);
  });
}

function goToPick() {
  document.getElementById('main-screen').classList.add('hidden');
  document.getElementById('pick-screen').classList.remove('hidden');
  isLearning = false;
}

function goToMain() {
  if (!selectedLang) return;
  localStorage.setItem('lf_lang', selectedLang.code);
  document.getElementById('pick-screen').classList.add('hidden');
  document.getElementById('main-screen').classList.remove('hidden');
  document.getElementById('target-flag-sm').textContent = selectedLang.flag;
  document.getElementById('target-name-sm').textContent = selectedLang.native.toUpperCase();
  isLearning = false;
  updateMainUI();
  wordIdx = Math.floor(Math.random() * selectedLang.words.length);
  renderWord();
  document.getElementById('streak-num').textContent = streak;
}

function flipLanguage() {
  isLearning = !isLearning;

  if (isLearning) {
    // Update streak
    const today = new Date().toDateString();
    if (lastDate !== today) {
      streak++;
      lastDate = today;
      localStorage.setItem('lf_streak', streak);
      localStorage.setItem('lf_date', today);
      document.getElementById('streak-num').textContent = streak;
    }
    // Open settings immediately
    window.location.href = 'App-prefs:root=General&path=LANGUAGE';
    // Show hint
    const hint = document.getElementById('settings-hint');
    hint.classList.add('show');
    setTimeout(() => hint.classList.remove('show'), 3000);
  } else {
    // Going back to English — open settings so they can switch back
    window.location.href = 'App-prefs:root=General&path=LANGUAGE';
    const hint = document.getElementById('settings-hint');
    hint.textContent = 'Opening iPhone Language Settings…';
    hint.classList.add('show');
    setTimeout(() => {
      hint.classList.remove('show');
      hint.textContent = 'Opening iPhone Language Settings…';
    }, 3000);
  }

  updateMainUI();
}

function updateMainUI() {
  const lang = selectedLang;
  const ring = document.getElementById('ring');
  const inner = document.getElementById('inner');
  const pulse = document.getElementById('pulse');
  const bigFlag = document.getElementById('big-flag');
  const modeLabel = document.getElementById('mode-label');
  const toggleHint = document.getElementById('toggle-hint');
  const enItem = document.getElementById('en-item');
  const targetItem = document.getElementById('target-item');
  const statusPill = document.getElementById('status-pill');

  if (isLearning) {
    ring.classList.add('active');
    inner.classList.add('active');
    pulse.classList.add('active');
    bigFlag.textContent = lang.flag;
    modeLabel.textContent = lang.native.toUpperCase() + ' MODE';
    toggleHint.textContent = 'TAP TO FLIP BACK';
    toggleHint.classList.add('learning');
    enItem.classList.remove('active');
    targetItem.classList.add('active');
    statusPill.textContent = `Practicing ${lang.name} 🎯`;
    statusPill.classList.add('learning');
  } else {
    ring.classList.remove('active');
    inner.classList.remove('active');
    pulse.classList.remove('active');
    bigFlag.textContent = '🇺🇸';
    modeLabel.textContent = 'ENGLISH MODE';
    toggleHint.textContent = 'TAP TO FLIP';
    toggleHint.classList.remove('learning');
    enItem.classList.add('active');
    targetItem.classList.remove('active');
    statusPill.textContent = "You're browsing in English";
    statusPill.classList.remove('learning');
  }
}

function nextWord() {
  const btn = document.getElementById('word-btn');
  btn.classList.add('spin');
  setTimeout(() => btn.classList.remove('spin'), 420);
  wordIdx = (wordIdx + 1) % selectedLang.words.length;
  renderWord();
}

function renderWord() {
  const w = selectedLang.words[wordIdx];
  document.getElementById('word-target').textContent = w.w;
  document.getElementById('word-native').textContent = w.m;
}

// Init
buildGrid();
const saved = localStorage.getItem('lf_lang');
if (saved) {
  const lang = LANGUAGES.find(l => l.code === saved);
  if (lang) {
    selectedLang = lang;
    setTimeout(() => {
      const opts = document.querySelectorAll('.lang-option');
      const idx = LANGUAGES.indexOf(lang);
      if (opts[idx]) opts[idx].classList.add('selected');
      document.getElementById('start-btn').disabled = false;
      goToMain();
    }, 100);
  }
}
</script>
</body>
</html>