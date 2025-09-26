# Documentação Técnica de Instalação e Inicialização

## Manual de Configuração e Execução da Aplicação

Este documento técnico detalha o procedimento para configurar o ambiente de desenvolvimento, instalar as dependências e inicializar a aplicação. O manual é destinado a desenvolvedores que precisam replicar o ambiente de produção em uma máquina local.

-----

### 1\. Pré-requisitos do Sistema

Antes de iniciar a instalação, certifique-se de que os seguintes componentes estão instalados em seu sistema operacional:

  * **Python 3.11+**: O projeto requer uma versão do Python 3.
  * **Git**: Ferramenta de controle de versão para clonagem do repositório.
  * **Docker** e **Docker Compose**: Essenciais para gerenciar o banco de dados.
  * **WSL (Windows Subsystem for Linux)**: Requisito para usuários de Windows.

-----

## 🚀 Primeiros Passos: Clonagem e Configuração Inicial

Após clonar este repositório, é necessário rodar um script de configuração inicial que prepara o ambiente de desenvolvimento.

Esse script irá:

  * Atualizar e inicializar os **submódulos** (`docs`, `server`).
  * Configurar o **template de commits**.
  * Instalar o **hook de validação** de mensagens de commit.

O projeto estará devidamente configurado após a execução dos passos.

### Passo a Passo

1.  **Clone o repositório:**

    ```bash
    git clone https://github.com/AthosFatecSjc/Athos.git
    cd Athos
    ```

2.  **Dê permissão de execução para o script (se necessário):**

    ```bash
    chmod +x getting-started.sh
    ```

3.  **Execute o script de configuração inicial:**

    ```bash
    ./getting-started.sh
    ```

4.  **Confirme o status dos submódulos:**

    ```bash
    git submodule status
    ```

-----

## 2\. Configuração do Ambiente `server`

Após a clonagem e inicialização dos submódulos, vamos configurar o ambiente do servidor e do banco de dados.

### 2.1. Acessar o Submódulo `server`

Mova-se para o diretório da aplicação:

```bash
cd server
```

### 2.2. Criação e Ativação do Ambiente Virtual (`venv`)

Crie um ambiente isolado para evitar conflitos de dependências com o sistema operacional.

```bash
# Criar o ambiente virtual
python3 -m venv venv

# Ativar o ambiente virtual
source venv/bin/activate
```

### 2.3. Instalação das Dependências

Com o ambiente virtual **ativo** (`(venv)` no prompt), instale as dependências listadas em `requirements.txt`:

```bash
pip install -r requirements.txt
```

### 2.4. Configuração do Arquivo `.env`

Crie e configure o arquivo de variáveis de ambiente com as credenciais do banco de dados e a chave secreta da aplicação.

```bash
# Crie o arquivo .env
touch .env

# Abra o editor (nano é um editor simples de terminal)
nano .env
```

**Conteúdo do arquivo `.env`:**

```env
POSTGRES_DB=<nome-do-banco-vem-aqui>
POSTGRES_USER=<usuario-do-banco-vem-aqui>
POSTGRES_PASSWORD=<senha-do-usuario-do-banco-aqui>
POSTGRES_HOST=localhost
POSTGRES_PORT=5432

SECRET_KEY=<sua-secret-key-aqui>
```

-----

## 3\. Inicialização e Execução da Aplicação

### 3.1. Levantando o Serviço de Banco de Dados com Docker

Inicie o container do PostgreSQL em segundo plano (`-d`) usando o `docker-compose.yml` do projeto:

```bash
docker-compose up -d
```

### 3.2. Aplicar Migrações do Banco de Dados

Com o banco de dados rodando (e o ambiente virtual **ativo**), execute as migrações para criar as tabelas necessárias:

```bash
python manage.py migrate
```

### 3.3. Iniciar o Servidor de Desenvolvimento

Inicie a aplicação localmente.

```bash
python manage.py runserver
```

**A aplicação estará disponível em:**
👉 `http://127.0.0.1:8000`

### 3.4. Desativar o Ambiente Virtual

Quando terminar de trabalhar, você pode desativar o ambiente virtual:

```bash
deactivate
```

-----