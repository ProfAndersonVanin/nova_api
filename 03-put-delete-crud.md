# API de Livros com FastAPI e MySQL

## Parte 3 de 4: Rotas PUT, DELETE e conclusão do CRUD

### Datas de entrega da Etapa 3

| Turma | Data de entrega |
| --- | --- |
| 3F | 16/09/2026 |
| 3C | 17/09/2026 |

> **ATENÇÃO: NÃO SERÃO ACEITAS ENTREGAS ANTECIPADAS.** A data e o horário dos commits serão verificados no histórico do repositório do aluno no GitHub antes da aceitação da entrega. O commit da Etapa 3 deverá ser realizado na data correspondente à turma.

Nesta parte serão implementadas as duas operações que faltam para concluir o CRUD da API:

| Método | Rota | Objetivo |
| --- | --- | --- |
| `PUT` | `/livros/{id_livro}` | Atualizar um livro |
| `DELETE` | `/livros/{id_livro}` | Excluir um livro |

Ao final, a API terá as cinco operações principais para livros:

- `POST`: criar;
- `GET`: consultar;
- `PUT`: atualizar;
- `DELETE`: excluir.

A Parte 4 utilizará essas rotas no Front End criado com HTML, CSS e JavaScript.

> **Regra da atividade:** esta parte deve ser realizada em aula, uma etapa por vez. Não desenvolva tudo de uma única vez. Ao concluir cada etapa, faça o commit pelo painel **Source Control** do VS Code e sincronize com o GitHub. O trabalho completo será avaliado ao final.

---

## 1. Prepare a máquina para esta etapa

Os arquivos e o banco da aula anterior podem ter sido removidos quando a máquina foi desligada. Por isso, sempre comece com uma cópia nova do projeto.

### 1.1 Clone o projeto pelo VS Code

1. Abra o VS Code sem abrir uma cópia antiga.
2. Pressione `Ctrl+Shift+P` para abrir a Paleta de Comandos.
3. Execute **Git: Clone**.
4. Informe a URL do repositório GitHub do aluno ou selecione o repositório na lista.
5. Escolha a pasta onde o projeto será clonado.
6. Clique em **Open** para abrir a cópia clonada.

O clone recupera os arquivos enviados na Parte 2, incluindo `app/models.py`, `app/schemas.py`, `app/database.py`, `app/main.py`, `requirements.txt` e `database/biblioteca_db.sql`.

O arquivo `.env` não é recuperado porque contém a senha local do usuário `root` e está listado no `.gitignore`.

### 1.2 Reinstale as dependências

No VS Code, abra **Terminal > New Terminal**. No Prompt de Comando, crie e ative o ambiente virtual:

```bat
python -m venv .venv
.venv\Scripts\activate.bat
```

Instale as bibliotecas do projeto:

```bat
pip install -r requirements.txt
```

### 1.3 Recrie o arquivo `.env`

No **Explorer** do VS Code, crie `.env` na raiz do projeto e informe:

```dotenv
DB_USER=root
DB_PASSWORD=troque_esta_senha
DB_HOST=localhost
DB_PORT=3306
DB_NAME=biblioteca_db
```

Use a senha do usuário `root` configurado no MySQL do XAMPP. Não envie o `.env` para o GitHub.

### 1.4 Importe o banco pelo phpMyAdmin

1. Abra o painel do XAMPP.
2. Clique em **Start** no Apache e no MySQL.
3. Clique em **Admin** no módulo MySQL ou acesse `http://localhost/phpmyadmin`.
4. Crie ou selecione o banco `biblioteca_db`.
5. Abra a aba **Importar**.
6. Selecione `database/biblioteca_db.sql` dentro do projeto clonado.
7. Mantenha o formato SQL e clique em **Importar**.

Se o banco não existir, abra a aba **SQL** do phpMyAdmin e execute:

```sql
CREATE DATABASE biblioteca_db
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;
```

Depois selecione o banco criado e faça a importação novamente.

### Commit da etapa 1

Esta etapa recria arquivos locais e o banco, mas não altera o código. No **Source Control**, escolha **... > Commit > Commit Empty**, informe:

`chore: prepara ambiente para a parte 3`

Clique em **Sync Changes**.

---

## 2. Revise a estrutura da classe `Livro`

Abra `app/models.py` pelo **Explorer** e confirme que a classe do modelo está escrita em português:

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

A classe `Livro` representa a tabela `livros`. Nas rotas, usaremos `Livro` para localizar e modificar registros.

### Commit da etapa 2

Se a classe já estiver correta, não é necessário criar uma alteração artificial. Se você precisou corrigir o arquivo, selecione `app/models.py` no **Source Control**, clique em `+`, informe:

`fix: padroniza modelo livro`

Clique em **Commit** e depois em **Sync Changes**.

---

## 3. Revise os schemas

Abra `app/schemas.py` e confirme que os schemas estão assim:

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

Usaremos `LivroCriacao` tanto para `POST` quanto para `PUT`. Isso mantém o exercício simples: nos dois casos, o cliente envia os dados completos do livro.

O `id` não deve ser recebido no corpo do formulário. Ele vem na URL, por exemplo, em `/livros/3`.

### Commit da etapa 3

Se algum ajuste foi necessário, no **Source Control**, selecione `app/schemas.py`, clique em `+`, informe:

`fix: padroniza schemas de livros`

Clique em **Commit** e depois em **Sync Changes**.

---

## 4. Prepare o arquivo `main.py`

Abra `app/main.py`. A parte inicial deve manter os imports e a criação da tabela:

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
```

As rotas `criar_livro`, `listar_livros` e `obter_livro` da Parte 2 devem continuar no arquivo. Não as apague. As próximas etapas acrescentarão duas novas funções ao final.

### Commit da etapa 4

Se foi necessário corrigir os imports ou a estrutura inicial, selecione `app/main.py` no **Source Control**, clique em `+`, informe:

`fix: prepara arquivo principal para crud`

Clique em **Commit** e depois em **Sync Changes**.

---

## 5. Crie a rota `PUT` para atualizar um livro

Adicione ao final de `app/main.py`:

```python

@app.put("/livros/{id_livro}", response_model=LivroResposta, tags=["Livros"])
def atualizar_livro(
    id_livro: int,
    dados_livro: LivroCriacao,
    sessao_banco: Session = Depends(obter_sessao_banco),
):
    consulta = select(Livro).where(Livro.id == id_livro)
    resultado = sessao_banco.execute(consulta)
    livro = resultado.scalar_one_or_none()

    if livro is None:
        raise HTTPException(status_code=404, detail="Livro não encontrado")

    livro.titulo = dados_livro.titulo
    livro.autor = dados_livro.autor
    livro.ano_publicacao = dados_livro.ano_publicacao
    livro.disponivel = dados_livro.disponivel

    sessao_banco.commit()
    sessao_banco.refresh(livro)

    return livro
```

### Entenda a rota `PUT`

1. `@app.put` registra a rota de atualização.
2. `id_livro` recebe o identificador enviado na URL.
3. `dados_livro` recebe o JSON e passa pela validação de `LivroCriacao`.
4. `select(Livro)` cria uma consulta para localizar o registro.
5. `resultado` guarda o retorno da consulta.
6. `scalar_one_or_none()` devolve o livro encontrado ou `None`.
7. O `if` retorna `404` quando o identificador não existe.
8. Cada atributo do livro é atualizado explicitamente.
9. `commit()` grava as alterações no MySQL.
10. `refresh()` atualiza o objeto com os dados persistidos.
11. `return` devolve o livro atualizado.

A atualização explícita de cada campo foi escolhida para que o aluno veja exatamente o que será modificado. Não usamos `lambda`, funções anônimas ou uma operação escondida em uma abstração.

### Commit da etapa 5

No **Source Control**, selecione `app/main.py`, clique em `+`, informe:

`feat: cria rota para atualizar livros`

Clique em **Commit** e depois em **Sync Changes**.

---

## 6. Crie a rota `DELETE` para excluir um livro

Adicione depois da rota `PUT`:

```python

@app.delete("/livros/{id_livro}", tags=["Livros"])
def excluir_livro(
    id_livro: int,
    sessao_banco: Session = Depends(obter_sessao_banco),
):
    consulta = select(Livro).where(Livro.id == id_livro)
    resultado = sessao_banco.execute(consulta)
    livro = resultado.scalar_one_or_none()

    if livro is None:
        raise HTTPException(status_code=404, detail="Livro não encontrado")

    sessao_banco.delete(livro)
    sessao_banco.commit()

    return {"mensagem": "Livro excluído com sucesso"}
```

### Entenda a rota `DELETE`

1. `@app.delete` registra a rota de exclusão.
2. A consulta procura o livro pelo `id_livro`.
3. O `if` impede a tentativa de excluir um registro inexistente.
4. `delete(livro)` marca o objeto para exclusão.
5. `commit()` confirma a exclusão no MySQL.
6. A função devolve uma mensagem simples ao cliente.

O status padrão dessa resposta é `200`. O `404` é usado quando o livro não existe.

### Commit da etapa 6

No **Source Control**, selecione `app/main.py`, clique em `+`, informe:

`feat: cria rota para excluir livros`

Clique em **Commit** e depois em **Sync Changes**.

---

## 7. Execute o servidor e abra o Swagger

No terminal integrado do VS Code, com o ambiente virtual ativado, execute:

```bat
uvicorn app.main:app --reload
```

Abra a documentação em:

```text
http://127.0.0.1:8000/docs
```

As rotas disponíveis deverão ser:

- `POST /livros`;
- `GET /livros`;
- `GET /livros/{id_livro}`;
- `PUT /livros/{id_livro}`;
- `DELETE /livros/{id_livro}`.

### Commit da etapa 7

Depois de confirmar que a documentação exibe as cinco operações, use **Source Control > ... > Commit > Commit Empty**. Informe:

`test: confirma rotas completas do crud`

Clique em **Sync Changes**.

---

## 8. Teste o CRUD completo

Use o Swagger para testar as rotas na ordem abaixo.

### 8.1 Crie um livro

1. Abra `POST /livros`.
2. Clique em **Try it out**.
3. Envie:

```json
{
  "titulo": "Dom Casmurro",
  "autor": "Machado de Assis",
  "ano_publicacao": 1899,
  "disponivel": true
}
```

4. Clique em **Execute**.
5. Anote o `id` retornado.

### 8.2 Consulte o livro

1. Abra `GET /livros/{id_livro}`.
2. Informe o `id` anotado.
3. Clique em **Execute**.
4. Confirme os dados cadastrados.

### 8.3 Atualize o livro

1. Abra `PUT /livros/{id_livro}`.
2. Informe o mesmo `id`.
3. Clique em **Try it out**.
4. Altere, por exemplo, a disponibilidade:

```json
{
  "titulo": "Dom Casmurro",
  "autor": "Machado de Assis",
  "ano_publicacao": 1899,
  "disponivel": false
}
```

5. Clique em **Execute**.
6. Confirme que a resposta apresenta `disponivel: false`.

### 8.4 Liste os livros

1. Abra `GET /livros`.
2. Clique em **Try it out** e em **Execute**.
3. Confirme que o livro aparece com os dados atualizados.

### 8.5 Exclua o livro

1. Abra `DELETE /livros/{id_livro}`.
2. Informe o mesmo `id`.
3. Clique em **Try it out** e em **Execute**.
4. Confirme a mensagem de sucesso.
5. Execute `GET /livros/{id_livro}` novamente.
6. Confirme o status `404`.

### 8.6 Teste erros

Faça também estes testes:

- atualizar um `id` inexistente: deve retornar `404`;
- excluir um `id` inexistente: deve retornar `404`;
- enviar título vazio: deve retornar `422`;
- enviar ano maior que `2100`: deve retornar `422`;
- enviar texto no lugar do ano: deve retornar `422`.

### Commit da etapa 8

Depois de concluir todos os testes, use **Source Control > ... > Commit > Commit Empty**. Informe:

`test: valida crud completo pelo swagger`

Clique em **Sync Changes**.

---

## 9. Atualize o banco no arquivo SQL

Os livros de teste e a estrutura atualizada devem ser preservados para a próxima aula.

No phpMyAdmin:

1. Selecione o banco `biblioteca_db`.
2. Abra a aba **Exportar**.
3. Escolha o formato **SQL**.
4. Selecione estrutura e dados.
5. Clique em **Exportar**.
6. Salve substituindo `database/biblioteca_db.sql`.

No VS Code:

1. Abra **Source Control**.
2. Selecione `database/biblioteca_db.sql` em **Changes**.
3. Clique em `+`.
4. Informe `docs: atualiza banco apos conclusao do crud`.
5. Clique em **Commit**.
6. Clique em **Sync Changes**.

Não envie o `.env`. O arquivo SQL pode conter os dados de teste, mas nunca deve conter a senha do MySQL.

---

## 10. Verifique o contrato para o Front End

Antes de encerrar esta parte, confirme que o Front End da Parte 4 encontrará as rotas e os campos esperados:

| Método | Rota | Corpo |
| --- | --- | --- |
| `POST` | `/livros` | `titulo`, `autor`, `ano_publicacao`, `disponivel` |
| `GET` | `/livros` | Nenhum |
| `GET` | `/livros/{id_livro}` | Nenhum |
| `PUT` | `/livros/{id_livro}` | `titulo`, `autor`, `ano_publicacao`, `disponivel` |
| `DELETE` | `/livros/{id_livro}` | Nenhum |

O Front End não deve acessar o MySQL. Ele enviará requisições para o FastAPI, e o FastAPI continuará responsável por validar e alterar os dados.

### Commit da etapa 10

No **Source Control**, use **... > Commit > Commit Empty** e informe:

`docs: confirma contrato da api para frontend`

Clique em **Sync Changes**.

---

## 11. Checklist da Parte 3

- [ ] O projeto foi clonado novamente pelo VS Code.
- [ ] As dependências foram reinstaladas.
- [ ] O `.env` foi recriado localmente com `root`.
- [ ] O banco foi importado pelo phpMyAdmin do XAMPP.
- [ ] A classe usada nas rotas é `Livro`.
- [ ] Os schemas usados são `LivroCriacao` e `LivroResposta`.
- [ ] `PUT /livros/{id_livro}` atualiza um livro.
- [ ] `DELETE /livros/{id_livro}` exclui um livro.
- [ ] O erro `404` foi testado nas duas novas rotas.
- [ ] O erro `422` foi testado.
- [ ] O CRUD completo foi testado pelo Swagger.
- [ ] O arquivo `database/biblioteca_db.sql` foi atualizado.
- [ ] Cada etapa recebeu commit pelo **Source Control**.
- [ ] Os commits foram sincronizados com o GitHub.

Ao concluir este guia, a API estará pronta para ser consumida pelo Front End da Parte 4.
