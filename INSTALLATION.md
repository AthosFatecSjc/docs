# Documentação Técnica de Instalação e Inicialização

## Manual de Configuração e Execução da Aplicação

Este documento técnico detalha o procedimento para configurar o ambiente de desenvolvimento, instalar as dependências e inicializar a aplicação. O manual é destinado a desenvolvedores que precisam replicar o ambiente de produção em uma máquina local.

-----

### 1\. Pré-requisitos do Sistema

Antes de iniciar a instalação, certifique-se de que os seguintes componentes estão instalados em seu sistema operacional:

  * **Python 3.6+**: O projeto requer uma versão do Python 3.
  * **Git**: Ferramenta de controle de versão para clonagem do repositório.
  * **PostgreSQL**: Banco de dados utilizado pela aplicação. É necessário ter acesso a um servidor PostgreSQL na nuvem e suas credenciais.

-----

### 2\. Configuração do Ambiente Local

Esta seção aborda a preparação do ambiente Python, que é fundamental para isolar as dependências do projeto.

#### 2.1. Clonagem do Repositório

Use o Git para clonar o projeto e acessar a pasta raiz:

```bash
git clone https://github.com/AthosFatecSjc/server.git
cd server
```

#### 2.2. Criação e Ativação do Ambiente Virtual

Crie e ative um ambiente virtual para o projeto. Isso garante que as bibliotecas não entrem em conflito com outros projetos.

  * **Windows**:
    ```bash
    py -m venv venv
    .\venv\Scripts\activate
    ```
  * **Linux/macOS**:
    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```

-----

### 3\. Instalação das Dependências

Com o ambiente virtual ativo, instale as dependências listadas em `requirements.txt`:

```bash
pip install -r requirements.txt
```

-----

### 4\. Configuração do Banco de Dados (PostgreSQL na Nuvem)

O banco de dados é externo (nuvem) e deve ser configurado no arquivo `gestaohoras/settings.py`.

1.  Abra o arquivo `gestaohoras/settings.py`.
2.  Localize o dicionário `DATABASES`.
3.  Preencha com suas credenciais.

**Exemplo**:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'nome_do_seu_banco',
        'USER': 'seu_usuario',
        'PASSWORD': 'sua_senha',
        'HOST': 'endereco_do_servidor_na_nuvem.com',
        'PORT': '5432',
    }
}
```

-----

### 5\. Inicialização da Aplicação

#### 5.1. Aplicar Migrações

```bash
python manage.py migrate
```

#### 5.2. Iniciar o Servidor de Desenvolvimento

```bash
python manage.py runserver
```

A aplicação ficará disponível em:
👉 `http://127.0.0.1:8000`