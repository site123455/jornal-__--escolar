jornal-__--escola
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Jornal Escolar Afonso Pena</title>
  <link rel="icon" href="https://i.postimg.cc/HxG8Mxm5/logo-afonso.png" type="image/png" />
  <style>
    body {
      font-family: Arial, sans-serif;
      background-image: url('https://i.postimg.cc/bdLKtX07/Captura-de-tela-2025-07-05-201915.png');
      background-repeat: no-repeat;
      background-size: cover;
      background-position: center center;
      background-attachment: fixed;
      margin: 0;
      padding: 0;
      text-align: center;
      min-height: 100vh;
    }

    header {
      background-color: transparent;
      color: black;
      padding: 20px;
    }

    .site-content, .login-box, .admin-panel {
      display: none;
      margin-top: 30px;
    }

    .active {
      display: block;
    }

    .noticia {
      background: white;
      margin: 20px auto;
      padding: 20px;
      border-radius: 10px;
      width: 80%;
      max-width: 600px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      text-align: left;
      position: relative;
    }

    .noticia h2 {
      cursor: pointer;
      margin: 0;
    }

    .conteudo {
      display: none;
      margin-top: 10px;
    }

    input, textarea {
      padding: 10px;
      margin: 10px auto;
      display: block;
      width: 80%;
      max-width: 400px;
      font-size: 16px;
    }

    button {
      padding: 10px 20px;
      margin: 10px 5px;
      border: none;
      background-color: #0077cc;
      color: white;
      border-radius: 5px;
      cursor: pointer;
      font-size: 16px;
    }

    textarea {
      resize: vertical;
    }

    #formNoticia {
      margin-top: 20px;
      display: none;
    }

    .acoes {
      display: flex;
      flex-wrap: wrap;
      justify-content: flex-end;
      gap: 10px;
      margin-top: 10px;
    }

    .btn-acao {
      background: transparent;
      border: none;
      font-size: 20px;
      cursor: pointer;
      color: #555;
      transition: color 0.3s;
    }

    .btn-acao:hover {
      color: #0077cc;
    }

    .imagem-comunicado {
      width: 100%;
      border-radius: 10px;
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <header>
    <h1>Jornal Escolar Afonso Pena</h1>
    <button onclick="mostrarLogin()">Administração</button>
  </header>

  <div class="site-content active" id="siteContent"></div>

  <div class="login-box" id="loginBox">
    <h2>Login do Administrador</h2>
    <input type="password" id="senha" placeholder="Digite a senha" />
    <button onclick="verificarSenha()">Entrar</button>
    <p id="erro" style="color:red;"></p>
  </div>

  <div class="admin-panel" id="adminPanel">
    <h2>Painel do Administrador</h2>
    <button onclick="mostrarFormulario()">+ Nova Notícia</button>
    <button onclick="sair()">Sair</button>

    <form id="formNoticia" onsubmit="criarOuEditarNoticia(event)">
      <input type="text" id="titulo" placeholder="Título da notícia" required />
      <textarea id="conteudo" placeholder="Conteúdo da notícia" rows="5" required></textarea>
      <input type="hidden" id="indiceEditar" />
      <button type="submit">Salvar</button>
    </form>
  </div>

  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
    import { getDatabase, ref, push, onValue, remove, update } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-database.js";

    const firebaseConfig = {
      apiKey: "AIzaSyBDxkdw7NOy706HCI159oO_o05LGI6dg5c",
      authDomain: "jornal-escolar-7f8e6.firebaseapp.com",
      databaseURL: "https://jornal-escolar-7f8e6-default-rtdb.firebaseio.com",
      projectId: "jornal-escolar-7f8e6",
      storageBucket: "jornal-escolar-7f8e6.appspot.com",
      messagingSenderId: "583101705398",
      appId: "1:583101705398:web:05e70f99042ea8ac5a0a17",
      measurementId: "G-PPKYRDFMC1"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    let modoAdmin = false;
    let noticias = {};

    window.mostrarLogin = function () {
      document.getElementById('loginBox').classList.add('active');
      document.getElementById('siteContent').classList.remove('active');
      document.getElementById('adminPanel').classList.remove('active');
    }

    window.verificarSenha = function () {
      const senha = document.getElementById('senha').value;
      if (senha === "escola afonso pena 123455") {
        modoAdmin = true;
        document.getElementById('loginBox').classList.remove('active');
        document.getElementById('adminPanel').classList.add('active');
        document.getElementById('siteContent').classList.add('active');
      } else {
        document.getElementById('erro').textContent = "Senha incorreta!";
      }
    }

    window.mostrarFormulario = function (id = null) {
      const form = document.getElementById('formNoticia');
      if (id) {
        document.getElementById('titulo').value = noticias[id].titulo;
        document.getElementById('conteudo').value = noticias[id].conteudo;
        document.getElementById('indiceEditar').value = id;
      } else {
        form.reset();
        document.getElementById('indiceEditar').value = "";
      }
      form.style.display = 'block';
    }

    window.criarOuEditarNoticia = function (event) {
      event.preventDefault();
      const titulo = document.getElementById('titulo').value.trim();
      const conteudo = document.getElementById('conteudo').value.trim();
      const id = document.getElementById('indiceEditar').value;

      if (!titulo || !conteudo) {
        alert("Preencha todos os campos!");
        return;
      }

      if (id) {
        update(ref(db, 'noticias/' + id), { titulo, conteudo });
      } else {
        push(ref(db, 'noticias'), { titulo, conteudo });
      }

      document.getElementById('formNoticia').reset();
      document.getElementById('formNoticia').style.display = 'none';
    }

    window.alternarConteudo = function (elemento) {
      const conteudo = elemento.nextElementSibling;
      conteudo.style.display = conteudo.style.display === 'block' ? 'none' : 'block';
    }

    window.apagarNoticia = function (id) {
      if (confirm("Deseja apagar esta notícia?")) {
        remove(ref(db, 'noticias/' + id));
      }
    }

    window.sair = function () {
      modoAdmin = false;
      document.getElementById('adminPanel').classList.remove('active');
    }

    function atualizarNoticiasNaTela(snapshot) {
      const container = document.getElementById('siteContent');
      container.innerHTML = "";
      noticias = snapshot.val() || {};

      for (const id in noticias) {
        const noticia = noticias[id];

        const div = document.createElement('div');
        div.className = 'noticia';

        const tituloStyle = noticia.imagemTitulo ? `
          background-image: url('${noticia.imagemTitulo}');
          background-size: cover;
          background-position: center;
          color: white;
          padding: 20px;
          border-radius: 10px;
          text-shadow: 1px 1px 4px rgba(0,0,0,0.8);
        ` : '';

        let html = `
          <h2 onclick="alternarConteudo(this)" style="${tituloStyle}">${noticia.titulo}</h2>
          <div class="conteudo">
            ${noticia.imagemConteudo ? `<img src="${noticia.imagemConteudo}" class="imagem-comunicado" alt="Imagem do comunicado">` : ''}
            <p>${noticia.conteudo}</p>
          </div>
        `;

        div.innerHTML = html;

        if (modoAdmin) {
          const acoesDiv = document.createElement('div');
          acoesDiv.className = 'acoes';

          const btnEditar = document.createElement('button');
          btnEditar.innerHTML = "✏️";
          btnEditar.className = "btn-acao";
          btnEditar.title = "Editar notícia";
          btnEditar.onclick = () => mostrarFormulario(id);

          const btnExcluir = document.createElement('button');
          btnExcluir.innerHTML = "🗑️";
          btnExcluir.className = "btn-acao";
          btnExcluir.title = "Excluir notícia";
          btnExcluir.onclick = () => apagarNoticia(id);

          const btnImagemTitulo = document.createElement('button');
          btnImagemTitulo.innerHTML = "📷";
          btnImagemTitulo.className = "btn-acao";
          btnImagemTitulo.title = "Imagem no título";
          btnImagemTitulo.onclick = () => {
            const url = prompt("Cole o link da imagem de fundo do título (ou deixe em branco para remover):", noticia.imagemTitulo || "");
            if (url !== null) {
              update(ref(db, 'noticias/' + id), { imagemTitulo: url.trim() });
            }
          };

          const btnImagemConteudo = document.createElement('button');
          btnImagemConteudo.innerHTML = "🖼️";
          btnImagemConteudo.className = "btn-acao";
          btnImagemConteudo.title = "Imagem no comunicado";
          btnImagemConteudo.onclick = () => {
            const url = prompt("Cole o link da imagem do comunicado (ou deixe em branco para remover):", noticia.imagemConteudo || "");
            if (url !== null) {
              update(ref(db, 'noticias/' + id), { imagemConteudo: url.trim() });
            }
          };

          acoesDiv.appendChild(btnEditar);
          acoesDiv.appendChild(btnExcluir);
          acoesDiv.appendChild(btnImagemTitulo);
          acoesDiv.appendChild(btnImagemConteudo);
          div.appendChild(acoesDiv);
        }

        container.appendChild(div);
      }
    }

    onValue(ref(db, 'noticias'), atualizarNoticiasNaTela);
  </script>
</body>
<!-- Início do chatbot IA flutuante -->
<style>
  #chatbot-button {
    position: fixed;
    bottom: 20px;
    right: 20px;
    background-color: #004aad;
    color: white;
    border: none;
    border-radius: 50%;
    width: 60px;
    height: 60px;
    font-size: 28px;
    cursor: pointer;
    box-shadow: 0 4px 8px rgba(0,0,0,0.3);
    z-index: 10000;
  }
  #chatbot-window {
    position: fixed;
    bottom: 90px;
    right: 20px;
    width: 320px;
    max-height: 450px;
    background: #fff0f6; /* rosa claro */
    border: 2px solid #004aad;
    border-radius: 12px;
    box-shadow: 0 8px 20px rgba(0,0,0,0.3);
    display: none;
    flex-direction: column;
    font-family: Arial, sans-serif;
    z-index: 10000;
  }
  #chatbot-header {
    background-color: #004aad;
    color: white;
    padding: 12px;
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;
    font-weight: bold;
    text-align: center;
  }
  #chatbot-messages {
    flex: 1;
    padding: 10px;
    overflow-y: auto;
    font-size: 14px;
    color: #333;
  }
  .chatbot-msg {
    margin-bottom: 10px;
    padding: 8px 12px;
    border-radius: 15px;
    max-width: 80%;
    clear: both;
  }
  .chatbot-msg.user {
    background-color: #004aad;
    color: white;
    margin-left: auto;
    border-bottom-right-radius: 0;
  }
  .chatbot-msg.bot {
    background-color: #f8d7e9; /* rosa claro */
    color: #004aad;
    margin-right: auto;
    border-bottom-left-radius: 0;
  }
  #chatbot-input-area {
    display: flex;
    border-top: 1px solid #ddd;
    padding: 8px;
    background-color: white;
    border-bottom-left-radius: 10px;
    border-bottom-right-radius: 10px;
  }
  #chatbot-input {
    flex: 1;
    padding: 8px 12px;
    font-size: 14px;
    border: 1px solid #ccc;
    border-radius: 15px;
    outline: none;
  }
  #chatbot-send {
    background-color: #004aad;
    color: white;
    border: none;
    padding: 0 16px;
    margin-left: 8px;
    border-radius: 15px;
    cursor: pointer;
    font-weight: bold;
  }
  #chatbot-send:disabled {
    background-color: #7a9ac9;
    cursor: not-allowed;
  }
</style>

<button id="chatbot-button" title="Abrir chat da IA">🤖</button>

<div id="chatbot-window" aria-live="polite" aria-label="Chat da inteligência artificial">
  <div id="chatbot-header">IA do Jornal Escolar</div>
  <div id="chatbot-messages" role="log" aria-live="polite" aria-relevant="additions"></div>
  <div id="chatbot-input-area">
    <input type="text" id="chatbot-input" placeholder="Digite sua mensagem..." autocomplete="off" />
    <button id="chatbot-send" disabled>Enviar</button>
  </div>
</div>

<script type="module">
  import { getDatabase, ref, push, onValue } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-database.js";

  // Usa o app Firebase já inicializado no seu script principal
  const db = getDatabase();

  const button = document.getElementById('chatbot-button');
  const chatWindow = document.getElementById('chatbot-window');
  const messagesContainer = document.getElementById('chatbot-messages');
  const input = document.getElementById('chatbot-input');
  const sendBtn = document.getElementById('chatbot-send');

  let isOpen = false;

  button.addEventListener('click', () => {
    isOpen = !isOpen;
    chatWindow.style.display = isOpen ? 'flex' : 'none';
    if (isOpen) {
      input.focus();
      loadMessages();
    }
  });

  input.addEventListener('input', () => {
    sendBtn.disabled = input.value.trim() === "";
  });

  input.addEventListener('keydown', (e) => {
    if(e.key === 'Enter' && !sendBtn.disabled) {
      sendMessage();
    }
  });

  sendBtn.addEventListener('click', () => {
    sendMessage();
  });

  // Carrega mensagens do Firebase (últimos 50)
  function loadMessages() {
    messagesContainer.innerHTML = "";
    const chatRef = ref(db, 'chatbot-messages');
    onValue(chatRef, (snapshot) => {
      messagesContainer.innerHTML = "";
      const data = snapshot.val() || {};
      const keys = Object.keys(data).slice(-50);
      keys.forEach(key => {
        const msg = data[key];
        addMessageToChat(msg.text, msg.sender, false);
      });
      scrollToBottom();
    }, { onlyOnce: true });
  }

  // Adiciona mensagem na interface
  function addMessageToChat(text, sender, save = true) {
    const div = document.createElement('div');
    div.classList.add('chatbot-msg', sender);
    div.textContent = text;
    messagesContainer.appendChild(div);
    scrollToBottom();

    if (save) {
      const chatRef = ref(db, 'chatbot-messages');
      push(chatRef, { text, sender });
    }
  }

  // Faz o scroll ir para o final
  function scrollToBottom() {
    messagesContainer.scrollTop = messagesContainer.scrollHeight;
  }

  // Gera resposta simples (você pode ampliar)
  function gerarResposta(mensagem) {
    const msg = mensagem.toLowerCase();

    if (msg.includes('olá') || msg.includes('oi')) {
      return "Olá! Eu sou a IA do Jornal Escolar Afonso Pena. Como posso ajudar?";
    }
    if (msg.includes('jornal')) {
      return "Aqui você encontra as últimas notícias da escola e eventos importantes.";
    }
    if (msg.includes('quem é você')) {
      return "Sou um assistente virtual criado para ajudar com informações do jornal escolar.";
    }
    if (msg.includes('tchau') || msg.includes('adeus')) {
      return "Até logo! Volte sempre que precisar.";
    }
    if (msg.includes('como você funciona')) {
      return "Eu uso respostas programadas para conversar, mas estou sempre aprendendo!";
    }
    return "Desculpe, não entendi. Pode perguntar de outro jeito?";
  }

  // Função que usa Web Speech API para falar a resposta
  function falar(texto) {
    if (!('speechSynthesis' in window)) return;
    const utterance = new SpeechSynthesisUtterance(texto);
    utterance.lang = 'pt-BR';
    speechSynthesis.speak(utterance);
  }

  // Envia a mensagem do usuário e processa a resposta da IA
  function sendMessage() {
    const texto = input.value.trim();
    if (!texto) return;
    addMessageToChat(texto, 'user');
    input.value = '';
    sendBtn.disabled = true;

    // Simula delay da IA
    setTimeout(() => {
      const resposta = gerarResposta(texto);
      addMessageToChat(resposta, 'bot');
      falar(resposta);
    }, 800);
  }
</script>
<!-- Fim do chatbot IA flutuante -->
</html>
