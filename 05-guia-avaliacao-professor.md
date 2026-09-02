# Guia do Professor: Avaliação da API de Livros

## Conferência das entregas nas quatro etapas

Este guia orienta a conferência das entregas dos alunos durante a construção da API de livros com FastAPI, MySQL, XAMPP, phpMyAdmin, HTML, CSS e JavaScript.

A avaliação deve considerar dois aspectos:

1. **Processo:** o aluno desenvolveu a etapa durante a aula e registrou o commit no GitHub na data correta.
2. **Produto:** o código, o banco e os testes correspondem ao que foi solicitado na etapa.

> **ATENÇÃO:** não serão aceitas entregas antecipadas. Antes de avaliar o conteúdo, confira a data e o horário dos commits no histórico do repositório do aluno no GitHub. O horário exibido pelo GitHub deve ser analisado considerando o fuso horário apresentado na página.

> **Regra de acompanhamento:** cada etapa deve ser feita em aula, uma por vez. O aluno não deve realizar todas as etapas antecipadamente. A avaliação final deve considerar o histórico completo de commits, e não apenas o estado atual dos arquivos.

---

## 1. Materiais necessários ao professor

Antes de iniciar as avaliações, tenha disponível:

- sua conta do GitHub;
- a lista de alunos e suas turmas;
- as datas oficiais de cada etapa;
- o VS Code;
- o Python instalado;
- o XAMPP com Apache e MySQL;
- o phpMyAdmin;
- um navegador web;
- os quatro guias dos alunos;
- uma planilha ou ficha para registrar os resultados.

Recomenda-se avaliar cada aluno a partir do próprio repositório GitHub. Não substitua o repositório do aluno por um arquivo enviado por outro meio, pois o histórico de commits é parte da atividade.

---

## 2. Calendário oficial

| Etapa | Conteúdo principal | Turma 3F | Turma 3C |
| --- | --- | --- | --- |
| 1 | Ambiente, repositório, MySQL e conexão | 02/09/2026 | 03/09/2026 |
| 2 | Modelo, schemas e rotas `POST` e `GET` | 09/09/2026 | 10/09/2026 |
| 3 | Rotas `PUT` e `DELETE` e CRUD completo | 16/09/2026 | 17/09/2026 |
| 4 | Front End com HTML, CSS e JavaScript | 23/09/2026 | 24/09/2026 |

A entrega de cada etapa deve ser conferida na data correspondente à turma do aluno. Um commit realizado antes dessa data deve ser marcado como **entrega antecipada**.

---

## 3. Procedimento geral para cada avaliação

Repita os passos abaixo para cada aluno e para cada etapa.

### Passo 1: identifique o aluno e a turma

1. Confirme o nome do aluno.
2. Confirme se ele pertence à turma 3F ou 3C.
3. Anote a data oficial de entrega correspondente.
4. Abra o endereço do repositório individual do aluno.
5. Confirme se o repositório pertence ao aluno e se contém o projeto `api-livros`.

### Passo 2: confira o histórico no GitHub

1. Na página principal do repositório, clique no link que mostra a quantidade de commits ou acesse a área **History** da branch principal.
2. Localize o commit correspondente à etapa.
3. Abra os detalhes do commit.
4. Confira a mensagem, os arquivos alterados, a data e o horário.
5. Compare a data exibida com a data oficial da turma.
6. Confira se o commit está na branch principal usada pelo aluno, normalmente `main`.
7. Verifique se a etapa não foi enviada apenas em um commit posterior.

O GitHub permite observar a sequência das alterações. A existência do arquivo no estado atual não prova que ele foi entregue na data correta; por isso, a conferência deve ser feita no histórico.

### Passo 3: confira a mensagem e o conteúdo do commit

Compare a mensagem do aluno com o objetivo da etapa. Mensagens sugeridas pelos guias:

| Etapa | Mensagem ou mensagens esperadas |
| --- | --- |
| 1 | `chore: inicia repositorio da api de livros`, `feat: configura conexao com mysql`, `feat: cria aplicacao fastapi e rota de saude` |
| 2 | `feat: cria modelo de livros`, `feat: adiciona schemas de livros`, `feat: cria rota para cadastrar livros`, `feat: cria rota para listar livros` |
| 3 | `feat: cria rota para atualizar livros`, `feat: cria rota para excluir livros`, `test: valida crud completo pelo swagger` |
| 4 | `feat: cria pagina de livros`, `style: adiciona layout do frontend`, `feat: conecta frontend ao crud de livros`, `test: valida crud pelo frontend` |

As mensagens podem variar desde que o conteúdo e a data comprovem a etapa. O professor deve avaliar o trabalho realizado, não somente a frase usada no commit.

### Passo 4: clone o projeto pelo VS Code

A conferência prática deve ser feita a partir do repositório, e não de uma pasta antiga da máquina do aluno:

1. Abra o VS Code.
2. Pressione `Ctrl+Shift+P`.
3. Execute **Git: Clone**.
4. Informe a URL do repositório do aluno.
5. Escolha uma pasta temporária para o clone.
6. Clique em **Open**.
7. Abra o **Explorer** e confira os arquivos recebidos.

Não é necessário utilizar comandos Git no terminal. O clone deve ser realizado pela interface do VS Code.

### Passo 5: reconstrua o ambiente

No terminal integrado do VS Code, utilize somente os comandos de Python necessários:

```bat
python -m venv .venv
.venv\Scripts\activate.bat
pip install -r requirements.txt
```

O arquivo `.env` não deve estar no clone. Para a avaliação local, crie-o na raiz do projeto com os dados do MySQL do XAMPP:

```dotenv
DB_USER=root
DB_PASSWORD=senha_local_do_root
DB_HOST=localhost
DB_PORT=3306
DB_NAME=biblioteca_db
```

Nunca registre a senha real na ficha de avaliação ou no repositório.

### Passo 6: reconstrua o banco

1. Abra o painel do XAMPP.
2. Inicie **Apache** e **MySQL**.
3. Abra `http://localhost/phpmyadmin`.
4. Crie o banco `biblioteca_db`, caso ele não exista.
5. Selecione o banco.
6. Abra a aba **Importar**.
7. Selecione `database/biblioteca_db.sql` do clone.
8. Mantenha o formato SQL.
9. Clique em **Importar**.

Se a avaliação ocorrer antes de o aluno gerar dados de teste, o arquivo SQL poderá conter apenas a estrutura do banco. O importante é que ele acompanhe a etapa correspondente e seja importável.

### Passo 7: registre o resultado

Use uma ficha com os campos abaixo:

| Campo | Registro |
| --- | --- |
| Aluno | Nome completo |
| Turma | 3F ou 3C |
| Etapa | 1, 2, 3 ou 4 |
| Data oficial | Data da turma |
| Data do commit | Data exibida no GitHub |
| Entrega antecipada | Sim ou não |
| Código conferido | Sim ou não |
| Testes realizados | Descrição breve |
| Resultado | Aprovada, pendente ou não aceita |
| Observações | Correções necessárias |

---

## 4. Avaliação da Etapa 1

### Data esperada

- Turma 3F: **02/09/2026**;
- Turma 3C: **03/09/2026**.

### O que deve existir no repositório

Confira se o aluno enviou:

- `README.md`;
- `.gitignore`;
- `requirements.txt`;
- `.env` **não deve** estar no GitHub;
- `database/biblioteca_db.sql`;
- `app/__init__.py`;
- `app/database.py`;
- `app/main.py`.

### Conferência do processo

1. Confirme o commit inicial do repositório.
2. Confira o commit da configuração do projeto na data correta.
3. Verifique se o arquivo `.gitignore` foi enviado.
4. Confirme que o arquivo `.env` não aparece no repositório.
5. Verifique se `database/biblioteca_db.sql` foi enviado ao GitHub.
6. Confirme que a etapa não foi concluída antes da data da turma.

### Conferência do `.gitignore`

Abra o arquivo no GitHub e confirme a presença de:

```gitignore
.venv/
__pycache__/
*.pyc
.env
```

O professor deve verificar especialmente se `.env` não foi publicado. Se a senha do `root` aparecer no GitHub, oriente o aluno a alterar a senha imediatamente e remover o segredo do histórico conforme o procedimento da instituição.

### Conferência do banco

No phpMyAdmin:

1. Importe `database/biblioteca_db.sql`.
2. Confirme que a importação termina sem erro.
3. Abra o banco `biblioteca_db`.
4. Verifique se as tabelas presentes correspondem ao estado da etapa.

### Conferência da aplicação

No terminal integrado do VS Code, inicie:

```bat
uvicorn app.main:app --reload
```

Abra:

- `http://127.0.0.1:8000/health`;
- `http://127.0.0.1:8000/docs`.

A rota `/health` deve retornar:

```json
{
  "status": "ok",
  "database": "connected"
}
```

### Critério mínimo da Etapa 1

A etapa pode ser considerada aprovada quando:

- o commit está na data correta;
- o repositório foi publicado;
- o `.gitignore` está correto;
- o arquivo SQL está disponível;
- a conexão com o banco funciona;
- `/health` retorna `database: connected`.

---

## 5. Avaliação da Etapa 2

### Data esperada

- Turma 3F: **09/09/2026**;
- Turma 3C: **10/09/2026**.

### Preparação

1. Confira no histórico se o aluno iniciou a etapa com um novo clone ou se o repositório contém o estado da Etapa 1.
2. Clone novamente pelo VS Code.
3. Reinstale as dependências.
4. Recrie o `.env` local.
5. Importe `database/biblioteca_db.sql` pelo phpMyAdmin.

### Arquivos esperados

- `app/models.py`;
- `app/schemas.py`;
- `app/database.py` atualizado;
- `app/main.py` com as rotas da Parte 2;
- `database/biblioteca_db.sql` atualizado ao final da etapa.

### Conferência do modelo

Abra `app/models.py` e confirme:

- existe a classe `Livro`;
- a tabela se chama `livros`;
- existe um `id` como chave primária;
- existem `titulo`, `autor`, `ano_publicacao` e `disponivel`;
- os campos obrigatórios usam `nullable=False`.

### Conferência dos schemas

Abra `app/schemas.py` e confirme:

- existe `LivroCriacao`;
- existe `LivroResposta`;
- o ano aceita somente o intervalo definido;
- título e autor não podem ser vazios;
- `from_attributes = True` está configurado na resposta.

### Testes práticos

Inicie a API e abra `/docs`.

1. Execute `POST /livros` com um livro válido.
2. Confirme o status `201`.
3. Execute `GET /livros`.
4. Confirme que o livro aparece na lista.
5. Execute `GET /livros/{id_livro}`.
6. Confirme os dados do livro.
7. Consulte um identificador inexistente e confirme o status `404`.
8. Envie um título vazio ou ano inválido e confirme o status `422`.

### Conferência das funções e nomes

O código deve priorizar nomes em português e instruções simples, como:

- `criar_livro`;
- `listar_livros`;
- `obter_livro`;
- `dados_livro`;
- `sessao_banco`;
- `consulta`;
- `resultado`;
- `livros`.

Não devem ser exigidos `lambda`, funções anônimas ou soluções avançadas que escondam a lógica da rota.

### Critério mínimo da Etapa 2

A etapa pode ser considerada aprovada quando:

- a entrega foi registrada na data correta;
- `Livro` representa a tabela;
- os schemas validam os dados;
- `POST` cria livros;
- `GET` lista e consulta livros;
- os erros `404` e `422` foram demonstrados;
- o banco foi exportado novamente para o arquivo SQL.

---

## 6. Avaliação da Etapa 3

### Data esperada

- Turma 3F: **16/09/2026**;
- Turma 3C: **17/09/2026**.

### Preparação

1. Confira o histórico da Etapa 2.
2. Clone novamente o projeto pelo VS Code.
3. Reinstale as dependências.
4. Recrie o `.env` com o usuário `root`.
5. Importe o SQL pelo phpMyAdmin.

### Arquivos esperados

- `app/models.py` com `Livro`;
- `app/schemas.py` com `LivroCriacao` e `LivroResposta`;
- `app/database.py` com a sessão;
- `app/main.py` com as cinco operações;
- `database/biblioteca_db.sql` atualizado.

### Conferência da rota `PUT`

Confirme se a rota:

```text
PUT /livros/{id_livro}
```

1. Localiza o livro pelo identificador.
2. Retorna `404` quando o livro não existe.
3. Recebe os dados completos do livro.
4. Valida os dados com `LivroCriacao`.
5. Atualiza cada campo explicitamente.
6. Executa `commit()`.
7. Retorna o livro atualizado.

### Conferência da rota `DELETE`

Confirme se a rota:

```text
DELETE /livros/{id_livro}
```

1. Localiza o livro pelo identificador.
2. Retorna `404` quando o livro não existe.
3. Executa `delete(livro)`.
4. Executa `commit()`.
5. Retorna uma mensagem de sucesso.

### Teste prático do CRUD

1. Crie um livro com `POST`.
2. Consulte-o com `GET`.
3. Atualize um campo com `PUT`.
4. Consulte novamente e confirme a alteração.
5. Liste os livros com `GET`.
6. Exclua o livro com `DELETE`.
7. Consulte o mesmo identificador e confirme `404`.
8. Teste `PUT` e `DELETE` com identificador inexistente.
9. Teste dados inválidos e confirme `422`.

### Critério mínimo da Etapa 3

A etapa pode ser considerada aprovada quando:

- o commit está na data correta;
- `PUT` atualiza corretamente;
- `DELETE` exclui corretamente;
- erros `404` e `422` funcionam;
- o CRUD completo é demonstrado no Swagger;
- o arquivo SQL atualizado está no GitHub.

---

## 7. Avaliação da Etapa 4

### Data esperada

- Turma 3F: **23/09/2026**;
- Turma 3C: **24/09/2026**.

### Preparação

1. Confira no GitHub os commits da Etapa 3.
2. Clone novamente o projeto pelo VS Code.
3. Reinstale as dependências.
4. Recrie o `.env` local.
5. Importe `database/biblioteca_db.sql` no phpMyAdmin.
6. Inicie a API FastAPI.
7. Inicie o servidor do Front End pelo terminal integrado:

```bat
python -m http.server 5500 --directory frontend
```

### Arquivos esperados

- `frontend/index.html`;
- `frontend/styles.css`;
- `frontend/app.js`;
- `app/main.py` com CORS configurado;
- arquivo SQL atualizado;
- nenhum `.env` publicado.

### Conferência do HTML

Abra `frontend/index.html` e confirme:

- existe um formulário de cadastro;
- existem campos para título, autor, ano e disponibilidade;
- existe uma tabela para os livros;
- existe uma área de mensagens;
- o arquivo `styles.css` está vinculado;
- o arquivo `app.js` está vinculado com `defer`.

### Conferência do CSS

Abra `frontend/styles.css` e confirme:

- a página possui layout organizado;
- o formulário e a tabela são legíveis;
- os botões possuem estados visuais;
- a tabela pode ser consultada em tela pequena;
- não há dependência de um framework não solicitado.

### Conferência do JavaScript

Abra `frontend/app.js` e confirme:

- a URL da API está definida em uma única constante;
- `GET` carrega a lista;
- `POST` cadastra um livro;
- `PUT` atualiza um livro;
- `DELETE` exclui um livro;
- as mensagens de erro são exibidas;
- o formulário pode entrar e sair do modo de edição;
- a lista é atualizada após cada alteração.

Os nomes das variáveis e funções próprias devem permanecer preferencialmente em português, por exemplo `carregarLivros`, `salvarLivro`, `excluirLivro`, `resposta`, `dadosLivro` e `mensagemLista`.

### Teste prático pelo navegador

Abra `http://127.0.0.1:5500` e execute:

1. Atualize a página e confirme que os livros são carregados.
2. Cadastre um livro.
3. Confirme a nova linha na tabela.
4. Edite o livro.
5. Confirme a alteração na tabela e no Swagger.
6. Exclua o livro.
7. Confirme que ele não aparece após atualizar a lista.
8. Pare a API e confirme que o Front End apresenta uma mensagem de erro.
9. Confira o console do navegador para identificar erros de CORS ou de JavaScript.

### Conferência do CORS

Abra `app/main.py` e confirme que as origens usadas no teste estão autorizadas:

- `http://127.0.0.1:5500`;
- `http://localhost:5500`.

Não considere a configuração `allow_origins=["*"]` como suficiente para uma aplicação publicada sem discutir o risco com o aluno.

### Critério mínimo da Etapa 4

A etapa pode ser considerada aprovada quando:

- o commit está na data correta;
- o CORS permite a comunicação;
- a página carrega os livros;
- o cadastro funciona;
- a edição funciona;
- a exclusão funciona;
- os erros são apresentados;
- os arquivos HTML, CSS e JavaScript estão no GitHub.

---

## 8. Conferência das datas e entregas antecipadas

Use este procedimento antes de aprovar qualquer etapa:

1. Abra o histórico de commits do repositório.
2. Localize o commit mais antigo que contém a implementação da etapa.
3. Abra o commit e confira a data completa.
4. Compare com o calendário da turma.
5. Verifique também commits anteriores que possam conter a mesma implementação.
6. Se a implementação já existia antes da data oficial, registre **entrega antecipada**.
7. Não aprove a etapa como entregue na data correta quando o histórico mostrar que ela foi concluída antes.

O professor deve registrar a decisão e a justificativa na ficha de avaliação. O estado atual do projeto não substitui a análise do histórico.

---

## 9. Avaliação final do projeto

Depois da Etapa 4, faça uma avaliação integrada:

- [ ] As quatro etapas foram entregues nas datas da turma.
- [ ] Não houve entrega antecipada.
- [ ] O histórico de commits mostra evolução por etapa.
- [ ] O repositório pertence ao aluno.
- [ ] O `.env` não foi publicado.
- [ ] O arquivo SQL está no repositório.
- [ ] O banco pode ser reconstruído pelo phpMyAdmin.
- [ ] `POST`, `GET`, `PUT` e `DELETE` funcionam.
- [ ] A validação dos dados funciona.
- [ ] Os erros `404` e `422` foram tratados.
- [ ] O Front End consome a API.
- [ ] Cadastro, listagem, edição e exclusão funcionam pelo navegador.
- [ ] O código utiliza nomes próprios preferencialmente em português.
- [ ] As rotas permanecem simples e didáticas.

### Registro do resultado final

Classifique o projeto usando uma das opções definidas pela instituição:

- **Aprovado:** atende aos requisitos e às datas;
- **Aprovado com pendências:** funciona parcialmente e possui correções identificadas;
- **Não aceito:** houve entrega antecipada, ausência do repositório, falta de etapas ou funcionamento incompatível com o solicitado.

Registre sempre a justificativa. A avaliação deve ser baseada no histórico do GitHub, no código disponibilizado e na demonstração prática do sistema.
