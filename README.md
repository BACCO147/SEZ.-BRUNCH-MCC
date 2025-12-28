# SEZ.-BRUNCH-MCC
sez brunch mcc per iphone

<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<title>MCC BRUNCH</title>

<!-- Icona app -->
<link rel="icon" type="image/png" href="icon.png">
<link rel="apple-touch-icon" href="icon.png">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="mobile-web-app-capable" content="yes">

<style>
body { margin:0; font-family: Arial, sans-serif; transition: all 0.3s; }
.container { max-width:480px; margin:auto; padding:25px; border-radius:20px; text-align:center; box-shadow:0 0 40px rgba(0,0,0,0.5); transition: all 0.3s;}
.title { font-size:26px; font-weight:900; margin-bottom:20px; transition: all 0.5s;}
.timeDisplay { font-size:50px; font-weight:bold; margin:15px 0; transition: all 0.3s;}
.button { width:100%; padding:12px; margin-top:8px; border:none; border-radius:12px; font-weight:600; cursor:pointer; transition:all 0.3s;}
.sleepBtn { background:#2563eb; color:white;}
.wakeBtn { background:#16a34a; color:white;}
.bananaBtn { background:#f59e0b; color:white;}
.resetBtn { background:#dc2626; color:white;}
.ceremonyBtn { background:#a855f7; color:white;}
.section { margin-top:25px; text-align:left;}
.section h3 { margin-bottom:8px; border-bottom:1px solid #ccc; padding-bottom:5px;}
.item { padding:4px 0; border-bottom:1px solid #ccc; display:flex; justify-content:space-between; align-items:center;}
.folder { cursor:pointer; padding:8px; border-radius:10px; margin-top:5px; background:#1e293b; color:white;}
#counterBananaDisplay { margin-top:10px; font-weight:bold;}
#recapBox { margin-top:15px; padding:12px; background:#f3f4f6; border-radius:10px; font-size:14px; white-space:pre-line;}
#cinemaScreen { position:fixed; inset:0; background:black; color:#fff; display:none; padding:40px; font-size:20px; line-height:1.6; white-space:pre-line; overflow:auto;}
</style>
</head>

<body>
<div class="container" id="mainContainer">
  <div class="title" id="titleText">MCC BRUNCH</div>

  <div>
    <div id="counterLabel">🛌 Ore dormite / ☀️ Ore sveglio</div>
    <div class="timeDisplay" id="mainCounter">0.00</div>
  </div>

  <button class="button sleepBtn" onclick="startSleep()">Inizia a dormire 🛌</button>
  <button class="button wakeBtn" onclick="stopSleep()">Sveglio ☀️</button>
  <button class="button bananaBtn" onclick="toggleBanana()">Counter Banana 🍌</button>
  <div id="counterBananaDisplay">Ore in banana: 0.00</div>

  <div class="section">
    <h3>Compartimenti</h3>
    <div class="folder" onclick="openFolder()">📁 Archivio riservato</div>
    <div id="folderStatus"></div>
    <div class="ceremony" onclick="finalCeremony()">CERIMONIA FINALE</div>
    <div id="branchList"></div>
  </div>

  <div class="section">
    <h3>Recap giornaliero (14:00)</h3>
    <div id="recapBox">In attesa del recap giornaliero…</div>
  </div>
</div>

<div id="cinemaScreen"></div>

<script>
let sleepStart=null, awakeStart=null, bananaStart=null;
let sleeping=false, awake=false, inBanana=false;
const GRANMAESTRO="bomber", FOUNDERS=["finella","corra"];
let mainCounter=parseFloat(localStorage.getItem("mainCounter"))||0;
let bananaCounter=parseFloat(localStorage.getItem("bananaCounter"))||0;
let lastRecapDay=localStorage.getItem("lastRecapDay");

function updateDisplay(){
  const mainDisplay=document.getElementById("mainCounter");
  const bananaDisplay=document.getElementById("counterBananaDisplay");
  mainDisplay.innerText=mainCounter.toFixed(2);
  bananaDisplay.innerText=`Ore in banana: ${bananaCounter.toFixed(2)}`;
  const container=document.getElementById("mainContainer");
  const title=document.getElementById("titleText");
  if(sleeping){
    container.style.background="#000"; container.style.color="#fff"; title.style.color="#fff";
    document.getElementById("counterLabel").style.color="#fff";
  } else {
    container.style.background="#fff"; container.style.color="#000"; title.style.color="#000";
    document.getElementById("counterLabel").style.color="#000";
  }
}

// --- CONTATORI ---
function startSleep(){ if(!sleeping){ sleeping=true; awake=false; sleepStart=Date.now(); updateDisplay();}}
function stopSleep(){ 
  if(sleeping){ mainCounter+=(Date.now()-sleepStart)/3600000; sleeping=false; awake=true; awakeStart=Date.now(); sleepStart=null; localStorage.setItem("mainCounter",mainCounter); updateDisplay(); } 
}
function toggleBanana(){
  if(!inBanana){ bananaStart=Date.now(); inBanana=true; } 
  else{ bananaCounter+=(Date.now()-bananaStart)/3600000; inBanana=false; bananaStart=null; localStorage.setItem("bananaCounter",bananaCounter);} 
  updateDisplay();
}

// --- INTERVALLO ---
setInterval(()=>{
  if(sleeping) { mainCounter += 0.01/60; localStorage.setItem("mainCounter",mainCounter);}
  if(inBanana) { bananaCounter += 0.01/60; localStorage.setItem("bananaCounter",bananaCounter);}
  updateDisplay();
  checkRecap();
},1000);

// --- RECAPP GIORNALIERO 14:00 ---
function checkRecap(){
  const now=new Date(); const today=now.toLocaleDateString("it-IT");
  if(now.getHours()===14 && lastRecapDay!==today){
    const recapBox=document.getElementById("recapBox");
    recapBox.innerText=`🛌 Ore dormite totali: ${mainCounter.toFixed(2)}\n🍌 Ore in Banana: ${bananaCounter.toFixed(2)}\n☀️ Ore sveglio: ${awake?((Date.now()-awakeStart)/3600000).toFixed(2):0}`;
    lastRecapDay=today; localStorage.setItem("lastRecapDay",today);
  }
}

// --- SEZIONE RISERVATA ---
function openFolder(){
  const name=prompt("Inserisci il tuo nome per accedere all'archivio").toLowerCase();
  const status=document.getElementById("folderStatus");
  if(name===GRANMAESTRO){ status.innerText="ACCESSO CONSENTITO – Archivio vuoto."; status.style.color="#22c55e";}
  else{ status.innerText="ACCESSO NEGATO – Solo Gran Maestro può entrare."; status.style.color="#ef4444";}
}

// --- CERIMONIA FINALE CINEMATICA ---
function finalCeremony(){
  const cinema=document.getElementById("cinemaScreen");
  cinema.style.display="block"; cinema.innerText="";
  const lines=[
    "🎬 CERIMONIA FINALE MCC BRUNCH 🎬\n",
    `🛌 Ore dormite totali: ${mainCounter.toFixed(2)}`,
    `🍌 Ore in Banana: ${bananaCounter.toFixed(2)}`,
    `☀️ Ore sveglio: ${awake?((Date.now()-awakeStart)/3600000).toFixed(2):0}`,
    "",
    "⭐ Bomber - Gran Maestro del Branch",
    "Co-Fondatori: Finella, Corra"
  ];
  let idx=0;
  function showNext(){ 
    if(idx<lines.length){ cinema.innerText+=lines[idx]+"\n"; idx++; setTimeout(showNext,600);} 
  }
  showNext();
}

updateDisplay();
</script>
</body>
</html>
