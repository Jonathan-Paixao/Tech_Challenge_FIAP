# Tech_Challenge_FIAP
# 🍷 Análise de Dados Vitivinícolas para Business Intelligence

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Jonathan-Paixao/Tech_Challenge_FIAP/blob/main/ETL_FiapChall.ipynb)

## 🎯 Objetivo do Projeto

Este repositório contém o processo de **ETL (Extração, Transformação e Carga)** desenvolvido para o Tech Challenge da FIAP. O objetivo é atuar como uma equipe de Data Analytics para uma empresa brasileira de vinhos, preparando um conjunto de dados robusto para análise de exportações.

Os dados tratados neste notebook servirão como base para a criação de um **dashboard interativo no Power BI**, que será apresentado a investidores e acionistas para apoiar a tomada de decisões estratégicas e identificar oportunidades de mercado.

---

## 📂 Estrutura do Projeto

O projeto está organizado em uma estrutura de pastas que separa os dados brutos dos dados tratados, seguindo as melhores práticas de um data lakehouse.
```
/
├── Dados Vinícula/             # Camada Bronze: Dados brutos fornecidos pela faculdade
│   ├── Producao.csv
│   ├── Processamento.csv
│   ├── Comercializacao.csv
│   ├── Importacao.csv
│   └── Exportacao.csv
│
├── Dados Temperatura/          # Camada Bronze: Dados climáticos externos
│   └── ... (vários arquivos CSV)
│
├── Dados Comex/                # Camada Bronze: Dados de comércio exterior
│   ├── EXP_....csv
│   ├── NCM.csv
│   └── PAIS.csv
│
├── Gold_Data/                  # Camada Ouro: Dados finais, limpos e prontos para consumo
│   ├── processamento.xlsx
│   ├── processamento.parquet
│   └── ... (outros arquivos tratados)
│
├── ETL_FiapChall.ipynb         # O notebook principal com todo o processo de ETL
└── README.md                   # Esta documentação 
```

## ⚙️ Processo de ETL (Extração, Transformação e Carga)

O notebook `ETL_FiapChall.ipynb` realiza a limpeza e preparação de múltiplas fontes de dados.

### 1. Extração (Extract)

Os dados são coletados de duas fontes principais:

* **Dados da Vinícula:** Um conjunto de 5 arquivos CSV (`Producao`, `Processamento`, `Comercializacao`, `Importacao`, `Exportacao`) disponibilizados pela instituição e carregados a partir do Google Drive.
* **Dados Externos:**
    * **Dados Climáticos:** Múltiplos arquivos `.CSV` de estações meteorológicas do Rio Grande do Sul, consolidados em uma única base.
    * **Dados Cambiais:** Cotação diária do Dólar (USD/BRL) consumida via API do Banco Central do Brasil (BCB).
    * **Dados de Comércio Exterior (Comex Stat):** Arquivos de exportação que são enriquecidos com tabelas de apoio (`NCM` e `PAIS`) para traduzir códigos em descrições legíveis.

### 2. Transformação (Transform)

Para cada fonte de dados, aplicamos uma série de transformações para garantir a qualidade e a consistência dos dados:

* **Pivotagem (Melt):** Converte tabelas no formato "largo" (anos como colunas) para o formato "longo" (tidy data), ideal para análises.
* **Limpeza de Dados:** Substitui valores inconsistentes (como `*`, `nd`) por `0`, remove linhas de subtotal e trata valores nulos, utilizando a mediana para dados climáticos.
* **Criação de Colunas (Feature Engineering):**
    * Criação da coluna `Grupo` nas bases da vinícula para categorizar os produtos (ex: "Vinho de Mesa", "Vinho Fino de Mesa").
    * Criação de colunas `KG` e `USD` nas bases de importação/exportação.
    * Extração do ano a partir de colunas de data.
* **Padronização:** Renomeia colunas para nomes mais claros e padroniza o conteúdo de texto (ex: `.str.title()`).
* **Enriquecimento de Dados:** Realiza o `merge` dos dados de comércio exterior com as tabelas `NCM` e `PAIS` para adicionar descrições de produtos e nomes de países.
* **Tipagem de Dados:** Aplica os tipos de dados corretos para cada coluna (`int64`, `float`, `category`, `datetime64[ns]`), otimizando o uso de memória e garantindo a integridade dos dados.

### 3. Carga (Load)

Após a transformação, os DataFrames finais e limpos são carregados na pasta `Gold_Data/` do Google Drive em dois formatos otimizados:
* **`.xlsx` (Excel):** Para fácil visualização e verificação manual.
* **`.parquet`:** Um formato colunar de alta performance, ideal para ser consumido por ferramentas de BI como o Power BI.

---

## 🚀 Como Executar o Notebook

1.  **Abrir no Colab:** Clique no emblema "Open in Colab" no topo deste arquivo.
2.  **Estrutura de Pastas:** Certifique-se de que sua estrutura de pastas no Google Drive corresponde à descrita acima para que o notebook encontre os arquivos CSV.
3.  **Execução:** Execute todas as células em ordem sequencial. O notebook irá montar seu Google Drive, ler os arquivos brutos, processá-los e salvar os resultados na pasta `Gold_Data`.

---

## 👥 Autores
* * [Wagner da Silva Junior - wagner_silvajr@hotmail.com]
* * [Bruno de Oliveira Fernandes Machado de Assis - bruno.assis.14@hotmail.com]
* * [Gabriel Nunes - gabrielnunes.gna@gmail.com]
* * [Jonathan Silveira Paixão - jonathan.s.paixao@Outlook.com]
* * [Rafael Vieira Vidal - rafaelvieiravidal@gmail.com]
