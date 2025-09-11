# Documento de Orientação da Estrutura do Projeto

## 1\. Introdução

Este documento tem como objetivo descrever a organização estrutural do projeto server, desenvolvido em Python com o framework Django. A estrutura proposta segue o padrão MTV (Model-Template-View), com separação de responsabilidades em módulos específicos, visando facilitar a manutenção, escalabilidade e entendimento por parte da equipe de desenvolvimento.

-----

## 2\. Estrutura Geral

```
meu_projeto/
├── manage.py
├── requirements.txt
├── .env
├── config/
├── apps/
├── templates/
├── static/
├── media/
└── tests/
```

-----

## 3\. Descrição dos Diretórios e Arquivos

### 3.1 Raiz do Projeto

  * **`manage.py`**: Script principal de linha de comando do Django. Permite executar comandos como migrações, criação de usuários e inicialização do servidor.
  * **`requirements.txt`**: Lista das dependências necessárias para execução do projeto.
  * **`.env`**: Arquivo de variáveis de ambiente, utilizado para armazenar informações sensíveis (chaves secretas, credenciais de banco de dados, etc).

### 3.2 Configurações – `config/`

Contém os arquivos de configuração global do projeto.

```
config/
├── __init__.py
├── settings.py
├── urls.py
├── asgi.py
└── wsgi.py
```

  * **`settings.py`**: Configurações gerais do projeto, incluindo banco de dados, apps instaladas, middlewares, templates e arquivos estáticos.
  * **`urls.py`**: Define o roteamento global do projeto. Inclui as rotas principais e referencia as rotas de cada aplicação.
  * **`asgi.py`**: Arquivo de inicialização para deploy utilizando ASGI (suporte a aplicações assíncronas).
  * **`wsgi.py`**: Arquivo de inicialização para deploy utilizando WSGI (modo tradicional).

### 3.3 Aplicações – `apps/`

Contém todas as aplicações modulares do projeto. Cada aplicação representa um domínio de negócio.

#### 3.3.1 Aplicação `usuarios/`

Responsável pelo gerenciamento de autenticação e perfis de usuários.

```
usuarios/
├── models.py
├── forms.py
├── views.py
├── services.py
├── urls.py
├── templates/usuarios/
└── static/usuarios/
```

  * **`models.py`**: Define os modelos de usuário, perfil e permissões.
  * **`forms.py`**: Formulários relacionados a cadastro, login e edição de perfil.
  * **`views.py`**: Controladores que processam requisições e retornam respostas (telas de login, cadastro, etc).
  * **`services.py`**: Implementa regras de negócio específicas (ex.: redefinição de senha, validações extras).
  * **`urls.py`**: Define as rotas relacionadas a usuários.
  * **`templates/usuarios/`**: Contém os arquivos HTML específicos da aplicação (login, cadastro, perfil).
  * **`static/usuarios/`**: Arquivos estáticos (CSS, JS, imagens) específicos da aplicação.

#### 3.3.2 Aplicação `relatorios/`

Aplicação principal responsável pela geração e exibição de relatórios. Está organizada em submódulos, cada um correspondente a uma User Story definida no backlog.

```
relatorios/
├── urls.py
├── produtividade/
├── atividades/
└── comparacao/
```

  * **`urls.py`**: Roteamento das funcionalidades de relatórios.

**a) Submódulo `produtividade/`**
Relatório de produtividade diária/mensal (User Story 1.1).

  * **`views.py`**: Controla a renderização do relatório.
  * **`services.py`**: Implementa regras de negócio (cálculo de produtividade).
  * **`exporters.py`**: Exportação de relatórios para diferentes formatos (ex.: PDF, CSV).
  * **`templates/produtividade/`**: Interface HTML do relatório.
  * **`static/produtividade/`**: Arquivos estáticos (gráficos, scripts, estilos).

**b) Submódulo `atividades/`**
Relatório de atividades por projeto (User Story 1.2).

  * **`views.py`**: Controla a exibição do relatório de atividades.
  * **`services.py`**: Consolidação de horas por projeto e usuário.
  * **`exporters.py`**: Exportação para formatos externos.
  * **`templates/atividades/`**: Interface HTML.
  * **`static/atividades/`**: Arquivos estáticos.

**c) Submódulo `comparacao/`**
Relatório de comparação entre horas previstas e realizadas (User Story 1.3).

  * **`views.py`**: Exibição dos dados comparativos.
  * **`services.py`**: Implementação da lógica de comparação.
  * **`exporters.py`**: Exportação para diferentes formatos.
  * **`templates/comparacao/`**: Interface HTML.
  * **`static/comparacao/`**: Arquivos estáticos.

### 3.4 Templates Globais – `templates/`

```
templates/
├── base.html
├── navbar.html
└── dashboard.html
```

  * **`base.html`**: Layout principal do sistema, herdado pelos demais templates.
  * **`navbar.html`**: Barra de navegação principal.
  * **`dashboard.html`**: Tela inicial após login do usuário.

### 3.5 Arquivos Estáticos Globais – `static/`

```
static/
├── css/
│   └── main.css
├── js/
│   └── main.js
└── img/
```

Arquivos estáticos compartilhados entre todas as aplicações (ex.: estilos globais, scripts comuns, imagens).

### 3.6 Media – `media/`

Diretório destinado ao armazenamento de arquivos enviados por usuários (ex.: imagens de perfil, anexos).

### 3.7 Testes – `tests/`

```
tests/
├── unit/
└── integration/
```

  * **`unit/`**: Testes unitários que validam cada aplicação de forma isolada.
  * **`integration/`**: Testes de integração que validam o funcionamento conjunto entre aplicações e banco de dados.