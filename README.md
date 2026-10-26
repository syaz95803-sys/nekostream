<!DOCTYPE html>
<html lang="ms">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<meta name="theme-color" content="#111318">
<title>NekoStream</title>
<style>
:root{--bg:#111318;--panel:#1b1e25;--panel2:#252a34;--text:#f7f7fb;--muted:#a7adbb;--accent:#8b5cf6;--pink:#ec4899;--line:#303541}
body.light{--bg:#f4f5f9;--panel:#fff;--panel2:#eceef5;--text:#171923;--muted:#656b7a;--line:#d9dce6}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--text);font:14px Arial,sans-serif}
button,input,select,textarea{font:inherit}
button{cursor:pointer;border:0;border-radius:10px;padding:10px;background:var(--panel2);color:var(--text)}
input,select,textarea{width:100%;padding:11px;background:var(--panel2);color:var(--text);border:1px solid var(--line);border-radius:10px}
.top{position:sticky;top:0;z-index:10;background:var(--bg);padding:12px;border-bottom:1px solid var(--line)}
.brand{font-size:22px;font-weight:900;margin-bottom:10px}
.brand span{color:var(--pink)}
.search{display:flex;gap:8px}
.search input{flex:1}.search button{width:80px}
.primary{background:linear-gradient(110deg,var(--accent),var(--pink));color:#fff;font-weight:bold}
.genres{display:flex;gap:7px;overflow-x:auto;padding:10px 12px}
.genres button{white-space:nowrap;border-radius:20px;font-size:12px}
.genres button.active{background:var(--accent);color:#fff}
.page{display:none;padding:10px 12px 90px}
.page.active{display:block}
.heading{display:flex;justify-content:space-between;align-items:center;margin:10px 0}
.heading h2{margin:0;font-size:19px}
.muted{font-size:12px;color:var(--muted)}
.grid{display:grid;grid-template-columns:repeat(5,minmax(0,1fr));gap:12px 7px}
.card{min-width:0;cursor:pointer}
.poster{position:relative;width:100%;aspect-ratio:2/3;border-radius:9px;background:var(--panel2);overflow:hidden;display:grid;place-items:center;border:1px solid var(--line)}
.poster img{position:absolute;width:100%;height:100%;object-fit:cover}
.initial{width:100%;height:100%;display:grid;place-items:center;background:linear-gradient(140deg,var(--accent),var(--pink));font-size:24px;font-weight:900;color:#fff}
.badge{position:absolute;top:4px;left:4px;background:var(--pink);color:#fff;border-radius:5px;padding:3px 5px;font-size:9px;z-index:1}
.title{font-size:11px;font-weight:bold;line-height:1.3;margin-top:6px;display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;overflow:hidden}
.meta{font-size:10px;color:var(--muted);margin-top:3px}
.empty{text-align:center;color:var(--muted);padding:30px 8px;border:1px dashed var(--line);border-radius:12px;margin-top:12px}
.nav{position:fixed;bottom:0;left:0;right:0;z-index:20;display:flex;justify-content:space-around;background:var(--panel);border-top:1px solid var(--line);padding:7px 2px calc(7px + env(safe-area-inset-bottom))}
.nav button{background:transparent;color:var(--muted);font-size:11px;flex:1}
.nav button.active{color:var(--accent)}
.nav b{display:block;font-size:18px;margin-bottom:2px}
.panel{background:var(--panel);border:1px solid var(--line);border-radius:13px;padding:13px;margin:12px 0}
.field{margin:12px 0}
.field label{display:block;font-size:12px;color:var(--muted);margin-bottom:6px}
.row{display:flex;gap:8px;align-items:center}
.row>*{flex:1;min-width:0}
.profiletop{display:flex;gap:12px;align-items:center}
.avatar{width:58px;height:58px;border-radius:50%;flex-shrink:0;background:linear-gradient(135deg,var(--accent),var(--pink));display:grid;place-items:center;color:#fff;font-size:22px;font-weight:bold;overflow:hidden}
.avatar img{width:100%;height:100%;object-fit:cover}
.ownerbox{border:1px solid var(--accent);border-radius:12px;padding:12px;margin-top:12px}
.hide{display:none!important}
.modal{position:fixed;inset:0;z-index:50;background:var(--bg);display:none;overflow-y:auto;padding:12px}
.modal.open{display:block}
.modalhead{display:flex;align-items:center;gap:8px;position:sticky;top:-12px;background:var(--bg);padding:10px 0;z-index:2}
.modalhead h2{flex:1;font-size:17px;margin:0}
.video{width:100%;aspect-ratio:16/9;background:#000;border-radius:12px;overflow:hidden;margin:10px 0}
.video video{width:100%;height:100%;object-fit:contain}
.episodes{display:flex;flex-direction:row;direction:rtl;gap:8px;overflow-x:auto;padding:10px 0}
.episode{direction:ltr;flex:0 0 75px;text-align:center;font-size:12px}
.episode.active{background:var(--accent);color:#fff}
.danger{background:#991b1b;color:#fff}
.toast{position:fixed;z-index:100;bottom:80px;left:50%;transform:translateX(-50%);background:var(--panel2);padding:12px;border-radius:10px;display:none;max-width:90%;text-align:center}
.toast.show{display:block}
@media(max-width:380px){.grid{gap:9px 5px}.title{font-size:10px}.meta{font-size:9px}}
</style>
</head>
<body>
<header class="top">
 <div class="brand">Neko<span>Stream</span> <span id="ownerBadge" class="hide" style="font-size:10px">OWNER</span></div>
 <div class="search">
  <input id="search" placeholder="Cari anime...">
  <button id="searchBtn" class="primary">Cari</button>
 </div>
</header>

<div class="genres" id="genres"></div>

<section class="page active" id="homePage">
 <div class="heading"><h2>Popular</h2><span id="count" class="muted">0 anime</span></div>
 <div class="row" style="margin-bottom:8px"><button id="pickVideos" class="primary">📁 Pilih video</button><button id="pickFolder">📂 Pilih folder</button></div>
 <div id="linkedInfo" class="muted" style="margin-bottom:8px"></div>
 <div class="grid" id="animeGrid"></div>
 <div class="empty" id="empty">Belum ada anime.<br>Owner boleh upload anime melalui Profile.</div>
</section>

<section class="page" id="chatPage">
 <div class="heading"><h2>Chat</h2></div>
 <div class="panel">
  <div class="field"><label>Nama</label><input id="chatName" maxlength="30" placeholder="Nama anda"></div>
  <div class="field"><label>Mesej</label><textarea id="chatText" maxlength="500"></textarea></div>
  <button id="sendChat" class="primary" style="width:100%">Hantar</button>
  <p class="muted">Mesej disimpan pada peranti ini. Chat online memerlukan server.</p>
 </div>
 <div id="chatMessages"></div>
</section>

<section class="page" id="profilePage">
 <div class="heading"><h2>Profile</h2></div>
 <div class="panel">
  <div class="profiletop">
   <div class="avatar" id="avatar">N</div>
   <div><strong id="profileDisplay">Pengguna</strong><div id="bioDisplay" class="muted">Peminat anime</div></div>
  </div>
  <div class="field"><label>Nama profile</label><input id="profileName" maxlength="30"></div>
  <div class="field"><label>Bio</label><textarea id="profileBio" maxlength="160"></textarea></div>
  <div class="field"><label>Gambar profile</label><input id="profileFile" type="file" accept="image/*"></div>
  <div class="row"><button id="saveProfile" class="primary">Simpan</button><button id="themeBtn">Putih / Hitam</button></div>
 </div>

 <div class="panel">
  <h3>Owner Panel</h3>
  <p class="muted">Masukkan PIN owner untuk mengurus kandungan.</p>
  <div class="field"><label>PIN Owner</label><input id="ownerPin" type="password" inputmode="numeric"></div>
  <button id="unlock" class="primary" style="width:100%">Buka Owner Panel</button>

  <div id="ownerPanel" class="ownerbox hide">
   <label class="muted" style="display:flex;gap:8px;align-items:flex-start;margin-bottom:10px"><input type="checkbox" id="linkMode" style="width:auto;margin-top:2px" checked><span>Jangan salin video ke dalam app (tiada had storan). Anda perlu tekan <b>Pilih video</b> di Home setiap kali buka app. Jangan tukar nama fail video.</span></label>
   <h3 id="formTitle">Upload anime</h3>
   <div class="field"><label>Nama anime</label><input id="upTitle" maxlength="90"></div>
   <div class="row">
    <div class="field"><label>Genre</label><select id="upGenre">
     <option>Action</option><option>Adventure</option><option>Comedy</option><option>Drama</option><option>Fantasy</option><option>Harem</option><option>Isekai</option><option>Mystery</option><option>Romance</option><option>Sci-Fi</option><option>Slice of Life</option><option>Sports</option><option>Supernatural</option>
    </select></div>
    <div class="field"><label>Status</label><select id="upStatus"><option>Ongoing</option><option>Completed</option></select></div>
   </div>
   <div class="field"><label>Label anime</label><input id="upLabel" placeholder="Contoh: NEW / TAMAT"></div>
   <div class="row">
    <div class="field"><label>Jumlah episod</label><input id="upCount" type="number" min="1" max="300" value="1"></div>
    <div class="field"><label>Video bermula dari episod</label><input id="upStart" type="number" min="1" max="300" value="1"></div>
   </div>
   <div class="field"><label>Poster anime</label><input id="upPoster" type="file" accept="image/*"></div>
   <div class="field"><label>Video episod — pilih beberapa video mengikut turutan</label><input id="upVideos" type="file" accept="video/*" multiple><p class="muted">Video pertama akan menjadi episod yang dipilih di atas, kemudian seterusnya.</p></div>
   <div class="row"><button id="publish" class="primary">Simpan / Terbitkan</button><button id="cancelEdit" class="hide">Batal edit</button></div>
   <h3>Senarai anime</h3><div id="ownerList"></div>
   <div id="manager" class="ownerbox hide">
    <h3 id="mHead">Urus anime</h3>
    <div class="field"><label>Nama anime</label><input id="mTitle" maxlength="90"></div>
    <div class="row">
     <div class="field"><label>Genre</label><select id="mGenre"></select></div>
     <div class="field"><label>Status</label><select id="mStatus"><option>Ongoing</option><option>Completed</option></select></div>
    </div>
    <div class="field"><label>Label</label><input id="mLabel" placeholder="Contoh: NEW / TAMAT"></div>
    <div class="field"><label>Tukar poster (pilihan)</label><input id="mPoster" type="file" accept="image/*"></div>
    <button id="mSave" class="primary" style="width:100%">Simpan butiran</button>
    <h3>Episod</h3>
    <div id="mEpisodes"></div>
    <div class="field"><label>Tambah episod baharu (pilih video, boleh banyak; kosongkan untuk tambah 1 episod kosong)</label><input id="mAddFiles" type="file" accept="video/*" multiple></div>
    <button id="mAdd" class="primary" style="width:100%">Tambah episod</button>
    <button id="mClose" style="width:100%;margin-top:8px">Tutup</button>
   </div>
   <button id="lockOwner" style="width:100%;margin-top:10px">Kunci Owner Panel</button>
  </div>
 </div>
</section>

<nav class="nav">
 <button class="active" data-page="homePage"><b>⌂</b>Home</button>
 <button data-page="chatPage"><b>▤</b>Chat</button>
 <button data-page="profilePage"><b>◉</b>Profile</button>
</nav>

<div class="modal" id="watchModal">
 <div class="modalhead">
  <button id="closeWatch">←</button><h2 id="watchTitle">Anime</h2>
  <button id="deleteWatching" class="danger hide">Padam</button>
 </div>
 <div class="video"><video id="player" controls playsinline></video></div>
 <div id="watchMeta" class="muted"></div>
 <h3>Episode</h3><div class="episodes" id="episodeList"></div>
 <p class="muted">Geser senarai dari kanan ke kiri.</p>
 <div class="panel"><strong id="episodeTitle">Pilih episode</strong><p id="episodeDescription" class="muted"></p></div>
</div>
<input id="pickInput" type="file" accept="video/*" multiple class="hide">
<input id="pickDir" type="file" webkitdirectory multiple class="hide">
<div id="toast" class="toast"></div>

<script>
'use strict';
const $=id=>document.getElementById(id);
const memLS={};
const LS={
 get(k){try{return localStorage.getItem(k)}catch(e){return Object.prototype.hasOwnProperty.call(memLS,k)?memLS[k]:null}},
 set(k,v){try{localStorage.setItem(k,v)}catch(e){if(e&&e.name==='QuotaExceededError')throw e;memLS[k]=v}}
};
window.addEventListener('error',e=>{try{notify('Ralat: '+(e.message||'tidak diketahui'))}catch(_){}});
window.addEventListener('unhandledrejection',e=>{try{notify('Ralat: '+((e.reason&&e.reason.message)||e.reason))}catch(_){}});
const DB_NAME='NekoStreamDatabase',STORE='anime';
const OWNER_PIN='1308'; // Tukar PIN ini. PIN HTML bukan keselamatan server.
let db,anime=[],selectedGenre='Semua',ownerOpen=false;
let watching=null,playingUrl=null,editingId=null,profileImage=null;
const genreNames=['Semua','Action','Adventure','Comedy','Drama','Fantasy','Harem','Isekai','Mystery','Romance','Sci-Fi','Slice of Life','Sports','Supernatural'];

const liveFiles={};
const fkey=f=>f.name+'|'+f.size;
function addLive(files){
 let n=0;
 for(const f of files){
  if(/^video\//.test(f.type)||/\.(mp4|mkv|webm|mov|m4v|avi|3gp)$/i.test(f.name)){liveFiles[fkey(f)]=f;n++}
 }
 return n;
}
function epFile(ep){return ep?(ep.data||(ep.key&&liveFiles[ep.key])||null):null}
function mkEp(f){
 addLive([f]);
 return $('linkMode').checked?{name:f.name,key:fkey(f),linked:true}:{name:f.name,data:f};
}
function updateLinkedInfo(){
 const n=Object.keys(liveFiles).length;
 $('linkedInfo').textContent=n?n+' video sedia untuk ditonton sesi ini':'Tekan Pilih video untuk memuatkan video anda (tidak disalin).';
}
function afterPick(files){
 const n=addLive(files);
 notify(n?n+' video dipilih':'Tiada fail video dipilih');
 updateLinkedInfo();
 if(watching){
  renderEpisodes();
  if(!playingUrl){const f=watching.episodes.findIndex(e=>epFile(e));if(f>=0)playEpisode(f)}
 }
}
function notify(text){
 $('toast').textContent=text;$('toast').classList.add('show');
 clearTimeout(notify.timer);
 notify.timer=setTimeout(()=>$('toast').classList.remove('show'),2600);
}
function openDatabase(){
 return new Promise((resolve,reject)=>{
  const r=indexedDB.open(DB_NAME,1);
  r.onupgradeneeded=()=>{if(!r.result.objectStoreNames.contains(STORE))r.result.createObjectStore(STORE,{keyPath:'id'})};
  r.onsuccess=()=>resolve(r.result);r.onerror=()=>reject(r.error);
 });
}
let useMem=false;const memStore=new Map();
function store(mode='readonly'){return db.transaction(STORE,mode).objectStore(STORE)}
function getAll(){if(useMem)return Promise.resolve([...memStore.values()]);return new Promise((res,rej)=>{const r=store().getAll();r.onsuccess=()=>res(r.result||[]);r.onerror=()=>rej(r.error)})}
function putAnime(a){
 if(useMem){memStore.set(a.id,a);return Promise.resolve()}
 return new Promise((res,rej)=>{
  const tx=db.transaction(STORE,'readwrite');tx.objectStore(STORE).put(a);
  tx.oncomplete=()=>res();tx.onerror=()=>rej(tx.error);tx.onabort=()=>rej(tx.error);
 });
}
function removeAnime(id){
 if(useMem){memStore.delete(id);return Promise.resolve()}
 return new Promise((res,rej)=>{
  const tx=db.transaction(STORE,'readwrite');tx.objectStore(STORE).delete(id);
  tx.oncomplete=()=>res();tx.onerror=()=>rej(tx.error);
 });
}
function readFile(file){
 return new Promise((resolve,reject)=>{
  if(!file)return resolve(null);
  const r=new FileReader();r.onload=()=>resolve(r.result);r.onerror=()=>reject(r.error);r.readAsDataURL(file);
 });
}

function drawGenres(){
 $('genres').innerHTML='';
 genreNames.forEach(g=>{
  const b=document.createElement('button');b.textContent=g;
  b.className=g===selectedGenre?'active':'';
  b.onclick=()=>{selectedGenre=g;drawGenres();renderAnime()};
  $('genres').appendChild(b);
 });
}
function createCard(a){
 const card=document.createElement('div');card.className='card';
 const poster=document.createElement('div');poster.className='poster';
 if(a.poster){const img=document.createElement('img');img.src=a.poster;img.alt=a.title;poster.appendChild(img)}
 else{const i=document.createElement('div');i.className='initial';i.textContent=(a.title||'A').slice(0,1).toUpperCase();poster.appendChild(i)}
 if(a.label){const b=document.createElement('span');b.className='badge';b.textContent=a.label;poster.appendChild(b)}
 const title=document.createElement('div');title.className='title';title.textContent=a.title;
 const meta=document.createElement('div');meta.className='meta';meta.textContent=a.genre+' · '+a.status;
 card.append(poster,title,meta);card.onclick=()=>openAnime(a.id);return card;
}
function renderAnime(){
 const q=$('search').value.trim().toLowerCase();
 const list=anime.filter(a=>(selectedGenre==='Semua'||a.genre===selectedGenre)&&(!q||(a.title+' '+a.genre+' '+a.status).toLowerCase().includes(q)));
 $('animeGrid').innerHTML='';
 list.forEach(a=>$('animeGrid').appendChild(createCard(a)));
 $('count').textContent=list.length+' anime';
 $('empty').classList.toggle('hide',list.length>0);
}
async function refresh(){
 anime=await getAll();
 anime.sort((a,b)=>(b.updated||0)-(a.updated||0));
 renderAnime();
 if(ownerOpen)renderOwnerList();
 if(managingId)renderManager();
}

function openAnime(id){
 watching=anime.find(a=>a.id===id);
 if(!watching)return;
 $('watchTitle').textContent=watching.title;
 $('watchMeta').textContent=watching.genre+' · '+watching.status+' · '+watching.episodeCount+' episod';
 $('watchModal').classList.add('open');
 $('deleteWatching').classList.toggle('hide',!ownerOpen);
 renderEpisodes();
 const first=watching.episodes.findIndex(e=>epFile(e));
 if(first>=0)playEpisode(first);
 else{
  $('player').pause();$('player').removeAttribute('src');$('player').load();
  $('episodeTitle').textContent=watching.episodes.some(e=>e&&e.linked)?'Pilih video dahulu':'Video belum dimuat naik';
  $('episodeDescription').textContent=watching.episodes.some(e=>e&&e.linked)?'Tekan nombor episod, kemudian pilih fail video dari gallery atau folder.':'Belum ada video untuk episod ini.';
 }
}
function renderEpisodes(){
 $('episodeList').innerHTML='';
 for(let i=0;i<watching.episodeCount;i++){
  const ep=watching.episodes[i],b=document.createElement('button');
  b.className='episode';b.textContent='Ep '+(i+1);
  b.onclick=()=>epFile(ep)?playEpisode(i):(ep&&ep.linked?(notify('Pilih fail video episod ini'),$('pickInput').click()):notify('Episod ini belum ada video'));
  $('episodeList').appendChild(b);
 }
}
function playEpisode(i){
 const ep=watching.episodes[i];
 const src=epFile(ep);if(!src){notify('Video episod tiada');return}
 if(playingUrl)URL.revokeObjectURL(playingUrl);
 playingUrl=URL.createObjectURL(src);
 $('player').src=playingUrl;$('player').load();
 $('episodeTitle').textContent='Episode '+(i+1);
 $('episodeDescription').textContent=ep.name||watching.title;
 [...$('episodeList').children].forEach((b,n)=>b.classList.toggle('active',n===i));
 $('player').play().catch(()=>{});
}
$('player').addEventListener('ended',()=>{
 if(!watching)return;
 const i=[...$('episodeList').children].findIndex(b=>b.classList.contains('active'));
 if(i>=0&&i+1<watching.episodeCount&&epFile(watching.episodes[i+1]))playEpisode(i+1);
});
$('closeWatch').onclick=()=>{
 $('watchModal').classList.remove('open');$('player').pause();
 $('player').removeAttribute('src');$('player').load();
 if(playingUrl)URL.revokeObjectURL(playingUrl);
 playingUrl=null;watching=null;
};
$('deleteWatching').onclick=async()=>{
 if(watching&&ownerOpen&&confirm('Padam anime ini?')){
  const id=watching.id;
  $('closeWatch').click();
  await removeAnime(id);await refresh();notify('Anime dipadam');
 }
};

$('searchBtn').onclick=renderAnime;
$('search').addEventListener('input',renderAnime);
$('search').addEventListener('keydown',e=>{if(e.key==='Enter')renderAnime()});

document.querySelectorAll('.nav button').forEach(b=>b.onclick=()=>{
 document.querySelectorAll('.nav button').forEach(x=>x.classList.toggle('active',x===b));
 document.querySelectorAll('.page').forEach(p=>p.classList.toggle('active',p.id===b.dataset.page));
 if(b.dataset.page==='chatPage')renderChat();
 window.scrollTo(0,0);
});

function getProfile(){try{return JSON.parse(LS.get('ns_profile')||'{}')}catch(e){return{}}}
function loadProfile(){
 const p=getProfile();
 $('profileName').value=p.name||'';$('profileBio').value=p.bio||'';
 $('profileDisplay').textContent=p.name||'Pengguna';
 $('bioDisplay').textContent=p.bio||'Peminat anime';
 if(p.pic){$('avatar').innerHTML='';const im=document.createElement('img');im.alt='Profile';im.src=p.pic;$('avatar').appendChild(im)}
 else $('avatar').textContent=(p.nam
