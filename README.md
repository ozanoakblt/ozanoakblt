<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<title>Program Yöneticisi - Ozan Akbulut</title>
<style>
  * { box-sizing: border-box; -webkit-font-smoothing: none; }
  html, body { margin:0; padding:0; height:100%; font-family: Tahoma,"MS Sans Serif",Geneva,sans-serif; font-size:12px; overflow:hidden; }
  :root {
    --face:#C0C0C0; --hi:#FFFFFF; --lo:#808080; --lo2:#404040;
    --navy:#000080; --navy2:#1084D0; --teal:#008080; --purple:#7B0080;
  }

  #boot {
    position: fixed; inset:0; background:#000; color:#00c000;
    font-family: "Courier New", monospace; font-size:14px; padding:24px;
    z-index: 999; white-space: pre-wrap; line-height:1.6;
  }
  #boot .cursor { display:inline-block; width:8px; height:14px; background:#00c000; animation: blink 1s steps(1) infinite; vertical-align:middle; }
  @keyframes blink { 50% { opacity:0; } }
  #boot.hide { display:none; }

  #desktop {
    position: relative; width:100vw; height:100vh;
    background-color: var(--teal);
    background-image:
      repeating-linear-gradient(45deg, transparent 0 12px, rgba(123,0,128,.55) 12px 24px),
      repeating-linear-gradient(-45deg, transparent 0 12px, rgba(0,0,0,.18) 12px 24px);
    opacity:0; transition: opacity .8s ease; cursor:default; user-select:none;
  }
  #desktop.show { opacity:1; }

  .win {
    position:absolute; background:var(--face);
    border-top:2px solid var(--hi); border-left:2px solid var(--hi);
    border-bottom:2px solid var(--lo2); border-right:2px solid var(--lo2);
    box-shadow: inset 1px 1px 0 #dfdfdf, inset -1px -1px 0 var(--lo), 4px 4px 12px rgba(0,0,0,.4);
    display:flex; flex-direction:column;
    transform: scale(.85); opacity:0; transition: transform .18s ease, opacity .18s ease;
    pointer-events:none;
  }
  .win.open { transform:scale(1); opacity:1; pointer-events:auto; }

  .titlebar { background:linear-gradient(90deg,var(--navy),var(--navy2)); color:#fff; font-weight:bold; padding:3px 4px; display:flex; align-items:center; gap:5px; cursor:move; }
  .titlebar .ticon { width:14px; height:14px; background:var(--face); flex-shrink:0; }
  .titlebar .ttext { flex:1; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
  .winbtn { width:16px; height:14px; background:var(--face); border-top:1px solid var(--hi); border-left:1px solid var(--hi); border-bottom:1px solid var(--lo2); border-right:1px solid var(--lo2); display:flex; align-items:center; justify-content:center; font-size:9px; cursor:pointer; line-height:0; }

  .winbody { padding:12px; flex:1; overflow:auto; font-size:12px; line-height:1.6; }
  .icon-grid { display:grid; grid-template-columns: repeat(3, 84px); gap:8px 4px; }
  .app-icon { display:flex; flex-direction:column; align-items:center; gap:4px; padding:6px 2px; cursor:pointer; }
  .icon-label { font-size:11px; text-align:center; color:#000; line-height:1.2; padding:1px 2px; }
  .app-icon:hover .icon-label { background:var(--navy); color:#fff; }
  .app-icon svg { image-rendering:pixelated; }

  .btn { background:var(--face); border-top:1px solid var(--hi); border-left:1px solid var(--hi); border-bottom:1px solid var(--lo2); border-right:1px solid var(--lo2); font-size:12px; padding:4px 12px; cursor:pointer; display:inline-block; text-decoration:none; color:#000; }
  .btn:active { border-color: var(--lo2) var(--hi) var(--hi) var(--lo2); }

  .type-cursor { display:inline-block; width:7px; height:13px; background:#000; animation: blink 1s steps(1) infinite; vertical-align:text-bottom; margin-left:1px; }

  .taskbar { position:absolute; bottom:0; left:0; right:0; background:var(--face); border-top:2px solid var(--hi); display:flex; align-items:center; justify-content:flex-end; padding:3px 10px; font-size:11px; gap:6px; }
  .clock-box { background:var(--face); border-top:1px solid var(--lo2); border-left:1px solid var(--lo2); border-bottom:1px solid var(--hi); border-right:1px solid var(--hi); padding:2px 8px; }

  table.skills { border-collapse:collapse; width:100%; font-size:12px; }
  table.skills td, table.skills th { border:1px solid var(--lo); padding:4px 6px; text-align:left; }
  table.skills th { background:var(--navy); color:#fff; }

  ::-webkit-scrollbar { width:16px; }
  ::-webkit-scrollbar-track { background:var(--face); }
  ::-webkit-scrollbar-thumb { background:var(--face); border:1px solid var(--lo2); box-shadow: inset 1px 1px 0 #dfdfdf, inset -1px -1px 0 var(--lo); }
</style>
</head>
<body>

<div id="boot"><span id="boot-text"></span><span class="cursor"></span></div>

<div id="desktop">

  <div class="win open" id="win-main" style="left:50px; top:30px; width:420px;">
    <div class="titlebar" data-drag="win-main">
      <div class="ticon"></div>
      <div class="ttext">Program Yöneticisi - Ozan Akbulut</div>
      <div class="winbtn">▁</div><div class="winbtn">□</div>
    </div>
    <div class="winbody">
      <div class="icon-grid">
        <div class="app-icon" ondblclick="openWin('win-about')">
          <svg width="32" height="32" viewBox="0 0 16 16"><rect x="2" y="2" width="12" height="12" fill="#fff" stroke="#000"/><circle cx="8" cy="6" r="2" fill="#000080"/><path d="M4 13c0-2 8-2 8 0" fill="#000080"/></svg>
          <div class="icon-label">Hakkımda</div>
        </div>
        <div class="app-icon" ondblclick="openWin('win-skills')">
          <svg width="32" height="32" viewBox="0 0 16 16"><rect x="1" y="1" width="14" height="14" fill="#c0c0c0" stroke="#000"/><circle cx="8" cy="8" r="4" fill="#e0c000" stroke="#000"/><rect x="7" y="1" width="2" height="3" fill="#000"/><rect x="7" y="12" width="2" height="3" fill="#000"/></svg>
          <div class="icon-label">Denetim Masası</div>
        </div>
        <div class="app-icon" ondblclick="openWin('win-projects')">
          <svg width="32" height="32" viewBox="0 0 20 16"><rect x="1" y="3" width="18" height="12" fill="#e0c060" stroke="#000"/><rect x="1" y="1" width="9" height="3" fill="#e0c060" stroke="#000"/></svg>
          <div class="icon-label">Projelerim</div>
        </div>
        <div class="app-icon" ondblclick="openWin('win-contact')">
          <svg width="32" height="32" viewBox="0 0 16 16"><rect x="1" y="3" width="14" height="10" fill="#fff" stroke="#000"/><path d="M1 3l7 6 7-6" fill="none" stroke="#000"/></svg>
          <div class="icon-label">İletişim</div>
        </div>
        <div class="app-icon" ondblclick="window.open('https://github.com/ozanoakblt','_blank')">
          <svg width="32" height="32" viewBox="0 0 16 16"><circle cx="8" cy="8" r="7" fill="#24292e" stroke="#000"/><path d="M8 3c-2.8 0-5 2.2-5 5 0 2.2 1.4 4 3.4 4.7.2 0 .3-.1.3-.3v-1c-1.4.3-1.7-.6-1.7-.6-.2-.6-.5-.7-.5-.7-.5-.3 0-.3 0-.3.5 0 .8.5.8.5.5.8 1.3.6 1.6.4.1-.4.2-.6.4-.8-1.1-.1-2.3-.6-2.3-2.5 0-.6.2-1 .5-1.4 0-.1-.2-.6.1-1.3 0 0 .4-.1 1.4.5.4-.1.8-.2 1.3-.2s.9.1 1.3.2c1-.6 1.4-.5 1.4-.5.3.7.1 1.2.1 1.3.3.4.5.9.5 1.4 0 1.9-1.2 2.4-2.3 2.5.2.2.4.5.4 1v1.4c0 .2.1.3.3.3C10.6 12 12 10.2 12 8c0-2.8-2.2-5-5-5z" fill="#fff"/></svg>
          <div class="icon-label">GitHub</div>
        </div>
      </div>
    </div>
  </div>

  <div class="win" id="win-about" style="left:120px; top:70px; width:360px;">
    <div class="titlebar" data-drag="win-about">
      <div class="ticon"></div><div class="ttext">Hakkımda</div>
      <div class="winbtn" onclick="closeWin('win-about')">×</div>
    </div>
    <div class="winbody">
      <b>Ozan Akbulut</b><br>
      <span style="color:#555;">Yönetim Bilişim Sistemleri Öğrencisi &amp; MLOps Tutkunu</span>
      <div style="margin-top:10px;" id="typewriter"></div>
      <div style="margin-top:12px; text-align:right;"><span class="btn" onclick="closeWin('win-about')">Tamam</span></div>
    </div>
  </div>

  <div class="win" id="win-skills" style="left:180px; top:110px; width:380px;">
    <div class="titlebar" data-drag="win-skills">
      <div class="ticon"></div><div class="ttext">Denetim Masası - Teknik Yetkinlikler</div>
      <div class="winbtn" onclick="closeWin('win-skills')">×</div>
    </div>
    <div class="winbody">
      <table class="skills">
        <tr><th>Kategori</th><th>Yetkinlikler</th></tr>
        <tr><td>Programlama</td><td>Python, SQL</td></tr>
        <tr><td>Veri &amp; ML</td><td>Numpy, Pandas, Matplotlib, Seaborn</td></tr>
        <tr><td>Makine Öğrenimi</td><td>Scikit-Learn</td></tr>
        <tr><td>MLOps &amp; Dağıtım</td><td>MLflow, Docker, Kubernetes, FastAPI</td></tr>
      </table>
      <div style="margin-top:12px; text-align:right;"><span class="btn" onclick="closeWin('win-skills')">Tamam</span></div>
    </div>
  </div>

  <div class="win" id="win-projects" style="left:240px; top:60px; width:400px;">
    <div class="titlebar" data-drag="win-projects">
      <div class="ticon"></div><div class="ttext">Projelerim</div>
      <div class="winbtn" onclick="closeWin('win-projects')">×</div>
    </div>
    <div class="winbody">
      <div class="icon-grid" style="grid-template-columns: repeat(3, 108px);">
        <div class="app-icon" ondblclick="openWin('win-proj-house')">
          <svg width="32" height="32" viewBox="0 0 16 16"><path d="M8 1 1 6v9h14V6z" fill="#c04020" stroke="#000"/><rect x="6" y="9" width="4" height="6" fill="#fff" stroke="#000"/></svg>
          <div class="icon-label">House Energy MLOps</div>
        </div>
        <div class="app-icon" ondblclick="openWin('win-proj-kredi')">
          <svg width="32" height="32" viewBox="0 0 16 16"><rect x="1" y="4" width="14" height="9" fill="#c0c0c0" stroke="#000"/><rect x="1" y="4" width="14" height="3" fill="#000080"/><rect x="10" y="9" width="4" height="3" fill="#c0c000" stroke="#000"/></svg>
          <div class="icon-label">KrediRadar</div>
        </div>
        <div class="app-icon" ondblclick="openWin('win-proj-magaza')">
          <svg width="32" height="32" viewBox="0 0 16 16"><rect x="1" y="1" width="14" height="14" fill="#fff" stroke="#000"/><rect x="3" y="9" width="2" height="4" fill="#000080"/><rect x="6" y="6" width="2" height="7" fill="#008080"/><rect x="9" y="8" width="2" height="5" fill="#800080"/></svg>
          <div class="icon-label">Mağaza Analizi</div>
        </div>
      </div>
    </div>
  </div>

  <div class="win" id="win-proj-house" style="left:160px; top:150px; width:340px;">
    <div class="titlebar" data-drag="win-proj-house"><div class="ticon"></div><div class="ttext">House Energy MLOps Project</div><div class="winbtn" onclick="closeWin('win-proj-house')">×</div></div>
    <div class="winbody">
      Bir evin enerji tüketimini tahmin etmek için geliştirilmiş <b>uçtan uca bir MLOps çözümü</b>.<br><br>
      <b>Teknolojiler:</b> Python, MLflow, FastAPI, Docker, Kubernetes<br><br>
      Model, Docker ile imajlanıp Kubernetes üzerinden kolayca dağıtılabilir bir web servisi olarak sunuluyor.
      <div style="margin-top:12px; display:flex; justify-content:space-between;">
        <a class="btn" href="https://github.com/ozanoakblt/House_Energy_MLOps_Project" target="_blank">Repoya Git</a>
        <span class="btn" onclick="closeWin('win-proj-house')">Kapat</span>
      </div>
    </div>
  </div>

  <div class="win" id="win-proj-kredi" style="left:200px; top:180px; width:340px;">
    <div class="titlebar" data-drag="win-proj-kredi"><div class="ticon"></div><div class="ttext">KrediRadar - MLOps Kredi Risk Sistemi</div><div class="winbtn" onclick="closeWin('win-proj-kredi')">×</div></div>
    <div class="winbody">
      Bankacılık kredi risk skorlaması için <b>uçtan uca MLOps sistemi</b> — model eğitimi, orkestrasyon, serving, monitoring, CI/CD ve retro tarzda bir frontend.<br><br>
      <b>Teknolojiler:</b> MLflow, Airflow, FastAPI, Prometheus, Grafana, Docker, GitHub Actions<br><br>
      Proje, veriden kullanıcıya ulaşan tüm zinciri (5 seviye) kapsıyor.
      <div style="margin-top:12px; display:flex; justify-content:space-between;">
        <a class="btn" href="https://github.com/ozanoakblt/kredi-radar" target="_blank">Repoya Git</a>
        <span class="btn" onclick="closeWin('win-proj-kredi')">Kapat</span>
      </div>
    </div>
  </div>

  <div class="win" id="win-proj-magaza" style="left:230px; top:210px; width:340px;">
    <div class="titlebar" data-drag="win-proj-magaza"><div class="ticon"></div><div class="ttext">Online Mağaza Satış Trendleri</div><div class="winbtn" onclick="closeWin('win-proj-magaza')">×</div></div>
    <div class="winbody">
      İngiltere merkezli bir online mağazanın 2010-2011 satış verilerinin analizi.<br><br>
      <b>Odak:</b> Zaman, ürün ve lokasyon ekseninde satış trendleri; en yoğun alışveriş saatleri ve en çok satan ürünler.<br><br>
      <b>Teknolojiler:</b> Numpy, Pandas, Matplotlib, Seaborn
      <div style="margin-top:12px; display:flex; justify-content:space-between;">
        <a class="btn" href="https://github.com/ozanoakblt/Online-Magaza-Satis-Trendlerinin-Zaman-Urun-ve-Lokasyon-Ekseninin-Analizi" target="_blank">Repoya Git</a>
        <span class="btn" onclick="closeWin('win-proj-magaza')">Kapat</span>
      </div>
    </div>
  </div>

  <div class="win" id="win-contact" style="left:280px; top:250px; width:320px;">
    <div class="titlebar" data-drag="win-contact"><div class="ticon"></div><div class="ttext">İletişim</div><div class="winbtn" onclick="closeWin('win-contact')">×</div></div>
    <div class="winbody">
      <b>Üniversite:</b> Karadeniz Teknik Üniversitesi<br>
      Yönetim Bilişim Sistemleri (2023 - 2027)<br><br>
      <b>E-posta:</b> ozan.akbltt@gmail.com<br>
      <b>LinkedIn:</b> <a href="https://www.linkedin.com/in/ozan-akbulutt/" target="_blank">ozan-akbulutt</a><br>
      <b>GitHub:</b> <a href="https://github.com/ozanoakblt" target="_blank">ozanoakblt</a>
      <div style="margin-top:12px; text-align:right;"><span class="btn" onclick="closeWin('win-contact')">Tamam</span></div>
    </div>
  </div>

  <div class="taskbar"><div class="clock-box" id="clock">00:00:00</div></div>
</div>

<script>
const bootLines = [
  "Microsoft Windows 3.1",
  "Copyright (c) Ozan Akbulut Systems",
  "",
  "Starting Windows 3.1...",
  "Loading VGA driver... OK",
  "Loading MLOPS.DRV... OK",
  "Loading PYTHON.SYS... OK",
  "Mounting KREDIRADAR.VXD... OK",
  "",
  "Program Yoneticisi baslatiliyor..."
];

function typeBoot(cb) {
  const el = document.getElementById('boot-text');
  let i = 0, li = 0;
  function step() {
    if (li >= bootLines.length) { cb(); return; }
    const line = bootLines[li];
    if (i <= line.length) {
      el.textContent = bootLines.slice(0, li).join('\n') + (li>0?'\n':'') + line.slice(0, i);
      i++;
      setTimeout(step, line.length ? 18 : 5);
    } else {
      li++; i = 0;
      setTimeout(step, 120);
    }
  }
  step();
}

typeBoot(() => {
  setTimeout(() => {
    document.getElementById('boot').classList.add('hide');
    document.getElementById('desktop').classList.add('show');
  }, 500);
});

function openWin(id) {
  const w = document.getElementById(id);
  w.classList.add('open');
  bringToFront(id);
  if (id === 'win-about') runTypewriter();
}
function closeWin(id) { document.getElementById(id).classList.remove('open'); }
function bringToFront(id) {
  let max = 10;
  document.querySelectorAll('.win').forEach(w => { const z = parseInt(w.style.zIndex||10); if (z>max) max=z; });
  document.getElementById(id).style.zIndex = max+1;
}

let dragState = null;
document.querySelectorAll('[data-drag]').forEach(bar => {
  bar.addEventListener('mousedown', e => {
    const id = bar.getAttribute('data-drag');
    const w = document.getElementById(id);
    bringToFront(id);
    dragState = { w, offX: e.clientX - w.offsetLeft, offY: e.clientY - w.offsetTop };
  });
});
document.addEventListener('mousemove', e => {
  if (!dragState) return;
  dragState.w.style.left = Math.max(0, e.clientX - dragState.offX) + 'px';
  dragState.w.style.top = Math.max(0, e.clientY - dragState.offY) + 'px';
});
document.addEventListener('mouseup', () => dragState = null);

const bio = "Karadeniz Teknik Universitesi Yonetim Bilisim Sistemleri 3. sinif ogrencisiyim. Teorik bilgimi, uctan uca model gelistirme ve devreye alma sureclerine odaklanarak pratige dokuyorum. MLOps, veri analizi ve yazilim gelistirme alanlarinda projeler uretiyorum.";
let typed = false;
function runTypewriter() {
  if (typed) return;
  typed = true;
  const el = document.getElementById('typewriter');
  let i = 0;
  function step() {
    el.innerHTML = bio.slice(0, i) + '<span class="type-cursor"></span>';
    i++;
    if (i <= bio.length) setTimeout(step, 14);
    else el.innerHTML = bio;
  }
  step();
}

function updateClock() {
  const now = new Date();
  const p = n => String(n).padStart(2,'0');
  document.getElementById('clock').textContent = p(now.getHours())+':'+p(now.getMinutes())+':'+p(now.getSeconds());
}
setInterval(updateClock, 1000);
updateClock();
</script>
</body>
</html>
