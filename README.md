# Tech_Challenge_FIAP
# 🍷 Análise de Dados Vitivinícolas para Business Intelligence

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Jonathan-Paixao/Tech_Challenge_FIAP/blob/main/ETL_FiapChall.ipynb)

## 🎯 Objetivo do Projeto

O mercado internacional de vinhos tem apresentado crescimento consistente, mas a participação brasileira ainda é tímida. Para que uma vinícola nacional possa expandir sua atuação, é fundamental compreender os fatores que impulsionam ou dificultam a exportação.

Este projeto responde à pergunta norteadora: **“Quais fatores impactam a exportação de vinhos brasileiros e como identificar oportunidades de crescimento no mercado internacional?”**

Para isso, foi desenvolvido um processo de **ETL (Extração, Transformação e Carga)** que consolida dados internos da vinícola com fontes de dados externas (climáticos, cambiais, demográficos, de comércio exterior e avaliações de qualidade). Os dados tratados servirão como base para um **dashboard interativo no Power BI**, destinado a investidores e acionistas para apoiar a tomada de decisões estratégicas.

---

## 🛠️ Ferramentas e Tecnologias
* **Linguagem:** Python
* **Bibliotecas Principais:** Pandas, Requests, Glob, OS
* **Ambiente de Desenvolvimento:** Google Colab
* **Metodologia de ETL:** Arquitetura Medallion (Bronze, Silver, Gold)
* **Ferramenta de Visualização:** Power BI

---

## 🧱 Arquitetura de Dados (Metodologia Medallion)

O projeto segue a arquitetura Medallion para garantir a qualidade e a governança dos dados em diferentes estágios do pipeline:

* **Camada Bronze:** Contém todos os dados brutos, exatamente como foram extraídos das fontes originais. Pastas como `Dados Vinícula/`, `Dados Temperatura/` e `Dados Comex/` representam esta camada.
* **Camada Silver (Implícita no Notebook):** Durante a fase de transformação, os dados são limpos, padronizados e enriquecidos. Esta camada intermediária reside nos DataFrames Pandas dentro do notebook `ETL_FiapChall.ipynb`.
* **Camada Gold:** Armazena os dados finais, agregados e prontos para consumo por ferramentas de BI. A pasta `Gold_Data/` contém os arquivos `.parquet` e `.xlsx` que alimentam o dashboard final.

---

## 📂 Estrutura do Repositório`
```
/
├── Dados Vinícula/              # Camada Bronze: Dados brutos fornecidos pela faculdade
│   ├── Producao.csv
│   ├── Processamento.csv
│   ├── Comercializacao.csv
│   ├── Importacao.csv
│   └── Exportacao.csv
│
├── Dados Temperatura/           # Camada Bronze: Dados climáticos externos (INMET)
│   └── ... (vários arquivos .CSV)
│
├── Dados Comex/                 # Camada Bronze: Dados de comércio exterior (Comex Stat)
│   ├── EXP_....csv
│   ├── NCM.csv
│   └── PAIS.csv
│
├── Dados_XLSX_Externos/         # Camada Bronze: Outros dados externos
│   ├── ibge_pam_bronze.xlsx
│   └── Dados_avaliacao_vinhos.xlsx
│
├── Gold_Data/                   # Camada Ouro: Dados finais, limpos e prontos para consumo
│   ├── Dados_internos/
│       ├── Excel/
│       ├── Parquet/
│
│   └── Dados_externos/
│       ├── Excel/
│       ├── Parquet/
│
├── ETL_FiapChall.ipynb          # O notebook principal com todo o processo de ETL
└── README.md                    # Esta documentação
```
---
## ⚙️ Processo de ETL Detalhado

O notebook `ETL_FiapChall.ipynb` executa as três etapas do processo de ETL:

### 1. Extração (Extract)
Os dados são extraídos de múltiplas fontes, carregados a partir do Google Drive:
* **Dados Internos da Vinícola:** 5 arquivos CSV (`Producao`, `Processamento`, `Comercializacao`, `Importacao`, `Exportacao`).
* **Dados Externos:**
    * **Comércio Exterior (Comex Stat):** Arquivos `EXP_*.csv` detalhando exportações, enriquecidos pelas tabelas de apoio `NCM.csv` e `PAIS.csv`.
    * **Dados Climáticos (INMET):** Dezenas de arquivos `.CSV` de estações meteorológicas do Rio Grande do Sul, consolidados em uma base única.
    * **Dados Cambiais (USD/BRL):** Cotação diária do Dólar consumida via **API do Banco Central do Brasil**.
    * **Produção Agrícola (IBGE PAM):** Dados sobre a produção de uva por município.
    * **Avaliações de Vinhos (Vivino):** Dataset com avaliações de qualidade, notas e contagem de reviews para vinhos brasileiros.

### 2. Transformação (Transform)
Para cada fonte, aplicamos uma série de transformações robustas para garantir a qualidade e a consistência:

* **Pivotagem de Dados (`.melt()`):** Conversão de tabelas de formato "largo" (anos em colunas) para o formato "longo" (tidy data), essencial para análises em BI.
* **Limpeza e Padronização:**
    * Substituição de valores inconsistentes (ex: `*`, `nd`, ` - `) por `0` ou `NaN`.
    * Remoção de linhas de subtotal para evitar duplicidade em agregações.
    * Tratamento de valores nulos, utilizando a **mediana** para dados climáticos a fim de mitigar o impacto de outliers.
    * Padronização de texto com métodos como `.str.strip()` e `.str.title()`.
* **Criação de Novas Colunas (Feature Engineering):**
    * Extração de `Ano` e `UF` de campos de texto e data.
    * Criação da coluna `Grupo` para categorizar produtos (ex: "Vinho de Mesa", "Vinho Fino de Mesa").
    * Separação das colunas de `KG` e `USD` nos arquivos de importação/exportação.
* **Enriquecimento de Dados (`.merge()`):**
    * Junção dos dados de comércio exterior com as tabelas `NCM` e `PAIS` para traduzir códigos em descrições legíveis.
    * Junção dos dados de produção agrícola com dados demográficos para adicionar informações de `Região` e `Nome_UF`.
* **Tipagem de Dados (`.astype()`):** Aplicação dos tipos de dados corretos (`int64`, `float`, `category`, `datetime64[ns]`, `boolean`) para otimizar o uso de memória e garantir a integridade das análises.

### 3. Carga (Load)
Os DataFrames finais são carregados na camada `Gold_Data/` em dois formatos de alta performance:
* **`.xlsx` (Excel):** Para fácil verificação e análise manual.
* **`.parquet`:** Formato colunar otimizado, ideal para ingestão rápida e eficiente em ferramentas de BI como o Power BI.

---
## 📊 Análise e KPIs Estratégicos

Os dados tratados permitem a análise de indicadores-chave de desempenho (KPIs) para orientar a estratégia de negócio:

#### Principais KPIs
* **Volume Exportado (Litros):** Mede o alcance físico do produto no mercado global.
* **Valor Exportado (US$):** Acompanha a receita gerada pelas exportações.
* **Variação Anual (%):** Identifica o crescimento ou a retração das vendas internacionais.
* **Market Share Brasileiro:** Compara o volume de exportação da vinícola com o total exportado pelo Brasil e pelo Rio Grande do Sul.
* **Elasticidade Cambial:** Analisa a correlação entre a cotação do dólar e o valor exportado.
* **Avaliação Média de Qualidade:** Média das notas por mercado, indicando a percepção de qualidade dos vinhos.

#### Visualizações e Insights Propostos
* Evolução temporal das exportações nos últimos 15 anos.
* Ranking dos principais países importadores de vinhos brasileiros.
* Heatmap de correlação entre preço, qualidade percebida e volume exportado.
* Análise do impacto do câmbio na receita em dólar.

---

## 💡 Conclusão e Recomendações

* O Brasil apresenta **potencial de crescimento** em mercados internacionais ainda pouco explorados.
* A valorização da **qualidade percebida** (notas e avaliações) pode ser um diferencial competitivo importante.
* Estratégias financeiras para **proteger a receita contra a variação cambial** são altamente recomendadas.
* O incentivo a **práticas agrícolas sustentáveis** e a mitigação de riscos climáticos são cruciais para garantir a consistência da produção.
* **Sugestão para pesquisas futuras:** Desenvolver uma análise preditiva de volume de exportação baseada em variáveis climáticas, cambiais e tendências de consumo.

---

## 🚀 Como Executar o Notebook

1.  **Abrir no Colab:** Clique no emblema "Open in Colab" no topo deste arquivo.
2.  **Estrutura de Pastas:** Certifique-se de que sua estrutura de pastas no Google Drive corresponde à descrita acima.
3.  **Execução:** Execute todas as células em ordem sequencial. O notebook irá montar seu Google Drive, ler os arquivos brutos, processá-los e salvar os resultados na pasta `Gold_Data`.

---

## 👥 Autores
* [Wagner da Silva Junior - wagner_silvajr@hotmail.com]
* [Bruno de Oliveira Fernandes Machado de Assis - bruno.assis.14@hotmail.com]
* [Gabriel Nunes - gabrielnunes.gna@gmail.com]
* [Jonathan Silveira Paixão - jonathan.s.paixao@Outlook.com]
* [Rafael Vieira Vidal - rafaelvieiravidal@gmail.com]
