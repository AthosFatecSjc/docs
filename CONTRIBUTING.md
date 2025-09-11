# Guia de Padrão de Commit

## 1. Padrão de Escrita

Todo o conteúdo deve ser escrito em letras minúsculas, exceto os IDs da Tarefa.
Os IDs devem ser separados por espaço.

## 2. Boas Práticas de Commit

Prefira realizar commits pequenos e frequentes, cada um rastreando a Tarefa correspondente.
Evite concentrar várias tarefas em um único commit.
Escreva a descrição em português para seguirmos um padrão.
Exemplo: `git commit -m "ATHOS-1 feat(autenticação): adicionar endpoint de login"`

---

Onde encontrar esses elementos para realizar o commit:

* **ID da Task**: identificador da tarefa no Jira, no formato `ATHOS-00`.
* **Tipo**: define o tipo de alteração realizada (ex.: `feat`, `fix`, `docs`, `test`).
* **Escopo**: indica a parte do código impactada pela alteração (ex.: um módulo, função ou componente específico).
* **Descrição**: resumo curto e objetivo da mudança implementada.

---

### Usar template de Commit
### Exemplo de commit e onde achar o ID da tarefa no Jira:

<img width="1210" height="1203" alt="image" src="https://github.com/user-attachments/assets/0727a76b-0106-4188-b306-94e286e5cb18" />



---

## 3. Tipos e Exemplos de commits

* **feat**: usado para novas funcionalidades.  
  * Exemplo: `ATHOS-01 feat(autenticacao): adiciona endpoint de login`

* **fix**: usado para correções de bugs.  
  * Exemplo: `ATHOS-01 fix(usuario): corrige upload da foto de perfil`

* **chore**: usado para tarefas de manutenção, sem impacto direto no código de produção.  
  * Exemplo: `ATHOS-01 chore(deps): atualiza dependências do projeto`

* **docs**: usado para alterações em documentação.  
  * Exemplo: `ATHOS-01 docs(readme): atualiza instruções de instalação`

* **style**: usado para ajustes de formatação e estilo, sem alterar comportamento do código.  
  * Exemplo: `ATHOS-01 style(css): corrige indentação no main.css`

* **refactor**: usado para refatoração de código, sem corrigir bugs nem adicionar funcionalidades.  
  * Exemplo: `ATHOS-01 refactor(servico-usuario): remove verificações redundantes`

* **perf**: usado para melhorias de desempenho.  
  * Exemplo: `ATHOS-01 perf(api): reduz tempo de resposta do endpoint de busca`

* **test**: usado para adição ou ajuste de testes.  
  * Exemplo: `ATHOS-01 test(usuario): adiciona testes unitários para atualização de perfil`

* **build**: usado para mudanças em sistema de build ou dependências externas.  
  * Exemplo: `ATHOS-01 build(docker): adiciona configuração Docker para deployment`

* **ci**: usado para mudanças em arquivos ou scripts de integração contínua (CI).  
  * Exemplo: `ATHOS-01 ci(workflow): atualiza workflow do GitHub Actions para PRs`

* **revert**: usado para reverter um commit anterior.  
  * Exemplo: `ATHOS-01 revert(auth): reverte "feat(auth): adiciona endpoint de login com JWT"`

* **hotfix**: usado para correções urgentes em produção.  
  * Exemplo: `ATHOS-01 hotfix(usuario): corrige falha ao registrar usuário`

---

## 4. Padrão de Escrita Feature Branch

Uma **feature branch** é uma ramificação criada a partir da branch principal com o objetivo de desenvolver uma nova funcionalidade ou melhoria no projeto. Ela serve para isolar o desenvolvimento, garantindo que as alterações em andamento não interfiram no trabalho de outros desenvolvedores nem afetem a estabilidade do código principal.

O nome da branch deve ser escrito em letras minúsculas, iniciando com o ID da Task (letras maiúsculas), separados por barra (`/`). Não são permitidos espaços; portanto, a descrição da funcionalidade deve ser escrita utilizando hífens (`-`) entre as palavras.

Exemplo: `git checkout -b ATHOS-1/criar-tela-de-login`

### Padrão Fix Branch

Uma **fix branch** é uma ramificação criada a partir da branch principal com o objetivo de corrigir um bug ou erro no código. Diferente da feature branch, seu foco está em ajustes e reparos, e não na adição de novas funcionalidades.
O nome da branch deve ser escrito em letras minúsculas, incluindo o ID da Task (letras maiúsculas), separados por barra (`/`). Não deve haver espaços; por isso, a descrição da correção deve utilizar hífens (`-`) entre as palavras.

Exemplo: `git checkout -b ATHOS-1/correção-do-modal-de-cadastro-de-usuário`
`git switch -c ATHOS-1/criar-tela-de-login`

---

## 5. Por que seguimos esse padrão?

Vamos usar um fluxo parecido com o gitflow. Esse modelo foi pensado para melhor:

* **Isolamento**: as alterações feitas em uma feature ou fix branch ficam separadas da branch principal até estarem finalizadas e aprovadas, evitando impacto no código estável.
* **Colaboração**: permite que vários desenvolvedores trabalhem em diferentes funcionalidades e correções ao mesmo tempo, sem conflitos diretos entre si.
* **Controle de qualidade**: garante que cada mudança passe por revisão de código e testes antes de ser integrada à branch principal, preservando a estabilidade do projeto.

---

## 6. Pull Request

Após concluir a funcionalidade ou correção, deve-se abrir uma Pull Request (PR) contendo todos os IDs envolvidos (Tasks) e uma descrição detalhada das implementações realizadas.
Essa solicitação pode ser feita diretamente pelo Jira, na opção de Pull Request, ou pelo GitHub.
**Importante**: cada tarefa deve ter sua própria Pull Request para evitarmos problemas.
Exemplo: `ATHOS-1 Atualiza as tabelas do banco.`
