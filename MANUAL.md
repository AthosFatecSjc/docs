<div align="center">
  
<img src="img/logo_athos.png" alt="API 5 SEMESTRE" width="300" height="300">

</div> 

# Necto / Projeto Athos Insight

* **Versão:** 2.0
* **Responsável Técnica:** Ruth Da Silva Mira
* **E-mail:** ruth.mira@fatec.sp.gov.br
* **Ambiente:** Microsoft Azure — http://20.39.132.228:8000/
* **Disponibilidade:** 12h às 00h (horário educacional)

---

## 1. Sobre o Sistema

O Athos Insight é uma ferramenta analítica desenvolvida para gestão de projetos, produtividade e custos de equipes de desenvolvimento.
O sistema consolida dados de sistemas de gestão (como o Jira) em um Data Warehouse, gerando relatórios, dashboards e indicadores de desempenho para apoiar decisões estratégicas. Hospedado em uma VM Microsoft Azure, o sistema é acessado via navegador, sem necessidade de instalação local.
O ambiente atual comporta até 30 usuários simultâneos, garantindo estabilidade para fins acadêmicos e operacionais.

## 2. Acesso ao Sistema

* ***Endereço:** http://20.39.132.228:8000/
* **Disponibilidade:** 12h às 00h

**Acesso inicial:**
* O usuário e a senha iniciais serão enviados via canal seguro (Slack).
* Após o primeiro login, o administrador poderá cadastrar novos usuários diretamente no sistema, conforme as permissões configuradas.

**Tela de Login**
* Autenticação por usuário e senha.
* Acesso controlado por perfis de permissão (Gerente, Líder, Membro).
* Sessões seguras via protocolo HTTPS.

## 3. Interface e Navegação

A interface do Athos Insight é composta por:
* Menu lateral: navegação entre dashboards e relatórios.
* Área principal: exibição dos dados, gráficos e relatórios.
* Cards de resumo: indicadores financeiros e de produtividade.
* Filtros: seleção de período, projeto e equipe.
* Botão “Exportar PDF”: gera relatórios e dashboards no formato PDF.

## 4. Funcionalidades Entregues

| Módulo | Descrição |
| --- | --- |
| Relatórios de Atividades e Horas | Relatórios diários, mensais e anuais por desenvolvedor e projeto, incluindo calendário, consolidados por projeto, gráficos e exportação em PDF. |
| Gerenciamento de Valor/Hora | Controle do valor/hora dos desenvolvedores, com edição restrita ao perfil Gerente. |
| Dashboard de Custos | Visão financeira com orçamento previsto, custo realizado, saldo, % utilizado, custo por desenvolvedor e exportação em PDF. |
| Dashboard de Issues | Quantidade de issues por status, custo médio, tabela com custos por desenvolvedor e filtros por projeto. |
| Dashboard de Bugs | Quantidade de bugs por status, custo médio, tabela por desenvolvedor e filtros por projeto. |
| Dashboard de Produtividade da Equipe | Gráficos comparativos de horas por dia e módulos, filtros avançados por equipe/período/desenvolvedor e exportação em PDF. |
| Integração com Banco de Dados Real | Substituição completa de dados mockados por consultas reais OLTP/OLAP, garantindo consistência entre filtros, relatórios e dashboards. |
| Integração com Jira | Importação de issues, bugs, tarefas, subtarefas, worklogs e usuários, incluindo criação de placeholders e sincronização de cargos. |
| Exportação em PDF | Exportação padronizada para todos os relatórios e dashboards (custos, produtividade, issues, bugs, atividades). |
| Gestão de Usuários | CRUD completo de usuários, perfis e cargos, com filtros combinados e permissões por perfil (Membro, Líder, Gerente). |
| Indicadores e Cálculos Automáticos | Cálculo automático de custos, horas, produtividade, distribuição por módulo e consolidação por projeto. |


## 5. Perfis de Usuário

| Perfil | Permissões |
| --- | --- |
| Gerente | Acesso total a relatórios e dashboards, incluindo edição de custos. |
| Líder de Equipe | Acesso parcial, sem editar valores. |
| Membro | Visualiza apenas indicadores gerais. |

## 6. Relatórios e Dashboards

**Relatório Diário/Mensal de Produtividade**
- Mostra horas lançadas por desenvolvedor em formato de calendário.
- Exibe metas e status diários (FO, FÉ, LI).
- Exportação em PDF.

**Relatório Mensal de Atividades por Projeto**
- Tabela consolidada com horas por projeto e colaborador.
- Gráfico de pizza com proporção de dedicação.
- Exportação em PDF.

**Relatório Comparativo Anual**
- Horas previstas x realizadas, com saldo e gráfico comparativo.
- Exportação em PDF.

**Dashboard de Custos**
- Cards com Orçamento Previsto, Custo Realizado, Saldo e Percentual Utilizado.
- Gráfico de barras com custo por desenvolvedor.
- Filtro por projeto.
- Exportação em PDF.

**Dashboard de Issues**
- Gráfico de pizza com quantidade de issues por status (Não iniciado, Em progresso, MR, Concluído).
- Card com custo médio das issues abertas.
- Tabela com ID da issue, desenvolvedor responsável e custo associado.
- Filtro por projeto.
- Exportação em PDF.

**Dashboard de Bugs**
- Gráfico de pizza com quantidade de bugs por status.
- Card com custo médio dos bugs em aberto.
- Tabela com ID do bug, desenvolvedor responsável e custo associado.
- Filtro por projeto.
- Exportação em PDF.

**Dashboard de Produtividade da Equipe**
- Gráfico de barras comparando horas trabalhadas por dia entre desenvolvedores.
- Gráfico empilhado com distribuição de horas por módulo/epic.
- Filtros por desenvolvedor, período e projeto.
- Exportação em PDF.

## 7. Ambiente Técnico

| Componente | Tecnologia |
| --- | --- |
| Backend (API) | Python + Django |
| Frontend | JavaScript + TailwindCSS |
| Banco de Dados | PostgreSQL + Docker |
| Infraestrutura | Docker + Cron Service |
| Hospedagem | Microsoft Azure (Ubuntu 22.04) |

## 8. Suporte e Contato

| Tipo | Informação |
| --- | --- |
| Canal | Slack do Athos |
| Tempo de resposta | Até 24h úteis |
| Atualizações | Entregues via release no ambiente Azure |
| Backup | Automático até o dia de encerramento do ano letivo |

## 9. Roadmap

* Integração completa com API Jira
* Relatórios de Atividade / Produtividade e de Comparação
* Gerenciamento de Valor/Hora do Desenvolvedor
* Dashboard de Saúde do Projeto (Custos.Issues e Bugs)
* Dashboard de Produtividade da Equipe (Módulo/Épico e Desenvolvedor)
* Módulo de Cadastro e Perfis Avançados

### Documentação Legal

- [Contrato De Prestação De Serviço (Sla)](pdf/Contrato%20De%20Prestação%20De%20Serviço%20(Sla).pdf)
- [Política De Privacidade E Segurança](pdf/Política%20De%20Privacidade%20E%20Segurança.pdf)
