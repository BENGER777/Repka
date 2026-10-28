<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no,viewport-fit=cover">
<title>Репка — книжка-сказка</title>
<style>
  *,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
  html,body{height:100%;overflow:hidden;overscroll-behavior:none}
  body{
    background:radial-gradient(120% 90% at 50% 28%, #3d2b1d 0%, #26190f 45%, #130c07 100%);
    display:flex;align-items:center;justify-content:center;
    font-family:Georgia,'Times New Roman',serif;
    -webkit-tap-highlight-color:transparent;
    touch-action:none;
    user-select:none;-webkit-user-select:none;
  }

  .stage{
    position:relative;
    width:100%;height:100%;
    display:flex;align-items:center;justify-content:center;
  }

  /* ---------- КНИГА ---------- */
  .book{
    position:relative;
    width:min(86vw, 50vh, 400px);
    aspect-ratio:2 / 3;
    perspective:2200px;
    perspective-origin:50% 50%;
    transform-style:preserve-3d;
    margin-bottom:6vh;
  }

  .book::before{
    content:'';
    position:absolute;
    inset:0;
    border-radius:4px 10px 10px 4px;
    background:linear-gradient(90deg,#2b1e15 0%,#3f2c1e 100%);
    box-shadow:
      0 28px 55px rgba(0,0,0,.78),
      0 10px 24px rgba(0,0,0,.6),
      0 0 0 1px rgba(0,0,0,.5);
    z-index:0;
  }

  .spine{
    position:absolute;
    left:0;top:0;bottom:0;
    width:11px;
    z-index:900;
    pointer-events:none;
    border-radius:4px 0 0 4px;
    background:linear-gradient(90deg,
      rgba(0,0,0,.60) 0%,
      rgba(255,236,200,.14) 30%,
      rgba(0,0,0,.08) 60%,
      rgba(0,0,0,.45) 100%);
    box-shadow:inset -1px 0 4px rgba(0,0,0,.35);
  }

  /* ---------- СТРАНИЦА ---------- */
  .page{
    position:absolute;
    inset:0;
    transform-origin:left center;
    transform-style:preserve-3d;
    transform:rotateY(0deg);
    will-change:transform;
  }
  .page.animate{
    transition:transform .7s cubic-bezier(.24,.82,.32,1);
  }

  .face{
    position:absolute;
    inset:0;
    backface-visibility:hidden;
    -webkit-backface-visibility:hidden;
    overflow:hidden;
    border-radius:3px 9px 9px 3px;
    background:#FFF9EE;
    box-shadow:inset 0 0 44px rgba(126,95,52,.13);
  }
  .face.back{
    transform:rotateY(180deg);
    border-radius:9px 3px 3px 9px;
    background:linear-gradient(160deg,#FBF2E2 0%,#F3E7D2 100%);
  }

  /* бумажная текстура */
  .face::after{
    content:'';
    position:absolute;
    inset:0;
    pointer-events:none;
    z-index:6;
    opacity:.16;
    mix-blend-mode:multiply;
    background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='120' height='120' filter='url(%23n)' opacity='0.55'/%3E%3C/svg%3E");
  }

  /* лёгкое затемнение к корешку */
  .face::before{
    content:'';
    position:absolute;
    inset:0;
    pointer-events:none;
    z-index:5;
    background:linear-gradient(90deg, rgba(90,62,32,.22) 0%, rgba(90,62,32,.06) 6%, rgba(90,62,32,0) 14%);
  }
  .face.back::before{
    background:linear-gradient(270deg, rgba(90,62,32,.22) 0%, rgba(90,62,32,.06) 6%, rgba(90,62,32,0) 14%);
  }

  .ill{
    position:absolute;
    inset:0;
    width:100%;
    height:100%;
    display:block;
    z-index:1;
  }

  .txt{
    position:absolute;
    left:0;right:0;bottom:0;
    z-index:3;
    padding:34px 20px 20px;
    text-align:center;
    font-size:var(--fs,15px);
    line-height:1.46;
    color:#4a3524;
    letter-spacing:.2px;
    background:linear-gradient(to top,
      rgba(255,250,238,.985) 38%,
      rgba(255,250,238,.94) 62%,
      rgba(255,250,238,.72) 82%,
      rgba(255,250,238,0) 100%);
    text-shadow:0 1px 0 rgba(255,255,255,.7);
  }
  .txt em{
    display:block;
    font-style:normal;
    font-size:.86em;
    color:#8a6a48;
    margin-top:4px;
    letter-spacing:.6px;
  }

  /* пустая обратная сторона — легкий орнамент */
  .back-orn{
    position:absolute;
    inset:0;
    display:flex;
    align-items:center;
    justify-content:center;
    opacity:.16;
    z-index:2;
  }
  .back-orn svg{width:44%;height:auto}

  /* ---------- ИНТЕРФЕЙС ---------- */
  .dots{
    position:absolute;
    left:50%;
    bottom:calc(16px + env(safe-area-inset-bottom,0px));
    transform:translateX(-50%);
    display:flex;
    gap:7px;
    z-index:1000;
  }
  .dot{
    width:7px;height:7px;border-radius:50%;
    background:rgba(238,215,178,.22);
    transition:background .35s, width .35s, border-radius .35s;
  }
  .dot.on{
    background:#E9C98F;
    width:19px;
    border-radius:4px;
    box-shadow:0 0 10px rgba(233,201,143,.55);
  }

  .hint{
    position:absolute;
    left:50%;
    bottom:calc(38px + env(safe-area-inset-bottom,0px));
    transform:translateX(-50%);
    z-index:1000;
    color:rgba(240,220,186,.72);
    font-size:12.5px;
    letter-spacing:1.4px;
    white-space:nowrap;
    pointer-events:none;
    transition:opacity .9s ease;
    animation:hintFloat 2.4s ease-in-out infinite;
  }
  .hint.hide{opacity:0;animation:none}
  @keyframes hintFloat{
    0%,100%{transform:translateX(-50%) translateY(0)}
    50%{transform:translateX(-50%) translateY(-4px)}
  }
</style>
</head>
<body>

<div class="stage">
  <div class="book" id="book">
    <div class="spine"></div>
  </div>
  <div class="dots" id="dots"></div>
  <div class="hint" id="hint">проведите пальцем →</div>
</div>

<script>
(function(){
"use strict";

/* =========================================================
   SVG-ПОМОЩНИКИ
   ========================================================= */
const svgWrap = (inner) =>
  `<svg class="ill" viewBox="0 0 400 480" preserveAspectRatio="xMidYMid slice" xmlns="http://www.w3.org/2000/svg">${inner}</svg>`;

const sun = (x,y,r) => `
  <circle cx="${x}" cy="${y}" r="${r*2.0}" fill="#F7B267" opacity="0.10"/>
  <circle cx="${x}" cy="${y}" r="${r*1.4}" fill="#F7B267" opacity="0.16"/>
  <circle cx="${x}" cy="${y}" r="${r}" fill="#F8B96B"/>
  <circle cx="${x}" cy="${y}" r="${r*0.68}" fill="#FFDCA8"/>`;

const cloud = (x,y,s,o) => `
  <g transform="translate(${x},${y}) scale(${s})" fill="#FFF8EC" opacity="${o||0.82}">
    <ellipse cx="0" cy="0" rx="28" ry="14"/>
    <ellipse cx="20" cy="5" rx="20" ry="11"/>
    <ellipse cx="-20" cy="6" rx="16" ry="9"/>
    <ellipse cx="4" cy="-10" rx="17" ry="12"/>
  </g>`;

function landscape(p, sky1, sky2, hill1, hill2, grass1, grass2, sx, sy, sr){
  return `
  <defs>
    <linearGradient id="sky${p}" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0" stop-color="${sky1}"/><stop offset="1" stop-color="${sky2}"/>
    </linearGradient>
    <linearGradient id="grs${p}" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0" stop-color="${grass1}"/><stop offset="1" stop-color="${grass2}"/>
    </linearGradient>
  </defs>
  <rect width="400" height="480" fill="url(#sky${p})"/>
  ${sun(sx, sy, sr)}
  ${cloud(92, 78, 1, .85)}
  ${cloud(302, 124, .72, .62)}
  <path d="M0 292 Q 62 250 132 284 Q 202 318 270 286 Q 338 254 400 290 L400 480 L0 480 Z" fill="${hill1}"/>
  <path d="M0 326 Q 82 294 170 322 Q 260 350 342 318 Q 372 306 400 318 L400 480 L0 480 Z" fill="${hill2}"/>
  <path d="M0 364 Q 100 342 200 358 Q 300 374 400 352 L400 480 L0 480 Z" fill="url(#grs${p})"/>
  <path d="M0 376 Q 100 356 200 370 Q 300 384 400 364 L400 392 L0 392 Z" fill="#000" opacity="0.05"/>
  `;
}

const grassTufts = (p) => `
  <g stroke="#6E8F4E" stroke-width="2" stroke-linecap="round" fill="none" opacity="0.7">
    <path d="M70 402 q 3 -12 7 -16"/><path d="M76 402 q 0 -13 4 -18"/>
    <path d="M150 418 q 3 -12 7 -16"/><path d="M156 418 q 0 -13 4 -18"/>
    <path d="M330 398 q 3 -12 7 -16"/><path d="M336 398 q 0 -13 4 -18"/>
    <path d="M368 424 q 3 -12 7 -16"/><path d="M374 424 q 0 -13 4 -18"/>
    <path d="M44 436 q 3 -12 7 -16"/><path d="M50 436 q 0 -13 4 -18"/>
  </g>`;

/* -------- персонажи -------- */

function grandpa(x,y,s,rot){
  return `<g transform="translate(${x},${y}) scale(${s}) rotate(${rot||0})">
    <ellipse cx="0" cy="3" rx="27" ry="6" fill="#3a2818" opacity="0.16"/>
    <rect x="-15" y="-40" width="11" height="40" rx="5" fill="#5A4A3A"/>
    <rect x="4" y="-40" width="11" height="40" rx="5" fill="#5A4A3A"/>
    <path d="M-19 -8 Q -19 3 -8 3 L -4 3 L -4 -10 Z" fill="#3B2C22"/>
    <path d="M3 -8 Q 3 3 14 3 L 18 3 L 18 -10 Z" fill="#3B2C22"/>
    <path d="M-26 -92 Q 0 -102 26 -92 L 30 -36 Q 0 -29 -30 -36 Z" fill="#7E9C86"/>
    <path d="M-26 -88 Q -41 -74 -37 -54" stroke="#7E9C86" stroke-width="11" fill="none" stroke-linecap="round"/>
    <path d="M26 -88 Q 41 -74 37 -54" stroke="#7E9C86" stroke-width="11" fill="none" stroke-linecap="round"/>
    <circle cx="-38" cy="-52" r="7.6" fill="#F2C79A"/>
    <circle cx="38" cy="-52" r="7.6" fill="#F2C79A"/>
    <circle cx="0" cy="-110" r="18" fill="#F2C79A"/>
    <path d="M-16 -104 Q -18 -79 0 -71 Q 18 -79 16 -104 Q 8 -94 0 -94 Q -8 -94 -16 -104 Z" fill="#F0EDE4"/>
    <circle cx="-6" cy="-114" r="2.3" fill="#3A2A1E"/>
    <circle cx="6" cy="-114" r="2.3" fill="#3A2A1E"/>
    <ellipse cx="0" cy="-107" rx="4.6" ry="3.6" fill="#E3A77C"/>
    <path d="M-20 -122 Q 0 -143 20 -122 Z" fill="#8B5E3C"/>
    <rect x="-22" y="-126" width="44" height="7" rx="3.5" fill="#6E4829"/>
  </g>`;
}

function grandma(x,y,s,rot){
  return `<g transform="translate(${x},${y}) scale(${s}) rotate(${rot||0})">
    <ellipse cx="0" cy="3" rx="24" ry="6" fill="#3a2818" opacity="0.16"/>
    <path d="M-24 -6 L-30 -62 L30 -62 L24 -6 Z" fill="#8E4C63"/>
    <path d="M-24 -6 L-30 -62 L-6 -62 L-4 -6 Z" fill="#9C5670" opacity="0.55"/>
    <path d="M-26 -86 Q 0 -96 26 -86 L 30 -58 Q 0 -51 -30 -58 Z" fill="#E6DEC9"/>
    <path d="M-26 -82 Q -41 -70 -37 -52" stroke="#E6DEC9" stroke-width="10" fill="none" stroke-linecap="round"/>
    <path d="M26 -82 Q 41 -70 37 -52" stroke="#E6DEC9" stroke-width="10" fill="none" stroke-linecap="round"/>
    <circle cx="-38" cy="-50" r="7" fill="#F2C79A"/>
    <circle cx="38" cy="-50" r="7" fill="#F2C79A"/>
    <circle cx="0" cy="-107" r="16.5" fill="#F2C79A"/>
    <path d="M-19 -110 Q -21 -136 0 -136 Q 21 -136 19 -110 Q 10 -122 0 -122 Q -10 -122 -19 -110 Z" fill="#C25B4E"/>
    <path d="M-19 -110 Q -24 -96 -20 -88 L-14 -90 Q -17 -100 -14 -110 Z" fill="#B34E42"/>
    <circle cx="-6" cy="-108" r="2.2" fill="#3A2A1E"/>
    <circle cx="6" cy="-108" r="2.2" fill="#3A2A1E"/>
    <ellipse cx="0" cy="-101" rx="4" ry="3.2" fill="#E3A77C"/>
    <path d="M-6 -94 Q 0 -90 6 -94" stroke="#C98C6B" stroke-width="1.6" fill="none" stroke-linecap="round"/>
  </g>`;
}

function girl(x,y,s,rot){
  return `<g transform="translate(${x},${y}) scale(${s}) rotate(${rot||0})">
    <ellipse cx="0" cy="3" rx="18" ry="5" fill="#3a2818" opacity="0.16"/>
    <rect x="-10" y="-32" width="8" height="32" rx="4" fill="#F4D0A6"/>
    <rect x="2" y="-32" width="8" height="32" rx="4" fill="#F4D0A6"/>
    <path d="M-13 -6 Q -13 3 -4 3 L 0 3 L 0 -8 Z" fill="#5A4030"/>
    <path d="M1 -6 Q 1 3 10 3 L 14 3 L 14 -8 Z" fill="#5A4030"/>
    <path d="M-20 -6 L-16 -56 L16 -56 L20 -6 Z" fill="#D96C5A"/>
    <path d="M-20 -6 L-16 -56 L-4 -56 L-6 -6 Z" fill="#E17D6B" opacity="0.6"/>
    <path d="M-17 -52 Q -29 -42 -27 -28" stroke="#F4D0A6" stroke-width="7" fill="none" stroke-linecap="round"/>
    <path d="M17 -52 Q 29 -42 27 -28" stroke="#F4D0A6" stroke-width="7" fill="none" stroke-linecap="round"/>
    <circle cx="0" cy="-70" r="15" fill="#F6D3AC"/>
    <path d="M-15 -72 Q -16 -92 0 -92 Q 16 -92 15 -72 Q 8 -82 0 -82 Q -8 -82 -15 -72 Z" fill="#B5713C"/>
    <circle cx="-15" cy="-74" r="6" fill="#B5713C"/>
    <circle cx="15" cy="-74" r="6" fill="#B5713C"/>
    <circle cx="-5" cy="-72" r="2" fill="#3A2A1E"/>
    <circle cx="5" cy="-72" r="2" fill="#3A2A1E"/>
    <path d="M-3.5 -64 Q 0 -61 3.5 -64" stroke="#C98C6B" stroke-width="1.5" fill="none" stroke-linecap="round"/>
  </g>`;
}

function dog(x,y,s,rot){
  return `<g transform="translate(${x},${y}) scale(${s}) rotate(${rot||0})">
    <ellipse cx="0" cy="3" rx="20" ry="5" fill="#3a2818" opacity="0.16"/>
    <ellipse cx="0" cy="-17" rx="21" ry="13" fill="#C08B4E"/>
    <rect x="-15" y="-10" width="7" height="12" rx="3" fill="#A97540"/>
    <rect x="8" y="-10" width="7" height="12" rx="3" fill="#A97540"/>
    <path d="M19 -21 Q 31 -31 29 -41" stroke="#C08B4E" stroke-width="6" fill="none" stroke-linecap="round"/>
    <circle cx="-19" cy="-29" r="12.5" fill="#C08B4E"/>
    <ellipse cx="-29" cy="-25" rx="8" ry="6" fill="#D9A868"/>
    <circle cx="-32" cy="-27" r="2.6" fill="#3A2A1E"/>
    <path d="M-27 -38 Q -33 -50 -21 -44 Z" fill="#A97540"/>
    <path d="M-11 -38 Q -5 -50 -15 -44 Z" fill="#A97540"/>
    <circle cx="-21" cy="-32" r="2" fill="#3A2A1E"/>
  </g>`;
}

function cat(x,y,s,rot){
  return `<g transform="translate(${x},${y}) scale(${s}) rotate(${rot||0})">
    <ellipse cx="0" cy="3" rx="15" ry="4" fill="#3a2818" opacity="0.16"/>
    <ellipse cx="0" cy="-14" rx="17" ry="10.5" fill="#8A8072"/>
    <rect x="-12" y="-8" width="6" height="10" rx="3" fill="#776D60"/>
    <rect x="6" y="-8" width="6" height="10" rx="3" fill="#776D60"/>
    <path d="M14 -18 Q 26 -26 24 -37" stroke="#8A8072" stroke-width="5" fill="none" stroke-linecap="round"/>
    <circle cx="-15" cy="-24" r="11" fill="#8A8072"/>
    <path d="M-24 -32 L-26 -45 L-16 -37 Z" fill="#8A8072"/>
    <path d="M-8 -32 L-6 -45 L-15 -37 Z" fill="#8A8072"/>
    <circle cx="-18" cy="-26" r="1.8" fill="#3A2A1E"/>
    <circle cx="-12" cy="-26" r="1.8" fill="#3A2A1E"/>
    <path d="M-17 -21 L-13 -21" stroke="#5A5048" stroke-width="1.5" stroke-linecap="round"/>
  </g>`;
}

function mouse(x,y,s,rot){
  return `<g transform="translate(${x},${y}) scale(${s}) rotate(${rot||0})">
    <ellipse cx="0" cy="3" rx="10" ry="3" fill="#3a2818" opacity="0.16"/>
    <ellipse cx="0" cy="-10" rx="13" ry="9" fill="#A9A29A"/>
    <path d="M11 -14 Q 22 -20 20 -29" stroke="#A9A29A" stroke-width="3" fill="none" stroke-linecap="round"/>
    <circle cx="-11" cy="-16" r="8.5" fill="#A9A29A"/>
    <circle cx="-16" cy="-24" r="6" fill="#CBC1B6"/>
    <circle cx="-6" cy="-24" r="6" fill="#CBC1B6"/>
    <circle cx="-13" cy="-17" r="1.6" fill="#3A2A1E"/>
    <circle cx="-8" cy="-17" r="1.6" fill="#3A2A1E"/>
    <circle cx="-19" cy="-13" r="2.2" fill="#E8918C" opacity="0.55"/>
  </g>`;
}

function turnipTops(cx,cy,s,rot){
  return `<g transform="translate(${cx},${cy}) scale(${s}) rotate(${rot||0})">
    <path d="M0 0 Q -40 -50 -72 -98 Q -36 -70 -14 -36 Z" fill="#7FA653"/>
    <path d="M0 0 Q 40 -50 72 -98 Q 36 -70 14 -36 Z" fill="#8DB55D"/>
    <path d="M0 0 Q -18 -62 -24 -112 Q -6 -66 2 -30 Z" fill="#6E9448"/>
    <path d="M0 0 Q 20 -58 32 -102 Q 14 -62 6 -28 Z" fill="#95BE64"/>
    <path d="M0 0 Q -2 -72 4 -120 Q 10 -72 6 -20 Z" fill="#84AC56"/>
    <path d="M0 0 Q -6 -30 -12 -58" stroke="#5C7F3B" stroke-width="2" fill="none" opacity="0.6"/>
  </g>`;
}

/* =========================================================
   СОДЕРЖИМОЕ СТРАНИЦ
   ========================================================= */

const N = 8;

const pageSVG = [];

/* --- 0. ОБЛОЖКА --- */
pageSVG[0] = svgWrap(`
  <defs>
    <linearGradient id="covbg" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0" stop-color="#B4423B"/>
      <stop offset="0.48" stop-color="#9C332E"/>
      <stop offset="1" stop-color="#7A231E"/>
    </linearGradient>
    <radialGradient id="covglow" cx="0.5" cy="0.45" r="0.62">
      <stop offset="0" stop-color="#D4715C" stop-opacity="0.5"/>
      <stop offset="1" stop-color="#7A231E" stop-opacity="0"/>
    </radialGradient>
  </defs>
  <rect width="400" height="480" fill="url(#covbg)"/>
  <rect width="400" height="480" fill="url(#covglow)"/>

  <g stroke="#E9C98F" fill="none" opacity="0.42">
    <rect x="16" y="16" width="368" height="448" stroke-width="1.6"/>
    <rect x="24" y="24" width="352" height="432" stroke-width="0.7"/>
  </g>
  <g fill="#E9C98F" opacity="0.55">
    <circle cx="16" cy="16" r="3.2"/><circle cx="384" cy="16" r="3.2"/>
    <circle cx="16" cy="464" r="3.2"/><circle cx="384" cy="464" r="3.2"/>
    <circle cx="200" cy="16" r="2"/><circle cx="200" cy="464" r="2"/>
  </g>

  <text x="200" y="112" text-anchor="middle" font-family="Georgia,'Times New Roman',serif"
        font-size="15" fill="#EBCB94" letter-spacing="3" font-style="italic">Русская народная сказка</text>

  <text x="200" y="182" text-anchor="middle" font-family="Georgia,'Times New Roman',serif"
        font-size="62" font-weight="bold" fill="#F7E2B4" letter-spacing="7">РЕПКА</text>

  <g stroke="#E9C98F" stroke-width="1.2" opacity="0.55" fill="none">
    <path d="M112 200 L152 200"/><path d="M248 200 L288 200"/>
  </g>
  <g fill="#E9C98F" opacity="0.75">
    <path d="M200 194 l 4 6 -4 6 -4 -6 z"/>
  </g>

  <g transform="translate(200,336)">
    <ellipse cx="0" cy="76" rx="86" ry="12" fill="#3A0F0B" opacity="0.35"/>
    <path d="M0 -22 Q -34 -74 -58 -118 Q -22 -84 -8 -46 Z" fill="#7FA653"/>
    <path d="M0 -22 Q 34 -74 58 -118 Q 22 -84 8 -46 Z" fill="#8DB55D"/>
    <path d="M0 -22 Q -14 -84 -18 -132 Q -4 -80 3 -38 Z" fill="#6E9448"/>
    <path d="M0 -22 Q 16 -78 26 -124 Q 10 -76 4 -36 Z" fill="#95BE64"/>
    <path d="M0 -22 Q -2 -90 3 -140 Q 8 -88 5 -30 Z" fill="#84AC56"/>
    <path d="M-48 0 Q -54 50 0 70 Q 54 50 48 0 Q 0 -16 -48 0 Z" fill="#F2A65A"/>
    <path d="M-48 0 Q -54 50 0 70 Q 26 60 36 44 Q 4 44 -18 12 Q -30 -3 -48 0 Z" fill="#E8914A" opacity="0.55"/>
    <path d="M-4 66 Q -2 82 1 90" stroke="#D9823F" stroke-width="3" fill="none" stroke-linecap="round"/>
    <ellipse cx="-18" cy="22" rx="10" ry="17" fill="#FFD79A" opacity="0.3" transform="rotate(20 -18 22)"/>
  </g>

  <text x="200" y="440" text-anchor="middle" font-family="Georgia,serif"
        font-size="11.5" fill="#E9C98F" opacity="0.68" letter-spacing="4">ОТКРОЙТЕ КНИГУ</text>
`);

/* --- 1. ПОСАДИЛ ДЕД РЕПКУ --- */
pageSVG[1] = svgWrap(`
  ${landscape(1,'#FDEBCB','#F6CF97','#CFAE7C','#AE8F60','#94B36E','#7C9C5A',322,74,30)}
  ${grassTufts(1)}
  <ellipse cx="248" cy="406" rx="90" ry="25" fill="#8A6238"/>
  <ellipse cx="248" cy="401" rx="90" ry="25" fill="#A67C4E"/>
  <g stroke="#6E4C2C" stroke-width="2" opacity="0.55" fill="none" stroke-linecap="round">
    <path d="M182 397 Q 248 389 314 397"/>
    <path d="M188 407 Q 248 399 308 407"/>
    <path d="M196 417 Q 248 409 300 417"/>
  </g>
  <ellipse cx="248" cy="400" rx="7" ry="4.5" fill="#D9B44A" transform="rotate(-25 248 400)"/>
  <path d="M252 396 Q 260 388 268 386" stroke="#C9A227" stroke-width="1.5" fill="none" opacity="0.7"/>
  ${grandpa(136, 398, 1, 0)}
  <g stroke="#7FA653" stroke-width="2.5" fill="none" stroke-linecap="round" opacity="0.85">
    <path d="M330 400 q 4 -16 9 -22"/><path d="M338 402 q 0 -17 5 -24"/>
    <path d="M346 404 q -2 -15 2 -23"/>
  </g>
`);

/* --- 2. ВЫРОСЛА РЕПКА --- */
pageSVG[2] = svgWrap(`
  ${landscape(2,'#FCE6C0','#F4C88E','#C9A876','#A98A5C','#94B36E','#7C9C5A',88,70,28)}
  ${grassTufts(2)}
  <ellipse cx="240" cy="394" rx="86" ry="26" fill="#8A6238"/>
  <ellipse cx="240" cy="388" rx="86" ry="26" fill="#A67C4E"/>
  ${turnipTops(240, 386, 1.72, 0)}
  <path d="M200 388 Q 240 372 280 388 Q 240 400 200 388 Z" fill="#F2A65A" opacity="0.85"/>
  ${grandpa(108, 398, 0.82, -4)}
  <g opacity="0.9">
    <text x="140" y="252" font-family="Georgia,serif" font-size="34" font-weight="bold" fill="#C4453C">!</text>
    <text x="318" y="300" font-family="Georgia,serif" font-size="26" font-weight="bold" fill="#C4453C" opacity="0.7">!</text>
  </g>
`);

/* --- 3. ТЯНЕТ-ПОТЯНЕТ --- */
pageSVG[3] = svgWrap(`
  ${landscape(3,'#FBE3BC','#F2C086','#C9A876','#A98A5C','#94B36E','#7C9C5A',316,72,29)}
  ${grassTufts(3)}
  <ellipse cx="278" cy="400" rx="78" ry="23" fill="#8A6238"/>
  <ellipse cx="278" cy="395" rx="78" ry="23" fill="#A67C4E"/>
  ${turnipTops(278, 392, 1.5, 0)}
  ${grandpa(178, 402, 0.98, -20)}
  <g stroke="#7E9C86" stroke-width="0" fill="none"></g>
  <g opacity="0.55">
    <path d="M244 372 q -8 -14 -2 -26" stroke="#9C7A52" stroke-width="2.5" fill="none" stroke-linecap="round" stroke-dasharray="4 6"/>
  </g>
  <text x="112" y="286" font-family="Georgia,serif" font-size="17" fill="#8A6A48" font-style="italic" opacity="0.85">Ух!</text>
`);

/* --- 4. БАБКА --- */
pageSVG[4] = svgWrap(`
  ${landscape(4,'#FBE0B6','#F0BA7C','#C6A472','#A68659','#94B36E','#7C9C5A',312,70,28)}
  ${grassTufts(4)}
  <ellipse cx="300" cy="402" rx="72" ry="22" fill="#8A6238"/>
  <ellipse cx="300" cy="397" rx="72" ry="22" fill="#A67C4E"/>
  ${turnipTops(300, 394, 1.42, 0)}
  ${grandpa(196, 404, 0.86, -18)}
  ${grandma(124, 406, 0.86, -14)}
  <text x="86" y="290" font-family="Georgia,serif" font-size="15" fill="#8A6A48" font-style="italic" opacity="0.85">Раз!</text>
  <text x="176" y="268" font-family="Georgia,serif" font-size="15" fill="#8A6A48" font-style="italic" opacity="0.85">Два!</text>
`);

/* --- 5. ВНУЧКА, ЖУЧКА И КОШКА --- */
pageSVG[5] = svgWrap(`
  ${landscape(5,'#FADCAF','#EFB573','#C3A16F','#A38356','#94B36E','#7C9C5A',308,68,27)}
  ${grassTufts(5)}
  <ellipse cx="330" cy="402" rx="62" ry="20" fill="#8A6238"/>
  <ellipse cx="330" cy="398" rx="62" ry="20" fill="#A67C4E"/>
  ${turnipTops(330, 396, 1.26, 0)}
  ${grandpa(214, 406, 0.74, -17)}
  ${grandma(160, 408, 0.74, -13)}
  ${girl(108, 408, 0.76, -10)}
  ${dog(66, 412, 0.82, -8)}
  ${cat(30, 414, 0.86, -6)}
`);

/* --- 6. МЫШКА — ВЫТЯНУЛИ РЕПКУ --- */
pageSVG[6] = svgWrap(`
  ${landscape(6,'#F9D69F','#EDAB62','#BE9A64','#9E7E4E','#96B56F','#7C9C5A',300,64,28)}
  ${grassTufts(6)}
  <g transform="translate(214,352) rotate(-22)">
    <ellipse cx="0" cy="34" rx="86" ry="12" fill="#3a2818" opacity="0.16"/>
    <path d="M-52 0 Q -58 46 0 66 Q 58 46 52 0 Q 0 -16 -52 0 Z" fill="#F2A65A"/>
    <path d="M-52 0 Q -58 46 0 66 Q 28 56 38 40 Q 6 40 -20 10 Q -32 -4 -52 0 Z" fill="#E8914A" opacity="0.55"/>
    <path d="M-4 62 Q -2 78 1 86" stroke="#D9823F" stroke-width="3" fill="none" stroke-linecap="round"/>
    <path d="M0 -14 Q -34 -58 -56 -92 Q -22 -64 -8 -34 Z" fill="#7FA653"/>
    <path d="M0 -14 Q 34 -58 56 -92 Q 22 -64 8 -34 Z" fill="#8DB55D"/>
    <path d="M0 -14 Q -2 -72 4 -104 Q 10 -72 6 -24 Z" fill="#84AC56"/>
  </g>
  ${grandpa(96, 410, 0.72, -14)}
  ${grandma(52, 412, 0.72, -10)}
  ${girl(14, 412, 0.72, -6)}
  ${mouse(340, 388, 1.15, 0)}
  <g opacity="0.9">
    <text x="176" y="226" font-family="Georgia,serif" font-size="15" fill="#8A6A48" font-style="italic">Ура!</text>
    <text x="196" y="250" font-family="Georgia,serif" font-size="12" fill="#8A6A48" font-style="italic" opacity="0.75">Вытянули репку!</text>
  </g>
`);

/* --- 7. ФИНАЛ --- */
pageSVG[7] = svgWrap(`
  <defs>
    <linearGradient id="sky7" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0" stop-color="#F8D3A0"/><stop offset="0.6" stop-color="#F3BE7F"/><stop offset="1" stop-color="#EDAE6C"/>
    </linearGradient>
    <linearGradient id="grs7" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0" stop-color="#9BB974"/><stop offset="1" stop-color="#7C9C5A"/>
    </linearGradient>
  </defs>
  <rect width="400" height="480" fill="url(#sky7)"/>
  ${sun(200, 150, 46)}
  <g opacity="0.5" stroke="#F5C98A" stroke-width="2" fill="none">
    <circle cx="200" cy="150" r="72"/><circle cx="200" cy="150" r="96"/>
  </g>
  ${cloud(84, 96, .9, .75)}
  ${cloud(320, 84, .8, .7)}
  <path d="M0 300 Q 70 262 145 292 Q 220 322 295 292 Q 350 270 400 300 L400 480 L0 480 Z" fill="#C6A470"/>
  <path d="M0 336 Q 90 306 180 332 Q 275 358 400 326 L400 480 L0 480 Z" fill="#A68659"/>
  <path d="M0 372 Q 100 350 200 366 Q 300 382 400 360 L400 480 L0 480 Z" fill="url(#grs7)"/>
  ${grassTufts(7)}
  <g transform="translate(200,404)">
    <ellipse cx="0" cy="30" rx="72" ry="10" fill="#3a2818" opacity="0.14"/>
    <path d="M-44 0 Q -48 38 0 54 Q 48 38 44 0 Q 0 -13 -44 0 Z" fill="#F2A65A"/>
    <path d="M-44 0 Q -48 38 0 54 Q 22 46 32 32 Q 4 34 -16 8 Q -28 -3 -44 0 Z" fill="#E8914A" opacity="0.5"/>
    <path d="M0 -12 Q -28 -50 -46 -80 Q -18 -56 -6 -30 Z" fill="#7FA653"/>
    <path d="M0 -12 Q 28 -50 46 -80 Q 18 -56 6 -30 Z" fill="#8DB55D"/>
    <path d="M0 -12 Q -2 -62 3 -90 Q 8 -62 5 -20 Z" fill="#84AC56"/>
  </g>
  ${grandpa(62, 428, 0.62, -8)}
  ${grandma(28, 430, 0.62, -4)}
  ${girl(104, 430, 0.62, 4)}
  ${dog(144, 432, 0.68, 2)}
  ${cat(316, 428, 0.7, 0)}
  ${mouse(276, 424, 0.95, 0)}

  <g>
    <text x="200" y="196" text-anchor="middle" font-family="Georgia,'Times New Roman',serif"
          font-size="26" font-weight="bold" fill="#7A3A22" letter-spacing="1.5">Тут и сказке конец</text>
    <text x="200" y="226" text-anchor="middle" font-family="Georgia,serif"
          font-size="13" fill="#8A5A38" opacity="0.85" font-style="italic" letter-spacing="1">а кто слушал — молодец</text>
    <g stroke="#A9703F" stroke-width="1.2" opacity="0.55" fill="none">
      <path d="M148 244 L180 244"/><path d="M220 244 L252 244"/>
    </g>
    <path d="M200 240 l 4 6 -4 6 -4 -6 z" fill="#A9703F" opacity="0.7"/>
  </g>
`);

/* =========================================================
   ТЕКСТЫ
   ========================================================= */
const pageText = [
  '',
  'Посадил дед репку и приговаривает: «Расти, репка, большая-пребольшая!»',
  'Выросла репка большая-пребольшая. Стал дед репку из земли тащить.',
  'Тянет-потянет — вытянуть не может! Позвал дед бабку.',
  'Бабка за дедку, дедка за репку. Тянут-потянут — вытянуть не могут!',
  'Позвали внучку, Жучку и кошку. Тянут-потянут — вытянуть не могут!',
  'Прибежала мышка. Мышка за кошку, кошка за Жучку, Жучка за внучку, внучка за бабку, бабка за дедку, дедка за репку — тянут-потянут, вытянули репку!',
  ''
];

/* =========================================================
   СБОРКА КНИГИ
   ========================================================= */
const bookEl = document.getElementById('book');
const dotsEl = document.getElementById('dots');
const hintEl = document.getElementById('hint');

const backOrn = `<svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">
  <g fill="none" stroke="#B08A5A" stroke-width="1.4">
    <circle cx="100" cy="100" r="58"/>
    <circle cx="100" cy="100" r="44"/>
    <path d="M100 42 l 12 20 -12 20 -12 -20 z"/>
    <path d="M100 158 l 12 -20 -12 -20 -12 20 z"/>
    <path d="M42 100 l 20 12 20 -12 -20 -12 z"/>
    <path d="M158 100 l -20 12 -20 -12 20 -12 z"/>
  </g>
</svg>`;

const pagesEl = [];

for (let i = 0; i < N; i++) {
  const el = document.createElement('div');
  el.className = 'page';
  el.dataset.i = i;

  const txt = pageText[i]
    ? `<div class="txt">${pageText[i]}</div>`
    : '';

  el.innerHTML = `
    <div class="face front">
      ${pageSVG[i]}
      ${txt}
    </div>
    <div class="face back">
      <div class="back-orn">${backOrn}</div>
    </div>
  `;

  bookEl.appendChild(el);
  pagesEl.push(el);
}

/* точки-индикаторы */
for (let i = 0; i < N; i++) {
  const d = document.createElement('div');
  d.className = 'dot' + (i === 0 ? ' on' : '');
  dotsEl.appendChild(d);
}
const dots = Array.from(dotsEl.children);

/* =========================================================
   СОСТОЯНИЕ
   ========================================================= */
let current = 0;        // индекс страницы, лежащей справа сверху
let flippingEl = null;
let drag = null;

function updateDots(){
  dots.forEach((d,i)=> d.classList.toggle('on', i === current));
}

function updateZ(){
  for (let i = 0; i < N; i++){
    const p = pagesEl[i];
    if (p === flippingEl){ p.style.zIndex = 600; continue; }
    if (i < current){ p.style.zIndex = 100 + i; }
    else { p.style.zIndex = 300 - i; }
  }
}

function setTransform(el, deg){
  el.style.transform = 'rotateY(' + (-deg) + 'deg)';
}

/* =========================================================
   ЗВУК ШЕЛЕСТА (Web Audio API)
   ========================================================= */
let audioCtx = null;
let noiseBuffer = null;

function ensureAudio(){
  if (!audioCtx){
    const AC = window.AudioContext || window.webkitAudioContext;
    if (!AC) return;
    audioCtx = new AC();
    // заранее генерируем буфер белого шума
    const len = Math.floor(audioCtx.sampleRate * 0.6);
    noiseBuffer = audioCtx.createBuffer(1, len, audioCtx.sampleRate);
    const data = noiseBuffer.getChannelData(0);
    for (let i = 0; i < len; i++) data[i] = Math.random() * 2 - 1;
  }
  if (audioCtx.state === 'suspended') audioCtx.resume();
}

function playFlip(intensity){
  if (!audioCtx || !noiseBuffer) return;
  const ctx = audioCtx;
  const now = ctx.currentTime;
  const dur = 0.30;
  const k = Math.max(0.25, Math.min(1.2, intensity || 1));

  const src = ctx.createBufferSource();
  src.buffer = noiseBuffer;

  const bp = ctx.createBiquadFilter();
  bp.type = 'bandpass';
  bp.Q.value = 0.9;
  bp.frequency.setValueAtTime(900, now);
  bp.frequency.exponentialRampToValueAtTime(3400, now + 0.07);
  bp.frequency.exponentialRampToValueAtTime(1100, now + dur);

  const hp = ctx.createBiquadFilter();
  hp.type = 'highpass';
  hp.frequency.value = 420;

  const lp = ctx.createBiquadFilter();
  lp.type = 'lowpass';
  lp.frequency.value = 6800;

  const gain = ctx.createGain();
  const peak = 0.16 * k;
  gain.gain.setValueAtTime(0.0001, now);
  gain.gain.exponentialRampToValueAtTime(peak, now + 0.028);
  gain.gain.exponentialRampToValueAtTime(peak * 0.55, now + 0.10);
  gain.gain.exponentialRampToValueAtTime(0.0001, now + dur);

  src.connect(bp);
  bp.connect(hp);
  hp.connect(lp);
  lp.connect(gain);
  gain.connect(ctx.destination);

  src.start(now);
  src.stop(now + dur + 0.02);

  // лёгкий "щелчок" бумаги
  const click = ctx.createOscillator();
  const cg = ctx.createGain();
  click.type = 'triangle';
  click.frequency.setValueAtTime(220, now);
  click.frequency.exponentialRampToValueAtTime(90, now + 0.09);
  cg.gain.setValueAtTime(0.0001, now);
  cg.gain.exponentialRampToValueAtTime(0.035 * k, now + 0.012);
  cg.gain.exponentialRampToValueAtTime(0.0001, now + 0.13);
  click.connect(cg);
  cg.connect(ctx.destination);
  click.start(now);
  click.stop(now + 0.15);
}

/* =========================================================
   АНИМАЦИЯ ПЕРЕЛИСТЫВАНИЯ
   ========================================================= */
function flipTo(idx, forward){
  const el = pagesEl[idx];
  if (!el) return;

  flippingEl = el;
  el.classList.add('animate');
  void el.offsetWidth;

  el.style.transform = forward ? 'rotateY(-180deg)' : 'rotateY(0deg)';
  playFlip(forward ? 1.0 : 0.75);

  current = forward ? idx + 1 : idx;
  updateDots();
  updateZ();

  clearTimeout(el._t);
  el._t = setTimeout(() => {
    el.classList.remove('animate');
    if (flippingEl === el) flippingEl = null;
    updateZ();
  }, 720);
}

function goNext(){
  if (drag || current >= N - 1) return;
  flipTo(current, true);
}
function goPrev(){
  if (drag || current <= 0) return;
  flipTo(current - 1, false);
}

/* =========================================================
   ПЕРЕТАСКИВАНИЕ
   ========================================================= */
function onDown(e){
  if (drag) return;
  if (e.button !== undefined && e.button > 0) return;
  if (e.target.closest('.dots')) return;

  ensureAudio();

  const r = bookEl.getBoundingClientRect();
  const relX = (e.clientX - r.left) / r.width;

  let dir;
  if (current <= 0) dir = 'fwd';
  else if (current >= N - 1) dir = 'back';
  else dir = relX < 0.5 ? 'back' : 'fwd';

  const idx = dir === 'fwd' ? current : current - 1;
  if (idx < 0 || idx >= N) return;

  const el = pagesEl[idx];
  el.classList.remove('animate');

  drag = {
    dir: dir,
    idx: idx,
    el: el,
    startX: e.clientX,
    w: r.width,
    startT: performance.now(),
    angle: dir === 'fwd' ? 0 : 180,
    moved: false
  };

  flippingEl = el;
  updateZ();

  hintEl.classList.add('hide');
}

function onMove(e){
  if (!drag) return;
  e.preventDefault();

  const dx = drag.dir === 'fwd'
    ? (drag.startX - e.clientX)
    : (e.clientX - drag.startX);

  if (Math.abs(e.clientX - drag.startX) > 4) drag.moved = true;

  let p = dx / (drag.w * 0.85);
  p = Math.max(0, Math.min(1, p));

  let angle = p * 180;
  if (drag.dir === 'back') angle = 180 - angle;

  drag.angle = angle;
  setTransform(drag.el, angle);
}

function onUp(e){
  if (!drag) return;
  const d = drag;
  drag = null;

  const dt = Math.max(performance.now() - d.startT, 1);
  const dx = d.dir === 'fwd'
    ? (d.startX - e.clientX)
    : (e.clientX - d.startX);
  const speed = dx / dt; // px / ms

  const progress = d.dir === 'fwd'
    ? d.angle / 180
    : (180 - d.angle) / 180;

  if (!d.moved){
    // обычный клик — возвращаем как было
    d.el.classList.add('animate');
    d.el.style.transform = d.dir === 'fwd' ? 'rotateY(0deg)' : 'rotateY(-180deg)';
    if (flippingEl === d.el) flippingEl = null;
    updateZ();
    clearTimeout(d.el._t);
    d.el._t = setTimeout(()=>{ d.el.classList.remove('animate'); }, 400);
    return;
  }

  const complete = progress > 0.5 || (speed > 0.55 && dt < 400);

  d.el.classList.add('animate');

  if (complete){
    d.el.style.transform = d.dir === 'fwd' ? 'rotateY(-180deg)' : 'rotateY(0deg)';
    playFlip(1.0);
    if (d.dir === 'fwd') current = d.idx + 1;
    else current = d.idx;
    updateDots();
  } else {
    d.el.style.transform = d.dir === 'fwd' ? 'rotateY(0deg)' : 'rotateY(-180deg)';
    playFlip(0.45);
  }

  updateZ();

  clearTimeout(d.el._t);
  d.el._t = setTimeout(() => {
    d.el.classList.remove('animate');
    if (flippingEl === d.el) flippingEl = null;
    updateZ();
  }, 730);
}

/* =========================================================
   КЛАВИАТУРА
   ========================================================= */
window.addEventListener('keydown', (e) => {
  if (e.key === 'ArrowRight' || e.key === 'ArrowDown' || e.key === ' '){
    e.preventDefault(); goNext();
  } else if (e.key === 'ArrowLeft' || e.key === 'ArrowUp'){
    e.preventDefault(); goPrev();
  }
});

/* =========================================================
   ИНИЦИАЛИЗАЦИЯ
   ========================================================= */
function resize(){
  const w = bookEl.getBoundingClientRect().width;
  const fs = Math.max(11, Math.min(17, w * 0.047));
  bookEl.style.setProperty('--fs', fs.toFixed(2) + 'px');
}

window.addEventListener('pointerdown', onDown, { passive: true });
window.addEventListener('pointermove', onMove, { passive: false });
window.addEventListener('pointerup', onUp);
window.addEventListener('pointercancel', onUp);
window.addEventListener('resize', resize);
window.addEventListener('orientationchange', () => setTimeout(resize, 120));
document.addEventListener('gesturestart', (e)=> e.preventDefault());

resize();
updateZ();
updateDots();

setTimeout(() => hintEl.classList.add('hide'), 6000);
setTimeout(() => hintEl.classList.add('hide'), 6000);

})();
</script>
</body>
</html>
