# Case Processo Seletivo — Analista de Dados Produto & Growth
## Etapa 1 — Limpeza, Diagnóstico e Modelagem

## Objetivo

Preparar os dados para responder à pergunta de negócio proposta pelo Head de Produto:

> "O que está determinando se um usuário volta a jogar e o que podemos fazer para aumentar isso?"

---

# Dataset

O case é composto por três arquivos CSV.

| Arquivo | Função |
|----------|--------|
| palavritas_sessions.csv | Sessões de jogo (Tabela Fato) |
| palavritas_attempts.csv | Tentativas realizadas em cada sessão (Tabela Fato de Detalhamento) |
| user_profile.csv | Perfil dos usuários (Tabela Dimensão) |

---

# Ferramentas utilizadas

- Power BI
- Power Query
- Modelagem Dimensional
- DAX (próximas etapas)

---

# Estrutura adotada

## Fact_Sessions

Granularidade:

> 1 linha = 1 sessão de jogo

Principais informações:

- usuário
- palavra
- data
- resultado
- quantidade de tentativas
- tempo de conclusão
- dispositivo
- retenção D1
- retenção D30

---

## Fact_Attempts

Granularidade:

> 1 linha = 1 tentativa realizada durante uma sessão

Principais informações:

- número da tentativa
- palavra digitada
- letras corretas
- posições corretas

Esta tabela será utilizada para análises mais detalhadas do comportamento do jogador.

---

## Dim_User

Granularidade:

> 1 linha = 1 usuário

Contém atributos demográficos e comportamentais, como:

- faixa etária
- cidade
- estado
- salário
- setor
- dispositivo principal
- assinatura da newsletter
- hábitos de food delivery
- horário típico de jogo

---

# Limpeza dos Dados

## 1. Fact_Sessions

### Padronização da coluna Device

Foram encontrados valores escritos de formas diferentes:

- ios
- IOS
- Ios

e

- Android
- ANDROID
- android

Todos foram padronizados.

---

### Registros inválidos

Foram encontrados 28 registros contendo:

- attempts = 0
- time_to_complete_sec = -1 ou -5

Esses registros representam sessões inconsistentes, incompatíveis com a lógica do jogo.

Tratamento:

> Removidos da análise.

---

### Duplicidade de session_id

Segundo o dicionário de dados, session_id representa um identificador único da sessão.

Durante a validação foram encontrados registros duplicados.

Foi realizada investigação manual comparando todas as colunas.

Resultado:

As linhas eram completamente idênticas.

Tratamento:

> Remoção das duplicatas utilizando session_id.

---

## 2. Fact_Attempts

Nenhuma inconsistência estrutural foi encontrada.

Validação realizada:

- sem valores nulos
- sem erros
- granularidade preservada

Nenhum tratamento foi necessário.

---

## 3. Dim_User

### Valores ausentes

Identificados:

- age_range
- city
- salary_range

Decisão:

Os registros foram mantidos, pois representam ausência de resposta do usuário e podem ser utilizados como categoria "Não informado".

---

### Inconsistência Estado × Cidade

Foram encontrados registros onde:

Exemplo:

Estado = RJ

Cidade = São Paulo

Como não existe fonte oficial para validação desses registros, optou-se por não realizar alterações arbitrárias.

Esses registros permaneceram na base.

---

### Padronizações

Foram realizadas padronizações em:

- Job Role
- Orders Food Delivery
- Tipos de dados
- Colunas booleanas

---

# Modelagem

Modelo dimensional adotado:

Dim_User

↓

Fact_Sessions

↓

Fact_Attempts

Relacionamentos:

Dim_User (1) → (*) Fact_Sessions

Fact_Sessions (1) → (*) Fact_Attempts

---

# Decisões Técnicas

Durante a modelagem foi identificado que o Power BI sugeria relacionamento Muitos para Muitos entre Fact_Sessions e Fact_Attempts.

Após investigação verificou-se que a causa eram registros duplicados em Fact_Sessions.

Após remoção das duplicatas foi possível criar relacionamento:

1 → Muitos

preservando a granularidade correta da base.

---

# Resultado da etapa

Ao final da preparação foi obtida uma base consistente para iniciar as análises de Produto e Growth.

A próxima etapa consiste em identificar quais variáveis possuem maior relação com:

- Retenção D1 (played_next_day)
- Retenção D30 (active_d30)

e propor experimentos que aumentem essas métricas.
