# New-pickleball-game-<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Dink & Drive — Pickleball</title>
<style>
  :root{
    --court-blue:#1f6fa8;
    --court-blue-dark:#155580;
    --kitchen:#2a86bf;
    --line:#f4f1ea;
    --out:#2f3e35;
    --accent:#e8ff4d;
    --ball:#dfff3a;
    --ink:#0d1512;
    --panel:#0d1a16cc;
  }
  *{box-sizing:border-box;}
  html,body{
    margin:0;padding:0;height:100%;overflow:hidden;
    background:radial-gradient(ellipse at 50% 0%, #12241d 0%, #060b09 70%);
    font-family:'Segoe UI',system-ui,-apple-system,sans-serif;
    color:var(--line);
    touch-action:none;
    -webkit-user-select:none;user-select:none;
  }
  #wrap{
    position:relative;width:100%;height:100%;
    display:flex;flex-direction:column;align-items:center;justify-content:center;
    gap:10px;
  }
  #stage{
    position:relative;
    display:flex;align-items:center;justify-content:center;
    max-width:100%;
    flex:0 1 auto;
    min-height:0;
  }
  canvas{
    display:block;
    background:#000;
    box-shadow:0 20px 60px rgba(0,0,0,.6);
    max-width:100%;max-height:100%;
    touch-action:none;
  }
  #hud{
    position:absolute;top:48px;left:0;right:0;
    display:flex;justify-content:space-between;align-items:flex-start;
    padding:0 10px;pointer-events:none;
    font-variant-numeric:tabular-nums;
  }
  .hud-box{
    background:var(--panel);
    border:1px solid #3a5a4c;
    border-radius:10px;
    padding:5px 10px;
    min-width:70px;
    box-sizing:border-box;
    backdrop-filter:blur(4px);
  }
  .score-big{
    font-size:22px;font-weight:800;letter-spacing:1px;
    color:var(--accent);
    text-shadow:0 0 12px rgba(232,255,77,.4);
  }
  .score-label{font-size:9px;text-transform:uppercase;letter-spacing:2px;opacity:.7;margin-bottom:1px;}
  .server-dot{
    display:inline-block;width:8px;height:8px;border-radius:50%;
    background:var(--accent);margin-left:6px;
    box-shadow:0 0 8px var(--accent);
    vertical-align:middle;
  }
  #center-msg{
    position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);
    text-align:center;pointer-events:none;
  }
  #overlay{
    position:fixed;inset:0;
    display:flex;align-items:center;justify-content:center;
    background:rgba(6,11,9,.86);
    backdrop-filter:blur(6px);
    z-index:20;
  }
  .card{
    max-width:500px;width:92%;
    background:linear-gradient(160deg,#0f2019,#0a1512);
    border:1px solid #2f5344;
    border-radius:18px;
    padding:32px 28px;
    text-align:center;
    box-shadow:0 30px 80px rgba(0,0,0,.5);
  }
  h1{
    font-size:30px;margin:0 0 6px;letter-spacing:.5px;
    color:var(--accent);
    font-weight:800;
  }
  .sub{opacity:.65;font-size:13px;margin-bottom:22px;letter-spacing:.5px;}
  .rules{
    text-align:left;font-size:13px;line-height:1.7;
    background:#0006;border-radius:10px;padding:14px 16px;
    margin-bottom:20px;border:1px solid #234034;
  }
  .rules b{color:var(--accent);}
  button{
    font-family:inherit;font-weight:700;font-size:15px;
    background:var(--accent);color:#10200f;
    border:none;border-radius:10px;
    padding:13px 28px;cursor:pointer;
    letter-spacing:.5px;
    transition:transform .12s ease, box-shadow .12s ease;
    box-shadow:0 8px 20px rgba(232,255,77,.25);
  }
  button:active{transform:scale(.96);}
  .diffrow{display:flex;gap:8px;justify-content:center;margin-bottom:18px;flex-wrap:wrap;}
  .diffbtn{
    flex:1;min-width:70px;background:#132a20;color:var(--line);
    border:1px solid #2f5344;border-radius:8px;
    padding:9px 6px;font-size:11px;font-weight:600;cursor:pointer;
  }
  .diffbtn.active{background:var(--accent);color:#10200f;border-color:var(--accent);}
  .setting-label{
    text-align:left;font-size:10px;text-transform:uppercase;letter-spacing:2px;
    opacity:.55;font-weight:700;margin:14px 0 6px;
  }
  .charrow{display:flex;gap:8px;justify-content:center;margin-bottom:4px;}
  .char-swatch{
    width:34px;height:34px;border-radius:50%;cursor:pointer;
    border:3px solid transparent;box-sizing:border-box;
    transition:transform .12s, border-color .12s;
  }
  .char-swatch.active{border-color:#fff;transform:scale(1.12);}
  .modebtns{display:flex;gap:10px;margin-top:20px;}
  .modebtns button{flex:1;padding:13px 10px;}
  #tournamentBtn{background:#2a4d3a;color:var(--accent);border:1px solid var(--accent);box-shadow:none;}
  #pauseBtn{
    position:absolute;top:8px;left:10px;z-index:15;
    min-width:70px;height:30px;border-radius:8px;
    box-sizing:border-box;
    background:var(--panel);border:1px solid #3a5a4c;
    color:var(--line);font-size:14px;
    display:none;align-items:center;justify-content:center;
    cursor:pointer;
  }
  #pauseOverlay{
    display:none;position:fixed;inset:0;z-index:30;
    align-items:center;justify-content:center;
    background:rgba(6,11,9,.86);backdrop-filter:blur(6px);
  }
  #windIndicator{
    position:absolute;top:8px;right:10px;z-index:5;
    min-width:70px;box-sizing:border-box;
    background:var(--panel);border:1px solid #3a5a4c;border-radius:8px;
    padding:3px 9px;text-align:center;display:none;
    pointer-events:none;
  }
  #windIndicator .wind-label{font-size:9px;letter-spacing:1.5px;opacity:.6;text-transform:uppercase;}
  #windIndicator .wind-arrow{font-size:16px;color:var(--accent);display:inline-block;}
  #windIndicator .wind-speed{font-size:11px;font-weight:700;}
  #settingsBtn{
    position:fixed;top:10px;right:10px;z-index:50;
    width:34px;height:34px;border-radius:9px;
    background:var(--panel);border:1px solid #3a5a4c;
    color:var(--line);font-size:16px;
    display:flex;align-items:center;justify-content:center;
    cursor:pointer;
  }
  #settingsOverlay{
    display:none;position:fixed;inset:0;z-index:55;
    align-items:center;justify-content:center;
    background:rgba(6,11,9,.86);backdrop-filter:blur(6px);
  }
  .account-field{
    width:100%;box-sizing:border-box;padding:11px 12px;
    background:#0d1a16;border:1px solid #3a5a4c;border-radius:9px;
    color:var(--line);font-size:14px;margin-bottom:10px;
    font-family:inherit;
  }
  .account-field::placeholder{color:#6b8378;}
  .stat-grid{
    display:grid;grid-template-columns:1fr 1fr;gap:8px;
    margin:14px 0;text-align:left;
  }
  .stat-box{
    background:#0d1a16aa;border:1px solid #2f5344;border-radius:9px;
    padding:9px 11px;
  }
  .stat-box .stat-num{font-size:19px;font-weight:800;color:var(--accent);}
  .stat-box .stat-lbl{font-size:9px;text-transform:uppercase;letter-spacing:1px;opacity:.6;}
  .stat-breakdown{
    text-align:left;font-size:11px;line-height:1.9;
    background:#0d1a16aa;border:1px solid #2f5344;border-radius:9px;
    padding:10px 12px;margin-bottom:14px;
  }
  .stat-breakdown b{color:var(--accent);}
  .trophy-emoji{font-size:56px;margin-bottom:6px;}
  .ncaa-bracket{
    display:flex;gap:12px;justify-content:center;align-items:stretch;
    margin:14px -8px;overflow-x:auto;padding:2px 8px 8px;
  }
  .ncaa-round{
    display:flex;flex-direction:column;justify-content:space-around;
    gap:10px;min-width:108px;flex-shrink:0;
  }
  .ncaa-round-title{
    font-size:9px;text-transform:uppercase;letter-spacing:1px;
    opacity:.55;text-align:center;margin-bottom:2px;
  }
  .ncaa-match{
    display:flex;flex-direction:column;gap:3px;
    background:#0d1a16aa;border:1px solid #2f5344;border-radius:8px;
    padding:5px 7px;
  }
  .ncaa-slot{
    display:flex;align-items:center;gap:5px;font-size:10px;
    opacity:.5;padding:2px 0;white-space:nowrap;
  }
  .ncaa-slot.you{font-weight:800;color:var(--accent);opacity:1;}
  .ncaa-slot.winner{opacity:1;font-weight:700;}
  .ncaa-slot.unknown{opacity:.35;font-style:italic;}
  .ncaa-dot{
    width:10px;height:10px;border-radius:50%;flex-shrink:0;
    border:1px solid rgba(255,255,255,.3);
  }
  .menu-link{
    margin-top:14px;font-size:12px;opacity:.55;text-decoration:underline;
    cursor:pointer;letter-spacing:.3px;
  }
  .menu-link:hover{opacity:.85;}
  #point-msg{
    position:absolute;left:50%;bottom:26px;transform:translateX(-50%);
    font-size:18px;font-weight:800;color:var(--accent);
    text-shadow:0 0 20px rgba(232,255,77,.5);
    background:var(--panel);border:1px solid #3a5a4c;border-radius:20px;
    padding:5px 16px;
    opacity:0;transition:opacity .2s;
    pointer-events:none;white-space:nowrap;
  }
  #controls-hint{
    position:absolute;bottom:10px;left:0;right:0;
    text-align:center;font-size:11px;opacity:.5;letter-spacing:.5px;
    pointer-events:none;
  }

  /* ---- Touch control bar (below the game) ---- */
  #touchControls{
    display:none;
    width:100%;
    max-width:900px;
    flex:0 0 auto;
    align-items:stretch;
    justify-content:center;
    gap:12px;
    padding:0 14px 10px;
  }
  body.is-touch #touchControls{ display:flex; }
  body.is-touch #controls-hint{ display:none; }

  #movePad{
    position:relative;
    flex:1 1 auto;
    max-width:220px;
    height:112px;
    background:linear-gradient(160deg,#132a20,#0c1a14);
    border:1px solid #2f5344;
    border-radius:16px;
    overflow:hidden;
  }
  #movePad .pad-label{
    position:absolute;top:8px;left:0;right:0;
    text-align:center;font-size:10px;letter-spacing:2px;
    text-transform:uppercase;opacity:.55;font-weight:700;
    pointer-events:none;
  }
  #movePad .pad-arrows{
    position:absolute;inset:0;
    display:flex;align-items:center;justify-content:center;
    pointer-events:none;
    opacity:.35;
    font-size:20px;
    color:var(--line);
  }
  #movePad .pad-arrows span{position:absolute;}
  #movePad .arrow-up{top:24px;left:50%;transform:translateX(-50%);}
  #movePad .arrow-down{bottom:8px;left:50%;transform:translateX(-50%);}
  #movePad .arrow-left{left:10px;top:50%;transform:translateY(-50%);}
  #movePad .arrow-right{right:10px;top:50%;transform:translateY(-50%);}
  #padStick{
    position:absolute;width:46px;height:46px;border-radius:50%;
    background:var(--accent);
    box-shadow:0 0 18px rgba(232,255,77,.5);
    left:50%;top:58%;
    transform:translate(-50%,-50%);
    transition:transform .08s ease;
    pointer-events:none;
  }

  #jumpBtn{
    position:relative;
    flex:1 1 auto;
    max-width:220px;
    height:112px;
    background:linear-gradient(160deg,#132a20,#0c1a14);
    border:1px solid #2f5344;
    border-radius:16px;
    display:flex;flex-direction:column;align-items:center;justify-content:center;
    gap:4px;
    color:var(--line);
    -webkit-user-select:none;user-select:none;
    touch-action:none;
    transition:background .1s, border-color .1s, transform .08s;
  }
  #jumpBtn .jump-icon{font-size:26px;opacity:.75;}
  #jumpBtn .jump-label{font-size:12px;letter-spacing:1.5px;opacity:.6;font-weight:700;}
  #jumpBtn.pressed{
    background:var(--accent); border-color:var(--accent);
    transform:scale(0.96);
  }
  #jumpBtn.pressed .jump-icon, #jumpBtn.pressed .jump-label{ color:#10200f; opacity:1; }
  #jumpBtn.cooldown{ opacity:.4; }
  .power-bar{display:flex;gap:3px;justify-content:center;margin-top:6px;}
  .power-pip{
    width:12px;height:6px;border-radius:2px;
    background:#3a3a3a;border:1px solid #555;
    transition:background .15s, border-color .15s, box-shadow .15s;
  }
  .power-pip.filled{
    background:#ff9a2e;border-color:#ffc07a;
    box-shadow:0 0 5px rgba(255,154,46,.7);
  }

  @media (max-width:640px){
    .score-big{font-size:20px;}
    h1{font-size:24px;}
    #controls-hint{display:none;}
    #touchControls{padding:0 8px 8px;}
    #movePad,#jumpBtn{height:92px;}
  }
</style>
</head>
<body>
<div id="settingsBtn">⚙</div>
<div id="settingsOverlay">
  <div class="card" id="settingsCard"></div>
</div>

<div id="wrap">
  <div id="stage">
    <canvas id="game" width="900" height="600"></canvas>

    <div id="pauseBtn">☰</div>
    <div id="windIndicator">
      <div class="wind-label">Wind</div>
      <div><span class="wind-arrow" id="windArrow">➤</span> <span class="wind-speed" id="windSpeed">0 mph</span></div>
    </div>

    <div id="hud">
      <div class="hud-box">
        <div class="score-label">You</div>
        <div class="score-big" id="scoreYou">0<span class="server-dot" id="dotYou"></span></div>
      </div>
      <div class="hud-box" style="text-align:right;">
        <div class="score-label">CPU</div>
        <div class="score-big" id="scoreCPU">0<span class="server-dot" id="dotCPU"></span></div>
      </div>
    </div>

    <div id="point-msg">NICE SHOT</div>

    <div id="controls-hint">MOVE: Arrow Keys / WASD &nbsp;•&nbsp; SHIFT to jump &nbsp;•&nbsp; SPACE, or tap/swipe the COURT to hit &nbsp;•&nbsp; ball lands where your swipe ends</div>

    <div id="overlay">
      <div class="card">
        <h1>DINK &amp; DRIVE</h1>
        <div class="sub">a pickleball game</div>
        <div class="rules">
          Score to <b>11</b>, win by 2 — only the <b>server</b> scores. The
          ball lands exactly where you end your swipe on the court. Jump to
          reach high shots. No volleys while standing in the kitchen.
        </div>

        <div class="setting-label">Character</div>
        <div class="charrow" id="charrow"></div>

        <div class="setting-label">Venue</div>
        <div class="diffrow" id="venuerow">
          <div class="diffbtn active" data-v="indoor">Indoor</div>
          <div class="diffbtn" data-v="outdoor">Outdoor (wind)</div>
          <div class="diffbtn" data-v="moon">🌙 Moon</div>
        </div>

        <div class="setting-label">CPU Difficulty</div>
        <div class="diffrow" id="diffrow">
          <div class="diffbtn" data-d="easy">Easy</div>
          <div class="diffbtn active" data-d="mid">Rec Player</div>
          <div class="diffbtn" data-d="hard">Hard</div>
          <div class="diffbtn" data-d="superhard">Super Hard</div>
        </div>

        <div class="modebtns">
          <button id="startBtn">Quick Play</button>
          <button id="tournamentBtn">Tournament</button>
        </div>
        <div class="modebtns" style="margin-top:8px;">
          <button id="practiceBtn" style="background:#2a4d3a;color:var(--accent);border:1px solid var(--accent);box-shadow:none;">🏋️ Practice Mode</button>
        </div>
    </div>
  </div>
  </div><!-- /stage -->

  <div id="pauseOverlay">
    <div class="card">
      <h1>PAUSED</h1>
      <div class="sub">take a breather</div>
      <div class="modebtns" style="margin-top:6px;">
        <button id="resumeBtn">Resume</button>
        <button id="homeBtn" style="background:#2a4d3a;color:var(--accent);border:1px solid var(--accent);box-shadow:none;">Home</button>
      </div>
    </div>
  </div>

  <div id="touchControls">
    <div id="movePad">
      <div class="pad-label">Drag to move</div>
      <div class="pad-arrows">
        <span class="arrow-up">▲</span>
        <span class="arrow-down">▼</span>
        <span class="arrow-left">◀</span>
        <span class="arrow-right">▶</span>
      </div>
      <div id="padStick"></div>
    </div>
    <div id="jumpBtn">
      <div class="jump-icon">⤴</div>
      <div class="jump-label">JUMP</div>
      <div class="power-bar" id="powerBar"></div>
    </div>
  </div>
</div>

<script>
(function(){
"use strict";

const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
const W = canvas.width, H = canvas.height;
const homeCardHTML = document.querySelector('#overlay .card').innerHTML; // pristine copy for "back to menu"

// ---------- Court geometry (top-down, perspective-shaded) ----------
// Court coordinate system: x in [0, COURT_W], y in [0, COURT_L] (y=0 is player's baseline, y=COURT_L is CPU baseline)
const COURT_W = 20;      // feet, standard doubles court width used for pickleball (20x44)
const COURT_L = 44;
const KITCHEN_DEPTH = 7; // non-volley zone depth from net each side
const NET_Y = COURT_L/2;

// screen mapping: we render top-down with slight perspective (far side smaller)
const MARGIN_X = 90, MARGIN_Y_TOP = 70, MARGIN_Y_BOT = 70;
function courtToScreen(x, y){
  // y=0 is the player's baseline (rendered at the BOTTOM of the screen, near the camera)
  // y=COURT_L is the opponent's baseline (rendered near the top, receding into the distance)
  return courtToScreenP(x, y);
}
function scaleAt(y){
  return scaleAtP(y);
}
// ---- Real perspective camera: player's baseline at bottom of screen, far baseline recedes toward a vanishing point ----
const FAR_SCALE = 0.46;      // how much the far baseline shrinks vs the near one
const DEPTH_EXP = 0.58;      // <1 compresses far lines together, spreads near lines apart (true perspective feel)
const TOP_Y = 64;
const BOTTOM_Y = H - 56;
function perspT(y){
  const t = clamp(y / COURT_L, 0, 1);
  return Math.pow(t, DEPTH_EXP);
}
function courtToScreenP(x, y){
  const pt = perspT(y);
  const scale = 1 - pt * (1 - FAR_SCALE);
  const usableW = (W - MARGIN_X*2);
  const cx = W/2;
  const sx = cx + (x - COURT_W/2) * (usableW/COURT_W) * scale;
  const sy = BOTTOM_Y - pt * (BOTTOM_Y - TOP_Y);
  return {x:sx, y:sy};
}
function scaleAtP(y){
  const pt = perspT(y);
  return 1 - pt * (1 - FAR_SCALE);
}
// inverse of courtToScreenP: given a point on screen (canvas-internal pixels),
// solve for the court (x,y) it corresponds to. Used so a swipe's endpoint on
// screen maps to an exact landing spot on the court.
function screenToCourt(sx, sy){
  const pt = clamp((BOTTOM_Y - sy) / (BOTTOM_Y - TOP_Y), 0, 1);
  const y = COURT_L * Math.pow(pt, 1/DEPTH_EXP);
  const scale = 1 - pt * (1 - FAR_SCALE);
  const usableW = (W - MARGIN_X*2);
  const cx = W/2;
  const x = COURT_W/2 + (sx - cx) * COURT_W / (usableW * scale);
  return {x, y};
}

// ---------- Game state ----------
let state = 'menu'; // menu, serve, rally, point, gameover
let difficulty = 'mid';
let scoreYou = 0, scoreCPU = 0;
let serverIsYou = true;
let pointMsgTimer = 0;
let isPaused = false;
let playerColor = '#2d6cdf';
let venue = 'indoor'; // 'indoor' | 'outdoor'
let wind = {x: 0, speedMph: 0}; // wind.x is a lateral court accel (ft/s^2); regenerated each point outdoors
let gameMode = 'quick'; // 'quick' | 'tournament' | 'practice'
let practiceType = 'free'; // 'free' | 'target'
let practiceHasCPU = true;
let practiceDifficulty = 'mid';
let targetZone = null; // {x1,y1,x2,y2} -- current zone to aim for in target practice
let powerShotsLeft = 5;
const POWER_SHOTS_PER_RALLY = 5;
let tournamentRound = 0; // 0=QF, 1=SF, 2=Final (index into TOURNAMENT_ROUNDS)
const TOURNAMENT_ROUNDS = [
  {name:'Quarterfinal', diff:'easy'},
  {name:'Semifinal',    diff:'mid'},
  {name:'Final',        diff:'hard'},
];
// 8 distinct colors, evenly spread around the color wheel so no two players
// ever look alike, no matter which one you pick as your own character
const BRACKET_COLORS = Array.from({length:8},(_,i)=>{
  const hue = Math.round(i*(360/8));
  return `hsl(${hue},62%,55%)`;
});
const CHAR_COLORS = BRACKET_COLORS;
const OPPONENT_NAMES = ['Ray','Vic','Max','Nia','Zoe','Kai','Ash','Jo'];
function shuffle(arr){
  for(let i=arr.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [arr[i],arr[j]]=[arr[j],arr[i]]; }
  return arr;
}

// ---------- 8-player single-elimination bracket ----------
// slots 0-7 are the initial draw. QF pairs: (0,1) (2,3) (4,5) (6,7).
// SF group 0 = winners of QF0+QF1, SF group 1 = winners of QF2+QF3.
// Final = winner of SF0 vs winner of SF1.
let bracketPlayers = [];      // 8 entries: {name, color, isYou}
let youSlot = 0;
let qfWinner = [null,null,null,null]; // winner slot per QF match (index 0-3)
let sfWinner = [null,null];           // winner slot per SF match (index 0-1)
let finalWinnerSlot = null;           // champion's slot, once the Final is won
const QF_PAIRS = [[0,1],[2,3],[4,5],[6,7]];
function qfIndexForSlot(slot){ return Math.floor(slot/2); }
function siblingQFIndex(qfIdx){ return qfIdx%2===0 ? qfIdx+1 : qfIdx-1; }
function sfIndexForQF(qfIdx){ return Math.floor(qfIdx/2); }
function siblingSFIndex(sfIdx){ return 1-sfIdx; }

// ---------- Local account system (stored in this browser only -- no server,
// no password, just a simple named profile so stats can persist between
// sessions on this device) ----------
const ACCOUNTS_KEY = 'pb_accounts_v1';
const CURRENT_USER_KEY = 'pb_current_user_v1';
function loadAccounts(){
  try{ return JSON.parse(localStorage.getItem(ACCOUNTS_KEY)) || {}; }
  catch(e){ return {}; }
}
function saveAccounts(accounts){
  try{ localStorage.setItem(ACCOUNTS_KEY, JSON.stringify(accounts)); }catch(e){}
}
function blankStats(){
  return {
    createdAt: Date.now(),
    gamesPlayed: 0, gamesWon: 0,
    tournamentsPlayed: 0, tournamentsWon: 0,
    pointsFor: 0, pointsAgainst: 0,
    byVenue: { indoor:{played:0,won:0}, outdoor:{played:0,won:0}, moon:{played:0,won:0} },
    byDifficulty: { easy:{played:0,won:0}, mid:{played:0,won:0}, hard:{played:0,won:0}, superhard:{played:0,won:0} },
  };
}
function getCurrentUser(){
  return localStorage.getItem(CURRENT_USER_KEY) || null;
}
function setCurrentUser(name){
  if(name) localStorage.setItem(CURRENT_USER_KEY, name);
  else localStorage.removeItem(CURRENT_USER_KEY);
}
function getCurrentAccount(){
  const name = getCurrentUser();
  if(!name) return null;
  const accounts = loadAccounts();
  return accounts[name] || null;
}
// creates the account if it's new, or signs into it if it already exists
function loginOrCreateAccount(name){
  name = name.trim();
  if(!name) return {ok:false, msg:'Enter a name first.'};
  if(name.length>20) return {ok:false, msg:'Keep it under 20 characters.'};
  const accounts = loadAccounts();
  const isNew = !accounts[name];
  if(isNew) accounts[name] = blankStats();
  saveAccounts(accounts);
  setCurrentUser(name);
  return {ok:true, isNew};
}
function signOutAccount(){ setCurrentUser(null); }
// records the result of one played GAME (quick play, or a single tournament
// round) into the signed-in account's stats, if anyone's signed in
function recordGameStats(won, pointsFor, pointsAgainst){
  const name = getCurrentUser();
  if(!name) return;
  const accounts = loadAccounts();
  const acc = accounts[name];
  if(!acc) return;
  acc.gamesPlayed++;
  if(won) acc.gamesWon++;
  acc.pointsFor += pointsFor;
  acc.pointsAgainst += pointsAgainst;
  if(!acc.byVenue[venue]) acc.byVenue[venue] = {played:0,won:0};
  acc.byVenue[venue].played++;
  if(won) acc.byVenue[venue].won++;
  if(!acc.byDifficulty[difficulty]) acc.byDifficulty[difficulty] = {played:0,won:0};
  acc.byDifficulty[difficulty].played++;
  if(won) acc.byDifficulty[difficulty].won++;
  saveAccounts(accounts);
}
// records a full TOURNAMENT run's outcome (call once, when the run ends)
function recordTournamentStats(wonWholeThing){
  const name = getCurrentUser();
  if(!name) return;
  const accounts = loadAccounts();
  const acc = accounts[name];
  if(!acc) return;
  acc.tournamentsPlayed++;
  if(wonWholeThing) acc.tournamentsWon++;
  saveAccounts(accounts);
}

function setupBracket(){
  const colors = shuffle(BRACKET_COLORS.filter(c=>c!==playerColor));
  const names = shuffle([...OPPONENT_NAMES]);
  youSlot = Math.floor(Math.random()*8);
  bracketPlayers = [];
  let ci=0;
  for(let i=0;i<8;i++){
    if(i===youSlot) bracketPlayers.push({name:'You', color:playerColor, isYou:true});
    else bracketPlayers.push({name:names[ci], color:colors[ci], isYou:false}), ci++;
  }
  qfWinner = [null,null,null,null];
  sfWinner = [null,null];
  finalWinnerSlot = null;
  // round 1 pairings are always known from the start -- lock them in as "TBD"
  // until each match is actually played/simulated
}
function opponentSlotForRound(round){
  if(round===0){
    const qf = qfIndexForSlot(youSlot);
    const pair = QF_PAIRS[qf];
    return pair[0]===youSlot ? pair[1] : pair[0];
  }
  if(round===1){
    const sibling = siblingQFIndex(qfIndexForSlot(youSlot));
    return qfWinner[sibling]; // null if not yet revealed
  }
  const yourSF = sfIndexForQF(qfIndexForSlot(youSlot));
  return sfWinner[siblingSFIndex(yourSF)]; // null if not yet revealed
}
function resolveOtherMatchesForRound(round){
  if(round===0){
    const yourQF = qfIndexForSlot(youSlot);
    qfWinner[yourQF] = youSlot;
    for(let m=0;m<4;m++){
      if(qfWinner[m]==null){
        const pair = QF_PAIRS[m];
        qfWinner[m] = pair[Math.random()<0.5?0:1];
      }
    }
  } else if(round===1){
    const yourSF = sfIndexForQF(qfIndexForSlot(youSlot));
    sfWinner[yourSF] = youSlot;
    for(let s=0;s<2;s++){
      if(sfWinner[s]==null){
        const q0 = s*2, q1 = s*2+1;
        const pair = [qfWinner[q0], qfWinner[q1]];
        sfWinner[s] = pair[Math.random()<0.5?0:1];
      }
    }
  }
}
function cpuColor(){
  if(gameMode!=='tournament') return '#c0453a';
  const opp = opponentSlotForRound(tournamentRound);
  return opp!=null ? bracketPlayers[opp].color : '#c0453a';
}
function cpuOpponentName(round){
  const opp = opponentSlotForRound(round!=null?round:tournamentRound);
  return opp!=null ? bracketPlayers[opp].name : 'CPU';
}
let fireTrail = []; // recent court positions for the power-shot fire trail
const SHOT_PARAMS = {
  drive: {speedXY: 26, height: 2.0, arcMul: 1.0},
  dink:  {speedXY: 11, height: 1.1, arcMul: 0.55},
  lob:   {speedXY: 17, height: 7.5, arcMul: 2.1},
};
function lerp(a,b,t){ return a + (b-a)*t; }

// player & cpu positions in court coords
const player = {x: COURT_W/2, y: 4, speed: 15, reach: 3.0, swinging:0, jumpTimer:0, jumpCooldown:0};
const cpu = {x: COURT_W/2, y: COURT_L-4, speed: 15, beliefX: COURT_W/2, reach: 3.0, swinging:0, readError: 0};
const MOVE_SPEED_BASE = 8.5; // ft/s -- the shared top speed for both player and CPU
const NORMAL_REACH_Z = 6.0;  // how high you can hit a ball without jumping
const JUMP_REACH_Z_BASE = 13.5;   // how high you can hit a ball while jumping
const JUMP_DURATION = 0.68;  // seconds the raised reach lasts (slow-mo hang time)
const JUMP_COOLDOWN = 0.4;   // seconds before you can jump again
// Moon mode: lower gravity (floatier, slower-feeling ball), higher jumps,
// but everyone's footspeed is a little slower in a bulky low-g way.
function moveSpeed(){ return venue==='moon' ? MOVE_SPEED_BASE*0.82 : MOVE_SPEED_BASE; }
function jumpReachZ(){ return venue==='moon' ? JUMP_REACH_Z_BASE*1.7 : JUMP_REACH_Z_BASE; }
function jumpLiftMul(){ return venue==='moon' ? 1.7 : 1; }
function gravity(){ return venue==='moon' ? GRAVITY_BASE*0.42 : GRAVITY_BASE; }
// Moon shots still land on the exact same target (same "how far") -- they
// just take a slower, floatier pace to get there, which naturally gives
// them noticeably more hang time and arc along the way.
function ballPaceMul(){ return venue==='moon' ? 0.52 : 1; }
function moveToward(entity, tx, ty, speed, dt){
  const dx=tx-entity.x, dy=ty-entity.y;
  const dist=Math.hypot(dx,dy);
  const step=speed*dt;
  if(dist<=step || dist<1e-4){ entity.x=tx; entity.y=ty; }
  else { entity.x += dx/dist*step; entity.y += dy/dist*step; }
}

// ball: x,y court pos, z height (feet), vx,vy,vz
let ball = {x:COURT_W/2, y:4, z:0, vx:0, vy:0, vz:0, curve:0, inFlight:false, lastHitBy:null, bouncesSinceHit:0, bounceSideCounted:false, cpuWillMiss:false, resting:false, isServe:false, serveRequiredHigh:true, powerShot:false};
let outMarker = {active:false, x:0, y:0, timer:0};
// targetX/targetY: the exact court spot your swipe ended on -- the ball is
// aimed to land there. curve: -1..1 spin. power: 0 soft .. 1 hard (pace/arc).
let shotAim = {targetX: COURT_W/2, targetY: NET_Y+6, curve:0, power:0.55};
let pendingSwipe = null; // a completed swipe/tap waiting for the ball to come into reach
// kitchen momentum-rule watch: >0 means "this player just hit a legal volley
// from outside the kitchen -- if they step into the kitchen before this
// timer runs out, it's a fault." 0 means not currently being watched.
let momentumWatch = {player: 0, cpu: 0};
let autoHitCooldown = 0;

const GRAVITY_BASE = 32; // ft/s^2 (exaggerated a bit for feel)
const NET_HEIGHT = 2.8; // feet -- roughly a real pickleball net's center height

let keys = {};
window.addEventListener('keydown', e=>{
  keys[e.key.toLowerCase()] = true;
  if(e.key===' ') { e.preventDefault(); trySwing(); }
  if(e.key==='Shift') { e.preventDefault(); tryJump(); }
});
window.addEventListener('keyup', e=>{ keys[e.key.toLowerCase()] = false; });

// ---------- Touch controls (dedicated bar below the game) ----------
// Detect touch capability and reveal the control bar / hide the keyboard hint.
if('ontouchstart' in window || navigator.maxTouchPoints > 0){
  document.body.classList.add('is-touch');
}

// --- Movement pad: a virtual joystick. Drag from the pad center in any
// direction to move; the further you drag (up to the pad's radius), the
// faster the player moves that way. Release to stop. ---
const movePad = document.getElementById('movePad');
const padStick = document.getElementById('padStick');
let joy = {x:0, y:0, active:false}; // -1..1 on each axis
let joyTouchId = null;

function padVectorFromTouch(t){
  const r = movePad.getBoundingClientRect();
  const cx = r.left + r.width/2;
  const cy = r.top + r.height/2;
  const radius = Math.min(r.width, r.height)/2 - 8;
  let dx = t.clientX - cx;
  let dy = t.clientY - cy;
  const dist = Math.hypot(dx,dy);
  if(dist > radius){ dx = dx/dist*radius; dy = dy/dist*radius; }
  return {nx: dx/radius, ny: dy/radius, px: dx, py: dy};
}

movePad.addEventListener('touchstart', e=>{
  e.preventDefault();
  const t = e.changedTouches[0];
  joyTouchId = t.identifier;
  const v = padVectorFromTouch(t);
  joy.x = v.nx; joy.y = v.ny; joy.active = true;
  padStick.style.transform = `translate(calc(-50% + ${v.px}px), calc(-50% + ${v.py}px))`;
},{passive:false});
movePad.addEventListener('touchmove', e=>{
  e.preventDefault();
  for(const t of e.changedTouches){
    if(t.identifier !== joyTouchId) continue;
    const v = padVectorFromTouch(t);
    joy.x = v.nx; joy.y = v.ny;
    padStick.style.transform = `translate(calc(-50% + ${v.px}px), calc(-50% + ${v.py}px))`;
  }
},{passive:false});
function releasePad(e){
  for(const t of e.changedTouches){
    if(t.identifier !== joyTouchId) continue;
    joyTouchId = null; joy.x=0; joy.y=0; joy.active=false;
    padStick.style.transform = 'translate(-50%,-50%)';
  }
}
movePad.addEventListener('touchend', releasePad, {passive:false});
movePad.addEventListener('touchcancel', releasePad, {passive:false});

// --- Jump button: raises your reach temporarily so you can hit shots that
// would otherwise sail over your head. It doesn't move you sideways, and
// it's on a short cooldown so it can't just be held down. ---
const jumpBtn = document.getElementById('jumpBtn');
function tryJump(){
  if(state!=='rally' || player.jumpCooldown>0) return;
  player.jumpTimer = JUMP_DURATION;
  player.jumpCooldown = JUMP_DURATION + JUMP_COOLDOWN;
  jumpBtn.classList.add('pressed');
  setTimeout(()=>jumpBtn.classList.remove('pressed'), 150);
}
jumpBtn.addEventListener('touchstart', e=>{ e.preventDefault(); tryJump(); }, {passive:false});
jumpBtn.addEventListener('mousedown', ()=>{ tryJump(); });

// --- Swipe on the COURT itself to hit the ball. Any gesture -- a tap or a
// swipe in any direction -- swings as long as the ball is currently in your
// reach. A swipe's direction + length set where the shot goes and how deep;
// a curved swipe puts side-spin on the ball. A faint white trail traces your
// finger and fades out (or vanishes the instant the CPU returns the ball). ---
let aimGesture = null;
let swipeTrail = {points:[], timer:0}; // points are in canvas-internal coordinates

function clientToCanvas(clientX, clientY){
  const r = canvas.getBoundingClientRect();
  return {
    x: (clientX - r.left) * (W / r.width),
    y: (clientY - r.top) * (H / r.height)
  };
}

function beginAim(clientX, clientY){
  const p = clientToCanvas(clientX, clientY);
  p.t = performance.now();
  aimGesture = {startX:p.x, startY:p.y, points:[p]};
  swipeTrail.points = [p];
  swipeTrail.timer = 999; // stays visible while actively drawing
}
function moveAim(clientX, clientY){
  if(!aimGesture) return;
  const p = clientToCanvas(clientX, clientY);
  p.t = performance.now();
  aimGesture.points.push(p);
  if(aimGesture.points.length>40) aimGesture.points.shift();
  swipeTrail.points = aimGesture.points;
}
function endAim(clientX, clientY){
  if(!aimGesture) return;
  moveAim(clientX, clientY);
  const pts = aimGesture.points;
  const a=pts[0], b=pts[pts.length-1];
  const vx=b.x-a.x, vy=b.y-a.y;
  const len=Math.hypot(vx,vy);
  let signedCurve=0;
  if(len>18 && pts.length>2){
    // signed perpendicular deviation from the straight start->end path = curvature
    for(const q of pts){
      const dev = ((q.x-a.x)*vy - (q.y-a.y)*vx)/len;
      if(Math.abs(dev)>Math.abs(signedCurve)) signedCurve=dev;
    }
  }
  const curve = Math.abs(signedCurve/60) > 0.12 ? clamp(signedCurve/60, -1, 1) : 0;

  // the ball is aimed at the EXACT court spot where the gesture ended --
  // whether that's a tap (the tap point itself) or the release point of a
  // longer swipe. It always follows the true path of your finger.
  const landing = screenToCourt(b.x, b.y);
  const targetX = clamp(landing.x, 0.3, COURT_W-0.3);
  const targetY = clamp(landing.y, NET_Y+0.5, COURT_L-0.5);

  let power;
  if(len < 14){
    // a quick tap: no real speed to measure, so a moderate default pace
    power = 0.42;
  } else {
    // power comes from how FAST the finger moved, not how far it traveled --
    // and it's deliberately desensitized (sqrt curve + a floor) so ordinary
    // swipes land mid-power and only a genuinely fast flick maxes it out.
    const durationMs = Math.max(16, b.t - a.t);
    const speedPxPerSec = (len / durationMs) * 1000;
    const REF_SPEED = 1300; // px/sec that counts as "full power"
    const speedFrac = clamp(speedPxPerSec / REF_SPEED, 0, 1);
    power = clamp(0.32 + 0.68*Math.sqrt(speedFrac), 0.15, 1);
  }
  const aim = {targetX, targetY, curve, power};
  queuePlayerSwipe(aim);
  aimGesture = null;
  swipeTrail.timer = 1.1; // now start fading
}

canvas.addEventListener('touchstart', e=>{
  e.preventDefault();
  const t=e.changedTouches[0];
  beginAim(t.clientX, t.clientY);
},{passive:false});
canvas.addEventListener('touchmove', e=>{
  e.preventDefault();
  const t=e.changedTouches[0];
  moveAim(t.clientX, t.clientY);
},{passive:false});
canvas.addEventListener('touchend', e=>{
  e.preventDefault();
  const t=e.changedTouches[0];
  endAim(t.clientX, t.clientY);
},{passive:false});
canvas.addEventListener('touchcancel', ()=>{ aimGesture=null; });
// mouse equivalents for desktop testing
canvas.addEventListener('mousedown', e=>{ beginAim(e.clientX,e.clientY); });
window.addEventListener('mousemove', e=>{ if(aimGesture) moveAim(e.clientX,e.clientY); });
window.addEventListener('mouseup', e=>{ if(aimGesture) endAim(e.clientX,e.clientY); });

function clamp(v,a,b){return Math.max(a,Math.min(b,v));}

// ---------- Serve / swing logic ----------
// A completed tap/swipe (or the Space bar) calls this. If the ball is already
// in reach right now, it hits immediately. If not -- e.g. you swiped a little
// early, before the ball arrived -- the swipe is QUEUED: as soon as you reach
// the ball, it fires automatically using this same aim. If you never reach
// the ball (the point ends some other way), the queued swipe is simply
// discarded and never counts.
function playerCanHitNow(){
  if(state!=='rally' || !ball.inFlight || ball.lastHitBy==='player') return false;
  const dist = Math.hypot(ball.x-player.x, ball.y-player.y);
  const reachZ = player.jumpTimer>0 ? jumpReachZ() : NORMAL_REACH_Z;
  if(dist >= player.reach || ball.z >= reachZ) return false;
  // two-bounce rule: a serve can never be volleyed -- it must bounce first
  if(ball.isServe && !ball.hasBouncedOnce) return false;
  const kitchenBlock = inKitchen(player.y) && ball.z>0.3 && !ball.hasBouncedOnce; // no volleys in the kitchen
  if(kitchenBlock) return false;
  return true;
}
function queuePlayerSwipe(aim){
  if(state==='serve'){
    if(gameMode==='practice'){
      // no formal serve rules in practice -- the ball is already sitting
      // with you, so any tap/swipe just launches it straight from where
      // you're standing
      Object.assign(shotAim, aim);
      ball.x = player.x; ball.y = player.y; ball.z = 1.0;
      ball.isServe = false;
      ball.resting = false;
      ball.inFlight = true; // hitBall() alone doesn't set this -- normally doServe() does
      state = 'rally';
      hitBall(player, cpu, true);
      return;
    }
    if(!serverIsYou) return; // wait for cpu serve
    Object.assign(shotAim, aim);
    doServe(player);
    return;
  }
  if(state!=='rally' || !ball.inFlight || ball.lastHitBy==='player'){
    return; // no live incoming ball right now -- nothing to queue for
  }
  if(playerCanHitNow()){
    Object.assign(shotAim, aim);
    hitBall(player, cpu, true);
    pendingSwipe = null;
  } else {
    pendingSwipe = aim; // wait for the ball to arrive
  }
}
function trySwing(){
  // default-aim hit: used by the Space bar -- aims deep, away from the CPU
  const tx = cpu.x < COURT_W/2 ? COURT_W*0.72 : COURT_W*0.28;
  queuePlayerSwipe({targetX: tx, targetY: COURT_L-5, curve:0, power:0.55});
}

function doServe(server){
  const isPlayer = server===player;
  if(isPlayer) cpu.readError = Math.random()*2-1; // freeze the CPU's read-noise for this serve
  ball.x = server.x;
  ball.y = server.y;
  ball.z = 3.0;

  // Score-based serve position: server serves from the right of their own
  // court when their score is even, left when it's odd. "Right" is mirrored
  // for the CPU since it faces the opposite direction. The required landing
  // court is always the DIAGONAL opposite half from wherever the server is
  // actually standing right now.
  const serverStoodHigh = server.x > COURT_W/2;
  ball.serveRequiredHigh = !serverStoodHigh;

  let targetX, targetY, power;
  if(isPlayer){
    targetX = shotAim.targetX;
    targetY = shotAim.targetY;
    power = shotAim.power;
  } else {
    // CPU always serves legally: crosscourt, beyond the kitchen
    const lowHalf = [1, COURT_W/2-1];
    const highHalf = [COURT_W/2+1, COURT_W-1];
    const range = ball.serveRequiredHigh ? highHalf : lowHalf;
    targetX = range[0] + Math.random()*(range[1]-range[0]);
    targetY = 3 + Math.random()*(NET_Y-KITCHEN_DEPTH-5);
    power = 0.5;
  }
  const speedXY = lerp(SHOT_PARAMS.dink.speedXY, SHOT_PARAMS.drive.speedXY, power) * 0.85 * ballPaceMul();
  aimBallTo(targetX, targetY, 0, speedXY);
  ball.inFlight = true;
  ball.isServe = true;
  ball.lastHitBy = isPlayer ? 'player':'cpu';
  ball.bounceCountThisSide = 0;
  ball.hasBouncedOnce = false;
  ball.netFaultChecked = false;
  ball.bounceSideCounted = false;
  ball.resting = false;
  ball.curve = isPlayer ? (shotAim.curve * 8) : 0;
  ball.cpuWillMiss = false;
  ball.powerShot = false;
  fireTrail = [];
  autoHitCooldown = 0.16;
  state='rally';
}

function aimBallTo(targetX, targetY, targetZ, speedXY){
  const dx = targetX-ball.x, dy = targetY-ball.y;
  const dist = Math.hypot(dx,dy) || 0.001;
  const t = dist/speedXY; // time of flight estimate
  ball.vx = dx/t;
  ball.vy = dy/t;
  // vz chosen so the ball lands exactly at targetZ after time t
  ball.vz = (targetZ - ball.z + 0.5*gravity()*t*t) / t;
}

function hitBall(hitter, opponent, isPlayer){
  const incomingWasPowerShot = ball.powerShot; // capture before we overwrite it below
  if(!isPlayer){
    // the CPU has returned the ball -- clear the player's swipe trail immediately
    swipeTrail.points = [];
    swipeTrail.timer = 0;
  } else {
    // a new shot is heading toward the CPU -- roll its read-error ONCE for
    // this whole incoming shot (see updateCPU) instead of every frame
    cpu.readError = Math.random()*2-1;
  }
  // Kitchen momentum rule: a volley legally taken from OUTSIDE the kitchen
  // (reaching your paddle over the line is fine) still faults if your own
  // forward momentum carries your feet into the kitchen shortly afterward.
  const wasVolley = !ball.hasBouncedOnce && ball.inFlight;
  if(wasVolley){
    if(isPlayer && !inKitchen(player.y)) momentumWatch.player = 0.5;
    if(!isPlayer && !inKitchen(cpu.y)) momentumWatch.cpu = 0.5;
  }
  let targetX, targetY, speedXY, netError=false;
  // Power shot: hitting the ball while airborne from a jump sends it out much
  // faster, with a fire trail behind it. On Hard+, the CPU recognizes the same
  // kind of opportunity -- a ball sitting up high -- and smashes it too.
  const smashChance = difficulty==='superhard' ? 0.8 : difficulty==='hard' ? 0.55 : 0;
  const smashHeightMin = difficulty==='superhard' ? 2.4 : 3.2;
  const cpuSeesSmash = !isPlayer && smashChance>0 && ball.z>smashHeightMin && Math.random()<smashChance;
  const isPowerShot = (isPlayer && player.jumpTimer>0 && powerShotsLeft>0) || cpuSeesSmash;
  if(isPlayer && player.jumpTimer>0 && powerShotsLeft>0){
    powerShotsLeft--;
    updatePowerBar();
  }
  if(isPlayer){
    // the ball goes exactly where your swipe ended
    targetX = shotAim.targetX;
    targetY = shotAim.targetY;
    // swipe speed sets pace (soft <-> hard)
    speedXY = lerp(SHOT_PARAMS.dink.speedXY, SHOT_PARAMS.drive.speedXY, shotAim.power);
    if(isPowerShot) speedXY *= 1.45;
  } else {
    const chosenType = cpuChooseShot();
    const params = SHOT_PARAMS[chosenType];
    // A smash should always be genuinely fast -- it shouldn't inherit a slow
    // dink/lob pace just because that's what got randomly rolled underneath.
    speedXY = isPowerShot ? SHOT_PARAMS.drive.speedXY : params.speedXY;
    if(isPowerShot) speedXY *= 1.45;
    const away = player.x < COURT_W/2 ? COURT_W*0.75 : COURT_W*0.25;
    targetX = clamp(away + (Math.random()*4-2), 1, COURT_W-1);
    if(chosenType==='dink' && !isPowerShot){
      targetY = NET_Y - 2.5 - Math.random()*3; // just past the net, onto the player's side
    } else if(chosenType==='lob' && !isPowerShot){
      targetY = 2 + Math.random()*4;
    } else {
      targetY = 4 + Math.random()*8;
    }
    // the CPU isn't perfect -- a small, difficulty-based chance of an
    // unforced error: shanking it out of bounds, or dumping it into the net.
    // Super Hard barely ever misses. In practice mode, the CPU is a reliable
    // rally partner instead -- no random unforced errors cutting reps short.
    const errorChance = gameMode==='practice' ? 0 :
      (difficulty==='easy' ? 0.16 : difficulty==='hard' ? 0.05 : difficulty==='superhard' ? 0.015 : 0.10);
    if(Math.random() < errorChance){
      if(Math.random() < 0.5){
        // shank it out -- wide or long
        if(Math.random() < 0.5){
          targetX = Math.random()<0.5 ? -(1+Math.random()*2.5) : COURT_W+1+Math.random()*2.5;
        } else {
          targetY = -(1+Math.random()*3); // sails past the player's baseline
        }
      } else {
        netError = true; // dumps it into the net
      }
    }
  }
  ball.x = hitter.x; ball.y = hitter.y; ball.z = Math.max(ball.z, 1.0);
  ball.isServe = false; // any real hitBall() call is a return, not a serve

  // Returning a power shot without countering with a power shot of your own
  // is hard to control. A fifth of the time it pops up (briefer and lower
  // than it used to, and more often still lands in), two fifths of the time
  // you barely control it into a soft little dink, and two fifths of the
  // time you actually time it clean and send back a normal, solid shot.
  const defendingPowerShot = incomingWasPowerShot && !isPowerShot && !netError;
  let returnType = null;
  if(defendingPowerShot){
    const r = Math.random();
    returnType = r < 1/5 ? 'popup' : (r < 3/5 ? 'dink' : 'fast');
  }
  if(returnType==='popup'){
    speedXY = SHOT_PARAMS.dink.speedXY * 0.82; // quicker pace = less hang time than before
    if(Math.random() < 0.22){
      // mishandled -- it pops out of bounds
      if(Math.random() < 0.5){
        targetX = Math.random()<0.5 ? -(1+Math.random()*2.2) : COURT_W+1+Math.random()*2.2;
      } else {
        targetY = isPlayer ? COURT_L+1+Math.random()*3 : -(1+Math.random()*3);
      }
    }
  } else if(returnType==='dink'){
    // a barely-controlled little dink, just over the net into the kitchen
    speedXY = SHOT_PARAMS.dink.speedXY * 0.85;
    targetY = isPlayer ? (NET_Y + 2.5 + Math.random()*3) : (NET_Y - 2.5 - Math.random()*3);
  } else if(returnType==='fast'){
    // timed it clean -- a normal, solid shot back
    speedXY = SHOT_PARAMS.drive.speedXY * 0.9;
  }

  speedXY *= ballPaceMul(); // moon: same target, slower pace, more hang time
  if(netError){
    // Aim to land almost exactly ON the net line with zero height there,
    // instead of shrinking vz after the fact -- shrinking vz post-hoc left
    // the ball landing short on the CPU's own side without ever crossing
    // the net, which let it bounce twice there and wrongly "win" the point
    // via the double-bounce rule instead of being caught as a net fault.
    const netAimY = NET_Y - 0.4; // just barely across, onto the player's side
    aimBallTo(targetX, netAimY, 0, speedXY);
  } else {
    aimBallTo(targetX, targetY, 0, speedXY);
  }
  // a curved swipe puts side-spin on the ball, bending its flight
  ball.curve = isPlayer ? (shotAim.curve * 13) : 0;
  ball.lastHitBy = isPlayer?'player':'cpu';
  ball.hasBouncedOnce = false;
  ball.bounceSideCounted = false;
  ball.netFaultChecked = false;
  ball.resting = false;
  ball.powerShot = isPowerShot;
  fireTrail = [];
  // give the CPU a small, difficulty-based chance to miss a reachable shot.
  // In practice mode, the CPU always tries -- it can still fail to physically
  // reach a shot (reach/kitchen rules still apply), but it won't deliberately
  // whiff a ball it could've returned, since that would cut a rally short.
  if(isPlayer && gameMode!=='practice'){
    let missChance = difficulty==='easy' ? 0.24 : difficulty==='hard' ? 0.07 : difficulty==='superhard' ? 0.02 : 0.13;
    // Hard and Super Hard are much better at handling a power shot fired at
    // them specifically -- sharper reflexes, not just generally better
    if(isPowerShot && (difficulty==='hard' || difficulty==='superhard')) missChance *= 0.35;
    ball.cpuWillMiss = Math.random() < missChance;
  } else {
    ball.cpuWillMiss = false;
  }
  autoHitCooldown = 0.16;
  hitter.swinging = 0.15;
  const returnFlash = returnType==='popup' ? 'POP UP' : returnType==='dink' ? 'DEFENSIVE DINK' : returnType==='fast' ? 'CLEAN RETURN' : null;
  flashPoint(isPowerShot ? 'POWER SHOT' : (returnFlash ? returnFlash : (isPlayer? (Math.abs(shotAim.curve)>0.16 ? 'CURVE SHOT' : pickCallout()) : '')));
}
function cpuChooseShot(){
  const r = Math.random();
  let t;
  if(r<0.55) t='drive'; else if(r<0.85) t='dink'; else t='lob';
  cpu._chosenShot = t;
  return t;
}
function pickCallout(){
  const opts = ['NICE SHOT','DINK CITY','ON A STRING','TOP SPIN'];
  return Math.random()<0.15 ? opts[Math.floor(Math.random()*opts.length)] : '';
}
function flashPoint(msg){
  if(!msg) return;
  const el = document.getElementById('point-msg');
  el.textContent = msg;
  el.style.opacity = 1;
  clearTimeout(flashPoint._t);
  flashPoint._t = setTimeout(()=>{el.style.opacity=0;},650);
}

function updateWindDisplay(dir){
  const box = document.getElementById('windIndicator');
  const arrow = document.getElementById('windArrow');
  const speed = document.getElementById('windSpeed');
  if(venue!=='outdoor'){ box.style.display='none'; return; }
  box.style.display='block';
  // dir>0 blows toward the CPU's side (screen-right push), dir<0 the opposite
  arrow.style.transform = dir>0 ? 'scaleX(1)' : 'scaleX(-1)';
  speed.textContent = Math.round(wind.speedMph) + ' mph';
}

// ---------- Kitchen (non-volley) rule ----------
function inKitchen(y){
  return y > NET_Y-KITCHEN_DEPTH && y < NET_Y+KITCHEN_DEPTH;
}

// ---------- Point resolution ----------
// Side-out scoring: only the SERVING side can ever add to the score. If the
// receiver wins the rally, nothing is added -- service simply passes to them
// (a "side out") and they become the new server.
function resolveRallyEnd(winnerSide){
  if(gameMode==='practice'){
    // no scoring, no side-out, no game over, and no repositioning --
    // the ball just respawns with you wherever you're standing
    practiceReset();
    return;
  }
  const winnerIsServer = (winnerSide==='player') === serverIsYou;
  if(winnerIsServer){
    if(winnerSide==='player'){ scoreYou++; document.getElementById('scoreYou').firstChild.textContent = scoreYou; }
    else { scoreCPU++; document.getElementById('scoreCPU').firstChild.textContent = scoreCPU; }
  } else {
    serverIsYou = (winnerSide==='player'); // side out -- serve passes to the winner
  }
  document.getElementById('dotYou').style.visibility = serverIsYou?'visible':'hidden';
  document.getElementById('dotCPU').style.visibility = !serverIsYou?'visible':'hidden';

  if((scoreYou>=11||scoreCPU>=11) && Math.abs(scoreYou-scoreCPU)>=2){
    state='gameover';
    showGameOver(scoreYou>scoreCPU);
    return;
  }
  resetForServe();
}
// picks a random rectangular zone on the CPU's side of the court to aim for
function generateTargetZone(){
  const zones = [
    {x1:0.5, y1:NET_Y+1, x2:COURT_W/2-1, y2:NET_Y+KITCHEN_DEPTH},           // near-net left
    {x1:COURT_W/2+1, y1:NET_Y+1, x2:COURT_W-0.5, y2:NET_Y+KITCHEN_DEPTH},   // near-net right
    {x1:0.5, y1:COURT_L-8, x2:COURT_W/2-1, y2:COURT_L-1},                  // deep left
    {x1:COURT_W/2+1, y1:COURT_L-8, x2:COURT_W-0.5, y2:COURT_L-1},          // deep right
    {x1:0.5, y1:NET_Y+KITCHEN_DEPTH+2, x2:COURT_W-0.5, y2:COURT_L-10},     // mid-court, full width
  ];
  targetZone = zones[Math.floor(Math.random()*zones.length)];
}
function updatePowerBar(){
  const bar = document.getElementById('powerBar');
  if(!bar) return;
  bar.innerHTML = '';
  for(let i=0;i<POWER_SHOTS_PER_RALLY;i++){
    const pip = document.createElement('div');
    pip.className = 'power-pip' + (i<powerShotsLeft ? ' filled' : '');
    bar.appendChild(pip);
  }
}
function practiceReset(){
  ball.inFlight = false;
  pendingSwipe = null;
  ball.powerShot = false;
  fireTrail = [];
  ball.x = player.x; ball.y = player.y; ball.z = 0;
  ball.vx=0; ball.vy=0; ball.vz=0;
  ball.resting = false;
  ball.isServe = false;
  ball.curve = 0;
  cpu.readError = 0;
  powerShotsLeft = POWER_SHOTS_PER_RALLY;
  updatePowerBar();
  state = 'serve'; // waiting for the next tap/swipe -- no positions are touched
}
function resetForServe(){
  ball.inFlight=false;
  pendingSwipe = null; // discard any swipe that never got its ball
  ball.powerShot = false;
  fireTrail = [];
  powerShotsLeft = POWER_SHOTS_PER_RALLY;
  updatePowerBar();

  // a fresh gust for outdoor points (never in practice -- the gym is indoors)
  if(venue==='outdoor' && gameMode!=='practice'){
    const mph = 2 + Math.random()*13;
    const dir = Math.random()<0.5 ? -1 : 1;
    wind.speedMph = mph;
    wind.x = dir * mph * 0.2; // ft/s^2 lateral push, scaled from mph
    updateWindDisplay(dir);
  } else {
    wind.x = 0; wind.speedMph = 0;
    document.getElementById('windIndicator').style.display='none';
  }

  // score-based serve position: right side of your own court when your score
  // is even, left when odd. The CPU's "right" is mirrored since it faces the
  // opposite direction across the net.
  const serverScore = serverIsYou ? scoreYou : scoreCPU;
  const stanceRight = (serverScore % 2 === 0);
  const serverStandsHigh = serverIsYou ? stanceRight : !stanceRight;
  const receiveHigh = !serverStandsHigh; // serve must go diagonally opposite

  // the receiver starts on their OWN back line (baseline), centered
  // horizontally on the square the serve is required to land in
  const receiveBoxX = receiveHigh ? COURT_W*0.75 : COURT_W*0.25;

  if(serverIsYou){
    player.x = stanceRight ? COURT_W*0.72 : COURT_W*0.28;
    player.y = 4;
    cpu.x = receiveBoxX;
    cpu.y = COURT_L-2; // cpu's back baseline
  } else {
    cpu.x = stanceRight ? COURT_W*0.28 : COURT_W*0.72;
    cpu.y = COURT_L-4;
    player.x = receiveBoxX;
    player.y = 2; // player's back baseline
  }
  cpu.beliefX = cpu.x;

  ball.x = serverIsYou? player.x : cpu.x;
  ball.y = serverIsYou? player.y : cpu.y;
  ball.z=0; ball.vx=0;ball.vy=0;ball.vz=0; ball.resting=false; ball.isServe=false;
  state='serve';
  if(!serverIsYou){
    setTimeout(()=>{ if(state==='serve') doServe(cpu); }, 700);
  }
}

function showGameOver(youWin){
  const ov = document.getElementById('overlay');
  ov.style.display='flex';
  const card = ov.querySelector('.card');
  recordGameStats(youWin, scoreYou, scoreCPU);

  if(gameMode!=='tournament'){
    card.innerHTML = `
      <h1>${youWin? 'GAME BALL — YOU WIN' : 'CPU TAKES IT'}</h1>
      <div class="sub">${scoreYou} – ${scoreCPU}</div>
      <div class="rules">${youWin? 'Clean footwork, good shot selection. Run it back?' : 'The kitchen got the best of you. Adjust your dinks and try again.'}</div>
      <button id="playAgainBtn">Back to Menu</button>
    `;
    document.getElementById('playAgainBtn').onclick = showHomeMenu;
    return;
  }

  // Tournament mode
  const round = TOURNAMENT_ROUNDS[tournamentRound];
  if(youWin && tournamentRound >= TOURNAMENT_ROUNDS.length-1){
    // won the whole bracket -- celebrate
    finalWinnerSlot = youSlot;
    recordTournamentStats(true);
    card.innerHTML = `
      <div class="trophy-emoji">🏆</div>
      <h1>TOURNAMENT CHAMPION</h1>
      <div class="sub">${scoreYou} – ${scoreCPU} in the ${round.name}</div>
      ${ncaaBracketHTML()}
      <div class="rules">You swept the bracket. Every round, every rally -- champion.</div>
      <button id="playAgainBtn">Back to Menu</button>
    `;
    document.getElementById('playAgainBtn').onclick = showHomeMenu;
  } else if(youWin){
    const next = TOURNAMENT_ROUNDS[tournamentRound+1];
    card.innerHTML = `
      <h1>${round.name.toUpperCase()} WON</h1>
      <div class="sub">${scoreYou} – ${scoreCPU}</div>
      <div id="bracketHost">${ncaaBracketHTML()}</div>
      <div class="rules" id="advanceRules">Waiting on the rest of the bracket...</div>
      <button id="nextMatchBtn" style="visibility:hidden;">Next Match</button>
      <div class="menu-link" id="tourneyMenuLink1">← Back to Menu</div>
    `;
    // reveal the other results in this round, which is what determines who
    // you actually face next -- you don't know them until this resolves
    setTimeout(()=>{
      resolveOtherMatchesForRound(tournamentRound);
      document.getElementById('bracketHost').innerHTML = ncaaBracketHTML();
      const oppName = cpuOpponentName(tournamentRound+1);
      document.getElementById('advanceRules').innerHTML = `You're through to the <b>${next.name}</b> to face <b>${oppName}</b>.`;
      const btn = document.getElementById('nextMatchBtn');
      btn.textContent = `Next Match vs ${oppName}`;
      btn.style.visibility='visible';
    }, 900);
    document.getElementById('nextMatchBtn').onclick = ()=>{
      tournamentRound++;
      difficulty = TOURNAMENT_ROUNDS[tournamentRound].diff;
      startGame();
    };
    document.getElementById('tourneyMenuLink1').onclick = showHomeMenu;
  } else {
    // mark your own match as lost so the bracket reflects the real result,
    // even though the rest of the bracket stays unresolved/hidden
    const oppSlot = opponentSlotForRound(tournamentRound);
    if(tournamentRound===0) qfWinner[qfIndexForSlot(youSlot)] = oppSlot;
    else if(tournamentRound===1) sfWinner[sfIndexForQF(qfIndexForSlot(youSlot))] = oppSlot;
    else finalWinnerSlot = oppSlot;
    recordTournamentStats(false);
    card.innerHTML = `
      <h1>ELIMINATED</h1>
      <div class="sub">${scoreYou} – ${scoreCPU} in the ${round.name}</div>
      ${ncaaBracketHTML()}
      <div class="rules">Your tournament run ends here. The bracket resets -- back to the Quarterfinal.</div>
      <button id="restartTourneyBtn">Restart Tournament</button>
      <div class="menu-link" id="tourneyMenuLink2">← Back to Menu</div>
    `;
    document.getElementById('restartTourneyBtn').onclick = ()=>{
      tournamentRound = 0;
      difficulty = TOURNAMENT_ROUNDS[0].diff;
      setupBracket(); // fresh 8-player draw for the new run
      showBracketScreen();
    };
    document.getElementById('tourneyMenuLink2').onclick = showHomeMenu;
  }
}

function showHomeMenu(){
  state='menu';
  gameMode='quick';
  isPaused=false;
  document.getElementById('pauseOverlay').style.display='none';
  document.getElementById('windIndicator').style.display='none';
  const ov = document.getElementById('overlay');
  ov.style.display='flex';
  ov.querySelector('.card').innerHTML = homeCardHTML;
  wireHomeCard();
}

// ---------- Physics update ----------
function update(dt){
  if(outMarker.timer>0){ outMarker.timer -= dt; if(outMarker.timer<=0) outMarker.active=false; }
  if(autoHitCooldown>0) autoHitCooldown -= dt;
  if(player.jumpTimer>0) player.jumpTimer -= dt;
  if(player.jumpCooldown>0) player.jumpCooldown -= dt;
  // the swipe trail fades once released (timer<999 means it's no longer being actively drawn)
  if(swipeTrail.timer>0 && swipeTrail.timer<999){
    swipeTrail.timer -= dt;
    if(swipeTrail.timer<=0){ swipeTrail.timer=0; swipeTrail.points=[]; }
  }

  const cpuActive = !(gameMode==='practice' && !practiceHasCPU);
  if(state==='rally'){
    // cpu AI and swing-animation timers -- like the player, the CPU holds its
    // serve stance during the 'serve' state and only starts moving once the
    // ball is actually live. In "No CPU" practice, it just isn't in play at all.
    if(cpuActive) updateCPU(dt);
    if(cpu.swinging>0) cpu.swinging-=dt;
    if(player.swinging>0) player.swinging-=dt;
  }

  if(state==='rally' || (gameMode==='practice' && state==='serve')){
    // player movement is locked until the ball is actually in play --
    // you can't move before the serve, only once the rally has started.
    // (Practice mode is the one exception: move freely any time so you can
    // drill from wherever you want.) Moves at a true constant speed
    // (moveSpeed() ft/s) -- exactly the same top speed the CPU is allowed to use.
    let mx=0,my=0;
    if(keys['arrowleft']||keys['a']) mx-=1;
    if(keys['arrowright']||keys['d']) mx+=1;
    if(keys['arrowup']||keys['w']) my-=1;
    if(keys['arrowdown']||keys['s']) my+=1;
    if(joy.active){ mx += joy.x; my += joy.y; }
    const len=Math.hypot(mx,my);
    if(len>0.001){
      const clampedLen = Math.min(1,len);
      const ux=mx/len, uy=my/len;
      const spd = moveSpeed()*clampedLen;
      player.x = clamp(player.x + ux*spd*dt, 0.5, COURT_W-0.5);
      player.y = clamp(player.y - uy*spd*dt, 0.5, NET_Y-0.5);
    }
  }

  // Practice mode: the ball just stays with you, following wherever you
  // move, until you tap/swipe to hit it -- no formal serve, no reset.
  if(gameMode==='practice' && state==='serve' && !ball.inFlight){
    ball.x = player.x; ball.y = player.y;
  }

  // Kitchen momentum rule: if a player's own forward movement carries them
  // into the kitchen shortly after legally volleying from outside it, that's
  // a fault -- even though the volley contact itself was legal.
  if(state==='rally' && ball.inFlight){
    if(momentumWatch.player>0){
      momentumWatch.player -= dt;
      if(inKitchen(player.y)){
        momentumWatch.player = 0;
        resolveOut('cpu', false, 'FAULT — MOMENTUM');
        return;
      }
      if(momentumWatch.player<=0) momentumWatch.player = 0;
    }
    if(momentumWatch.cpu>0){
      momentumWatch.cpu -= dt;
      if(inKitchen(cpu.y)){
        momentumWatch.cpu = 0;
        resolveOut('player', false, 'CPU FAULT — MOMENTUM');
        return;
      }
      if(momentumWatch.cpu<=0) momentumWatch.cpu = 0;
    }
  }

  if(state==='rally' && ball.inFlight){
    const prevY = ball.y, prevZ = ball.z;
    ball.x += ball.vx*dt;
    ball.y += ball.vy*dt;
    // side-spin from a curved swipe bends the flight progressively, then decays
    ball.vx += ball.curve*dt;
    ball.curve *= Math.pow(0.985, dt*60);
    // outdoor wind gives the ball a steady lateral push
    if(venue==='outdoor' && gameMode!=='practice') ball.vx += wind.x*dt;

    if(!ball.resting){
      ball.vz -= gravity()*dt;
      ball.z += ball.vz*dt;
    }

    // power-shot fire trail: remember recent positions to draw a fading trail
    if(ball.powerShot){
      fireTrail.push({x:ball.x, y:ball.y, z:ball.z});
      if(fireTrail.length>16) fireTrail.shift();
    }

    // must clear the net: check the instant the ball crosses the net line
    if(!ball.netFaultChecked && ((prevY<NET_Y && ball.y>=NET_Y) || (prevY>NET_Y && ball.y<=NET_Y))){
      ball.netFaultChecked = true;
      const span = (ball.y-prevY)||0.0001;
      const frac = clamp((NET_Y-prevY)/span, 0, 1);
      const zAtNet = prevZ + (ball.z-prevZ)*frac;
      if(zAtNet < NET_HEIGHT){
        // hit the net -- nobody scores, just replay the point with the same server
        resolveNetFault();
        return;
      }
    }

    // bounce -- a ball is only ruled OUT if its FIRST bounce since the hit lands
    // outside the court. Once it has legally bounced in, it stays live even if
    // it later rolls or skids past the lines. Once the ball settles to a rest
    // (very low bounce energy), gravity stops nudging it below the ground --
    // that used to re-trigger a "bounce" every single frame and instantly
    // register a false double-bounce fault right after the real first bounce.
    if(!ball.resting && ball.inFlight && ball.z<=0){
      ball.z=0;
      if(!ball.hasBouncedOnce && (ball.x<0 || ball.x>COURT_W || ball.y<0 || ball.y>COURT_L)){
        resolveOut(ball.lastHitBy==='player'? 'cpu':'player', true, ball.isServe ? 'FAULT — OUT' : 'OUT');
        return;
      }
      // serve-only legality: must land beyond the receiver's kitchen line,
      // and in the diagonally-opposite service court from where the server
      // stood. Any miss here is a service fault -- an immediate side out.
      if(!ball.hasBouncedOnce && ball.isServe){
        const servedTowardCPU = ball.lastHitBy==='player'; // player always serves toward larger y
        const kitchenFault = servedTowardCPU
          ? (ball.y <= NET_Y+KITCHEN_DEPTH)
          : (ball.y >= NET_Y-KITCHEN_DEPTH);
        const landedHigh = ball.x > COURT_W/2;
        const wrongBoxFault = landedHigh !== ball.serveRequiredHigh;
        if(kitchenFault || wrongBoxFault){
          resolveOut(ball.lastHitBy==='player'? 'cpu':'player', true,
            kitchenFault ? 'FAULT — KITCHEN' : 'FAULT — WRONG COURT');
          return;
        }
      }
      // target-zone practice: check if this first bounce landed in the zone
      if(!ball.hasBouncedOnce && gameMode==='practice' && practiceType==='target' && ball.lastHitBy==='player' && targetZone){
        const hit = ball.x>=targetZone.x1 && ball.x<=targetZone.x2 && ball.y>=targetZone.y1 && ball.y<=targetZone.y2;
        flashPoint(hit ? 'TARGET HIT! 🎯' : 'missed the zone');
        generateTargetZone();
      }
      ball.vz = -ball.vz*0.42;
      ball.vx *= 0.86; ball.vy*=0.86;
      handleBounce();
      if(Math.abs(ball.vz) < 1.2){ ball.vz = 0; ball.resting = true; } // truly settle
    }

    // a queued swipe fires the instant the ball comes into reach
    if(pendingSwipe && ball.inFlight && ball.lastHitBy!=='player' && autoHitCooldown<=0 && playerCanHitNow()){
      Object.assign(shotAim, pendingSwipe);
      pendingSwipe = null;
      hitBall(player, cpu, true);
    }

    // cpu auto-return when in reach (occasionally misses on purpose, see hitBall)
    if(cpuActive && ball.inFlight && ball.lastHitBy==='player' && autoHitCooldown<=0){
      const dx=ball.x-cpu.x, dy=ball.y-cpu.y;
      const dist=Math.hypot(dx,dy);
      const serveVolleyBlock = ball.isServe && !ball.hasBouncedOnce; // two-bounce rule
      const kitchenBlock = inKitchen(cpu.y) && ball.z>0.3 && !ball.hasBouncedOnce; // can't volley in kitchen
      if(dist<cpu.reach && ball.z<6.0 && !kitchenBlock && !serveVolleyBlock){
        if(!ball.cpuWillMiss){
          hitBall(cpu, player, false);
        }
        // if cpuWillMiss is true, the CPU deliberately lets this one go by --
        // the normal bounce/double-bounce rules below then award the player the point
      }
    }
  }
}

function handleBounce(){
  // determine which side ball bounced on
  const onPlayerSide = ball.y < NET_Y;
  ball.hasBouncedOnce = true;

  if(!ball.crossedNetOK){
    // (kept simple: net-cross validity checked at hit time implicitly by aim direction)
  }

  if(ball.bounceSideCounted===false){
    ball.bounceSideCounted = true;
    ball.firstBounceSide = onPlayerSide? 'player':'cpu';
    ball.bounceCountThisSide=1;
  } else if((onPlayerSide?'player':'cpu')===ball.firstBounceSide){
    ball.bounceCountThisSide++;
    if(ball.bounceCountThisSide>=2){
      // double bounce -> point to the hitter of last shot
      resolveOut(ball.lastHitBy);
      return;
    }
  } else {
    ball.bounceSideCounted=true;
    ball.firstBounceSide = onPlayerSide?'player':'cpu';
    ball.bounceCountThisSide=1;
  }

  // reset the "must bounce before volley" flag for opposite side after crossing
  ball.bounceSideCounted = true;
}

function resolveOut(winner, showMarker=false, msg='OUT'){
  if(!ball.inFlight) return;
  if(showMarker){
    outMarker.active = true;
    outMarker.x = ball.x;
    outMarker.y = ball.y;
    outMarker.timer = 0.95;
  }
  // when the CPU is the one who errs (not a serve fault), celebrate it as a
  // nice shot rather than the flat, neutral "OUT" call
  if(winner==='player' && msg==='OUT') msg='NICE SHOT';
  flashPoint(msg);
  ball.inFlight=false;
  resolveRallyEnd(winner);
}

function resolveNetFault(){
  if(!ball.inFlight) return;
  ball.inFlight = false;
  ball.curve = 0;
  const winner = ball.lastHitBy==='player' ? 'cpu' : 'player';
  // same idea for net faults: the CPU netting it is a nice shot for you, not
  // just a neutral fault callout (unless it's a serve fault, which stays specific)
  flashPoint(ball.isServe ? 'FAULT — INTO NET' : (winner==='player' ? 'NICE SHOT' : 'NET'));
  // whoever hit the ball into the net loses the rally
  resolveRallyEnd(winner);
}

// simple CPU AI: move toward predicted ball landing x, respect kitchen rule roughly
// Difficulty changes how SMART the CPU is (how accurately it predicts where the
// ball is going, and how quickly it commits to that read) -- NEVER how fast it
// physically moves. Its feet always travel at exactly MOVE_SPEED, same as the
// player, so a higher difficulty wins through better positioning, not speed.
const CPU_SKILL = {
  easy:      {predict: 0.5,  reactionRate: 2.2,  error: 2.6,  windRead: 0.15},
  mid:       {predict: 0.8,  reactionRate: 4.5,  error: 1.1,  windRead: 0.5},
  hard:      {predict: 1.0,  reactionRate: 9.0,  error: 0.2,  windRead: 0.85},
  superhard: {predict: 1.08, reactionRate: 13.0, error: 0.05, windRead: 1.0},
};
function updateCPU(dt){
  const skill = CPU_SKILL[difficulty] || CPU_SKILL.mid;
  let targetX, targetY;
  if(!ball.inFlight){
    targetX = COURT_W/2; targetY = COURT_L-4;
  } else {
    // predict where ball will be when it reaches cpu's depth zone
    let predX = ball.x;
    if(ball.vy>0){ // moving toward cpu
      const t = Math.max(0,(cpu.y-ball.y)/Math.max(0.1,ball.vy));
      predX = ball.x + ball.vx*t*skill.predict;
      // a smarter CPU also reads the wind and compensates for how much
      // further it'll drift the ball before it arrives
      if(venue==='outdoor'){
        predX += 0.5*wind.x*t*t*skill.windRead;
      }
    }
    // lower skill reads the ball's path less precisely -- this noise is
    // frozen per-shot (see hitBall) rather than re-rolled every frame, which
    // used to make the CPU's target jitter and look like it was snapping/

    // cutting erratically toward the ball instead of moving there smoothly
    predX += cpu.readError * skill.error;
    targetX = clamp(predX, 1, COURT_W-1);

    // predict how DEEP the shot is actually going to land, using real
    // projectile physics (time to next touchdown) -- a shot aimed at the
    // baseline needs the CPU to actually retreat and cover it. The old
    // formula here never looked at the ball at all, which is why deep
    // serves/shots were basically unreturnable.
    let predY = cpu.y;
    if(ball.vy>0){
      const g = gravity();
      const disc = ball.vz*ball.vz + 2*g*Math.max(0,ball.z);
      const tLand = disc>0 ? (ball.vz + Math.sqrt(disc))/g : 0.3;
      const rawLandingY = ball.y + ball.vy*Math.max(0.05,tLand);
      predY = cpu.y + (rawLandingY-cpu.y)*skill.predict + cpu.readError*skill.error*0.6;
    }
    targetY = clamp(predY, NET_Y+2.2, COURT_L-1);
  }
  // "reactionRate" is how fast the CPU updates its BELIEF about where to go --
  // a mental reaction lag, not a physical speed boost. Its feet then walk
  // toward that belief at the same constant speed the player has.
  cpu.beliefX += (targetX - cpu.beliefX) * clamp(dt*skill.reactionRate, 0, 1);
  moveToward(cpu, cpu.beliefX, targetY, moveSpeed(), dt);
}

// ---------- Rendering ----------
function drawOutdoorBackground(farEdgeY){
  // sky
  const sky = ctx.createLinearGradient(0,0,0,farEdgeY+40);
  sky.addColorStop(0,'#7ec8f0');
  sky.addColorStop(0.7,'#bfe3f5');
  sky.addColorStop(1,'#d9efd0');
  ctx.fillStyle=sky;
  ctx.fillRect(0,0,W,H);

  // sun
  ctx.beginPath();
  ctx.arc(W*0.85, 46, 22, 0, Math.PI*2);
  ctx.fillStyle='rgba(255,247,200,0.95)';
  ctx.fill();

  // drifting clouds
  const t = performance.now()/1000;
  const clouds = [[0.15,38,1.0],[0.42,26,0.8],[0.68,50,1.15],[0.9,30,0.7]];
  clouds.forEach((c,i)=>{
    const cx = ((c[0]*W + t*6*(i%2?1:-1)) % (W+120)) - 60;
    const cy = c[1];
    const s = c[2];
    ctx.fillStyle='rgba(255,255,255,0.85)';
    ctx.beginPath();
    ctx.ellipse(cx,cy,22*s,11*s,0,0,Math.PI*2);
    ctx.ellipse(cx+18*s,cy+4*s,16*s,9*s,0,0,Math.PI*2);
    ctx.ellipse(cx-18*s,cy+5*s,15*s,8*s,0,0,Math.PI*2);
    ctx.fill();
  });

  // distant tree line along the far edge
  ctx.fillStyle='#3d7a3f';
  for(let i=-1;i<=22;i++){
    const tx = (i/21)*W;
    const th = 20+((i*37)%14);
    ctx.beginPath();
    ctx.arc(tx, farEdgeY-2, th*0.42, 0, Math.PI*2);
    ctx.fill();
  }
  ctx.fillStyle='#2f5f33';
  for(let i=-1;i<=22;i++){
    const tx = (i/21)*W + 12;
    const th = 16+((i*23)%10);
    ctx.beginPath();
    ctx.arc(tx, farEdgeY+1, th*0.34, 0, Math.PI*2);
    ctx.fill();
  }

  // grass surrounding the court
  const grass = ctx.createLinearGradient(0,farEdgeY,0,H);
  grass.addColorStop(0,'#6fae52');
  grass.addColorStop(1,'#4d8f3e');
  ctx.fillStyle=grass;
  ctx.fillRect(0,farEdgeY,W,H-farEdgeY);
}

function drawIndoorBackground(farEdgeY){
  // dark arena gradient
  const bg = ctx.createLinearGradient(0,0,0,H);
  bg.addColorStop(0,'#161f22');
  bg.addColorStop(0.55,'#0d1614');
  bg.addColorStop(1,'#06100c');
  ctx.fillStyle=bg;
  ctx.fillRect(0,0,W,H);

  // stadium lights (bright glows up top)
  [W*0.18, W*0.5, W*0.82].forEach(lx=>{
    const glow = ctx.createRadialGradient(lx,18,2,lx,18,70);
    glow.addColorStop(0,'rgba(255,255,235,0.55)');
    glow.addColorStop(1,'rgba(255,255,235,0)');
    ctx.fillStyle=glow;
    ctx.fillRect(lx-70,-40,140,140);
    ctx.beginPath();
    ctx.arc(lx,16,5,0,Math.PI*2);
    ctx.fillStyle='#fffceb';
    ctx.fill();
  });

  // crowd stands -- rows of tiny fans in the stands behind the far baseline
  const rows = 5;
  for(let r=0;r<rows;r++){
    const rowY = 24 + r*((farEdgeY-24)/rows);
    const seatCount = 26 + r*3;
    for(let i=0;i<seatCount;i++){
      const fx = (i/(seatCount-1))*W;
      // deterministic pseudo-random per seat so fans don't flicker every frame
      const seed = (r*97 + i*13) % 100;
      if(seed<28) continue; // gaps/aisles
      const shade = 0.35 + (seed%10)*0.045;
      const hueSet = ['#c96a4a','#4a7fc9','#c9b04a','#8f4ac9','#4ac97e','#c94a6a'];
      ctx.fillStyle = hueSet[seed%hueSet.length];
      ctx.globalAlpha = shade;
      ctx.beginPath();
      ctx.arc(fx, rowY, 2.4, 0, Math.PI*2);
      ctx.fill();
      ctx.globalAlpha = 1;
    }
  }

  // a dark rail separating the stands from the court out-of-bounds area
  ctx.fillStyle='#12291f';
  ctx.fillRect(0, farEdgeY-4, W, 6);
}

function drawMoonBackground(farEdgeY){
  // black space sky
  const sky = ctx.createLinearGradient(0,0,0,farEdgeY+30);
  sky.addColorStop(0,'#050510');
  sky.addColorStop(1,'#0d0f1a');
  ctx.fillStyle=sky;
  ctx.fillRect(0,0,W,H);

  // stars (deterministic so they don't flicker every frame)
  ctx.fillStyle='rgba(255,255,255,0.85)';
  for(let i=0;i<70;i++){
    const sx = (i*53.7)%W;
    const sy = (i*97.3)%(farEdgeY+20);
    const r = (i%5===0)?1.6:0.9;
    ctx.globalAlpha = 0.4 + (i%4)*0.15;
    ctx.beginPath();
    ctx.arc(sx,sy,r,0,Math.PI*2);
    ctx.fill();
  }
  ctx.globalAlpha=1;

  // Earth, hanging distant in the sky
  const ex=W*0.78, ey=42, er=26;
  ctx.beginPath();
  ctx.arc(ex,ey,er,0,Math.PI*2);
  const eg = ctx.createRadialGradient(ex-8,ey-8,4,ex,ey,er);
  eg.addColorStop(0,'#7ec8f0');
  eg.addColorStop(0.55,'#3f7fd9');
  eg.addColorStop(1,'#1c3f7a');
  ctx.fillStyle=eg;
  ctx.fill();
  ctx.fillStyle='rgba(255,255,255,0.5)';
  ctx.beginPath(); ctx.ellipse(ex-6,ey-4,9,5,0.4,0,Math.PI*2); ctx.fill();
  ctx.beginPath(); ctx.ellipse(ex+7,ey+8,7,4,-0.3,0,Math.PI*2); ctx.fill();

  // the sun, harsh and small with no atmosphere to soften it
  ctx.beginPath();
  ctx.arc(W*0.16,34,14,0,Math.PI*2);
  ctx.fillStyle='#fffdf2';
  ctx.fill();

  // distant crater-pocked mountains along the horizon
  ctx.fillStyle='#3a3a42';
  ctx.beginPath();
  ctx.moveTo(0,farEdgeY);
  for(let x=0;x<=W;x+=30){
    const h = 10+((x*7)%16);
    ctx.lineTo(x,farEdgeY-h);
  }
  ctx.lineTo(W,farEdgeY); ctx.closePath(); ctx.fill();

  // grey moon dust surrounding the court
  const dust = ctx.createLinearGradient(0,farEdgeY,0,H);
  dust.addColorStop(0,'#8a8a8f');
  dust.addColorStop(1,'#5c5c63');
  ctx.fillStyle=dust;
  ctx.fillRect(0,farEdgeY,W,H-farEdgeY);
  // craters
  ctx.strokeStyle='rgba(0,0,0,0.18)';
  ctx.lineWidth=1.5;
  for(let i=0;i<14;i++){
    const cx = (i*137.3)%W;
    const cy = farEdgeY + ((i*211.7)%(H-farEdgeY));
    const r = 4+(i%3)*3;
    ctx.beginPath(); ctx.arc(cx,cy,r,0,Math.PI*2); ctx.stroke();
  }
}

function drawGymBackground(farEdgeY){
  // warm indoor gym gradient
  const bg = ctx.createLinearGradient(0,0,0,H);
  bg.addColorStop(0,'#2a2420');
  bg.addColorStop(0.6,'#1c1815');
  bg.addColorStop(1,'#100d0b');
  ctx.fillStyle=bg;
  ctx.fillRect(0,0,W,H);

  // overhead gym lights
  [W*0.22,W*0.5,W*0.78].forEach(lx=>{
    ctx.fillStyle='rgba(255,236,200,0.9)';
    ctx.fillRect(lx-30,10,60,7);
    const glow = ctx.createRadialGradient(lx,20,4,lx,20,60);
    glow.addColorStop(0,'rgba(255,236,200,0.35)');
    glow.addColorStop(1,'rgba(255,236,200,0)');
    ctx.fillStyle=glow;
    ctx.fillRect(lx-60,-20,120,110);
  });

  // wall padding stripes behind the far edge
  ctx.fillStyle='#5a3a2a';
  ctx.fillRect(0, farEdgeY-30, W, 30);
  ctx.fillStyle='#3f2a1f';
  for(let x=0;x<W;x+=26) ctx.fillRect(x, farEdgeY-30, 12, 30);

  // hardwood gym floor surrounding the court
  const floor = ctx.createLinearGradient(0,farEdgeY,0,H);
  floor.addColorStop(0,'#8a6238');
  floor.addColorStop(1,'#5f4224');
  ctx.fillStyle=floor;
  ctx.fillRect(0,farEdgeY,W,H-farEdgeY);
  ctx.strokeStyle='rgba(0,0,0,0.12)';
  ctx.lineWidth=1;
  for(let x=0;x<W;x+=18){
    ctx.beginPath(); ctx.moveTo(x,farEdgeY); ctx.lineTo(x,H); ctx.stroke();
  }
}

function drawCourt(){
  ctx.clearRect(0,0,W,H);

  const tl = courtToScreen(0,0), tr = courtToScreen(COURT_W,0);
  const bl = courtToScreen(0,COURT_L), br = courtToScreen(COURT_W,COURT_L);
  const farEdgeY = Math.min(tl.y,tr.y,bl.y,br.y);

  if(gameMode==='practice'){
    drawGymBackground(farEdgeY);
  } else if(venue==='outdoor'){
    drawOutdoorBackground(farEdgeY);
  } else if(venue==='moon'){
    drawMoonBackground(farEdgeY);
  } else {
    drawIndoorBackground(farEdgeY);
  }

  // court surface
  ctx.beginPath();
  ctx.moveTo(bl.x,bl.y); ctx.lineTo(br.x,br.y); ctx.lineTo(tr.x,tr.y); ctx.lineTo(tl.x,tl.y); ctx.closePath();
  const courtGrad = ctx.createLinearGradient(0,tl.y,0,bl.y);
  courtGrad.addColorStop(0,'#1a5f8f');
  courtGrad.addColorStop(1,'#1f6fa8');
  ctx.fillStyle=courtGrad;
  ctx.fill();

  // kitchen zones (both sides)
  drawZone(0, NET_Y-KITCHEN_DEPTH, COURT_W, NET_Y, 'rgba(255,255,255,0.07)');
  drawZone(0, NET_Y, COURT_W, NET_Y+KITCHEN_DEPTH, 'rgba(255,255,255,0.07)');

  // lines
  ctx.strokeStyle=var_line();
  ctx.lineWidth=2.5;
  strokeCourtRect(0,0,COURT_W,COURT_L);
  strokeCourtLine(0,NET_Y-KITCHEN_DEPTH,COURT_W,NET_Y-KITCHEN_DEPTH);
  strokeCourtLine(0,NET_Y+KITCHEN_DEPTH,COURT_W,NET_Y+KITCHEN_DEPTH);
  strokeCourtLine(COURT_W/2,0,COURT_W/2,NET_Y-KITCHEN_DEPTH);
  strokeCourtLine(COURT_W/2,NET_Y+KITCHEN_DEPTH,COURT_W/2,COURT_L);

  // net
  const nl = courtToScreen(0,NET_Y), nr = courtToScreen(COURT_W,NET_Y);
  const netH = 26*scaleAt(NET_Y);
  ctx.save();
  ctx.strokeStyle='#0d1512';
  ctx.fillStyle='rgba(15,20,18,0.85)';
  ctx.fillRect(nl.x, nl.y-netH, nr.x-nl.x, netH);
  ctx.lineWidth=1;
  for(let i=0;i<=20;i++){
    const xx = nl.x + (nr.x-nl.x)*i/20;
    ctx.beginPath(); ctx.moveTo(xx, nl.y-netH); ctx.lineTo(xx, nl.y); ctx.strokeStyle='rgba(232,255,77,0.15)'; ctx.stroke();
  }
  ctx.fillStyle=var_accentDim();
  ctx.fillRect(nl.x, nl.y-netH-3, nr.x-nl.x, 3);
  ctx.restore();
}
function var_line(){return getComputedStyle(document.documentElement).getPropertyValue('--line').trim();}
function var_accentDim(){return 'rgba(232,255,77,0.6)';}

function drawZone(x1,y1,x2,y2,color){
  const p1=courtToScreen(x1,y1), p2=courtToScreen(x2,y1), p3=courtToScreen(x2,y2), p4=courtToScreen(x1,y2);
  ctx.beginPath();
  ctx.moveTo(p1.x,p1.y);ctx.lineTo(p2.x,p2.y);ctx.lineTo(p3.x,p3.y);ctx.lineTo(p4.x,p4.y);ctx.closePath();
  ctx.fillStyle=color; ctx.fill();
}
function strokeCourtRect(x1,y1,x2,y2){
  const p1=courtToScreen(x1,y1), p2=courtToScreen(x2,y1), p3=courtToScreen(x2,y2), p4=courtToScreen(x1,y2);
  ctx.beginPath();
  ctx.moveTo(p1.x,p1.y);ctx.lineTo(p2.x,p2.y);ctx.lineTo(p3.x,p3.y);ctx.lineTo(p4.x,p4.y);ctx.closePath();
  ctx.stroke();
}
function strokeCourtLine(x1,y1,x2,y2){
  const p1=courtToScreen(x1,y1), p2=courtToScreen(x2,y2);
  ctx.beginPath(); ctx.moveTo(p1.x,p1.y); ctx.lineTo(p2.x,p2.y); ctx.stroke();
}

function drawPlayerFig(p, color, swinging, faceDown, jumpLift){
  const s = scaleAt(p.y);
  const pos = courtToScreen(p.x,p.y);
  const lift = (jumpLift||0) * 26 * s; // upward bob while jumping
  const bodyH = 46*s, bodyW = 20*s;
  // shadow -- stays on the ground even while the figure hops up, so the
  // jump reads clearly instead of the whole sprite just floating in place
  ctx.beginPath();
  ctx.ellipse(pos.x, pos.y+4*s, bodyW*0.55*(1-lift/60), 6*s*(1-lift/60), 0,0,Math.PI*2);
  ctx.fillStyle='rgba(0,0,0,0.35)';
  ctx.fill();
  // body
  ctx.fillStyle=color;
  roundRect(pos.x-bodyW/2, pos.y-bodyH-lift, bodyW, bodyH, 6*s);
  ctx.fill();
  // head
  ctx.beginPath();
  ctx.arc(pos.x, pos.y-bodyH-8*s-lift, 8*s, 0, Math.PI*2);
  ctx.fillStyle='#f2d8b8';
  ctx.fill();
  // paddle
  const swingAmt = swinging>0 ? Math.sin(swinging*20)*18 : 0;
  ctx.save();
  ctx.translate(pos.x + bodyW*0.55, pos.y-bodyH*0.55-lift);
  ctx.rotate((faceDown? 1:-1)*(0.5+swingAmt*0.05));
  ctx.fillStyle='#e8ff4d';
  roundRect(-4*s, -18*s, 8*s, 18*s, 3*s);
  ctx.fill();
  ctx.fillStyle='#2f3e35';
  ctx.fillRect(-1.5*s, 0, 3*s, 10*s);
  ctx.restore();
}
function roundRect(x,y,w,h,r){
  ctx.beginPath();
  ctx.moveTo(x+r,y);
  ctx.arcTo(x+w,y,x+w,y+h,r);
  ctx.arcTo(x+w,y+h,x,y+h,r);
  ctx.arcTo(x,y+h,x,y,r);
  ctx.arcTo(x,y,x+w,y,r);
  ctx.closePath();
}

// resolves ANY CSS color string (hex, hsl, named, etc) to r,g,b via a 1x1
// canvas -- lets us build custom rgba() gradients from a character's color
// even though our color pool is a mix of hex and hsl() strings
function colorToRGB(colorStr){
  if(!colorToRGB._ctx){
    const c = document.createElement('canvas'); c.width=1; c.height=1;
    colorToRGB._ctx = c.getContext('2d');
  }
  const ctx2 = colorToRGB._ctx;
  ctx2.clearRect(0,0,1,1);
  ctx2.fillStyle = colorStr;
  ctx2.fillRect(0,0,1,1);
  const d = ctx2.getImageData(0,0,1,1).data;
  return {r:d[0], g:d[1], b:d[2]};
}
// the color of whoever most recently hit the ball -- used so a power shot
// glows/trails in that player's own character color
function hitterColor(){
  return ball.lastHitBy==='cpu' ? cpuColor() : playerColor;
}

function drawFireTrail(){
  if(!ball.powerShot || fireTrail.length<2) return;
  const rgb = colorToRGB(hitterColor());
  for(let i=0;i<fireTrail.length;i++){
    const pt = fireTrail[i];
    const age = i/fireTrail.length; // 0 = oldest, 1 = newest
    const s = scaleAt(pt.y);
    const pos = courtToScreen(pt.x, pt.y);
    const liftPx = pt.z * 9 * s;
    const r = (3 + age*4) * s;
    const grad = ctx.createRadialGradient(pos.x, pos.y-liftPx, 0, pos.x, pos.y-liftPx, r);
    grad.addColorStop(0, `rgba(${rgb.r},${rgb.g},${rgb.b},${0.55*age})`);
    grad.addColorStop(0.5, `rgba(${rgb.r},${rgb.g},${rgb.b},${0.32*age})`);
    grad.addColorStop(1, `rgba(${rgb.r},${rgb.g},${rgb.b},0)`);
    ctx.beginPath();
    ctx.arc(pos.x, pos.y-liftPx, r, 0, Math.PI*2);
    ctx.fillStyle = grad;
    ctx.fill();
  }
}

function drawBall(){
  const s = scaleAt(ball.y);
  const pos = courtToScreen(ball.x, ball.y);
  const liftPx = ball.z * 9 * s;
  // shadow -- kept clearly visible at all times (even high in the air) so
  // it always reads as an obvious landing-spot indicator, not just a subtle
  // ground detail that fades away.
  const shadowScale = clamp(1 - ball.z/22, 0.55, 1);
  ctx.beginPath();
  ctx.ellipse(pos.x, pos.y, 8*s*shadowScale, 3.6*s*shadowScale, 0,0,Math.PI*2);
  ctx.fillStyle='rgba(0,0,0,0.55)';
  ctx.fill();
  ctx.lineWidth=1;
  ctx.strokeStyle='rgba(0,0,0,0.3)';
  ctx.stroke();
  drawFireTrail();
  // ball
  ctx.beginPath();
  ctx.arc(pos.x, pos.y-liftPx, 6.5*s, 0, Math.PI*2);
  if(ball.powerShot){
    const rgb = colorToRGB(hitterColor());
    ctx.fillStyle = `rgb(${rgb.r},${rgb.g},${rgb.b})`;
    ctx.strokeStyle = `rgb(${Math.round(rgb.r*0.55)},${Math.round(rgb.g*0.55)},${Math.round(rgb.b*0.55)})`;
  } else {
    ctx.fillStyle = '#e9ff6a';
    ctx.strokeStyle = '#8fae1f';
  }
  ctx.fill();
  ctx.lineWidth=1;
  ctx.stroke();
}

function drawOutMarker(){
  if(!outMarker.active) return;
  const pos = courtToScreen(outMarker.x, outMarker.y);
  const s = scaleAt(outMarker.y);
  const pulse = 1 + 0.18*Math.sin(outMarker.timer*26);
  ctx.save();
  ctx.beginPath();
  ctx.arc(pos.x, pos.y, 20*s*pulse, 0, Math.PI*2);
  ctx.fillStyle='rgba(235,45,45,0.35)';
  ctx.fill();
  ctx.lineWidth=5;
  ctx.strokeStyle='rgba(255,60,60,0.98)';
  ctx.stroke();
  ctx.font=`bold ${Math.max(13,18*s)}px Segoe UI`;
  ctx.fillStyle='#fff';
  ctx.textAlign='center';
  ctx.textBaseline='middle';
  ctx.fillText('OUT', pos.x, pos.y);
  ctx.restore();
}

function drawSwipeTrail(){
  const pts = swipeTrail.points;
  if(!pts || pts.length<2) return;
  const fadeFrac = swipeTrail.timer>=999 ? 1 : clamp(swipeTrail.timer/1.1, 0, 1);
  const alpha = 0.22*fadeFrac;
  if(alpha<=0.004) return;
  ctx.save();
  ctx.strokeStyle = `rgba(255,255,255,${alpha})`;
  ctx.lineWidth = 3;
  ctx.lineCap = 'round';
  ctx.lineJoin = 'round';
  ctx.beginPath();
  ctx.moveTo(pts[0].x, pts[0].y);
  for(let i=1;i<pts.length;i++) ctx.lineTo(pts[i].x, pts[i].y);
  ctx.stroke();
  ctx.restore();
}

function drawReachIndicator(){
  const inReach = state==='rally' && ball.inFlight && ball.lastHitBy!=='player' &&
                  Math.hypot(ball.x-player.x, ball.y-player.y) < player.reach;
  jumpBtn.classList.toggle('cooldown', player.jumpCooldown>0);
  if(!inReach) return;
  const pos = courtToScreen(player.x,player.y);
  const s = scaleAt(player.y);
  ctx.beginPath();
  ctx.arc(pos.x,pos.y-20*s, 30*s, 0, Math.PI*2);
  ctx.strokeStyle='rgba(232,255,77,0.7)';
  ctx.lineWidth=2;
  ctx.stroke();
}

function drawTargetZone(){
  if(!(gameMode==='practice' && practiceType==='target' && targetZone)) return;
  const t = performance.now()/500;
  const pulse = 0.18 + 0.1*Math.sin(t);
  drawZone(targetZone.x1, targetZone.y1, targetZone.x2, targetZone.y2, `rgba(232,255,77,${pulse})`);
  const p1=courtToScreen(targetZone.x1,targetZone.y1), p2=courtToScreen(targetZone.x2,targetZone.y1);
  const p3=courtToScreen(targetZone.x2,targetZone.y2), p4=courtToScreen(targetZone.x1,targetZone.y2);
  ctx.beginPath();
  ctx.moveTo(p1.x,p1.y);ctx.lineTo(p2.x,p2.y);ctx.lineTo(p3.x,p3.y);ctx.lineTo(p4.x,p4.y);ctx.closePath();
  ctx.strokeStyle='rgba(232,255,77,0.9)';
  ctx.lineWidth=2.5;
  ctx.setLineDash([6,4]);
  ctx.stroke();
  ctx.setLineDash([]);
}

function render(){
  drawCourt();
  drawTargetZone();
  drawPlayerFig(cpu, cpuColor(), cpu.swinging, true, 0);
  const jumpProgress = player.jumpTimer>0 ? 1 - (player.jumpTimer/JUMP_DURATION) : 1;
  const jumpLift = player.jumpTimer>0 ? Math.sin(jumpProgress*Math.PI)*jumpLiftMul() : 0;
  drawPlayerFig(player, playerColor, player.swinging, false, jumpLift);
  drawBall();
  drawOutMarker();
  drawSwipeTrail();
  drawReachIndicator();
}

// ---------- Main loop ----------
let last = performance.now();
function loop(now){
  const dt = Math.min(0.033,(now-last)/1000);
  last = now;
  if(state!=='menu' && !isPaused){
    update(dt);
    render();
  }
  requestAnimationFrame(loop);
}

function startGame(){
  document.getElementById('overlay').style.display='none';
  scoreYou=0; scoreCPU=0;
  document.getElementById('scoreYou').firstChild.textContent='0';
  document.getElementById('scoreCPU').firstChild.textContent='0';
  serverIsYou = true;
  document.getElementById('dotYou').style.visibility='visible';
  document.getElementById('dotCPU').style.visibility='hidden';
  document.getElementById('pauseBtn').style.display='flex';
  document.getElementById('hud').style.display = gameMode==='practice' ? 'none' : 'flex';
  document.getElementById('controls-hint').textContent = gameMode==='practice'
    ? (practiceType==='target' ? '🎯 TARGET ZONE — land it in the highlighted box' : '🏋️ PRACTICE — move freely, tap/swipe to hit, unlimited shots')
    : 'MOVE: Arrow Keys / WASD • SHIFT to jump • SPACE, or tap/swipe the COURT to hit • ball lands where your swipe ends';
  targetZone = null;
  if(gameMode==='practice' && practiceType==='target') generateTargetZone();
  resetForServe();
}

// ---------- Tournament bracket display ----------
function slotChip(slot, winnerSlot){
  if(slot==null) return `<div class="ncaa-slot unknown">?</div>`;
  const p = bracketPlayers[slot];
  const isWinner = winnerSlot!=null && winnerSlot===slot;
  const cls = 'ncaa-slot' + (p.isYou?' you':'') + (isWinner?' winner':'');
  return `<div class="${cls}"><span class="ncaa-dot" style="background:${p.color}"></span>${p.name}</div>`;
}
function renderMatch(slotA, slotB, winnerSlot){
  return `<div class="ncaa-match">${slotChip(slotA,winnerSlot)}${slotChip(slotB,winnerSlot)}</div>`;
}
function ncaaBracketHTML(){
  const qf = `<div class="ncaa-round"><div class="ncaa-round-title">Quarterfinals</div>` +
    QF_PAIRS.map((p,i)=>renderMatch(p[0],p[1],qfWinner[i])).join('') + `</div>`;
  const sf = `<div class="ncaa-round"><div class="ncaa-round-title">Semifinals</div>` +
    [0,1].map(s=>renderMatch(qfWinner[s*2], qfWinner[s*2+1], sfWinner[s])).join('') + `</div>`;
  const fin = `<div class="ncaa-round"><div class="ncaa-round-title">Final</div>` +
    renderMatch(sfWinner[0], sfWinner[1], finalWinnerSlot) + `</div>`;
  return `<div class="ncaa-bracket">${qf}${sf}${fin}</div>`;
}
function showBracketScreen(){
  const ov = document.getElementById('overlay');
  ov.style.display='flex';
  const card = ov.querySelector('.card');
  card.innerHTML = `
    <h1>YOUR BRACKET</h1>
    <div class="sub">8-player single elimination — win it all for the trophy</div>
    ${ncaaBracketHTML()}
    <div class="rules">Your round-1 matchup is set. Who you'd face after that stays hidden until those results come in.</div>
    <button id="beginTourneyBtn">Begin ${TOURNAMENT_ROUNDS[0].name}</button>
    <div class="menu-link" id="bracketMenuLink">← Back to Menu</div>
  `;
  document.getElementById('beginTourneyBtn').onclick = startGame;
  document.getElementById('bracketMenuLink').onclick = showHomeMenu;
}

// ---------- Practice mode setup: 4 combinations (Free/Target Zone x With/Without CPU) ----------
function showPracticeSetup(){
  const ov = document.getElementById('overlay');
  ov.style.display='flex';
  const card = ov.querySelector('.card');
  card.innerHTML = `
    <h1>PRACTICE SETUP</h1>
    <div class="sub">move freely, unlimited shots, nothing ever resets you</div>

    <div class="setting-label">Drill Type</div>
    <div class="diffrow" id="practiceTypeRow">
      <div class="diffbtn active" data-t="free">Free Practice</div>
      <div class="diffbtn" data-t="target">🎯 Target Zone</div>
    </div>

    <div class="setting-label">Opponent</div>
    <div class="diffrow" id="practiceCpuRow">
      <div class="diffbtn active" data-c="yes">With CPU</div>
      <div class="diffbtn" data-c="no">No CPU (open court)</div>
    </div>

    <div class="setting-label" id="practiceDiffLabel">CPU Difficulty</div>
    <div class="diffrow" id="practiceDiffRow">
      <div class="diffbtn" data-pd="easy">Easy</div>
      <div class="diffbtn active" data-pd="mid">Rec Player</div>
      <div class="diffbtn" data-pd="hard">Hard</div>
      <div class="diffbtn" data-pd="superhard">Super Hard</div>
    </div>

    <button id="beginPracticeBtn">Start Practice</button>
    <div class="menu-link" id="practiceMenuLink">← Back to Menu</div>
  `;
  document.querySelectorAll('#practiceTypeRow .diffbtn').forEach(btn=>{
    btn.classList.toggle('active', btn.dataset.t===practiceType);
    btn.onclick = ()=>{
      document.querySelectorAll('#practiceTypeRow .diffbtn').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      practiceType = btn.dataset.t;
    };
  });
  document.querySelectorAll('#practiceCpuRow .diffbtn').forEach(btn=>{
    btn.classList.toggle('active', (btn.dataset.c==='yes')===practiceHasCPU);
    btn.onclick = ()=>{
      document.querySelectorAll('#practiceCpuRow .diffbtn').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      practiceHasCPU = btn.dataset.c==='yes';
      document.getElementById('practiceDiffRow').style.display = practiceHasCPU ? 'flex' : 'none';
      document.getElementById('practiceDiffLabel').style.display = practiceHasCPU ? 'block' : 'none';
    };
  });
  document.querySelectorAll('#practiceDiffRow .diffbtn').forEach(btn=>{
    btn.classList.toggle('active', btn.dataset.pd===practiceDifficulty);
    btn.onclick = ()=>{
      document.querySelectorAll('#practiceDiffRow .diffbtn').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      practiceDifficulty = btn.dataset.pd;
    };
  });
  document.getElementById('beginPracticeBtn').onclick = ()=>{
    gameMode = 'practice';
    difficulty = practiceHasCPU ? practiceDifficulty : difficulty;
    startGame();
  };
  document.getElementById('practiceMenuLink').onclick = showHomeMenu;
}

// ---------- Home menu wiring (re-attached every time we return to the menu,
// since the card's innerHTML gets replaced by the game-over / trophy screens) ----------
function wireHomeCard(){
  document.getElementById('startBtn').onclick = ()=>{ gameMode='quick'; startGame(); };
  document.getElementById('tournamentBtn').onclick = ()=>{
    gameMode='tournament';
    tournamentRound=0;
    difficulty = TOURNAMENT_ROUNDS[0].diff;
    setupBracket();
    showBracketScreen();
  };
  document.getElementById('practiceBtn').onclick = showPracticeSetup;
  document.querySelectorAll('#diffrow .diffbtn').forEach(btn=>{
    btn.classList.toggle('active', btn.dataset.d===difficulty);
    btn.onclick = ()=>{
      document.querySelectorAll('#diffrow .diffbtn').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      difficulty = btn.dataset.d;
    };
  });
  document.querySelectorAll('#venuerow .diffbtn').forEach(btn=>{
    btn.classList.toggle('active', btn.dataset.v===venue);
    btn.onclick = ()=>{
      document.querySelectorAll('#venuerow .diffbtn').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      venue = btn.dataset.v;
    };
  });
  // character swatches are generated from the same 8 colors used in the
  // tournament bracket, so your pick always matches a real bracket slot
  const charrow = document.getElementById('charrow');
  charrow.innerHTML = BRACKET_COLORS.map(c=>
    `<div class="char-swatch${c===playerColor?' active':''}" data-c="${c}" style="background:${c};"></div>`
  ).join('');
  charrow.querySelectorAll('.char-swatch').forEach(sw=>{
    sw.onclick = ()=>{
      charrow.querySelectorAll('.char-swatch').forEach(s=>s.classList.remove('active'));
      sw.classList.add('active');
      playerColor = sw.dataset.c;
    };
  });
}
wireHomeCard();

// ---------- Pause menu ----------
document.getElementById('pauseBtn').onclick = ()=>{
  if(state!=='rally' && state!=='serve') return;
  isPaused = true;
  document.getElementById('pauseOverlay').style.display='flex';
};
document.getElementById('resumeBtn').onclick = ()=>{
  isPaused = false;
  document.getElementById('pauseOverlay').style.display='none';
  last = performance.now(); // avoid a big dt jump after being paused
};
document.getElementById('homeBtn').onclick = ()=>{
  document.getElementById('pauseOverlay').style.display='none';
  document.getElementById('pauseBtn').style.display='none';
  showHomeMenu();
};

// ---------- Settings / account panel ----------
function renderSettings(){
  const card = document.getElementById('settingsCard');
  const acc = getCurrentAccount();
  const name = getCurrentUser();

  if(!acc){
    card.innerHTML = `
      <h1>SETTINGS</h1>
      <div class="sub">create a free local profile to track your stats</div>
      <input type="text" id="acctNameInput" class="account-field" placeholder="Choose a name" maxlength="20">
      <div id="acctError" style="color:#e07a5f;font-size:12px;margin:-4px 0 10px;min-height:14px;"></div>
      <button id="acctCreateBtn">Create Account / Sign In</button>
      <div class="rules" style="margin-top:14px;">
        This just saves your stats in <b>this browser</b> under a name you pick
        — no password, no server, nothing leaves your device. If the name
        already exists here, you'll sign back into it instead of making a
        new one.
      </div>
      <div class="menu-link" id="settingsCloseLink">Close</div>
    `;
    document.getElementById('acctCreateBtn').onclick = ()=>{
      const val = document.getElementById('acctNameInput').value;
      const res = loginOrCreateAccount(val);
      if(!res.ok){ document.getElementById('acctError').textContent = res.msg; return; }
      renderSettings();
    };
    document.getElementById('acctNameInput').addEventListener('keydown', e=>{
      if(e.key==='Enter') document.getElementById('acctCreateBtn').click();
    });
  } else {
    const winPct = acc.gamesPlayed ? Math.round(100*acc.gamesWon/acc.gamesPlayed) : 0;
    const venueRows = Object.entries(acc.byVenue)
      .filter(([,v])=>v.played>0)
      .map(([k,v])=>`<div>${venueLabel(k)}: <b>${v.won}/${v.played}</b> won</div>`).join('') || '<div>No games yet.</div>';
    const diffRows = Object.entries(acc.byDifficulty)
      .filter(([,v])=>v.played>0)
      .map(([k,v])=>`<div>${diffLabel(k)}: <b>${v.won}/${v.played}</b> won</div>`).join('') || '<div>No games yet.</div>';
    card.innerHTML = `
      <h1>SETTINGS</h1>
      <div class="sub">signed in as <b style="color:var(--accent);">${name}</b></div>
      <div class="stat-grid">
        <div class="stat-box"><div class="stat-num">${acc.gamesWon}/${acc.gamesPlayed}</div><div class="stat-lbl">Games Won (${winPct}%)</div></div>
        <div class="stat-box"><div class="stat-num">${acc.tournamentsWon}/${acc.tournamentsPlayed}</div><div class="stat-lbl">Tournaments Won</div></div>
        <div class="stat-box"><div class="stat-num">${acc.pointsFor}</div><div class="stat-lbl">Total Points Scored</div></div>
        <div class="stat-box"><div class="stat-num">${acc.pointsAgainst}</div><div class="stat-lbl">Total Points Against</div></div>
      </div>
      <div class="stat-breakdown">
        <b>By venue</b><br>${venueRows}
        <br><b>By difficulty</b><br>${diffRows}
      </div>
      <button id="acctSignOutBtn" style="background:#2a4d3a;color:var(--accent);border:1px solid var(--accent);box-shadow:none;">Sign Out</button>
      <div class="menu-link" id="settingsCloseLink">Close</div>
    `;
    document.getElementById('acctSignOutBtn').onclick = ()=>{
      signOutAccount();
      renderSettings();
    };
  }
  document.getElementById('settingsCloseLink').onclick = ()=>{
    document.getElementById('settingsOverlay').style.display='none';
  };
}
function venueLabel(v){ return v==='indoor'?'Indoor':v==='outdoor'?'Outdoor':'Moon'; }
function diffLabel(d){ return d==='easy'?'Easy':d==='mid'?'Rec Player':d==='hard'?'Hard':'Super Hard'; }
document.getElementById('settingsBtn').onclick = ()=>{
  renderSettings();
  document.getElementById('settingsOverlay').style.display='flex';
};

// score span structure fix: wrap number in its own text node access
(function fixScoreDom(){
  ['scoreYou','scoreCPU'].forEach(id=>{
    const el = document.getElementById(id);
    const dot = el.querySelector('.server-dot');
    el.innerHTML = '';
    el.appendChild(document.createTextNode('0'));
    el.appendChild(dot);
  });
})();
updatePowerBar();

requestAnimationFrame(loop);
})();
</script>
</body>
</html>
