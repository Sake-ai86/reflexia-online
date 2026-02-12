<!DOCTYPE html>
<html lang="kk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Онлайн Рефлексия + QR</title>

  <!-- Firebase -->
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

  <style>
    body{margin:0;font-family:Arial,sans-serif;background:linear-gradient(135deg,#667eea,#764ba2);min-height:100vh;display:flex;justify-content:center;align-items:center;padding:15px;}
    .container{background:white;width:100%;max-width:500px;padding:25px;border-radius:20px;text-align:center;box-shadow:0 8px 25px rgba(0,0,0,0.25);}
    h2{margin-bottom:10px;}
    p{color:#666;margin-bottom:15px;}
    .buttons{display:flex;flex-direction:column;gap:12px;margin-bottom:20px;}
    button{padding:16px;border:none;border-radius:15px;font-size:16px;font-weight:bold;cursor:pointer;}
    .ok{background:#28a745;color:white;}
    .question{background:#ffc107;color:#333;}
    .no{background:#dc3545;color:white;}
    .reset{background:#6c757d;color:white;margin-top:10px;}
    .counter{margin-top:15px;font-size:14px;line-height:1.6;}
    .result{margin-top:15px;padding:12px;background:#f2f2f2;border-radius:12px;font-weight:bold;}
    .qr img{width:200px;height:200px;margin-top:15px;}
    input{width:100%;padding:10px;border-radius:8px;border:1px solid #ccc;margin:10px 0;font-size:14px;}
  </style>
</head>
<body>
<div class="container">

  <h2>📘 Онлайн рефлексия + QR</h2>
  <p>Сабақты қалай түсіндің?</p>

  <div class="buttons">
    <button class="ok" onclick="vote('ok')">✅ Түсінікті</button>
    <button class="question" onclick="vote('question')">❓ Сұрағым бар</button>
    <button class="no" onclick="vote('no')">❌ Түсінбедім</button>
  </div>

  <div class="counter">
    ✅ Түсінікті: <span id="okCount">0</span><br>
    ❓ Сұрағым бар: <span id="questionCount">0</span><br>
    ❌ Түсінбедім: <span id="noCount">0</span>
  </div>

  <div class="result" id="result">Дауыс беріңіз</div>

  <button class="reset" onclick="resetVotes()">🔄 Қайта бастау</button>

  <p>QR арқылы кіру үшін сілтеме:</p>
  <input type="text" id="link" placeholder="https://mysite.kz/reflexia.html">
  <button onclick="generateQR()">QR-код жасау</button>
  <div class="qr" id="qr"></div>
</div>

<script>
// Firebase баптау
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  databaseURL: "https://YOUR_PROJECT.firebaseio.com",
  projectId: "YOUR_PROJECT",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "000000000",
  appId: "APP_ID"
};
firebase.initializeApp(firebaseConfig);
const db = firebase.database();
const votesRef = db.ref("votes");

// Дауыс беру
function vote(type){
  votesRef.child(type).transaction(current => (current || 0) + 1);
}

// Нақты уақыттағы нәтиже
votesRef.on("value", snapshot => {
  const data = snapshot.val() || {};
  const ok = data.ok || 0;
  const question = data.question || 0;
  const no = data.no || 0;
  document.getElementById("okCount").textContent = ok;
  document.getElementById("questionCount").textContent = question;
  document.getElementById("noCount").textContent = no;
  showResult(ok,question,no);
});

function showResult(ok,question,no){
  const max = Math.max(ok,question,no);
  let text="";
  if(max===0) text="Дауыс жоқ";
  else if(max===ok) text="📗 Көпшілік түсінді";
  else if(max===question) text="📙 Көпшілікте сұрақ бар";
  else text="📕 Қайта түсіндіру қажет";
  document.getElementById("result").textContent=text;
}

function resetVotes(){
  votesRef.set({ok:0,question:0,no:0});
}

// QR генератор
function generateQR(){
  const link = document.getElementById("link").value;
  if(link===""){alert("Сілтемені енгізіңіз!"); return;}
  const url = "https://api.qrserver.com/v1/create-qr-code/?size=300x300&data="+encodeURIComponent(link);
  document.getElementById("qr").innerHTML=`<img src="${url}" alt="QR Code">`;
}
</script>
</body>
</html>
