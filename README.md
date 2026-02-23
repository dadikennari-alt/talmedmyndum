<!DOCTYPE html>
<html lang="is">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Talæfing FB - Autoplay Myndbönd</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<style>
  body { font-family: 'Segoe UI', sans-serif; background-color: #f0f4f8; text-align: center; padding: 10px; margin: 0; }
  .header-img { width: 100%; max-width: 600px; border-radius: 10px; margin-bottom: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
  .card { background: white; max-width: 500px; margin: 10px auto; padding: 25px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); border-top: 8px solid #004a99; }
  .overall-progress { margin-bottom: 20px; font-size: 0.9em; color: #555; }
  .progress-container { width: 100%; background: #e0e0e0; height: 10px; border-radius: 5px; margin-top: 5px; }
  .progress-fill { height: 100%; width: 0%; background: #004a99; border-radius: 5px; transition: width 0.5s ease; }
  h2 { color: #2c3e50; margin-top: 0; }
  .counter { color: #888; font-size: 0.9em; margin-bottom: 10px; display: block; font-weight: bold;}
  
  .media-container { width: 100%; height: 280px; background-color: #000; border-radius: 10px; margin-bottom: 20px; overflow: hidden; display: flex; align-items: center; justify-content: center; position: relative; }
  .exercise-img { width: 100%; height: 100%; object-fit: cover; }
  .video-frame { width: 100%; height: 100%; border: none; }

  .phrase-box { background-color: #fdfdfe; border: 2px solid #e1e4e8; border-radius: 12px; padding: 15px; margin-bottom: 15px; }
  .target-text { font-size: 1.2em; color: #2c3e50; font-weight: bold; }
  .record-btn { background-color: #27ae60; color: white; border: none; padding: 15px 30px; font-size: 1.1em; border-radius: 50px; cursor: pointer; transition: 0.2s; width: 100%; margin-bottom: 15px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
  .record-btn.recording { background-color: #ff5252; animation: pulse 1.5s infinite; }
  @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(255, 82, 82, 0.7); } 70% { box-shadow: 0 0 0 10px rgba(255, 82, 82, 0); } 100% { box-shadow: 0 0 0 0 rgba(255, 82, 82, 0); } }
  .nav-row { display: flex; justify-content: space-between; margin-top: 10px; }
  .nav-btn { background-color: #eceff1; border: none; padding: 10px 15px; border-radius: 8px; cursor: pointer; color: #555; font-weight: bold; }
  .result-area { min-height: 100px; margin-top: 10px; }
  .score-bar-bg { width: 100%; background: #e0e0e0; height: 10px; border-radius: 5px; overflow: hidden; margin: 10px 0; }
  .score-bar-fill { height: 100%; width: 0%; background: #27ae60; transition: width 0.6s ease; }
  .user-said { font-size: 1.1em; color: #2c3e50; margin-top: 5px; font-style: italic; font-weight: bold; }

  #modal-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.9); z-index: 2000; justify-content: center; align-items: center; flex-direction: column; }
  #cert-wrap { background: white; padding: 40px; border: 12px double #004a99; width: 550px; text-align: center; box-sizing: border-box; position: relative; }
  .cert-logo { width: 100px; margin-bottom: 10px; border-radius: 5px; }
  .student-name-input { font-size: 1.8em; border: none; border-bottom: 2px solid #004a99; text-align: center; width: 90%; margin: 20px 0; outline: none; }
  .action-btns { margin-top: 20px; }
  .save-btn { background: #004a99; color: white; border: none; padding: 15px 30px; border-radius: 5px; font-weight: bold; cursor: pointer; }
  .close-btn { background: #95a5a6; color: white; border: none; padding: 15px 30px; border-radius: 5px; cursor: pointer; margin-left: 10px; }
</style>
</head>
<body>

<img src="https://geymd.arnastofnun.is/ismus/data/e534627e-82ae-497a-8a52-22e8170a9f5a/filename.jpg" alt="Hausmynd" class="header-img">

<div class="card">
    <div class="overall-progress">
        Árangur: <span id="completed-count">0</span> / 10
        <div class="progress-container"><div id="overall-fill" class="progress-fill"></div></div>
    </div>

    <h2>Daglegt líf nemanda</h2>
    <span class="counter" id="counter">1 / 10</span>

    <div class="media-container" id="media-content">
        <img id="current-image" class="exercise-img" src="" alt="Sæki...">
    </div>

    <div class="phrase-box"><span class="target-text" id="target">Sæki texta...</span></div>

    <button class="record-btn" id="record-btn">🎙️ Ýttu og lestu</button>

    <div class="result-area">
        <div class="score-bar-bg"><div id="score-fill" class="score-bar-fill"></div></div>
        <div id="accuracy" style="font-weight:bold; color:#004a99;">Nákvæmni: 0%</div>
        <div class="user-said" id="user-input"></div>
    </div>

    <div class="nav-row">
        <button class="nav-btn" onclick="move(-1)">⬅ Fyrri</button>
        <button class="nav-btn" onclick="move(1)">Næsta ➡</button>
    </div>
</div>

<div id="modal-overlay">
    <div id="cert-wrap">
        <img src="https://geymd.arnastofnun.is/ismus/data/e534627e-82ae-497a-8a52-22e8170a9f5a/filename.jpg" alt="Logo" class="cert-logo">
        <h1 style="font-family: serif; font-size: 2.8em; margin: 0; color: #004a99;">SKÍRTEINI</h1>
        <p style="margin: 10px 0;">Þetta staðfestir að nemandinn:</p>
        <input type="text" id="student-name" class="student-name-input" placeholder="Skrifaðu nafnið þitt">
        <p>hefur lokið talæfingunni í íslensku</p>
        <h3 style="color: #333;">Daglegt líf nemanda</h3>
        <p id="cert-date" style="font-size: 0.9em; color: #777;"></p>
        <div style="font-size: 1.5em; margin-top: 10px;">🏆</div>
    </div>
    <div class="action-btns">
        <button class="save-btn" onclick="downloadCert()">💾 Vista skírteini</button>
        <button class="close-btn" onclick="closeModal()">Loka</button>
    </div>
</div>

<script>
// ATH: Fyrir YouTube þarf að nota /embed/ og bæta við ?autoplay=1&mute=1
const exerciseData = [
    { text: "Hér er fyrsta myndbandið.", url: "https://www.youtube.com/embed/dQw4w9WgXcQ?autoplay=1&mute=1" },
    { text: "Ísland er fallegt land.", url: "https://www.youtube.com/embed/3S-n-vR68O8?autoplay=1&mute=1" },
    { text: "Tónlist og fjör.", url: "https://www.youtube.com/embed/5dbG4v6SNoM?autoplay=1&mute=1" },
    { text: "Ég vakna snemma.", url: "https://images.unsplash.com/photo-1511216113906-8f57bb83e776?w=600" },
    { text: "Ég klæði mig.", url: "https://images.unsplash.com/photo-1495474472287-4d71bcdd2085?w=600" },
    { text: "Ég fer í skólann.", url: "https://images.unsplash.com/photo-1544620347-c4fd4a3d5957?w=600" },
    { text: "Ég læri mikið.", url: "https://images.unsplash.com/photo-1524178232363-1fb28f74b671?w=600" },
    { text: "Ég borða nesti.", url: "https://images.unsplash.com/photo-1529107386315-e1a2ed48a620?w=600" },
    { text: "Ég nota tölvu.", url: "https://images.unsplash.com/photo-1496181133206-80ce9b88a853?w=600" },
    { text: "Ég fer að sofa.", url: "https://images.unsplash.com/photo-1515377905703-c4788e51af15?w=600" }
];

let currentIndex = 0;
const recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
recognition.lang = 'is-IS';

window.onload = () => { localStorage.clear(); updateUI(); };

function updateUI() {
    const curr = exerciseData[currentIndex];
    const mediaBox = document.getElementById('media-content');
    document.getElementById('target').innerText = curr.text;
    document.getElementById('counter').innerText = `${currentIndex + 1} / ${exerciseData.length}`;
    document.getElementById('user-input').innerText = "";
    document.getElementById('accuracy').innerText = "Nákvæmni: 0%";
    document.getElementById('score-fill').style.width = "0%";

    if (curr.url.includes('youtube.com')) {
        // allow="autoplay" er nauðsynlegt fyrir iframe
        mediaBox.innerHTML = `<iframe class="video-frame" src="${curr.url}" allow="autoplay; encrypted-media" allowfullscreen></iframe>`;
    } else {
        mediaBox.innerHTML = `<img id="current-image" class="exercise-img" src="${curr.url}" alt="Mynd">`;
    }
}

function calculateProgress() {
    let done = 0;
    for(let i=0; i<exerciseData.length; i++) {
        if((localStorage.getItem('score_'+i) || 0) >= 80) done++;
    }
    document.getElementById('completed-count').innerText = done;
    document.getElementById('overall-fill').style.width = (done/exerciseData.length)*100 + "%";
    if(done === exerciseData.length) {
        document.getElementById('cert-date').innerText = new Date().toLocaleDateString('is-IS');
        document.getElementById('modal-overlay').style.display = 'flex';
    }
}

function move(dir) {
    currentIndex += dir;
    if(currentIndex < 0) currentIndex = 0;
    if(currentIndex >= exerciseData.length) currentIndex = exerciseData.length - 1;
    updateUI();
}

function formatUserSpeech(text) {
    if (!text) return "";
    let formatted = text.trim();
    formatted = formatted.charAt(0).toUpperCase() + formatted.slice(1);
    if (!formatted.endsWith('.')) formatted += ".";
    return formatted;
}

const recBtn = document.getElementById('record-btn');
recBtn.onclick = () => { 
    try {
        recognition.start(); 
        recBtn.classList.add('recording'); 
        recBtn.innerText = "Hlusta..."; 
    } catch(e) { console.log("Hljóðnemi þegar í gangi"); }
};

recognition.onresult = (e) => {
    recBtn.classList.remove('recording'); 
    recBtn.innerText = "🎙️ Ýttu og lestu";
    const rawSpeech = e.results[0][0].transcript;
    const formattedSpeech = formatUserSpeech(rawSpeech);
    const target = exerciseData[currentIndex].text;
    const score = compare(rawSpeech, target);
    
    document.getElementById('user-input').innerText = `„${formattedSpeech}“`;
    document.getElementById('accuracy').innerText = `Nákvæmni: ${score}%`;
    document.getElementById('score-fill').style.width = score + "%";
    if(score >= 80) { localStorage.setItem('score_'+currentIndex, score); calculateProgress(); }
};

recognition.onend = () => {
    recBtn.classList.remove('recording');
    recBtn.innerText = "🎙️ Ýttu og lestu";
};

function compare(s1, s2) {
    const clean = s => s.toLowerCase().replace(/[.,/#!$%^&*;:{}=\-_`~()]/g,"").trim();
    const w1 = clean(s1).split(/\s+/); const w2 = clean(s2).split(/\s+/);
    let m = 0; w1.forEach(w => { if(w2.includes(w)) m++; });
    return Math.round((m / Math.max(w1.length, w2.length)) * 100);
}

async function downloadCert() {
    const name = document.getElementById('student-name').value;
    if(!name) return alert("Vinsamlegast skrifaðu nafn!");
    const wrap = document.getElementById('cert-wrap');
    const input = document.getElementById('student-name');
    const nameText = document.createElement('h2');
    nameText.innerText = name;
    input.style.display = "none";
    input.parentNode.insertBefore(nameText, input);
    
    const canvas = await html2canvas(wrap, {useCORS: true});
    const link = document.createElement('a');
    link.download = `Skirteini-${name}.png`; link.href = canvas.toDataURL(); link.click();
    
    input.style.display = "inline-block"; nameText.remove();
}

function closeModal() { document.getElementById('modal-overlay').style.display = 'none'; }
</script>
