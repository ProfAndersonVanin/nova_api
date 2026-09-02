# API de Livros

## SW-II - Sistemas Web II

Este repositório apresenta a atividade prática de construção de uma aplicação web para gerenciamento de livros.

A proposta é desenvolver uma API utilizando **FastAPI**, com persistência de dados em **MySQL**, administrado pelo **XAMPP** e pelo **phpMyAdmin**. Ao final da atividade, a API será integrada a um Front End criado com **HTML, CSS e JavaScript**.

Esta atividade tem como finalidade o **fechamento da nota do 3º Bimestre da disciplina de SW-II (Sistemas Web II)**.

---

## O que será construído

Durante as quatro etapas, será desenvolvida uma aplicação capaz de cadastrar e gerenciar livros.

Cada livro terá, inicialmente, as seguintes informações:

- `id`: identificador do livro;
- `titulo`: título do livro;
- `autor`: autor do livro;
- `ano_publicacao`: ano de publicação;
- `disponivel`: situação de disponibilidade do livro.

A aplicação deverá executar as operações CRUD:

- **Create:** cadastrar livros;
- **Read:** listar e consultar livros;
- **Update:** atualizar livros;
- **Delete:** excluir livros.

---

## Objetivos da atividade

Ao concluir o projeto, o aluno deverá ser capaz de:

- compreender a estrutura básica de uma API Web;
- criar rotas com FastAPI;
- utilizar métodos HTTP como `GET`, `POST`, `PUT` e `DELETE`;
- conectar uma aplicação Python a um banco MySQL;
- criar e utilizar modelos e schemas;
- validar dados recebidos pela API;
- tratar respostas e erros HTTP;
- utilizar o XAMPP e o phpMyAdmin para administrar o banco;
- construir uma interface com HTML, CSS e JavaScript;
- consumir uma API usando JavaScript e `fetch`;
- utilizar o Source Control do VS Code;
- registrar a evolução do projeto em um repositório GitHub.

---

## Organização das etapas

### Etapa 1 - Preparação do ambiente

Nesta etapa serão preparados:

- o projeto no VS Code;
- o ambiente virtual Python;
- as dependências da aplicação;
- o banco `biblioteca_db`;
- a conexão entre FastAPI e MySQL;
- a rota de verificação da aplicação.

Guia: [01-fundacao-fastapi-mysql.md](01-fundacao-fastapi-mysql.md)

### Etapa 2 - Modelo e rotas de cadastro e consulta

Nesta etapa serão criados:

- o modelo `Livro`;
- os schemas `LivroCriacao` e `LivroResposta`;
- a sessão de acesso ao banco;
- a rota `POST /livros`;
- as rotas `GET /livros` e `GET /livros/{id_livro}`.

Guia: [02-modelo-post-get.md](02-modelo-post-get.md)

### Etapa 3 - Atualização, exclusão e CRUD completo

Nesta etapa serão criadas:

- a rota `PUT /livros/{id_livro}`;
- a rota `DELETE /livros/{id_livro}`;
- os tratamentos de erro;
- os testes do CRUD completo;
- a atualização do arquivo SQL do banco.

Guia: [03-put-delete-crud.md](03-put-delete-crud.md)

### Etapa 4 - Construção do Front End

Nesta etapa será desenvolvida uma interface com:

- HTML para a estrutura da página;
- CSS para o layout e a aparência;
- JavaScript para comunicação com a API;
- formulário de cadastro e edição;
- listagem dos livros;
- ações de atualização e exclusão.

Guia: [04-frontend-html-css-js.md](04-frontend-html-css-js.md)

---

## Ferramentas utilizadas

- Python;
- FastAPI;
- Uvicorn;
- SQLAlchemy;
- PyMySQL;
- MySQL;
- XAMPP;
- phpMyAdmin;
- HTML;
- CSS;
- JavaScript;
- Visual Studio Code;
- GitHub.

O banco será administrado exclusivamente pelo **phpMyAdmin**, utilizando o MySQL disponibilizado pelo XAMPP. Não será utilizado o MySQL Workbench.

---

## Organização do trabalho

Esta atividade deverá ser realizada em aula, uma etapa por vez.

Não faça as quatro etapas de uma única vez. Cada etapa deverá ser desenvolvida conforme a orientação do professor e entregue na data correspondente à turma.

| Etapa | Turma 3F | Turma 3C |
| --- | --- | --- |
| 1 | 02/09/2026 | 03/09/2026 |
| 2 | 09/09/2026 | 10/09/2026 |
| 3 | 16/09/2026 | 17/09/2026 |
| 4 | 23/09/2026 | 24/09/2026 |

> **ATENÇÃO:** não serão aceitas entregas antecipadas. As datas e os horários serão conferidos no histórico de commits do repositório GitHub do aluno antes da avaliação.

Ao final de cada etapa, o aluno deverá:

1. concluir somente o conteúdo trabalhado em aula;
2. conferir os arquivos no VS Code;
3. utilizar o painel **Source Control**;
4. criar o commit da etapa;
5. sincronizar as alterações com o GitHub.

O histórico do GitHub será utilizado para acompanhar a evolução individual do projeto.

---

## Recomposição do projeto entre as aulas

Como os computadores utilizados nas aulas podem ser limpos ao serem desligados, o GitHub será a fonte oficial do projeto.

No início de cada nova etapa, o aluno deverá:

1. clonar novamente o repositório pelo VS Code;
2. recriar o ambiente virtual;
3. reinstalar as dependências usando `requirements.txt`;
4. recriar o arquivo local `.env`;
5. iniciar o Apache e o MySQL pelo XAMPP;
6. importar `database/biblioteca_db.sql` pelo phpMyAdmin;
7. continuar o desenvolvimento a partir do conteúdo enviado ao GitHub.

O arquivo `.env` não deve ser publicado, pois contém a senha local do usuário `root`. O arquivo SQL deve ser mantido no repositório para permitir a reconstrução do banco.

---

## Padrão de código

O material utiliza exemplos simples e didáticos. As funções, classes e variáveis criadas pelo aluno devem preferencialmente utilizar nomes em português, como:

- `Livro`;
- `LivroCriacao`;
- `LivroResposta`;
- `criar_livro`;
- `listar_livros`;
- `atualizar_livro`;
- `excluir_livro`;
- `sessao_banco`;
- `resultado`;
- `consulta`.

As rotas deverão utilizar funções nomeadas com `def` e instruções claras. Não serão necessários `lambda`, funções anônimas ou abstrações avançadas que escondam a lógica da aplicação.

---

## Avaliação

A avaliação considerará:

- a realização das etapas durante as aulas;
- o funcionamento da API;
- a conexão com o banco MySQL;
- a implementação das operações CRUD;
- a construção do Front End;
- a organização do código;
- a utilização de nomes claros e preferencialmente em português;
- os testes realizados;
- a presença dos arquivos no GitHub;
- a sequência e as datas dos commits.



---

## Resultado esperado

Ao final das quatro etapas, o aluno deverá possuir uma aplicação de livros com:

- API desenvolvida em FastAPI;
- banco MySQL funcionando no XAMPP;
- banco administrado pelo phpMyAdmin;
- operações CRUD completas;
- validação de dados;
- tratamento de erros;
- interface web funcional;
- código e histórico de desenvolvimento armazenados no GitHub.

Este projeto representa a consolidação dos conteúdos trabalhados em **SW-II - Sistemas Web II** durante o 3º Bimestre.
