# Documento de Orientação da Estrutura do Projeto (Atualizado)

## 1\. Introdução

Este documento tem como objetivo descrever a organização estrutural do projeto server, desenvolvido em Python com o framework **Django**. A estrutura proposta segue o padrão MTV (**Model-Template-View**), com separação de responsabilidades em módulos específicos, visando facilitar a manutenção, escalabilidade e entendimento por parte da equipe de desenvolvimento.

-----

## 2\. Estrutura Geral

A estrutura do projeto é a seguinte, a partir do diretório raiz:

```
SERVER/ (Raiz do Projeto)
├── .github/
├── .vscode/
├── apps/
│   ├── usuarios/
│   ├── relatorios/
│   └── dashboards/
├── config/
├── olap_models/
├── banco/
├── migrations/
├── templates/
├── static/
├── .env
├── .env.example
├── .gitignore
├── docker-compose.yml
├── manage.py
├── README.md
├── requirements.txt
└── sonar-project.properties
```

-----

## 3\. Descrição dos Diretórios e Arquivos

### 3.1 Raiz do Projeto (`SERVER/`)

  * **`manage.py`**: Script principal de linha de comando do Django. Permite executar comandos como migrações, criação de usuários e inicialização do servidor.
  * **`requirements.txt`**: Lista das dependências Python necessárias para execução do projeto.
  * **`.env`**: Arquivo de variáveis de ambiente, utilizado para armazenar informações sensíveis (chaves secretas, credenciais de banco de dados, etc). **(Ignorado pelo `.gitignore`)**
  * **`.env.example`**: Exemplo do arquivo `.env` para referência, sem valores sensíveis.
  * **`.gitignore`**: Define arquivos e diretórios que devem ser ignorados pelo controle de versão Git.
  * **`docker-compose.yml`**: Arquivo de configuração para orquestração de containers Docker (servidor, banco de dados, etc.).
  * **`README.md`**: Documento de introdução e instruções básicas para a configuração e execução do projeto.
  * **`sonar-project.properties`**: Arquivo de configuração para a ferramenta de análise estática de código **SonarQube/SonarCloud**.
  * **`.github/`**: Contém arquivos de configuração para **GitHub Actions** (CI/CD) ou outros recursos do GitHub.
  * **`.vscode/`**: Contém configurações específicas para o ambiente de desenvolvimento **VS Code**.

### 3.2 Configurações – `config/`

Contém os arquivos de configuração global do projeto.

```
config/
├── __init__.py
├── settings.py
├── urls.py
├── asgi.py
├── wsgi.py
└── routers.py (NOVO)
```

  * **`settings.py`**: Configurações gerais do projeto, incluindo banco de dados, apps instaladas, middlewares, templates e arquivos estáticos.
  * **`urls.py`**: Define o roteamento global do projeto. Inclui as rotas principais e referencia as rotas de cada aplicação.
  * **`asgi.py`**: Arquivo de inicialização para deploy utilizando ASGI (suporte a aplicações assíncronas).
  * **`wsgi.py`**: Arquivo de inicialização para deploy utilizando WSGI (modo tradicional).
  * **`routers.py` (NOVO)**: Possivelmente usado para definir roteadores de API (com **Django Rest Framework**) ou para configurar o roteamento de banco de dados (`DATABASE_ROUTERS`).

### 3.3 Aplicações – `apps/`

Contém todas as aplicações modulares do projeto. Cada aplicação representa um domínio de negócio.

#### 3.3.1 Aplicação `usuarios/`

Responsável pelo gerenciamento de autenticação e perfis de usuários.

```
usuarios/
├── __init__.py
├── admin.py
├── apps.py
├── models.py
├── services.py
├── tests.py
├── urls.py
├── views.py
├── templates/
└── static/
```

  * **`admin.py`**: Registra os modelos no painel administrativo do Django.
  * **`apps.py`**: Configuração da aplicação.
  * **`views.py`**: Controladores que processam requisições e retornam respostas.
  * **`services.py`**: Implementa regras de negócio específicas.
  * **`urls.py`**: Define as rotas relacionadas a usuários.
  * **`models.py`**: Define os modelos de usuário, perfil e permissões.
  * **`tests.py`**: Implementação dos testes unitários e de integração da aplicação.
  * **`templates/`**: Contém os arquivos HTML específicos da aplicação.
  * **`static/`**: Arquivos estáticos (CSS, JS, imagens) específicos da aplicação.

#### 3.3.2 Aplicação `relatorios/`

Aplicação principal responsável pela geração e exibição de relatórios.

```
relatorios/
├── __init__.py
├── admin.py
├── apps.py
├── models.py
├── urls.py
├── views.py
├── atividade/
├── comparacao/
├── produtividade/
├── migrations/
├── templates/
└── static/
```

  * **Estrutura interna similar a `usuarios/`**, mas com submódulos para organização de funcionalidades complexas.
  * **`atividade/`, `comparacao/`, `produtividade/`**: Submódulos organizacionais, conforme o plano original, contendo arquivos como `views.py`, `services.py`, `exporters.py` e seus próprios diretórios `templates/` e `static/`.

#### 3.3.3 Aplicação `dashboards/` (NOVO)

Responsável por telas de visualização de dados agregados (painéis/dashboards).

```
dashboards/
├── __init__.py
├── admin.py
├── apps.py
├── models.py
├── services.py
├── tests.py
├── urls.py
├── views.py
├── templates/
└── static/
```

  * **Estrutura Padrão**: Segue o padrão de aplicação do Django, tratando o domínio de visualização de dashboards.

### 3.4 Outros Diretórios de Alto Nível

  * **`olap_models/` (NOVO)**: Diretório dedicado a modelos de dados otimizados para consultas analíticas (OLAP).

    ```
    olap_models/
    ├── __init__.py
    ├── management/
    │   ├── __init__.py
    │   └── commands/
    │       ├── __init__.py
    │       └── rodar_etl.py (NOVO)
    ```

      * **`management/commands/`**: Diretório padrão do Django para criação de comandos customizados.
      * **`rodar_etl.py`**: Comando customizado que provavelmente executa o processo de **ETL (Extract, Transform, Load)** para popular os modelos de dados analíticos.

  * **`banco/` (NOVO)**: Diretório destinado a scripts de banco de dados.

      * **`insert.sql`**: Script SQL com comandos de `INSERT`, geralmente para dados iniciais (fixtures) ou de teste.

  * **`migrations/`**: Contém os arquivos de migração de banco de dados gerados pelo Django para rastrear alterações nos modelos.

  * **`templates/`**: Diretório de templates **globais** (ex.: `base.html`, `dashboard.html`).

  * **`static/`**: Diretório de arquivos estáticos **globais** (CSS, JS, imagens compartilhadas).

-----