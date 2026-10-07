# 🏭 Análise de Dados da Produção Industrial

### Python • Pandas • Análise de Dados • Visualização • Indicadores Industriais

> **Transformando dados de produção em informações para apoiar decisões na indústria.**

Este projeto apresenta uma análise exploratória de dados de uma operação industrial, integrando informações de **produção diária, cadastro de máquinas e metas mensais de produção e qualidade**.

Desenvolvido durante a formação **Fundamentos de IA e Análise de Dados — SENAI Roberto Simonsen**, o projeto simula um cenário real de análise de dados industriais, utilizando Python para transformar diferentes fontes de dados em indicadores capazes de revelar **custos operacionais, desempenho, qualidade e utilização da capacidade produtiva**.

---

## 🎯 Objetivo do projeto

O principal objetivo é demonstrar como técnicas de **tratamento, integração e análise de dados** podem ser utilizadas para identificar padrões e oportunidades de melhoria em um ambiente industrial.

A análise busca responder perguntas relevantes para a gestão da produção:

- 💰 Qual máquina apresenta o maior custo operacional?
- ⚙️ Existe relação entre a idade das máquinas e a ocorrência de defeitos?
- 📉 Quantos dias a produção ficou abaixo das metas estabelecidas?
- 🛠️ Quais setores apresentam maior frequência de problemas de qualidade?
- 📊 Quais máquinas estão operando próximas do limite de capacidade?

---

## 🧩 Desafio de negócio

Em um ambiente industrial, os dados geralmente estão distribuídos em diferentes sistemas e arquivos.

Neste projeto, as informações estavam separadas em:

```
Produção diária
      │
      ├──────────────┐
      │              │
      ▼              ▼
Cadastro de      Metas mensais
máquinas         de produção
      │              │
      └──────┬───────┘
             ▼
      DataFrame integrado
          df_completo
             │
             ▼
       Análise de dados
             │
      ┌──────┼──────┐
      ▼      ▼      ▼
    Custos  Qualidade  Capacidade
```

O desafio foi **integrar essas fontes corretamente** e utilizar o conjunto consolidado para gerar indicadores e conclusões.

---

## 📂 Fontes de dados

O projeto utiliza três fontes principais:

### 🏭 Produção diária

Arquivo:

`alpha_sp_producao_diaria.csv`

Contém informações como:

- máquina;
- data;
- setor;
- produção;
- horas de utilização;
- peças defeituosas.

### ⚙️ Cadastro das máquinas

Arquivo:

`dados_complementares_alpha_sp.xlsx`

Aba:

`Cadastro_Maquinas`

Contém informações como:

- fabricante;
- modelo;
- ano de aquisição;
- capacidade máxima diária;
- custo por hora de operação.

### 🎯 Metas mensais

Arquivo:

`dados_complementares_alpha_sp.xlsx`

Aba:

`Metas_Mensais`

Contém:

- metas de produção diária;
- limite máximo de taxa de defeitos;
- mês;
- setor.

---

# 🔗 Integração dos dados

Um dos principais desafios do projeto foi combinar as diferentes fontes utilizando chaves de relacionamento.

### Primeiro `merge`

Produção diária + cadastro das máquinas:

```
df_completo = pd.merge(
    df,
    cadastro,
    on="maquina",
    how="left"
)
```

### Segundo `merge`

Dados combinados + metas mensais:

```
df_completo = pd.merge(
    df_completo,
    metas,
    on=["mes", "setor"],
    how="left"
)
```

O resultado é o **`df_completo`**, que concentra todas as informações necessárias para as análises.

> 💡 A utilização de `left join` garante que os registros de produção sejam preservados durante a integração dos dados.

---

# 📊 Indicadores analisados

## 💰 1. Custo operacional

Foi calculado o custo diário de operação de cada máquina:

```
custo_operacao_dia = (
    horas_uso *
    custo_hora_operacao_reais
)
```

Posteriormente, os custos foram agrupados por máquina para identificar quais equipamentos representam maior impacto financeiro na operação.

### Resultado do conjunto analisado

| Máquina | Custo total |
| --- | --- |
| 🥇 Solda Robô 01 | R$ 69.665,40 |
| 🥈 Torno CNC 03 | R$ 51.297,50 |
| 🥉 Prensa 12 | R$ 42.387,20 |

A **Solda Robô 01** apresentou o maior custo acumulado de operação no período analisado.

---

# 🧪 2. Idade das máquinas × qualidade

Para avaliar a qualidade da produção, foi criada uma nova métrica:

```
taxa_defeito_pct = (
    pecas_defeituosas /
    producao
) * 100
```

A taxa média de defeitos foi então comparada com o **ano de aquisição das máquinas**.

Além da comparação visual, foi utilizado o **coeficiente de correlação** para investigar a existência de uma relação entre idade do equipamento e taxa de defeitos.

### Insight

Essa análise permite investigar uma hipótese importante para a indústria:

> **Máquinas mais antigas apresentam maior tendência de gerar defeitos?**

Esse tipo de indicador pode apoiar decisões relacionadas a:

- manutenção preventiva;
- substituição de equipamentos;
- controle de qualidade;
- investimentos em modernização.

---

# 📉 3. Cumprimento das metas de produção

Cada registro de produção foi comparado com a meta estabelecida para o respectivo **mês e setor**.

```
abaixo_meta_producao = (
    producao < meta_producao_diaria
)
```

A partir disso, foram identificados:

- quantidade de dias abaixo da meta;
- meses com maior ocorrência;
- setores com maior frequência de descumprimento.

### Por que esse indicador é importante?

Apenas observar a produção total pode esconder problemas.

Uma máquina pode apresentar uma produção acumulada aparentemente boa, mas estar frequentemente abaixo da meta diária.

A análise permite localizar **quando e onde o desempenho está abaixo do esperado**.

---

# 🛠️ 4. Indicador de qualidade

Foi criada uma regra para identificar dias em que a taxa de defeitos ultrapassou o limite estabelecido:

```
acima_meta_defeito = (
    taxa_defeito_pct >
    meta_taxa_defeito_max_pct
)
```

A análise permite identificar os setores com maior número de ocorrências fora do padrão.

### Aplicação prática

Esse indicador pode auxiliar equipes de produção e qualidade na priorização de ações como:

- análise de causa raiz;
- manutenção;
- treinamento operacional;
- revisão de processos;
- acompanhamento de máquinas críticas.

---

# 📈 5. Utilização da capacidade produtiva

Foi calculada a relação entre a produção média diária e a capacidade máxima de cada máquina:

```
utilizacao = (
    producao_media /
    capacidade_maxima_diaria
) * 100
```

Com esse indicador é possível identificar:

### 🔴 Máquinas próximas do limite

Equipamentos com alta utilização podem representar:

- risco de sobrecarga;
- necessidade de manutenção preventiva;
- gargalos produtivos;
- oportunidade de expansão da capacidade.

### 🟢 Máquinas com maior folga

Equipamentos com baixa utilização podem indicar:

- capacidade ociosa;
- possibilidade de redistribuição da produção;
- oportunidade de aumento de produtividade.

---

# 📊 Visualizações

Os resultados foram apresentados utilizando gráficos para facilitar a interpretação dos indicadores.

### Custo operacional por máquina

!Custo operacional por máquina

### Qualidade por setor

!Dias fora da meta de qualidade

### Utilização da capacidade

!Utilização da capacidade

---

# 💡 Principais aprendizados

Este projeto permitiu aplicar conceitos fundamentais de **Data Analytics** em um contexto próximo de um cenário industrial real.

Entre os principais aprendizados estão:

- 🔹 Importação e tratamento de dados;
- 🔹 Manipulação de DataFrames com Pandas;
- 🔹 Integração de diferentes fontes de dados;
- 🔹 Utilização de `merge` com chaves simples e compostas;
- 🔹 Criação de indicadores;
- 🔹 Análise de correlação;
- 🔹 Agrupamento e agregação de dados;
- 🔹 Identificação de padrões e desvios;
- 🔹 Visualização de dados com Matplotlib;
- 🔹 Transformação de dados em informações para tomada de decisão.

---

# 🧠 Insights para o negócio

A análise demonstra como dados operacionais podem apoiar diferentes áreas da indústria.

| Área | Possível aplicação |
| --- | --- |
| 💰 Custos | Identificação de máquinas com maior custo operacional |
| 🏭 Produção | Monitoramento do cumprimento das metas |
| 🧪 Qualidade | Identificação de setores com maior taxa de não conformidade |
| 🔧 Manutenção | Identificação de equipamentos que podem exigir maior atenção |
| 📈 Capacidade | Identificação de máquinas próximas do limite produtivo |
| 📊 Gestão | Apoio à tomada de decisões baseada em dados |

---

# 🛠️ Tecnologias utilizadas

- **Python**
- **Pandas**
- **Matplotlib**
- **Jupyter Notebook**
- **Google Colab**
- **Microsoft Excel**
- **Git**
- **GitHub**

---

# 📁 Estrutura do projeto

```
analise-dados-industria/
│
├── README.md
│
├── notebooks/
│   └── analise_industria.ipynb
│
├── dados/
│   ├── alpha_sp_producao_diaria.csv
│   └── dados_complementares_alpha_sp.xlsx
│
├── imagens/
│   ├── custo_por_maquina.png
│   ├── defeitos_por_setor.png
│   └── utilizacao_capacidade.png
│
├── resultados/
│   └── df_completo.csv
│
└── requirements.txt
```

---

# ▶️ Como executar

Clone o repositório:

```
git clone https://github.com/SEU-USUARIO/analise-dados-industria.git
```

Instale as dependências:

```
pip install -r requirements.txt
```

Depois, abra o notebook:

```
notebooks/analise_industria.ipynb
```

O projeto também pode ser executado diretamente no **Google Colab**.

---

# 🎓 Contexto acadêmico

Este projeto foi desenvolvido como parte da formação:

**SENAI Roberto Simonsen** **Curso:** Fundamentos de IA e Análise de Dados **Projeto:** Missão 6/7

O trabalho representa a aplicação prática de conceitos de análise e tratamento de dados em um cenário de produção industrial.

---

# 🚀 Próximos passos

Como evolução do projeto, algumas possibilidades seriam:

- criação de um dashboard interativo com **Power BI**;
- desenvolvimento de indicadores em tempo real;
- aplicação de modelos preditivos para previsão de falhas;
- análise de séries temporais;
- previsão de produção;
- criação de um modelo para identificar risco de aumento da taxa de defeitos;
- desenvolvimento de um sistema de monitoramento de KPIs industriais.

---

# 👨‍💻 Autor

**Seu Nome**

📊 Data Analytics | Python | Pandas | Inteligência Artificial

> **Projeto desenvolvido com foco em transformar dados em insights e apoiar decisões baseadas em evidências.**

:::

### 💡 Uma sugestão importante para seu portfólio

Eu deixaria **"SENAI — Missão 6/7" como contexto**, e não como o destaque principal. Para recrutadores, a primeira impressão deve ser algo como:

> **Análise de Dados da Produção Industrial** _Python • Pandas • Data Analytics • Indicadores Industriais_

Isso faz o projeto parecer um **case de portfólio**, e não apenas uma atividade de curso.

Também recomendo colocar no topo do README alguns badges, por exemplo:

```
![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-150458?logo=pandas)
![Matplotlib](https://img.shields.io/badge/Matplotlib-Visualization-orange)
![Status](https://img.shields.io/badge/Status-Concluído-success)
```

Se você quiser, no próximo passo posso montar **a versão ainda mais profissional, estilo portfólio de candidato a vaga de Analista de Dados**, incluindo **capa do projeto, badges, seção de KPI, "Business Problem", "Data Pipeline", "Insights", "Resultados" e uma descrição curta para aparecer na página principal do GitHub**.
