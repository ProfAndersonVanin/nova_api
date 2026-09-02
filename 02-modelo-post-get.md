# API de Livros com FastAPI e MySQL

## Parte 2 de 4: Modelo, validação e rotas POST e GET

### Datas de entrega da Etapa 2

| Turma | Data de entrega |
| --- | --- |
| 3F | 09/09/2026 |
| 3C | 10/09/2026 |

> **ATENÇÃO: NÃO SERÃO ACEITAS ENTREGAS ANTECIPADAS.** A data e o horário dos commits serão verificados no histórico do repositório do aluno no GitHub antes da aceitação da entrega. O commit da Etapa 2 deverá ser realizado na data correspondente à turma.

Nesta parte serão criados os primeiros recursos reais da API. Ao final, a aplicação poderá cadastrar livros e consultar os livros armazenados no MySQL.

As operações desenvolvidas serão:

| Método | Rota | Objetivo |
| --- | --- | --- |
| `POST` | `/livros` | Cadastrar um livro |
| `GET` | `/livros` | Listar todos os livros |
| `GET` | `/livros/{id}` | Consultar um livro pelo identificador |

A Parte 3 implementará `PUT` e `DELETE`, completando o CRUD.

> **Regra da atividade:** esta parte deve ser realizada em aula, uma etapa por vez. Não copie todas as etapas de uma única vez. Ao terminar cada etapa, faça o commit pelo painel **Source Control** do VS Code e sincronize com o GitHub. O projeto será avaliado ao final.

---

## 1. Prepare a máquina para esta etapa

Como os arquivos da máquina podem ser removidos ao final da aula, comece sempre com uma cópia nova do projeto.

### 1.1 Clone o projeto pelo VS Code

1. Abra o VS Code sem abrir uma cópia antiga do projeto.
2. Pressione `Ctrl+Shift+P` para abrir a Paleta de Comandos.
3. Execute **Git: Clone**.
4. Informe a URL do repositório GitHub do aluno ou selecione o repositório na lista.
5. Escolha a pasta onde o projeto será clonado.
6. Clique em **Open** quando o VS Code perguntar se deseja abrir o projeto.

O clone recupera o código e os arquivos versionados na aula anterior. O arquivo `.env` não será recuperado, pois ele está no `.gitignore`.

### 1.2 Reinstale as dependências pelo terminal integrado

No VS Code, abra **Terminal > New Terminal**. Use o terminal apenas para os comandos de Python e para iniciar o servidor; os commits serão feitos visualmente pelo **Source Control**.

Crie e ative o ambiente virtual no Prompt de Comando:

```bat
python -m venv .venv
.venv\Scripts\activate.bat
```

Instale as dependências registradas no projeto:

```bat
pip install -r requirements.txt
```

### 1.3 Recrie o arquivo `.env`

Na raiz do projeto, abra o **Explorer**, crie o arquivo `.env` e preencha com os dados locais do MySQL:

```dotenv
DB_USER=root
DB_PASSWORD=troque_esta_senha
DB_HOST=localhost
DB_PORT=3306
DB_NAME=biblioteca_db
```

Substitua `troque_esta_senha` pela senha do usuário `root` da máquina. Não envie o `.env` para o GitHub.

### 1.4 Importe o banco existente

No phpMyAdmin, com Apache e MySQL ativos no XAMPP:

1. Crie ou selecione o banco `biblioteca_db`.
2. Abra a aba **Importar**.
3. Selecione o arquivo `database/biblioteca_db.sql` clonado do GitHub.
4. Mantenha o formato SQL.
5. Clique em **Importar**.

Se o banco ainda não existir nessa máquina, abra a aba **SQL** do phpMyAdmin e execute:

```sql
CREATE DATABASE biblioteca_db
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;
```

Depois faça a importação novamente.

### Commit da etapa 1

A preparação usa arquivos locais que não devem ser enviados. Registre a confirmação no VS Code usando **Source Control > ... > Commit > Commit Empty**. Use a mensagem:

`chore: prepara ambiente para a parte 2`

Depois clique em **Sync Changes**.

---

## 2. Crie o modelo da tabela `livros`

Um modelo é uma classe Python que representa uma tabela do banco. Cada atributo da classe representa uma coluna.

No **Explorer** do VS Code, dentro da pasta `app`, crie o arquivo `models.py`. Adicione:

```python
from sqlalchemy import Boolean, Column, Integer, String

from app.database import BaseBanco


class Livro(BaseBanco):
    __tablename__ = "livros"

    id = Column(Integer, primary_key=True, index=True)
    titulo = Column(String(150), nullable=False)
    autor = Column(String(120), nullable=False)
    ano_publicacao = Column(Integer, nullable=False)
    disponivel = Column(Boolean, nullable=False, default=True)
```

### Entenda o modelo

- `Livro` é a classe Python que representa a tabela.
- `__tablename__` define o nome da tabela no MySQL.
- `id` é a chave primária. O banco usará esse valor para identificar cada livro.
- `titulo` e `autor` são textos obrigatórios.
- `ano_publicacao` é um número inteiro obrigatório.
- `disponivel` armazena `True` ou `False`.
- `nullable=False` impede que a coluna receba `NULL`.
- `default=True` define disponibilidade como verdadeira quando nenhum valor for informado.

Neste momento, apenas escrever a classe não cria a tabela. O SQLAlchemy precisa executar `BaseBanco.metadata.create_all()` depois que o modelo for importado.

### Commit da etapa 2

No **Source Control** do VS Code, selecione `app/models.py`, clique em `+`, escreva:

`feat: cria modelo de livros`

Clique em **Commit** e depois em **Sync Changes**.

---

## 3. Crie os schemas de entrada e saída

O modelo representa o banco. O schema representa os dados que a API aceita ou devolve. Essa separação evita que detalhes internos do banco sejam usados diretamente como contrato HTTP.

Na pasta `app`, crie `schemas.py` e adicione:

```python
from pydantic import BaseModel, Field


class LivroCriacao(BaseModel):
    titulo: str = Field(min_length=1, max_length=150)
    autor: str = Field(min_length=1, max_length=120)
    ano_publicacao: int = Field(ge=0, le=2100)
    disponivel: bool = True


class LivroResposta(BaseModel):
    id: int
    titulo: str
    autor: str
    ano_publicacao: int
    disponivel: bool

    class Config:
        from_attributes = True
```

### Entenda os schemas

- `LivroCriacao` descreve o corpo esperado no `POST`.
- O `id` não aparece em `LivroCriacao`, porque o banco será responsável por criá-lo.
- `Field(min_length=1)` impede texto vazio.
- `Field(max_length=150)` limita o tamanho do título.
- `Field(ge=0, le=2100)` permite somente anos dentro do intervalo definido para a atividade.
- `LivroResposta` descreve a resposta enviada ao cliente.
- `from_attributes = True` permite que o Pydantic leia os atributos do objeto SQLAlchemy.

A validação do Pydantic acontece antes da função da rota ser executada. Se o cliente enviar dados inválidos, o FastAPI responderá com HTTP `422`.

### Commit da etapa 3

No **Source Control**, selecione `app/schemas.py`, clique em `+`, informe:

`feat: adiciona schemas de livros`

Clique em **Commit** e depois em **Sync Changes**.

---

## 4. Crie a dependência da sessão do banco

Cada rota precisa de uma sessão para conversar com o MySQL. A sessão deve ser aberta no início da requisição e fechada ao final.

Abra `app/database.py` e deixe o arquivo assim:

```python
from collections.abc import Generator

from pydantic_settings import BaseSettings, SettingsConfigDict
from sqlalchemy import create_engine
from sqlalchemy.orm import DeclarativeBase, Session, sessionmaker


class Configuracoes(BaseSettings):
    db_user: str
    db_password: str
    db_host: str = "localhost"
    db_port: int = 3306
    db_name: str

    model_config = SettingsConfigDict(env_file=".env", extra="ignore")


configuracoes = Configuracoes()

DATABASE_URL = (
    f"mysql+pymysql://{configuracoes.db_user}:{configuracoes.db_password}"
    f"@{configuracoes.db_host}:{configuracoes.db_port}/{configuracoes.db_name}"
)

mecanismo_banco = create_engine(DATABASE_URL, pool_pre_ping=True)
criar_sessao = sessionmaker(bind=mecanismo_banco, autoflush=False, autocommit=False)


class BaseBanco(DeclarativeBase):
    pass


def obter_sessao_banco() -> Generator[Session, None, None]:
    sessao_banco = criar_sessao()

    try:
        yield sessao_banco
    finally:
        sessao_banco.close()
```

### Entenda `obter_sessao_banco`

1. `criar_sessao()` cria uma sessão.
2. `yield` entrega essa sessão para a rota.
3. A rota executa sua consulta ou alteração.
4. O bloco `finally` fecha a sessão, mesmo quando ocorre um erro.

A função é nomeada com `def` e possui etapas explícitas. Não é usada nenhuma função anônima ou `lambda`.

### Commit da etapa 4

No **Source Control**, selecione `app/database.py`, clique em `+`, informe:

`feat: cria sessao do banco`

Clique em **Commit** e depois em **Sync Changes**.

---

## 5. Crie a tabela e a rota de cadastro

Abra `app/main.py` e substitua o conteúdo pelo código abaixo:

```python
from fastapi import Depends, FastAPI, HTTPException
from sqlalchemy import select
from sqlalchemy.orm import Session

from app.database import BaseBanco, mecanismo_banco, obter_sessao_banco
from app.models import Livro
from app.schemas import LivroCriacao, LivroResposta


BaseBanco.metadata.create_all(bind=mecanismo_banco)

app = FastAPI(
    title="API de Livros",
    version="1.0.0",
    description="API didática para gerenciamento de livros.",
)


@app.post("/livros", response_model=LivroResposta, status_code=201, tags=["Livros"])
def criar_livro(dados_livro: LivroCriacao, sessao_banco: Session = Depends(obter_sessao_banco)):
    novo_livro = Livro(
        titulo=dados_livro.titulo,
        autor=dados_livro.autor,
        ano_publicacao=dados_livro.ano_publicacao,
        disponivel=dados_livro.disponivel,
    )

    sessao_banco.add(novo_livro)
    sessao_banco.commit()
    sessao_banco.refresh(novo_livro)

    return novo_livro
```

### Entenda a rota `POST`

1. `@app.post` registra a rota.
2. `dados_livro` recebe e valida o JSON usando `LivroCriacao`.
3. `novo_livro` transforma os dados validados em um objeto `Livro`.
4. `add` coloca o objeto na sessão.
5. `commit` grava o registro no MySQL.
6. `refresh` atualiza o objeto com o `id` criado pelo banco.
7. `return` devolve o livro usando o formato `LivroResposta`.

O status `201` significa que um recurso foi criado.

A instrução `BaseBanco.metadata.create_all(bind=mecanismo_banco)` cria a tabela `livros` se ela ainda não existir. Ela não apaga a tabela existente.

### Commit da etapa 5

No **Source Control**, selecione `app/main.py`, clique em `+`, informe:

`feat: cria rota para cadastrar livros`

Clique em **Commit** e depois em **Sync Changes**.

---

## 6. Crie a rota para listar livros

Agora adicione ao final de `app/main.py`:

```python

@app.get("/livros", response_model=list[LivroResposta], tags=["Livros"])
def listar_livros(sessao_banco: Session = Depends(obter_sessao_banco)):
    consulta = select(Livro)
    resultado = sessao_banco.execute(consulta)
    livros = resultado.scalars().all()

    return livros
```

### Entenda a rota de listagem

1. `@app.get` registra a rota `GET /livros`.
2. `select(Livro)` monta uma consulta para a tabela `livros`.
3. `execute` envia a consulta ao banco.
4. `scalars` extrai os objetos `Livro` do resultado.
5. `all` transforma o resultado em uma lista.
6. `return` entrega a lista ao cliente.

A anotação `list[LivroResposta]` informa que a resposta será uma lista de livros.

### Commit da etapa 6

No **Source Control**, selecione `app/main.py`, clique em `+`, informe:

`feat: cria rota para listar livros`

Clique em **Commit** e depois em **Sync Changes**.

---

## 7. Crie a rota para consultar um livro

Adicione também ao final de `app/main.py`:

```python

@app.get("/livros/{id_livro}", response_model=LivroResposta, tags=["Livros"])
def obter_livro(id_livro: int, sessao_banco: Session = Depends(obter_sessao_banco)):
    consulta = select(Livro).where(Livro.id == id_livro)
    resultado = sessao_banco.execute(consulta)
    livro = resultado.scalar_one_or_none()

    if livro is None:
        raise HTTPException(status_code=404, detail="Livro não encontrado")

    return livro
```

### Entenda a consulta por `id`

- `id_livro: int` informa que o parâmetro da URL deve ser um número.
- `where(Livro.id == id_livro)` procura somente o registro solicitado.
- `scalar_one_or_none()` devolve um livro ou `None` quando não encontra o registro.
- O `if` verifica o resultado.
- `HTTPException` retorna HTTP `404`, informando que o livro não foi encontrado.

O `if` foi escrito de forma explícita para que o fluxo da rota seja fácil de acompanhar.

### Commit da etapa 7

No **Source Control**, selecione `app/main.py`, clique em `+`, informe:

`feat: cria consulta de livro por id`

Clique em **Commit** e depois em **Sync Changes**.

---

## 8. Execute e teste a API

No terminal integrado do VS Code, com o ambiente virtual ativado, inicie o servidor:

```bat
uvicorn app.main:app --reload
```

Abra a documentação em:

```text
http://127.0.0.1:8000/docs
```

### 8.1 Teste `POST /livros`

1. Abra `POST /livros` no Swagger.
2. Clique em **Try it out**.
3. Use o JSON:

```json
{
  "titulo": "O Hobbit",
  "autor": "J. R. R. Tolkien",
  "ano_publicacao": 1937,
  "disponivel": true
}
```

4. Clique em **Execute**.
5. Confirme o status `201` e o `id` retornado.

### 8.2 Teste `GET /livros`

1. Abra `GET /livros`.
2. Clique em **Try it out**.
3. Clique em **Execute**.
4. Confirme que o livro cadastrado aparece na lista.

### 8.3 Teste `GET /livros/{id_livro}`

1. Abra `GET /livros/{id_livro}`.
2. Informe o `id` recebido no cadastro.
3. Clique em **Execute**.
4. Confirme os dados do livro.
5. Teste também um `id` inexistente e confirme o status `404`.

### 8.4 Teste de validação

Tente cadastrar um livro sem título ou com ano maior que `2100`. O FastAPI deve responder com status `422` e informar o campo inválido.

### Commit da etapa 8

Depois de executar todos os testes, use no **Source Control** a opção **... > Commit > Commit Empty**. Informe:

`test: valida rotas post e get`

Clique em **Sync Changes**.

---

## 9. Atualize o arquivo SQL do projeto

A tabela e os registros criados nesta parte precisam ser levados para a próxima aula.

No phpMyAdmin, com Apache e MySQL ativos no XAMPP:

1. Selecione `biblioteca_db`.
2. Abra a aba **Exportar**.
3. Escolha o formato **SQL** e selecione estrutura e dados.
4. Salve substituindo `database/biblioteca_db.sql`.
5. Clique em **Exportar**.

Depois, no VS Code:

1. Abra **Source Control**.
2. Selecione `database/biblioteca_db.sql` em **Changes**.
3. Clique em `+`.
4. Escreva `docs: atualiza banco apos rotas de cadastro e consulta`.
5. Clique em **Commit**.
6. Clique em **Sync Changes**.

Não inclua o `.env` no commit. O arquivo SQL contém a estrutura e os dados do exercício, mas não deve conter senhas.

---

## 10. Problemas comuns

### A tabela `livros` não foi criada

Confirme se `from app.models import Livro` aparece em `main.py` antes de `BaseBanco.metadata.create_all(bind=mecanismo_banco)`. O modelo precisa ser importado antes da criação das tabelas.

### A resposta apresenta erro de validação no retorno

Confira se `LivroResposta` possui todos os campos retornados pelo objeto `Livro` e se `from_attributes = True` está dentro da classe `Config`.

### O cadastro retorna erro de conexão

Confira o `.env`, confirme que o MySQL está em execução e verifique se `DB_USER=root` está correto.

### O livro aparece na resposta, mas não no banco

Confirme se `sessao_banco.commit()` foi executado. Sem `commit`, a alteração não é gravada permanentemente.

### A importação do SQL não recuperou os dados

Confirme se o arquivo correto foi exportado com **Dump Structure and Data** e se o banco `biblioteca_db` foi selecionado como destino da importação.

---

## 11. Checklist da Parte 2

- [ ] O projeto foi clonado novamente pelo VS Code.
- [ ] O ambiente virtual foi recriado.
- [ ] As dependências foram reinstaladas com `requirements.txt`.
- [ ] O `.env` local foi recriado com o usuário `root`.
- [ ] O arquivo SQL foi importado no phpMyAdmin do XAMPP.
- [ ] `app/models.py` representa a tabela `livros`.
- [ ] `app/schemas.py` valida os dados.
- [ ] `get_db` abre e fecha a sessão.
- [ ] `POST /livros` cadastra um livro.
- [ ] `GET /livros` lista os livros.
- [ ] `GET /livros/{id}` consulta um livro.
- [ ] O erro `404` foi testado.
- [ ] O erro `422` foi testado.
- [ ] O arquivo SQL foi exportado novamente.
- [ ] Cada etapa recebeu seu commit no VS Code.

Na Parte 3 serão criadas as rotas `PUT` e `DELETE`, completando o CRUD e preparando a API para o Front End.
