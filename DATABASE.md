# 📊 Documentação do Banco de Dados

## 🧭 Visão Geral da Arquitetura de Dados

A aplicação utiliza **duas bases de dados complementares**, com papéis distintos dentro do ecossistema:

| Componente                      | Modelo                  | Função Principal                                               | Características                                                |
| ------------------------------- | ----------------------- | -------------------------------------------------------------- | -------------------------------------------------------------- |
| 🧱 **Banco Operacional (OLTP)** | Entidade-Relacionamento | Base de uso diário dos desenvolvedores e integração com o Jira | Dados tratados e normalizados, operações CRUD constantes       |
| ❄️ **Data Warehouse (OLAP)**    | Snowflake               | Armazenamento analítico para relatórios e dashboards           | Dados espelhados, cargas diárias via ETL, consultas otimizadas |

* O **OLTP** (PostgreSQL) recebe e mantém os dados em tempo real.
* O **Snowflake** é atualizado **1x por dia** por um processo de **ETL automatizado**, refletindo o estado do OLTP para análises históricas e gerenciais.
* Operações de escrita complexas **ocorrem apenas no OLTP**; o OLAP é **somente leitura**, com dados organizados em **dimensões e fatos**.

---

## ❄️ Modelo Snowflake (Data Warehouse - OLAP)

<div align="center">
  <img src="img/snowflake.png" alt="Modelo Snowflake do Data Warehouse" width="800" />
</div>

### 🗂️ Estrutura das Tabelas

#### Tabela `dim_cargo`

* **Descrição:** Dimensão de cargos.
* **Colunas:**

  | Coluna       | Tipo        | Propriedades | Descrição           |
  | ------------ | ----------- | ------------ | ------------------- |
  | `id`         | bigint      | PK           | Identificador único |
  | `nome_cargo` | varchar(20) | unique       | Nome do cargo       |

#### Tabela `dim_projeto`

* **Descrição:** Dimensão de projetos.
* **Colunas:**

  | Coluna         | Tipo         | Propriedades | Descrição                  |
  | -------------- | ------------ | ------------ | -------------------------- |
  | `id`           | bigint       | PK           | Identificador único        |
  | `nome_projeto` | varchar(100) |              | Nome do projeto            |
  | `data_criacao` | date         | null         | Data de criação (opcional) |

#### Tabela `dim_funcionario`

* **Descrição:** Dimensão de funcionários.
* **Colunas:**

  | Coluna             | Tipo                 | Propriedades | Descrição                        |
  | ------------------ | -------------------- | ------------ | -------------------------------- |
  | `id`               | bigint               | PK           | Identificador único              |
  | `nome_funcionario` | varchar(100)         |              | Nome completo                    |
  | `time`             | varchar(100)         | null         | Squad / equipe                   |
  | `data_contratacao` | date                 | null         | Data de contratação              |
  | `fk_cargo`         | FK → dim_cargo       | null         | Cargo                            |
  | `fk_gerente`       | FK → dim_funcionario | null         | Auto-relacionamento para gerente |

#### Tabela `dim_tempo`

* **Descrição:** Dimensão de tempo (gerada por ETL).
* **Colunas:**

  | Coluna          | Tipo        | Propriedades | Descrição             |
  | --------------- | ----------- | ------------ | --------------------- |
  | `id`            | int         | PK           | Identificador da data |
  | `data_completa` | date        | unique       | Data completa         |
  | `ano`           | int         |              | Ano                   |
  | `trimestre`     | varchar(2)  |              | Trimestre             |
  | `mes`           | int         |              | Número do mês         |
  | `nome_mes`      | varchar(20) |              | Nome do mês           |
  | `dia_do_mes`    | int         |              | Dia do mês            |
  | `dia_da_semana` | varchar(20) |              | Nome do dia da semana |

#### Tabela `fato_registro_horas`

* **Descrição:** Fato principal — registro de horas por projeto, funcionário e data.
* **Colunas:**

  | Coluna           | Tipo                 | Propriedades | Descrição             |
  | ---------------- | -------------------- | ------------ | --------------------- |
  | `id`             | bigint               | PK           | Identificador único   |
  | `fk_projeto`     | FK → dim_projeto     | null         | Projeto               |
  | `fk_funcionario` | FK → dim_funcionario | null         | Funcionário           |
  | `fk_data`        | FK → dim_tempo       | null         | Data                  |
  | `horas_gastas`   | decimal(6,2)         | default 0    | Total de horas gastas |

---

## ⚙️ Processo de ETL Diário

O pipeline ETL (Extract, Transform, Load) foi implementado como um **comando customizado do Django**, responsável por:

1. **Limpar as tabelas OLAP** → garante consistência a cada carga.
2. **Gerar DimTempo** → calendário completo para análises temporais.
3. **Extrair e carregar dimensões** (`DimCargo`, `DimProjeto`, `DimFuncionario`) a partir do banco OLTP.
4. **Carregar fatos** (`FatoRegistroHoras`) com base em registros de horas do banco OLTP.

```bash
python manage.py etl_olap
```

📝 O processo está versionado e roda contra o banco `olap` configurado no `DATABASES` do Django.

---

## 🧪 Validação de Funcionamento

Após execução do ETL, os dados ficam disponíveis no Snowflake e podem ser consultados diretamente:

```sql
-- Total de horas por projeto no Data Warehouse
SELECT p.nome_projeto, SUM(f.horas_gastas) AS total_horas
FROM fato_registro_horas f
LEFT JOIN dim_projeto p ON p.id = f.fk_projeto
GROUP BY p.nome_projeto
ORDER BY total_horas DESC;
```

✅ Testes executados confirmam:

* Integridade referencial mantida nas dimensões.
* Fato populado corretamente com dados históricos.
* Consultas analíticas (ex. agregações mensais, por time ou por cargo) executam com performance estável.

---

## 🧱 Modelo Entidade-Relacionamento (OLTP)

> ⚠️ Esta seção é **secundária** e serve principalmente para referência técnica dos desenvolvedores e integração com o Jira.

<div align="center">
  <img src="img/banco.png" alt="Modelo Lógico do Banco de Dados OLTP" width="800" />
</div>

**Motor do Banco de Dados:** PostgreSQL
**Versão:** 2.4

-----

### Tabela de Conteúdo

1.  [Tabelas](#tabelas)
      - [cargo](#cargo)
      - [projeto](#projeto)
      - [meta\_tempo\_controle](#meta_tempo_controle)
      - [controle\_horas\_equipe\_resumo](#controle_horas_equipe_resumo)
      - [funcionario](#funcionario)
      - [controle\_tempo\_equipe](#controle_tempo_equipe)
      - [controle\_tempo\_resumo](#controle_tempo_resumo)
      - [controle\_horas\_equipe](#controle_horas_equipe)
      - [funcionario\_funcionario](#funcionario_funcionario)
3.  [Referências](#referencias)
      - [funcionario\_funcionario\_funcionario](#funcionario_funcionario_funcionario)
      - [funcionario\_funcionario\_funcionario\_2](#funcionario_funcionario_funcionario_2)
      - [funcionario\_cargo](#funcionario_cargo)
      - [controle\_tempo\_equipe\_funcionario](#controle_tempo_equipe_funcionario)
      - [controle\_tempo\_equipe\_controle\_tempo\_resumo](#controle_tempo_equipe_controle_tempo_resumo)
      - [controle\_horas\_equipe\_funcionario](#controle_horas_equipe_funcionario)
      - [controle\_horas\_equipe\_projeto](#controle_horas_equipe_projeto)
      - [meta\_tempo\_controle\_controle\_tempo\_equipe](#meta_tempo_controle_controle_tempo_equipe)
      - [controle\_horas\_equipe\_resumo\_controle\_horas\_equipe](#controle_horas_equipe_resumo_controle_horas_equipe)
4.  [Sequências](#sequences)

-----

### Tabelas

#### cargo

  - **Descrição:** Armazena os diferentes cargos de funcionários.
  - **Colunas:**
    | Coluna | Tipo | Propriedades | Descrição |
    | :--- | :--- | :--- | :--- |
    | `id` | int | PK | Chave primária. Auto-incrementada, gerada automaticamente. |
    | `sigla` | varchar(%) | | Representa a sigla ou nome do cargo. |

#### projeto

  - **Descrição:** Armazena informações sobre projetos.
  - **Colunas:**
    | Coluna | Tipo | Propriedades | Descrição |
    | :--- | :--- | :--- | :--- |
    | `id` | int | PK | Chave primária. Auto-incrementada, gerada automaticamente. |
    | `nome` | varchar(%) | | Nome do projeto. |
    | `data_criacao` | timestamp | | Data de criação do projeto. O valor é definido automaticamente na criação do registro. |

#### meta\_tempo\_controle

  - **Descrição:** Define metas de tempo para diferentes categorias de funcionários.
  - **Colunas:**
    | Coluna | Tipo | Propriedades | Descrição |
    | :--- | :--- | :--- | :--- |
    | `id` | int | PK | |
    | `objetivo_clt` | int | null | Objetivo de tempo para funcionários CLT. O campo pode ficar em branco. |
    | `objetivo_estagiario`| int | null | Objetivo de tempo para estagiários. O campo pode ficar em branco. |
    | `controle_tempo_equipe_id`| int | | Chave primária. Auto-incrementada, gerada automaticamente. |

#### controle\_horas\_equipe\_resumo

  - **Descrição:** Resumo das horas totais por equipe.
  - **Colunas:**
    | Coluna | Tipo | Propriedades | Descrição |
    | :--- | :--- | :--- | :--- |
    | `id` | int | PK | Chave primária. Auto-incrementada, gerada automaticamente. |
    | `total_dev` | decimal(%,%) | null | Total de horas de desenvolvimento, com 2 casas decimais e valor padrão 0. |
    | `total_projeto`| decimal(%,%) | null | Total de horas do projeto, com 2 casas decimais e valor padrão 0. |
    | `controle_horas_equipe_id`| int | | Chave primária. Auto-incrementada, gerada automaticamente. |

#### funcionario

  - **Descrição:** Tabela central de funcionários.
  - **Colunas:**
    | Coluna | Tipo | Propriedades | Descrição |
    | :--- | :--- | :--- | :--- |
    | `id` | int | PK | Chave primária. Auto-incrementada, gerada automaticamente. |
    | `nome` | varchar(%) | | Nome completo do funcionário. |
    | `time` | int | null | Time ao qual o funcionário pertence. O campo pode ficar em branco. |
    | `data_criacao` | timestamp | | Data de criação do registro do funcionário. |
    | `cargo_id` | int | | ForeignKey(to='relatorios.cargo', on\_delete=SET\_NULL, null=True). Relacionamento: Um Funcionario tem um Cargo (relação muitos-para-um). Comportamento de Exclusão: Se um Cargo for deletado, o campo cargo do funcionário será definido como NULL. |
    | `gerente_id` | int | | Relacionamento: Um Funcionario pode ter um Gerente, que também é um Funcionario (relação de auto-referência). Comportamento de Exclusão: Se um Funcionario (gerente) for deletado, o campo gerente dos seus subordinados será definido como NULL. related\_name: Permite acessar os subordinados de um gerente com gerente.subordinados.all(). |
    | `cargo_id` | int | | Chave primária. Auto-incrementada, gerada automaticamente. |
    | `controle_tempo_equipe_id`| int | | Chave primária. Auto-incrementada, gerada automaticamente. |
    | `controle_horas_equipe_id`| int | | Chave primária. Auto-incrementada, gerada automaticamente. |

#### controle\_tempo\_equipe

  - **Descrição:** Registra o tempo gasto por cada funcionário.
  - **Colunas:**
    | Coluna | Tipo | Propriedades | Descrição |
    | :--- | :--- | :--- | :--- |
    | `id` | int | PK | Chave primária. Auto-incrementada, gerada automaticamente. |
    | `dia_semana` | varchar(%) | | Dia da semana em que o tempo foi gasto. |
    | `dia_mes` | int | | O dia do mês. |
    | `mes` | date | | O mês em que o registro foi feito. |
    | `tempo_gasto` | decimal(%,%) | | Quantidade de tempo gasto, com 2 casas decimais. |
    | `funcionario_id`| int | | Relacionamento: Cada registro está associado a um Funcionario (relação muitos-para-um). Comportamento de Exclusão: Se um Funcionario for deletado, todos os seus registros de tempo nesta tabela também serão excluídos. |
    | `meta_id` | int | | Relacionamento: O registro pode ser associado a uma MetaTempoControle. Comportamento de Exclusão: Se uma MetaTempoControle for deletada, o campo meta será definido como NULL. |
    | `controle_tempo_resumo_id`| int | | Chave primária. Auto-incrementada, gerada automaticamente. |

#### controle\_tempo\_resumo

  - **Descrição:** Resumo dos valores de controle de tempo.
  - **Colunas:**
    | Coluna | Tipo | Propriedades | Descrição |
    | :--- | :--- | :--- | :--- |
    | `id` | int | PK | Chave primária. Auto-incrementada, gerada automaticamente. |
    | `realizado_equipe`| decimal(%,%) | | Tempo realizado pela equipe. |
    | `total_meta` | decimal(%,%) | | Total de tempo da meta. |
    | `total_real` | decimal(%,%) | | Total de tempo real. |
    | `aproveitamento`| decimal(%,%) | | Porcentagem de aproveitamento. |
    | `controle_tempo_equipe_id`| int | | Relacionamento: Cada resumo está diretamente ligado a um registro de TempoGastoEquipe. Comportamento de Exclusão: Se um registro de tempo gasto for excluído, seu resumo também será. |

#### controle\_horas\_equipe

  - **Descrição:** Controla as horas gastas por funcionário em um projeto específico durante um determinado mês.
  - **Colunas:**
    | Coluna | Tipo | Propriedades | Descrição |
    | :--- | :--- | :--- | :--- |
    | `id` | int | PK | Chave primária. Auto-incrementada, gerada automaticamente. |
    | `mes` | date | | O mês em que as horas foram registradas. |
    | `horas` | decimal(%,%) | | Quantidade de horas gastas. |
    | `resumo_id` | int | | Relacionamento: Pode ter uma relação com a tabela de resumo de horas, mas não é obrigatório. |
    | `funcionario_id`| int | | Relacionamento: Vincula as horas a um Funcionario. Comportamento de Exclusão: Exclui os registros de horas se o funcionário for deletado. |
    | `projeto_id` | int | | Relacionamento: Vincula as horas a um Projeto. Comportamento de Exclusão: Exclui os registros de horas se o projeto for deletado. |
    | `projeto_id` | int | | Chave primária. Auto-incrementada, gerada automaticamente. |

#### funcionario\_funcionario

  - **Colunas:**
    | Coluna | Tipo | Propriedades | Descrição |
    | :--- | :--- | :--- | :--- |
    | `funcionario_id`| int | PK | Chave primária. Auto-incrementada, gerada automaticamente. |
    | `funcionario_id`| int | PK | Chave primária. Auto-incrementada, gerada automaticamente. |

-----

### Referências

#### funcionario\_funcionario\_funcionario

  - **Colunas:** `funcionario.id` \<-\> `funcionario_funcionario.funcionario_id`

#### funcionario\_funcionario\_funcionario\_2

  - **Colunas:** `funcionario.id` \<-\> `funcionario_funcionario.funcionario_id`

#### funcionario\_cargo

  - **Descrição:** Um Funcionario tem um Cargo (relação muitos-para-um).
  - **Colunas:** `cargo.id` \<-\> `funcionario.cargo_id`

#### controle\_tempo\_equipe\_funcionario

  - **Descrição:** Cada registro está associado a um Funcionario (relação muitos-para-um).
  - **Colunas:** `controle_tempo_equipe.id` \<-\> `funcionario.controle_tempo_equipe_id`

#### controle\_tempo\_equipe\_controle\_tempo\_resumo

  - **Colunas:** `controle_tempo_resumo.id` \<-\> `controle_tempo_equipe.controle_tempo_resumo_id`

#### controle\_horas\_equipe\_funcionario

  - **Descrição:** Vincula as horas a um Funcionario.
  - **Colunas:** `controle_horas_equipe.id` \<-\> `funcionario.controle_horas_equipe_id`

#### controle\_horas\_equipe\_projeto

  - **Descrição:** Vincula as horas a um Projeto.
  - **Colunas:** `projeto.id` \<-\> `controle_horas_equipe.projeto_id`

#### meta\_tempo\_controle\_controle\_tempo\_equipe

  - **Descrição:** O registro pode ser associado a uma MetaTempoControle.
  - **Colunas:** `controle_tempo_equipe.id` \<-\> `meta_tempo_controle.controle_tempo_equipe_id`

#### controle\_horas\_equipe\_resumo\_controle\_horas\_equipe

  - **Descrição:** Vincula as horas a um Projeto.
  - **Colunas:** `controle_horas_equipe.id` \<-\> `controle_horas_equipe_resumo.controle_horas_equipe_id`

-----

### Sequências

| Nome da Sequência | Início | Descrição |
| :--- | :--- | :--- |
| `cargo_seq` | 1 | |
| `projeto_seq` | 1 | |
| `meta_tempo_controle_seq` | 1 | |
| `controle_horas_equipe_resumo_seq` | 1 | |
| `funcionario_seq` | 1 | |
| `controle_tempo_equipe_seq` | 1 | |
| `controle_tempo_resumo_seq` | 1 | |
| `controle_horas_equipe_seq` | 1 | |

---

## 📝 Conclusão

* O **modelo Snowflake** é a base **principal para relatórios, dashboards e análises**.
* O **modelo Entidade-Relacionamento** permanece como a camada de captura e tratamento operacional.
* O ETL conecta esses dois mundos, garantindo dados consistentes e prontos para exploração analítica diária.

---