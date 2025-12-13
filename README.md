<html lang="pt-BR">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  
  <!-- 🔹 Ícone do navegador (favicon) -->
  <link rel="icon" type="image/x-icon" href="logo.ico">

  <title>Cadastro de Colaboradores</title>

  <style>
  :root {
    --accent:#2196F3;
    --dark:#111;
  }

  *{box-sizing:border-box;margin:0;padding:0;}

  body{
    font-family:Inter, Arial, sans-serif;
    background: url('fundo.jpg') no-repeat center center fixed;
    background-size: 1000px 1000px; /* 🔹 ajuste o tamanho do fundo */
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    color: var(--dark);
  }

  .container{
    max-width:500px;
    width: 100%;
    background: rgba(255,255,255,0.7);
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    padding: 30px 24px;
    border-radius: 12px;
    box-shadow: 0 6px 25px rgba(0,0,0,0.2);
  }

  label{display:block;margin-top:10px;color:var(--dark);}
  input, select{
    width:100%;
    padding:10px;
    margin-top:6px;
    border-radius:6px;
    border:1px solid #ccc;
    font-size:15px;
    background: #fff;
  }

  button{
    width:100%;
    padding:10px;
    margin-top:12px;
    border-radius:8px;
    border:0;
    background: var(--accent);
    color:#fff;
    cursor:pointer;
    font-weight:bold;
  }
  button:hover{opacity:.95;}

  .msg{
    margin-top:12px;
    text-align:center;
    font-weight:600;
  }
  .msg.success{color:green;}
  .msg.err{color:#c62828;}
  </style>
</head>
<body>

<div class="container">
  <form id="formCadastro">
    <label for="nome">Nome completo</label>
    <input id="nome" type="text" required>
    <label for="matricula">Matrícula (4–11 dígitos)</label>
    <input id="matricula" type="text" pattern="\d{4,11}" required placeholder="Somente números">
    <label for="cargo">Cargo</label>
    <input id="cargo" type="text" required>
    <label for="turno">Turno</label>
    <select id="turno" required>
      <option value="">Selecione</option>
      <option>Manhã</option>
      <option>Tarde</option>
      <option>Noite</option>
    </select>
    <label for="email">E-mail (obrigatório)</label>
    <input id="email" type="email" required placeholder="seu@exemplo.com">
    <button type="submit">Cadastrar</button>
    <p id="msg" class="msg" style="display:none"></p>
  </form>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
import { 
  getFirestore, collection, addDoc, query, where, getDocs, serverTimestamp 
} from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "AIzaSyCpBiFzqOod4K32cWMr5hfx13fw6LGcPVY",
  authDomain: "ponto-eletronico-f35f9.firebaseapp.com",
  projectId: "ponto-eletronico-f35f9",
  storageBucket: "ponto-eletronico-f35f9.firebasestorage.app",
  messagingSenderId: "208638350255",
  appId: "1:208638350255:web:63d016867a67575b5e155a"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

const form = document.getElementById('formCadastro');
const msgEl = document.getElementById('msg');

form.addEventListener('submit', async (e)=>{
  e.preventDefault();
  msgEl.style.display='none';

  const nome = form.nome.value.trim();
  const matricula = form.matricula.value.trim();
  const cargo = form.cargo.value.trim();
  const turno = form.turno.value;
  const email = form.email.value.trim();

  if (!nome || !matricula || !cargo || !turno || !email) {
    showMsg('Preencha todos os campos.', true);
    return;
  }

  try {
    const colRef = collection(db, 'colaboradores');
    const q = query(colRef, where('matricula', '==', matricula));
    const q2 = query(colRef, where('email', '==', email));

    const [snapMat, snapEmail] = await Promise.all([getDocs(q), getDocs(q2)]);

    if (!snapMat.empty) {
      showMsg('❌ Matrícula já cadastrada.', true);
      return;
    }
    if (!snapEmail.empty) {
      showMsg('❌ E-mail já cadastrado.', true);
      return;
    }

    await addDoc(colRef, {
      nome, matricula, cargo, turno, email, criadoEm: serverTimestamp()
    });

    showMsg('✅ Cadastro realizado com sucesso!', false);
    form.reset();

  } catch (err) {
    console.error(err);
    showMsg('Erro ao cadastrar: ' + (err.message || err), true);
  }
});

function showMsg(text, isError){
  msgEl.textContent = text;
  msgEl.className = isError ? 'msg err' : 'msg success';
  msgEl.style.display = 'block';
  setTimeout(()=> msgEl.style.display='none', 5000);
}
</script>
<body>
    <!-- TODO O SEU SITE AQUI -->
    <footer style="text-align:center; margin-top:20px; padding:10px; font-size:14px; color:#555;">
        Criado por CLX
    </footer>
</body>
