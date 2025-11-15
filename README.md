<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Key System — Generator & Admin (Manual GitHub Export)</title>
<style>
:root{
  --bg:#041028; --panel:#081628; --accent:#2b80ff; --muted:#9fb3d6;
  --card:#0f2033; --danger:#ff5c5c; --ok:#16a34a;
}
*{box-sizing:border-box}
body{margin:0;font-family:Inter,system-ui,Roboto,Arial;background:linear-gradient(180deg,var(--bg),#021223);color:#e6f7ff}
.container{max-width:1100px;margin:20px auto;padding:16px}
.header{display:flex;align-items:center;gap:12px}
.brand{font-weight:800;color:var(--accent);font-size:20px}
.lead{color:var(--muted);font-size:14px;margin-left:auto}
.card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border:1px solid rgba(255,255,255,0.03);padding:14px;border-radius:10px}
.grid{display:grid;grid-template-columns:1fr 420px;gap:16px;margin-top:14px}
@media (max-width:980px){.grid{grid-template-columns:1fr}}
.controls{display:flex;gap:8px;flex-wrap:wrap}
.btn{padding:8px 12px;border-radius:8px;border:0;background:var(--accent);color:white;cursor:pointer}
.btn.ghost{background:transparent;border:1px solid rgba(255,255,255,0.04)}
.btn.small{padding:6px 8px;font-size:13px}
.input,textarea,select{width:100%;padding:10px;border-radius:8px;border:0;background:rgba(255,255,255,0.02);color:#eaf4ff;margin-top:6px}
.h2{font-size:18px;margin:0 0 8px 0}
.note{color:var(--muted);font-size:13px;margin-top:8px}
.small{font-size:13px;color:var(--muted)}
.user-card{display:flex;gap:8px;align-items:center;justify-content:space-between;background:rgba(255,255,255,0.01);padding:8px;border-radius:8px;margin-bottom:8px}
.key-item{background:rgba(255,255,255,0.02);padding:8px;border-radius:8px;margin-bottom:8px;display:flex;justify-content:space-between;align-items:center;font-family:ui-monospace,monospace}
.viewer{background:#071428;padding:10px;border-radius:8px;color:#dbeefe;white-space:pre-wrap}
.table{width:100%;border-collapse:collapse;margin-top:10px}
.table th,.table td{padding:8px;text-align:left;border-bottom:1px solid rgba(255,255,255,0.02);font-size:13px}
.footer{margin-top:12px;color:var(--muted);font-size:13px;text-align:center}
.tag{padding:6px 8px;border-radius:999px;background:rgba(255,255,255,0.02);color:var(--muted);font-size:13px}
.actions{display:flex;gap:8px}
.bad{background:var(--danger);color:white;border-radius:6px;padding:6px 8px}
.ok{background:var(--ok);color:white;border-radius:6px;padding:6px 8px}
.search{display:flex;gap:8px}
.header-row{display:flex;gap:12px;align-items:center}
.right{margin-left:auto;display:flex;gap:8px}
hr{border:0;border-top:1px solid rgba(255,255,255,0.02);margin:12px 0}
</style>
</head>
<body>
<div class="container">
  <div class="header">
    <div>
      <div class="brand">Key System — Generator & Admin (Manual)</div>
      <div class="small note">Generate 24-hour keys, manage users, export JSON for your GitHub keys.json</div>
    </div>
    <div class="lead">Hosted locally — no token required</div>
  </div>

  <div class="card grid">
    <!-- LEFT: Main UI -->
    <div>
      <!-- Auth / account -->
      <div class="card">
        <div style="display:flex;align-items:center;gap:12px">
          <div>
            <div class="h2">Account</div>
            <div class="small note">Signup / Login (data stored in your browser)</div>
          </div>
          <div class="right">
            <div id="who" class="tag">Not logged in</div>
            <button id="btnLogout" class="btn ghost" style="display:none">Logout</button>
          </div>
        </div>
        <hr/>
        <div style="display:flex;gap:12px;flex-wrap:wrap">
          <div style="flex:1;min-width:220px">
            <label class="small">Email</label>
            <input id="suEmail" class="input" placeholder="you@example.com"/>
            <label class="small">Username</label>
            <input id="suUser" class="input" placeholder="username"/>
            <label class="small">Password</label>
            <input id="suPass" class="input" placeholder="password" type="password"/>
            <div style="display:flex;gap:8px;margin-top:8px">
              <button id="btnSignup" class="btn small">Create account</button>
              <button id="btnDemo" class="btn ghost small">Create demo user</button>
            </div>
            <div id="suMsg" class="small note"></div>
          </div>

          <div style="flex:1;min-width:220px">
            <label class="small">Username (login)</label>
            <input id="liUser" class="input" placeholder="username"/>
            <label class="small">Password (login)</label>
            <input id="liPass" class="input" placeholder="password" type="password"/>
            <div style="display:flex;gap:8px;margin-top:8px">
              <button id="btnLogin" class="btn small">Login</button>
            </div>
            <div id="liMsg" class="small note"></div>
          </div>
        </div>
      </div>

      <!-- Generate / Generator -->
      <div class="card" style="margin-top:12px">
        <div class="h2">Generator</div>
        <div class="small note">Generate a numeric key (XXXX-XXXX-XXXX-XXXX). Keys are valid for 24 hours.</div>
        <div style="display:flex;gap:8px;margin-top:10px">
          <select id="genUserSelect" class="input" style="width:220px"></select>
          <button id="btnGenKey" class="btn">Generate Key for Selected User</button>
          <button id="btnRevoke" class="btn ghost">Revoke Latest Key</button>
        </div>

        <div style="margin-top:12px;display:flex;gap:8px;flex-wrap:wrap;align-items:center">
          <div style="min-width:220px">
            <div class="small note">Last generated key (for currently selected user)</div>
            <div id="lastKey" style="margin-top:8px" class="viewer">(none)</div>
            <div class="small note" style="margin-top:6px">Use "Export messages" to produce a GitHub-friendly keys.json payload</div>
          </div>

          <div style="flex:1">
            <div class="small note">Actions</div>
            <div style="display:flex;gap:8px;margin-top:8px">
              <button id="btnCopyKey" class="btn ghost small">Copy Key</button>
              <button id="btnCopyJSON" class="btn small">Copy keys.json (export)</button>
              <button id="btnDownloadJSON" class="btn ghost small">Download keys.json</button>
              <button id="btnDownloadKeyTxt" class="btn ghost small">Download key.txt (virtual)</button>
            </div>
          </div>
        </div>
      </div>

      <!-- Keys tab / list for current user -->
      <div class="card" style="margin-top:12px">
        <div class="h2">Key (key.txt) — Current User</div>
        <div class="small note">View all active keys for selected user. Newest unexpired is shown as virtual key.txt.</div>

        <div id="currentKeyList" style="margin-top:10px"></div>

        <h4 style="margin-top:10px">Virtual file: <code>key.txt</code></h4>
        <div id="keyTxtViewer" class="viewer">(no active key)</div>
      </div>

      <!-- Key history and logs -->
      <div class="card" style="margin-top:12px">
        <div class="h2">Key History (Selected User)</div>
        <div id="historyList" style="margin-top:10px"></div>
      </div>
    </div>

    <!-- RIGHT: Admin / Export / Users -->
    <div>
      <div class="card">
        <div style="display:flex;align-items:center;gap:8px">
          <div style="flex:1">
            <div class="h2">Admin Dashboard</div>
            <div class="small note">View users, revoke keys, purge expired, export multi-user JSON</div>
          </div>
          <div>
            <button id="btnPurge" class="btn ghost small">Purge expired</button>
          </div>
        </div>

        <hr/>
        <div style="display:flex;gap:8px;align-items:center">
          <input id="searchUser" class="input" placeholder="Search username/email"/>
        </div>

        <div id="usersList" style="margin-top:10px;max-height:380px;overflow:auto"></div>

        <hr/>
        <div>
          <div class="small note">Export formats</div>
          <div style="display:flex;gap:8px;margin-top:8px">
            <button id="btnExportSingle" class="btn small">Export current user's keys.json</button>
            <button id="btnExportMulti" class="btn small">Export multi-user keys.json</button>
          </div>
          <div class="small note" style="margin-top:8px">When exported, copy & paste the JSON into your GitHub <code>keys.json</code>.</div>
        </div>
      </div>

      <div class="card" style="margin-top:12px">
        <div class="h2">Quick Guide</div>
        <ol class="small">
          <li>Create user (signup) or demo user.</li>
          <li>Select user from top right selector or admin list.</li>
          <li>Generate key — copy key or export JSON.</li>
          <li>Paste exported JSON into your repo's <code>keys.json</code> (manual update).</li>
          <li>Use your LuaDroid checker to verify keys against GitHub raw URL.</li>
        </ol>
      </div>
    </div>
  </div>

  <div class="footer">Data stored locally in your browser only. No servers. Manual export required for GitHub updates.</div>
</div>

<script>
/* -----------------------------------------------------------------------------
   Full static Key Generator + Admin (Manual GitHub Export)
   - Data stored in localStorage under KEY_SYSTEM_V1
   - Users: { email, username, password, keys: [{key,time}] }
   - Keys expire after 24 hours (86400 seconds)
   - Exports:
       - single user JSON: { key: "xxxx", expires: 12345 }  (for simple single-key usage)
       - multi-user JSON: { users: { username: { key: "xxxx", expires: 12345 }, ... } }
-----------------------------------------------------------------------------*/

const LS_KEY = 'KEY_SYSTEM_V1';

// --- Utilities ---
const $ = id => document.getElementById(id);
function nowSec(){ return Math.floor(Date.now()/1000); }
function saveState(){ localStorage.setItem(LS_KEY, JSON.stringify(state)); }
function loadState(){ try{ return JSON.parse(localStorage.getItem(LS_KEY) || '[]'); }catch(e){ return [] } }

// --- App State ---
let state = loadState(); // array of users
let currentUser = null;  // username string selected in UI
const TTL = 86400; // 24 hours

// --- DOM refs & init ---
const suEmail = $('suEmail'), suUser = $('suUser'), suPass = $('suPass'), btnSignup = $('btnSignup'),
      btnDemo = $('btnDemo'), liUser = $('liUser'), liPass = $('liPass'), btnLogin = $('btnLogin'),
      liMsg = $('liMsg'), suMsg = $('suMsg'), who = $('who'), btnLogout = $('btnLogout'),
      genUserSelect = $('genUserSelect'), btnGenKey = $('btnGenKey'), lastKey = $('lastKey'),
      currentKeyList = $('currentKeyList'), keyTxtViewer = $('keyTxtViewer'), btnCopyKey = $('btnCopyKey'),
      btnCopyJSON = $('btnCopyJSON'), btnDownloadJSON = $('btnDownloadJSON'), btnDownloadKeyTxt = $('btnDownloadKeyTxt'),
      usersList = $('usersList'), searchUser = $('searchUser'), btnPurge = $('btnPurge'),
      btnExportSingle = $('btnExportSingle'), btnExportMulti = $('btnExportMulti'), btnRevoke = $('btnRevoke'),
      historyList = $('historyList'), btnCopyJSONLocal = $('btnCopyJSON');

// --- Helpers: find user ---
function findUserByUsername(u){ return state.find(x => x.username === u); }
function findIndexByUsername(u){ return state.findIndex(x => x.username === u); }

// --- Init demo user if none exist ---
if(state.length === 0){
  state.push({ email:'demo@local', username:'demo', password:'demo', keys: [] });
  saveState();
}

// --- Render users in admin list & selector ---
function rebuildUserSelector(){
  genUserSelect.innerHTML = '';
  state.forEach(u => {
    const opt = document.createElement('option');
    opt.value = u.username; opt.innerText = u.username + ' • ' + u.email;
    genUserSelect.appendChild(opt);
  });
  // ensure currentUser exists
  if(!currentUser && state.length) currentUser = state[0].username;
  genUserSelect.value = currentUser || (state[0] && state[0].username);
  renderCurrentUser();
}

function renderAdminList(filter=''){
  usersList.innerHTML = '';
  const q = filter.trim().toLowerCase();
  state.forEach(u => {
    if(q && !(u.username.toLowerCase().includes(q) || u.email.toLowerCase().includes(q))) return;
    const div = document.createElement('div');
    div.className = 'user-card';
    const left = document.createElement('div');
    left.innerHTML = `<strong>${u.username}</strong><div class="small">${u.email}</div>`;
    const right = document.createElement('div');
    right.className='actions';
    const btnView = document.createElement('button'); btnView.className='btn small ghost'; btnView.innerText='Select';
    btnView.onclick = ()=>{ currentUser = u.username; genUserSelect.value = currentUser; renderCurrentUser(); showUserSelected(); };
    const btnRevoke = document.createElement('button'); btnRevoke.className='btn ghost small'; btnRevoke.innerText='Revoke All';
    btnRevoke.onclick = ()=>{ if(confirm('Revoke all keys for '+u.username+'?')){ u.keys=[]; saveState(); renderCurrentUser(); renderAdminList(searchUser.value); } };
    const btnDelete = document.createElement('button'); btnDelete.className='btn bad small'; btnDelete.innerText='Delete';
    btnDelete.onclick = ()=>{ if(confirm('DELETE user '+u.username+'? This cannot be undone.')){ state = state.filter(x=>x.username!==u.username); saveState(); rebuildUserSelector(); renderAdminList(); } };
    right.appendChild(btnView); right.appendChild(btnRevoke); right.appendChild(btnDelete);
    div.appendChild(left); div.appendChild(right);
    usersList.appendChild(div);
  });
}

// --- Key utilities ---
function genSegment(){ let s=''; for(let i=0;i<4;i++) s+=Math.floor(Math.random()*10); return s; }
function genKey(){ return `${genSegment()}-${genSegment()}-${genSegment()}-${genSegment()}`; }

// remove expired keys globally or per user
function cleanupExpiredForUser(user){
  const now = nowSec();
  user.keys = user.keys.filter(k => now < (k.time + TTL));
}
function cleanupAll(){ state.forEach(u=>cleanupExpiredForUser(u)); saveState(); }

// --- Render current user area ---
function renderCurrentUser(){
  // ensure selection
  const user = findUserByUsername(currentUser);
  $('who').innerText = user ? `${user.username} (logged)` : 'Not logged in';
  // update selector options
  rebuildSelectorVisual();
  if(!user){
    currentKeyList.innerHTML = '<div class="small note">Select a user</div>';
    keyTxtViewer.innerText = '(no active key)';
    historyList.innerHTML = '(no user selected)';
    lastKey.innerText = '(none)';
    return;
  }
  cleanupExpiredForUser(user);
  saveState();

  // list active keys (unexpired)
  const now = nowSec();
  currentKeyList.innerHTML = '';
  const active = user.keys.filter(k => now < (k.time + TTL));
  if(active.length === 0) currentKeyList.innerHTML = '<div class="small note">No active keys</div>';
  active.slice().reverse().forEach(kobj=>{
    const rem = (kobj.time + TTL) - now;
    const h = Math.floor(rem/3600), m = Math.floor((rem%3600)/60), s = rem%60;
    const div = document.createElement('div'); div.className='key-item';
    div.innerHTML = `<div>KEY=${kobj.key}</div><div class="small timer">${h}h ${m}m ${s}s</div>`;
    currentKeyList.appendChild(div);
  });

  // newest unexpired -> key.txt viewer
  const newest = active.length ? active[active.length-1] : null;
  if(newest){
    keyTxtViewer.innerText = `KEY=${newest.key}\nTIME=${newest.time}`;
    lastKey.innerText = newest.key;
  } else {
    keyTxtViewer.innerText = '(no active key)';
    lastKey.innerText = '(none)';
  }

  // history (all keys including expired)
  historyList.innerHTML = '';
  if(!user.keys || user.keys.length===0){ historyList.innerHTML = '<div class="small note">(no history)</div>'; return; }
  const table = document.createElement('table'); table.className='table';
  const thead = document.createElement('thead'); thead.innerHTML = '<tr><th>Key</th><th>Created</th><th>Expires In</th><th>Actions</th></tr>';
  table.appendChild(thead);
  const tbody = document.createElement('tbody');
  user.keys.slice().reverse().forEach(kobj=>{
    const tr = document.createElement('tr');
    const created = new Date(kobj.time*1000).toLocaleString();
    const rem = (kobj.time + TTL) - now;
    let expiresText = rem > 0 ? `${Math.floor(rem/3600)}h ${Math.floor((rem%3600)/60)}m` : 'expired';
    tr.innerHTML = `<td class="mono">${kobj.key}</td><td>${created}</td><td>${expiresText}</td><td></td>`;
    const td = tr.querySelector('td:last-child');
    const btnCopy = document.createElement('button'); btnCopy.className='btn ghost small'; btnCopy.innerText='Copy';
    btnCopy.onclick = ()=>{ navigator.clipboard.writeText(kobj.key); alert('Key copied'); };
    const btnDel = document.createElement('button'); btnDel.className='btn bad small'; btnDel.innerText='Delete';
    btnDel.onclick = ()=>{ if(confirm('Delete key?')){ user.keys = user.keys.filter(x=>x.key!==kobj.key); saveState(); renderCurrentUser(); renderAdminList(searchUser.value); } };
    td.appendChild(btnCopy); td.appendChild(btnDel);
    tbody.appendChild(tr);
  });
  table.appendChild(tbody);
  historyList.appendChild(table);
}

// helper to rebuild genUserSelect
function rebuildSelectorVisual(){
  genUserSelect.innerHTML = '';
  state.forEach(u=>{
    const opt = document.createElement('option'); opt.value=u.username; opt.innerText=u.username;
    genUserSelect.appendChild(opt);
  });
  if(currentUser) genUserSelect.value = currentUser;
}

// --- Event handlers ---
// Signup
btnSignup.addEventListener('click', ()=>{
  const email = suEmail.value.trim(), username = suUser.value.trim(), pass = suPass.value;
  if(!email || !username || !pass){ suMsg.innerText='Fill all fields'; return; }
  if(findUserByUsername(username)){ suMsg.innerText='Username exists'; return; }
  state.push({ email, username, password: pass, keys: [] });
  saveState();
  suMsg.innerText='Account created';
  suEmail.value=''; suUser.value=''; suPass.value='';
  rebuildUserSelector(); renderAdminList();
});

// Demo user
btnDemo.addEventListener('click', ()=>{
  const username = 'demo' + Math.floor(Math.random()*9000+1000);
  state.push({ email:username+'@local', username, password:'demo', keys: [] });
  saveState(); rebuildUserSelector(); renderAdminList();
  alert('Demo user created: ' + username);
});

// Login
btnLogin.addEventListener('click', ()=>{
  const username = liUser.value.trim(), pass = liPass.value;
  const u = findUserByUsername(username);
  if(!u){ liMsg.innerText='User not found'; return; }
  if(u.password !== pass){ liMsg.innerText='Wrong password'; return; }
  currentUser = username; liMsg.innerText=''; updateAuthUI();
});

// Logout
$('btnLogout').addEventListener('click', ()=>{
  currentUser = null; updateAuthUI();
});

// select user for generation
genUserSelect.addEventListener('change', (e)=>{ currentUser = e.target.value; renderCurrentUser(); });

// Generate key for selected user
btnGenKey.addEventListener('click', ()=>{
  const username = genUserSelect.value;
  const u = findUserByUsername(username);
  if(!u){ alert('Select a valid user'); return; }
  cleanupExpiredForUser(u);
  const k = genKey();
  const t = nowSec();
  u.keys.push({ key:k, time:t });
  saveState();
  renderCurrentUser();
  renderAdminList(searchUser.value);
  alert('Generated key for ' + username + ': ' + k);
});

// Revoke latest key for selected user
btnRevoke.addEventListener('click', ()=>{
  const username = genUserSelect.value;
  const u = findUserByUsername(username);
  if(!u || !u.keys || u.keys.length===0){ alert('No keys to revoke'); return; }
  if(!confirm('Revoke newest key for '+username+'?')) return;
  u.keys.pop();
  saveState(); renderCurrentUser(); renderAdminList(searchUser.value);
});

// copy key
btnCopyKey.addEventListener('click', ()=>{
  const u = findUserByUsername(genUserSelect.value);
  
