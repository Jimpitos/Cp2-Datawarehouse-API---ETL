# Checkpoint 2 - Data Warehouse & Azure Data Factory (ETL API USGS)

Repositório dedicado à entrega do Checkpoint 2 da FIAP, focado na construção de um pipeline completo de Engenharia de Dados utilizando o **Azure Data Factory (ADF)**, **Azure Data Lake Storage (ADLS) Gen2** e **Azure SQL Database**.

## 📋 Sobre o Projeto
O objetivo principal deste projeto é extrair dados sismológicos da API pública do USGS (*US Geological Survey*), armazenar o ficheiro GeoJSON original na camada bruta (*Raw*) do Data Lake, transformá-lo utilizando *Mapping Data Flows* e carregá-lo num modelo de dados relacional (Dimensão e Fato) num Data Warehouse hospedado no Azure SQL.

---

## 🏗️ Arquitetura e Fluxo de Dados

1. **Extração (Camada Raw):** Uma atividade de cópia (*Copy Activity*) consome a API REST anônima do USGS e grava o ficheiro original em `landing/raw/prova_usgs/` no ADLS Gen2.
2. **Transformação (Data Flows):** 
   - **Dimensão (`DIM_REDE_SISMICA`):** Lê o ficheiro bruto, aplica um *Flatten* no array de eventos e extrai redes distintas (`properties.net`) para carregar a dimensão.
   - **Fato (`FATO_TERREMOTO`):** Cruza os dados dos eventos com a dimensão via *Lookup*, converte o tempo unix (*timestamp*) para o formato UTC legível e extrai as coordenadas geográficas de latitude, longitude e profundidade do array `geometry.coordinates`.
3. **Carga (Data Warehouse):** Insere os dados tratados nas tabelas relacionais do Azure SQL, respeitando as restrições de chaves primárias (*identity*) e estrangeiras.

---

## 📂 Estrutura do Repositório

```text
├── exportacao_adf/          # Modelos ARM e definições exportadas do Azure Data Factory
├── evidencias/              # Capturas de ecrã comprobatórias da execução
│   ├── 01_conexoes.png      # Teste de conexão dos Linked Services
│   ├── 02_arquivo_raw.png   # Validação do ficheiro no Storage Browser
│   ├── 03_pipeline_sucesso.png # Pipeline executado com sucesso (vistos verdes)
│   └── 04_resultado_sql.png # Resultados das queries de validação no Azure SQL
└── modelo_e_validacao.sql   # Scripts DDL (criação de tabelas) e consultas de validação
