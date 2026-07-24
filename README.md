<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NZN CYBERLAB // vulnerable by design</title>
<style>
  :root{
    --bg:#020a03;
    --panel:#04140a;
    --border:#0e3d1f;
    --green:#39ff6a;
    --green-dim:#1f8a44;
    --amber:#ffb300;
    --red:#ff4d4d;
    --text:#c9ffd8;
    --mono: "JetBrains Mono","Share Tech Mono","Courier New",monospace;
  }
  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;min-height:100%;background:var(--bg);font-family:var(--mono);color:var(--text);}
  #matrix{position:fixed;inset:0;z-index:0;}
  .scanlines{position:fixed;inset:0;z-index:2;pointer-events:none;
    background:repeating-linear-gradient(to bottom, rgba(0,0,0,0) 0px, rgba(0,0,0,0) 2px, rgba(0,0,0,0.12) 3px);
    mix-blend-mode:overlay;opacity:0.5;}
  .vignette{position:fixed;inset:0;z-index:2;pointer-events:none;box-shadow:inset 0 0 180px 40px rgba(0,0,0,0.85);}

  .disclaimer{
    position:relative;z-index:5;
    background:rgba(255,179,0,0.08);
    border-bottom:1px solid var(--amber);
    color:var(--amber);
    font-size:11px;
    text-align:center;
    padding:8px 12px;
    letter-spacing:0.02em;
  }

  .wrap{position:relative;z-index:3;max-width:900px;margin:0 auto;padding:24px 16px 80px;}

  header{text-align:center;margin-bottom:18px;}
  .logo{
    font-size:clamp(16px,4vw,26px);
    color:var(--green);
    letter-spacing:0.15em;
    text-shadow:0 0 8px rgba(57,255,106,0.5);
    margin-bottom:4px;
  }
  .tagline{color:var(--green-dim);font-size:11px;letter-spacing:0.3em;text-transform:uppercase;}

  nav{
    display:flex;gap:8px;flex-wrap:wrap;justify-content:center;
    margin:22px 0;border:1px solid var(--border);background:rgba(3,20,10,0.5);padding:8px;
  }
  nav button{
    background:none;border:1px solid var(--border);color:var(--green-dim);
    font-family:var(--mono);font-size:12px;padding:8px 14px;cursor:pointer;letter-spacing:0.04em;
  }
  nav button:hover{color:var(--green);border-color:var(--green);}
  nav button.active{color:var(--bg);background:var(--green);border-color:var(--green);}
  nav button.locked{opacity:0.4;cursor:not-allowed;}
  nav button.locked:hover{color:var(--green-dim);border-color:var(--border);}

  .status-bar{
    display:flex;justify-content:space-between;align-items:center;
    border:1px solid var(--border);background:rgba(3,20,10,0.6);padding:10px 16px;margin-bottom:22px;
    font-size:12px;color:var(--green-dim);
  }
  .status-bar b{color:var(--green);}
  .progress-track{flex:1;height:6px;margin:0 16px;background:#04180b;border:1px solid var(--border);position:relative;overflow:hidden;}
  .progress-fill{position:absolute;top:0;left:0;bottom:0;width:0%;background:linear-gradient(90deg,var(--green-dim),var(--green));transition:width 0.5s ease;box-shadow:0 0 8px rgba(57,255,106,0.6);}

  .panel{
    border:1px solid var(--border);
    background:linear-gradient(180deg,rgba(4,25,12,0.9),rgba(2,15,7,0.9));
    padding:20px;margin-bottom:20px;display:none;
  }
  .panel.active{display:block;}
  .panel h2{color:var(--green);font-size:15px;letter-spacing:0.08em;margin-top:0;text-transform:uppercase;}
  .panel p{font-size:13px;line-height:1.7;color:var(--text);}
  .panel .muted{color:var(--green-dim);font-size:12px;}

  .module-card{border:1px solid var(--border);padding:12px 14px;margin-bottom:10px;font-size:13px;}
  .module-card b{color:var(--green);}
  .module-tag{display:inline-block;font-size:10px;padding:2px 8px;border:1px solid var(--border);color:var(--green-dim);margin-left:8px;text-transform:uppercase;}
  .module-tag.done{color:var(--bg);background:var(--green);border-color:var(--green);}

  label{display:block;font-size:12px;color:var(--green-dim);margin-bottom:4px;margin-top:12px;}
  input[type=text], input[type=password], textarea{
    width:100%;background:#010a04;border:1px solid var(--border);color:var(--text);
    font-family:var(--mono);font-size:13px;padding:9px 10px;outline:none;
  }
  input:focus, textarea:focus{border-color:var(--green-dim);}
  textarea{min-height:80px;resize:vertical;}
  .btn{
    margin-top:14px;background:var(--green-dim);color:#02170a;border:none;font-family:var(--mono);
    font-weight:bold;font-size:12px;padding:9px 16px;cursor:pointer;letter-spacing:0.05em;
  }
  .btn:hover{background:var(--green);}

  .report-list{margin-top:18px;border-top:1px dashed var(--border);padding-top:14px;}
  .report-item{border:1px solid var(--border);padding:8px 10px;margin-bottom:8px;font-size:13px;background:rgba(0,0,0,0.2);}

  .feedback{margin-top:10px;font-size:12px;min-height:16px;}
  .feedback.ok{color:var(--green);}
  .feedback.bad{color:var(--red);}

  .binary-box{
    font-size:12px;line-height:1.8;background:#010a04;border:1px solid var(--border);
    padding:12px;word-break:break-all;color:var(--green);margin:12px 0;
  }

  .admin-badge{
    display:inline-block;font-size:11px;padding:3px 10px;border:1px solid var(--green);color:var(--green);
    margin-bottom:10px;letter-spacing:0.08em;
  }

  .flag-banner{
    position:fixed;top:14px;left:50%;transform:translateX(-50%);
    z-index:10;background:rgba(2,15,7,0.95);border:1px solid var(--green);
    color:var(--green);padding:12px 20px;font-size:13px;box-shadow:0 0 30px rgba(57,255,106,0.3);
    display:none;text-align:center;
  }
  .flag-banner.show{display:block;}

  footer{margin-top:26px;font-size:10px;color:#1f4d2a;letter-spacing:0.1em;text-align:center;}
</style>
</head>
<body>

<canvas id="matrix"></canvas>
<div class="scanlines"></div>
<div class="vignette"></div>

<div class="disclaimer">⚠️ Situs ini SENGAJA dibuat rentan untuk tujuan edukasi. Semua "serangan" hanya berjalan lokal di browser Anda sendiri — jangan gunakan teknik ini pada sistem milik orang lain tanpa izin.</div>

<div class="flag-banner" id="flagBanner"></div>

<div class="wrap">
  <header>
    <div class="logo">NZN CYBERLAB</div>
    <div class="tagline">belajar keamanan siber lewat praktik langsung</div>
  </header>

  <div class="status-bar">
    <span>FLAG DITEMUKAN: <b id="flagCount">0</b>/3</span>
    <div class="progress-track"><div class="progress-fill" id="progressFill"></div></div>
    <span id="statusMsg">MULAI EKSPLORASI</span>
  </div>

  <nav>
    <button class="nav-btn active" data-section="home" onclick="showSection('home')">Beranda</button>
    <button class="nav-btn" data-section="report" onclick="showSection('report')">Laporkan Bug</button>
    <button class="nav-btn" data-section="login" onclick="showSection('login')">Login</button>
    <button class="nav-btn locked" id="adminNav" onclick="tryAdminNav()">🔒 Admin Panel</button>
  </nav>

  <!-- HOME -->
  <div class="panel active" id="section-home">
    <h2>&gt;&gt; Selamat datang, operator</h2>
    <p>NZN CyberLab adalah lingkungan latihan yang sengaja diberi celah keamanan agar kamu bisa mempraktikkan konsep-konsep dasar keamanan web secara langsung, bukan cuma teori.</p>

    <div class="module-card">
      <b>Modul 1 — Cross-Site Scripting (XSS)</b>
      <span class="module-tag" id="tag1">belum</span>
      <p class="muted" style="margin:6px 0 0;">Lokasi: halaman <b>Laporkan Bug</b>. Form ini menampilkan kembali isi laporanmu ke halaman tanpa penyaringan. Coba pikirkan bagaimana sebuah script bisa dijalankan lewat elemen HTML biasa (bukan cuma tag &lt;script&gt;).</p>
    </div>

    <div class="module-card">
      <b>Modul 2 — SQL Injection</b>
      <span class="module-tag" id="tag2">belum</span>
      <p class="muted" style="margin:6px 0 0;">Lokasi: halaman <b>Login</b>. Form ini kemungkinan menyusun query SQL langsung dari input username. Pikirkan bagaimana sebuah kondisi yang selalu bernilai benar bisa membuat autentikasi terlewati.</p>
    </div>

    <div class="module-card">
      <b>Modul 3 — Binary Code (The Root Code)</b>
      <span class="module-tag" id="tag3">terkunci</span>
      <p class="muted" style="margin:6px 0 0;">Terbuka setelah kamu berhasil masuk ke Admin Panel lewat Modul 2.</p>
    </div>
  </div>

  <!-- REPORT / XSS -->
  <div class="panel" id="section-report">
    <h2>&gt;&gt; Laporkan Bug</h2>
    <p class="muted">Form ini untuk melaporkan masalah pada situs. Laporanmu akan tampil di daftar di bawah setelah dikirim.</p>
    <label for="reportName">Nama</label>
    <input type="text" id="reportName" placeholder="nama kamu">
    <label for="reportMsg">Isi Laporan</label>
    <textarea id="reportMsg" placeholder="tuliskan laporanmu di sini..."></textarea>
    <button class="btn" onclick="submitReport()">Kirim Laporan</button>
    <div class="report-list" id="reportList"></div>
  </div>

  <!-- LOGIN / SQLi -->
  <div class="panel" id="section-login">
    <h2>&gt;&gt; Login</h2>
    <p class="muted">Masuk ke akun kamu untuk mengakses fitur tambahan.</p>
    <label for="username">Username</label>
    <input type="text" id="username" placeholder="username" autocomplete="off">
    <label for="password">Password</label>
    <input type="password" id="password" placeholder="password" autocomplete="off">
    <button class="btn" onclick="tryLogin()">Login</button>
    <div class="feedback" id="loginFeedback"></div>
  </div>

  <!-- ADMIN PANEL -->
  <div class="panel" id="section-admin">
    <span class="admin-badge">ACCESS GRANTED</span>
    <h2>&gt;&gt; Admin Panel</h2>
    <p>Autentikasi berhasil dilewati. Selamat, kamu baru saja mempraktikkan <b>SQL Injection auth bypass</b> menggunakan kondisi tautologi pada kolom username.</p>

    <div class="module-card">
      <b>📀 Binary — The Root Code</b>
      <p class="muted" style="margin:6px 0 10px;">Decode kode biner berikut menjadi teks biasa (ASCII 8-bit per karakter):</p>
      <div class="binary-box" id="binaryCode">01100001 01101011 01110101 00100000 01110011 01100101 01100010 01110101 01100001 01101000 00100000 01110110 01101001 01110010 01110100 01110101 01100001 01101100 00100000 01000101 01101110 01110110 01101001 01110010 01101111 01101110 01101101 01100101 01101110 01110100 00100000 01111001 01100001 01101001 01110100 01110101 00100000 01110000 01110010 01101111 01111000 01101101 01101111 01111000</div>
      <label for="binaryInput">Jawaban (teks hasil decode)</label>
      <input type="text" id="binaryInput" placeholder="tulis hasil decode di sini...">
      <button class="btn" onclick="checkBinary()">Submit</button>
      <div class="feedback" id="binaryFeedback"></div>
    </div>

    <div class="module-card" style="opacity:0.5;">
      <b>🔐 Cryptography Basics</b>
      <span class="module-tag">segera hadir</span>
    </div>
    <div class="module-card" style="opacity:0.5;">
      <b>🌐 Web Exploitation Lanjutan</b>
      <span class="module-tag">segera hadir</span>
    </div>
  </div>

  <footer>NZN CYBERLAB &middot; environment latihan lokal &middot; v1.0</footer>
</div>

<script>
/* ================= MATRIX RAIN ================= */
const canvas = document.getElementById('matrix');
const ctx = canvas.getContext('2d');
let w,h,columns,drops;
const chars = "アイウエオカキクケコサシスセソタチツテトナニヌネノハヒフヘホマミムメモヤユヨラリルレロワヲンNZN0123456789";
const fontSize = 15;
function resize(){ w=canvas.width=window.innerWidth; h=canvas.height=window.innerHeight; columns=Math.floor(w/fontSize); drops=new Array(columns).fill(1); }
window.addEventListener('resize',resize); resize();
function drawMatrix(){
  ctx.fillStyle="rgba(2,10,3,0.08)"; ctx.fillRect(0,0,w,h); ctx.font=fontSize+"px monospace";
  for(let i=0;i<drops.length;i++){
    const text=chars[Math.floor(Math.random()*chars.length)];
    const x=i*fontSize, y=drops[i]*fontSize;
    ctx.fillStyle = Math.random()>0.95 ? "#d6ffe0" : "#39ff6a";
    ctx.fillText(text,x,y);
    if(y>h && Math.random()>0.975) drops[i]=0;
    drops[i]++;
  }
}
setInterval(drawMatrix,45);

/* ================= DEMO COOKIE (untuk latihan XSS) ================= */
document.cookie = "nzn_session=" + Math.random().toString(36).slice(2,12) + "; path=/";

/* ================= NAV / SECTIONS ================= */
let adminUnlocked = false;
function showSection(name){
  if(name==='admin' && !adminUnlocked) return;
  document.querySelectorAll('.panel').forEach(p=>p.classList.remove('active'));
  document.getElementById('section-'+name).classList.add('active');
  document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active'));
  const btn = document.querySelector('.nav-btn[data-section="'+name+'"]');
  if(btn) btn.classList.add('active');
  if(name==='admin'){
    document.getElementById('adminNav').classList.add('active');
  }
}
function tryAdminNav(){
  if(!adminUnlocked){
    showFlagBanner('Akses ditolak. Kamu belum berhasil membuka Admin Panel.', false);
    return;
  }
  showSection('admin');
}

/* ================= FLAG TRACKING ================= */
const flagsFound = {1:false,2:false,3:false};
function markFlag(n){
  if(flagsFound[n]) return;
  flagsFound[n]=true;
  const count = Object.values(flagsFound).filter(Boolean).length;
  document.getElementById('flagCount').textContent = count;
  document.getElementById('progressFill').style.width = (count/3*100)+"%";
  document.getElementById('statusMsg').textContent = count>=3 ? "SEMUA FLAG DITEMUKAN" : "IN PROGRESS";
  const tag = document.getElementById('tag'+n);
  if(tag){ tag.textContent="selesai"; tag.classList.add('done'); }
}

function showFlagBanner(msg, ok=true){
  const b = document.getElementById('flagBanner');
  b.textContent = msg;
  b.style.borderColor = ok ? "var(--green)" : "var(--red)";
  b.style.color = ok ? "var(--green)" : "var(--red)";
  b.classList.add('show');
  setTimeout(()=>b.classList.remove('show'), 3200);
}

/* ================= MODUL 1: XSS via laporan (innerHTML tanpa sanitasi) ================= */
function submitReport(){
  const name = document.getElementById('reportName').value || 'Anonim';
  const msg = document.getElementById('reportMsg').value;
  if(!msg.trim()) return;
  const list = document.getElementById('reportList');
  const item = document.createElement('div');
  item.className = 'report-item';
  // SENGAJA tidak disaring -> ini adalah celah XSS untuk latihan
  item.innerHTML = '<b>' + name + ':</b> ' + msg;
  list.appendChild(item);
  document.getElementById('reportMsg').value = '';
}

// Deteksi ketika payload XSS berhasil memicu alert(document.cookie)
const originalAlert = window.alert;
window.alert = function(msg){
  originalAlert(msg);
  try{
    const val = String(msg);
    if(val.length > 0 && val === document.cookie){
      markFlag(1);
      showFlagBanner('FLAG 1 DITEMUKAN! XSS berhasil mencuri cookie. Mengalihkan...');
      setTimeout(()=>{ window.location.href = 'https://ftn-cyber.github.io/Base64/'; }, 1800);
    }
  }catch(e){}
};

/* ================= MODUL 2: SQL Injection login bypass ================= */
function tryLogin(){
  const u = document.getElementById('username').value;
  const feedback = document.getElementById('loginFeedback');
  const uNorm = u.toLowerCase().replace(/\s+/g,' ').trim();
  const sqliPattern = /'\s*or\s*1\s*=\s*1|'\s*or\s*'1'\s*=\s*'1'|or\s+1\s*=\s*1/;
  if(sqliPattern.test(uNorm)){
    adminUnlocked = true;
    document.getElementById('adminNav').classList.remove('locked');
    document.getElementById('adminNav').textContent = '🔓 Admin Panel';
    markFlag(2);
    feedback.className = 'feedback ok';
    feedback.textContent = 'Autentikasi berhasil dilewati! Mengalihkan ke Admin Panel...';
    setTimeout(()=>showSection('admin'), 900);
  } else {
    feedback.className = 'feedback bad';
    feedback.textContent = 'Username atau password salah.';
  }
}

/* ================= MODUL 3: Binary decode ================= */
function checkBinary(){
  const val = document.getElementById('binaryInput').value;
  const feedback = document.getElementById('binaryFeedback');
  const norm = val.toLowerCase().replace(/\s+/g,'');
  const answer = "akusebuahvirtualenvironmentyaituproxmox";
  if(norm === answer){
    markFlag(3);
    feedback.className = 'feedback ok';
    feedback.textContent = 'FLAG 3 DITEMUKAN! Decode benar: "aku sebuah virtual environment yaitu proxmox"';
    showFlagBanner('FLAG 3 DITEMUKAN!');
  } else {
    feedback.className = 'feedback bad';
    feedback.textContent = 'Belum tepat, coba periksa lagi tabel biner ke ASCII-nya.';
  }
}
</script>

</body>
</html>

