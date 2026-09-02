<div align="center">

# 📚 API de Livros

### 🧑‍💻 SW-II · Sistemas Web II

**Do banco de dados ao Front End, em quatro etapas práticas.**

[![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![MySQL](https://img.shields.io/badge/MySQL-4479A1?logo=mysql&logoColor=white)](https://www.mysql.com/)
[![XAMPP](https://img.shields.io/badge/XAMPP-FB7A24?logo=apache&logoColor=white)](https://www.apachefriends.org/)
[![HTML](https://img.shields.io/badge/HTML-E34F26?logo=html5&logoColor=white)](https://developer.mozilla.org/docs/Web/HTML)
[![CSS](https://img.shields.io/badge/CSS-1572B6?logo=css3&logoColor=white)](https://developer.mozilla.org/docs/Web/CSS)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=111111)](https://developer.mozilla.org/docs/Web/JavaScript)

</div>

> 🎓 **Atividade avaliativa do 3º Bimestre**  
> Esta atividade tem como finalidade o **fechamento da nota do 3º Bimestre da disciplina de SW-II (Sistemas Web II)**.

## 🎯 O desafio

Construir uma aplicação web para gerenciamento de livros, começando pela API e pelo banco de dados e chegando a uma interface completa no navegador.

Cada livro terá:

| Campo | Descrição |
| --- | --- |
| `id` | Identificador único |
| `titulo` | Título do livro |
| `autor` | Autor do livro |
| `ano_publicacao` | Ano de publicação |
| `disponivel` | Situação de disponibilidade |

## 🔁 CRUD completo

| Símbolo | Operação | Ação |
| --- | --- | --- |
| 🟢 | **Create** | Cadastrar livros |
| 🔵 | **Read** | Listar e consultar livros |
| 🟠 | **Update** | Atualizar livros |
| 🔴 | **Delete** | Excluir livros |

## 🚀 Objetivos

Ao concluir o projeto, o aluno deverá ser capaz de:

- criar rotas com FastAPI;
- utilizar `GET`, `POST`, `PUT` e `DELETE`;
- conectar Python a um banco MySQL;
- utilizar XAMPP e phpMyAdmin;
- criar modelos e schemas;
- validar dados e tratar erros HTTP;
- construir interfaces com HTML, CSS e JavaScript;
- consumir uma API usando `fetch`;
- utilizar o Source Control do VS Code;
- registrar a evolução do projeto no GitHub.

## 🧩 Jornada em 4 etapas

### 🟦 Etapa 1 · Fundação

Preparação do ambiente, dependências, banco `biblioteca_db`, conexão com MySQL e rota de saúde.

📖 [Abrir o Guia 1](01-fundacao-fastapi-mysql.md)

### 🟩 Etapa 2 · Modelo e consultas

Criação do modelo `Livro`, schemas, sessão do banco e rotas `POST` e `GET`.

📖 [Abrir o Guia 2](02-modelo-post-get.md)

### 🟧 Etapa 3 · CRUD completo

Implementação das rotas `PUT` e `DELETE`, tratamento de erros e testes do CRUD.

📖 [Abrir o Guia 3](03-put-delete-crud.md)

### 🟥 Etapa 4 · Front End

Construção da interface com HTML, CSS e JavaScript para cadastrar, listar, editar e excluir livros.

📖 [Abrir o Guia 4](04-frontend-html-css-js.md)

## 🛠️ Stack da atividade

`Python` · `FastAPI` · `Uvicorn` · `SQLAlchemy` · `PyMySQL` · `MySQL` · `XAMPP` · `phpMyAdmin` · `HTML` · `CSS` · `JavaScript` · `VS Code` · `GitHub`

> 🟧 **Banco de dados:** o arquivo `database/biblioteca_db.sql` será versionado no GitHub para permitir a reconstrução do banco entre as aulas. O banco será administrado exclusivamente pelo **phpMyAdmin** do XAMPP. Não será utilizado o MySQL Workbench.

## 📅 Calendário de entregas

A atividade deve ser realizada em aula, uma etapa por vez. Não faça as quatro etapas de uma única vez.

| Etapa | Turma 3F | Turma 3C | Entrega |
| --- | --- | --- | --- |
| 🟦 1 | 02/09/2026 | 03/09/2026 | Ambiente e conexão |
| 🟩 2 | 09/09/2026 | 10/09/2026 | Modelo e `POST`/`GET` |
| 🟧 3 | 16/09/2026 | 17/09/2026 | `PUT`/`DELETE` e CRUD |
| 🟥 4 | 23/09/2026 | 24/09/2026 | Interface Web |

> 🚨 **NÃO SERÃO ACEITAS ENTREGAS ANTECIPADAS.** As datas e os horários serão conferidos no histórico de commits do repositório GitHub do aluno antes da avaliação.

Ao final de cada etapa, o aluno deverá:

1. concluir somente o conteúdo trabalhado em aula;
2. conferir os arquivos no VS Code;
3. utilizar o painel **Source Control**;
4. criar o commit da etapa;
5. sincronizar as alterações com o GitHub.

## 🔄 Entre uma aula e outra

Como os computadores podem ser limpos ao serem desligados, o GitHub será a fonte oficial do projeto.

No início de cada nova etapa:

1. clone novamente o repositório pelo VS Code;
2. recrie o ambiente virtual;
3. reinstale as dependências usando `requirements.txt`;
4. recrie o `.env` local com o usuário `root`;
5. inicie Apache e MySQL pelo XAMPP;
6. importe `database/biblioteca_db.sql` pelo phpMyAdmin;
7. continue a partir do conteúdo enviado ao GitHub.

🔒 O arquivo `.env` não deve ser publicado, pois contém a senha local do MySQL. O arquivo SQL deve permanecer no repositório.

## 🧱 Padrão de código

O projeto utiliza exemplos simples e didáticos. Prefira nomes em português para classes, funções e variáveis próprias:

`Livro` · `LivroCriacao` · `LivroResposta` · `criar_livro` · `listar_livros` · `atualizar_livro` · `excluir_livro` · `resultado` · `consulta`

As rotas devem utilizar funções nomeadas com `def` e instruções claras. Não serão necessários `lambda`, funções anônimas ou abstrações avançadas que escondam a lógica.

## ✅ Avaliação

A avaliação considerará:

- funcionamento da API e do banco;
- implementação do CRUD;
- construção do Front End;
- organização e clareza do código;
- testes realizados;
- presença dos arquivos no GitHub;
- sequência e datas dos commits;
- realização das etapas durante as aulas.

## 🏁 Resultado esperado

Ao final das quatro etapas, o aluno deverá possuir uma aplicação de livros com API FastAPI, banco MySQL funcionando no XAMPP, banco administrado pelo phpMyAdmin, CRUD completo, validações, tratamento de erros e interface web funcional.

<div align="center">

**📚 Projeto de SW-II · 3º Bimestre**

</div>
