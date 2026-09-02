# API de Livros com FastAPI e MySQL

## Parte 1 de 4: Preparação do ambiente e conexão com o banco

### Datas de entrega da Etapa 1

| Turma | Data de entrega |
| --- | --- |
| 3F | 02/09/2026 |
| 3C | 03/09/2026 |

> **ATENÇÃO: NÃO SERÃO ACEITAS ENTREGAS ANTECIPADAS.** A data e o horário dos commits serão verificados no histórico do repositório do aluno no GitHub antes da aceitação da entrega. O commit da Etapa 1 deverá ser realizado na data correspondente à turma.

> **Regra da atividade:** cada etapa deste guia deve ser realizada durante a aula, acompanhando a explicação do professor. Não faça todas as etapas de uma única vez. Ao final de cada etapa, faça o commit indicado e envie as alterações para o repositório GitHub do aluno. O desenvolvimento será avaliado ao final do projeto.

Como os computadores da aula podem ser limpos ao serem desligados, o GitHub será a fonte oficial do projeto. No início de cada nova etapa, o projeto deverá ser clonado novamente pelo VS Code, as dependências deverão ser reinstaladas e o banco deverá ser importado a partir do arquivo SQL salvo no GitHub.

---

## 0. Crie manualmente o repositório no GitHub

Cada aluno deverá ter um repositório próprio para registrar a evolução da API. Esta criação será feita manualmente pelo site do GitHub.

### 0.1 Crie o repositório pelo site

1. Acesse `https://github.com` e entre com sua conta.
2. No canto superior direito, clique no sinal `+`.
3. Escolha **New repository**.
4. Em **Repository name**, informe `api-livros`.
5. Adicione uma descrição, por exemplo: `API de livros desenvolvida nas aulas`.
6. Escolha **Public** ou **Private**, de acordo com a orientação do professor.
7. Não marque **Add a README file**, **Add .gitignore** nem **Choose a license** neste momento.
8. Clique em **Create repository**.

O repositório será criado vazio. Deixe a página aberta, pois a URL dele será usada na próxima etapa.

### 0.2 Prepare a pasta local pelo VS Code

1. Abra o VS Code.
2. Acesse **File > Open Folder...** e crie ou selecione a pasta `api-livros`.
3. Abra **Source Control** pelo ícone de ramificação na barra lateral ou use `Ctrl+Shift+G`.
4. Clique em **Initialize Repository**. Essa ação cria o repositório Git local sem usar o terminal.
5. Abra o **Explorer** e crie o arquivo `README.md`.

O botão **Initialize Repository** inicializa o Git no projeto. A branch principal deverá aparecer como `main` na barra inferior ou na área **Source Control**.

Para conectar o projeto ao GitHub, use **Publish Branch** ou **Publish to GitHub** na área **Source Control**. Entre na conta quando o VS Code solicitar, informe `api-livros` como nome e escolha se o repositório será público ou privado.

Se o repositório já foi criado manualmente no site, selecione esse repositório quando o VS Code solicitar a publicação. Não crie um segundo repositório com outro nome.

### Commit da etapa 0

Como o repositório ainda está vazio, faça o primeiro commit pelo VS Code:

1. Em **Source Control**, confira se `README.md` aparece em **Changes**.
2. Clique no sinal `+` ao lado do arquivo para colocá-lo em **Staged Changes**.
3. No campo de mensagem, escreva `chore: inicia repositorio da api de livros`.
4. Clique em **Commit**.
5. Clique em **Sync Changes** ou **Publish Branch** para enviar o commit ao GitHub.

Atualize a página do GitHub e confirme que `README.md` apareceu. Esse registro comprova a conclusão da etapa inicial.

### 0.3 Procedimento no início de cada nova etapa

Ao iniciar a Parte 2, a Parte 3 ou a Parte 4, faça este procedimento antes de escrever código:

1. Abra o VS Code sem abrir uma cópia antiga do projeto.
2. Pressione `Ctrl+Shift+P` para abrir a Paleta de Comandos.
3. Execute **Git: Clone**.
4. Informe a URL do repositório do aluno ou selecione-o na lista.
5. Escolha a pasta onde o projeto será clonado e clique em **Open** quando o VS Code perguntar.
6. Abra **Terminal > New Terminal** no VS Code. Esse terminal será usado para Python e Uvicorn, não para comandos Git.
7. Crie e ative o ambiente virtual conforme a etapa 4.
8. Reinstale as dependências com `pip install -r requirements.txt`.
9. Recrie o arquivo local `.env` usando `DB_USER=root` e a senha local do MySQL.
10. Importe `database/biblioteca_db.sql` pelo phpMyAdmin, conforme explicado na etapa 6.

Repita essa sequência no início de cada nova etapa, mesmo que o aluno já tenha trabalhado no projeto em uma aula anterior.

Este manual inicia a construção de uma API para cadastro de livros. Ao final desta parte, o projeto estará organizado, o MySQL estará preparado, a aplicação FastAPI conseguirá abrir uma conexão com o banco e haverá uma rota simples para verificar se o servidor está funcionando.

Nesta primeira parte ainda não criaremos as operações CRUD nem a interface web. As operações serão desenvolvidas nas partes 2 e 3, e o Front End será construído na Parte 4, depois que a base do projeto estiver funcionando.

---

## 1. O que será construído

A aplicação terá como tema livros. Cada livro poderá possuir, inicialmente, os seguintes dados:

- `id`: identificador numérico único;
- `titulo`: título do livro;
- `autor`: nome do autor;
- `ano_publicacao`: ano em que o livro foi publicado;
- `disponivel`: indica se o livro está disponível.

Nas próximas partes, a API terá as seguintes operações:

| Método | Rota | Objetivo |
| --- | --- | --- |
| `POST` | `/livros` | Criar um livro |
| `GET` | `/livros` | Listar livros |
| `GET` | `/livros/{id}` | Consultar um livro específico |
| `PUT` | `/livros/{id}` | Atualizar um livro |
| `DELETE` | `/livros/{id}` | Excluir um livro |

Na Parte 4, uma interface criada com HTML, CSS e JavaScript consumirá essas rotas pelo navegador.

### Tecnologias utilizadas

- **Python**: linguagem da aplicação;
- **FastAPI**: framework para criação da API;
- **Uvicorn**: servidor que executará a aplicação;
- **SQLAlchemy**: biblioteca de comunicação entre a aplicação e o banco;
- **PyMySQL**: driver Python para conexão com MySQL;
- **Pydantic Settings**: leitura e validação de configurações;
- **MySQL**: banco de dados relacional.

A separação entre FastAPI, SQLAlchemy e MySQL é importante: o FastAPI receberá as requisições HTTP, o SQLAlchemy organizará o acesso aos dados e o MySQL armazenará as informações.

### Padrão didático adotado

Para facilitar o aprendizado, as rotas das Partes 2 e 3 serão escritas com instruções simples e visíveis. O material utilizará:

- funções nomeadas com `def`;
- variáveis com nomes completos e claros;
- estruturas `if`, `for` e `try/except` explicadas passo a passo;
- consultas SQLAlchemy escritas de forma explícita;
- uma etapa por vez, evitando encadeamentos difíceis de acompanhar.

Não serão utilizados `lambda`, funções anônimas, compreensões complexas ou padrões avançados que escondam o funcionamento da rota. O objetivo é que o aluno consiga entender o caminho completo: receber os dados, validar, consultar ou alterar o banco e devolver a resposta.

---

## 2. Pré-requisitos

Antes de começar, instale:

1. **Python 3.11 ou superior**;
2. **MySQL Server**;
3. **XAMPP**, com Apache e MySQL ativados;
4. **phpMyAdmin**, acessado pelo XAMPP;
5. **Visual Studio Code**, recomendado;
6. **Git**, opcional, para versionar o projeto.

---

## 3. Crie a pasta do projeto

Escolha um local para o projeto e execute:

```bat
mkdir api-livros
cd api-livros
```

A pasta deverá ficar semelhante a esta:

```text
api-livros/
```

O nome da pasta não interfere no funcionamento da API. O importante é executar os próximos comandos dentro dela.

### Commit da etapa 3

Registre a preparação da pasta no GitHub:

No VS Code, em **Source Control**, confira as alterações, clique em `+` para prepará-las, escreva `chore: prepara pasta do projeto`, clique em **Commit** e depois em **Sync Changes**.

---

## 4. Crie e ative um ambiente virtual

Um ambiente virtual mantém as bibliotecas deste projeto separadas de outros projetos Python. Isso evita conflitos de versões e torna a instalação reproduzível.

### Windows: Prompt de Comando (CMD)

```bat
python -m venv .venv
.venv\Scripts\activate.bat
```

O arquivo `activate.bat` é o script de ativação próprio do Prompt de Comando.

Quando a ativação funcionar, o CMD normalmente exibirá `(.venv)` no início da linha.

### Commit da etapa 4

O ambiente virtual não deve ser enviado ao GitHub. Registre a configuração:

No VS Code, em **Source Control**, selecione o `.gitignore`, clique em `+`, use a mensagem `chore: configura ambiente virtual`, clique em **Commit** e depois em **Sync Changes**.

---

## 5. Instale as dependências

Com o ambiente virtual ativado, atualize o `pip`:

```bat
python -m pip install --upgrade pip
```

Agora instale as bibliotecas necessárias:

```bat
pip install fastapi uvicorn sqlalchemy pymysql pydantic-settings python-dotenv
```

### Para que serve cada pacote?

- `fastapi`: cria as rotas e disponibiliza a documentação automática;
- `uvicorn`: inicia o servidor ASGI da aplicação;
- `sqlalchemy`: representa tabelas e executa operações no banco;
- `pymysql`: permite que o SQLAlchemy converse com o MySQL;
- `pydantic-settings`: carrega configurações e variáveis de ambiente;
- `python-dotenv`: auxilia na leitura de variáveis armazenadas em arquivos `.env`.

Salve as versões instaladas em um arquivo para que outra pessoa consiga preparar o mesmo ambiente:

```bat
pip freeze > requirements.txt
```

O arquivo `requirements.txt` será criado automaticamente. No futuro, as dependências poderão ser instaladas com:

```bat
pip install -r requirements.txt
```

### Commit da etapa 5

Envie o arquivo de dependências para que a instalação possa ser reproduzida:

No VS Code, selecione `requirements.txt` em **Source Control**, clique em `+`, use a mensagem `chore: adiciona dependencias da api`, clique em **Commit** e depois em **Sync Changes**.

---

## 6. Crie o banco de dados no MySQL

Abra o painel do XAMPP, clique em **Start** no módulo **Apache** e no módulo **MySQL**. Depois, abra o phpMyAdmin pelo botão **Admin** do módulo MySQL ou acesse `http://localhost/phpmyadmin`.

```bat
mysql -u root -p
```

O parâmetro `-u root` informa o usuário e `-p` solicita a senha. Não coloque a senha diretamente no comando, pois ela pode ficar registrada no histórico do terminal.

Execute o SQL abaixo no MySQL:

```sql
CREATE DATABASE biblioteca_db
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;
```

O banco foi criado, mas ainda não possui tabelas. As tabelas serão criadas pelo SQLAlchemy na próxima etapa.

Para confirmar a criação:

```sql
SHOW DATABASES;
```

Procure por `biblioteca_db` na lista. Para sair do cliente do MySQL:

```sql
exit;
```

### Observação sobre usuário do banco

Nesta atividade será utilizado somente o usuário padrão `root` do MySQL. Não será criado outro usuário para a aplicação. A senha do `root` deve ser informada apenas no arquivo `.env` local e nunca publicada no GitHub.

### Salve o banco em um arquivo SQL

O banco criado no computador deve ser salvo em um arquivo SQL dentro do projeto. Esse arquivo permitirá reconstruir o banco quando o projeto for clonado em outra máquina.

Antes da exportação, no **Explorer** do VS Code, clique com o botão direito na pasta do projeto, escolha **New Folder** e crie a pasta `database`.

No phpMyAdmin:

1. Selecione o banco `biblioteca_db`.
2. Abra a aba **Exportar**.
3. Escolha o método **Personalizado**.
4. Mantenha o formato **SQL** e selecione estrutura e dados.
5. Clique em **Exportar** e salve o arquivo como `database/biblioteca_db.sql` dentro da pasta do projeto.

Na Parte 1, o arquivo poderá conter apenas a estrutura inicial. Nas Partes 2 e 3, exporte o banco novamente após criar ou alterar tabelas e dados, substituindo o arquivo SQL anterior. Assim, o banco ficará atualizado no GitHub junto com o código.

Para importar o arquivo no início de uma nova etapa, abra o phpMyAdmin, crie ou selecione o banco `biblioteca_db`, abra a aba **Importar**, escolha `database/biblioteca_db.sql` em **Procurar no seu computador** e clique em **Importar**. Se o banco ainda não existir, crie-o primeiro pela aba **Banco de dados**.

### Commit da etapa 6

O arquivo `database/biblioteca_db.sql` deve ser enviado ao GitHub. A senha do usuário `root` continua apenas no `.env` local e nunca deve ser enviada.

No VS Code, em **Source Control**, selecione `database/biblioteca_db.sql`, clique em `+`, informe `docs: salva estrutura inicial do banco`, clique em **Commit** e depois em **Sync Changes**.

---

## 7. Organize a estrutura inicial

Dentro da pasta `api-livros`, crie a seguinte estrutura:

```text
api-livros/
├── .env
├── .gitignore
├── requirements.txt
├── database/
│   └── biblioteca_db.sql
└── app/
    ├── __init__.py
    ├── database.py
    └── main.py
```

Crie a pasta e os arquivos pelo Explorer do VS Code ou com os comandos abaixo:

```bat
mkdir app
type nul > app\__init__.py
type nul > app\database.py
type nul > app\main.py
type nul > .env
type nul > .gitignore
mkdir database
```

O arquivo `__init__.py` informa que `app` pode ser tratado como um pacote Python. Ele pode ficar vazio.

### Commit da etapa 7

Envie a estrutura inicial:

No VS Code, em **Source Control**, selecione os arquivos da pasta `app`, clique em `+`, informe `chore: cria estrutura inicial da aplicacao`, clique em **Commit** e depois em **Sync Changes**.

---

## 8. Configure as variáveis de ambiente

Abra o arquivo `.env` e adicione:

```dotenv
DB_USER=root
DB_PASSWORD=troque_esta_senha
DB_HOST=localhost
DB_PORT=3306
DB_NAME=biblioteca_db
```

O arquivo `.env` contém dados específicos da máquina, principalmente a senha. Por isso, ele não deve ser enviado para um repositório público.

### Como criar e preencher o `.gitignore`

No **Explorer** do VS Code, clique no arquivo `.gitignore` para abri-lo. Digite exatamente as linhas abaixo e salve com `Ctrl+S`:

```gitignore
.venv/
__pycache__/
*.pyc
.env
```

O `.gitignore` é um arquivo de texto que informa ao Git quais arquivos e pastas não devem aparecer para commit. Cada linha possui um padrão:

- `.venv/`: ignora toda a pasta do ambiente virtual, que pode ser recriada;
- `__pycache__/`: ignora pastas geradas automaticamente pelo Python;
- `*.pyc`: ignora arquivos compilados do Python;
- `.env`: ignora as configurações locais, incluindo a senha do usuário `root`.

O `requirements.txt` deve ser versionado, porque ele descreve as dependências do projeto. Depois de salvar, abra **Source Control** e confirme que `.env` não aparece na lista de alterações. O `.gitignore`, por outro lado, deve aparecer e ser enviado ao GitHub.

### Commit da etapa 8

O `.env` não será enviado. Registre o `.gitignore` e os arquivos que não contêm segredos:

No VS Code, selecione `.gitignore` em **Source Control**, clique em `+`, informe `chore: protege configuracoes locais`, clique em **Commit** e depois em **Sync Changes**.

---

## 9. Crie a configuração da conexão

Abra `app/database.py` e escreva:

```python
from pydantic_settings import BaseSettings, SettingsConfigDict
from sqlalchemy import create_engine
from sqlalchemy.orm import DeclarativeBase, sessionmaker


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
```

### Entenda o código

1. `Settings` descreve quais configurações a aplicação espera encontrar.
2. `SettingsConfigDict(env_file=".env")` informa que os valores devem ser lidos do arquivo `.env`.
3. `DATABASE_URL` monta a URL no formato usado pelo SQLAlchemy para MySQL com PyMySQL.
4. `create_engine` cria o objeto responsável por gerenciar a comunicação com o banco.
5. `pool_pre_ping=True` verifica se uma conexão do pool ainda está ativa antes de utilizá-la.
6. `criar_sessao` será usado nas partes seguintes para abrir sessões de banco.
7. `BaseBanco` será a classe pai dos modelos que representarão as tabelas.

Não confunda `mecanismo_banco` com uma conexão individual. Ele administra a comunicação e o pool de conexões; as sessões serão usadas para executar operações dentro das requisições.

> Atenção: se a senha tiver caracteres especiais, como `@`, `#` ou `/`, a URL pode precisar de codificação. Para o primeiro exercício, use uma senha sem esses caracteres ou faça a codificação adequada antes de colocá-la na URL.

### Commit da etapa 9

Envie o código da configuração, verificando antes que o `.env` continua ignorado:

No VS Code, selecione `app/database.py` em **Source Control**, clique em `+`, informe `feat: configura conexao com mysql`, clique em **Commit** e depois em **Sync Changes**.

---

## 10. Crie a aplicação FastAPI

Abra `app/main.py` e escreva:

```python
from fastapi import FastAPI
from sqlalchemy import text

from app.database import mecanismo_banco


app = FastAPI(
    title="API de Livros",
    version="1.0.0",
    description="API didática para gerenciamento de livros.",
)


@app.get("/health", tags=["Saúde"])
def health_check():
    with mecanismo_banco.connect() as conexao:
        conexao.execute(text("SELECT 1"))

    return {"status": "ok", "database": "connected"}
```

A rota `/health` é uma verificação técnica. Ela executa `SELECT 1`, uma consulta simples que não altera dados, e confirma que a aplicação consegue alcançar o MySQL.

O uso de `text()` é recomendado pelo SQLAlchemy para representar uma instrução SQL textual explícita.

### Commit da etapa 10

Registre a primeira aplicação FastAPI:

No VS Code, selecione `app/main.py` em **Source Control**, clique em `+`, informe `feat: cria aplicacao fastapi e rota de saude`, clique em **Commit** e depois em **Sync Changes**.

---

## 11. Inicie o servidor

Na raiz do projeto, com o ambiente virtual ativado, execute:

```bat
uvicorn app.main:app --reload
```

A expressão `app.main:app` significa:

- `app`: a pasta do pacote Python;
- `main`: o arquivo `main.py`;
- `app`: a variável que contém a instância do FastAPI.

A opção `--reload` reinicia o servidor quando um arquivo Python é alterado. Ela é útil durante o desenvolvimento, mas não deve ser usada como configuração de produção.

O terminal deverá informar um endereço semelhante a:

```text
Uvicorn running on http://127.0.0.1:8000
```

Mantenha esse terminal aberto enquanto testa a API.

### Commit da etapa 11

O servidor em execução não é enviado ao GitHub. Depois de confirmar que ele inicia, registre a conclusão da etapa:

Como esta etapa apenas valida a execução do servidor, use **Source Control > ... > Commit > Commit Empty**, informe `test: valida inicializacao da api` e clique em **Sync Changes**.

---

## 12. Faça os primeiros testes

### 12.1 Teste da rota de saúde

Abra no navegador:

```text
http://127.0.0.1:8000/health
```

A resposta esperada é:

```json
{
  "status": "ok",
  "database": "connected"
}
```

Se aparecer essa resposta, o FastAPI está em execução e a conexão com o MySQL foi validada.

### 12.2 Documentação automática

O FastAPI gera uma interface interativa em:

```text
http://127.0.0.1:8000/docs
```

Também é possível acessar a documentação alternativa em:

```text
http://127.0.0.1:8000/redoc
```

Por enquanto, a documentação exibirá somente a rota `/health`. As rotas de livros aparecerão nas próximas partes.

### 12.3 Teste pelo CMD ou pelo navegador

O teste mais simples é abrir a rota no navegador. Também é possível usar o comando `curl`, disponível nas versões atuais do Windows:

```bat
curl http://127.0.0.1:8000/health
```

O resultado deve mostrar os campos `status` e `database`.

### Commit da etapa 12

Depois de confirmar a rota `/health` e a documentação, registre a validação:

Como esta etapa apenas valida o funcionamento, use **Source Control > ... > Commit > Commit Empty**, informe `test: valida conexao com banco de dados` e clique em **Sync Changes**.

---

## 13. Problemas comuns

### `ModuleNotFoundError: No module named 'fastapi'`

O ambiente virtual pode não estar ativado ou as dependências podem não ter sido instaladas nele. Ative `.venv` e execute novamente:

```bat
pip install -r requirements.txt
```

### `pydantic_core.ValidationError`

Alguma variável obrigatória não foi encontrada. Verifique se `.env` está na raiz do projeto, no mesmo nível de `app`, e se contém `DB_USER`, `DB_PASSWORD` e `DB_NAME`.

### `Access denied for user`

O usuário ou a senha estão incorretos, ou o usuário não recebeu permissão no banco. Teste as mesmas credenciais diretamente no MySQL e confira os valores do `.env`.

### `Can't connect to MySQL server`

O serviço do MySQL pode estar parado, ou a porta pode ser diferente de `3306`. Confirme o serviço e ajuste `DB_PORT` caso necessário.

### `ModuleNotFoundError: No module named 'app'`

Execute o comando `uvicorn` a partir da raiz `api-livros`, e não de dentro da pasta `app`.

### A senha possui caracteres especiais

Caracteres especiais podem alterar a interpretação da URL de conexão. Durante o exercício, use uma senha sem caracteres reservados ou aplique codificação de URL nos valores antes de montar `DATABASE_URL`.

---

## 14. Checklist da Parte 1

Antes de seguir para a Parte 2, confirme:

- [ ] O Python está instalado e acessível pelo terminal.
- [ ] O MySQL está instalado e em execução.
- [ ] O banco `biblioteca_db` foi criado.
- [ ] O ambiente virtual `.venv` foi criado e ativado.
- [ ] O arquivo `requirements.txt` foi gerado.
- [ ] O arquivo `.env` contém as configurações corretas.
- [ ] O `.env` está listado no `.gitignore`.
- [ ] `app/database.py` cria o engine e a sessão.
- [ ] `uvicorn app.main:app --reload` inicia sem erros.
- [ ] `/health` retorna `database: connected`.
- [ ] `/docs` abre a documentação interativa.

---

## Próxima parte

Na **Parte 2**, serão criados o modelo da tabela `livros`, os schemas de validação com Pydantic e as operações para cadastrar e consultar livros usando `POST` e `GET`.

Na **Parte 3**, serão implementadas as operações de atualização e exclusão com `PUT` e `DELETE`, além dos tratamentos de erro e da conclusão do CRUD.

Na **Parte 4**, será criada uma interface web com HTML, CSS e JavaScript para listar, cadastrar, editar e excluir livros por meio da API.
