# txokiassistente
const chatBody = document.getElementById("chat-body");
const chatForm = document.getElementById("chat-form");
const userInput = document.getElementById("user-input");
const clearButton = document.getElementById("clear-chat");
const suggestions = document.getElementById("suggestions");

document.getElementById("year").textContent = new Date().getFullYear();

const initialMessage = "Olá! 👋 Sou o Txoki. Posso falar sobre tecnologia, programação ou ajudar-te a organizar uma ideia. O que gostarias de saber?";

function addMessage(text, sender) {
  const row = document.createElement("div");
  row.className = `message-row ${sender === "user" ? "user-row" : "bot-row"}`;

  if (sender === "bot") {
    const avatar = document.createElement("div");
    avatar.className = "mini-avatar";
    avatar.textContent = "T";
    row.appendChild(avatar);
  }

  const bubble = document.createElement("div");
  bubble.className = `bubble ${sender === "user" ? "user-bubble" : "bot-bubble"}`;
  // textContent evita interpretar mensagens como HTML.
  bubble.textContent = text;
  row.appendChild(bubble);
  chatBody.appendChild(row);
  chatBody.scrollTop = chatBody.scrollHeight;
}

function getReply(rawText) {
  const text = rawText.toLocaleLowerCase("pt").normalize("NFD").replace(/[\u0300-\u036f]/g, "");

  if (/\b(ola|oi|bom dia|boa tarde|boa noite|hey|hello)\b/.test(text)) {
    return "Olá! 😊 Como estás? Diz-me o que queres explorar e vou tentar ajudar.";
  }
  if (text.includes("quem es") || text.includes("teu nome") || text.includes("quem e voce")) {
    return "Sou o Txoki, um assistente virtual de demonstração criado em HTML, CSS e JavaScript. Ainda não sou uma IA real: respondo através de regras simples programadas no código.";
  }
  if (text.includes("programacao") || text.includes("programar") || text.includes("codigo")) {
    return "Programação é escrever instruções para o computador executar. Para começar, experimenta HTML para a estrutura, CSS para o visual e JavaScript para as interações. Este próprio projeto usa os três!";
  }
  if (text.includes("projeto") || text.includes("ideia")) {
    return "Ideia para o próximo projeto: uma lista de tarefas com botão para adicionar, concluir e apagar tarefas. Depois podes guardar as tarefas no navegador com localStorage.";
  }
  if (text.includes("github") || text.includes("publicar") || text.includes("site online")) {
    return "Para publicar: cria um repositório no GitHub, envia index.html, style.css, script.js e favicon.svg. Depois abre Settings → Pages, escolhe Deploy from a branch, seleciona main e / (root), e guarda.";
  }
  if (text.includes("html")) {
    return "HTML define a estrutura de uma página. Por exemplo, <h1>Olá!</h1> cria um título. O CSS controla o aspeto e o JavaScript acrescenta comportamento.";
  }
  if (text.includes("obrigad")) {
    return "De nada! 🚀 Continua a experimentar e a melhorar o projeto.";
  }
  if (text.includes("ajuda") || text.includes("podes fazer")) {
    return "Posso responder a perguntas básicas sobre programação, explicar este projeto ou sugerir ideias. Experimenta perguntar: “O que é programação?” ou “Dá-me uma ideia de projeto”.";
  }
  return "Boa pergunta! Nesta versão de demonstração ainda tenho respostas limitadas. Experimenta perguntar sobre programação, HTML, GitHub ou pedir uma ideia de projeto. Para respostas abertas, seria necessário ligar uma API de IA a um servidor seguro.";
}

function sendMessage(text) {
  const cleanText = text.trim();
  if (!cleanText) return;
  addMessage(cleanText, "user");
  if (suggestions) suggestions.remove();
  userInput.value = "";
  // Pequena pausa para simular uma resposta; não é uma chamada a uma IA.
  window.setTimeout(() => addMessage(getReply(cleanText), "bot"), 250);
}

chatForm.addEventListener("submit", (event) => {
  event.preventDefault();
  sendMessage(userInput.value);
});

document.querySelectorAll(".suggestion").forEach((button) => {
  button.addEventListener("click", () => sendMessage(button.dataset.prompt || button.textContent));
});

clearButton.addEventListener("click", () => {
  chatBody.replaceChildren();
  addMessage(initialMessage, "bot");
  const newSuggestions = document.createElement("div");
  newSuggestions.className = "suggestions";
  newSuggestions.id = "suggestions";
  [
    ["O que é programação?", "O que é programação?"],
    ["Dá-me uma ideia de projeto", "Ideia de projeto"],
    ["Quem és tu?", "Quem és tu?"]
  ].forEach(([prompt, label]) => {
    const button = document.createElement("button");
    button.className = "suggestion";
    button.dataset.prompt = prompt;
    button.textContent = label;
    button.addEventListener("click", () => sendMessage(prompt));
    newSuggestions.appendChild(button);
  });
  chatBody.appendChild(newSuggestions);
  userInput.focus();
});
