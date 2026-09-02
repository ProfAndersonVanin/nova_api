# API de Livros com FastAPI e MySQL

## Parte 4 de 4: Construção do Front End com HTML, CSS e JavaScript

### Datas de entrega da Etapa 4

| Turma | Data de entrega |
| --- | --- |
| 3F | 23/09/2026 |
| 3C | 24/09/2026 |

> **ATENÇÃO: NÃO SERÃO ACEITAS ENTREGAS ANTECIPADAS.** A data e o horário dos commits serão verificados no histórico do repositório do aluno no GitHub antes da aceitação da entrega. O commit da Etapa 4 deverá ser realizado na data correspondente à turma.

> **Regra da atividade:** esta parte deve ser realizada em aula, uma etapa por vez. Não desenvolva todo o Front End de uma única vez. Ao final de cada etapa prática, faça o commit indicado e envie as alterações para o repositório GitHub criado no Guia 1. O resultado será avaliado ao final do projeto.

Neste manual será criada uma interface web para consumir a API de livros construída nas partes anteriores. O usuário poderá:

- visualizar os livros cadastrados;
- cadastrar um novo livro;
- editar um livro existente;
- excluir um livro;
- receber mensagens de sucesso ou erro.

O Front End será feito sem frameworks. Usaremos apenas:

- **HTML** para estruturar a página;
- **CSS** para aparência e responsividade;
- **JavaScript** para fazer requisições HTTP e atualizar a tela.

A API continuará sendo responsável pelas regras, validações e pelo acesso ao MySQL. O navegador não acessará o banco diretamente.

As rotas consumidas neste manual devem seguir o padrão didático definido na Parte 1: funções nomeadas com `def` e instruções simples, sem `lambda` ou funções anônimas. O JavaScript deste manual apenas envia requisições e exibe os resultados; as regras de negócio continuam nas rotas do FastAPI.

---

## 1. Pré-requisitos

Antes de iniciar, confirme que:

1. A API FastAPI está funcionando.
2. O banco MySQL está disponível.
3. As rotas CRUD de livros das Partes 2 e 3 estão implementadas.
4. A API responde em `http://127.0.0.1:8000`.

Como esta é uma nova etapa de aula, clone novamente o projeto pelo VS Code antes de começar. Use `Ctrl+Shift+P`, execute **Git: Clone**, abra o projeto clonado e reinstale as dependências com `pip install -r requirements.txt` no terminal integrado. Depois, importe `database/biblioteca_db.sql` pelo phpMyAdmin do XAMPP para reconstruir o banco antes de executar o Front End.

Este manual considera que as rotas possuem o seguinte contrato:

| Método | URL | Corpo enviado | Resultado esperado |
| --- | --- | --- | --- |
| `GET` | `/livros` | Nenhum | Lista de livros |
| `POST` | `/livros` | Dados do livro sem `id` | Livro criado |
| `PUT` | `/livros/{id}` | Dados atualizados | Livro atualizado |
| `DELETE` | `/livros/{id}` | Nenhum | Livro excluído |

O JSON usado para criar ou atualizar um livro será:

```json
{
  "titulo": "O Hobbit",
  "autor": "J. R. R. Tolkien",
  "ano_publicacao": 1937,
  "disponivel": true
}
```

Se as Partes 2 e 3 usarem nomes de campos diferentes, será necessário alterar os nomes no HTML e no JavaScript deste manual para que os dois lados tenham o mesmo contrato.

### Commit da etapa 1

Antes de alterar o código, confirme que a API está funcionando e registre a preparação desta parte:

No VS Code, em **Source Control**, confira as alterações, clique em `+`, escreva `docs: prepara integracao do frontend`, clique em **Commit** e depois em **Sync Changes**.

---

## 2. Configure o CORS na API

Quando o HTML é executado por um servidor local diferente do servidor FastAPI, o navegador considera que são origens diferentes. Por exemplo:

- Front End: `http://127.0.0.1:5500`;
- API: `http://127.0.0.1:8000`.

Por segurança, o navegador bloqueia requisições entre origens diferentes quando a API não autoriza essa comunicação. Essa regra é chamada de **CORS**, sigla para Cross-Origin Resource Sharing.

Abra `app/main.py` e adicione o import:

```python
from fastapi.middleware.cors import CORSMiddleware
```

Depois da criação da variável `app`, configure as origens permitidas:

```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "http://127.0.0.1:5500",
        "http://localhost:5500",
    ],
    allow_credentials=False,
    allow_methods=["GET", "POST", "PUT", "DELETE"],
    allow_headers=["Content-Type"],
)
```

O bloco completo deve ficar próximo da criação do FastAPI:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware


app = FastAPI(
    title="API de Livros",
    version="1.0.0",
    description="API didática para gerenciamento de livros.",
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "http://127.0.0.1:5500",
        "http://localhost:5500",
    ],
    allow_credentials=False,
    allow_methods=["GET", "POST", "PUT", "DELETE"],
    allow_headers=["Content-Type"],
)
```

### Por que não usar `allow_origins=["*"]`?

O asterisco libera a API para qualquer origem. Isso pode ser aceitável em um teste muito simples, mas não é uma boa configuração para uma aplicação publicada. Durante o desenvolvimento, informe explicitamente os endereços usados pelo Front End.

Depois de salvar `main.py`, reinicie a API caso ela não esteja usando `--reload`.

### Commit da etapa 2

Registre a configuração do CORS na API:

No VS Code, selecione `app/main.py` em **Source Control**, clique em `+`, informe `feat: permite acesso do frontend`, clique em **Commit** e depois em **Sync Changes**.

---

## 3. Crie a pasta do Front End

Na raiz do projeto, crie a pasta `frontend`:

```bat
mkdir frontend
type nul > frontend\index.html
type nul > frontend\styles.css
type nul > frontend\app.js
```

A estrutura ficará assim:

```text
api-livros/
├── app/
│   ├── database.py
│   └── main.py
└── frontend/
    ├── index.html
    ├── styles.css
    └── app.js
```

A separação em três arquivos facilita a manutenção:

- `index.html` contém os elementos da página;
- `styles.css` contém somente a apresentação;
- `app.js` contém o comportamento e a comunicação com a API.

### Commit da etapa 3

Registre a criação da estrutura do Front End:

No VS Code, selecione os arquivos da pasta `frontend` em **Source Control**, clique em `+`, informe `chore: cria estrutura do frontend`, clique em **Commit** e depois em **Sync Changes**.

---

## 4. Construa a estrutura HTML

Abra `frontend/index.html` e adicione:

```html
<!DOCTYPE html>
<html lang="pt-BR">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Biblioteca de Livros</title>
    <link rel="stylesheet" href="styles.css" />
    <script src="app.js" defer></script>
  </head>
  <body>
    <header class="site-header">
      <div class="container header-content">
        <p class="eyebrow">CATÁLOGO DIGITAL</p>
        <h1>Biblioteca de Livros</h1>
        <p class="subtitle">Organize seu acervo em um só lugar.</p>
      </div>
    </header>

    <main class="container page-content">
      <section class="panel form-panel" aria-labelledby="form-title">
        <div class="section-heading">
          <div>
            <p class="eyebrow">GERENCIAMENTO</p>
            <h2 id="form-title">Cadastrar livro</h2>
          </div>
          <span id="form-mode" class="mode-label">Novo registro</span>
        </div>

        <form id="book-form">
          <input id="book-id" type="hidden" />

          <div class="form-grid">
            <label for="title">
              Título
              <input id="title" name="titulo" type="text" required maxlength="150" />
            </label>

            <label for="author">
              Autor
              <input id="author" name="autor" type="text" required maxlength="120" />
            </label>

            <label for="publication-year">
              Ano de publicação
              <input
                id="publication-year"
                name="ano_publicacao"
                type="number"
                min="0"
                max="2100"
                required
              />
            </label>

            <label class="checkbox-label" for="available">
              <input id="available" name="disponivel" type="checkbox" checked />
              Livro disponível
            </label>
          </div>

          <div class="form-actions">
            <button id="submit-button" class="button button-primary" type="submit">
              Salvar livro
            </button>
            <button id="cancel-button" class="button button-secondary" type="button" hidden>
              Cancelar edição
            </button>
          </div>
        </form>

        <p id="form-message" class="message" role="status" aria-live="polite"></p>
      </section>

      <section class="panel list-panel" aria-labelledby="list-title">
        <div class="section-heading list-heading">
          <div>
            <p class="eyebrow">ACERVO</p>
            <h2 id="list-title">Livros cadastrados</h2>
          </div>
          <button id="refresh-button" class="button button-secondary" type="button">
            Atualizar lista
          </button>
        </div>

        <p id="list-message" class="message" role="status" aria-live="polite"></p>

        <div class="table-wrapper">
          <table>
            <thead>
              <tr>
                <th>Título</th>
                <th>Autor</th>
                <th>Ano</th>
                <th>Status</th>
                <th class="actions-column">Ações</th>
              </tr>
            </thead>
            <tbody id="books-table-body"></tbody>
          </table>
        </div>

        <p id="empty-state" class="empty-state" hidden>
          Nenhum livro foi cadastrado ainda.
        </p>
      </section>
    </main>
  </body>
</html>
```

### Entenda os principais elementos

- O formulário possui `id="book-form"`, que será localizado pelo JavaScript.
- O campo oculto `book-id` guarda o identificador durante uma edição.
- Os campos `name` correspondem aos nomes esperados pela API.
- `required`, `maxlength`, `min` e `max` fornecem uma primeira validação no navegador.
- O `tbody` com `id="books-table-body"` receberá as linhas geradas pelo JavaScript.
- `aria-live` permite que leitores de tela percebam mensagens de resultado.
- O atributo `defer` faz o navegador carregar o JavaScript sem bloquear a leitura do HTML.

A validação do navegador melhora a experiência, mas não substitui a validação da API. Um usuário ou outro cliente pode enviar requisições diretamente para o servidor.

### Commit da etapa 4

Envie a estrutura HTML:

No VS Code, selecione `frontend/index.html` em **Source Control**, clique em `+`, informe `feat: cria pagina de livros`, clique em **Commit** e depois em **Sync Changes**.

---

## 5. Adicione os estilos CSS

Abra `frontend/styles.css` e adicione:

```css
:root {
  --ink: #17252f;
  --muted: #65747d;
  --paper: #f5f1e8;
  --surface: #fffdf8;
  --line: #ded7c9;
  --accent: #c45032;
  --accent-dark: #913a27;
  --teal: #176b67;
  --success: #246b48;
  --danger: #a13b32;
  font-family: Georgia, "Times New Roman", serif;
}

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  min-width: 320px;
  color: var(--ink);
  background:
    linear-gradient(135deg, rgba(196, 80, 50, 0.08), transparent 38%),
    var(--paper);
}

.site-header {
  color: #fffaf0;
  background: var(--teal);
  border-bottom: 8px solid var(--accent);
}

.container {
  width: min(1120px, calc(100% - 40px));
  margin: 0 auto;
}

.header-content {
  padding: 56px 0 48px;
}

.eyebrow {
  margin: 0 0 10px;
  color: var(--accent);
  font-family: Arial, sans-serif;
  font-size: 0.72rem;
  font-weight: 700;
  letter-spacing: 0.12em;
}

.site-header .eyebrow {
  color: #f2b29d;
}

h1,
h2,
p {
  margin-top: 0;
}

h1 {
  max-width: 700px;
  margin-bottom: 12px;
  font-size: clamp(2.6rem, 7vw, 5.6rem);
  font-weight: 400;
  line-height: 0.95;
}

h2 {
  margin-bottom: 0;
  font-size: clamp(1.65rem, 3vw, 2.35rem);
  font-weight: 400;
}

.subtitle {
  max-width: 500px;
  margin-bottom: 0;
  color: #d5e4df;
  font-size: 1.2rem;
}

.page-content {
  display: grid;
  gap: 28px;
  padding: 36px 0 64px;
}

.panel {
  padding: clamp(22px, 4vw, 38px);
  background: var(--surface);
  border: 1px solid var(--line);
  box-shadow: 8px 8px 0 rgba(23, 37, 47, 0.07);
}

.section-heading {
  display: flex;
  align-items: end;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 28px;
}

.mode-label {
  color: var(--muted);
  font-family: Arial, sans-serif;
  font-size: 0.8rem;
  text-transform: uppercase;
}

.form-grid {
  display: grid;
  grid-template-columns: 1.2fr 1.2fr 0.7fr 0.8fr;
  gap: 18px;
  align-items: end;
}

label {
  display: grid;
  gap: 8px;
  color: var(--muted);
  font-family: Arial, sans-serif;
  font-size: 0.82rem;
  font-weight: 700;
}

input[type="text"],
input[type="number"] {
  width: 100%;
  min-height: 44px;
  padding: 10px 12px;
  color: var(--ink);
  font: inherit;
  background: #fff;
  border: 1px solid var(--line);
}

input:focus {
  outline: 3px solid rgba(196, 80, 50, 0.22);
  border-color: var(--accent);
}

.checkbox-label {
  display: flex;
  align-items: center;
  min-height: 44px;
  color: var(--ink);
  cursor: pointer;
}

.checkbox-label input {
  width: 18px;
  height: 18px;
  accent-color: var(--teal);
}

.form-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-top: 24px;
}

.button {
  min-height: 42px;
  padding: 10px 16px;
  border: 1px solid transparent;
  font-family: Arial, sans-serif;
  font-size: 0.82rem;
  font-weight: 700;
  cursor: pointer;
}

.button:disabled {
  cursor: wait;
  opacity: 0.6;
}

.button-primary {
  color: #fff;
  background: var(--accent);
}

.button-primary:hover {
  background: var(--accent-dark);
}

.button-secondary {
  color: var(--teal);
  background: transparent;
  border-color: var(--teal);
}

.button-secondary:hover {
  color: #fff;
  background: var(--teal);
}

.message {
  min-height: 1.3em;
  margin: 18px 0 0;
  font-family: Arial, sans-serif;
  font-size: 0.9rem;
}

.message.success {
  color: var(--success);
}

.message.error {
  color: var(--danger);
}

.list-heading {
  align-items: center;
}

table {
  width: 100%;
  border-collapse: collapse;
  font-family: Arial, sans-serif;
}

th,
td {
  padding: 15px 12px;
  text-align: left;
  border-bottom: 1px solid var(--line);
}

th {
  color: var(--muted);
  font-size: 0.72rem;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

td {
  font-size: 0.92rem;
}

.status {
  display: inline-block;
  padding: 5px 8px;
  color: var(--success);
  background: #e2f0e5;
  font-size: 0.76rem;
  font-weight: 700;
}

.status.unavailable {
  color: var(--danger);
  background: #f7e4df;
}

.actions-column {
  width: 170px;
}

.row-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.table-action {
  padding: 5px 0;
  color: var(--teal);
  background: transparent;
  border: 0;
  font-weight: 700;
  cursor: pointer;
}

.table-action.delete {
  color: var(--danger);
}

.empty-state {
  margin: 24px 0 0;
  color: var(--muted);
  font-style: italic;
}

@media (max-width: 760px) {
  .container {
    width: min(100% - 24px, 620px);
  }

  .header-content {
    padding: 40px 0 34px;
  }

  .form-grid {
    grid-template-columns: 1fr;
  }

  .section-heading,
  .list-heading {
    align-items: flex-start;
    flex-direction: column;
  }

  .table-wrapper {
    overflow-x: auto;
  }

  table {
    min-width: 680px;
  }
}
```

### O que observar no CSS

- As variáveis em `:root` concentram as cores e facilitam alterações futuras.
- O `grid` organiza o formulário em colunas no computador e em uma coluna no celular.
- `overflow-x: auto` permite consultar a tabela em telas estreitas sem quebrar o layout.
- `clamp()` ajusta o tamanho do título entre limites definidos, sem depender apenas da largura da tela.
- Os estados de sucesso e erro usam classes diferentes para que a mensagem seja compreendida rapidamente.

### Commit da etapa 5

Envie os estilos da página:

No VS Code, selecione `frontend/styles.css` em **Source Control**, clique em `+`, informe `style: adiciona layout do frontend`, clique em **Commit** e depois em **Sync Changes**.

---

## 6. Implemente a comunicação com a API

Abra `frontend/app.js` e adicione:

```javascript
const URL_API = "http://127.0.0.1:8000";

const formularioLivro = document.querySelector("#book-form");
const entradaIdLivro = document.querySelector("#book-id");
const entradaTitulo = document.querySelector("#title");
const entradaAutor = document.querySelector("#author");
const entradaAnoPublicacao = document.querySelector("#publication-year");
const entradaDisponivel = document.querySelector("#available");
const botaoEnviar = document.querySelector("#submit-button");
const botaoCancelar = document.querySelector("#cancel-button");
const modoFormulario = document.querySelector("#form-mode");
const mensagemFormulario = document.querySelector("#form-message");
const mensagemLista = document.querySelector("#list-message");
const corpoTabela = document.querySelector("#books-table-body");
const estadoVazio = document.querySelector("#empty-state");
const botaoAtualizar = document.querySelector("#refresh-button");

function mostrarMensagem(elemento, mensagem, tipo = "") {
  elemento.textContent = mensagem;
  elemento.className = `message ${tipo}`.trim();
}

function definirCarregamentoFormulario(estaCarregando) {
  botaoEnviar.disabled = estaCarregando;
  botaoCancelar.disabled = estaCarregando;
  botaoEnviar.textContent = estaCarregando ? "Salvando..." : "Salvar livro";
}

function limparFormulario() {
  formularioLivro.reset();
  entradaIdLivro.value = "";
  entradaDisponivel.checked = true;
  modoFormulario.textContent = "Novo registro";
  botaoCancelar.hidden = true;
  mostrarMensagem(mensagemFormulario);
}

function iniciarEdicao(livro) {
  entradaIdLivro.value = livro.id;
  entradaTitulo.value = livro.titulo;
  entradaAutor.value = livro.autor;
  entradaAnoPublicacao.value = livro.ano_publicacao;
  entradaDisponivel.checked = livro.disponivel;
  modoFormulario.textContent = `Editando livro #${livro.id}`;
  botaoCancelar.hidden = false;
  entradaTitulo.focus();
}

function criarLinhaLivro(livro) {
  const linha = document.createElement("tr");
  const textoDisponibilidade = livro.disponivel ? "Disponível" : "Indisponível";
  const classeDisponibilidade = livro.disponivel ? "" : " unavailable";

  linha.innerHTML = `
    <td>${escaparHtml(livro.titulo)}</td>
    <td>${escaparHtml(livro.autor)}</td>
    <td>${livro.ano_publicacao}</td>
    <td><span class="status${classeDisponibilidade}">${textoDisponibilidade}</span></td>
    <td>
      <div class="row-actions">
        <button class="table-action edit" type="button" data-action="edit">Editar</button>
        <button class="table-action delete" type="button" data-action="delete">Excluir</button>
      </div>
    </td>
  `;

  linha.querySelector('[data-action="edit"]').addEventListener("click", () => {
    iniciarEdicao(livro);
  });

  linha.querySelector('[data-action="delete"]').addEventListener("click", () => {
    excluirLivro(livro.id, livro.titulo);
  });

  return linha;
}

function escaparHtml(valor) {
  return String(valor)
    .replaceAll("&", "&amp;")
    .replaceAll("<", "&lt;")
    .replaceAll(">", "&gt;")
    .replaceAll('"', "&quot;")
    .replaceAll("'", "&#039;");
}

async function lerErro(resposta) {
  try {
    const dadosErro = await resposta.json();

    if (Array.isArray(dadosErro.detail)) {
      return dadosErro.detail.map((dado) => dado.msg).join("; ");
    }

    return dadosErro.detail || "Não foi possível concluir a operação.";
  } catch {
    return "Não foi possível concluir a operação.";
  }
}

async function carregarLivros() {
  mostrarMensagem(mensagemLista, "Carregando livros...");
  botaoAtualizar.disabled = true;

  try {
    const resposta = await fetch(`${URL_API}/livros`);

    if (!resposta.ok) {
      throw new Error(await lerErro(resposta));
    }

    const livros = await resposta.json();
    corpoTabela.replaceChildren();

    livros.forEach((livro) => {
      corpoTabela.appendChild(criarLinhaLivro(livro));
    });

    estadoVazio.hidden = livros.length > 0;
    mostrarMensagem(mensagemLista, `${livros.length} livro(s) encontrado(s).`, "success");
  } catch (erro) {
    estadoVazio.hidden = true;
    mostrarMensagem(mensagemLista, `Erro ao carregar livros: ${erro.message}`, "error");
  } finally {
    botaoAtualizar.disabled = false;
  }
}

async function salvarLivro(evento) {
  evento.preventDefault();
  definirCarregamentoFormulario(true);
  mostrarMensagem(mensagemFormulario, "Salvando...");

  const idLivro = entradaIdLivro.value;
  const metodo = idLivro ? "PUT" : "POST";
  const endereco = idLivro ? `${URL_API}/livros/${idLivro}` : `${URL_API}/livros`;
  const dadosLivro = {
    titulo: entradaTitulo.value.trim(),
    autor: entradaAutor.value.trim(),
    ano_publicacao: Number(entradaAnoPublicacao.value),
    disponivel: entradaDisponivel.checked,
  };

  try {
    const resposta = await fetch(endereco, {
      method: metodo,
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(dadosLivro),
    });

    if (!resposta.ok) {
      throw new Error(await lerErro(resposta));
    }

    mostrarMensagem(
      mensagemFormulario,
      idLivro ? "Livro atualizado com sucesso." : "Livro cadastrado com sucesso.",
      "success",
    );
    limparFormulario();
    await carregarLivros();
  } catch (erro) {
    mostrarMensagem(mensagemFormulario, `Erro ao salvar livro: ${erro.message}`, "error");
  } finally {
    definirCarregamentoFormulario(false);
  }
}

async function excluirLivro(idLivro, titulo) {
  const confirmou = window.confirm(`Deseja excluir o livro "${titulo}"?`);

  if (!confirmou) {
    return;
  }

  try {
    const resposta = await fetch(`${URL_API}/livros/${idLivro}`, {
      method: "DELETE",
    });

    if (!resposta.ok) {
      throw new Error(await lerErro(resposta));
    }

    if (entradaIdLivro.value === String(idLivro)) {
      limparFormulario();
    }

    mostrarMensagem(mensagemLista, "Livro excluído com sucesso.", "success");
    await carregarLivros();
  } catch (erro) {
    mostrarMensagem(mensagemLista, `Erro ao excluir livro: ${erro.message}`, "error");
  }
}

formularioLivro.addEventListener("submit", salvarLivro);
botaoCancelar.addEventListener("click", limparFormulario);
botaoAtualizar.addEventListener("click", carregarLivros);

carregarLivros();
```

### Commit da etapa 6

Envie o JavaScript responsável pela comunicação com a API:

No VS Code, selecione `frontend/app.js` em **Source Control**, clique em `+`, informe `feat: conecta frontend ao crud de livros`, clique em **Commit** e depois em **Sync Changes**.

---

## 7. Entenda o JavaScript

### 7.1 Referência aos elementos

As chamadas `document.querySelector()` localizam elementos do HTML. Por exemplo:

```javascript
const entradaTitulo = document.querySelector("#title");
```

A partir desse momento, `entradaTitulo` representa o campo de título e permite ler ou alterar seu valor.

### 7.2 Carregamento da lista

A função `carregarLivros()` executa:

```javascript
const resposta = await fetch(`${URL_API}/livros`);
```

O `fetch` envia uma requisição `GET`. O `await` aguarda a resposta antes de continuar. Como a rede pode falhar, a chamada está dentro de `try...catch`.

Depois da resposta, o código verifica `resposta.ok`. Códigos HTTP como `404` e `500` não fazem o `fetch` lançar erro automaticamente; por isso essa verificação é necessária.

### 7.3 Cadastro e atualização

A função `salvarLivro()` identifica o modo pelo campo oculto:

- sem `book-id`: usa `POST /livros`;
- com `book-id`: usa `PUT /livros/{id}`.

O objeto `dadosLivro` é transformado em JSON por `JSON.stringify()` e enviado no corpo da requisição. O cabeçalho `Content-Type` informa à API que o corpo está no formato JSON.

### 7.4 Exclusão

A função `excluirLivro()` pede confirmação antes de chamar `DELETE`. Essa confirmação reduz o risco de uma exclusão acidental.

Depois da exclusão, `carregarLivros()` é executada novamente para que a tabela reflita o estado atual do banco.

### 7.5 Segurança ao inserir texto na tabela

A função `escapeHtml()` transforma caracteres especiais em entidades HTML antes de inserir título e autor na tabela. Isso evita que um texto enviado por usuário seja interpretado como HTML ou JavaScript pelo navegador.

Mesmo com essa proteção no Front End, a API também deve validar e tratar os dados recebidos.

### Commit da etapa 7

Registre a conclusão da explicação e da revisão do JavaScript:

No VS Code, selecione `frontend/app.js` em **Source Control**, clique em `+`, informe `docs: explica funcionamento do frontend`, clique em **Commit** e depois em **Sync Changes**.

---

## 8. Execute o Front End

Não abra `index.html` diretamente com duplo clique. Nesse caso, o navegador pode usar a origem `file://`, que não corresponde às origens autorizadas no CORS.

Com a API em execução no primeiro terminal, abra outro terminal na raiz do projeto e execute:

```bat
python -m http.server 5500 --directory frontend
```

A mensagem deverá indicar que o servidor está disponível na porta `5500`. Abra:

```text
http://127.0.0.1:5500
```

O fluxo esperado é:

1. A página carrega e chama `GET /livros` automaticamente.
2. O formulário envia `POST /livros` ao cadastrar.
3. O botão **Editar** preenche o formulário com os dados selecionados.
4. O formulário envia `PUT /livros/{id}` ao salvar uma edição.
5. O botão **Excluir** envia `DELETE /livros/{id}` após a confirmação.
6. A tabela é atualizada após cada alteração.

Mantenha os dois servidores ativos:

| Terminal | Comando | Porta |
| --- | --- | --- |
| API | `uvicorn app.main:app --reload` | `8000` |
| Front End | `python -m http.server 5500 --directory frontend` | `5500` |

### Commit da etapa 8

Depois de confirmar que a página abre pelo servidor local, registre a execução do Front End:

Como esta etapa apenas valida a execução, use **Source Control > ... > Commit > Commit Empty**, informe `test: executa frontend localmente` e clique em **Sync Changes**.

---

## 9. Teste completo do CRUD

Faça o teste nesta ordem:

### 9.1 Criar

1. Preencha título, autor e ano.
2. Marque ou desmarque a disponibilidade.
3. Clique em **Salvar livro**.
4. Verifique se a nova linha aparece na tabela.
5. Confirme no Swagger em `http://127.0.0.1:8000/docs` ou diretamente no MySQL.

### 9.2 Listar

1. Atualize a página.
2. Confirme que os livros continuam visíveis.
3. Clique em **Atualizar lista**.
4. Observe a mensagem com a quantidade encontrada.

### 9.3 Atualizar

1. Clique em **Editar** em uma linha.
2. Confirme que o formulário foi preenchido.
3. Altere um ou mais campos.
4. Clique em **Salvar livro**.
5. Verifique a mensagem de sucesso e a linha atualizada.

### 9.4 Excluir

1. Clique em **Excluir**.
2. Confirme a operação.
3. Verifique se a linha desapareceu.
4. Atualize a página para confirmar que o registro não voltou.

### 9.5 Testar erros

Também teste situações inválidas:

- envie o formulário sem título;
- informe um ano fora do intervalo;
- desligue o MySQL e tente carregar a lista;
- pare a API e tente cadastrar um livro;
- tente editar ou excluir um `id` que não existe.

A interface deve apresentar uma mensagem compreensível. O console do navegador, aberto com `F12`, e o terminal da API ajudam a localizar a origem de cada falha.

### Commit da etapa 9

Depois de testar cadastro, listagem, edição, exclusão e erros, registre a validação do CRUD:

Depois de testar o CRUD, abra **Source Control > ... > Commit > Commit Empty**, informe `test: valida crud pelo frontend` e clique em **Sync Changes**.

---

## 10. Diagnóstico de problemas

### Erro de CORS no console

Confirme:

1. se a API possui o `CORSMiddleware`;
2. se a origem usada no navegador está na lista `allow_origins`;
3. se você acessou `http://127.0.0.1:5500` ou `http://localhost:5500`, e não o arquivo com `file://`;
4. se a API foi reiniciada depois da alteração.

`localhost` e `127.0.0.1` são endereços diferentes para fins de origem. Autorize o endereço que realmente aparece na barra do navegador.

### Erro `Failed to fetch`

Esse erro normalmente indica que a API não está acessível. Confira se o servidor FastAPI está rodando na porta `8000` e se `URL_API` no `app.js` corresponde ao endereço correto.

### Erro `404 Not Found`

A rota usada pelo Front End não existe ou possui outro nome. Compare as URLs do contrato com as rotas exibidas em `http://127.0.0.1:8000/docs`.

### Erro `422 Unprocessable Entity`

A API recebeu dados que não passaram na validação. Confira os nomes dos campos, principalmente `ano_publicacao` e `disponivel`, e observe a mensagem retornada pela API.

### A tabela fica vazia sem mensagem

Abra as ferramentas de desenvolvedor do navegador, selecione a aba **Network** e examine a requisição `GET /livros`. Confira o código HTTP e o corpo da resposta.

---

## 11. Boas práticas para evoluir o projeto

- Mantenha a URL da API em uma única constante, como `URL_API`.
- Não coloque credenciais do MySQL no Front End.
- Valide os dados no navegador e novamente na API.
- Use códigos HTTP adequados nas respostas da API.
- Mostre mensagens que ajudem o usuário sem expor detalhes internos do servidor.
- Separe arquivos por responsabilidade.
- Em produção, sirva a aplicação com HTTPS e configure as origens reais do Front End.
- Antes de publicar, remova mensagens excessivamente detalhadas que possam expor informações internas.

---

## 12. Checklist final das quatro partes

- [ ] O ambiente Python e o MySQL foram preparados.
- [ ] A API consegue conectar-se ao banco.
- [ ] O modelo e os schemas de livros foram criados.
- [ ] `POST` e `GET` foram implementados e testados.
- [ ] `PUT` e `DELETE` foram implementados e testados.
- [ ] O CORS foi configurado para o endereço do Front End.
- [ ] `index.html`, `styles.css` e `app.js` foram criados.
- [ ] O Front End consegue listar livros.
- [ ] O Front End consegue cadastrar livros.
- [ ] O Front End consegue editar livros.
- [ ] O Front End consegue excluir livros.
- [ ] Os erros de comunicação e validação foram testados.

Com isso, a API de livros possui um Front End funcional em HTML, CSS e JavaScript consumindo o CRUD implementado no FastAPI.
