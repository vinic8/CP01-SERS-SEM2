# Análises de Consumo e Geração de Energia — Portfólio de Notebooks

integrantes:
**Gabriel Vilas - RM: 571603**
**Matheus Ferreira - RM: 569638
**Vinicius Molena - RM: 571270
Ricardo Algazi - RM: 569600
Gustavo Henrique - RM: 569921
Nathan Werner - RM: 572925

Este repositório reúne um conjunto de análises exploratórias em Python/Pandas sobre dados de **consumo e geração de energia**, aplicadas a diferentes datasets (residenciais, industriais, solares, eólicos e de operador nacional do sistema elétrico). Todas seguem uma metodologia semelhante: carregamento e organização dos dados, definição de indicadores estatísticos, identificação de picos/limiares de consumo ou geração, cruzamento com uma segunda condição (ambiental ou elétrica) e interpretação dos resultados.

## Sumário

| # | Notebook | Dataset | Foco da análise |
|---|----------|---------|------------------|
| 1 | [`EXEMPLO_01_1CCPK.ipynb`](#1-exemplo_01_1ccpkipynb--appliances-energy-prediction) | Appliances Energy Prediction (UCI) | Consumo de eletrodomésticos vs. temperatura/umidade |
| 2 | [`Dataset03.ipynb`](#2-dataset03ipynb--tetuan-city-power-consumption) | Tetuan City Power Consumption | Consumo por zona vs. temperatura |
| 3 | [`dataset6_potencia_corrente.ipynb`](#3-dataset6_potencia_correnteipynb--household-power-consumption) | Individual Household Electric Power Consumption (UCI) | Potência ativa vs. corrente elétrica |
| 4 | [`solar_power_generation.ipynb`](#4-solar_power_generationipynb--solar-power-generation) | Solar Power Generation Data | Geração CA por inversor |
| 5 | [`Dataset5_Wind_Solar_Energy__2_.ipynb`](#5-dataset5_wind_solar_energy_2_ipynb--wind--solar-energy-production) | Wind & Solar Energy Production (Kaggle) | Comparação de picos solar vs. eólica |
| 6 | [`Desafio_Final_Energia_ONS_API_Final.ipynb`](#6-desafio_final_energia_ons_api_finalipynb--carga-verificada-ons-api) | API pública ONS — Carga Verificada | Carga elétrica do SIN (SP) com relatório assistido por IA |
| 7 | [`desafio_2__1_.ipynb`](#7-desafio_2__1_ipynb--desafio-2) | Steel Industry Data / API ONS | Rascunho incompleto de tratamento de dados via API |

---

## 1. `EXEMPLO_01_1CCPK.ipynb` — Appliances Energy Prediction

**Fonte:** [UCI — Appliances Energy Prediction](https://archive.ics.uci.edu/dataset/374/appliances+energy+prediction)

**Situação-problema:** uma empresa de eficiência energética analisa uma residência de baixo consumo, buscando identificar períodos de consumo elevado dos eletrodomésticos e as condições de temperatura/umidade associadas a esses momentos.

**Etapas realizadas:**
- Carregamento do dataset (`energydata_SAMPLE_01.csv`) e inspeção inicial (`head()`, `shape`, `info()`, `describe()`).
- Renomeação da variável alvo `Appliances` → `Consumo_Eletrodomesticos` e simplificação de atributos ambientais (`T1` → `Temperatura_Setor_1`, `RH_1` → `Umidade_Setor_1`, `T2` → `Temperatura_Setor_2`, `RH_2` → `Umidade_Setor_2`).

**Status:** notebook introdutório/exemplo, cobre apenas a etapa de carregamento e renomeação de colunas.

---

## 2. `Dataset03.ipynb` — Tetuan City Power Consumption

**Fonte:** dataset de consumo elétrico da cidade de Tetuan (Marrocos), com três zonas de distribuição e variáveis climáticas (temperatura, umidade, velocidade do vento, radiação difusa).

**Situação-problema:** identificar qual das três zonas de consumo apresenta o maior pico e investigar se os períodos de consumo elevado coincidem com temperaturas acima da média.

**Etapas realizadas:**
1. Renomeação das variáveis de consumo para `Consumo_Zona_1`, `Consumo_Zona_2` e `Consumo_Zona_3`.
2. Cálculo do consumo máximo de cada zona.
3. Identificação da zona com maior pico de consumo.
4. Cálculo de 70% do valor máximo da zona identificada e filtragem dos registros acima desse limiar.
5. Contagem de registros e cálculo do percentual em relação ao total.
6. Cálculo da temperatura média e criação de um segundo recorte (consumo acima do limiar **e** temperatura acima da média).
7. Comparação entre os dois conjuntos.

**Principais resultados (amostra completa, 52.416 registros):**
- Máximos: Zona 1 = 52.204,40 | Zona 2 = 37.408,86 | Zona 3 = 47.598,33.
- **Zona 1** apresenta o maior pico de consumo.
- Limiar (70% do máximo): 36.543,08 → **14.718 registros (28,08%)** da amostra.
- Temperatura média: 18,81 °C.
- Registros com consumo acima do limiar **e** temperatura acima da média: **9.917 (18,92%)**.
- A segunda condição reduziu o conjunto em **4.801 registros (32,62%)**, indicando que boa parte dos picos de consumo da Zona 1 ocorre também em momentos de temperatura mais baixa (possivelmente ligados a aquecimento), e não exclusivamente em dias quentes.

---

## 3. `dataset6_potencia_corrente.ipynb` — Household Power Consumption

**Fonte:** [UCI — Individual Household Electric Power Consumption](https://archive.ics.uci.edu/dataset/235/individual+household+electric+power+consumption)

**Situação-problema:** uma residência com monitoramento elétrico detalhado deseja identificar episódios de demanda elevada (potência ativa) que também apresentem corrente acima da média.

**Etapas realizadas:**
- **Etapa A (Orange Data Mining):** pipeline de widgets (File → Data Table → Impute → Select Columns → Data Sampler → Save Data) para tratar ausentes e extrair uma amostra de 10%.
- **Etapa B (Python/Pandas):**
  1. Carregamento da amostra tratada e simplificação dos nomes de colunas (`global_active_power` → `pot_ativa`, `global_intensity` → `corrente`, etc.).
  2. Verificação e limpeza de valores ausentes.
  3. Cálculo do valor máximo, médio e mediano de potência ativa.
  4. Limiar de **75%** do máximo e DataFrame de alta potência.
  5. Contagem e percentual de registros acima do limiar.
  6. Cálculo da corrente média e criação de um segundo DataFrame (potência acima do limiar **e** corrente acima da média).
  7. Comparação entre os dois filtros, incluindo análise de correlação entre potência e corrente (fisicamente quase colineares: P ≈ V × I).
  8. Visualizações: dispersão potência × corrente e histograma da potência ativa com a linha do limiar.
  9. Exportação dos DataFrames filtrados (`df_alta_potencia.csv`, `df_alta_potencia_e_corrente.csv`).

**Observação analítica:** por potência e corrente serem eletricamente quase colineares (P ≈ V·I·cos φ), a correlação entre elas é muito alta, o que explica por que quase todos os registros de alta potência também apresentam corrente acima da média.

---

## 4. `solar_power_generation.ipynb` — Solar Power Generation

**Fonte:** `SOLAR_POWER_Generation_Data.csv` (dados de geração de uma usina solar com múltiplos inversores).

**Situação-problema:** identificar os períodos de maior geração de potência CA e qual inversor mais contribui para esses picos.

**Etapas realizadas:**
1. Carregamento dos dados e renomeação de colunas para o português (`DATE_TIME` → `DATA_HORA`, `DC_POWER` → `POTENCIA_CC`, `AC_POWER` → `POTENCIA_CA`, `DAILY_YIELD` → `RENDIMENTO_DIARIO`, `TOTAL_YIELD` → `RENDIMENTO_TOTAL`).
2. Inspeção (`head`, `shape`, `info`, `describe`).
3. Cálculo da maior potência CA registrada.
4. Cálculo do limiar de 70% da potência CA máxima.
5. Filtragem dos registros acima do limiar.
6. Contagem por `CHAVE_FONTE` (inversor) para identificar qual inversor aparece com mais frequência nos registros de alta geração.

**Resultado:** o inversor `adLQvlD726eNBSB` foi o mais frequente nos registros de alta geração (acima de 70% do máximo), com 68 ocorrências — sugerindo maior consistência/capacidade de produção em relação aos demais inversores da planta.

---

## 5. `Dataset5_Wind_Solar_Energy__2_.ipynb` — Wind & Solar Energy Production

**Fonte:** amostra de 20% (`amostra.csv`, exportada via Orange na Etapa A) do dataset Kaggle de produção eólica e solar, em **formato longo** (coluna `Source` com valores `Wind`/`Solar` e coluna `Production`).

**Situação-problema:** comparar a frequência de ocorrência de picos de alta produção solar e eólica, cada uma avaliada em relação ao seu **próprio** valor máximo.

**Etapas realizadas:**
1. Separação da série longa em duas séries nomeadas: `Geracao_Solar` e `Geracao_Eolica`.
2. Cálculo do valor máximo de cada fonte.
3. Cálculo de 70% do máximo de cada fonte (limiares independentes, por escalas diferentes).
4. Criação de DataFrames de alta geração para cada fonte.
5. Contagem e percentual de alta geração por fonte.
6. Comparação entre as fontes.
7. Justificativa de por que não se deve usar o mesmo valor absoluto como limiar para as duas fontes.

**Resultado:** a produção **eólica** apresentou frequência relativa de alta geração (2,87%) ligeiramente maior que a **solar** (2,44%). Como os máximos individuais diferem bastante (eólica: 22.929; solar: 16.316), um limiar fixo em valor absoluto distorceria a comparação — por isso o corte de 70% é aplicado ao máximo de cada fonte separadamente.

---

## 6. `Desafio_Final_Energia_ONS_API_Final.ipynb` — Carga Verificada ONS (API)

**Fonte:** API pública de **Carga Verificada** do Operador Nacional do Sistema Elétrico (ONS) — [dados.ons.org.br](https://dados.ons.org.br/dataset/carga-energia-verificada). Área analisada: **SP**, período de **01/08/2025 a 07/08/2025**.

**Situação-problema:** uma equipe de planejamento energético precisa analisar o comportamento da carga elétrica de uma região do SIN, obtendo os dados diretamente via API.

**Estrutura do desafio (9 etapas):**
1. **Construção e inspeção do DataFrame:** consulta à API, montagem do DataFrame `dados`, `head()`, `shape`, `columns`, `info()`, `describe()`.
2. **Organização dos dados:** renomeação dos atributos (`cod_areacarga` → `area_carga`, `dat_referencia` → `data`, `din_referenciautc` → `data_hora`, `val_cargaglobal` → `carga_global_MWmed`, etc.), seleção de colunas relevantes, checagem de nulos e tipos.
3. **Indicadores da carga elétrica:** mínima, máxima, média, mediana, amplitude e total de medições. Máximo (23.185,31 MWmed) substancialmente acima da média (17.870,83 MWmed) — amplitude de 11.046,06 MWmed, evidenciando picos relevantes de demanda.
4. **Períodos de alta demanda:** limiar de **90%** da carga máxima → **17 de 336 registros (5,06%)** — parcela pequena, indicando picos esporádicos.
5. **Segundo critério de análise:** carga acima da média, com novo DataFrame, contagem, percentual e comparação com o conjunto de alta demanda.
6. **Visualização:** gráfico de linha da carga ao longo do tempo (padrões diários, ciclos de 24h) e histograma da distribuição da carga (unimodal, levemente assimétrico à direita).
7. **Síntese para relatório:** variável `resumo_resultados` consolidando região, período, indicadores, limiar e resultados dos dois critérios.
8. **Relatório técnico assistido por IA (Gemini):** geração de relatório com base exclusivamente nos resultados calculados, sem invenção de causas.
9. **Validação crítica:** revisão humana do relatório gerado pela IA, checagem de uso correto dos indicadores e ausência de causalidade não sustentada pelos dados — culminando em relatório final revisado pela equipe.

**Destaque metodológico:** este é o notebook mais completo do conjunto, incluindo o ciclo completo de consumo de API pública → tratamento → indicadores → filtros → visualização → geração de relatório por IA → validação crítica humana.

---

## 7. `desafio_2__1_.ipynb` — Desafio 2

**Status:** notebook **incompleto/rascunho**. O código mistura dois contextos de dados diferentes — carrega um CSV local (`Steel_industry_data.csv`) mas em seguida tenta consultar uma API (usando uma variável `url` que não foi definida na célula) com parâmetros de carga elétrica da ONS (`dat_inicio`, `dat_fim`, `cod_areacarga`). O objetivo aparente é tratar os dados retornados (padronizar colunas de data/hora, área e carga, converter tipos) e reaproveitar a estrutura do Desafio 2 do notebook `Desafio_Final_Energia_ONS_API_Final.ipynb`. Precisa de correção antes de ser executado (definição da variável `url` e escolha de uma única fonte de dados consistente).

---

## Metodologia comum às análises

A maioria dos notebooks segue o mesmo roteiro analítico:

1. **Carregamento e organização** — leitura do dataset, renomeação de colunas para nomes mais claros/em português, checagem de tipos e valores ausentes.
2. **Indicadores descritivos** — mínimo, máximo, média, mediana (e às vezes amplitude) da variável de interesse.
3. **Definição de limiar de pico** — geralmente 70%, 75% ou 90% do valor máximo, usado para isolar períodos de "alta demanda/geração".
4. **Filtragem e quantificação** — criação de um DataFrame com os registros acima do limiar, contagem absoluta e percentual em relação ao total.
5. **Segunda condição** — cruzamento do critério de pico com uma variável adicional (temperatura, corrente, inversor, etc.), avaliando o quanto a interseção reduz o conjunto original.
6. **Interpretação** — discussão textual do que os números indicam sobre o comportamento do sistema analisado.

## Como reproduzir

Cada notebook espera o respectivo arquivo de dados no diretório de trabalho (originalmente `/content/` no Google Colab). Para rodar localmente, ajuste o caminho em `pd.read_csv(...)` para o local onde o CSV correspondente foi salvo. O notebook 6 (ONS) depende de acesso à internet para consultar a API pública e, opcionalmente, de uma chave de API do Gemini (`GEMINI_API_KEY`) para a etapa de geração de relatório por IA.
